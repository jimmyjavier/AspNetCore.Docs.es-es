---
<span data-ttu-id="85274-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="85274-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="85274-102">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="85274-102">'Blazor'</span></span>
- <span data-ttu-id="85274-103">"Identity"</span><span class="sxs-lookup"><span data-stu-id="85274-103">'Identity'</span></span>
- <span data-ttu-id="85274-104">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="85274-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="85274-105">"Razor"</span><span class="sxs-lookup"><span data-stu-id="85274-105">'Razor'</span></span>
- <span data-ttu-id="85274-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="85274-106">'SignalR' uid:</span></span> 

---
# <a name="kestrel-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="85274-107">Implementación del servidor web Kestrel en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="85274-107">Kestrel web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="85274-108">Por [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher) y [Stephen Halter](https://twitter.com/halter73)</span><span class="sxs-lookup"><span data-stu-id="85274-108">By [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), and [Stephen Halter](https://twitter.com/halter73)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="85274-109">Kestrel es un [servidor web multiplataforma de ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="85274-109">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="85274-110">Kestrel es el servidor web que se incluye de forma predeterminada en las plantillas de proyecto de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="85274-110">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="85274-111">Kestrel admite los siguientes escenarios:</span><span class="sxs-lookup"><span data-stu-id="85274-111">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="85274-112">HTTPS</span><span class="sxs-lookup"><span data-stu-id="85274-112">HTTPS</span></span>
* <span data-ttu-id="85274-113">Actualización opaca para habilitar [WebSockets](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="85274-113">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="85274-114">Sockets de Unix para alto rendimiento detrás de Nginx</span><span class="sxs-lookup"><span data-stu-id="85274-114">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="85274-115">HTTP/2 (excepto en macOS&dagger;)</span><span class="sxs-lookup"><span data-stu-id="85274-115">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="85274-116">&dagger;HTTP/2 se admitirá en una versión futura en macOS.</span><span class="sxs-lookup"><span data-stu-id="85274-116">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="85274-117">Kestrel admite todas las plataformas y versiones que sean compatibles con .NET Core.</span><span class="sxs-lookup"><span data-stu-id="85274-117">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="85274-118">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="85274-118">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="85274-119">Compatibilidad con HTTP/2</span><span class="sxs-lookup"><span data-stu-id="85274-119">HTTP/2 support</span></span>

<span data-ttu-id="85274-120">[HTTP/2](https://httpwg.org/specs/rfc7540.html) está disponible para las aplicaciones de ASP.NET Core si se cumplen los siguientes requisitos básicos:</span><span class="sxs-lookup"><span data-stu-id="85274-120">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="85274-121">Sistema operativo&dagger;</span><span class="sxs-lookup"><span data-stu-id="85274-121">Operating system&dagger;</span></span>
  * <span data-ttu-id="85274-122">Windows Server 2016/Windows 10 o posterior&Dagger;</span><span class="sxs-lookup"><span data-stu-id="85274-122">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="85274-123">Linux con OpenSSL 1.0.2 o posterior (por ejemplo, Ubuntu 16.04 o posterior)</span><span class="sxs-lookup"><span data-stu-id="85274-123">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="85274-124">Plataforma de destino: .NET Core 2.2 o posterior</span><span class="sxs-lookup"><span data-stu-id="85274-124">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="85274-125">Conexión con [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3)</span><span class="sxs-lookup"><span data-stu-id="85274-125">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="85274-126">Conexión con TLS 1.2 o una versión posterior</span><span class="sxs-lookup"><span data-stu-id="85274-126">TLS 1.2 or later connection</span></span>

<span data-ttu-id="85274-127">&dagger;HTTP/2 se admitirá en una versión futura en macOS.</span><span class="sxs-lookup"><span data-stu-id="85274-127">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="85274-128">&Dagger;Kestrel tiene compatibilidad limitada para HTTP/2 en Windows Server 2012 R2 y Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="85274-128">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="85274-129">La compatibilidad es limitada porque la lista de conjuntos de cifrado TLS admitidos y disponibles en estos sistemas operativos está limitada.</span><span class="sxs-lookup"><span data-stu-id="85274-129">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="85274-130">Se puede requerir un certificado generado mediante Elliptic Curve Digital Signature Algorithm (ECDSA) para proteger las conexiones TLS.</span><span class="sxs-lookup"><span data-stu-id="85274-130">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="85274-131">Si se establece una conexión HTTP/2, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) notifica `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="85274-131">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="85274-132">HTTP/2 está deshabilitado de manera predeterminada.</span><span class="sxs-lookup"><span data-stu-id="85274-132">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="85274-133">Para obtener más información sobre la configuración, consulte las secciones [Opciones de Kestrel](#kestrel-options) y [ListenOptions.Protocols](#listenoptionsprotocols).</span><span class="sxs-lookup"><span data-stu-id="85274-133">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="85274-134">Cuándo usar Kestrel con un proxy inverso</span><span class="sxs-lookup"><span data-stu-id="85274-134">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="85274-135">Kestrel se puede usar por sí solo o con un *servidor proxy inverso*, como [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org) o [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="85274-135">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="85274-136">Un servidor proxy inverso recibe las solicitudes HTTP de la red y las reenvía a Kestrel.</span><span class="sxs-lookup"><span data-stu-id="85274-136">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="85274-137">Kestrel empleado como servidor web perimetral (accesible desde Internet):</span><span class="sxs-lookup"><span data-stu-id="85274-137">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel se comunica directamente con Internet sin ningún servidor proxy inverso](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="85274-139">Kestrel empleado en una configuración de proxy inverso:</span><span class="sxs-lookup"><span data-stu-id="85274-139">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel se comunica indirectamente con Internet a través de un servidor proxy inverso, como IIS, Nginx o Apache](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="85274-141">Cualquiera de las configuraciones, con o sin servidor proxy inverso, es una configuración de hospedaje admitida.</span><span class="sxs-lookup"><span data-stu-id="85274-141">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="85274-142">Kestrel, utilizado como un servidor perimetral sin un servidor proxy inverso, no permite compartir la misma dirección IP y el mismo puerto entre varios procesos.</span><span class="sxs-lookup"><span data-stu-id="85274-142">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="85274-143">Si Kestrel se configura para escuchar en un puerto, controla todo el tráfico de ese puerto, independientemente de los encabezados `Host` de las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="85274-143">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="85274-144">Un proxy inverso que puede compartir puertos es capaz de reenviar solicitudes a Kestrel en una única dirección IP y puerto.</span><span class="sxs-lookup"><span data-stu-id="85274-144">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="85274-145">Aunque no sea necesario un servidor proxy inverso, su uso puede ser útil.</span><span class="sxs-lookup"><span data-stu-id="85274-145">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="85274-146">Un proxy inverso puede hacer lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="85274-146">A reverse proxy:</span></span>

* <span data-ttu-id="85274-147">Limitar el área expuesta públicamente de las aplicaciones que hospeda.</span><span class="sxs-lookup"><span data-stu-id="85274-147">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="85274-148">Proporcionar una capa extra de configuración y defensa.</span><span class="sxs-lookup"><span data-stu-id="85274-148">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="85274-149">Posiblemente, integrarse mejor con la infraestructura existente.</span><span class="sxs-lookup"><span data-stu-id="85274-149">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="85274-150">Simplificar el equilibrio de carga y la configuración de una comunicación segura (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="85274-150">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="85274-151">Solamente el servidor proxy inverso necesita un certificado X.509 y dicho servidor se puede comunicar con los servidores de la aplicación en la red interna por medio de HTTP sin formato.</span><span class="sxs-lookup"><span data-stu-id="85274-151">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="85274-152">El hospedaje en una configuración de proxy inverso requiere [filtrado de hosts](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="85274-152">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="kestrel-in-aspnet-core-apps"></a><span data-ttu-id="85274-153">Kestrel en aplicaciones ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="85274-153">Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="85274-154">Las plantillas de proyecto de ASP.NET Core usan Kestrel de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="85274-154">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="85274-155">En *Program.cs*, el método <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> llama a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span><span class="sxs-lookup"><span data-stu-id="85274-155">In *Program.cs*, the <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> method calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=8)]

<span data-ttu-id="85274-156">Para más información sobre la creación del host, vea las secciones *Configuración de un host* y *Configuración predeterminada del generador* de <xref:fundamentals/host/generic-host#set-up-a-host>.</span><span class="sxs-lookup"><span data-stu-id="85274-156">For more information on building the host, see the *Set up a host* and *Default builder settings* sections of <xref:fundamentals/host/generic-host#set-up-a-host>.</span></span>

<span data-ttu-id="85274-157">Para proporcionar configuración adicional después de llamar a `ConfigureWebHostDefaults`, use `ConfigureKestrel`:</span><span class="sxs-lookup"><span data-stu-id="85274-157">To provide additional configuration after calling `ConfigureWebHostDefaults`, use `ConfigureKestrel`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(serverOptions =>
            {
                // Set properties and call methods on options
            })
            .UseStartup<Startup>();
        });
```

## <a name="kestrel-options"></a><span data-ttu-id="85274-158">Opciones de Kestrel</span><span class="sxs-lookup"><span data-stu-id="85274-158">Kestrel options</span></span>

<span data-ttu-id="85274-159">El servidor web Kestrel tiene opciones de configuración de restricción que son especialmente útiles en las implementaciones con conexión a Internet.</span><span class="sxs-lookup"><span data-stu-id="85274-159">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="85274-160">Establezca restricciones en la propiedad <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> de la clase <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>.</span><span class="sxs-lookup"><span data-stu-id="85274-160">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="85274-161">La propiedad `Limits` contiene una instancia de la clase <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>.</span><span class="sxs-lookup"><span data-stu-id="85274-161">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="85274-162">En los ejemplos siguientes se usa el espacio de nombres <xref:Microsoft.AspNetCore.Server.Kestrel.Core>:</span><span class="sxs-lookup"><span data-stu-id="85274-162">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="85274-163">En los ejemplos que se muestran más adelante en este artículo, las opciones de Kestrel se configuran en código de C#.</span><span class="sxs-lookup"><span data-stu-id="85274-163">In examples shown later in this article, Kestrel options are configured in C# code.</span></span> <span data-ttu-id="85274-164">Las opciones de Kestrel también se pueden establecer mediante un [proveedor de configuración](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="85274-164">Kestrel options can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="85274-165">Por ejemplo, el [proveedor de configuración de archivo](xref:fundamentals/configuration/index#file-configuration-provider) puede cargar la configuración de Kestrel desde un archivo *appsettings.json* o *appsettings.{Environment}.json*:</span><span class="sxs-lookup"><span data-stu-id="85274-165">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider) can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    },
    "DisableStringReuse": true
  }
}
```

> [!NOTE]
> <span data-ttu-id="85274-166"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> y la [configuración del punto de conexión](#endpoint-configuration) se pueden establecer a partir de proveedores de configuración.</span><span class="sxs-lookup"><span data-stu-id="85274-166"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> and [endpoint configuration](#endpoint-configuration) are configurable from configuration providers.</span></span> <span data-ttu-id="85274-167">El resto de la configuración de Kestrel debe establecerse en código de C#.</span><span class="sxs-lookup"><span data-stu-id="85274-167">Remaining Kestrel configuration must be configured in C# code.</span></span>

<span data-ttu-id="85274-168">Siga **uno** de estos procedimientos:</span><span class="sxs-lookup"><span data-stu-id="85274-168">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="85274-169">Configure Kestrel en `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="85274-169">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="85274-170">Inserte una instancia de `IConfiguration` en la clase `Startup`.</span><span class="sxs-lookup"><span data-stu-id="85274-170">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="85274-171">En el ejemplo siguiente se da por supuesto que la configuración insertada está asignada a la propiedad `Configuration`.</span><span class="sxs-lookup"><span data-stu-id="85274-171">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="85274-172">En `Startup.ConfigureServices`, cargue la sección de configuración `Kestrel` en la configuración de Kestrel:</span><span class="sxs-lookup"><span data-stu-id="85274-172">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

     ```csharp
     using Microsoft.Extensions.Configuration
     
     public class Startup
     {
         public Startup(IConfiguration configuration)
         {
             Configuration = configuration;
         }

         public IConfiguration Configuration { get; }

         public void ConfigureServices(IServiceCollection services)
         {
             services.Configure<KestrelServerOptions>(
                 Configuration.GetSection("Kestrel"));
         }

         public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
         {
             ...
         }
     }
     ```

* <span data-ttu-id="85274-173">Configure Kestrel al compilar el host:</span><span class="sxs-lookup"><span data-stu-id="85274-173">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="85274-174">En *Program.cs*, cargue la sección de configuración `Kestrel` en la configuración de Kestrel:</span><span class="sxs-lookup"><span data-stu-id="85274-174">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IHostBuilder CreateHostBuilder(string[] args) =>
      Host.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .ConfigureWebHostDefaults(webBuilder =>
          {
              webBuilder.UseStartup<Startup>();
          });
  ```

<span data-ttu-id="85274-175">Los dos procedimientos anteriores funcionan con cualquier [proveedor de configuración](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="85274-175">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="85274-176">Tiempo de expiración de la conexión persistente</span><span class="sxs-lookup"><span data-stu-id="85274-176">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="85274-177">Obtiene o establece el [tiempo de expiración de la conexión persistente](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="85274-177">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="85274-178">El valor predeterminado es de 2 minutos.</span><span class="sxs-lookup"><span data-stu-id="85274-178">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a><span data-ttu-id="85274-179">Las conexiones máximas de cliente</span><span class="sxs-lookup"><span data-stu-id="85274-179">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="85274-180">El número máximo de conexiones de TCP abiertas simultáneas que se pueden establecer para toda la aplicación con este código:</span><span class="sxs-lookup"><span data-stu-id="85274-180">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="85274-181">Hay un límite independiente para las conexiones que se han actualizado desde HTTP o HTTPS a otro protocolo (por ejemplo, en una solicitud de WebSockets).</span><span class="sxs-lookup"><span data-stu-id="85274-181">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="85274-182">Cuando se actualiza una conexión, no se cuenta con respecto al límite de `MaxConcurrentConnections`.</span><span class="sxs-lookup"><span data-stu-id="85274-182">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="85274-183">El número máximo de conexiones es ilimitado de forma predeterminada (null).</span><span class="sxs-lookup"><span data-stu-id="85274-183">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="85274-184">El tamaño máximo del cuerpo de solicitud</span><span class="sxs-lookup"><span data-stu-id="85274-184">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="85274-185">El tamaño máximo predeterminado del cuerpo de solicitud es 30 000 000 bytes, que son aproximadamente 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="85274-185">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="85274-186">El método recomendado para invalidar el límite de una aplicación ASP.NET Core MVC es usar el atributo <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> en un método de acción:</span><span class="sxs-lookup"><span data-stu-id="85274-186">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="85274-187">Este es un ejemplo que muestra cómo configurar la restricción en la aplicación y todas las solicitudes:</span><span class="sxs-lookup"><span data-stu-id="85274-187">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="85274-188">Invalide la configuración en una solicitud específica de middleware:</span><span class="sxs-lookup"><span data-stu-id="85274-188">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="85274-189">Se inicia una excepción si la aplicación configura el límite de una solicitud después de que la aplicación haya empezado a leer la solicitud.</span><span class="sxs-lookup"><span data-stu-id="85274-189">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="85274-190">Hay una propiedad `IsReadOnly` que señala si la propiedad `MaxRequestBodySize` tiene el estado de solo lectura, lo que significa que es demasiado tarde para configurar el límite.</span><span class="sxs-lookup"><span data-stu-id="85274-190">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="85274-191">Cuando se ejecuta una aplicación [fuera de proceso](xref:host-and-deploy/iis/index#out-of-process-hosting-model) detrás del [módulo de ASP.NET Core](xref:host-and-deploy/aspnet-core-module), el límite de tamaño del cuerpo de la solicitud de Kestrel se deshabilita porque IIS ya establece el límite.</span><span class="sxs-lookup"><span data-stu-id="85274-191">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="85274-192">La velocidad mínima de los datos del cuerpo de solicitud.</span><span class="sxs-lookup"><span data-stu-id="85274-192">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="85274-193">Kestrel comprueba cada segundo si los datos entran a la velocidad especificada en bytes por segundo.</span><span class="sxs-lookup"><span data-stu-id="85274-193">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="85274-194">Si la velocidad está por debajo del mínimo, se agota el tiempo de espera de la conexión. El período de gracia es la cantidad de tiempo que Kestrel da al cliente para aumentar su velocidad de envío hasta el mínimo; no se comprueba la velocidad durante ese tiempo.</span><span class="sxs-lookup"><span data-stu-id="85274-194">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="85274-195">Este período de gracia permite evitar que se interrumpan las conexiones que inicialmente están enviando datos a una velocidad lenta debido a un inicio lento de TCP.</span><span class="sxs-lookup"><span data-stu-id="85274-195">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="85274-196">La velocidad mínima predeterminada es 240 bytes por segundo, con un período de gracia de cinco segundos.</span><span class="sxs-lookup"><span data-stu-id="85274-196">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="85274-197">También se aplica una velocidad mínima a la respuesta.</span><span class="sxs-lookup"><span data-stu-id="85274-197">A minimum rate also applies to the response.</span></span> <span data-ttu-id="85274-198">El código para establecer el límite de solicitudes y el límite de respuestas es el mismo, salvo que tienen `RequestBody` o `Response` en los nombres de propiedad y de interfaz.</span><span class="sxs-lookup"><span data-stu-id="85274-198">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="85274-199">Este es un ejemplo que muestra cómo configurar las velocidades de datos mínimas en *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="85274-199">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

<span data-ttu-id="85274-200">Invalide los límites de velocidad mínima por solicitud en el middleware:</span><span class="sxs-lookup"><span data-stu-id="85274-200">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="85274-201">La característica <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> a la que se hace referencia en el ejemplo anterior no está presente en `HttpContext.Features` para las solicitudes HTTP/2, porque generalmente no se permite modificar los límites de velocidad por solicitud debido a la compatibilidad del protocolo con la multiplexación de solicitudes.</span><span class="sxs-lookup"><span data-stu-id="85274-201">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> referenced in the prior sample is not present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis is generally not supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="85274-202">Sin embargo, <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> sigue estando presente en `HttpContext.Features` para las solicitudes HTTP/2, ya que el límite de velocidad de lectura aún puede estar *completamente deshabilitado* por solicitud estableciendo `IHttpMinRequestBodyDataRateFeature.MinDataRate` en `null` incluso para una solicitud HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="85274-202">However, the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> is still present `HttpContext.Features` for HTTP/2 requests, because the read rate limit can still be *disabled entirely* on a per-request basis by setting `IHttpMinRequestBodyDataRateFeature.MinDataRate` to `null` even for an HTTP/2 request.</span></span> <span data-ttu-id="85274-203">Si se intenta leer `IHttpMinRequestBodyDataRateFeature.MinDataRate` o se intenta su establecimiento en un valor distinto de `null`, se obtendrá una excepción `NotSupportedException` con una solicitud HTTP/2 dada.</span><span class="sxs-lookup"><span data-stu-id="85274-203">Attempting to read `IHttpMinRequestBodyDataRateFeature.MinDataRate` or attempting to set it to a value other than `null` will result in a `NotSupportedException` being thrown given an HTTP/2 request.</span></span>

<span data-ttu-id="85274-204">Se siguen aplicando límites de velocidad en todo el servidor configurados con `KestrelServerOptions.Limits` a las conexiones HTTP/1.x y HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="85274-204">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="85274-205">Tiempo de expiración de los encabezados de solicitud</span><span class="sxs-lookup"><span data-stu-id="85274-205">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="85274-206">Obtiene o establece la cantidad máxima de tiempo que el servidor pasa recibiendo las cabeceras de las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="85274-206">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="85274-207">El valor predeterminado es 30 segundos.</span><span class="sxs-lookup"><span data-stu-id="85274-207">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="85274-208">Secuencias máximas por conexión</span><span class="sxs-lookup"><span data-stu-id="85274-208">Maximum streams per connection</span></span>

<span data-ttu-id="85274-209">`Http2.MaxStreamsPerConnection` limita el número de secuencias de solicitudes simultáneas por conexión HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="85274-209">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="85274-210">Se rechazarán las secuencias en exceso.</span><span class="sxs-lookup"><span data-stu-id="85274-210">Excess streams are refused.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

<span data-ttu-id="85274-211">El valor predeterminado es 100.</span><span class="sxs-lookup"><span data-stu-id="85274-211">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="85274-212">Tamaño de la tabla de encabezado</span><span class="sxs-lookup"><span data-stu-id="85274-212">Header table size</span></span>

<span data-ttu-id="85274-213">El descodificador HPACK descomprime los encabezados HTTP para las conexiones HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="85274-213">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="85274-214">`Http2.HeaderTableSize` limita el tamaño de la tabla de compresión de encabezado que usa el descodificador HPACK.</span><span class="sxs-lookup"><span data-stu-id="85274-214">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="85274-215">El valor se proporciona en octetos y debe ser mayor que cero (0).</span><span class="sxs-lookup"><span data-stu-id="85274-215">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

<span data-ttu-id="85274-216">El valor predeterminado es 4096.</span><span class="sxs-lookup"><span data-stu-id="85274-216">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="85274-217">Tamaño máximo de marco</span><span class="sxs-lookup"><span data-stu-id="85274-217">Maximum frame size</span></span>

<span data-ttu-id="85274-218">`Http2.MaxFrameSize` indica el tamaño máximo permitido de una carga de marco de conexión HTTP/2 recibida o enviada por el servidor.</span><span class="sxs-lookup"><span data-stu-id="85274-218">`Http2.MaxFrameSize` indicates the maximum allowed size of an HTTP/2 connection frame payload received or sent by the server.</span></span> <span data-ttu-id="85274-219">El valor se proporciona en octetos y debe estar comprendido entre 2^14 (16 384) y 2^24-1 (16 777 215).</span><span class="sxs-lookup"><span data-stu-id="85274-219">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

<span data-ttu-id="85274-220">El valor predeterminado es 2^14 (16 384).</span><span class="sxs-lookup"><span data-stu-id="85274-220">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="85274-221">Tamaño máximo del encabezado de solicitud</span><span class="sxs-lookup"><span data-stu-id="85274-221">Maximum request header size</span></span>

<span data-ttu-id="85274-222">`Http2.MaxRequestHeaderFieldSize` indica el tamaño máximo permitido (en octetos) de los valores de los encabezados de solicitud.</span><span class="sxs-lookup"><span data-stu-id="85274-222">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="85274-223">Este límite se aplica al nombre y al valor en sus representaciones comprimidas y no comprimidas.</span><span class="sxs-lookup"><span data-stu-id="85274-223">This limit applies to both name and value in their compressed and uncompressed representations.</span></span> <span data-ttu-id="85274-224">El valor debe ser mayor que cero (0).</span><span class="sxs-lookup"><span data-stu-id="85274-224">The value must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

<span data-ttu-id="85274-225">El valor predeterminado es 8192.</span><span class="sxs-lookup"><span data-stu-id="85274-225">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="85274-226">Tamaño inicial de la ventana de conexión</span><span class="sxs-lookup"><span data-stu-id="85274-226">Initial connection window size</span></span>

<span data-ttu-id="85274-227">`Http2.InitialConnectionWindowSize` indica la cantidad máxima de datos del cuerpo de solicitud (en bytes) que el servidor almacena en búfer a la vez de forma agregada en todas las solicitudes (transmisiones) por conexión.</span><span class="sxs-lookup"><span data-stu-id="85274-227">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="85274-228">Las solicitudes también están limitadas por `Http2.InitialStreamWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="85274-228">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="85274-229">El valor debe ser igual o mayor que 65 535 y menor que 2^31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="85274-229">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

<span data-ttu-id="85274-230">El valor predeterminado es 128 KB (131 072).</span><span class="sxs-lookup"><span data-stu-id="85274-230">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="85274-231">Tamaño inicial de la ventana de transmisión</span><span class="sxs-lookup"><span data-stu-id="85274-231">Initial stream window size</span></span>

<span data-ttu-id="85274-232">`Http2.InitialStreamWindowSize` indica la cantidad máxima de datos del cuerpo de solicitud (en bytes) que el servidor almacena en búfer a la vez por solicitud (transmisión).</span><span class="sxs-lookup"><span data-stu-id="85274-232">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="85274-233">Las solicitudes también están limitadas por `Http2.InitialConnectionWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="85274-233">Requests are also limited by `Http2.InitialConnectionWindowSize`.</span></span> <span data-ttu-id="85274-234">El valor debe ser igual o mayor que 65 535 y menor que 2^31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="85274-234">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

<span data-ttu-id="85274-235">El valor predeterminado es 96 KB (98 304).</span><span class="sxs-lookup"><span data-stu-id="85274-235">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="85274-236">E/S síncrona</span><span class="sxs-lookup"><span data-stu-id="85274-236">Synchronous I/O</span></span>

<span data-ttu-id="85274-237"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controla si se permite la E/S sincrónica para la solicitud y la respuesta.</span><span class="sxs-lookup"><span data-stu-id="85274-237"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="85274-238">El valor predeterminado es `false`.</span><span class="sxs-lookup"><span data-stu-id="85274-238">The default value is `false`.</span></span>

> [!WARNING]
> <span data-ttu-id="85274-239">Un gran número de operaciones de E/S sincrónicas de bloqueo puede dar lugar al colapso del grupo de subprocesos, lo que hace que la aplicación no responda.</span><span class="sxs-lookup"><span data-stu-id="85274-239">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="85274-240">Habilite solo `AllowSynchronousIO` al usar una biblioteca que no admite la E/S asincrónica.</span><span class="sxs-lookup"><span data-stu-id="85274-240">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="85274-241">En el ejemplo siguiente se habilita la E/S sincrónica:</span><span class="sxs-lookup"><span data-stu-id="85274-241">The following example enables synchronous I/O:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="85274-242">Para más información sobre otras opciones y límites de Kestrel, vea:</span><span class="sxs-lookup"><span data-stu-id="85274-242">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="85274-243">Configuración de punto de conexión</span><span class="sxs-lookup"><span data-stu-id="85274-243">Endpoint configuration</span></span>

<span data-ttu-id="85274-244">ASP.NET Core enlaza de forma predeterminada a:</span><span class="sxs-lookup"><span data-stu-id="85274-244">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="85274-245">`https://localhost:5001` (cuando hay presente un certificado de desarrollo local)</span><span class="sxs-lookup"><span data-stu-id="85274-245">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="85274-246">Especifique direcciones URL mediante los siguientes elementos:</span><span class="sxs-lookup"><span data-stu-id="85274-246">Specify URLs using the:</span></span>

* <span data-ttu-id="85274-247">La variable de entorno `ASPNETCORE_URLS`.</span><span class="sxs-lookup"><span data-stu-id="85274-247">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="85274-248">El argumento de la línea de comandos `--urls`.</span><span class="sxs-lookup"><span data-stu-id="85274-248">`--urls` command-line argument.</span></span>
* <span data-ttu-id="85274-249">La clave de configuración de host `urls`.</span><span class="sxs-lookup"><span data-stu-id="85274-249">`urls` host configuration key.</span></span>
* <span data-ttu-id="85274-250">El método de extensión `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="85274-250">`UseUrls` extension method.</span></span>

<span data-ttu-id="85274-251">El valor que estos métodos suministran puede ser uno o más puntos de conexión HTTP y HTTPS (este último, si hay disponible un certificado predeterminado).</span><span class="sxs-lookup"><span data-stu-id="85274-251">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="85274-252">Configure el valor como una lista separada por punto y coma (por ejemplo, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="85274-252">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="85274-253">Para más información sobre estos enfoques, consulte [Direcciones URL del servidor](xref:fundamentals/host/web-host#server-urls) e [Invalidar la configuración](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="85274-253">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="85274-254">Un certificado de desarrollo se crea:</span><span class="sxs-lookup"><span data-stu-id="85274-254">A development certificate is created:</span></span>

* <span data-ttu-id="85274-255">Cuando el [SDK de .NET Core](/dotnet/core/sdk) está instalado.</span><span class="sxs-lookup"><span data-stu-id="85274-255">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="85274-256">Para crear un certificado, se usa la [herramienta dev-certs](xref:aspnetcore-2.1#https).</span><span class="sxs-lookup"><span data-stu-id="85274-256">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="85274-257">Algunos exploradores necesitan que se conceda permiso explícito para confiar en el certificado de desarrollo local.</span><span class="sxs-lookup"><span data-stu-id="85274-257">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="85274-258">Las plantillas de proyecto configuran aplicaciones para que se ejecuten en HTTPS de forma predeterminada e incluyen [redirección de HTTPS y compatibilidad con HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="85274-258">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="85274-259">Llame a los métodos <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> o <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> de <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> para configurar los puertos y los prefijos de dirección URL para Kestrel.</span><span class="sxs-lookup"><span data-stu-id="85274-259">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="85274-260">`UseUrls`, el argumento de línea de comandos `--urls`, la clave de configuración de host `urls` y la variable de entorno `ASPNETCORE_URLS` también funcionan, pero tienen las limitaciones que se indican más adelante en esta sección (debe haber disponible un certificado predeterminado para la configuración de puntos de conexión HTTPS).</span><span class="sxs-lookup"><span data-stu-id="85274-260">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="85274-261">Configuración de `KestrelServerOptions`:</span><span class="sxs-lookup"><span data-stu-id="85274-261">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="85274-262">ConfigureEndpointDefaults(Action\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="85274-262">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="85274-263">Especifica una `Action` de configuración para que se ejecute con cada punto de conexión especificado.</span><span class="sxs-lookup"><span data-stu-id="85274-263">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="85274-264">Al llamar a `ConfigureEndpointDefaults` varias veces, se reemplazan las `Action` anteriores por la última `Action` especificada.</span><span class="sxs-lookup"><span data-stu-id="85274-264">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });
});
```

> [!NOTE]
> <span data-ttu-id="85274-265">Los puntos de conexión que se crean mediante una llamada a <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **antes** de llamar a <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> no tendrán aplicados los valores predeterminados.</span><span class="sxs-lookup"><span data-stu-id="85274-265">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="85274-266">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="85274-266">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="85274-267">Especifica una `Action` de configuración para que se ejecute con cada punto de conexión HTTPS.</span><span class="sxs-lookup"><span data-stu-id="85274-267">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="85274-268">Al llamar a `ConfigureHttpsDefaults` varias veces, se reemplazan las `Action` anteriores por la última `Action` especificada.</span><span class="sxs-lookup"><span data-stu-id="85274-268">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        // certificate is an X509Certificate2
        listenOptions.ServerCertificate = certificate;
    });
});
```

> [!NOTE]
> <span data-ttu-id="85274-269">Los puntos de conexión que se crean mediante una llamada a <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **antes** de llamar a <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> no tendrán aplicados los valores predeterminados.</span><span class="sxs-lookup"><span data-stu-id="85274-269">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="85274-270">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="85274-270">Configure(IConfiguration)</span></span>

<span data-ttu-id="85274-271">Crea un cargador de configuración para configurar Kestrel que toma una <xref:Microsoft.Extensions.Configuration.IConfiguration> como entrada.</span><span class="sxs-lookup"><span data-stu-id="85274-271">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="85274-272">El ámbito de la configuración debe corresponderse con la sección de configuración de Kestrel.</span><span class="sxs-lookup"><span data-stu-id="85274-272">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="85274-273">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="85274-273">ListenOptions.UseHttps</span></span>

<span data-ttu-id="85274-274">Configure Kestrel para que use HTTPS.</span><span class="sxs-lookup"><span data-stu-id="85274-274">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="85274-275">Extensiones de `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="85274-275">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="85274-276">`UseHttps`: configure Kestrel para que use HTTPS con el certificado predeterminado.</span><span class="sxs-lookup"><span data-stu-id="85274-276">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="85274-277">Produce una excepción si no hay ningún certificado predeterminado configurado.</span><span class="sxs-lookup"><span data-stu-id="85274-277">Throws an exception if no default certificate is configured.</span></span>
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

<span data-ttu-id="85274-278">Parámetros de `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="85274-278">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="85274-279">`filename` es la ruta de acceso y el nombre de archivo de un archivo de certificado correspondiente al directorio donde están los archivos de contenido de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="85274-279">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="85274-280">`password` es la contraseña necesaria para obtener acceso a los datos del certificado X.509.</span><span class="sxs-lookup"><span data-stu-id="85274-280">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="85274-281">`configureOptions` es una `Action` para configurar `HttpsConnectionAdapterOptions`.</span><span class="sxs-lookup"><span data-stu-id="85274-281">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="85274-282">Devuelve `ListenOptions`.</span><span class="sxs-lookup"><span data-stu-id="85274-282">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="85274-283">`storeName` es el almacén de certificados desde el que se carga el certificado.</span><span class="sxs-lookup"><span data-stu-id="85274-283">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="85274-284">`subject` es el nombre del sujeto del certificado.</span><span class="sxs-lookup"><span data-stu-id="85274-284">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="85274-285">`allowInvalid` indica si se deben tener en cuenta los certificados no válidos, como los certificados autofirmados.</span><span class="sxs-lookup"><span data-stu-id="85274-285">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="85274-286">`location` es la ubicación del almacén desde el que se carga el certificado.</span><span class="sxs-lookup"><span data-stu-id="85274-286">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="85274-287">`serverCertificate` es el certificado X.509.</span><span class="sxs-lookup"><span data-stu-id="85274-287">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="85274-288">En un entorno de producción, HTTPS se debe configurar explícitamente.</span><span class="sxs-lookup"><span data-stu-id="85274-288">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="85274-289">Como mínimo, debe existir un certificado predeterminado.</span><span class="sxs-lookup"><span data-stu-id="85274-289">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="85274-290">Estas son las configuraciones compatibles:</span><span class="sxs-lookup"><span data-stu-id="85274-290">Supported configurations described next:</span></span>

* <span data-ttu-id="85274-291">Sin configuración</span><span class="sxs-lookup"><span data-stu-id="85274-291">No configuration</span></span>
* <span data-ttu-id="85274-292">Reemplazar el certificado predeterminado de configuración</span><span class="sxs-lookup"><span data-stu-id="85274-292">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="85274-293">Cambiar los valores predeterminados en el código</span><span class="sxs-lookup"><span data-stu-id="85274-293">Change the defaults in code</span></span>

<span data-ttu-id="85274-294">*Sin configuración*</span><span class="sxs-lookup"><span data-stu-id="85274-294">*No configuration*</span></span>

<span data-ttu-id="85274-295">Kestrel escucha en `http://localhost:5000` y en `https://localhost:5001` (si hay disponible un certificado predeterminado).</span><span class="sxs-lookup"><span data-stu-id="85274-295">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="85274-296">*Reemplazar el certificado predeterminado de configuración*</span><span class="sxs-lookup"><span data-stu-id="85274-296">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="85274-297">`CreateDefaultBuilder` llama a `Configure(context.Configuration.GetSection("Kestrel"))` de forma predeterminada para cargar la configuración de Kestrel.</span><span class="sxs-lookup"><span data-stu-id="85274-297">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="85274-298">Hay disponible un esquema de configuración de aplicación HTTPS predeterminado para Kestrel.</span><span class="sxs-lookup"><span data-stu-id="85274-298">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="85274-299">Configure varios puntos de conexión (incluidas las direcciones URL y los certificados que va a usar) desde un archivo en disco o desde un almacén de certificados.</span><span class="sxs-lookup"><span data-stu-id="85274-299">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="85274-300">En el siguiente ejemplo de *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="85274-300">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="85274-301">Establezca **AllowInvalid** en `true` para permitir el uso de certificados no válidos (por ejemplo, certificados autofirmados).</span><span class="sxs-lookup"><span data-stu-id="85274-301">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="85274-302">Cualquier punto de conexión HTTPS que no especifique un certificado (**HttpsDefaultCert** en el siguiente ejemplo) revierte al certificado definido en **Certificados** > **Predeterminado** o al certificado de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="85274-302">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },
      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },
      "HttpsInlineCertStore": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },
      "HttpsDefaultCert": {
        "Url": "https://localhost:5003"
      },
      "Https": {
        "Url": "https://*:5004",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

<span data-ttu-id="85274-303">Una alternativa al uso de **Path** y **Password** en cualquier nodo de certificado consiste en especificar el certificado por medio de campos del almacén de certificados.</span><span class="sxs-lookup"><span data-stu-id="85274-303">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="85274-304">Por ejemplo, el certificado en **Certificados** > **Predeterminado** se puede especificar así:</span><span class="sxs-lookup"><span data-stu-id="85274-304">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="85274-305">Notas sobre el esquema:</span><span class="sxs-lookup"><span data-stu-id="85274-305">Schema notes:</span></span>

* <span data-ttu-id="85274-306">En los nombres de los puntos de conexión se distingue entre mayúsculas y minúsculas.</span><span class="sxs-lookup"><span data-stu-id="85274-306">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="85274-307">Por ejemplo, `HTTPS` y `Https` son válidos.</span><span class="sxs-lookup"><span data-stu-id="85274-307">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="85274-308">El parámetro `Url` es necesario en cada punto de conexión.</span><span class="sxs-lookup"><span data-stu-id="85274-308">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="85274-309">El formato de este parámetro es el mismo que el del parámetro de configuración `Urls` de nivel superior, excepto por el hecho de que está limitado a un único valor.</span><span class="sxs-lookup"><span data-stu-id="85274-309">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="85274-310">En vez de agregarse, estos puntos de conexión reemplazan a los que están definidos en la configuración `Urls` de nivel superior.</span><span class="sxs-lookup"><span data-stu-id="85274-310">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="85274-311">Los puntos de conexión definidos en el código a través de `Listen` son acumulativos con respecto a los puntos de conexión definidos en la sección de configuración.</span><span class="sxs-lookup"><span data-stu-id="85274-311">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="85274-312">La sección `Certificate` es opcional.</span><span class="sxs-lookup"><span data-stu-id="85274-312">The `Certificate` section is optional.</span></span> <span data-ttu-id="85274-313">Si la sección `Certificate` no se especifica, se usan los valores predeterminados definidos en escenarios anteriores.</span><span class="sxs-lookup"><span data-stu-id="85274-313">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="85274-314">Si no hay valores predeterminados disponibles, el servidor produce una excepción y no se inicia.</span><span class="sxs-lookup"><span data-stu-id="85274-314">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="85274-315">La sección `Certificate` admite certificadostanto **Path**&ndash;**Password** como **Subject**&ndash;**Store**.</span><span class="sxs-lookup"><span data-stu-id="85274-315">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="85274-316">Se puede definir el número de puntos de conexión que se quiera de esta manera, siempre y cuando no produzcan conflictos de puerto.</span><span class="sxs-lookup"><span data-stu-id="85274-316">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="85274-317">`options.Configure(context.Configuration.GetSection("{SECTION}"))` devuelve un `KestrelConfigurationLoader` con un método `.Endpoint(string name, listenOptions => { })` que se puede usar para complementar la configuración de un punto de conexión configurado:</span><span class="sxs-lookup"><span data-stu-id="85274-317">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

```csharp
webBuilder.UseKestrel((context, serverOptions) =>
{
    serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
        .Endpoint("HTTPS", listenOptions =>
        {
            listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
        });
});
```

<span data-ttu-id="85274-318">Se puede acceder directamente a `KestrelServerOptions.ConfigurationLoader` para seguir con la iteración en el cargador existente, como el proporcionado por <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span><span class="sxs-lookup"><span data-stu-id="85274-318">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="85274-319">La sección de configuración de cada punto de conexión está disponible en las opciones del método `Endpoint` para que se pueda leer la configuración personalizada.</span><span class="sxs-lookup"><span data-stu-id="85274-319">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="85274-320">Se pueden cargar varias configuraciones volviendo a llamar a `options.Configure(context.Configuration.GetSection("{SECTION}"))` con otra sección.</span><span class="sxs-lookup"><span data-stu-id="85274-320">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="85274-321">Se usa la última configuración, a menos que se llame explícitamente a `Load` en instancias anteriores.</span><span class="sxs-lookup"><span data-stu-id="85274-321">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="85274-322">El metapaquete no llama a `Load`, con lo cual su sección de configuración predeterminada se puede reemplazar.</span><span class="sxs-lookup"><span data-stu-id="85274-322">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="85274-323">`KestrelConfigurationLoader` refleja la familia `Listen` de API de `KestrelServerOptions` como sobrecargas de `Endpoint`, por lo que los puntos de conexión de configuración y código se pueden configurar en el mismo lugar.</span><span class="sxs-lookup"><span data-stu-id="85274-323">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="85274-324">En estas sobrecargas no se usan nombres y solo consumen valores predeterminados de la configuración.</span><span class="sxs-lookup"><span data-stu-id="85274-324">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="85274-325">*Cambiar los valores predeterminados en el código*</span><span class="sxs-lookup"><span data-stu-id="85274-325">*Change the defaults in code*</span></span>

<span data-ttu-id="85274-326">`ConfigureEndpointDefaults` y `ConfigureHttpsDefaults` se pueden usar para cambiar la configuración predeterminada de `ListenOptions` y `HttpsConnectionAdapterOptions`, incluido sustituir el certificado predeterminado especificado en el escenario anterior.</span><span class="sxs-lookup"><span data-stu-id="85274-326">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="85274-327">Se debe llamar a `ConfigureEndpointDefaults` y a `ConfigureHttpsDefaults` antes de que se configure algún punto de conexión.</span><span class="sxs-lookup"><span data-stu-id="85274-327">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });

    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.SslProtocols = SslProtocols.Tls12;
    });
});
```

<span data-ttu-id="85274-328">*Compatibilidad de Kestrel con SNI*</span><span class="sxs-lookup"><span data-stu-id="85274-328">*Kestrel support for SNI*</span></span>

<span data-ttu-id="85274-329">[Indicación de nombre de servidor (SNI)](https://tools.ietf.org/html/rfc6066#section-3) se puede usar para hospedar varios dominios en la misma dirección IP y puerto.</span><span class="sxs-lookup"><span data-stu-id="85274-329">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="85274-330">Para que SNI funcione, el cliente envía el nombre de host de la sesión segura al servidor durante el protocolo de enlace TLS para que, de este modo, el servidor pueda proporcionar el certificado correcto.</span><span class="sxs-lookup"><span data-stu-id="85274-330">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="85274-331">El cliente usa el certificado proporcionado para la comunicación cifrada con el servidor durante la sesión segura que sigue al protocolo de enlace TLS.</span><span class="sxs-lookup"><span data-stu-id="85274-331">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="85274-332">Kestrel admite SNI a través de la devolución de llamada `ServerCertificateSelector`.</span><span class="sxs-lookup"><span data-stu-id="85274-332">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="85274-333">La devolución de llamada se invoca una vez por conexión para permitir que la aplicación inspeccione el nombre de host y seleccione el certificado adecuado.</span><span class="sxs-lookup"><span data-stu-id="85274-333">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="85274-334">La compatibilidad con SNI requiere lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="85274-334">SNI support requires:</span></span>

* <span data-ttu-id="85274-335">Ejecutarse en el marco de destino `netcoreapp2.1` o posterior.</span><span class="sxs-lookup"><span data-stu-id="85274-335">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="85274-336">En `net461` o posterior, se invoca la devolución de llamada, pero `name` siempre es `null`.</span><span class="sxs-lookup"><span data-stu-id="85274-336">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="85274-337">`name` también será `null` si el cliente no proporciona el parámetro de nombre de host en el protocolo de enlace TLS.</span><span class="sxs-lookup"><span data-stu-id="85274-337">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="85274-338">Todos los sitios web deben ejecutarse en la misma instancia de Kestrel.</span><span class="sxs-lookup"><span data-stu-id="85274-338">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="85274-339">Kestrel no admite el uso compartido de una dirección IP y un puerto entre varias instancias sin un proxy inverso.</span><span class="sxs-lookup"><span data-stu-id="85274-339">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ListenAnyIP(5005, listenOptions =>
    {
        listenOptions.UseHttps(httpsOptions =>
        {
            var localhostCert = CertificateLoader.LoadFromStoreCert(
                "localhost", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var exampleCert = CertificateLoader.LoadFromStoreCert(
                "example.com", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var subExampleCert = CertificateLoader.LoadFromStoreCert(
                "sub.example.com", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var certs = new Dictionary<string, X509Certificate2>(
                StringComparer.OrdinalIgnoreCase);
            certs["localhost"] = localhostCert;
            certs["example.com"] = exampleCert;
            certs["sub.example.com"] = subExampleCert;

            httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
            {
                if (name != null && certs.TryGetValue(name, out var cert))
                {
                    return cert;
                }

                return exampleCert;
            };
        });
    });
});
```

### <a name="connection-logging"></a><span data-ttu-id="85274-340">Registro de conexiones</span><span class="sxs-lookup"><span data-stu-id="85274-340">Connection logging</span></span>

<span data-ttu-id="85274-341">Llame a <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> para emitir registros de nivel de depuración para la comunicación a nivel de bytes en una conexión.</span><span class="sxs-lookup"><span data-stu-id="85274-341">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="85274-342">El registro de conexiones es útil para solucionar problemas en la comunicación de bajo nivel, como durante el cifrado TLS y detrás de los servidores proxy.</span><span class="sxs-lookup"><span data-stu-id="85274-342">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="85274-343">Si `UseConnectionLogging` se coloca antes de `UseHttps`, se registra el tráfico cifrado.</span><span class="sxs-lookup"><span data-stu-id="85274-343">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="85274-344">Si `UseConnectionLogging` se coloca después de `UseHttps`, se registra el tráfico descifrado.</span><span class="sxs-lookup"><span data-stu-id="85274-344">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="85274-345">Enlazar a un socket TCP</span><span class="sxs-lookup"><span data-stu-id="85274-345">Bind to a TCP socket</span></span>

<span data-ttu-id="85274-346">El método <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> se enlaza a un socket TCP y una expresión lambda de opciones permite configurar un certificado X.509:</span><span class="sxs-lookup"><span data-stu-id="85274-346">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=12-18)]

<span data-ttu-id="85274-347">En el ejemplo se configura HTTPS para un punto de conexión con <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span><span class="sxs-lookup"><span data-stu-id="85274-347">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="85274-348">Use la misma API para configurar otras opciones de Kestrel para puntos de conexión específicos.</span><span class="sxs-lookup"><span data-stu-id="85274-348">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="85274-349">Enlazar a un socket de Unix</span><span class="sxs-lookup"><span data-stu-id="85274-349">Bind to a Unix socket</span></span>

<span data-ttu-id="85274-350">Escuche en un socket de Unix con <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> para mejorar el rendimiento con Nginx, tal como se muestra en este ejemplo:</span><span class="sxs-lookup"><span data-stu-id="85274-350">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="85274-351">En el archivo de configuración de Nginx, establezca la entrada `server` > `location` > `proxy_pass` en `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span><span class="sxs-lookup"><span data-stu-id="85274-351">In the Nginx configuration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="85274-352">`{KESTREL SOCKET}` es el nombre del socket proporcionado para <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (por ejemplo, `kestrel-test.sock` en el ejemplo anterior).</span><span class="sxs-lookup"><span data-stu-id="85274-352">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="85274-353">Asegúrese de que el socket es grabable por Nginx (por ejemplo, `chmod go+w /tmp/kestrel-test.sock`).</span><span class="sxs-lookup"><span data-stu-id="85274-353">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span>

### <a name="port-0"></a><span data-ttu-id="85274-354">Puerto 0</span><span class="sxs-lookup"><span data-stu-id="85274-354">Port 0</span></span>

<span data-ttu-id="85274-355">Cuando se especifica el número de puerto `0`, Kestrel se enlaza de forma dinámica a un puerto disponible.</span><span class="sxs-lookup"><span data-stu-id="85274-355">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="85274-356">En el siguiente ejemplo se muestra cómo averiguar qué puerto Kestrel está realmente enlazado a un runtime:</span><span class="sxs-lookup"><span data-stu-id="85274-356">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="85274-357">Cuando la aplicación se ejecuta, la salida de la ventana de consola indica el puerto dinámico en el que se puede tener acceso a la aplicación:</span><span class="sxs-lookup"><span data-stu-id="85274-357">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="85274-358">Limitaciones</span><span class="sxs-lookup"><span data-stu-id="85274-358">Limitations</span></span>

<span data-ttu-id="85274-359">Configure puntos de conexión con los siguientes métodos:</span><span class="sxs-lookup"><span data-stu-id="85274-359">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="85274-360">El argumento de la línea de comandos `--urls`</span><span class="sxs-lookup"><span data-stu-id="85274-360">`--urls` command-line argument</span></span>
* <span data-ttu-id="85274-361">La clave de configuración de host `urls`</span><span class="sxs-lookup"><span data-stu-id="85274-361">`urls` host configuration key</span></span>
* <span data-ttu-id="85274-362">La variable de entorno `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="85274-362">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="85274-363">Estos métodos son útiles para que el código funcione con servidores que no sean de Kestrel.</span><span class="sxs-lookup"><span data-stu-id="85274-363">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="85274-364">Sin embargo, tenga en cuenta las siguientes limitaciones:</span><span class="sxs-lookup"><span data-stu-id="85274-364">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="85274-365">HTTPS no se puede usar con estos métodos, a menos que se proporcione un certificado predeterminado en la configuración del punto de conexión HTTPS (por ejemplo, por medio de la configuración `KestrelServerOptions` o de un archivo de configuración, tal y como se explicó anteriormente en este tema).</span><span class="sxs-lookup"><span data-stu-id="85274-365">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="85274-366">Cuando los métodos `Listen` y `UseUrls` se usan al mismo tiempo, los puntos de conexión de `Listen` sustituyen a los de `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="85274-366">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="85274-367">Configuración de puntos de conexión IIS</span><span class="sxs-lookup"><span data-stu-id="85274-367">IIS endpoint configuration</span></span>

<span data-ttu-id="85274-368">Cuando se usa IIS, los enlaces de direcciones URL de IIS reemplazan a los enlaces que se hayan establecido por medio de `Listen` o de `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="85274-368">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="85274-369">Para más información, vea el tema [Módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="85274-369">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="85274-370">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="85274-370">ListenOptions.Protocols</span></span>

<span data-ttu-id="85274-371">La propiedad `Protocols` establece los protocolos HTTP (`HttpProtocols`) habilitados en un punto de conexión o para el servidor.</span><span class="sxs-lookup"><span data-stu-id="85274-371">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="85274-372">Asigne un valor a la propiedad `Protocols` desde el valor de enumeración `HttpProtocols`.</span><span class="sxs-lookup"><span data-stu-id="85274-372">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="85274-373">Valor de enumeración `HttpProtocols`</span><span class="sxs-lookup"><span data-stu-id="85274-373">`HttpProtocols` enum value</span></span> | <span data-ttu-id="85274-374">Protocolo de conexión permitido</span><span class="sxs-lookup"><span data-stu-id="85274-374">Connection protocol permitted</span></span> |
| ---
<span data-ttu-id="85274-375">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="85274-375">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="85274-376">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="85274-376">'Blazor'</span></span>
- <span data-ttu-id="85274-377">"Identity"</span><span class="sxs-lookup"><span data-stu-id="85274-377">'Identity'</span></span>
- <span data-ttu-id="85274-378">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="85274-378">'Let's Encrypt'</span></span>
- <span data-ttu-id="85274-379">"Razor"</span><span class="sxs-lookup"><span data-stu-id="85274-379">'Razor'</span></span>
- <span data-ttu-id="85274-380">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="85274-380">'SignalR' uid:</span></span> 

-
<span data-ttu-id="85274-381">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="85274-381">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="85274-382">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="85274-382">'Blazor'</span></span>
- <span data-ttu-id="85274-383">"Identity"</span><span class="sxs-lookup"><span data-stu-id="85274-383">'Identity'</span></span>
- <span data-ttu-id="85274-384">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="85274-384">'Let's Encrypt'</span></span>
- <span data-ttu-id="85274-385">"Razor"</span><span class="sxs-lookup"><span data-stu-id="85274-385">'Razor'</span></span>
- <span data-ttu-id="85274-386">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="85274-386">'SignalR' uid:</span></span> 

-
<span data-ttu-id="85274-387">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="85274-387">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="85274-388">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="85274-388">'Blazor'</span></span>
- <span data-ttu-id="85274-389">"Identity"</span><span class="sxs-lookup"><span data-stu-id="85274-389">'Identity'</span></span>
- <span data-ttu-id="85274-390">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="85274-390">'Let's Encrypt'</span></span>
- <span data-ttu-id="85274-391">"Razor"</span><span class="sxs-lookup"><span data-stu-id="85274-391">'Razor'</span></span>
- <span data-ttu-id="85274-392">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="85274-392">'SignalR' uid:</span></span> 

-
<span data-ttu-id="85274-393">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="85274-393">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="85274-394">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="85274-394">'Blazor'</span></span>
- <span data-ttu-id="85274-395">"Identity"</span><span class="sxs-lookup"><span data-stu-id="85274-395">'Identity'</span></span>
- <span data-ttu-id="85274-396">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="85274-396">'Let's Encrypt'</span></span>
- <span data-ttu-id="85274-397">"Razor"</span><span class="sxs-lookup"><span data-stu-id="85274-397">'Razor'</span></span>
- <span data-ttu-id="85274-398">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="85274-398">'SignalR' uid:</span></span> 

-
<span data-ttu-id="85274-399">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="85274-399">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="85274-400">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="85274-400">'Blazor'</span></span>
- <span data-ttu-id="85274-401">"Identity"</span><span class="sxs-lookup"><span data-stu-id="85274-401">'Identity'</span></span>
- <span data-ttu-id="85274-402">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="85274-402">'Let's Encrypt'</span></span>
- <span data-ttu-id="85274-403">"Razor"</span><span class="sxs-lookup"><span data-stu-id="85274-403">'Razor'</span></span>
- <span data-ttu-id="85274-404">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="85274-404">'SignalR' uid:</span></span> 

-
<span data-ttu-id="85274-405">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="85274-405">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="85274-406">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="85274-406">'Blazor'</span></span>
- <span data-ttu-id="85274-407">"Identity"</span><span class="sxs-lookup"><span data-stu-id="85274-407">'Identity'</span></span>
- <span data-ttu-id="85274-408">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="85274-408">'Let's Encrypt'</span></span>
- <span data-ttu-id="85274-409">"Razor"</span><span class="sxs-lookup"><span data-stu-id="85274-409">'Razor'</span></span>
- <span data-ttu-id="85274-410">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="85274-410">'SignalR' uid:</span></span> 

-
<span data-ttu-id="85274-411">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="85274-411">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="85274-412">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="85274-412">'Blazor'</span></span>
- <span data-ttu-id="85274-413">"Identity"</span><span class="sxs-lookup"><span data-stu-id="85274-413">'Identity'</span></span>
- <span data-ttu-id="85274-414">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="85274-414">'Let's Encrypt'</span></span>
- <span data-ttu-id="85274-415">"Razor"</span><span class="sxs-lookup"><span data-stu-id="85274-415">'Razor'</span></span>
- <span data-ttu-id="85274-416">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="85274-416">'SignalR' uid:</span></span> 

-
<span data-ttu-id="85274-417">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="85274-417">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="85274-418">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="85274-418">'Blazor'</span></span>
- <span data-ttu-id="85274-419">"Identity"</span><span class="sxs-lookup"><span data-stu-id="85274-419">'Identity'</span></span>
- <span data-ttu-id="85274-420">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="85274-420">'Let's Encrypt'</span></span>
- <span data-ttu-id="85274-421">"Razor"</span><span class="sxs-lookup"><span data-stu-id="85274-421">'Razor'</span></span>
- <span data-ttu-id="85274-422">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="85274-422">'SignalR' uid:</span></span> 

-
<span data-ttu-id="85274-423">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="85274-423">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="85274-424">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="85274-424">'Blazor'</span></span>
- <span data-ttu-id="85274-425">"Identity"</span><span class="sxs-lookup"><span data-stu-id="85274-425">'Identity'</span></span>
- <span data-ttu-id="85274-426">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="85274-426">'Let's Encrypt'</span></span>
- <span data-ttu-id="85274-427">"Razor"</span><span class="sxs-lookup"><span data-stu-id="85274-427">'Razor'</span></span>
- <span data-ttu-id="85274-428">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="85274-428">'SignalR' uid:</span></span> 

-
<span data-ttu-id="85274-429">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="85274-429">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="85274-430">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="85274-430">'Blazor'</span></span>
- <span data-ttu-id="85274-431">"Identity"</span><span class="sxs-lookup"><span data-stu-id="85274-431">'Identity'</span></span>
- <span data-ttu-id="85274-432">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="85274-432">'Let's Encrypt'</span></span>
- <span data-ttu-id="85274-433">"Razor"</span><span class="sxs-lookup"><span data-stu-id="85274-433">'Razor'</span></span>
- <span data-ttu-id="85274-434">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="85274-434">'SignalR' uid:</span></span> 

-
<span data-ttu-id="85274-435">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="85274-435">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="85274-436">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="85274-436">'Blazor'</span></span>
- <span data-ttu-id="85274-437">"Identity"</span><span class="sxs-lookup"><span data-stu-id="85274-437">'Identity'</span></span>
- <span data-ttu-id="85274-438">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="85274-438">'Let's Encrypt'</span></span>
- <span data-ttu-id="85274-439">"Razor"</span><span class="sxs-lookup"><span data-stu-id="85274-439">'Razor'</span></span>
- <span data-ttu-id="85274-440">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="85274-440">'SignalR' uid:</span></span> 

<span data-ttu-id="85274-441">------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="85274-441">------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="85274-442">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="85274-442">'Blazor'</span></span>
- <span data-ttu-id="85274-443">"Identity"</span><span class="sxs-lookup"><span data-stu-id="85274-443">'Identity'</span></span>
- <span data-ttu-id="85274-444">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="85274-444">'Let's Encrypt'</span></span>
- <span data-ttu-id="85274-445">"Razor"</span><span class="sxs-lookup"><span data-stu-id="85274-445">'Razor'</span></span>
- <span data-ttu-id="85274-446">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="85274-446">'SignalR' uid:</span></span> 

-
<span data-ttu-id="85274-447">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="85274-447">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="85274-448">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="85274-448">'Blazor'</span></span>
- <span data-ttu-id="85274-449">"Identity"</span><span class="sxs-lookup"><span data-stu-id="85274-449">'Identity'</span></span>
- <span data-ttu-id="85274-450">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="85274-450">'Let's Encrypt'</span></span>
- <span data-ttu-id="85274-451">"Razor"</span><span class="sxs-lookup"><span data-stu-id="85274-451">'Razor'</span></span>
- <span data-ttu-id="85274-452">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="85274-452">'SignalR' uid:</span></span> 

-
<span data-ttu-id="85274-453">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="85274-453">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="85274-454">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="85274-454">'Blazor'</span></span>
- <span data-ttu-id="85274-455">"Identity"</span><span class="sxs-lookup"><span data-stu-id="85274-455">'Identity'</span></span>
- <span data-ttu-id="85274-456">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="85274-456">'Let's Encrypt'</span></span>
- <span data-ttu-id="85274-457">"Razor"</span><span class="sxs-lookup"><span data-stu-id="85274-457">'Razor'</span></span>
- <span data-ttu-id="85274-458">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="85274-458">'SignalR' uid:</span></span> 

-
<span data-ttu-id="85274-459">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="85274-459">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="85274-460">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="85274-460">'Blazor'</span></span>
- <span data-ttu-id="85274-461">"Identity"</span><span class="sxs-lookup"><span data-stu-id="85274-461">'Identity'</span></span>
- <span data-ttu-id="85274-462">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="85274-462">'Let's Encrypt'</span></span>
- <span data-ttu-id="85274-463">"Razor"</span><span class="sxs-lookup"><span data-stu-id="85274-463">'Razor'</span></span>
- <span data-ttu-id="85274-464">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="85274-464">'SignalR' uid:</span></span> 

-
<span data-ttu-id="85274-465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="85274-465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="85274-466">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="85274-466">'Blazor'</span></span>
- <span data-ttu-id="85274-467">"Identity"</span><span class="sxs-lookup"><span data-stu-id="85274-467">'Identity'</span></span>
- <span data-ttu-id="85274-468">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="85274-468">'Let's Encrypt'</span></span>
- <span data-ttu-id="85274-469">"Razor"</span><span class="sxs-lookup"><span data-stu-id="85274-469">'Razor'</span></span>
- <span data-ttu-id="85274-470">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="85274-470">'SignalR' uid:</span></span> 

-
<span data-ttu-id="85274-471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="85274-471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="85274-472">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="85274-472">'Blazor'</span></span>
- <span data-ttu-id="85274-473">"Identity"</span><span class="sxs-lookup"><span data-stu-id="85274-473">'Identity'</span></span>
- <span data-ttu-id="85274-474">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="85274-474">'Let's Encrypt'</span></span>
- <span data-ttu-id="85274-475">"Razor"</span><span class="sxs-lookup"><span data-stu-id="85274-475">'Razor'</span></span>
- <span data-ttu-id="85274-476">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="85274-476">'SignalR' uid:</span></span> 

-
<span data-ttu-id="85274-477">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="85274-477">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="85274-478">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="85274-478">'Blazor'</span></span>
- <span data-ttu-id="85274-479">"Identity"</span><span class="sxs-lookup"><span data-stu-id="85274-479">'Identity'</span></span>
- <span data-ttu-id="85274-480">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="85274-480">'Let's Encrypt'</span></span>
- <span data-ttu-id="85274-481">"Razor"</span><span class="sxs-lookup"><span data-stu-id="85274-481">'Razor'</span></span>
- <span data-ttu-id="85274-482">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="85274-482">'SignalR' uid:</span></span> 

-
<span data-ttu-id="85274-483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="85274-483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="85274-484">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="85274-484">'Blazor'</span></span>
- <span data-ttu-id="85274-485">"Identity"</span><span class="sxs-lookup"><span data-stu-id="85274-485">'Identity'</span></span>
- <span data-ttu-id="85274-486">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="85274-486">'Let's Encrypt'</span></span>
- <span data-ttu-id="85274-487">"Razor"</span><span class="sxs-lookup"><span data-stu-id="85274-487">'Razor'</span></span>
- <span data-ttu-id="85274-488">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="85274-488">'SignalR' uid:</span></span> 

-
<span data-ttu-id="85274-489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="85274-489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="85274-490">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="85274-490">'Blazor'</span></span>
- <span data-ttu-id="85274-491">"Identity"</span><span class="sxs-lookup"><span data-stu-id="85274-491">'Identity'</span></span>
- <span data-ttu-id="85274-492">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="85274-492">'Let's Encrypt'</span></span>
- <span data-ttu-id="85274-493">"Razor"</span><span class="sxs-lookup"><span data-stu-id="85274-493">'Razor'</span></span>
- <span data-ttu-id="85274-494">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="85274-494">'SignalR' uid:</span></span> 

-
<span data-ttu-id="85274-495">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="85274-495">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="85274-496">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="85274-496">'Blazor'</span></span>
- <span data-ttu-id="85274-497">"Identity"</span><span class="sxs-lookup"><span data-stu-id="85274-497">'Identity'</span></span>
- <span data-ttu-id="85274-498">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="85274-498">'Let's Encrypt'</span></span>
- <span data-ttu-id="85274-499">"Razor"</span><span class="sxs-lookup"><span data-stu-id="85274-499">'Razor'</span></span>
- <span data-ttu-id="85274-500">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="85274-500">'SignalR' uid:</span></span> 

-
<span data-ttu-id="85274-501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="85274-501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="85274-502">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="85274-502">'Blazor'</span></span>
- <span data-ttu-id="85274-503">"Identity"</span><span class="sxs-lookup"><span data-stu-id="85274-503">'Identity'</span></span>
- <span data-ttu-id="85274-504">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="85274-504">'Let's Encrypt'</span></span>
- <span data-ttu-id="85274-505">"Razor"</span><span class="sxs-lookup"><span data-stu-id="85274-505">'Razor'</span></span>
- <span data-ttu-id="85274-506">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="85274-506">'SignalR' uid:</span></span> 

-
<span data-ttu-id="85274-507">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="85274-507">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="85274-508">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="85274-508">'Blazor'</span></span>
- <span data-ttu-id="85274-509">"Identity"</span><span class="sxs-lookup"><span data-stu-id="85274-509">'Identity'</span></span>
- <span data-ttu-id="85274-510">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="85274-510">'Let's Encrypt'</span></span>
- <span data-ttu-id="85274-511">"Razor"</span><span class="sxs-lookup"><span data-stu-id="85274-511">'Razor'</span></span>
- <span data-ttu-id="85274-512">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="85274-512">'SignalR' uid:</span></span> 

<span data-ttu-id="85274-513">--------------- | | `Http1`                    | Solo HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="85274-513">--------------- | | `Http1`                    | HTTP/1.1 only.</span></span> <span data-ttu-id="85274-514">Puede usarse con o sin TLS.</span><span class="sxs-lookup"><span data-stu-id="85274-514">Can be used with or without TLS.</span></span> <span data-ttu-id="85274-515">| | `Http2`                    | Solo HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="85274-515">| | `Http2`                    | HTTP/2 only.</span></span> <span data-ttu-id="85274-516">Se pueden utilizar sin TLS solo si el cliente admite un [modo de conocimientos previos](https://tools.ietf.org/html/rfc7540#section-3.4).</span><span class="sxs-lookup"><span data-stu-id="85274-516">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> <span data-ttu-id="85274-517">| | `Http1AndHttp2`            | HTTP/1.1 y HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="85274-517">| | `Http1AndHttp2`            | HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="85274-518">HTTP/2 necesita que el cliente seleccione HTTP/2 en el protocolo de enlace [Negociación de protocolo de nivel de aplicación (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) de TLS; en caso contrario, el valor predeterminado de la conexión es HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="85274-518">HTTP/2 requires the client to select HTTP/2 in the TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="85274-519">El valor `ListenOptions.Protocols` predeterminado de cualquier punto de conexión es `HttpProtocols.Http1AndHttp2`.</span><span class="sxs-lookup"><span data-stu-id="85274-519">The default `ListenOptions.Protocols` value for any endpoint is `HttpProtocols.Http1AndHttp2`.</span></span>

<span data-ttu-id="85274-520">Restricciones de TLS para HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="85274-520">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="85274-521">TLS 1.2 o versiones posteriores</span><span class="sxs-lookup"><span data-stu-id="85274-521">TLS version 1.2 or later</span></span>
* <span data-ttu-id="85274-522">Renegociación deshabilitada</span><span class="sxs-lookup"><span data-stu-id="85274-522">Renegotiation disabled</span></span>
* <span data-ttu-id="85274-523">Compresión deshabilitada</span><span class="sxs-lookup"><span data-stu-id="85274-523">Compression disabled</span></span>
* <span data-ttu-id="85274-524">Tamaños de intercambio de claves efímeras mínimos:</span><span class="sxs-lookup"><span data-stu-id="85274-524">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="85274-525">Curva elíptica Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits como mínimo</span><span class="sxs-lookup"><span data-stu-id="85274-525">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits minimum</span></span>
  * <span data-ttu-id="85274-526">Campo finito Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits como mínimo</span><span class="sxs-lookup"><span data-stu-id="85274-526">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits minimum</span></span>
* <span data-ttu-id="85274-527">Conjunto de cifrado no restringido</span><span class="sxs-lookup"><span data-stu-id="85274-527">Cipher suite not blacklisted</span></span>

<span data-ttu-id="85274-528">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; con la curva elíptica P-256 &lbrack;`FIPS186`&rbrack; es compatible de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="85274-528">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="85274-529">El siguiente ejemplo permite conexiones HTTP/1.1 y HTTP/2 en el puerto 8000.</span><span class="sxs-lookup"><span data-stu-id="85274-529">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="85274-530">Las conexiones se protegen mediante TLS con un certificado proporcionado:</span><span class="sxs-lookup"><span data-stu-id="85274-530">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="85274-531">Si es necesario, use el middleware de conexión para filtrar los protocolos de enlace TLS por conexión en el caso de cifrados específicos:</span><span class="sxs-lookup"><span data-stu-id="85274-531">Use Connection Middleware to filter TLS handshakes on a per-connection basis for specific ciphers if required.</span></span>

<span data-ttu-id="85274-532">En el ejemplo siguiente se produce una excepción <xref:System.NotSupportedException> con cualquier algoritmo de cifrado que no admita la aplicación.</span><span class="sxs-lookup"><span data-stu-id="85274-532">The following example throws <xref:System.NotSupportedException> for any cipher algorithm that the app doesn't support.</span></span> <span data-ttu-id="85274-533">Como alternativa, defina y compare [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) con una lista de conjuntos de cifrado aceptables.</span><span class="sxs-lookup"><span data-stu-id="85274-533">Alternatively, define and compare [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) to a list of acceptable cipher suites.</span></span>

<span data-ttu-id="85274-534">No se usa ningún cifrado con un algoritmo de cifrado [CipherAlgorithmType.Null ](xref:System.Security.Authentication.CipherAlgorithmType).</span><span class="sxs-lookup"><span data-stu-id="85274-534">No encryption is used with a [CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) cipher algorithm.</span></span>

```csharp
// using System.Net;
// using Microsoft.AspNetCore.Connections;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.UseTlsFilter();
    });
});
```

```csharp
using System;
using System.Security.Authentication;
using Microsoft.AspNetCore.Connections.Features;

namespace Microsoft.AspNetCore.Connections
{
    public static class TlsFilterConnectionMiddlewareExtensions
    {
        public static IConnectionBuilder UseTlsFilter(
            this IConnectionBuilder builder)
        {
            return builder.Use((connection, next) =>
            {
                var tlsFeature = connection.Features.Get<ITlsHandshakeFeature>();

                if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
                {
                    throw new NotSupportedException("Prohibited cipher: " +
                        tlsFeature.CipherAlgorithm);
                }

                return next();
            });
        }
    }
}
```

<span data-ttu-id="85274-535">El filtrado de conexiones también puede configurarse mediante una función lambda <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder>:</span><span class="sxs-lookup"><span data-stu-id="85274-535">Connection filtering can also be configured via an <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda:</span></span>

```csharp
// using System;
// using System.Net;
// using System.Security.Authentication;
// using Microsoft.AspNetCore.Connections;
// using Microsoft.AspNetCore.Connections.Features;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.Use((context, next) =>
        {
            var tlsFeature = context.Features.Get<ITlsHandshakeFeature>();

            if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
            {
                throw new NotSupportedException(
                    $"Prohibited cipher: {tlsFeature.CipherAlgorithm}");
            }

            return next();
        });
    });
});
```

<span data-ttu-id="85274-536">En Linux, se puede usar <xref:System.Net.Security.CipherSuitesPolicy> para filtrar los protocolos de enlace TLS por conexión:</span><span class="sxs-lookup"><span data-stu-id="85274-536">On Linux, <xref:System.Net.Security.CipherSuitesPolicy> can be used to filter TLS handshakes on a per-connection basis:</span></span>

```csharp
// using System.Net.Security;
// using Microsoft.AspNetCore.Hosting;
// using Microsoft.AspNetCore.Server.Kestrel.Core;
// using Microsoft.Extensions.DependencyInjection;
// using Microsoft.Extensions.Hosting;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.OnAuthenticate = (context, sslOptions) =>
        {
            sslOptions.CipherSuitesPolicy = new CipherSuitesPolicy(
                new[]
                {
                    TlsCipherSuite.TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256,
                    TlsCipherSuite.TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384,
                    // ...
                });
        };
    });
});
```

<span data-ttu-id="85274-537">*Defina el protocolo a partir de la configuración*</span><span class="sxs-lookup"><span data-stu-id="85274-537">*Set the protocol from configuration*</span></span>

<span data-ttu-id="85274-538">`CreateDefaultBuilder` llama a `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` de forma predeterminada para cargar la configuración de Kestrel.</span><span class="sxs-lookup"><span data-stu-id="85274-538">`CreateDefaultBuilder` calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="85274-539">En el siguiente ejemplo de *appsettings.json*, se establece HTTP/1.1 como el protocolo de conexión predeterminado para todos los puntos de conexión:</span><span class="sxs-lookup"><span data-stu-id="85274-539">The following *appsettings.json* example establishes HTTP/1.1 as the default connection protocol for all endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1"
    }
  }
}
```

<span data-ttu-id="85274-540">En el siguiente ejemplo de *appsettings.json* se establece el protocolo de conexión HTTP/1.1 para un punto de conexión específico:</span><span class="sxs-lookup"><span data-stu-id="85274-540">The following *appsettings.json* example establishes the HTTP/1.1 connection protocol for a specific endpoint:</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "HttpsDefaultCert": {
        "Url": "https://localhost:5001",
        "Protocols": "Http1"
      }
    }
  }
}
```

<span data-ttu-id="85274-541">Los protocolos especificados en el código invalidan los valores establecidos por la configuración.</span><span class="sxs-lookup"><span data-stu-id="85274-541">Protocols specified in code override values set by configuration.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="85274-542">Configuración de transporte</span><span class="sxs-lookup"><span data-stu-id="85274-542">Transport configuration</span></span>

<span data-ttu-id="85274-543">Para los proyectos que necesitan el uso de Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>):</span><span class="sxs-lookup"><span data-stu-id="85274-543">For projects that require the use of Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>):</span></span>

* <span data-ttu-id="85274-544">Agregar una dependencia del paquete [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) al archivo de proyecto de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="85274-544">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

   ```xml
   <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                     Version="{VERSION}" />
   ```

* <span data-ttu-id="85274-545">Llame a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> en `IWebHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="85274-545">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> on the `IWebHostBuilder`:</span></span>

   ```csharp
   public class Program
   {
       public static void Main(string[] args)
       {
           CreateHostBuilder(args).Build().Run();
       }

       public static IHostBuilder CreateHostBuilder(string[] args) =>
           Host.CreateDefaultBuilder(args)
               .ConfigureWebHostDefaults(webBuilder =>
               {
                   webBuilder.UseLibuv();
                   webBuilder.UseStartup<Startup>();
               });
   }
   ```

### <a name="url-prefixes"></a><span data-ttu-id="85274-546">Prefijos de URL</span><span class="sxs-lookup"><span data-stu-id="85274-546">URL prefixes</span></span>

<span data-ttu-id="85274-547">Al usar `UseUrls`, el argumento de línea de comandos `--urls`, la clave de configuración de host `urls` o una variable de entorno `ASPNETCORE_URLS`, los prefijos de dirección URL pueden tener cualquiera de estos formatos.</span><span class="sxs-lookup"><span data-stu-id="85274-547">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="85274-548">Solo son válidos los prefijos de dirección URL HTTP.</span><span class="sxs-lookup"><span data-stu-id="85274-548">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="85274-549">Kestrel no admite HTTPS al configurar enlaces de dirección URL con `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="85274-549">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="85274-550">Dirección IPv4 con número de puerto</span><span class="sxs-lookup"><span data-stu-id="85274-550">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="85274-551">`0.0.0.0` es un caso especial que enlaza a todas las direcciones IPv4.</span><span class="sxs-lookup"><span data-stu-id="85274-551">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="85274-552">Dirección IPv6 con número de puerto</span><span class="sxs-lookup"><span data-stu-id="85274-552">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="85274-553">`[::]` es el equivalente en IPv6 de `0.0.0.0` en IPv4.</span><span class="sxs-lookup"><span data-stu-id="85274-553">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="85274-554">Nombre de host con número de puerto</span><span class="sxs-lookup"><span data-stu-id="85274-554">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="85274-555">Los nombres de host, `*` y `+` no son especiales.</span><span class="sxs-lookup"><span data-stu-id="85274-555">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="85274-556">Todo lo que no se identifique como una dirección IP o un `localhost` válido se enlaza a todas las direcciones IP de IPv6 e IPv4.</span><span class="sxs-lookup"><span data-stu-id="85274-556">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="85274-557">Para enlazar distintos nombres de host a distintas aplicaciones ASP.NET Core en el mismo puerto, use [HTTP.sys](xref:fundamentals/servers/httpsys) o un servidor proxy inverso, como IIS, Nginx o Apache.</span><span class="sxs-lookup"><span data-stu-id="85274-557">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="85274-558">El hospedaje en una configuración de proxy inverso requiere [filtrado de hosts](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="85274-558">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="85274-559">Nombre `localhost` del host con el número de puerto o la IP de bucle invertido con el número de puerto</span><span class="sxs-lookup"><span data-stu-id="85274-559">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="85274-560">Cuando se especifica `localhost`, Kestrel intenta enlazar a las interfaces de bucle invertido de IPv4 e IPv6.</span><span class="sxs-lookup"><span data-stu-id="85274-560">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="85274-561">Si el puerto solicitado lo está usando otro servicio en cualquier interfaz de bucle invertido, Kestrel no se puede iniciar.</span><span class="sxs-lookup"><span data-stu-id="85274-561">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="85274-562">Si ninguna de estas interfaces de bucle invertido está disponible por cualquier otra razón (normalmente porque no se admite IPv6), Kestrel registra una advertencia.</span><span class="sxs-lookup"><span data-stu-id="85274-562">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="85274-563">Filtrado de hosts</span><span class="sxs-lookup"><span data-stu-id="85274-563">Host filtering</span></span>

<span data-ttu-id="85274-564">Si bien Kestrel admite una configuración basada en prefijos como `http://example.com:5000`, pasa por alto completamente el nombre de host.</span><span class="sxs-lookup"><span data-stu-id="85274-564">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="85274-565">El host `localhost` es un caso especial que se usa para enlazar a direcciones de bucle invertido.</span><span class="sxs-lookup"><span data-stu-id="85274-565">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="85274-566">Cualquier otro host que no sea una dirección IP explícita se enlaza a todas las direcciones IP públicas.</span><span class="sxs-lookup"><span data-stu-id="85274-566">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="85274-567">Los encabezados `Host` no están validados.</span><span class="sxs-lookup"><span data-stu-id="85274-567">`Host` headers aren't validated.</span></span>

<span data-ttu-id="85274-568">Como solución alternativa, use el Middleware de filtrado de hosts.</span><span class="sxs-lookup"><span data-stu-id="85274-568">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="85274-569">El middleware de filtrado de host lo proporciona el paquete [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering), que se proporciona implícitamente para aplicaciones ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="85274-569">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is implicitly provided for ASP.NET Core apps.</span></span> <span data-ttu-id="85274-570">El middleware se agrega por medio de <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, que llama a <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span><span class="sxs-lookup"><span data-stu-id="85274-570">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="85274-571">El Middleware de filtrado de hosts está deshabilitado de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="85274-571">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="85274-572">Para habilitarlo, defina una clave `AllowedHosts` en *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span><span class="sxs-lookup"><span data-stu-id="85274-572">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="85274-573">El valor es una lista delimitada por punto y coma de nombres de host sin los números de puerto:</span><span class="sxs-lookup"><span data-stu-id="85274-573">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="85274-574">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="85274-574">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="85274-575">[Middleware de encabezados reenviados](xref:host-and-deploy/proxy-load-balancer) también tiene una opción <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts>.</span><span class="sxs-lookup"><span data-stu-id="85274-575">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="85274-576">El Middleware de encabezados reenviados y el Middleware de filtrado de hosts tienen una funcionalidad similar en diferentes escenarios.</span><span class="sxs-lookup"><span data-stu-id="85274-576">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="85274-577">Establecer `AllowedHosts` con el Middleware de encabezados reenviados es adecuado cuando el encabezado `Host` no se conserva mientras se reenvían solicitudes con un servidor proxy inverso o un equilibrador de carga.</span><span class="sxs-lookup"><span data-stu-id="85274-577">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="85274-578">Establecer `AllowedHosts` con el Middleware de filtrado de hosts es adecuado cuando se usa Kestrel como un servidor perimetral de acceso público o cuando el encabezado `Host` se reenvía directamente.</span><span class="sxs-lookup"><span data-stu-id="85274-578">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="85274-579">Para obtener más información sobre el Middleware de encabezados reenviados, consulte <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="85274-579">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="85274-580">Kestrel es un [servidor web multiplataforma de ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="85274-580">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="85274-581">Kestrel es el servidor web que se incluye de forma predeterminada en las plantillas de proyecto de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="85274-581">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="85274-582">Kestrel admite los siguientes escenarios:</span><span class="sxs-lookup"><span data-stu-id="85274-582">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="85274-583">HTTPS</span><span class="sxs-lookup"><span data-stu-id="85274-583">HTTPS</span></span>
* <span data-ttu-id="85274-584">Actualización opaca para habilitar [WebSockets](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="85274-584">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="85274-585">Sockets de Unix para alto rendimiento detrás de Nginx</span><span class="sxs-lookup"><span data-stu-id="85274-585">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="85274-586">HTTP/2 (excepto en macOS&dagger;)</span><span class="sxs-lookup"><span data-stu-id="85274-586">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="85274-587">&dagger;HTTP/2 se admitirá en una versión futura en macOS.</span><span class="sxs-lookup"><span data-stu-id="85274-587">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="85274-588">Kestrel admite todas las plataformas y versiones que sean compatibles con .NET Core.</span><span class="sxs-lookup"><span data-stu-id="85274-588">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="85274-589">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="85274-589">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="85274-590">Compatibilidad con HTTP/2</span><span class="sxs-lookup"><span data-stu-id="85274-590">HTTP/2 support</span></span>

<span data-ttu-id="85274-591">[HTTP/2](https://httpwg.org/specs/rfc7540.html) está disponible para las aplicaciones de ASP.NET Core si se cumplen los siguientes requisitos básicos:</span><span class="sxs-lookup"><span data-stu-id="85274-591">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="85274-592">Sistema operativo&dagger;</span><span class="sxs-lookup"><span data-stu-id="85274-592">Operating system&dagger;</span></span>
  * <span data-ttu-id="85274-593">Windows Server 2016/Windows 10 o posterior&Dagger;</span><span class="sxs-lookup"><span data-stu-id="85274-593">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="85274-594">Linux con OpenSSL 1.0.2 o posterior (por ejemplo, Ubuntu 16.04 o posterior)</span><span class="sxs-lookup"><span data-stu-id="85274-594">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="85274-595">Plataforma de destino: .NET Core 2.2 o posterior</span><span class="sxs-lookup"><span data-stu-id="85274-595">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="85274-596">Conexión con [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3)</span><span class="sxs-lookup"><span data-stu-id="85274-596">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="85274-597">Conexión con TLS 1.2 o una versión posterior</span><span class="sxs-lookup"><span data-stu-id="85274-597">TLS 1.2 or later connection</span></span>

<span data-ttu-id="85274-598">&dagger;HTTP/2 se admitirá en una versión futura en macOS.</span><span class="sxs-lookup"><span data-stu-id="85274-598">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="85274-599">&Dagger;Kestrel tiene compatibilidad limitada para HTTP/2 en Windows Server 2012 R2 y Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="85274-599">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="85274-600">La compatibilidad es limitada porque la lista de conjuntos de cifrado TLS admitidos y disponibles en estos sistemas operativos está limitada.</span><span class="sxs-lookup"><span data-stu-id="85274-600">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="85274-601">Se puede requerir un certificado generado mediante Elliptic Curve Digital Signature Algorithm (ECDSA) para proteger las conexiones TLS.</span><span class="sxs-lookup"><span data-stu-id="85274-601">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="85274-602">Si se establece una conexión HTTP/2, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) notifica `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="85274-602">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="85274-603">HTTP/2 está deshabilitado de manera predeterminada.</span><span class="sxs-lookup"><span data-stu-id="85274-603">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="85274-604">Para obtener más información sobre la configuración, consulte las secciones [Opciones de Kestrel](#kestrel-options) y [ListenOptions.Protocols](#listenoptionsprotocols).</span><span class="sxs-lookup"><span data-stu-id="85274-604">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="85274-605">Cuándo usar Kestrel con un proxy inverso</span><span class="sxs-lookup"><span data-stu-id="85274-605">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="85274-606">Kestrel se puede usar por sí solo o con un *servidor proxy inverso*, como [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org) o [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="85274-606">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="85274-607">Un servidor proxy inverso recibe las solicitudes HTTP de la red y las reenvía a Kestrel.</span><span class="sxs-lookup"><span data-stu-id="85274-607">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="85274-608">Kestrel empleado como servidor web perimetral (accesible desde Internet):</span><span class="sxs-lookup"><span data-stu-id="85274-608">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel se comunica directamente con Internet sin ningún servidor proxy inverso](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="85274-610">Kestrel empleado en una configuración de proxy inverso:</span><span class="sxs-lookup"><span data-stu-id="85274-610">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel se comunica indirectamente con Internet a través de un servidor proxy inverso, como IIS, Nginx o Apache](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="85274-612">Cualquiera de las configuraciones, con o sin servidor proxy inverso, es una configuración de hospedaje admitida.</span><span class="sxs-lookup"><span data-stu-id="85274-612">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="85274-613">Kestrel, utilizado como un servidor perimetral sin un servidor proxy inverso, no permite compartir la misma dirección IP y el mismo puerto entre varios procesos.</span><span class="sxs-lookup"><span data-stu-id="85274-613">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="85274-614">Si Kestrel se configura para escuchar en un puerto, controla todo el tráfico de ese puerto, independientemente de los encabezados `Host` de las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="85274-614">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="85274-615">Un proxy inverso que puede compartir puertos es capaz de reenviar solicitudes a Kestrel en una única dirección IP y puerto.</span><span class="sxs-lookup"><span data-stu-id="85274-615">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="85274-616">Aunque no sea necesario un servidor proxy inverso, su uso puede ser útil.</span><span class="sxs-lookup"><span data-stu-id="85274-616">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="85274-617">Un proxy inverso puede hacer lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="85274-617">A reverse proxy:</span></span>

* <span data-ttu-id="85274-618">Limitar el área expuesta públicamente de las aplicaciones que hospeda.</span><span class="sxs-lookup"><span data-stu-id="85274-618">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="85274-619">Proporcionar una capa extra de configuración y defensa.</span><span class="sxs-lookup"><span data-stu-id="85274-619">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="85274-620">Posiblemente, integrarse mejor con la infraestructura existente.</span><span class="sxs-lookup"><span data-stu-id="85274-620">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="85274-621">Simplificar el equilibrio de carga y la configuración de una comunicación segura (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="85274-621">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="85274-622">Solamente el servidor proxy inverso necesita un certificado X.509 y dicho servidor se puede comunicar con los servidores de la aplicación en la red interna por medio de HTTP sin formato.</span><span class="sxs-lookup"><span data-stu-id="85274-622">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="85274-623">El hospedaje en una configuración de proxy inverso requiere [filtrado de hosts](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="85274-623">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="85274-624">Cómo usar Kestrel en aplicaciones ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="85274-624">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="85274-625">El paquete [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) se incluye en el [metapaquete Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="85274-625">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="85274-626">Las plantillas de proyecto de ASP.NET Core usan Kestrel de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="85274-626">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="85274-627">En *Program.cs*, el código de plantilla llama a <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, que a su vez llama a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> en segundo plano.</span><span class="sxs-lookup"><span data-stu-id="85274-627">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

<span data-ttu-id="85274-628">Para más información sobre `CreateDefaultBuilder` y la compilación del host, consulte la sección sobre *cómo configurar un host* de <xref:fundamentals/host/web-host#set-up-a-host>.</span><span class="sxs-lookup"><span data-stu-id="85274-628">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

<span data-ttu-id="85274-629">Para proporcionar configuración adicional después de llamar a `CreateDefaultBuilder`, use `ConfigureKestrel`:</span><span class="sxs-lookup"><span data-stu-id="85274-629">To provide additional configuration after calling `CreateDefaultBuilder`, use `ConfigureKestrel`:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="85274-630">Si la aplicación no llama a `CreateDefaultBuilder` para configurar el host, llame a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>**antes** de llamar a `ConfigureKestrel`:</span><span class="sxs-lookup"><span data-stu-id="85274-630">If the app doesn't call `CreateDefaultBuilder` to set up the host, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **before** calling `ConfigureKestrel`:</span></span>

```csharp
public static void Main(string[] args)
{
    var host = new WebHostBuilder()
        .UseContentRoot(Directory.GetCurrentDirectory())
        .UseKestrel()
        .UseIISIntegration()
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        })
        .Build();

    host.Run();
}
```

## <a name="kestrel-options"></a><span data-ttu-id="85274-631">Opciones de Kestrel</span><span class="sxs-lookup"><span data-stu-id="85274-631">Kestrel options</span></span>

<span data-ttu-id="85274-632">El servidor web Kestrel tiene opciones de configuración de restricción que son especialmente útiles en las implementaciones con conexión a Internet.</span><span class="sxs-lookup"><span data-stu-id="85274-632">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="85274-633">Establezca restricciones en la propiedad <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> de la clase <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>.</span><span class="sxs-lookup"><span data-stu-id="85274-633">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="85274-634">La propiedad `Limits` contiene una instancia de la clase <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>.</span><span class="sxs-lookup"><span data-stu-id="85274-634">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="85274-635">En los ejemplos siguientes se usa el espacio de nombres <xref:Microsoft.AspNetCore.Server.Kestrel.Core>:</span><span class="sxs-lookup"><span data-stu-id="85274-635">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="85274-636">Las opciones de Kestrel, que se configuran en código de C# en los siguientes ejemplos, también se pueden establecer mediante un [proveedor de configuración](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="85274-636">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="85274-637">Por ejemplo, el proveedor de configuración de archivos puede cargar la configuración de Kestrel desde un archivo *appsettings.json* o *appsettings.{Environment}.json*:</span><span class="sxs-lookup"><span data-stu-id="85274-637">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    }
  }
}
```

<span data-ttu-id="85274-638">Siga **uno** de estos procedimientos:</span><span class="sxs-lookup"><span data-stu-id="85274-638">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="85274-639">Configure Kestrel en `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="85274-639">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="85274-640">Inserte una instancia de `IConfiguration` en la clase `Startup`.</span><span class="sxs-lookup"><span data-stu-id="85274-640">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="85274-641">En el ejemplo siguiente se da por supuesto que la configuración insertada está asignada a la propiedad `Configuration`.</span><span class="sxs-lookup"><span data-stu-id="85274-641">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="85274-642">En `Startup.ConfigureServices`, cargue la sección de configuración `Kestrel` en la configuración de Kestrel:</span><span class="sxs-lookup"><span data-stu-id="85274-642">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

     ```csharp
     using Microsoft.Extensions.Configuration
     
     public class Startup
     {
         public Startup(IConfiguration configuration)
         {
             Configuration = configuration;
         }

         public IConfiguration Configuration { get; }

         public void ConfigureServices(IServiceCollection services)
         {
             services.Configure<KestrelServerOptions>(
                 Configuration.GetSection("Kestrel"));
         }

         public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
         {
             ...
         }
     }
     ```

* <span data-ttu-id="85274-643">Configure Kestrel al compilar el host:</span><span class="sxs-lookup"><span data-stu-id="85274-643">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="85274-644">En *Program.cs*, cargue la sección de configuración `Kestrel` en la configuración de Kestrel:</span><span class="sxs-lookup"><span data-stu-id="85274-644">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
      WebHost.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .UseStartup<Startup>();
  ```

<span data-ttu-id="85274-645">Los dos procedimientos anteriores funcionan con cualquier [proveedor de configuración](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="85274-645">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="85274-646">Tiempo de expiración de la conexión persistente</span><span class="sxs-lookup"><span data-stu-id="85274-646">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="85274-647">Obtiene o establece el [tiempo de expiración de la conexión persistente](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="85274-647">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="85274-648">El valor predeterminado es de 2 minutos.</span><span class="sxs-lookup"><span data-stu-id="85274-648">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=15)]

### <a name="maximum-client-connections"></a><span data-ttu-id="85274-649">Las conexiones máximas de cliente</span><span class="sxs-lookup"><span data-stu-id="85274-649">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="85274-650">El número máximo de conexiones de TCP abiertas simultáneas que se pueden establecer para toda la aplicación con este código:</span><span class="sxs-lookup"><span data-stu-id="85274-650">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="85274-651">Hay un límite independiente para las conexiones que se han actualizado desde HTTP o HTTPS a otro protocolo (por ejemplo, en una solicitud de WebSockets).</span><span class="sxs-lookup"><span data-stu-id="85274-651">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="85274-652">Cuando se actualiza una conexión, no se cuenta con respecto al límite de `MaxConcurrentConnections`.</span><span class="sxs-lookup"><span data-stu-id="85274-652">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="85274-653">El número máximo de conexiones es ilimitado de forma predeterminada (null).</span><span class="sxs-lookup"><span data-stu-id="85274-653">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="85274-654">El tamaño máximo del cuerpo de solicitud</span><span class="sxs-lookup"><span data-stu-id="85274-654">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="85274-655">El tamaño máximo predeterminado del cuerpo de solicitud es 30 000 000 bytes, que son aproximadamente 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="85274-655">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="85274-656">El método recomendado para invalidar el límite de una aplicación ASP.NET Core MVC es usar el atributo <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> en un método de acción:</span><span class="sxs-lookup"><span data-stu-id="85274-656">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="85274-657">Este es un ejemplo que muestra cómo configurar la restricción en la aplicación y todas las solicitudes:</span><span class="sxs-lookup"><span data-stu-id="85274-657">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="85274-658">Invalide la configuración en una solicitud específica de middleware:</span><span class="sxs-lookup"><span data-stu-id="85274-658">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="85274-659">Se inicia una excepción si la aplicación configura el límite de una solicitud después de que la aplicación haya empezado a leer la solicitud.</span><span class="sxs-lookup"><span data-stu-id="85274-659">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="85274-660">Hay una propiedad `IsReadOnly` que señala si la propiedad `MaxRequestBodySize` tiene el estado de solo lectura, lo que significa que es demasiado tarde para configurar el límite.</span><span class="sxs-lookup"><span data-stu-id="85274-660">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="85274-661">Cuando se ejecuta una aplicación [fuera de proceso](xref:host-and-deploy/iis/index#out-of-process-hosting-model) detrás del [módulo de ASP.NET Core](xref:host-and-deploy/aspnet-core-module), el límite de tamaño del cuerpo de la solicitud de Kestrel se deshabilita porque IIS ya establece el límite.</span><span class="sxs-lookup"><span data-stu-id="85274-661">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="85274-662">La velocidad mínima de los datos del cuerpo de solicitud.</span><span class="sxs-lookup"><span data-stu-id="85274-662">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="85274-663">Kestrel comprueba cada segundo si los datos entran a la velocidad especificada en bytes por segundo.</span><span class="sxs-lookup"><span data-stu-id="85274-663">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="85274-664">Si la velocidad está por debajo del mínimo, se agota el tiempo de espera de la conexión. El período de gracia es la cantidad de tiempo que Kestrel da al cliente para aumentar su velocidad de envío hasta el mínimo; no se comprueba la velocidad durante ese tiempo.</span><span class="sxs-lookup"><span data-stu-id="85274-664">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="85274-665">Este período de gracia permite evitar que se interrumpan las conexiones que inicialmente están enviando datos a una velocidad lenta debido a un inicio lento de TCP.</span><span class="sxs-lookup"><span data-stu-id="85274-665">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="85274-666">La velocidad mínima predeterminada es 240 bytes por segundo, con un período de gracia de cinco segundos.</span><span class="sxs-lookup"><span data-stu-id="85274-666">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="85274-667">También se aplica una velocidad mínima a la respuesta.</span><span class="sxs-lookup"><span data-stu-id="85274-667">A minimum rate also applies to the response.</span></span> <span data-ttu-id="85274-668">El código para establecer el límite de solicitudes y el límite de respuestas es el mismo, salvo que tienen `RequestBody` o `Response` en los nombres de propiedad y de interfaz.</span><span class="sxs-lookup"><span data-stu-id="85274-668">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="85274-669">Este es un ejemplo que muestra cómo configurar las velocidades de datos mínimas en *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="85274-669">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-9)]

<span data-ttu-id="85274-670">Invalide los límites de velocidad mínima por solicitud en el middleware:</span><span class="sxs-lookup"><span data-stu-id="85274-670">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="85274-671">Ninguna de las características de velocidad a las que se hace referencia en el ejemplo anterior están presentes en `HttpContext.Features` para las solicitudes HTTP/2, porque no se permite modificar los límites de velocidad por solicitud en HTTP/2 debido a la compatibilidad del protocolo con la multiplexación de solicitudes.</span><span class="sxs-lookup"><span data-stu-id="85274-671">Neither rate feature referenced in the prior sample are present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis isn't supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="85274-672">Se siguen aplicando límites de velocidad en todo el servidor configurados con `KestrelServerOptions.Limits` a las conexiones HTTP/1.x y HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="85274-672">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="85274-673">Tiempo de expiración de los encabezados de solicitud</span><span class="sxs-lookup"><span data-stu-id="85274-673">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="85274-674">Obtiene o establece la cantidad máxima de tiempo que el servidor pasa recibiendo las cabeceras de las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="85274-674">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="85274-675">El valor predeterminado es 30 segundos.</span><span class="sxs-lookup"><span data-stu-id="85274-675">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=16)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="85274-676">Secuencias máximas por conexión</span><span class="sxs-lookup"><span data-stu-id="85274-676">Maximum streams per connection</span></span>

<span data-ttu-id="85274-677">`Http2.MaxStreamsPerConnection` limita el número de secuencias de solicitudes simultáneas por conexión HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="85274-677">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="85274-678">Se rechazarán las secuencias en exceso.</span><span class="sxs-lookup"><span data-stu-id="85274-678">Excess streams are refused.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
        });
```

<span data-ttu-id="85274-679">El valor predeterminado es 100.</span><span class="sxs-lookup"><span data-stu-id="85274-679">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="85274-680">Tamaño de la tabla de encabezado</span><span class="sxs-lookup"><span data-stu-id="85274-680">Header table size</span></span>

<span data-ttu-id="85274-681">El descodificador HPACK descomprime los encabezados HTTP para las conexiones HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="85274-681">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="85274-682">`Http2.HeaderTableSize` limita el tamaño de la tabla de compresión de encabezado que usa el descodificador HPACK.</span><span class="sxs-lookup"><span data-stu-id="85274-682">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="85274-683">El valor se proporciona en octetos y debe ser mayor que cero (0).</span><span class="sxs-lookup"><span data-stu-id="85274-683">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.HeaderTableSize = 4096;
        });
```

<span data-ttu-id="85274-684">El valor predeterminado es 4096.</span><span class="sxs-lookup"><span data-stu-id="85274-684">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="85274-685">Tamaño máximo de marco</span><span class="sxs-lookup"><span data-stu-id="85274-685">Maximum frame size</span></span>

<span data-ttu-id="85274-686">`Http2.MaxFrameSize` indica el tamaño máximo de la carga útil de la trama de conexión HTTP/2 que se puede recibir.</span><span class="sxs-lookup"><span data-stu-id="85274-686">`Http2.MaxFrameSize` indicates the maximum size of the HTTP/2 connection frame payload to receive.</span></span> <span data-ttu-id="85274-687">El valor se proporciona en octetos y debe estar comprendido entre 2^14 (16 384) y 2^24-1 (16 777 215).</span><span class="sxs-lookup"><span data-stu-id="85274-687">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxFrameSize = 16384;
        });
```

<span data-ttu-id="85274-688">El valor predeterminado es 2^14 (16 384).</span><span class="sxs-lookup"><span data-stu-id="85274-688">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="85274-689">Tamaño máximo del encabezado de solicitud</span><span class="sxs-lookup"><span data-stu-id="85274-689">Maximum request header size</span></span>

<span data-ttu-id="85274-690">`Http2.MaxRequestHeaderFieldSize` indica el tamaño máximo permitido (en octetos) de los valores de los encabezados de solicitud.</span><span class="sxs-lookup"><span data-stu-id="85274-690">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="85274-691">Este límite se aplica al nombre y al valor conjuntamente en sus representaciones comprimidas y no comprimidas.</span><span class="sxs-lookup"><span data-stu-id="85274-691">This limit applies to both name and value together in their compressed and uncompressed representations.</span></span> <span data-ttu-id="85274-692">El valor debe ser mayor que cero (0).</span><span class="sxs-lookup"><span data-stu-id="85274-692">The value must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
        });
```

<span data-ttu-id="85274-693">El valor predeterminado es 8192.</span><span class="sxs-lookup"><span data-stu-id="85274-693">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="85274-694">Tamaño inicial de la ventana de conexión</span><span class="sxs-lookup"><span data-stu-id="85274-694">Initial connection window size</span></span>

<span data-ttu-id="85274-695">`Http2.InitialConnectionWindowSize` indica la cantidad máxima de datos del cuerpo de solicitud (en bytes) que el servidor almacena en búfer a la vez de forma agregada en todas las solicitudes (transmisiones) por conexión.</span><span class="sxs-lookup"><span data-stu-id="85274-695">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="85274-696">Las solicitudes también están limitadas por `Http2.InitialStreamWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="85274-696">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="85274-697">El valor debe ser igual o mayor que 65 535 y menor que 2^31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="85274-697">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
        });
```

<span data-ttu-id="85274-698">El valor predeterminado es 128 KB (131 072).</span><span class="sxs-lookup"><span data-stu-id="85274-698">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="85274-699">Tamaño inicial de la ventana de transmisión</span><span class="sxs-lookup"><span data-stu-id="85274-699">Initial stream window size</span></span>

<span data-ttu-id="85274-700">`Http2.InitialStreamWindowSize` indica la cantidad máxima de datos del cuerpo de solicitud (en bytes) que el servidor almacena en búfer a la vez por solicitud (transmisión).</span><span class="sxs-lookup"><span data-stu-id="85274-700">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="85274-701">Las solicitudes también están limitadas por `Http2.InitialStreamWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="85274-701">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="85274-702">El valor debe ser igual o mayor que 65 535 y menor que 2^31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="85274-702">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
        });
```

<span data-ttu-id="85274-703">El valor predeterminado es 96 KB (98 304).</span><span class="sxs-lookup"><span data-stu-id="85274-703">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="85274-704">E/S síncrona</span><span class="sxs-lookup"><span data-stu-id="85274-704">Synchronous I/O</span></span>

<span data-ttu-id="85274-705"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controla si se permite la E/S sincrónica para la solicitud y la respuesta.</span><span class="sxs-lookup"><span data-stu-id="85274-705"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="85274-706">El valor predeterminado es `true`.</span><span class="sxs-lookup"><span data-stu-id="85274-706">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="85274-707">Un gran número de operaciones de E/S sincrónicas de bloqueo puede dar lugar al colapso del grupo de subprocesos, lo que hace que la aplicación no responda.</span><span class="sxs-lookup"><span data-stu-id="85274-707">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="85274-708">Habilite solo `AllowSynchronousIO` al usar una biblioteca que no admite la E/S asincrónica.</span><span class="sxs-lookup"><span data-stu-id="85274-708">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="85274-709">En el ejemplo siguiente se habilita la E/S sincrónica:</span><span class="sxs-lookup"><span data-stu-id="85274-709">The following example enables synchronous I/O:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="85274-710">Para más información sobre otras opciones y límites de Kestrel, vea:</span><span class="sxs-lookup"><span data-stu-id="85274-710">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="85274-711">Configuración de punto de conexión</span><span class="sxs-lookup"><span data-stu-id="85274-711">Endpoint configuration</span></span>

<span data-ttu-id="85274-712">ASP.NET Core enlaza de forma predeterminada a:</span><span class="sxs-lookup"><span data-stu-id="85274-712">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="85274-713">`https://localhost:5001` (cuando hay presente un certificado de desarrollo local)</span><span class="sxs-lookup"><span data-stu-id="85274-713">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="85274-714">Especifique direcciones URL mediante los siguientes elementos:</span><span class="sxs-lookup"><span data-stu-id="85274-714">Specify URLs using the:</span></span>

* <span data-ttu-id="85274-715">La variable de entorno `ASPNETCORE_URLS`.</span><span class="sxs-lookup"><span data-stu-id="85274-715">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="85274-716">El argumento de la línea de comandos `--urls`.</span><span class="sxs-lookup"><span data-stu-id="85274-716">`--urls` command-line argument.</span></span>
* <span data-ttu-id="85274-717">La clave de configuración de host `urls`.</span><span class="sxs-lookup"><span data-stu-id="85274-717">`urls` host configuration key.</span></span>
* <span data-ttu-id="85274-718">El método de extensión `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="85274-718">`UseUrls` extension method.</span></span>

<span data-ttu-id="85274-719">El valor que estos métodos suministran puede ser uno o más puntos de conexión HTTP y HTTPS (este último, si hay disponible un certificado predeterminado).</span><span class="sxs-lookup"><span data-stu-id="85274-719">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="85274-720">Configure el valor como una lista separada por punto y coma (por ejemplo, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="85274-720">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="85274-721">Para más información sobre estos enfoques, consulte [Direcciones URL del servidor](xref:fundamentals/host/web-host#server-urls) e [Invalidar la configuración](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="85274-721">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="85274-722">Un certificado de desarrollo se crea:</span><span class="sxs-lookup"><span data-stu-id="85274-722">A development certificate is created:</span></span>

* <span data-ttu-id="85274-723">Cuando el [SDK de .NET Core](/dotnet/core/sdk) está instalado.</span><span class="sxs-lookup"><span data-stu-id="85274-723">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="85274-724">Para crear un certificado, se usa la [herramienta dev-certs](xref:aspnetcore-2.1#https).</span><span class="sxs-lookup"><span data-stu-id="85274-724">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="85274-725">Algunos exploradores necesitan que se conceda permiso explícito para confiar en el certificado de desarrollo local.</span><span class="sxs-lookup"><span data-stu-id="85274-725">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="85274-726">Las plantillas de proyecto configuran aplicaciones para que se ejecuten en HTTPS de forma predeterminada e incluyen [redirección de HTTPS y compatibilidad con HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="85274-726">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="85274-727">Llame a los métodos <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> o <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> de <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> para configurar los puertos y los prefijos de dirección URL para Kestrel.</span><span class="sxs-lookup"><span data-stu-id="85274-727">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="85274-728">`UseUrls`, el argumento de línea de comandos `--urls`, la clave de configuración de host `urls` y la variable de entorno `ASPNETCORE_URLS` también funcionan, pero tienen las limitaciones que se indican más adelante en esta sección (debe haber disponible un certificado predeterminado para la configuración de puntos de conexión HTTPS).</span><span class="sxs-lookup"><span data-stu-id="85274-728">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="85274-729">Configuración de `KestrelServerOptions`:</span><span class="sxs-lookup"><span data-stu-id="85274-729">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="85274-730">ConfigureEndpointDefaults(Action\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="85274-730">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="85274-731">Especifica una `Action` de configuración para que se ejecute con cada punto de conexión especificado.</span><span class="sxs-lookup"><span data-stu-id="85274-731">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="85274-732">Al llamar a `ConfigureEndpointDefaults` varias veces, se reemplazan las `Action` anteriores por la última `Action` especificada.</span><span class="sxs-lookup"><span data-stu-id="85274-732">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
        });
```

> [!NOTE]
> <span data-ttu-id="85274-733">Los puntos de conexión que se crean mediante una llamada a <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **antes** de llamar a <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> no tendrán aplicados los valores predeterminados.</span><span class="sxs-lookup"><span data-stu-id="85274-733">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="85274-734">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="85274-734">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="85274-735">Especifica una `Action` de configuración para que se ejecute con cada punto de conexión HTTPS.</span><span class="sxs-lookup"><span data-stu-id="85274-735">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="85274-736">Al llamar a `ConfigureHttpsDefaults` varias veces, se reemplazan las `Action` anteriores por la última `Action` especificada.</span><span class="sxs-lookup"><span data-stu-id="85274-736">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                // certificate is an X509Certificate2
                listenOptions.ServerCertificate = certificate;
            });
        });
```

> [!NOTE]
> <span data-ttu-id="85274-737">Los puntos de conexión que se crean mediante una llamada a <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **antes** de llamar a <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> no tendrán aplicados los valores predeterminados.</span><span class="sxs-lookup"><span data-stu-id="85274-737">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>


### <a name="configureiconfiguration"></a><span data-ttu-id="85274-738">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="85274-738">Configure(IConfiguration)</span></span>

<span data-ttu-id="85274-739">Crea un cargador de configuración para configurar Kestrel que toma una <xref:Microsoft.Extensions.Configuration.IConfiguration> como entrada.</span><span class="sxs-lookup"><span data-stu-id="85274-739">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="85274-740">El ámbito de la configuración debe corresponderse con la sección de configuración de Kestrel.</span><span class="sxs-lookup"><span data-stu-id="85274-740">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="85274-741">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="85274-741">ListenOptions.UseHttps</span></span>

<span data-ttu-id="85274-742">Configure Kestrel para que use HTTPS.</span><span class="sxs-lookup"><span data-stu-id="85274-742">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="85274-743">Extensiones de `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="85274-743">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="85274-744">`UseHttps`: configure Kestrel para que use HTTPS con el certificado predeterminado.</span><span class="sxs-lookup"><span data-stu-id="85274-744">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="85274-745">Produce una excepción si no hay ningún certificado predeterminado configurado.</span><span class="sxs-lookup"><span data-stu-id="85274-745">Throws an exception if no default certificate is configured.</span></span>
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

<span data-ttu-id="85274-746">Parámetros de `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="85274-746">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="85274-747">`filename` es la ruta de acceso y el nombre de archivo de un archivo de certificado correspondiente al directorio donde están los archivos de contenido de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="85274-747">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="85274-748">`password` es la contraseña necesaria para obtener acceso a los datos del certificado X.509.</span><span class="sxs-lookup"><span data-stu-id="85274-748">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="85274-749">`configureOptions` es una `Action` para configurar `HttpsConnectionAdapterOptions`.</span><span class="sxs-lookup"><span data-stu-id="85274-749">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="85274-750">Devuelve `ListenOptions`.</span><span class="sxs-lookup"><span data-stu-id="85274-750">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="85274-751">`storeName` es el almacén de certificados desde el que se carga el certificado.</span><span class="sxs-lookup"><span data-stu-id="85274-751">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="85274-752">`subject` es el nombre del sujeto del certificado.</span><span class="sxs-lookup"><span data-stu-id="85274-752">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="85274-753">`allowInvalid` indica si se deben tener en cuenta los certificados no válidos, como los certificados autofirmados.</span><span class="sxs-lookup"><span data-stu-id="85274-753">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="85274-754">`location` es la ubicación del almacén desde el que se carga el certificado.</span><span class="sxs-lookup"><span data-stu-id="85274-754">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="85274-755">`serverCertificate` es el certificado X.509.</span><span class="sxs-lookup"><span data-stu-id="85274-755">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="85274-756">En un entorno de producción, HTTPS se debe configurar explícitamente.</span><span class="sxs-lookup"><span data-stu-id="85274-756">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="85274-757">Como mínimo, debe existir un certificado predeterminado.</span><span class="sxs-lookup"><span data-stu-id="85274-757">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="85274-758">Estas son las configuraciones compatibles:</span><span class="sxs-lookup"><span data-stu-id="85274-758">Supported configurations described next:</span></span>

* <span data-ttu-id="85274-759">Sin configuración</span><span class="sxs-lookup"><span data-stu-id="85274-759">No configuration</span></span>
* <span data-ttu-id="85274-760">Reemplazar el certificado predeterminado de configuración</span><span class="sxs-lookup"><span data-stu-id="85274-760">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="85274-761">Cambiar los valores predeterminados en el código</span><span class="sxs-lookup"><span data-stu-id="85274-761">Change the defaults in code</span></span>

<span data-ttu-id="85274-762">*Sin configuración*</span><span class="sxs-lookup"><span data-stu-id="85274-762">*No configuration*</span></span>

<span data-ttu-id="85274-763">Kestrel escucha en `http://localhost:5000` y en `https://localhost:5001` (si hay disponible un certificado predeterminado).</span><span class="sxs-lookup"><span data-stu-id="85274-763">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="85274-764">*Reemplazar el certificado predeterminado de configuración*</span><span class="sxs-lookup"><span data-stu-id="85274-764">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="85274-765">`CreateDefaultBuilder` llama a `Configure(context.Configuration.GetSection("Kestrel"))` de forma predeterminada para cargar la configuración de Kestrel.</span><span class="sxs-lookup"><span data-stu-id="85274-765">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="85274-766">Hay disponible un esquema de configuración de aplicación HTTPS predeterminado para Kestrel.</span><span class="sxs-lookup"><span data-stu-id="85274-766">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="85274-767">Configure varios puntos de conexión (incluidas las direcciones URL y los certificados que va a usar) desde un archivo en disco o desde un almacén de certificados.</span><span class="sxs-lookup"><span data-stu-id="85274-767">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="85274-768">En el siguiente ejemplo de *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="85274-768">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="85274-769">Establezca **AllowInvalid** en `true` para permitir el uso de certificados no válidos (por ejemplo, certificados autofirmados).</span><span class="sxs-lookup"><span data-stu-id="85274-769">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="85274-770">Cualquier punto de conexión HTTPS que no especifique un certificado (**HttpsDefaultCert** en el siguiente ejemplo) revierte al certificado definido en **Certificados** > **Predeterminado** o al certificado de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="85274-770">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },

      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },

      "HttpsInlineCertStore": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },

      "HttpsDefaultCert": {
        "Url": "https://localhost:5003"
      },

      "Https": {
        "Url": "https://*:5004",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

<span data-ttu-id="85274-771">Una alternativa al uso de **Path** y **Password** en cualquier nodo de certificado consiste en especificar el certificado por medio de campos del almacén de certificados.</span><span class="sxs-lookup"><span data-stu-id="85274-771">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="85274-772">Por ejemplo, el certificado en **Certificados** > **Predeterminado** se puede especificar así:</span><span class="sxs-lookup"><span data-stu-id="85274-772">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="85274-773">Notas sobre el esquema:</span><span class="sxs-lookup"><span data-stu-id="85274-773">Schema notes:</span></span>

* <span data-ttu-id="85274-774">En los nombres de los puntos de conexión se distingue entre mayúsculas y minúsculas.</span><span class="sxs-lookup"><span data-stu-id="85274-774">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="85274-775">Por ejemplo, `HTTPS` y `Https` son válidos.</span><span class="sxs-lookup"><span data-stu-id="85274-775">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="85274-776">El parámetro `Url` es necesario en cada punto de conexión.</span><span class="sxs-lookup"><span data-stu-id="85274-776">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="85274-777">El formato de este parámetro es el mismo que el del parámetro de configuración `Urls` de nivel superior, excepto por el hecho de que está limitado a un único valor.</span><span class="sxs-lookup"><span data-stu-id="85274-777">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="85274-778">En vez de agregarse, estos puntos de conexión reemplazan a los que están definidos en la configuración `Urls` de nivel superior.</span><span class="sxs-lookup"><span data-stu-id="85274-778">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="85274-779">Los puntos de conexión definidos en el código a través de `Listen` son acumulativos con respecto a los puntos de conexión definidos en la sección de configuración.</span><span class="sxs-lookup"><span data-stu-id="85274-779">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="85274-780">La sección `Certificate` es opcional.</span><span class="sxs-lookup"><span data-stu-id="85274-780">The `Certificate` section is optional.</span></span> <span data-ttu-id="85274-781">Si la sección `Certificate` no se especifica, se usan los valores predeterminados definidos en escenarios anteriores.</span><span class="sxs-lookup"><span data-stu-id="85274-781">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="85274-782">Si no hay valores predeterminados disponibles, el servidor produce una excepción y no se inicia.</span><span class="sxs-lookup"><span data-stu-id="85274-782">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="85274-783">La sección `Certificate` admite certificadostanto **Path**&ndash;**Password** como **Subject**&ndash;**Store**.</span><span class="sxs-lookup"><span data-stu-id="85274-783">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="85274-784">Se puede definir el número de puntos de conexión que se quiera de esta manera, siempre y cuando no produzcan conflictos de puerto.</span><span class="sxs-lookup"><span data-stu-id="85274-784">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="85274-785">`options.Configure(context.Configuration.GetSection("{SECTION}"))` devuelve un `KestrelConfigurationLoader` con un método `.Endpoint(string name, listenOptions => { })` que se puede usar para complementar la configuración de un punto de conexión configurado:</span><span class="sxs-lookup"><span data-stu-id="85274-785">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
                .Endpoint("HTTPS", listenOptions =>
                {
                    listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
                });
        });
```

<span data-ttu-id="85274-786">Se puede acceder directamente a `KestrelServerOptions.ConfigurationLoader` para seguir con la iteración en el cargador existente, como el proporcionado por <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span><span class="sxs-lookup"><span data-stu-id="85274-786">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="85274-787">La sección de configuración de cada punto de conexión está disponible en las opciones del método `Endpoint` para que se pueda leer la configuración personalizada.</span><span class="sxs-lookup"><span data-stu-id="85274-787">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="85274-788">Se pueden cargar varias configuraciones volviendo a llamar a `options.Configure(context.Configuration.GetSection("{SECTION}"))` con otra sección.</span><span class="sxs-lookup"><span data-stu-id="85274-788">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="85274-789">Se usa la última configuración, a menos que se llame explícitamente a `Load` en instancias anteriores.</span><span class="sxs-lookup"><span data-stu-id="85274-789">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="85274-790">El metapaquete no llama a `Load`, con lo cual su sección de configuración predeterminada se puede reemplazar.</span><span class="sxs-lookup"><span data-stu-id="85274-790">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="85274-791">`KestrelConfigurationLoader` refleja la familia `Listen` de API de `KestrelServerOptions` como sobrecargas de `Endpoint`, por lo que los puntos de conexión de configuración y código se pueden configurar en el mismo lugar.</span><span class="sxs-lookup"><span data-stu-id="85274-791">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="85274-792">En estas sobrecargas no se usan nombres y solo consumen valores predeterminados de la configuración.</span><span class="sxs-lookup"><span data-stu-id="85274-792">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="85274-793">*Cambiar los valores predeterminados en el código*</span><span class="sxs-lookup"><span data-stu-id="85274-793">*Change the defaults in code*</span></span>

<span data-ttu-id="85274-794">`ConfigureEndpointDefaults` y `ConfigureHttpsDefaults` se pueden usar para cambiar la configuración predeterminada de `ListenOptions` y `HttpsConnectionAdapterOptions`, incluido sustituir el certificado predeterminado especificado en el escenario anterior.</span><span class="sxs-lookup"><span data-stu-id="85274-794">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="85274-795">Se debe llamar a `ConfigureEndpointDefaults` y a `ConfigureHttpsDefaults` antes de que se configure algún punto de conexión.</span><span class="sxs-lookup"><span data-stu-id="85274-795">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
            
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                listenOptions.SslProtocols = SslProtocols.Tls12;
            });
        });
```

<span data-ttu-id="85274-796">*Compatibilidad de Kestrel con SNI*</span><span class="sxs-lookup"><span data-stu-id="85274-796">*Kestrel support for SNI*</span></span>

<span data-ttu-id="85274-797">[Indicación de nombre de servidor (SNI)](https://tools.ietf.org/html/rfc6066#section-3) se puede usar para hospedar varios dominios en la misma dirección IP y puerto.</span><span class="sxs-lookup"><span data-stu-id="85274-797">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="85274-798">Para que SNI funcione, el cliente envía el nombre de host de la sesión segura al servidor durante el protocolo de enlace TLS para que, de este modo, el servidor pueda proporcionar el certificado correcto.</span><span class="sxs-lookup"><span data-stu-id="85274-798">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="85274-799">El cliente usa el certificado proporcionado para la comunicación cifrada con el servidor durante la sesión segura que sigue al protocolo de enlace TLS.</span><span class="sxs-lookup"><span data-stu-id="85274-799">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="85274-800">Kestrel admite SNI a través de la devolución de llamada `ServerCertificateSelector`.</span><span class="sxs-lookup"><span data-stu-id="85274-800">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="85274-801">La devolución de llamada se invoca una vez por conexión para permitir que la aplicación inspeccione el nombre de host y seleccione el certificado adecuado.</span><span class="sxs-lookup"><span data-stu-id="85274-801">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="85274-802">La compatibilidad con SNI requiere lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="85274-802">SNI support requires:</span></span>

* <span data-ttu-id="85274-803">Ejecutarse en el marco de destino `netcoreapp2.1` o posterior.</span><span class="sxs-lookup"><span data-stu-id="85274-803">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="85274-804">En `net461` o posterior, se invoca la devolución de llamada, pero `name` siempre es `null`.</span><span class="sxs-lookup"><span data-stu-id="85274-804">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="85274-805">`name` también será `null` si el cliente no proporciona el parámetro de nombre de host en el protocolo de enlace TLS.</span><span class="sxs-lookup"><span data-stu-id="85274-805">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="85274-806">Todos los sitios web deben ejecutarse en la misma instancia de Kestrel.</span><span class="sxs-lookup"><span data-stu-id="85274-806">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="85274-807">Kestrel no admite el uso compartido de una dirección IP y un puerto entre varias instancias sin un proxy inverso.</span><span class="sxs-lookup"><span data-stu-id="85274-807">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ListenAnyIP(5005, listenOptions =>
            {
                listenOptions.UseHttps(httpsOptions =>
                {
                    var localhostCert = CertificateLoader.LoadFromStoreCert(
                        "localhost", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var exampleCert = CertificateLoader.LoadFromStoreCert(
                        "example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var subExampleCert = CertificateLoader.LoadFromStoreCert(
                        "sub.example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var certs = new Dictionary<string, X509Certificate2>(
                        StringComparer.OrdinalIgnoreCase);
                    certs["localhost"] = localhostCert;
                    certs["example.com"] = exampleCert;
                    certs["sub.example.com"] = subExampleCert;

                    httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
                    {
                        if (name != null && certs.TryGetValue(name, out var cert))
                        {
                            return cert;
                        }

                        return exampleCert;
                    };
                });
            });
        });
```

### <a name="connection-logging"></a><span data-ttu-id="85274-808">Registro de conexiones</span><span class="sxs-lookup"><span data-stu-id="85274-808">Connection logging</span></span>

<span data-ttu-id="85274-809">Llame a <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> para emitir registros de nivel de depuración para la comunicación a nivel de bytes en una conexión.</span><span class="sxs-lookup"><span data-stu-id="85274-809">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="85274-810">El registro de conexiones es útil para solucionar problemas en la comunicación de bajo nivel, como durante el cifrado TLS y detrás de los servidores proxy.</span><span class="sxs-lookup"><span data-stu-id="85274-810">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="85274-811">Si `UseConnectionLogging` se coloca antes de `UseHttps`, se registra el tráfico cifrado.</span><span class="sxs-lookup"><span data-stu-id="85274-811">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="85274-812">Si `UseConnectionLogging` se coloca después de `UseHttps`, se registra el tráfico descifrado.</span><span class="sxs-lookup"><span data-stu-id="85274-812">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="85274-813">Enlazar a un socket TCP</span><span class="sxs-lookup"><span data-stu-id="85274-813">Bind to a TCP socket</span></span>

<span data-ttu-id="85274-814">El método <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> se enlaza a un socket TCP y una expresión lambda de opciones permite configurar un certificado X.509:</span><span class="sxs-lookup"><span data-stu-id="85274-814">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

<span data-ttu-id="85274-815">En el ejemplo se configura HTTPS para un punto de conexión con <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span><span class="sxs-lookup"><span data-stu-id="85274-815">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="85274-816">Use la misma API para configurar otras opciones de Kestrel para puntos de conexión específicos.</span><span class="sxs-lookup"><span data-stu-id="85274-816">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="85274-817">Enlazar a un socket de Unix</span><span class="sxs-lookup"><span data-stu-id="85274-817">Bind to a Unix socket</span></span>

<span data-ttu-id="85274-818">Escuche en un socket de Unix con <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> para mejorar el rendimiento con Nginx, tal como se muestra en este ejemplo:</span><span class="sxs-lookup"><span data-stu-id="85274-818">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="85274-819">En el archivo de configuración de Nginx, establezca la entrada `server` > `location` > `proxy_pass` en `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span><span class="sxs-lookup"><span data-stu-id="85274-819">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="85274-820">`{KESTREL SOCKET}` es el nombre del socket proporcionado para <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (por ejemplo, `kestrel-test.sock` en el ejemplo anterior).</span><span class="sxs-lookup"><span data-stu-id="85274-820">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="85274-821">Asegúrese de que el socket es grabable por Nginx (por ejemplo, `chmod go+w /tmp/kestrel-test.sock`).</span><span class="sxs-lookup"><span data-stu-id="85274-821">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="85274-822">Puerto 0</span><span class="sxs-lookup"><span data-stu-id="85274-822">Port 0</span></span>

<span data-ttu-id="85274-823">Cuando se especifica el número de puerto `0`, Kestrel se enlaza de forma dinámica a un puerto disponible.</span><span class="sxs-lookup"><span data-stu-id="85274-823">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="85274-824">En el siguiente ejemplo se muestra cómo averiguar qué puerto Kestrel está realmente enlazado a un runtime:</span><span class="sxs-lookup"><span data-stu-id="85274-824">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="85274-825">Cuando la aplicación se ejecuta, la salida de la ventana de consola indica el puerto dinámico en el que se puede tener acceso a la aplicación:</span><span class="sxs-lookup"><span data-stu-id="85274-825">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="85274-826">Limitaciones</span><span class="sxs-lookup"><span data-stu-id="85274-826">Limitations</span></span>

<span data-ttu-id="85274-827">Configure puntos de conexión con los siguientes métodos:</span><span class="sxs-lookup"><span data-stu-id="85274-827">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="85274-828">El argumento de la línea de comandos `--urls`</span><span class="sxs-lookup"><span data-stu-id="85274-828">`--urls` command-line argument</span></span>
* <span data-ttu-id="85274-829">La clave de configuración de host `urls`</span><span class="sxs-lookup"><span data-stu-id="85274-829">`urls` host configuration key</span></span>
* <span data-ttu-id="85274-830">La variable de entorno `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="85274-830">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="85274-831">Estos métodos son útiles para que el código funcione con servidores que no sean de Kestrel.</span><span class="sxs-lookup"><span data-stu-id="85274-831">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="85274-832">Sin embargo, tenga en cuenta las siguientes limitaciones:</span><span class="sxs-lookup"><span data-stu-id="85274-832">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="85274-833">HTTPS no se puede usar con estos métodos, a menos que se proporcione un certificado predeterminado en la configuración del punto de conexión HTTPS (por ejemplo, por medio de la configuración `KestrelServerOptions` o de un archivo de configuración, tal y como se explicó anteriormente en este tema).</span><span class="sxs-lookup"><span data-stu-id="85274-833">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="85274-834">Cuando los métodos `Listen` y `UseUrls` se usan al mismo tiempo, los puntos de conexión de `Listen` sustituyen a los de `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="85274-834">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="85274-835">Configuración de puntos de conexión IIS</span><span class="sxs-lookup"><span data-stu-id="85274-835">IIS endpoint configuration</span></span>

<span data-ttu-id="85274-836">Cuando se usa IIS, los enlaces de direcciones URL de IIS reemplazan a los enlaces que se hayan establecido por medio de `Listen` o de `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="85274-836">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="85274-837">Para más información, vea el tema [Módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="85274-837">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="85274-838">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="85274-838">ListenOptions.Protocols</span></span>

<span data-ttu-id="85274-839">La propiedad `Protocols` establece los protocolos HTTP (`HttpProtocols`) habilitados en un punto de conexión o para el servidor.</span><span class="sxs-lookup"><span data-stu-id="85274-839">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="85274-840">Asigne un valor a la propiedad `Protocols` desde el valor de enumeración `HttpProtocols`.</span><span class="sxs-lookup"><span data-stu-id="85274-840">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="85274-841">Valor de enumeración `HttpProtocols`</span><span class="sxs-lookup"><span data-stu-id="85274-841">`HttpProtocols` enum value</span></span> | <span data-ttu-id="85274-842">Protocolo de conexión permitido</span><span class="sxs-lookup"><span data-stu-id="85274-842">Connection protocol permitted</span></span> |
| ---
<span data-ttu-id="85274-843">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="85274-843">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="85274-844">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="85274-844">'Blazor'</span></span>
- <span data-ttu-id="85274-845">"Identity"</span><span class="sxs-lookup"><span data-stu-id="85274-845">'Identity'</span></span>
- <span data-ttu-id="85274-846">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="85274-846">'Let's Encrypt'</span></span>
- <span data-ttu-id="85274-847">"Razor"</span><span class="sxs-lookup"><span data-stu-id="85274-847">'Razor'</span></span>
- <span data-ttu-id="85274-848">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="85274-848">'SignalR' uid:</span></span> 

-
<span data-ttu-id="85274-849">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="85274-849">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="85274-850">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="85274-850">'Blazor'</span></span>
- <span data-ttu-id="85274-851">"Identity"</span><span class="sxs-lookup"><span data-stu-id="85274-851">'Identity'</span></span>
- <span data-ttu-id="85274-852">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="85274-852">'Let's Encrypt'</span></span>
- <span data-ttu-id="85274-853">"Razor"</span><span class="sxs-lookup"><span data-stu-id="85274-853">'Razor'</span></span>
- <span data-ttu-id="85274-854">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="85274-854">'SignalR' uid:</span></span> 

-
<span data-ttu-id="85274-855">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="85274-855">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="85274-856">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="85274-856">'Blazor'</span></span>
- <span data-ttu-id="85274-857">"Identity"</span><span class="sxs-lookup"><span data-stu-id="85274-857">'Identity'</span></span>
- <span data-ttu-id="85274-858">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="85274-858">'Let's Encrypt'</span></span>
- <span data-ttu-id="85274-859">"Razor"</span><span class="sxs-lookup"><span data-stu-id="85274-859">'Razor'</span></span>
- <span data-ttu-id="85274-860">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="85274-860">'SignalR' uid:</span></span> 

-
<span data-ttu-id="85274-861">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="85274-861">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="85274-862">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="85274-862">'Blazor'</span></span>
- <span data-ttu-id="85274-863">"Identity"</span><span class="sxs-lookup"><span data-stu-id="85274-863">'Identity'</span></span>
- <span data-ttu-id="85274-864">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="85274-864">'Let's Encrypt'</span></span>
- <span data-ttu-id="85274-865">"Razor"</span><span class="sxs-lookup"><span data-stu-id="85274-865">'Razor'</span></span>
- <span data-ttu-id="85274-866">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="85274-866">'SignalR' uid:</span></span> 

-
<span data-ttu-id="85274-867">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="85274-867">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="85274-868">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="85274-868">'Blazor'</span></span>
- <span data-ttu-id="85274-869">"Identity"</span><span class="sxs-lookup"><span data-stu-id="85274-869">'Identity'</span></span>
- <span data-ttu-id="85274-870">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="85274-870">'Let's Encrypt'</span></span>
- <span data-ttu-id="85274-871">"Razor"</span><span class="sxs-lookup"><span data-stu-id="85274-871">'Razor'</span></span>
- <span data-ttu-id="85274-872">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="85274-872">'SignalR' uid:</span></span> 

-
<span data-ttu-id="85274-873">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="85274-873">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="85274-874">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="85274-874">'Blazor'</span></span>
- <span data-ttu-id="85274-875">"Identity"</span><span class="sxs-lookup"><span data-stu-id="85274-875">'Identity'</span></span>
- <span data-ttu-id="85274-876">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="85274-876">'Let's Encrypt'</span></span>
- <span data-ttu-id="85274-877">"Razor"</span><span class="sxs-lookup"><span data-stu-id="85274-877">'Razor'</span></span>
- <span data-ttu-id="85274-878">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="85274-878">'SignalR' uid:</span></span> 

-
<span data-ttu-id="85274-879">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="85274-879">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="85274-880">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="85274-880">'Blazor'</span></span>
- <span data-ttu-id="85274-881">"Identity"</span><span class="sxs-lookup"><span data-stu-id="85274-881">'Identity'</span></span>
- <span data-ttu-id="85274-882">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="85274-882">'Let's Encrypt'</span></span>
- <span data-ttu-id="85274-883">"Razor"</span><span class="sxs-lookup"><span data-stu-id="85274-883">'Razor'</span></span>
- <span data-ttu-id="85274-884">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="85274-884">'SignalR' uid:</span></span> 

-
<span data-ttu-id="85274-885">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="85274-885">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="85274-886">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="85274-886">'Blazor'</span></span>
- <span data-ttu-id="85274-887">"Identity"</span><span class="sxs-lookup"><span data-stu-id="85274-887">'Identity'</span></span>
- <span data-ttu-id="85274-888">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="85274-888">'Let's Encrypt'</span></span>
- <span data-ttu-id="85274-889">"Razor"</span><span class="sxs-lookup"><span data-stu-id="85274-889">'Razor'</span></span>
- <span data-ttu-id="85274-890">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="85274-890">'SignalR' uid:</span></span> 

-
<span data-ttu-id="85274-891">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="85274-891">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="85274-892">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="85274-892">'Blazor'</span></span>
- <span data-ttu-id="85274-893">"Identity"</span><span class="sxs-lookup"><span data-stu-id="85274-893">'Identity'</span></span>
- <span data-ttu-id="85274-894">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="85274-894">'Let's Encrypt'</span></span>
- <span data-ttu-id="85274-895">"Razor"</span><span class="sxs-lookup"><span data-stu-id="85274-895">'Razor'</span></span>
- <span data-ttu-id="85274-896">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="85274-896">'SignalR' uid:</span></span> 

-
<span data-ttu-id="85274-897">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="85274-897">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="85274-898">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="85274-898">'Blazor'</span></span>
- <span data-ttu-id="85274-899">"Identity"</span><span class="sxs-lookup"><span data-stu-id="85274-899">'Identity'</span></span>
- <span data-ttu-id="85274-900">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="85274-900">'Let's Encrypt'</span></span>
- <span data-ttu-id="85274-901">"Razor"</span><span class="sxs-lookup"><span data-stu-id="85274-901">'Razor'</span></span>
- <span data-ttu-id="85274-902">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="85274-902">'SignalR' uid:</span></span> 

-
<span data-ttu-id="85274-903">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="85274-903">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="85274-904">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="85274-904">'Blazor'</span></span>
- <span data-ttu-id="85274-905">"Identity"</span><span class="sxs-lookup"><span data-stu-id="85274-905">'Identity'</span></span>
- <span data-ttu-id="85274-906">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="85274-906">'Let's Encrypt'</span></span>
- <span data-ttu-id="85274-907">"Razor"</span><span class="sxs-lookup"><span data-stu-id="85274-907">'Razor'</span></span>
- <span data-ttu-id="85274-908">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="85274-908">'SignalR' uid:</span></span> 

<span data-ttu-id="85274-909">------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="85274-909">------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="85274-910">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="85274-910">'Blazor'</span></span>
- <span data-ttu-id="85274-911">"Identity"</span><span class="sxs-lookup"><span data-stu-id="85274-911">'Identity'</span></span>
- <span data-ttu-id="85274-912">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="85274-912">'Let's Encrypt'</span></span>
- <span data-ttu-id="85274-913">"Razor"</span><span class="sxs-lookup"><span data-stu-id="85274-913">'Razor'</span></span>
- <span data-ttu-id="85274-914">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="85274-914">'SignalR' uid:</span></span> 

-
<span data-ttu-id="85274-915">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="85274-915">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="85274-916">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="85274-916">'Blazor'</span></span>
- <span data-ttu-id="85274-917">"Identity"</span><span class="sxs-lookup"><span data-stu-id="85274-917">'Identity'</span></span>
- <span data-ttu-id="85274-918">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="85274-918">'Let's Encrypt'</span></span>
- <span data-ttu-id="85274-919">"Razor"</span><span class="sxs-lookup"><span data-stu-id="85274-919">'Razor'</span></span>
- <span data-ttu-id="85274-920">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="85274-920">'SignalR' uid:</span></span> 

-
<span data-ttu-id="85274-921">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="85274-921">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="85274-922">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="85274-922">'Blazor'</span></span>
- <span data-ttu-id="85274-923">"Identity"</span><span class="sxs-lookup"><span data-stu-id="85274-923">'Identity'</span></span>
- <span data-ttu-id="85274-924">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="85274-924">'Let's Encrypt'</span></span>
- <span data-ttu-id="85274-925">"Razor"</span><span class="sxs-lookup"><span data-stu-id="85274-925">'Razor'</span></span>
- <span data-ttu-id="85274-926">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="85274-926">'SignalR' uid:</span></span> 

-
<span data-ttu-id="85274-927">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="85274-927">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="85274-928">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="85274-928">'Blazor'</span></span>
- <span data-ttu-id="85274-929">"Identity"</span><span class="sxs-lookup"><span data-stu-id="85274-929">'Identity'</span></span>
- <span data-ttu-id="85274-930">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="85274-930">'Let's Encrypt'</span></span>
- <span data-ttu-id="85274-931">"Razor"</span><span class="sxs-lookup"><span data-stu-id="85274-931">'Razor'</span></span>
- <span data-ttu-id="85274-932">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="85274-932">'SignalR' uid:</span></span> 

-
<span data-ttu-id="85274-933">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="85274-933">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="85274-934">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="85274-934">'Blazor'</span></span>
- <span data-ttu-id="85274-935">"Identity"</span><span class="sxs-lookup"><span data-stu-id="85274-935">'Identity'</span></span>
- <span data-ttu-id="85274-936">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="85274-936">'Let's Encrypt'</span></span>
- <span data-ttu-id="85274-937">"Razor"</span><span class="sxs-lookup"><span data-stu-id="85274-937">'Razor'</span></span>
- <span data-ttu-id="85274-938">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="85274-938">'SignalR' uid:</span></span> 

-
<span data-ttu-id="85274-939">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="85274-939">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="85274-940">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="85274-940">'Blazor'</span></span>
- <span data-ttu-id="85274-941">"Identity"</span><span class="sxs-lookup"><span data-stu-id="85274-941">'Identity'</span></span>
- <span data-ttu-id="85274-942">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="85274-942">'Let's Encrypt'</span></span>
- <span data-ttu-id="85274-943">"Razor"</span><span class="sxs-lookup"><span data-stu-id="85274-943">'Razor'</span></span>
- <span data-ttu-id="85274-944">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="85274-944">'SignalR' uid:</span></span> 

-
<span data-ttu-id="85274-945">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="85274-945">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="85274-946">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="85274-946">'Blazor'</span></span>
- <span data-ttu-id="85274-947">"Identity"</span><span class="sxs-lookup"><span data-stu-id="85274-947">'Identity'</span></span>
- <span data-ttu-id="85274-948">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="85274-948">'Let's Encrypt'</span></span>
- <span data-ttu-id="85274-949">"Razor"</span><span class="sxs-lookup"><span data-stu-id="85274-949">'Razor'</span></span>
- <span data-ttu-id="85274-950">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="85274-950">'SignalR' uid:</span></span> 

-
<span data-ttu-id="85274-951">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="85274-951">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="85274-952">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="85274-952">'Blazor'</span></span>
- <span data-ttu-id="85274-953">"Identity"</span><span class="sxs-lookup"><span data-stu-id="85274-953">'Identity'</span></span>
- <span data-ttu-id="85274-954">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="85274-954">'Let's Encrypt'</span></span>
- <span data-ttu-id="85274-955">"Razor"</span><span class="sxs-lookup"><span data-stu-id="85274-955">'Razor'</span></span>
- <span data-ttu-id="85274-956">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="85274-956">'SignalR' uid:</span></span> 

-
<span data-ttu-id="85274-957">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="85274-957">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="85274-958">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="85274-958">'Blazor'</span></span>
- <span data-ttu-id="85274-959">"Identity"</span><span class="sxs-lookup"><span data-stu-id="85274-959">'Identity'</span></span>
- <span data-ttu-id="85274-960">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="85274-960">'Let's Encrypt'</span></span>
- <span data-ttu-id="85274-961">"Razor"</span><span class="sxs-lookup"><span data-stu-id="85274-961">'Razor'</span></span>
- <span data-ttu-id="85274-962">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="85274-962">'SignalR' uid:</span></span> 

-
<span data-ttu-id="85274-963">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="85274-963">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="85274-964">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="85274-964">'Blazor'</span></span>
- <span data-ttu-id="85274-965">"Identity"</span><span class="sxs-lookup"><span data-stu-id="85274-965">'Identity'</span></span>
- <span data-ttu-id="85274-966">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="85274-966">'Let's Encrypt'</span></span>
- <span data-ttu-id="85274-967">"Razor"</span><span class="sxs-lookup"><span data-stu-id="85274-967">'Razor'</span></span>
- <span data-ttu-id="85274-968">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="85274-968">'SignalR' uid:</span></span> 

-
<span data-ttu-id="85274-969">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="85274-969">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="85274-970">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="85274-970">'Blazor'</span></span>
- <span data-ttu-id="85274-971">"Identity"</span><span class="sxs-lookup"><span data-stu-id="85274-971">'Identity'</span></span>
- <span data-ttu-id="85274-972">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="85274-972">'Let's Encrypt'</span></span>
- <span data-ttu-id="85274-973">"Razor"</span><span class="sxs-lookup"><span data-stu-id="85274-973">'Razor'</span></span>
- <span data-ttu-id="85274-974">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="85274-974">'SignalR' uid:</span></span> 

-
<span data-ttu-id="85274-975">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="85274-975">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="85274-976">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="85274-976">'Blazor'</span></span>
- <span data-ttu-id="85274-977">"Identity"</span><span class="sxs-lookup"><span data-stu-id="85274-977">'Identity'</span></span>
- <span data-ttu-id="85274-978">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="85274-978">'Let's Encrypt'</span></span>
- <span data-ttu-id="85274-979">"Razor"</span><span class="sxs-lookup"><span data-stu-id="85274-979">'Razor'</span></span>
- <span data-ttu-id="85274-980">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="85274-980">'SignalR' uid:</span></span> 

<span data-ttu-id="85274-981">--------------- | | `Http1`                    | Solo HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="85274-981">--------------- | | `Http1`                    | HTTP/1.1 only.</span></span> <span data-ttu-id="85274-982">Puede usarse con o sin TLS.</span><span class="sxs-lookup"><span data-stu-id="85274-982">Can be used with or without TLS.</span></span> <span data-ttu-id="85274-983">| | `Http2`                    | Solo HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="85274-983">| | `Http2`                    | HTTP/2 only.</span></span> <span data-ttu-id="85274-984">Se pueden utilizar sin TLS solo si el cliente admite un [modo de conocimientos previos](https://tools.ietf.org/html/rfc7540#section-3.4).</span><span class="sxs-lookup"><span data-stu-id="85274-984">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> <span data-ttu-id="85274-985">| | `Http1AndHttp2`            | HTTP/1.1 y HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="85274-985">| | `Http1AndHttp2`            | HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="85274-986">HTTP/2 necesita TLS y una conexión de [Negociación de protocolo de nivel de aplicación (ALPN)](https://tools.ietf.org/html/rfc7301#section-3); en caso contrario, la opción predeterminada para la conexión es HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="85274-986">HTTP/2 requires a TLS and [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="85274-987">El protocolo predeterminado es HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="85274-987">The default protocol is HTTP/1.1.</span></span>

<span data-ttu-id="85274-988">Restricciones de TLS para HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="85274-988">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="85274-989">TLS 1.2 o versiones posteriores</span><span class="sxs-lookup"><span data-stu-id="85274-989">TLS version 1.2 or later</span></span>
* <span data-ttu-id="85274-990">Renegociación deshabilitada</span><span class="sxs-lookup"><span data-stu-id="85274-990">Renegotiation disabled</span></span>
* <span data-ttu-id="85274-991">Compresión deshabilitada</span><span class="sxs-lookup"><span data-stu-id="85274-991">Compression disabled</span></span>
* <span data-ttu-id="85274-992">Tamaños de intercambio de claves efímeras mínimos:</span><span class="sxs-lookup"><span data-stu-id="85274-992">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="85274-993">Curva elíptica Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits como mínimo</span><span class="sxs-lookup"><span data-stu-id="85274-993">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits minimum</span></span>
  * <span data-ttu-id="85274-994">Campo finito Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits como mínimo</span><span class="sxs-lookup"><span data-stu-id="85274-994">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits minimum</span></span>
* <span data-ttu-id="85274-995">Conjunto de cifrado no restringido</span><span class="sxs-lookup"><span data-stu-id="85274-995">Cipher suite not blacklisted</span></span>

<span data-ttu-id="85274-996">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; con la curva elíptica P-256 &lbrack;`FIPS186`&rbrack; es compatible de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="85274-996">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="85274-997">El siguiente ejemplo permite conexiones HTTP/1.1 y HTTP/2 en el puerto 8000.</span><span class="sxs-lookup"><span data-stu-id="85274-997">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="85274-998">Las conexiones se protegen mediante TLS con un certificado proporcionado:</span><span class="sxs-lookup"><span data-stu-id="85274-998">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
.ConfigureKestrel((context, serverOptions) =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.Protocols = HttpProtocols.Http1AndHttp2;
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="85274-999">Opcionalmente, cree una implementación `IConnectionAdapter` para filtrar los protocolos de enlace TLS en una base por conexión para los cifrados específicos:</span><span class="sxs-lookup"><span data-stu-id="85274-999">Optionally create an `IConnectionAdapter` implementation to filter TLS handshakes on a per-connection basis for specific ciphers:</span></span>

```csharp
.ConfigureKestrel((context, serverOptions) =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.Protocols = HttpProtocols.Http1AndHttp2;
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.ConnectionAdapters.Add(new TlsFilterAdapter());
    });
});
```

```csharp
private class TlsFilterAdapter : IConnectionAdapter
{
    public bool IsHttps => false;

    public Task<IAdaptedConnection> OnConnectionAsync(ConnectionAdapterContext context)
    {
        var tlsFeature = context.Features.Get<ITlsHandshakeFeature>();

        // Throw NotSupportedException for any cipher algorithm that the app doesn't
        // wish to support. Alternatively, define and compare
        // ITlsHandshakeFeature.CipherAlgorithm to a list of acceptable cipher
        // suites.
        //
        // No encryption is used with a CipherAlgorithmType.Null cipher algorithm.
        if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
        {
            throw new NotSupportedException("Prohibited cipher: " + tlsFeature.CipherAlgorithm);
        }

        return Task.FromResult<IAdaptedConnection>(new AdaptedConnection(context.ConnectionStream));
    }

    private class AdaptedConnection : IAdaptedConnection
    {
        public AdaptedConnection(Stream adaptedStream)
        {
            ConnectionStream = adaptedStream;
        }

        public Stream ConnectionStream { get; }

        public void Dispose()
        {
        }
    }
}
```

<span data-ttu-id="85274-1000">*Defina el protocolo a partir de la configuración*</span><span class="sxs-lookup"><span data-stu-id="85274-1000">*Set the protocol from configuration*</span></span>

<span data-ttu-id="85274-1001"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> llama a `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` de forma predeterminada para cargar la configuración de Kestrel.</span><span class="sxs-lookup"><span data-stu-id="85274-1001"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="85274-1002">En el siguiente ejemplo de *appsettings.json*, se establece un protocolo de conexión predeterminado (HTTP/1.1 y HTTP/2) para todos los puntos de conexión de Kestrel:</span><span class="sxs-lookup"><span data-stu-id="85274-1002">In the following *appsettings.json* example, a default connection protocol (HTTP/1.1 and HTTP/2) is established for all of Kestrel's endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1AndHttp2"
    }
  }
}
```

<span data-ttu-id="85274-1003">El siguiente ejemplo de archivo de configuración establece un protocolo de conexión para un punto de conexión específico:</span><span class="sxs-lookup"><span data-stu-id="85274-1003">The following configuration file example establishes a connection protocol for a specific endpoint:</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "HttpsDefaultCert": {
        "Url": "https://localhost:5001",
        "Protocols": "Http1AndHttp2"
      }
    }
  }
}
```

<span data-ttu-id="85274-1004">Los protocolos especificados en el código invalidan los valores establecidos por la configuración.</span><span class="sxs-lookup"><span data-stu-id="85274-1004">Protocols specified in code override values set by configuration.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="85274-1005">Configuración de transporte</span><span class="sxs-lookup"><span data-stu-id="85274-1005">Transport configuration</span></span>

<span data-ttu-id="85274-1006">Desde el lanzamiento de ASP.NET Core 2.1, el transporte predeterminado de Kestrel deja de basarse en Libuv y pasa a basarse en sockets administrados.</span><span class="sxs-lookup"><span data-stu-id="85274-1006">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="85274-1007">Se trata de un cambio muy importante para las aplicaciones ASP.NET Core 2.0 que actualizan a 2.1 y llaman a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>, y que dependen de cualquiera de los siguientes paquetes:</span><span class="sxs-lookup"><span data-stu-id="85274-1007">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="85274-1008">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (referencia de paquete directa)</span><span class="sxs-lookup"><span data-stu-id="85274-1008">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="85274-1009">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="85274-1009">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="85274-1010">Para los proyectos que necesitan el uso de Libuv:</span><span class="sxs-lookup"><span data-stu-id="85274-1010">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="85274-1011">Agregar una dependencia del paquete [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) al archivo de proyecto de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="85274-1011">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="85274-1012">Llame a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span><span class="sxs-lookup"><span data-stu-id="85274-1012">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

  ```csharp
  public class Program
  {
      public static void Main(string[] args)
      {
          CreateWebHostBuilder(args).Build().Run();
      }

      public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
          WebHost.CreateDefaultBuilder(args)
              .UseLibuv()
              .UseStartup<Startup>();
  }
  ```

### <a name="url-prefixes"></a><span data-ttu-id="85274-1013">Prefijos de URL</span><span class="sxs-lookup"><span data-stu-id="85274-1013">URL prefixes</span></span>

<span data-ttu-id="85274-1014">Al usar `UseUrls`, el argumento de línea de comandos `--urls`, la clave de configuración de host `urls` o una variable de entorno `ASPNETCORE_URLS`, los prefijos de dirección URL pueden tener cualquiera de estos formatos.</span><span class="sxs-lookup"><span data-stu-id="85274-1014">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="85274-1015">Solo son válidos los prefijos de dirección URL HTTP.</span><span class="sxs-lookup"><span data-stu-id="85274-1015">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="85274-1016">Kestrel no admite HTTPS al configurar enlaces de dirección URL con `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="85274-1016">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="85274-1017">Dirección IPv4 con número de puerto</span><span class="sxs-lookup"><span data-stu-id="85274-1017">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="85274-1018">`0.0.0.0` es un caso especial que enlaza a todas las direcciones IPv4.</span><span class="sxs-lookup"><span data-stu-id="85274-1018">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="85274-1019">Dirección IPv6 con número de puerto</span><span class="sxs-lookup"><span data-stu-id="85274-1019">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="85274-1020">`[::]` es el equivalente en IPv6 de `0.0.0.0` en IPv4.</span><span class="sxs-lookup"><span data-stu-id="85274-1020">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="85274-1021">Nombre de host con número de puerto</span><span class="sxs-lookup"><span data-stu-id="85274-1021">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="85274-1022">Los nombres de host, `*` y `+` no son especiales.</span><span class="sxs-lookup"><span data-stu-id="85274-1022">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="85274-1023">Todo lo que no se identifique como una dirección IP o un `localhost` válido se enlaza a todas las direcciones IP de IPv6 e IPv4.</span><span class="sxs-lookup"><span data-stu-id="85274-1023">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="85274-1024">Para enlazar distintos nombres de host a distintas aplicaciones ASP.NET Core en el mismo puerto, use [HTTP.sys](xref:fundamentals/servers/httpsys) o un servidor proxy inverso, como IIS, Nginx o Apache.</span><span class="sxs-lookup"><span data-stu-id="85274-1024">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="85274-1025">El hospedaje en una configuración de proxy inverso requiere [filtrado de hosts](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="85274-1025">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="85274-1026">Nombre `localhost` del host con el número de puerto o la IP de bucle invertido con el número de puerto</span><span class="sxs-lookup"><span data-stu-id="85274-1026">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="85274-1027">Cuando se especifica `localhost`, Kestrel intenta enlazar a las interfaces de bucle invertido de IPv4 e IPv6.</span><span class="sxs-lookup"><span data-stu-id="85274-1027">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="85274-1028">Si el puerto solicitado lo está usando otro servicio en cualquier interfaz de bucle invertido, Kestrel no se puede iniciar.</span><span class="sxs-lookup"><span data-stu-id="85274-1028">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="85274-1029">Si ninguna de estas interfaces de bucle invertido está disponible por cualquier otra razón (normalmente porque no se admite IPv6), Kestrel registra una advertencia.</span><span class="sxs-lookup"><span data-stu-id="85274-1029">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="85274-1030">Filtrado de hosts</span><span class="sxs-lookup"><span data-stu-id="85274-1030">Host filtering</span></span>

<span data-ttu-id="85274-1031">Si bien Kestrel admite una configuración basada en prefijos como `http://example.com:5000`, pasa por alto completamente el nombre de host.</span><span class="sxs-lookup"><span data-stu-id="85274-1031">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="85274-1032">El host `localhost` es un caso especial que se usa para enlazar a direcciones de bucle invertido.</span><span class="sxs-lookup"><span data-stu-id="85274-1032">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="85274-1033">Cualquier otro host que no sea una dirección IP explícita se enlaza a todas las direcciones IP públicas.</span><span class="sxs-lookup"><span data-stu-id="85274-1033">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="85274-1034">Los encabezados `Host` no están validados.</span><span class="sxs-lookup"><span data-stu-id="85274-1034">`Host` headers aren't validated.</span></span>

<span data-ttu-id="85274-1035">Como solución alternativa, use el Middleware de filtrado de hosts.</span><span class="sxs-lookup"><span data-stu-id="85274-1035">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="85274-1036">El middleware de filtrado de host se facilita a través del paquete [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering), que se incluye en el [metapaquete Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 o 2.2).</span><span class="sxs-lookup"><span data-stu-id="85274-1036">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="85274-1037">El middleware se agrega por medio de <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, que llama a <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span><span class="sxs-lookup"><span data-stu-id="85274-1037">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="85274-1038">El Middleware de filtrado de hosts está deshabilitado de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="85274-1038">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="85274-1039">Para habilitarlo, defina una clave `AllowedHosts` en *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span><span class="sxs-lookup"><span data-stu-id="85274-1039">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="85274-1040">El valor es una lista delimitada por punto y coma de nombres de host sin los números de puerto:</span><span class="sxs-lookup"><span data-stu-id="85274-1040">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="85274-1041">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="85274-1041">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="85274-1042">[Middleware de encabezados reenviados](xref:host-and-deploy/proxy-load-balancer) también tiene una opción <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts>.</span><span class="sxs-lookup"><span data-stu-id="85274-1042">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="85274-1043">El Middleware de encabezados reenviados y el Middleware de filtrado de hosts tienen una funcionalidad similar en diferentes escenarios.</span><span class="sxs-lookup"><span data-stu-id="85274-1043">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="85274-1044">Establecer `AllowedHosts` con el Middleware de encabezados reenviados es adecuado cuando el encabezado `Host` no se conserva mientras se reenvían solicitudes con un servidor proxy inverso o un equilibrador de carga.</span><span class="sxs-lookup"><span data-stu-id="85274-1044">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="85274-1045">Establecer `AllowedHosts` con el Middleware de filtrado de hosts es adecuado cuando se usa Kestrel como un servidor perimetral de acceso público o cuando el encabezado `Host` se reenvía directamente.</span><span class="sxs-lookup"><span data-stu-id="85274-1045">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="85274-1046">Para obtener más información sobre el Middleware de encabezados reenviados, consulte <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="85274-1046">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="85274-1047">Kestrel es un [servidor web multiplataforma de ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="85274-1047">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="85274-1048">Kestrel es el servidor web que se incluye de forma predeterminada en las plantillas de proyecto de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="85274-1048">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="85274-1049">Kestrel admite los siguientes escenarios:</span><span class="sxs-lookup"><span data-stu-id="85274-1049">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="85274-1050">HTTPS</span><span class="sxs-lookup"><span data-stu-id="85274-1050">HTTPS</span></span>
* <span data-ttu-id="85274-1051">Actualización opaca para habilitar [WebSockets](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="85274-1051">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="85274-1052">Sockets de Unix para alto rendimiento detrás de Nginx</span><span class="sxs-lookup"><span data-stu-id="85274-1052">Unix sockets for high performance behind Nginx</span></span>

<span data-ttu-id="85274-1053">Kestrel admite todas las plataformas y versiones que sean compatibles con .NET Core.</span><span class="sxs-lookup"><span data-stu-id="85274-1053">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="85274-1054">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="85274-1054">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="85274-1055">Cuándo usar Kestrel con un proxy inverso</span><span class="sxs-lookup"><span data-stu-id="85274-1055">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="85274-1056">Kestrel se puede usar por sí solo o con un *servidor proxy inverso*, como [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org) o [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="85274-1056">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="85274-1057">Un servidor proxy inverso recibe las solicitudes HTTP de la red y las reenvía a Kestrel.</span><span class="sxs-lookup"><span data-stu-id="85274-1057">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="85274-1058">Kestrel empleado como servidor web perimetral (accesible desde Internet):</span><span class="sxs-lookup"><span data-stu-id="85274-1058">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel se comunica directamente con Internet sin ningún servidor proxy inverso](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="85274-1060">Kestrel empleado en una configuración de proxy inverso:</span><span class="sxs-lookup"><span data-stu-id="85274-1060">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel se comunica indirectamente con Internet a través de un servidor proxy inverso, como IIS, Nginx o Apache](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="85274-1062">Cualquiera de las configuraciones, con o sin servidor proxy inverso, es una configuración de hospedaje admitida.</span><span class="sxs-lookup"><span data-stu-id="85274-1062">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="85274-1063">Kestrel, utilizado como un servidor perimetral sin un servidor proxy inverso, no permite compartir la misma dirección IP y el mismo puerto entre varios procesos.</span><span class="sxs-lookup"><span data-stu-id="85274-1063">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="85274-1064">Si Kestrel se configura para escuchar en un puerto, controla todo el tráfico de ese puerto, independientemente de los encabezados `Host` de las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="85274-1064">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="85274-1065">Un proxy inverso que puede compartir puertos es capaz de reenviar solicitudes a Kestrel en una única dirección IP y puerto.</span><span class="sxs-lookup"><span data-stu-id="85274-1065">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="85274-1066">Aunque no sea necesario un servidor proxy inverso, su uso puede ser útil.</span><span class="sxs-lookup"><span data-stu-id="85274-1066">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="85274-1067">Un proxy inverso puede hacer lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="85274-1067">A reverse proxy:</span></span>

* <span data-ttu-id="85274-1068">Limitar el área expuesta públicamente de las aplicaciones que hospeda.</span><span class="sxs-lookup"><span data-stu-id="85274-1068">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="85274-1069">Proporcionar una capa extra de configuración y defensa.</span><span class="sxs-lookup"><span data-stu-id="85274-1069">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="85274-1070">Posiblemente, integrarse mejor con la infraestructura existente.</span><span class="sxs-lookup"><span data-stu-id="85274-1070">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="85274-1071">Simplificar el equilibrio de carga y la configuración de una comunicación segura (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="85274-1071">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="85274-1072">Solamente el servidor proxy inverso necesita un certificado X.509 y dicho servidor se puede comunicar con los servidores de la aplicación en la red interna por medio de HTTP sin formato.</span><span class="sxs-lookup"><span data-stu-id="85274-1072">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="85274-1073">El hospedaje en una configuración de proxy inverso requiere [filtrado de hosts](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="85274-1073">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="85274-1074">Cómo usar Kestrel en aplicaciones ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="85274-1074">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="85274-1075">El paquete [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) se incluye en el [metapaquete Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="85274-1075">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="85274-1076">Las plantillas de proyecto de ASP.NET Core usan Kestrel de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="85274-1076">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="85274-1077">En *Program.cs*, el código de plantilla llama a <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, que a su vez llama a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> en segundo plano.</span><span class="sxs-lookup"><span data-stu-id="85274-1077">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

<span data-ttu-id="85274-1078">Para proporcionar configuración adicional después de llamar a `CreateDefaultBuilder`, llame a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span><span class="sxs-lookup"><span data-stu-id="85274-1078">To provide additional configuration after calling `CreateDefaultBuilder`, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="85274-1079">Para más información sobre `CreateDefaultBuilder` y la compilación del host, consulte la sección sobre *cómo configurar un host* de <xref:fundamentals/host/web-host#set-up-a-host>.</span><span class="sxs-lookup"><span data-stu-id="85274-1079">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

## <a name="kestrel-options"></a><span data-ttu-id="85274-1080">Opciones de Kestrel</span><span class="sxs-lookup"><span data-stu-id="85274-1080">Kestrel options</span></span>

<span data-ttu-id="85274-1081">El servidor web Kestrel tiene opciones de configuración de restricción que son especialmente útiles en las implementaciones con conexión a Internet.</span><span class="sxs-lookup"><span data-stu-id="85274-1081">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="85274-1082">Establezca restricciones en la propiedad <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> de la clase <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>.</span><span class="sxs-lookup"><span data-stu-id="85274-1082">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="85274-1083">La propiedad `Limits` contiene una instancia de la clase <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>.</span><span class="sxs-lookup"><span data-stu-id="85274-1083">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="85274-1084">En los ejemplos siguientes se usa el espacio de nombres <xref:Microsoft.AspNetCore.Server.Kestrel.Core>:</span><span class="sxs-lookup"><span data-stu-id="85274-1084">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="85274-1085">Las opciones de Kestrel, que se configuran en código de C# en los siguientes ejemplos, también se pueden establecer mediante un [proveedor de configuración](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="85274-1085">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="85274-1086">Por ejemplo, el proveedor de configuración de archivos puede cargar la configuración de Kestrel desde un archivo *appsettings.json* o *appsettings.{Environment}.json*:</span><span class="sxs-lookup"><span data-stu-id="85274-1086">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    }
  }
}
```

<span data-ttu-id="85274-1087">Siga **uno** de estos procedimientos:</span><span class="sxs-lookup"><span data-stu-id="85274-1087">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="85274-1088">Configure Kestrel en `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="85274-1088">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="85274-1089">Inserte una instancia de `IConfiguration` en la clase `Startup`.</span><span class="sxs-lookup"><span data-stu-id="85274-1089">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="85274-1090">En el ejemplo siguiente se da por supuesto que la configuración insertada está asignada a la propiedad `Configuration`.</span><span class="sxs-lookup"><span data-stu-id="85274-1090">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="85274-1091">En `Startup.ConfigureServices`, cargue la sección de configuración `Kestrel` en la configuración de Kestrel:</span><span class="sxs-lookup"><span data-stu-id="85274-1091">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

     ```csharp
     using Microsoft.Extensions.Configuration
     
     public class Startup
     {
         public Startup(IConfiguration configuration)
         {
             Configuration = configuration;
         }

         public IConfiguration Configuration { get; }

         public void ConfigureServices(IServiceCollection services)
         {
             services.Configure<KestrelServerOptions>(
                 Configuration.GetSection("Kestrel"));
         }

         public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
         {
             ...
         }
     }
     ```

* <span data-ttu-id="85274-1092">Configure Kestrel al compilar el host:</span><span class="sxs-lookup"><span data-stu-id="85274-1092">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="85274-1093">En *Program.cs*, cargue la sección de configuración `Kestrel` en la configuración de Kestrel:</span><span class="sxs-lookup"><span data-stu-id="85274-1093">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
      WebHost.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .UseStartup<Startup>();
  ```

<span data-ttu-id="85274-1094">Los dos procedimientos anteriores funcionan con cualquier [proveedor de configuración](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="85274-1094">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="85274-1095">Tiempo de expiración de la conexión persistente</span><span class="sxs-lookup"><span data-stu-id="85274-1095">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="85274-1096">Obtiene o establece el [tiempo de expiración de la conexión persistente](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="85274-1096">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="85274-1097">El valor predeterminado es de 2 minutos.</span><span class="sxs-lookup"><span data-stu-id="85274-1097">Defaults to 2 minutes.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.KeepAliveTimeout = TimeSpan.FromMinutes(2);
        });
```

### <a name="maximum-client-connections"></a><span data-ttu-id="85274-1098">Las conexiones máximas de cliente</span><span class="sxs-lookup"><span data-stu-id="85274-1098">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="85274-1099">El número máximo de conexiones de TCP abiertas simultáneas que se pueden establecer para toda la aplicación con este código:</span><span class="sxs-lookup"><span data-stu-id="85274-1099">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentConnections = 100;
        });
```

<span data-ttu-id="85274-1100">Hay un límite independiente para las conexiones que se han actualizado desde HTTP o HTTPS a otro protocolo (por ejemplo, en una solicitud de WebSockets).</span><span class="sxs-lookup"><span data-stu-id="85274-1100">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="85274-1101">Cuando se actualiza una conexión, no se cuenta con respecto al límite de `MaxConcurrentConnections`.</span><span class="sxs-lookup"><span data-stu-id="85274-1101">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentUpgradedConnections = 100;
        });
```

<span data-ttu-id="85274-1102">El número máximo de conexiones es ilimitado de forma predeterminada (null).</span><span class="sxs-lookup"><span data-stu-id="85274-1102">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="85274-1103">El tamaño máximo del cuerpo de solicitud</span><span class="sxs-lookup"><span data-stu-id="85274-1103">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="85274-1104">El tamaño máximo predeterminado del cuerpo de solicitud es 30 000 000 bytes, que son aproximadamente 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="85274-1104">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="85274-1105">El método recomendado para invalidar el límite de una aplicación ASP.NET Core MVC es usar el atributo <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> en un método de acción:</span><span class="sxs-lookup"><span data-stu-id="85274-1105">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="85274-1106">Este es un ejemplo que muestra cómo configurar la restricción en la aplicación y todas las solicitudes:</span><span class="sxs-lookup"><span data-stu-id="85274-1106">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxRequestBodySize = 10 * 1024;
        });
```

<span data-ttu-id="85274-1107">Invalide la configuración en una solicitud específica de middleware:</span><span class="sxs-lookup"><span data-stu-id="85274-1107">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="85274-1108">Se inicia una excepción si la aplicación configura el límite de una solicitud después de que la aplicación haya empezado a leer la solicitud.</span><span class="sxs-lookup"><span data-stu-id="85274-1108">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="85274-1109">Hay una propiedad `IsReadOnly` que señala si la propiedad `MaxRequestBodySize` tiene el estado de solo lectura, lo que significa que es demasiado tarde para configurar el límite.</span><span class="sxs-lookup"><span data-stu-id="85274-1109">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="85274-1110">Cuando se ejecuta una aplicación [fuera de proceso](xref:host-and-deploy/iis/index#out-of-process-hosting-model) detrás del [módulo de ASP.NET Core](xref:host-and-deploy/aspnet-core-module), el límite de tamaño del cuerpo de la solicitud de Kestrel se deshabilita porque IIS ya establece el límite.</span><span class="sxs-lookup"><span data-stu-id="85274-1110">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="85274-1111">La velocidad mínima de los datos del cuerpo de solicitud.</span><span class="sxs-lookup"><span data-stu-id="85274-1111">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="85274-1112">Kestrel comprueba cada segundo si los datos entran a la velocidad especificada en bytes por segundo.</span><span class="sxs-lookup"><span data-stu-id="85274-1112">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="85274-1113">Si la velocidad está por debajo del mínimo, se agota el tiempo de espera de la conexión. El período de gracia es la cantidad de tiempo que Kestrel da al cliente para aumentar su velocidad de envío hasta el mínimo; no se comprueba la velocidad durante ese tiempo.</span><span class="sxs-lookup"><span data-stu-id="85274-1113">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="85274-1114">Este período de gracia permite evitar que se interrumpan las conexiones que inicialmente están enviando datos a una velocidad lenta debido a un inicio lento de TCP.</span><span class="sxs-lookup"><span data-stu-id="85274-1114">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="85274-1115">La velocidad mínima predeterminada es 240 bytes por segundo, con un período de gracia de cinco segundos.</span><span class="sxs-lookup"><span data-stu-id="85274-1115">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="85274-1116">También se aplica una velocidad mínima a la respuesta.</span><span class="sxs-lookup"><span data-stu-id="85274-1116">A minimum rate also applies to the response.</span></span> <span data-ttu-id="85274-1117">El código para establecer el límite de solicitudes y el límite de respuestas es el mismo, salvo que tienen `RequestBody` o `Response` en los nombres de propiedad y de interfaz.</span><span class="sxs-lookup"><span data-stu-id="85274-1117">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="85274-1118">Este es un ejemplo que muestra cómo configurar las velocidades de datos mínimas en *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="85274-1118">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MinRequestBodyDataRate =
                new MinDataRate(bytesPerSecond: 100, gracePeriod: TimeSpan.FromSeconds(10));
            serverOptions.Limits.MinResponseDataRate =
                new MinDataRate(bytesPerSecond: 100, gracePeriod: TimeSpan.FromSeconds(10));
        });
```

### <a name="request-headers-timeout"></a><span data-ttu-id="85274-1119">Tiempo de expiración de los encabezados de solicitud</span><span class="sxs-lookup"><span data-stu-id="85274-1119">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="85274-1120">Obtiene o establece la cantidad máxima de tiempo que el servidor pasa recibiendo las cabeceras de las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="85274-1120">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="85274-1121">El valor predeterminado es 30 segundos.</span><span class="sxs-lookup"><span data-stu-id="85274-1121">Defaults to 30 seconds.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.RequestHeadersTimeout = TimeSpan.FromMinutes(1);
        });
```

### <a name="synchronous-io"></a><span data-ttu-id="85274-1122">E/S síncrona</span><span class="sxs-lookup"><span data-stu-id="85274-1122">Synchronous I/O</span></span>

<span data-ttu-id="85274-1123"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controla si se permite la E/S sincrónica para la solicitud y la respuesta.</span><span class="sxs-lookup"><span data-stu-id="85274-1123"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="85274-1124">El valor predeterminado es `true`.</span><span class="sxs-lookup"><span data-stu-id="85274-1124">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="85274-1125">Un gran número de operaciones de E/S sincrónicas de bloqueo puede dar lugar al colapso del grupo de subprocesos, lo que hace que la aplicación no responda.</span><span class="sxs-lookup"><span data-stu-id="85274-1125">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="85274-1126">Habilite solo `AllowSynchronousIO` al usar una biblioteca que no admite la E/S asincrónica.</span><span class="sxs-lookup"><span data-stu-id="85274-1126">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="85274-1127">En el ejemplo siguiente se deshabilita la E/S sincrónica:</span><span class="sxs-lookup"><span data-stu-id="85274-1127">The following example disables synchronous I/O:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.AllowSynchronousIO = false;
        });
```

<span data-ttu-id="85274-1128">Para más información sobre otras opciones y límites de Kestrel, vea:</span><span class="sxs-lookup"><span data-stu-id="85274-1128">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="85274-1129">Configuración de punto de conexión</span><span class="sxs-lookup"><span data-stu-id="85274-1129">Endpoint configuration</span></span>

<span data-ttu-id="85274-1130">ASP.NET Core enlaza de forma predeterminada a:</span><span class="sxs-lookup"><span data-stu-id="85274-1130">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="85274-1131">`https://localhost:5001` (cuando hay presente un certificado de desarrollo local)</span><span class="sxs-lookup"><span data-stu-id="85274-1131">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="85274-1132">Especifique direcciones URL mediante los siguientes elementos:</span><span class="sxs-lookup"><span data-stu-id="85274-1132">Specify URLs using the:</span></span>

* <span data-ttu-id="85274-1133">La variable de entorno `ASPNETCORE_URLS`.</span><span class="sxs-lookup"><span data-stu-id="85274-1133">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="85274-1134">El argumento de la línea de comandos `--urls`.</span><span class="sxs-lookup"><span data-stu-id="85274-1134">`--urls` command-line argument.</span></span>
* <span data-ttu-id="85274-1135">La clave de configuración de host `urls`.</span><span class="sxs-lookup"><span data-stu-id="85274-1135">`urls` host configuration key.</span></span>
* <span data-ttu-id="85274-1136">El método de extensión `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="85274-1136">`UseUrls` extension method.</span></span>

<span data-ttu-id="85274-1137">El valor que estos métodos suministran puede ser uno o más puntos de conexión HTTP y HTTPS (este último, si hay disponible un certificado predeterminado).</span><span class="sxs-lookup"><span data-stu-id="85274-1137">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="85274-1138">Configure el valor como una lista separada por punto y coma (por ejemplo, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="85274-1138">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="85274-1139">Para más información sobre estos enfoques, consulte [Direcciones URL del servidor](xref:fundamentals/host/web-host#server-urls) e [Invalidar la configuración](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="85274-1139">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="85274-1140">Un certificado de desarrollo se crea:</span><span class="sxs-lookup"><span data-stu-id="85274-1140">A development certificate is created:</span></span>

* <span data-ttu-id="85274-1141">Cuando el [SDK de .NET Core](/dotnet/core/sdk) está instalado.</span><span class="sxs-lookup"><span data-stu-id="85274-1141">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="85274-1142">Para crear un certificado, se usa la [herramienta dev-certs](xref:aspnetcore-2.1#https).</span><span class="sxs-lookup"><span data-stu-id="85274-1142">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="85274-1143">Algunos exploradores necesitan que se conceda permiso explícito para confiar en el certificado de desarrollo local.</span><span class="sxs-lookup"><span data-stu-id="85274-1143">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="85274-1144">Las plantillas de proyecto configuran aplicaciones para que se ejecuten en HTTPS de forma predeterminada e incluyen [redirección de HTTPS y compatibilidad con HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="85274-1144">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="85274-1145">Llame a los métodos <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> o <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> de <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> para configurar los puertos y los prefijos de dirección URL para Kestrel.</span><span class="sxs-lookup"><span data-stu-id="85274-1145">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="85274-1146">`UseUrls`, el argumento de línea de comandos `--urls`, la clave de configuración de host `urls` y la variable de entorno `ASPNETCORE_URLS` también funcionan, pero tienen las limitaciones que se indican más adelante en esta sección (debe haber disponible un certificado predeterminado para la configuración de puntos de conexión HTTPS).</span><span class="sxs-lookup"><span data-stu-id="85274-1146">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="85274-1147">Configuración de `KestrelServerOptions`:</span><span class="sxs-lookup"><span data-stu-id="85274-1147">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="85274-1148">ConfigureEndpointDefaults(Action\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="85274-1148">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="85274-1149">Especifica una `Action` de configuración para que se ejecute con cada punto de conexión especificado.</span><span class="sxs-lookup"><span data-stu-id="85274-1149">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="85274-1150">Al llamar a `ConfigureEndpointDefaults` varias veces, se reemplazan las `Action` anteriores por la última `Action` especificada.</span><span class="sxs-lookup"><span data-stu-id="85274-1150">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
        });
```

> [!NOTE]
> <span data-ttu-id="85274-1151">Los puntos de conexión que se crean mediante una llamada a <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **antes** de llamar a <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> no tendrán aplicados los valores predeterminados.</span><span class="sxs-lookup"><span data-stu-id="85274-1151">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="85274-1152">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="85274-1152">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="85274-1153">Especifica una `Action` de configuración para que se ejecute con cada punto de conexión HTTPS.</span><span class="sxs-lookup"><span data-stu-id="85274-1153">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="85274-1154">Al llamar a `ConfigureHttpsDefaults` varias veces, se reemplazan las `Action` anteriores por la última `Action` especificada.</span><span class="sxs-lookup"><span data-stu-id="85274-1154">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                // certificate is an X509Certificate2
                listenOptions.ServerCertificate = certificate;
            });
        });
```

> [!NOTE]
> <span data-ttu-id="85274-1155">Los puntos de conexión que se crean mediante una llamada a <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **antes** de llamar a <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> no tendrán aplicados los valores predeterminados.</span><span class="sxs-lookup"><span data-stu-id="85274-1155">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="85274-1156">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="85274-1156">Configure(IConfiguration)</span></span>

<span data-ttu-id="85274-1157">Crea un cargador de configuración para configurar Kestrel que toma una <xref:Microsoft.Extensions.Configuration.IConfiguration> como entrada.</span><span class="sxs-lookup"><span data-stu-id="85274-1157">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="85274-1158">El ámbito de la configuración debe corresponderse con la sección de configuración de Kestrel.</span><span class="sxs-lookup"><span data-stu-id="85274-1158">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="85274-1159">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="85274-1159">ListenOptions.UseHttps</span></span>

<span data-ttu-id="85274-1160">Configure Kestrel para que use HTTPS.</span><span class="sxs-lookup"><span data-stu-id="85274-1160">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="85274-1161">Extensiones de `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="85274-1161">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="85274-1162">`UseHttps`: configure Kestrel para que use HTTPS con el certificado predeterminado.</span><span class="sxs-lookup"><span data-stu-id="85274-1162">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="85274-1163">Produce una excepción si no hay ningún certificado predeterminado configurado.</span><span class="sxs-lookup"><span data-stu-id="85274-1163">Throws an exception if no default certificate is configured.</span></span>
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

<span data-ttu-id="85274-1164">Parámetros de `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="85274-1164">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="85274-1165">`filename` es la ruta de acceso y el nombre de archivo de un archivo de certificado correspondiente al directorio donde están los archivos de contenido de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="85274-1165">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="85274-1166">`password` es la contraseña necesaria para obtener acceso a los datos del certificado X.509.</span><span class="sxs-lookup"><span data-stu-id="85274-1166">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="85274-1167">`configureOptions` es una `Action` para configurar `HttpsConnectionAdapterOptions`.</span><span class="sxs-lookup"><span data-stu-id="85274-1167">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="85274-1168">Devuelve `ListenOptions`.</span><span class="sxs-lookup"><span data-stu-id="85274-1168">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="85274-1169">`storeName` es el almacén de certificados desde el que se carga el certificado.</span><span class="sxs-lookup"><span data-stu-id="85274-1169">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="85274-1170">`subject` es el nombre del sujeto del certificado.</span><span class="sxs-lookup"><span data-stu-id="85274-1170">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="85274-1171">`allowInvalid` indica si se deben tener en cuenta los certificados no válidos, como los certificados autofirmados.</span><span class="sxs-lookup"><span data-stu-id="85274-1171">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="85274-1172">`location` es la ubicación del almacén desde el que se carga el certificado.</span><span class="sxs-lookup"><span data-stu-id="85274-1172">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="85274-1173">`serverCertificate` es el certificado X.509.</span><span class="sxs-lookup"><span data-stu-id="85274-1173">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="85274-1174">En un entorno de producción, HTTPS se debe configurar explícitamente.</span><span class="sxs-lookup"><span data-stu-id="85274-1174">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="85274-1175">Como mínimo, debe existir un certificado predeterminado.</span><span class="sxs-lookup"><span data-stu-id="85274-1175">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="85274-1176">Estas son las configuraciones compatibles:</span><span class="sxs-lookup"><span data-stu-id="85274-1176">Supported configurations described next:</span></span>

* <span data-ttu-id="85274-1177">Sin configuración</span><span class="sxs-lookup"><span data-stu-id="85274-1177">No configuration</span></span>
* <span data-ttu-id="85274-1178">Reemplazar el certificado predeterminado de configuración</span><span class="sxs-lookup"><span data-stu-id="85274-1178">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="85274-1179">Cambiar los valores predeterminados en el código</span><span class="sxs-lookup"><span data-stu-id="85274-1179">Change the defaults in code</span></span>

<span data-ttu-id="85274-1180">*Sin configuración*</span><span class="sxs-lookup"><span data-stu-id="85274-1180">*No configuration*</span></span>

<span data-ttu-id="85274-1181">Kestrel escucha en `http://localhost:5000` y en `https://localhost:5001` (si hay disponible un certificado predeterminado).</span><span class="sxs-lookup"><span data-stu-id="85274-1181">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="85274-1182">*Reemplazar el certificado predeterminado de configuración*</span><span class="sxs-lookup"><span data-stu-id="85274-1182">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="85274-1183">`CreateDefaultBuilder` llama a `Configure(context.Configuration.GetSection("Kestrel"))` de forma predeterminada para cargar la configuración de Kestrel.</span><span class="sxs-lookup"><span data-stu-id="85274-1183">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="85274-1184">Hay disponible un esquema de configuración de aplicación HTTPS predeterminado para Kestrel.</span><span class="sxs-lookup"><span data-stu-id="85274-1184">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="85274-1185">Configure varios puntos de conexión (incluidas las direcciones URL y los certificados que va a usar) desde un archivo en disco o desde un almacén de certificados.</span><span class="sxs-lookup"><span data-stu-id="85274-1185">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="85274-1186">En el siguiente ejemplo de *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="85274-1186">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="85274-1187">Establezca **AllowInvalid** en `true` para permitir el uso de certificados no válidos (por ejemplo, certificados autofirmados).</span><span class="sxs-lookup"><span data-stu-id="85274-1187">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="85274-1188">Cualquier punto de conexión HTTPS que no especifique un certificado (**HttpsDefaultCert** en el siguiente ejemplo) revierte al certificado definido en **Certificados** > **Predeterminado** o al certificado de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="85274-1188">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },

      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },

      "HttpsInlineCertStore": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },

      "HttpsDefaultCert": {
        "Url": "https://localhost:5003"
      },

      "Https": {
        "Url": "https://*:5004",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

<span data-ttu-id="85274-1189">Una alternativa al uso de **Path** y **Password** en cualquier nodo de certificado consiste en especificar el certificado por medio de campos del almacén de certificados.</span><span class="sxs-lookup"><span data-stu-id="85274-1189">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="85274-1190">Por ejemplo, el certificado en **Certificados** > **Predeterminado** se puede especificar así:</span><span class="sxs-lookup"><span data-stu-id="85274-1190">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="85274-1191">Notas sobre el esquema:</span><span class="sxs-lookup"><span data-stu-id="85274-1191">Schema notes:</span></span>

* <span data-ttu-id="85274-1192">En los nombres de los puntos de conexión se distingue entre mayúsculas y minúsculas.</span><span class="sxs-lookup"><span data-stu-id="85274-1192">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="85274-1193">Por ejemplo, `HTTPS` y `Https` son válidos.</span><span class="sxs-lookup"><span data-stu-id="85274-1193">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="85274-1194">El parámetro `Url` es necesario en cada punto de conexión.</span><span class="sxs-lookup"><span data-stu-id="85274-1194">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="85274-1195">El formato de este parámetro es el mismo que el del parámetro de configuración `Urls` de nivel superior, excepto por el hecho de que está limitado a un único valor.</span><span class="sxs-lookup"><span data-stu-id="85274-1195">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="85274-1196">En vez de agregarse, estos puntos de conexión reemplazan a los que están definidos en la configuración `Urls` de nivel superior.</span><span class="sxs-lookup"><span data-stu-id="85274-1196">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="85274-1197">Los puntos de conexión definidos en el código a través de `Listen` son acumulativos con respecto a los puntos de conexión definidos en la sección de configuración.</span><span class="sxs-lookup"><span data-stu-id="85274-1197">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="85274-1198">La sección `Certificate` es opcional.</span><span class="sxs-lookup"><span data-stu-id="85274-1198">The `Certificate` section is optional.</span></span> <span data-ttu-id="85274-1199">Si la sección `Certificate` no se especifica, se usan los valores predeterminados definidos en escenarios anteriores.</span><span class="sxs-lookup"><span data-stu-id="85274-1199">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="85274-1200">Si no hay valores predeterminados disponibles, el servidor produce una excepción y no se inicia.</span><span class="sxs-lookup"><span data-stu-id="85274-1200">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="85274-1201">La sección `Certificate` admite certificadostanto **Path**&ndash;**Password** como **Subject**&ndash;**Store**.</span><span class="sxs-lookup"><span data-stu-id="85274-1201">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="85274-1202">Se puede definir el número de puntos de conexión que se quiera de esta manera, siempre y cuando no produzcan conflictos de puerto.</span><span class="sxs-lookup"><span data-stu-id="85274-1202">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="85274-1203">`options.Configure(context.Configuration.GetSection("{SECTION}"))` devuelve un `KestrelConfigurationLoader` con un método `.Endpoint(string name, listenOptions => { })` que se puede usar para complementar la configuración de un punto de conexión configurado:</span><span class="sxs-lookup"><span data-stu-id="85274-1203">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
                .Endpoint("HTTPS", listenOptions =>
                {
                    listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
                });
        });
```

<span data-ttu-id="85274-1204">Se puede acceder directamente a `KestrelServerOptions.ConfigurationLoader` para seguir con la iteración en el cargador existente, como el proporcionado por <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span><span class="sxs-lookup"><span data-stu-id="85274-1204">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="85274-1205">La sección de configuración de cada punto de conexión está disponible en las opciones del método `Endpoint` para que se pueda leer la configuración personalizada.</span><span class="sxs-lookup"><span data-stu-id="85274-1205">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="85274-1206">Se pueden cargar varias configuraciones volviendo a llamar a `options.Configure(context.Configuration.GetSection("{SECTION}"))` con otra sección.</span><span class="sxs-lookup"><span data-stu-id="85274-1206">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="85274-1207">Se usa la última configuración, a menos que se llame explícitamente a `Load` en instancias anteriores.</span><span class="sxs-lookup"><span data-stu-id="85274-1207">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="85274-1208">El metapaquete no llama a `Load`, con lo cual su sección de configuración predeterminada se puede reemplazar.</span><span class="sxs-lookup"><span data-stu-id="85274-1208">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="85274-1209">`KestrelConfigurationLoader` refleja la familia `Listen` de API de `KestrelServerOptions` como sobrecargas de `Endpoint`, por lo que los puntos de conexión de configuración y código se pueden configurar en el mismo lugar.</span><span class="sxs-lookup"><span data-stu-id="85274-1209">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="85274-1210">En estas sobrecargas no se usan nombres y solo consumen valores predeterminados de la configuración.</span><span class="sxs-lookup"><span data-stu-id="85274-1210">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="85274-1211">*Cambiar los valores predeterminados en el código*</span><span class="sxs-lookup"><span data-stu-id="85274-1211">*Change the defaults in code*</span></span>

<span data-ttu-id="85274-1212">`ConfigureEndpointDefaults` y `ConfigureHttpsDefaults` se pueden usar para cambiar la configuración predeterminada de `ListenOptions` y `HttpsConnectionAdapterOptions`, incluido sustituir el certificado predeterminado especificado en el escenario anterior.</span><span class="sxs-lookup"><span data-stu-id="85274-1212">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="85274-1213">Se debe llamar a `ConfigureEndpointDefaults` y a `ConfigureHttpsDefaults` antes de que se configure algún punto de conexión.</span><span class="sxs-lookup"><span data-stu-id="85274-1213">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
            
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                listenOptions.SslProtocols = SslProtocols.Tls12;
            });
        });
```

<span data-ttu-id="85274-1214">*Compatibilidad de Kestrel con SNI*</span><span class="sxs-lookup"><span data-stu-id="85274-1214">*Kestrel support for SNI*</span></span>

<span data-ttu-id="85274-1215">[Indicación de nombre de servidor (SNI)](https://tools.ietf.org/html/rfc6066#section-3) se puede usar para hospedar varios dominios en la misma dirección IP y puerto.</span><span class="sxs-lookup"><span data-stu-id="85274-1215">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="85274-1216">Para que SNI funcione, el cliente envía el nombre de host de la sesión segura al servidor durante el protocolo de enlace TLS para que, de este modo, el servidor pueda proporcionar el certificado correcto.</span><span class="sxs-lookup"><span data-stu-id="85274-1216">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="85274-1217">El cliente usa el certificado proporcionado para la comunicación cifrada con el servidor durante la sesión segura que sigue al protocolo de enlace TLS.</span><span class="sxs-lookup"><span data-stu-id="85274-1217">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="85274-1218">Kestrel admite SNI a través de la devolución de llamada `ServerCertificateSelector`.</span><span class="sxs-lookup"><span data-stu-id="85274-1218">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="85274-1219">La devolución de llamada se invoca una vez por conexión para permitir que la aplicación inspeccione el nombre de host y seleccione el certificado adecuado.</span><span class="sxs-lookup"><span data-stu-id="85274-1219">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="85274-1220">La compatibilidad con SNI requiere lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="85274-1220">SNI support requires:</span></span>

* <span data-ttu-id="85274-1221">Ejecutarse en el marco de destino `netcoreapp2.1` o posterior.</span><span class="sxs-lookup"><span data-stu-id="85274-1221">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="85274-1222">En `net461` o posterior, se invoca la devolución de llamada, pero `name` siempre es `null`.</span><span class="sxs-lookup"><span data-stu-id="85274-1222">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="85274-1223">`name` también será `null` si el cliente no proporciona el parámetro de nombre de host en el protocolo de enlace TLS.</span><span class="sxs-lookup"><span data-stu-id="85274-1223">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="85274-1224">Todos los sitios web deben ejecutarse en la misma instancia de Kestrel.</span><span class="sxs-lookup"><span data-stu-id="85274-1224">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="85274-1225">Kestrel no admite el uso compartido de una dirección IP y un puerto entre varias instancias sin un proxy inverso.</span><span class="sxs-lookup"><span data-stu-id="85274-1225">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.ListenAnyIP(5005, listenOptions =>
            {
                listenOptions.UseHttps(httpsOptions =>
                {
                    var localhostCert = CertificateLoader.LoadFromStoreCert(
                        "localhost", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var exampleCert = CertificateLoader.LoadFromStoreCert(
                        "example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var subExampleCert = CertificateLoader.LoadFromStoreCert(
                        "sub.example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var certs = new Dictionary<string, X509Certificate2>(
                        StringComparer.OrdinalIgnoreCase);
                    certs["localhost"] = localhostCert;
                    certs["example.com"] = exampleCert;
                    certs["sub.example.com"] = subExampleCert;

                    httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
                    {
                        if (name != null && certs.TryGetValue(name, out var cert))
                        {
                            return cert;
                        }

                        return exampleCert;
                    };
                });
            });
        })
        .Build();
```

### <a name="connection-logging"></a><span data-ttu-id="85274-1226">Registro de conexiones</span><span class="sxs-lookup"><span data-stu-id="85274-1226">Connection logging</span></span>

<span data-ttu-id="85274-1227">Llame a <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> para emitir registros de nivel de depuración para la comunicación a nivel de bytes en una conexión.</span><span class="sxs-lookup"><span data-stu-id="85274-1227">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="85274-1228">El registro de conexiones es útil para solucionar problemas en la comunicación de bajo nivel, como durante el cifrado TLS y detrás de los servidores proxy.</span><span class="sxs-lookup"><span data-stu-id="85274-1228">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="85274-1229">Si `UseConnectionLogging` se coloca antes de `UseHttps`, se registra el tráfico cifrado.</span><span class="sxs-lookup"><span data-stu-id="85274-1229">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="85274-1230">Si `UseConnectionLogging` se coloca después de `UseHttps`, se registra el tráfico descifrado.</span><span class="sxs-lookup"><span data-stu-id="85274-1230">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="85274-1231">Enlazar a un socket TCP</span><span class="sxs-lookup"><span data-stu-id="85274-1231">Bind to a TCP socket</span></span>

<span data-ttu-id="85274-1232">El método <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> se enlaza a un socket TCP y una expresión lambda de opciones permite configurar un certificado X.509:</span><span class="sxs-lookup"><span data-stu-id="85274-1232">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Listen(IPAddress.Loopback, 5000);
            serverOptions.Listen(IPAddress.Loopback, 5001, listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testPassword");
            });
        });
```

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Listen(IPAddress.Loopback, 5000);
            serverOptions.Listen(IPAddress.Loopback, 5001, listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testPassword");
            });
        });
```

<span data-ttu-id="85274-1233">En el ejemplo se configura HTTPS para un punto de conexión con <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span><span class="sxs-lookup"><span data-stu-id="85274-1233">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="85274-1234">Use la misma API para configurar otras opciones de Kestrel para puntos de conexión específicos.</span><span class="sxs-lookup"><span data-stu-id="85274-1234">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="85274-1235">Enlazar a un socket de Unix</span><span class="sxs-lookup"><span data-stu-id="85274-1235">Bind to a Unix socket</span></span>

<span data-ttu-id="85274-1236">Escuche en un socket de Unix con <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> para mejorar el rendimiento con Nginx, tal como se muestra en este ejemplo:</span><span class="sxs-lookup"><span data-stu-id="85274-1236">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.ListenUnixSocket("/tmp/kestrel-test.sock");
            serverOptions.ListenUnixSocket("/tmp/kestrel-test.sock", listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testpassword");
            });
        });
```

* <span data-ttu-id="85274-1237">En el archivo de configuración de Nginx, establezca la entrada `server` > `location` > `proxy_pass` en `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span><span class="sxs-lookup"><span data-stu-id="85274-1237">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="85274-1238">`{KESTREL SOCKET}` es el nombre del socket proporcionado para <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (por ejemplo, `kestrel-test.sock` en el ejemplo anterior).</span><span class="sxs-lookup"><span data-stu-id="85274-1238">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="85274-1239">Asegúrese de que el socket es grabable por Nginx (por ejemplo, `chmod go+w /tmp/kestrel-test.sock`).</span><span class="sxs-lookup"><span data-stu-id="85274-1239">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="85274-1240">Puerto 0</span><span class="sxs-lookup"><span data-stu-id="85274-1240">Port 0</span></span>

<span data-ttu-id="85274-1241">Cuando se especifica el número de puerto `0`, Kestrel se enlaza de forma dinámica a un puerto disponible.</span><span class="sxs-lookup"><span data-stu-id="85274-1241">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="85274-1242">En el siguiente ejemplo se muestra cómo averiguar qué puerto Kestrel está realmente enlazado a un runtime:</span><span class="sxs-lookup"><span data-stu-id="85274-1242">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="85274-1243">Cuando la aplicación se ejecuta, la salida de la ventana de consola indica el puerto dinámico en el que se puede tener acceso a la aplicación:</span><span class="sxs-lookup"><span data-stu-id="85274-1243">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="85274-1244">Limitaciones</span><span class="sxs-lookup"><span data-stu-id="85274-1244">Limitations</span></span>

<span data-ttu-id="85274-1245">Configure puntos de conexión con los siguientes métodos:</span><span class="sxs-lookup"><span data-stu-id="85274-1245">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="85274-1246">El argumento de la línea de comandos `--urls`</span><span class="sxs-lookup"><span data-stu-id="85274-1246">`--urls` command-line argument</span></span>
* <span data-ttu-id="85274-1247">La clave de configuración de host `urls`</span><span class="sxs-lookup"><span data-stu-id="85274-1247">`urls` host configuration key</span></span>
* <span data-ttu-id="85274-1248">La variable de entorno `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="85274-1248">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="85274-1249">Estos métodos son útiles para que el código funcione con servidores que no sean de Kestrel.</span><span class="sxs-lookup"><span data-stu-id="85274-1249">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="85274-1250">Sin embargo, tenga en cuenta las siguientes limitaciones:</span><span class="sxs-lookup"><span data-stu-id="85274-1250">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="85274-1251">HTTPS no se puede usar con estos métodos, a menos que se proporcione un certificado predeterminado en la configuración del punto de conexión HTTPS (por ejemplo, por medio de la configuración `KestrelServerOptions` o de un archivo de configuración, tal y como se explicó anteriormente en este tema).</span><span class="sxs-lookup"><span data-stu-id="85274-1251">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="85274-1252">Cuando los métodos `Listen` y `UseUrls` se usan al mismo tiempo, los puntos de conexión de `Listen` sustituyen a los de `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="85274-1252">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="85274-1253">Configuración de puntos de conexión IIS</span><span class="sxs-lookup"><span data-stu-id="85274-1253">IIS endpoint configuration</span></span>

<span data-ttu-id="85274-1254">Cuando se usa IIS, los enlaces de direcciones URL de IIS reemplazan a los enlaces que se hayan establecido por medio de `Listen` o de `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="85274-1254">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="85274-1255">Para más información, vea el tema [Módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="85274-1255">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="85274-1256">Configuración de transporte</span><span class="sxs-lookup"><span data-stu-id="85274-1256">Transport configuration</span></span>

<span data-ttu-id="85274-1257">Desde el lanzamiento de ASP.NET Core 2.1, el transporte predeterminado de Kestrel deja de basarse en Libuv y pasa a basarse en sockets administrados.</span><span class="sxs-lookup"><span data-stu-id="85274-1257">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="85274-1258">Se trata de un cambio muy importante para las aplicaciones ASP.NET Core 2.0 que actualizan a 2.1 y llaman a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>, y que dependen de cualquiera de los siguientes paquetes:</span><span class="sxs-lookup"><span data-stu-id="85274-1258">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="85274-1259">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (referencia de paquete directa)</span><span class="sxs-lookup"><span data-stu-id="85274-1259">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="85274-1260">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="85274-1260">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="85274-1261">Para los proyectos que necesitan el uso de Libuv:</span><span class="sxs-lookup"><span data-stu-id="85274-1261">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="85274-1262">Agregar una dependencia del paquete [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) al archivo de proyecto de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="85274-1262">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="85274-1263">Llame a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span><span class="sxs-lookup"><span data-stu-id="85274-1263">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

  ```csharp
  public class Program
  {
      public static void Main(string[] args)
      {
          CreateWebHostBuilder(args).Build().Run();
      }

      public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
          WebHost.CreateDefaultBuilder(args)
              .UseLibuv()
              .UseStartup<Startup>();
  }
  ```

### <a name="url-prefixes"></a><span data-ttu-id="85274-1264">Prefijos de URL</span><span class="sxs-lookup"><span data-stu-id="85274-1264">URL prefixes</span></span>

<span data-ttu-id="85274-1265">Al usar `UseUrls`, el argumento de línea de comandos `--urls`, la clave de configuración de host `urls` o una variable de entorno `ASPNETCORE_URLS`, los prefijos de dirección URL pueden tener cualquiera de estos formatos.</span><span class="sxs-lookup"><span data-stu-id="85274-1265">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="85274-1266">Solo son válidos los prefijos de dirección URL HTTP.</span><span class="sxs-lookup"><span data-stu-id="85274-1266">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="85274-1267">Kestrel no admite HTTPS al configurar enlaces de dirección URL con `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="85274-1267">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="85274-1268">Dirección IPv4 con número de puerto</span><span class="sxs-lookup"><span data-stu-id="85274-1268">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="85274-1269">`0.0.0.0` es un caso especial que enlaza a todas las direcciones IPv4.</span><span class="sxs-lookup"><span data-stu-id="85274-1269">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="85274-1270">Dirección IPv6 con número de puerto</span><span class="sxs-lookup"><span data-stu-id="85274-1270">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="85274-1271">`[::]` es el equivalente en IPv6 de `0.0.0.0` en IPv4.</span><span class="sxs-lookup"><span data-stu-id="85274-1271">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="85274-1272">Nombre de host con número de puerto</span><span class="sxs-lookup"><span data-stu-id="85274-1272">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="85274-1273">Los nombres de host, `*` y `+` no son especiales.</span><span class="sxs-lookup"><span data-stu-id="85274-1273">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="85274-1274">Todo lo que no se identifique como una dirección IP o un `localhost` válido se enlaza a todas las direcciones IP de IPv6 e IPv4.</span><span class="sxs-lookup"><span data-stu-id="85274-1274">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="85274-1275">Para enlazar distintos nombres de host a distintas aplicaciones ASP.NET Core en el mismo puerto, use [HTTP.sys](xref:fundamentals/servers/httpsys) o un servidor proxy inverso, como IIS, Nginx o Apache.</span><span class="sxs-lookup"><span data-stu-id="85274-1275">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="85274-1276">El hospedaje en una configuración de proxy inverso requiere [filtrado de hosts](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="85274-1276">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="85274-1277">Nombre `localhost` del host con el número de puerto o la IP de bucle invertido con el número de puerto</span><span class="sxs-lookup"><span data-stu-id="85274-1277">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="85274-1278">Cuando se especifica `localhost`, Kestrel intenta enlazar a las interfaces de bucle invertido de IPv4 e IPv6.</span><span class="sxs-lookup"><span data-stu-id="85274-1278">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="85274-1279">Si el puerto solicitado lo está usando otro servicio en cualquier interfaz de bucle invertido, Kestrel no se puede iniciar.</span><span class="sxs-lookup"><span data-stu-id="85274-1279">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="85274-1280">Si ninguna de estas interfaces de bucle invertido está disponible por cualquier otra razón (normalmente porque no se admite IPv6), Kestrel registra una advertencia.</span><span class="sxs-lookup"><span data-stu-id="85274-1280">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="85274-1281">Filtrado de hosts</span><span class="sxs-lookup"><span data-stu-id="85274-1281">Host filtering</span></span>

<span data-ttu-id="85274-1282">Si bien Kestrel admite una configuración basada en prefijos como `http://example.com:5000`, pasa por alto completamente el nombre de host.</span><span class="sxs-lookup"><span data-stu-id="85274-1282">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="85274-1283">El host `localhost` es un caso especial que se usa para enlazar a direcciones de bucle invertido.</span><span class="sxs-lookup"><span data-stu-id="85274-1283">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="85274-1284">Cualquier otro host que no sea una dirección IP explícita se enlaza a todas las direcciones IP públicas.</span><span class="sxs-lookup"><span data-stu-id="85274-1284">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="85274-1285">Los encabezados `Host` no están validados.</span><span class="sxs-lookup"><span data-stu-id="85274-1285">`Host` headers aren't validated.</span></span>

<span data-ttu-id="85274-1286">Como solución alternativa, use el Middleware de filtrado de hosts.</span><span class="sxs-lookup"><span data-stu-id="85274-1286">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="85274-1287">El middleware de filtrado de host se facilita a través del paquete [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering), que se incluye en el [metapaquete Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 o 2.2).</span><span class="sxs-lookup"><span data-stu-id="85274-1287">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="85274-1288">El middleware se agrega por medio de <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, que llama a <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span><span class="sxs-lookup"><span data-stu-id="85274-1288">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="85274-1289">El Middleware de filtrado de hosts está deshabilitado de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="85274-1289">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="85274-1290">Para habilitarlo, defina una clave `AllowedHosts` en *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span><span class="sxs-lookup"><span data-stu-id="85274-1290">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="85274-1291">El valor es una lista delimitada por punto y coma de nombres de host sin los números de puerto:</span><span class="sxs-lookup"><span data-stu-id="85274-1291">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="85274-1292">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="85274-1292">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="85274-1293">[Middleware de encabezados reenviados](xref:host-and-deploy/proxy-load-balancer) también tiene una opción <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts>.</span><span class="sxs-lookup"><span data-stu-id="85274-1293">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="85274-1294">El Middleware de encabezados reenviados y el Middleware de filtrado de hosts tienen una funcionalidad similar en diferentes escenarios.</span><span class="sxs-lookup"><span data-stu-id="85274-1294">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="85274-1295">Establecer `AllowedHosts` con el Middleware de encabezados reenviados es adecuado cuando el encabezado `Host` no se conserva mientras se reenvían solicitudes con un servidor proxy inverso o un equilibrador de carga.</span><span class="sxs-lookup"><span data-stu-id="85274-1295">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="85274-1296">Establecer `AllowedHosts` con el Middleware de filtrado de hosts es adecuado cuando se usa Kestrel como un servidor perimetral de acceso público o cuando el encabezado `Host` se reenvía directamente.</span><span class="sxs-lookup"><span data-stu-id="85274-1296">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="85274-1297">Para obtener más información sobre el Middleware de encabezados reenviados, consulte <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="85274-1297">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

## <a name="http11-request-draining"></a><span data-ttu-id="85274-1298">Vaciado de solicitudes HTTP/1.1</span><span class="sxs-lookup"><span data-stu-id="85274-1298">HTTP/1.1 request draining</span></span>

<span data-ttu-id="85274-1299">La apertura de conexiones HTTP lleva mucho tiempo.</span><span class="sxs-lookup"><span data-stu-id="85274-1299">Opening HTTP connections is time consuming.</span></span> <span data-ttu-id="85274-1300">En HTTPS, además consume muchos recursos.</span><span class="sxs-lookup"><span data-stu-id="85274-1300">For HTTPS, it's also resource intensive.</span></span> <span data-ttu-id="85274-1301">Por lo tanto, Kestrel intenta reutilizar las conexiones conforme al protocolo HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="85274-1301">Therefore, Kestrel tries to reuse connections per the HTTP/1.1 protocol.</span></span> <span data-ttu-id="85274-1302">El cuerpo de una solicitud debe usarse por completo para que se vuelva a usar la conexión.</span><span class="sxs-lookup"><span data-stu-id="85274-1302">A request body must be fully consumed to allow the connection to be reused.</span></span> <span data-ttu-id="85274-1303">La aplicación no siempre consume el cuerpo de la solicitud, por ejemplo, `POST` solicita dónde devuelve un redireccionamiento o una respuesta 404 el servidor.</span><span class="sxs-lookup"><span data-stu-id="85274-1303">The app doesn't always consume the request body, such as a `POST` requests where the server returns a redirect or 404 response.</span></span> <span data-ttu-id="85274-1304">En el caso de un redireccionamiento `POST`:</span><span class="sxs-lookup"><span data-stu-id="85274-1304">In the `POST`-redirect case:</span></span>

* <span data-ttu-id="85274-1305">Es posible que el cliente ya haya enviado parte de los datos de `POST`.</span><span class="sxs-lookup"><span data-stu-id="85274-1305">The client may already have sent part of the `POST` data.</span></span>
* <span data-ttu-id="85274-1306">El servidor escribe la respuesta 301.</span><span class="sxs-lookup"><span data-stu-id="85274-1306">The server writes the 301 response.</span></span>
* <span data-ttu-id="85274-1307">La conexión no se puede usar para una nueva solicitud hasta que los datos de `POST` del cuerpo de la solicitud anterior se han leído en su totalidad.</span><span class="sxs-lookup"><span data-stu-id="85274-1307">The connection can't be used for a new request until the `POST` data from the previous request body has been fully read.</span></span>
* <span data-ttu-id="85274-1308">Kestrel intenta vaciar el cuerpo de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="85274-1308">Kestrel tries to drain the request body.</span></span> <span data-ttu-id="85274-1309">El vaciado del cuerpo de la solicitud significa leer y descartar los datos sin procesarlos.</span><span class="sxs-lookup"><span data-stu-id="85274-1309">Draining the request body means reading and discarding the data without processing it.</span></span>

<span data-ttu-id="85274-1310">El proceso de vaciado llega a un equilibrio entre permitir que se vuelva a usar la conexión y el tiempo que se tarda en vaciar los datos restantes:</span><span class="sxs-lookup"><span data-stu-id="85274-1310">The draining process makes a tradoff between allowing the connection to be reused and the time it takes to drain any remaining data:</span></span>

* <span data-ttu-id="85274-1311">El vaciado tiene un tiempo de espera de cinco segundos que no se puede configurar.</span><span class="sxs-lookup"><span data-stu-id="85274-1311">Draining has a timeout of five seconds, which isn't configurable.</span></span>
* <span data-ttu-id="85274-1312">Si no se han leído todos los datos especificados por el encabezado `Content-Length` o `Transfer-Encoding` antes de que se agote el tiempo de espera, se cierra la conexión.</span><span class="sxs-lookup"><span data-stu-id="85274-1312">If all of the data specified by the `Content-Length` or `Transfer-Encoding` header hasn't been read before the timeout, the connection is closed.</span></span>

<span data-ttu-id="85274-1313">A veces, puede que quiera finalizar la solicitud inmediatamente, antes o después de escribir la respuesta.</span><span class="sxs-lookup"><span data-stu-id="85274-1313">Sometimes you may want to terminate the request immediately, before or after writing the response.</span></span> <span data-ttu-id="85274-1314">Por ejemplo, los clientes pueden tener límites de datos restrictivos, así que limitar los datos cargados podría ser una prioridad.</span><span class="sxs-lookup"><span data-stu-id="85274-1314">For example, clients may have restrictive data caps, so limiting uploaded data might be a priority.</span></span> <span data-ttu-id="85274-1315">En tales casos, para finalizar una solicitud, llame a [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) desde un controlador, una página de Razor o un middleware.</span><span class="sxs-lookup"><span data-stu-id="85274-1315">In such cases to terminate a request, call [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) from a controller, Razor Page, or middleware.</span></span>

<span data-ttu-id="85274-1316">Hay advertencias respecto a la llamada a `Abort`:</span><span class="sxs-lookup"><span data-stu-id="85274-1316">There are caveats to calling `Abort`:</span></span>

* <span data-ttu-id="85274-1317">La creación de nuevas conexiones puede ser lenta y costosa.</span><span class="sxs-lookup"><span data-stu-id="85274-1317">Creating new connections can be slow and expensive.</span></span>
* <span data-ttu-id="85274-1318">No hay ninguna garantía de que el cliente haya leído la respuesta antes de que se cierre la conexión.</span><span class="sxs-lookup"><span data-stu-id="85274-1318">There's no guarantee that the client has read the response before the connection closes.</span></span>
* <span data-ttu-id="85274-1319">La llamada a `Abort` debe ser poco frecuente y reservada para casos de error graves, no comunes.</span><span class="sxs-lookup"><span data-stu-id="85274-1319">Calling `Abort` should be rare and reserved for severe error cases, not common errors.</span></span>
  * <span data-ttu-id="85274-1320">Llame a `Abort` solo cuando sea necesario resolver un problema concreto.</span><span class="sxs-lookup"><span data-stu-id="85274-1320">Only call `Abort` when a specific problem needs to be solved.</span></span> <span data-ttu-id="85274-1321">Por ejemplo, llame a `Abort` si hay clientes malintencionados intentando aplicar `POST` a los datos o si hay un error en el código del cliente que produce muchas solicitudes o de gran tamaño.</span><span class="sxs-lookup"><span data-stu-id="85274-1321">For example, call `Abort` if malicious clients are trying to `POST` data or when there's a bug in client code that causes large or numerous requests.</span></span>
  * <span data-ttu-id="85274-1322">No llame a `Abort` para situaciones de error comunes, como HTTP 404 (No encontrado).</span><span class="sxs-lookup"><span data-stu-id="85274-1322">Don't call `Abort` for common error situations, such as HTTP 404 (Not Found).</span></span>

<span data-ttu-id="85274-1323">La llamada a [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) antes de llamar a `Abort` garantiza que el servidor haya terminado de escribir la respuesta.</span><span class="sxs-lookup"><span data-stu-id="85274-1323">Calling [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) before calling `Abort` ensures that the server has completed writing the response.</span></span> <span data-ttu-id="85274-1324">Pero el comportamiento del cliente no es predecible y es posible que no lea la respuesta antes de que se anule la conexión.</span><span class="sxs-lookup"><span data-stu-id="85274-1324">However, client behavior isn't predictable and they may not read the response before the connection is aborted.</span></span>

<span data-ttu-id="85274-1325">Este proceso es diferente para HTTP/2, ya que el protocolo admite la anulación de flujos de solicitud individuales sin cerrar la conexión.</span><span class="sxs-lookup"><span data-stu-id="85274-1325">This process is different for HTTP/2 because the protocol supports aborting individual request streams without closing the connection.</span></span> <span data-ttu-id="85274-1326">El tiempo de espera de vaciado de cinco segundos no se aplica.</span><span class="sxs-lookup"><span data-stu-id="85274-1326">The five second drain timeout doesn't apply.</span></span> <span data-ttu-id="85274-1327">Si hay datos del cuerpo de la solicitud sin leer después de completar una respuesta, el servidor envía un marco RST HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="85274-1327">If there's any unread request body data after completing a response, then the server sends an HTTP/2 RST frame.</span></span> <span data-ttu-id="85274-1328">Se omiten los marcos de datos del cuerpo de la solicitud adicionales.</span><span class="sxs-lookup"><span data-stu-id="85274-1328">Additional request body data frames are ignored.</span></span>

<span data-ttu-id="85274-1329">Si es posible, es mejor que los clientes usen el encabezado de solicitud [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) y esperen a que el servidor responda para empezar a enviar el cuerpo de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="85274-1329">If possible, it's better for clients to utilize the [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) request header and wait for the server to respond before starting to send the request body.</span></span> <span data-ttu-id="85274-1330">Esto proporciona al cliente la oportunidad de examinar la respuesta y anular antes de enviar datos innecesarios.</span><span class="sxs-lookup"><span data-stu-id="85274-1330">That gives the client an opportunity to examine the response and abort before sending unneeded data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="85274-1331">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="85274-1331">Additional resources</span></span>

* <span data-ttu-id="85274-1332">Al usar sockets de UNIX en Linux, el socket no se elimina de forma automática al apagar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="85274-1332">When using UNIX sockets on Linux, the socket is not automatically deleted on app shut down.</span></span> <span data-ttu-id="85274-1333">Para más información, consulte [este problema de GitHub](https://github.com/dotnet/aspnetcore/issues/14134).</span><span class="sxs-lookup"><span data-stu-id="85274-1333">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/14134).</span></span>
* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* <span data-ttu-id="85274-1334">[RFC 7230: Message Syntax and Routing (Section 5.4: Host)](https://tools.ietf.org/html/rfc7230#section-5.4) (RFC 7230: Enrutamiento y sintaxis de mensajes [Sección 5.4: Host])</span><span class="sxs-lookup"><span data-stu-id="85274-1334">[RFC 7230: Message Syntax and Routing (Section 5.4: Host)](https://tools.ietf.org/html/rfc7230#section-5.4)</span></span>
