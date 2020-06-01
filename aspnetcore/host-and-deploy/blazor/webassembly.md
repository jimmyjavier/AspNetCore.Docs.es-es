---
<span data-ttu-id="8c506-101">title: "Hospedaje e implementación de ASP.NET Core Blazor WebAssembly" author: description: "Aprenda a hospedar e implementar una aplicación Blazor con ASP.NET Core, redes de entrega de contenido (CDN), servidores de archivos y páginas de GitHub".</span><span class="sxs-lookup"><span data-stu-id="8c506-101">title: 'Host and deploy ASP.NET Core Blazor WebAssembly' author: description: 'Learn how to host and deploy a Blazor app using ASP.NET Core, Content Delivery Networks (CDN), file servers, and GitHub Pages.'</span></span>
<span data-ttu-id="8c506-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c506-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c506-103">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c506-103">'Blazor'</span></span>
- <span data-ttu-id="8c506-104">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c506-104">'Identity'</span></span>
- <span data-ttu-id="8c506-105">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c506-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c506-106">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c506-106">'Razor'</span></span>
- <span data-ttu-id="8c506-107">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c506-107">'SignalR' uid:</span></span> 

---
# <a name="host-and-deploy-aspnet-core-blazor-webassembly"></a><span data-ttu-id="8c506-108">Hospedaje e implementación de ASP.NET CoreBlazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="8c506-108">Host and deploy ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="8c506-109">Por [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams) y [Safia Abdalla](https://safia.rocks)</span><span class="sxs-lookup"><span data-stu-id="8c506-109">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams), and [Safia Abdalla](https://safia.rocks)</span></span>

<span data-ttu-id="8c506-110">Con el modelo de hospedaje de [Blazor WebAssembly ](xref:blazor/hosting-models#blazor-webassembly):</span><span class="sxs-lookup"><span data-stu-id="8c506-110">With the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly):</span></span>

* <span data-ttu-id="8c506-111">La aplicación Blazor, sus dependencias y el entorno de ejecución de .NET se descargan en el explorador en paralelo.</span><span class="sxs-lookup"><span data-stu-id="8c506-111">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser in parallel.</span></span>
* <span data-ttu-id="8c506-112">La aplicación se ejecuta directamente en el subproceso de interfaz de usuario del explorador.</span><span class="sxs-lookup"><span data-stu-id="8c506-112">The app is executed directly on the browser UI thread.</span></span>

<span data-ttu-id="8c506-113">Se admiten las estrategias de implementación siguientes:</span><span class="sxs-lookup"><span data-stu-id="8c506-113">The following deployment strategies are supported:</span></span>

* <span data-ttu-id="8c506-114">Una aplicación ASP.NET Core suministra la aplicación Blazor.</span><span class="sxs-lookup"><span data-stu-id="8c506-114">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="8c506-115">Esta estrategia se trata en la sección [Implementación hospedada con ASP.NET Core](#hosted-deployment-with-aspnet-core).</span><span class="sxs-lookup"><span data-stu-id="8c506-115">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
* <span data-ttu-id="8c506-116">La aplicación Blazor se coloca en un servicio o servidor web de hospedaje estático, donde no se usa .NET para suministrar la aplicación Blazor.</span><span class="sxs-lookup"><span data-stu-id="8c506-116">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="8c506-117">Esta estrategia se trata en la sección [Implementación independiente](#standalone-deployment), que incluye información sobre cómo hospedar una aplicación Blazor WebAssembly como una subaplicación de IIS.</span><span class="sxs-lookup"><span data-stu-id="8c506-117">This strategy is covered in the [Standalone deployment](#standalone-deployment) section, which includes information on hosting a Blazor WebAssembly app as an IIS sub-app.</span></span>

## <a name="brotli-precompression"></a><span data-ttu-id="8c506-118">Precompresión de Brotli</span><span class="sxs-lookup"><span data-stu-id="8c506-118">Brotli precompression</span></span>

<span data-ttu-id="8c506-119">Cuando se publica una aplicación WebAssembly de Blazor, la salida se comprime mediante el [algoritmo de compresión de Brotli](https://tools.ietf.org/html/rfc7932) en el nivel más alto para reducir el tamaño de la aplicación y quitar la necesidad de compresión en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="8c506-119">When a Blazor WebAssembly app is published, the output is precompressed using the [Brotli compression algorithm](https://tools.ietf.org/html/rfc7932) at the highest level to reduce the app size and remove the need for runtime compression.</span></span>

<span data-ttu-id="8c506-120">Para ver la configuración de compresión de *web.config* de IIS, vea la sección [IIS: compresión Brotli y Gzip](#brotli-and-gzip-compression).</span><span class="sxs-lookup"><span data-stu-id="8c506-120">For IIS *web.config* compression configuration, see the [IIS: Brotli and Gzip compression](#brotli-and-gzip-compression) section.</span></span>

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="8c506-121">Reescritura de las URL para conseguir un enrutamiento correcto</span><span class="sxs-lookup"><span data-stu-id="8c506-121">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="8c506-122">Enrutar las solicitudes de los componentes de página de una aplicación Blazor WebAssembly no es tan sencillo como enrutar las solicitudes de una aplicación Blazor Server hospedada.</span><span class="sxs-lookup"><span data-stu-id="8c506-122">Routing requests for page components in a Blazor WebAssembly app isn't as straightforward as routing requests in a Blazor Server, hosted app.</span></span> <span data-ttu-id="8c506-123">Se recomienda usar una aplicación Blazor WebAssembly con dos componentes:</span><span class="sxs-lookup"><span data-stu-id="8c506-123">Consider a Blazor WebAssembly app with two components:</span></span>

* <span data-ttu-id="8c506-124">*Main.razor*: se carga en la raíz de la aplicación y contiene un vínculo al componente `About` (`href="About"`).</span><span class="sxs-lookup"><span data-stu-id="8c506-124">*Main.razor*: Loads at the root of the app and contains a link to the `About` component (`href="About"`).</span></span>
* <span data-ttu-id="8c506-125">*About.razor*: el componente `About`.</span><span class="sxs-lookup"><span data-stu-id="8c506-125">*About.razor*: `About` component.</span></span>

<span data-ttu-id="8c506-126">Cuando se solicita el documento predeterminado de la aplicación mediante la barra de direcciones del explorador (por ejemplo, `https://www.contoso.com/`):</span><span class="sxs-lookup"><span data-stu-id="8c506-126">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="8c506-127">El explorador realiza una solicitud.</span><span class="sxs-lookup"><span data-stu-id="8c506-127">The browser makes a request.</span></span>
1. <span data-ttu-id="8c506-128">Se devuelve la página predeterminada, que suele ser *index.html*.</span><span class="sxs-lookup"><span data-stu-id="8c506-128">The default page is returned, which is usually *index.html*.</span></span>
1. <span data-ttu-id="8c506-129">*index.html* arranca la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8c506-129">*index.html* bootstraps the app.</span></span>
1. <span data-ttu-id="8c506-130">Se carga el enrutador de Blazor y se representa el componente `Main` de Razor.</span><span class="sxs-lookup"><span data-stu-id="8c506-130">Blazor's router loads, and the Razor `Main` component is rendered.</span></span>

<span data-ttu-id="8c506-131">En la página principal, la selección del vínculo al componente `About` funciona en el cliente porque el enrutador de Blazor impide que el explorador haga una solicitud en Internet a `www.contoso.com` sobre `About` y presenta el propio componente `About` representado.</span><span class="sxs-lookup"><span data-stu-id="8c506-131">In the Main page, selecting the link to the `About` component works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the rendered `About` component itself.</span></span> <span data-ttu-id="8c506-132">Todas las solicitudes de puntos de conexión internos *dentro de la aplicación Blazor WebAssembly* funcionan del mismo modo: Las solicitudes no desencadenan solicitudes basadas en el explorador a recursos hospedados en el servidor en Internet.</span><span class="sxs-lookup"><span data-stu-id="8c506-132">All of the requests for internal endpoints *within the Blazor WebAssembly app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="8c506-133">El enrutador controla las solicitudes de forma interna.</span><span class="sxs-lookup"><span data-stu-id="8c506-133">The router handles the requests internally.</span></span>

<span data-ttu-id="8c506-134">Si se realiza una solicitud mediante la barra de direcciones del explorador para `www.contoso.com/About`, se produce un error.</span><span class="sxs-lookup"><span data-stu-id="8c506-134">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="8c506-135">Este recurso no existe en el host de Internet de la aplicación, por lo que se devuelve una respuesta *404 No encontrado*.</span><span class="sxs-lookup"><span data-stu-id="8c506-135">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="8c506-136">Dado que los exploradores realizan solicitudes a hosts basados en Internet para las páginas del lado cliente, los servidores web y los servicios de hospedaje deben reescribir todas las solicitudes de recursos que no estén físicamente en el servidor a la página *index.html*.</span><span class="sxs-lookup"><span data-stu-id="8c506-136">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the *index.html* page.</span></span> <span data-ttu-id="8c506-137">Cuando se devuelve *index.html*, el enrutador Blazor de la aplicación se hace cargo y responde con el recurso correcto.</span><span class="sxs-lookup"><span data-stu-id="8c506-137">When *index.html* is returned, the app's Blazor router takes over and responds with the correct resource.</span></span>

<span data-ttu-id="8c506-138">Al implementar en un servidor IIS, puede usar el módulo URL Rewrite con el archivo *web.config* publicado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8c506-138">When deploying to an IIS server, you can use the URL Rewrite Module with the app's published *web.config* file.</span></span> <span data-ttu-id="8c506-139">Para más información, consulte la sección sobre [IIS](#iis).</span><span class="sxs-lookup"><span data-stu-id="8c506-139">For more information, see the [IIS](#iis) section.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="8c506-140">Implementación hospedada con ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8c506-140">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="8c506-141">Una *implementación hospedada* se encarga de suministrar la aplicación Blazor WebAssembly a los exploradores desde una [aplicación ASP.NET Core](xref:index) que se ejecuta en un servidor web.</span><span class="sxs-lookup"><span data-stu-id="8c506-141">A *hosted deployment* serves the Blazor WebAssembly app to browsers from an [ASP.NET Core app](xref:index) that runs on a web server.</span></span>

<span data-ttu-id="8c506-142">La aplicación WebAssembly del cliente Blazor se publica en la carpeta */bin/Release/{MARCO DE DESTINO}/publish/wwwroot* de la aplicación de servidor, junto con cualquier otro recurso web estático de la aplicación de servidor.</span><span class="sxs-lookup"><span data-stu-id="8c506-142">The client Blazor WebAssembly app is published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="8c506-143">Las dos aplicaciones se implementan juntas.</span><span class="sxs-lookup"><span data-stu-id="8c506-143">The two apps are deployed together.</span></span> <span data-ttu-id="8c506-144">Se requiere un servidor web que pueda hospedar una aplicación ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8c506-144">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="8c506-145">En el caso de una implementación hospedada, Visual Studio incluye la plantilla de proyecto **Blazor WebAssembly App** (la plantilla `blazorwasm` al usar el comando [dotnet new](/dotnet/core/tools/dotnet-new)) con la opción **Hosted** (Hospedada) seleccionada (`-ho|--hosted` al usar el comando `dotnet new`).</span><span class="sxs-lookup"><span data-stu-id="8c506-145">For a hosted deployment, Visual Studio includes the **Blazor WebAssembly App** project template (`blazorwasm` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command) with the **Hosted** option selected (`-ho|--hosted` when using the `dotnet new` command).</span></span>

<span data-ttu-id="8c506-146">Para obtener más información sobre la implementación y el hospedaje de aplicaciones de ASP.NET Core, consulte <xref:host-and-deploy/index>.</span><span class="sxs-lookup"><span data-stu-id="8c506-146">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="8c506-147">Para obtener información sobre cómo implementar en Azure App Service, vea <xref:tutorials/publish-to-azure-webapp-using-vs>.</span><span class="sxs-lookup"><span data-stu-id="8c506-147">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

## <a name="standalone-deployment"></a><span data-ttu-id="8c506-148">Implementación independiente</span><span class="sxs-lookup"><span data-stu-id="8c506-148">Standalone deployment</span></span>

<span data-ttu-id="8c506-149">Una *implementación independiente* suministra la aplicación Blazor WebAssembly como un conjunto de archivos estáticos que los clientes solicitan directamente.</span><span class="sxs-lookup"><span data-stu-id="8c506-149">A *standalone deployment* serves the Blazor WebAssembly app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="8c506-150">Cualquier servidor de archivos estático es capaz de suministrar la aplicación Blazor.</span><span class="sxs-lookup"><span data-stu-id="8c506-150">Any static file server is able to serve the Blazor app.</span></span>

<span data-ttu-id="8c506-151">Los activos de implementación independientes se publican en la carpeta */bin/Release/{MARCO DE DESTINO}/publish/wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="8c506-151">Standalone deployment assets are published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="8c506-152">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="8c506-152">Azure App Service</span></span>

<span data-ttu-id="8c506-153">Las aplicaciones Blazor WebAssembly se pueden implementar en Azure App Service en Windows, que hospeda la aplicación en [IIS](#iis).</span><span class="sxs-lookup"><span data-stu-id="8c506-153">Blazor WebAssembly apps can be deployed to Azure App Services on Windows, which hosts the app on [IIS](#iis).</span></span>

<span data-ttu-id="8c506-154">Actualmente no se admite la implementación de una aplicación Blazor WebAssembly independiente en Azure App Service para Linux.</span><span class="sxs-lookup"><span data-stu-id="8c506-154">Deploying a standalone Blazor WebAssembly app to Azure App Service for Linux isn't currently supported.</span></span> <span data-ttu-id="8c506-155">En este momento no hay disponible una imagen de servidor de Linux para hospedar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8c506-155">A Linux server image to host the app isn't available at this time.</span></span> <span data-ttu-id="8c506-156">Se está trabajando para habilitar este escenario.</span><span class="sxs-lookup"><span data-stu-id="8c506-156">Work is in progress to enable this scenario.</span></span>

### <a name="iis"></a><span data-ttu-id="8c506-157">IIS</span><span class="sxs-lookup"><span data-stu-id="8c506-157">IIS</span></span>

<span data-ttu-id="8c506-158">IIS es un servidor de archivos estáticos compatible con las aplicaciones Blazor.</span><span class="sxs-lookup"><span data-stu-id="8c506-158">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="8c506-159">Para configurar IIS para hospedar Blazor, consulte [Compilación de un sitio web estático en IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span><span class="sxs-lookup"><span data-stu-id="8c506-159">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="8c506-160">Los recursos publicados se crean en la carpeta */bin/Release/{TARGET FRAMEWORK}/publish*.</span><span class="sxs-lookup"><span data-stu-id="8c506-160">Published assets are created in the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span> <span data-ttu-id="8c506-161">Hospede el contenido de la carpeta *publish* en el servidor web o el servicio de hospedaje.</span><span class="sxs-lookup"><span data-stu-id="8c506-161">Host the contents of the *publish* folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="8c506-162">web.config</span><span class="sxs-lookup"><span data-stu-id="8c506-162">web.config</span></span>

<span data-ttu-id="8c506-163">Cuando se publica un proyecto de Blazor, se crea un archivo *web.config* con la siguiente configuración de IIS:</span><span class="sxs-lookup"><span data-stu-id="8c506-163">When a Blazor project is published, a *web.config* file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="8c506-164">Se establecen los tipos MIME de las siguientes extensiones de archivo:</span><span class="sxs-lookup"><span data-stu-id="8c506-164">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="8c506-165">*.dll*: `application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="8c506-165">*.dll*: `application/octet-stream`</span></span>
  * <span data-ttu-id="8c506-166">*.json*: `application/json`</span><span class="sxs-lookup"><span data-stu-id="8c506-166">*.json*: `application/json`</span></span>
  * <span data-ttu-id="8c506-167">*.wasm*: `application/wasm`</span><span class="sxs-lookup"><span data-stu-id="8c506-167">*.wasm*: `application/wasm`</span></span>
  * <span data-ttu-id="8c506-168">*.woff*: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="8c506-168">*.woff*: `application/font-woff`</span></span>
  * <span data-ttu-id="8c506-169">*.woff2*: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="8c506-169">*.woff2*: `application/font-woff`</span></span>
* <span data-ttu-id="8c506-170">Se habilita la compresión HTTP de los siguientes tipos MIME:</span><span class="sxs-lookup"><span data-stu-id="8c506-170">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="8c506-171">Se establecen reglas del módulo URL Rewrite:</span><span class="sxs-lookup"><span data-stu-id="8c506-171">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="8c506-172">Proporcionar el subdirectorio donde residen los recursos estáticos de la aplicación (*wwwroot/{RUTA SOLICITADA}* ).</span><span class="sxs-lookup"><span data-stu-id="8c506-172">Serve the sub-directory where the app's static assets reside (*wwwroot/{PATH REQUESTED}*).</span></span>
  * <span data-ttu-id="8c506-173">Crear el enrutamiento de reserva de SPA para que las solicitudes de recursos que no sean archivos se redirijan al documento predeterminado de la aplicación en su carpeta de recursos estáticos (*wwwroot/index.html*).</span><span class="sxs-lookup"><span data-stu-id="8c506-173">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (*wwwroot/index.html*).</span></span>
  
#### <a name="use-a-custom-webconfig"></a><span data-ttu-id="8c506-174">Uso de un archivo web.config personalizado</span><span class="sxs-lookup"><span data-stu-id="8c506-174">Use a custom web.config</span></span>

<span data-ttu-id="8c506-175">Para usar un archivo *web.config* personalizado, coloque el archivo *web.config* personalizado en la raíz de la carpeta del proyecto y publique el proyecto.</span><span class="sxs-lookup"><span data-stu-id="8c506-175">To use a custom *web.config* file, place the custom *web.config* file at the root of the project folder and publish the project.</span></span>

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="8c506-176">Instalación del módulo URL Rewrite</span><span class="sxs-lookup"><span data-stu-id="8c506-176">Install the URL Rewrite Module</span></span>

<span data-ttu-id="8c506-177">El [módulo URL Rewrite](https://www.iis.net/downloads/microsoft/url-rewrite) es necesario para reescribir las URL.</span><span class="sxs-lookup"><span data-stu-id="8c506-177">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="8c506-178">El módulo no se instala de forma predeterminada y no está disponible para instalar como una característica de servicio de rol del servidor web (IIS).</span><span class="sxs-lookup"><span data-stu-id="8c506-178">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="8c506-179">El módulo se debe descargar desde el sitio web de IIS.</span><span class="sxs-lookup"><span data-stu-id="8c506-179">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="8c506-180">Use el instalador de plataforma web para instalar el módulo:</span><span class="sxs-lookup"><span data-stu-id="8c506-180">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="8c506-181">De forma local, vaya a la [página de descargas del módulo URL Rewrite](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span><span class="sxs-lookup"><span data-stu-id="8c506-181">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="8c506-182">En el caso de la versión en inglés, seleccione **WebPI** para descargar el instalador de WebPI.</span><span class="sxs-lookup"><span data-stu-id="8c506-182">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="8c506-183">En el caso de otros idiomas, seleccione la arquitectura adecuada del servidor (x86/x64) para descargar el instalador.</span><span class="sxs-lookup"><span data-stu-id="8c506-183">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="8c506-184">Copie el instalador en el servidor.</span><span class="sxs-lookup"><span data-stu-id="8c506-184">Copy the installer to the server.</span></span> <span data-ttu-id="8c506-185">Ejecute el instalador.</span><span class="sxs-lookup"><span data-stu-id="8c506-185">Run the installer.</span></span> <span data-ttu-id="8c506-186">Haga clic en el botón **Instalar** y acepte los términos de licencia.</span><span class="sxs-lookup"><span data-stu-id="8c506-186">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="8c506-187">No es necesario reiniciar el servidor al finalizar la instalación.</span><span class="sxs-lookup"><span data-stu-id="8c506-187">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="8c506-188">Configuración del sitio web</span><span class="sxs-lookup"><span data-stu-id="8c506-188">Configure the website</span></span>

<span data-ttu-id="8c506-189">Configure la **ruta de acceso física** del sitio web a la carpeta de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8c506-189">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="8c506-190">La carpeta contiene:</span><span class="sxs-lookup"><span data-stu-id="8c506-190">The folder contains:</span></span>

* <span data-ttu-id="8c506-191">El archivo *web.config* que usa IIS para configurar el sitio web, incluidas las reglas de redireccionamiento y los tipos de contenido de archivos necesarios.</span><span class="sxs-lookup"><span data-stu-id="8c506-191">The *web.config* file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="8c506-192">La carpeta de recursos estáticos de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8c506-192">The app's static asset folder.</span></span>

#### <a name="host-as-an-iis-sub-app"></a><span data-ttu-id="8c506-193">Hospedaje como subaplicación de IIS</span><span class="sxs-lookup"><span data-stu-id="8c506-193">Host as an IIS sub-app</span></span>

<span data-ttu-id="8c506-194">Si una aplicación independiente se hospeda como una subaplicación de IIS, realice una de las siguientes acciones:</span><span class="sxs-lookup"><span data-stu-id="8c506-194">If a standalone app is hosted as an IIS sub-app, perform either of the following:</span></span>

* <span data-ttu-id="8c506-195">Deshabilite el controlador del módulo de ASP.NET Core heredado.</span><span class="sxs-lookup"><span data-stu-id="8c506-195">Disable the inherited ASP.NET Core Module handler.</span></span>

  <span data-ttu-id="8c506-196">Para quitar el controlador del archivo Blazorweb.config*publicado de la aplicación*, agregue una sección `<handlers>` al archivo:</span><span class="sxs-lookup"><span data-stu-id="8c506-196">Remove the handler in the Blazor app's published *web.config* file by adding a `<handlers>` section to the file:</span></span>

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* <span data-ttu-id="8c506-197">Deshabilite la herencia de la sección `<system.webServer>` de la aplicación raíz (principal) mediante un elemento `<location>` con `inheritInChildApplications` establecido en `false`:</span><span class="sxs-lookup"><span data-stu-id="8c506-197">Disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <handlers>
          <add name="aspNetCore" ... />
        </handlers>
        <aspNetCore ... />
      </system.webServer>
    </location>
  </configuration>
  ```

<span data-ttu-id="8c506-198">Además de [configurarse la ruta de acceso base de la aplicación](xref:host-and-deploy/blazor/index#app-base-path), se quita el controlador o se deshabilita la herencia.</span><span class="sxs-lookup"><span data-stu-id="8c506-198">Removing the handler or disabling inheritance is performed in addition to [configuring the app's base path](xref:host-and-deploy/blazor/index#app-base-path).</span></span> <span data-ttu-id="8c506-199">Establezca la ruta de acceso base de la aplicación en el archivo *index.html* de la aplicación en el alias de IIS que ha usado al configurar la subaplicación en IIS.</span><span class="sxs-lookup"><span data-stu-id="8c506-199">Set the app base path in the app's *index.html* file to the IIS alias used when configuring the sub-app in IIS.</span></span>

#### <a name="brotli-and-gzip-compression"></a><span data-ttu-id="8c506-200">Compresión Brotli y Gzip</span><span class="sxs-lookup"><span data-stu-id="8c506-200">Brotli and Gzip compression</span></span>

<span data-ttu-id="8c506-201">IIS se puede configurar a través de *web.config* para dar servicio a activos de Blazor comprimidos con Brotli o Gzip.</span><span class="sxs-lookup"><span data-stu-id="8c506-201">IIS can be configured via *web.config* to serve Brotli or Gzip compressed Blazor assets.</span></span> <span data-ttu-id="8c506-202">Para ver una configuración de ejemplo, vea [web.config](webassembly/_samples/web.config?raw=true).</span><span class="sxs-lookup"><span data-stu-id="8c506-202">For an example configuration, see [web.config](webassembly/_samples/web.config?raw=true).</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="8c506-203">Solución de problemas</span><span class="sxs-lookup"><span data-stu-id="8c506-203">Troubleshooting</span></span>

<span data-ttu-id="8c506-204">Si se recibe un error *500 Error interno del servidor* y el administrador de IIS produce errores al intentar acceder a la configuración del sitio web, confirme que el módulo URL Rewrite está instalado.</span><span class="sxs-lookup"><span data-stu-id="8c506-204">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="8c506-205">Si no lo está, IIS no puede analizar el archivo *web.config*.</span><span class="sxs-lookup"><span data-stu-id="8c506-205">When the module isn't installed, the *web.config* file can't be parsed by IIS.</span></span> <span data-ttu-id="8c506-206">Esto impide que el Administrador de IIS cargue la configuración del sitio web y que el sitio web suministre los archivos estáticos de Blazor.</span><span class="sxs-lookup"><span data-stu-id="8c506-206">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="8c506-207">Para obtener más información sobre cómo solucionar problemas de las implementaciones en IIS, vea <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="8c506-207">For more information on troubleshooting deployments to IIS, see <xref:test/troubleshoot-azure-iis>.</span></span>

### <a name="azure-storage"></a><span data-ttu-id="8c506-208">Almacenamiento de Azure</span><span class="sxs-lookup"><span data-stu-id="8c506-208">Azure Storage</span></span>

<span data-ttu-id="8c506-209">El hospedaje de archivos estáticos de [Azure Storage](/azure/storage/) permite el hospedaje de aplicaciones Blazor sin servidor.</span><span class="sxs-lookup"><span data-stu-id="8c506-209">[Azure Storage](/azure/storage/) static file hosting allows serverless Blazor app hosting.</span></span> <span data-ttu-id="8c506-210">Se admiten nombres de dominio personalizados, Azure Content Delivery Network (CDN) y HTTPS.</span><span class="sxs-lookup"><span data-stu-id="8c506-210">Custom domain names, the Azure Content Delivery Network (CDN), and HTTPS are supported.</span></span>

<span data-ttu-id="8c506-211">Cuando el servicio de blob está habilitado para el hospedaje de sitios web estáticos en una cuenta de almacenamiento:</span><span class="sxs-lookup"><span data-stu-id="8c506-211">When the blob service is enabled for static website hosting on a storage account:</span></span>

* <span data-ttu-id="8c506-212">Establece el **nombre de documento de índice** en `index.html`.</span><span class="sxs-lookup"><span data-stu-id="8c506-212">Set the **Index document name** to `index.html`.</span></span>
* <span data-ttu-id="8c506-213">Establece la **ruta de acceso del documento de error** en `index.html`.</span><span class="sxs-lookup"><span data-stu-id="8c506-213">Set the **Error document path** to `index.html`.</span></span> <span data-ttu-id="8c506-214">Los componentes Razor y otros puntos de conexión que no son de archivo no residen en las rutas de acceso físicas del contenido estático almacenado por el servicio de blob.</span><span class="sxs-lookup"><span data-stu-id="8c506-214">Razor components and other non-file endpoints don't reside at physical paths in the static content stored by the blob service.</span></span> <span data-ttu-id="8c506-215">Cuando se recibe una solicitud de uno de estos recursos que debe controlar el enrutador de Blazor, el error *404 - No encontrado* generado por el servicio de blob enruta la solicitud a la **ruta de acceso del documento de error**.</span><span class="sxs-lookup"><span data-stu-id="8c506-215">When a request for one of these resources is received that the Blazor router should handle, the *404 - Not Found* error generated by the blob service routes the request to the **Error document path**.</span></span> <span data-ttu-id="8c506-216">Se devuelve el blob *index.html* y el enrutador de Blazor carga y procesa la ruta de acceso.</span><span class="sxs-lookup"><span data-stu-id="8c506-216">The *index.html* blob is returned, and the Blazor router loads and processes the path.</span></span>

<span data-ttu-id="8c506-217">Para más información, consulte [Hospedaje de sitios web estáticos en Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span><span class="sxs-lookup"><span data-stu-id="8c506-217">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span>

### <a name="nginx"></a><span data-ttu-id="8c506-218">Nginx</span><span class="sxs-lookup"><span data-stu-id="8c506-218">Nginx</span></span>

<span data-ttu-id="8c506-219">El siguiente archivo *nginx.conf* se ha simplificado para mostrar cómo configurar Nginx para enviar el archivo *index.html* siempre que no pueda encontrar un archivo correspondiente en el disco.</span><span class="sxs-lookup"><span data-stu-id="8c506-219">The following *nginx.conf* file is simplified to show how to configure Nginx to send the *index.html* file whenever it can't find a corresponding file on disk.</span></span>

```
events { }
http {
    server {
        listen 80;

        location / {
            root /usr/share/nginx/html;
            try_files $uri $uri/ /index.html =404;
        }
    }
}
```

<span data-ttu-id="8c506-220">Para obtener más información sobre la configuración del servidor web de producción de Nginx, consulte [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/) (Creación de archivos de configuración de NGINX y NGINX Plus).</span><span class="sxs-lookup"><span data-stu-id="8c506-220">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="nginx-in-docker"></a><span data-ttu-id="8c506-221">Nginx en Docker</span><span class="sxs-lookup"><span data-stu-id="8c506-221">Nginx in Docker</span></span>

<span data-ttu-id="8c506-222">Para hospedar Blazor en Docker mediante Nginx, configure Dockerfile para usar la imagen de Nginx basada en Alpine.</span><span class="sxs-lookup"><span data-stu-id="8c506-222">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="8c506-223">Actualice el Dockerfile para copiar el archivo *nginx.config* en el contenedor.</span><span class="sxs-lookup"><span data-stu-id="8c506-223">Update the Dockerfile to copy the *nginx.config* file into the container.</span></span>

<span data-ttu-id="8c506-224">Agregue una línea al Dockerfile, como se muestra en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="8c506-224">Add one line to the Dockerfile, as shown in the following example:</span></span>

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a><span data-ttu-id="8c506-225">Apache</span><span class="sxs-lookup"><span data-stu-id="8c506-225">Apache</span></span>

<span data-ttu-id="8c506-226">Para implementar una aplicación Blazor WebAssembly en CentOS 7 o posterior:</span><span class="sxs-lookup"><span data-stu-id="8c506-226">To deploy a Blazor WebAssembly app to CentOS 7 or later:</span></span>

1. <span data-ttu-id="8c506-227">Cree el archivo de configuración de Apache.</span><span class="sxs-lookup"><span data-stu-id="8c506-227">Create the Apache configuration file.</span></span> <span data-ttu-id="8c506-228">El siguiente ejemplo es un archivo de configuración simplificado (*blazorapp.config*):</span><span class="sxs-lookup"><span data-stu-id="8c506-228">The following example is a simplified configuration file (*blazorapp.config*):</span></span>

   ```
   <VirtualHost *:80>
       ServerName www.example.com
       ServerAlias *.example.com

       DocumentRoot "/var/www/blazorapp"
       ErrorDocument 404 /index.html

       AddType application/wasm .wasm
       AddType application/octet-stream .dll
   
       <Directory "/var/www/blazorapp">
           Options -Indexes
           AllowOverride None
       </Directory>

       <IfModule mod_deflate.c>
           AddOutputFilterByType DEFLATE text/css
           AddOutputFilterByType DEFLATE application/javascript
           AddOutputFilterByType DEFLATE text/html
           AddOutputFilterByType DEFLATE application/octet-stream
           AddOutputFilterByType DEFLATE application/wasm
           <IfModule mod_setenvif.c>
           BrowserMatch ^Mozilla/4 gzip-only-text/html
           BrowserMatch ^Mozilla/4.0[678] no-gzip
           BrowserMatch bMSIE !no-gzip !gzip-only-text/html
       </IfModule>
       </IfModule>

       ErrorLog /var/log/httpd/blazorapp-error.log
       CustomLog /var/log/httpd/blazorapp-access.log common
   </VirtualHost>
   ```

1. <span data-ttu-id="8c506-229">Coloque el archivo de configuración de Apache en el directorio `/etc/httpd/conf.d/`, que es el directorio de configuración de Apache predeterminado en CentOS 7.</span><span class="sxs-lookup"><span data-stu-id="8c506-229">Place the Apache configuration file into the `/etc/httpd/conf.d/` directory, which is the default Apache configuration directory in CentOS 7.</span></span>

1. <span data-ttu-id="8c506-230">Coloque los archivos de la aplicación en el directorio `/var/www/blazorapp` (la ubicación especificada para `DocumentRoot` en el archivo de configuración).</span><span class="sxs-lookup"><span data-stu-id="8c506-230">Place the app's files into the `/var/www/blazorapp` directory (the location specified to `DocumentRoot` in the configuration file).</span></span>

1. <span data-ttu-id="8c506-231">Reinicie el servicio de Apache.</span><span class="sxs-lookup"><span data-stu-id="8c506-231">Restart the Apache service.</span></span>

<span data-ttu-id="8c506-232">Para obtener más información, vea [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) y [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span><span class="sxs-lookup"><span data-stu-id="8c506-232">For more information, see [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) and [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span></span>

### <a name="github-pages"></a><span data-ttu-id="8c506-233">GitHub Pages</span><span class="sxs-lookup"><span data-stu-id="8c506-233">GitHub Pages</span></span>

<span data-ttu-id="8c506-234">Para controlar las reescrituras de URL, agregue un archivo *404.html* con un script que controle el redireccionamiento de la solicitud a la página *index.html*.</span><span class="sxs-lookup"><span data-stu-id="8c506-234">To handle URL rewrites, add a *404.html* file with a script that handles redirecting the request to the *index.html* page.</span></span> <span data-ttu-id="8c506-235">Para consultar una implementación de ejemplo que ha proporcionado la comunidad, vea [Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)) (Aplicaciones de página única para GitHub Pages [rafrex/spa-github-pages en GitHub]).</span><span class="sxs-lookup"><span data-stu-id="8c506-235">For an example implementation provided by the community, see [Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span></span> <span data-ttu-id="8c506-236">Se puede ver un ejemplo del enfoque de la comunidad en [blazor-demo/blazor-demo.github.io en GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([sitio activo](https://blazor-demo.github.io/)).</span><span class="sxs-lookup"><span data-stu-id="8c506-236">An example using the community approach can be seen at [blazor-demo/blazor-demo.github.io on GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([live site](https://blazor-demo.github.io/)).</span></span>

<span data-ttu-id="8c506-237">Al usar un sitio de proyecto en lugar de un sitio de la organización, agregue o actualice la etiqueta `<base>` en *index.html*.</span><span class="sxs-lookup"><span data-stu-id="8c506-237">When using a project site instead of an organization site, add or update the `<base>` tag in *index.html*.</span></span> <span data-ttu-id="8c506-238">Defina el valor del atributo `href` con el nombre del repositorio de GitHub con una barra diagonal final (por ejemplo, `my-repository/`).</span><span class="sxs-lookup"><span data-stu-id="8c506-238">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `my-repository/`.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="8c506-239">Valores de configuración de host</span><span class="sxs-lookup"><span data-stu-id="8c506-239">Host configuration values</span></span>

<span data-ttu-id="8c506-240">Las aplicaciones [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) pueden aceptar los siguientes valores de configuración de host como argumentos de línea de comandos en tiempo de ejecución en el entorno de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="8c506-240">[Blazor WebAssembly apps](xref:blazor/hosting-models#blazor-webassembly) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="8c506-241">Raíz del contenido</span><span class="sxs-lookup"><span data-stu-id="8c506-241">Content root</span></span>

<span data-ttu-id="8c506-242">El argumento `--contentroot` establece la ruta de acceso absoluta al directorio que incluye los archivos de contenido de la aplicación ([raíz del contenido](xref:fundamentals/index#content-root)).</span><span class="sxs-lookup"><span data-stu-id="8c506-242">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files ([content root](xref:fundamentals/index#content-root)).</span></span> <span data-ttu-id="8c506-243">En los ejemplos siguientes, `/content-root-path` es la ruta de acceso raíz del contenido de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8c506-243">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="8c506-244">Pase el argumento al ejecutar la aplicación de forma local en un símbolo del sistema.</span><span class="sxs-lookup"><span data-stu-id="8c506-244">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="8c506-245">En el directorio de la aplicación, ejecute lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="8c506-245">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="8c506-246">Agregue una entrada al archivo *launchSettings.json* de la aplicación en el perfil **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="8c506-246">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="8c506-247">Esta configuración se utiliza cuando se ejecuta la aplicación mediante el depurador de Visual Studio y desde un símbolo del sistema con `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="8c506-247">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="8c506-248">En Visual Studio, especifique el argumento en **Propiedades** > **Depuración** > **Argumentos de la aplicación**.</span><span class="sxs-lookup"><span data-stu-id="8c506-248">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="8c506-249">Al establecer el argumento en la página de propiedades de Visual Studio, se agrega el argumento al archivo *launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="8c506-249">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="8c506-250">Ruta de acceso base</span><span class="sxs-lookup"><span data-stu-id="8c506-250">Path base</span></span>

<span data-ttu-id="8c506-251">El argumento `--pathbase` establece la ruta de acceso base de la aplicación para una aplicación que se ejecuta localmente con una ruta de acceso de URL relativa que no es raíz (el valor `href` de la etiqueta `<base>` se establece en una ruta de acceso que no sea `/` para ensayo y producción).</span><span class="sxs-lookup"><span data-stu-id="8c506-251">The `--pathbase` argument sets the app base path for an app run locally with a non-root relative URL path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="8c506-252">En los ejemplos siguientes, `/relative-URL-path` es la ruta de acceso base de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8c506-252">In the following examples, `/relative-URL-path` is the app's path base.</span></span> <span data-ttu-id="8c506-253">Para obtener más información, vea [Ruta de acceso base de la aplicación](xref:host-and-deploy/blazor/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="8c506-253">For more information, see [App base path](xref:host-and-deploy/blazor/index#app-base-path).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="8c506-254">A diferencia de la ruta de acceso proporcionada al valor `href` de la etiqueta `<base>`, no incluya una barra diagonal final (`/`) al pasar el valor del argumento `--pathbase`.</span><span class="sxs-lookup"><span data-stu-id="8c506-254">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="8c506-255">Si se proporciona la ruta de acceso base de la aplicación en la etiqueta `<base>` como `<base href="/CoolApp/">` (se incluye una barra diagonal final), se pasa el valor del argumento de línea de comandos como `--pathbase=/CoolApp` (sin barra diagonal final).</span><span class="sxs-lookup"><span data-stu-id="8c506-255">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="8c506-256">Pase el argumento al ejecutar la aplicación de forma local en un símbolo del sistema.</span><span class="sxs-lookup"><span data-stu-id="8c506-256">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="8c506-257">En el directorio de la aplicación, ejecute lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="8c506-257">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* <span data-ttu-id="8c506-258">Agregue una entrada al archivo *launchSettings.json* de la aplicación en el perfil **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="8c506-258">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="8c506-259">Esta configuración se utiliza cuando se ejecuta la aplicación mediante el depurador de Visual Studio y desde un símbolo del sistema con `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="8c506-259">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* <span data-ttu-id="8c506-260">En Visual Studio, especifique el argumento en **Propiedades** > **Depuración** > **Argumentos de la aplicación**.</span><span class="sxs-lookup"><span data-stu-id="8c506-260">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="8c506-261">Al establecer el argumento en la página de propiedades de Visual Studio, se agrega el argumento al archivo *launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="8c506-261">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a><span data-ttu-id="8c506-262">Direcciones URL</span><span class="sxs-lookup"><span data-stu-id="8c506-262">URLs</span></span>

<span data-ttu-id="8c506-263">El argumento `--urls` establece las direcciones IP o las direcciones de host con los puertos y protocolos en los que escuchar las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="8c506-263">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="8c506-264">Pase el argumento al ejecutar la aplicación de forma local en un símbolo del sistema.</span><span class="sxs-lookup"><span data-stu-id="8c506-264">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="8c506-265">En el directorio de la aplicación, ejecute lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="8c506-265">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="8c506-266">Agregue una entrada al archivo *launchSettings.json* de la aplicación en el perfil **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="8c506-266">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="8c506-267">Esta configuración se utiliza cuando se ejecuta la aplicación mediante el depurador de Visual Studio y desde un símbolo del sistema con `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="8c506-267">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="8c506-268">En Visual Studio, especifique el argumento en **Propiedades** > **Depuración** > **Argumentos de la aplicación**.</span><span class="sxs-lookup"><span data-stu-id="8c506-268">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="8c506-269">Al establecer el argumento en la página de propiedades de Visual Studio, se agrega el argumento al archivo *launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="8c506-269">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="configure-the-linker"></a><span data-ttu-id="8c506-270">Configurar el enlazador</span><span class="sxs-lookup"><span data-stu-id="8c506-270">Configure the Linker</span></span>

Blazor<span data-ttu-id="8c506-271"> realiza la vinculación de lenguaje intermedio (IL) en cada compilación de lanzamiento para quitar el IL innecesario de los ensamblados de salida.</span><span class="sxs-lookup"><span data-stu-id="8c506-271"> performs Intermediate Language (IL) linking on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="8c506-272">Para obtener más información, vea <xref:host-and-deploy/blazor/configure-linker>.</span><span class="sxs-lookup"><span data-stu-id="8c506-272">For more information, see <xref:host-and-deploy/blazor/configure-linker>.</span></span>

## <a name="custom-boot-resource-loading"></a><span data-ttu-id="8c506-273">Carga de recursos de arranque personalizados</span><span class="sxs-lookup"><span data-stu-id="8c506-273">Custom boot resource loading</span></span>

<span data-ttu-id="8c506-274">Una aplicación Blazor WebAssembly se puede inicializar con la función `loadBootResource` para invalidar el mecanismo de carga de recursos de arranque integrado.</span><span class="sxs-lookup"><span data-stu-id="8c506-274">A Blazor WebAssembly app can be initialized with the `loadBootResource` function to override the built-in boot resource loading mechanism.</span></span> <span data-ttu-id="8c506-275">Use `loadBootResource` en los siguientes escenarios:</span><span class="sxs-lookup"><span data-stu-id="8c506-275">Use `loadBootResource` for the following scenarios:</span></span>

* <span data-ttu-id="8c506-276">Para permitir a los usuarios cargar recursos estáticos, como datos de zona horaria o *dotnet.wasm* desde una red CDN.</span><span class="sxs-lookup"><span data-stu-id="8c506-276">Allow users to load static resources, such as timezone data or *dotnet.wasm* from a CDN.</span></span>
* <span data-ttu-id="8c506-277">Para cargar los ensamblados comprimidos mediante una solicitud HTTP y descomprimirlos en el cliente para aquellos hosts que no admiten la captura de contenido comprimido del servidor.</span><span class="sxs-lookup"><span data-stu-id="8c506-277">Load compressed assemblies using an HTTP request and decompress them on the client for hosts that don't support fetching compressed contents from the server.</span></span>
* <span data-ttu-id="8c506-278">Para asignar otro alias a los recursos mediante el redireccionamiento de cada solicitud `fetch` a un nuevo nombre.</span><span class="sxs-lookup"><span data-stu-id="8c506-278">Alias resources to a different name by redirecting each `fetch` request to a new name.</span></span>

<span data-ttu-id="8c506-279">Los parámetros `loadBootResource` aparecen en la tabla siguiente.</span><span class="sxs-lookup"><span data-stu-id="8c506-279">`loadBootResource` parameters appear in the following table.</span></span>

| <span data-ttu-id="8c506-280">Parámetro</span><span class="sxs-lookup"><span data-stu-id="8c506-280">Parameter</span></span>    | <span data-ttu-id="8c506-281">Descripción</span><span class="sxs-lookup"><span data-stu-id="8c506-281">Description</span></span> |
| ---
<span data-ttu-id="8c506-282">title: "Hospedaje e implementación de ASP.NET Core Blazor WebAssembly" author: description: "Aprenda a hospedar e implementar una aplicación Blazor con ASP.NET Core, redes de entrega de contenido (CDN), servidores de archivos y páginas de GitHub".</span><span class="sxs-lookup"><span data-stu-id="8c506-282">title: 'Host and deploy ASP.NET Core Blazor WebAssembly' author: description: 'Learn how to host and deploy a Blazor app using ASP.NET Core, Content Delivery Networks (CDN), file servers, and GitHub Pages.'</span></span>
<span data-ttu-id="8c506-283">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c506-283">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c506-284">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c506-284">'Blazor'</span></span>
- <span data-ttu-id="8c506-285">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c506-285">'Identity'</span></span>
- <span data-ttu-id="8c506-286">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c506-286">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c506-287">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c506-287">'Razor'</span></span>
- <span data-ttu-id="8c506-288">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c506-288">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c506-289">title: "Hospedaje e implementación de ASP.NET Core Blazor WebAssembly" author: description: "Aprenda a hospedar e implementar una aplicación Blazor con ASP.NET Core, redes de entrega de contenido (CDN), servidores de archivos y páginas de GitHub".</span><span class="sxs-lookup"><span data-stu-id="8c506-289">title: 'Host and deploy ASP.NET Core Blazor WebAssembly' author: description: 'Learn how to host and deploy a Blazor app using ASP.NET Core, Content Delivery Networks (CDN), file servers, and GitHub Pages.'</span></span>
<span data-ttu-id="8c506-290">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c506-290">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c506-291">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c506-291">'Blazor'</span></span>
- <span data-ttu-id="8c506-292">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c506-292">'Identity'</span></span>
- <span data-ttu-id="8c506-293">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c506-293">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c506-294">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c506-294">'Razor'</span></span>
- <span data-ttu-id="8c506-295">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c506-295">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c506-296">title: "Hospedaje e implementación de ASP.NET Core Blazor WebAssembly" author: description: "Aprenda a hospedar e implementar una aplicación Blazor con ASP.NET Core, redes de entrega de contenido (CDN), servidores de archivos y páginas de GitHub".</span><span class="sxs-lookup"><span data-stu-id="8c506-296">title: 'Host and deploy ASP.NET Core Blazor WebAssembly' author: description: 'Learn how to host and deploy a Blazor app using ASP.NET Core, Content Delivery Networks (CDN), file servers, and GitHub Pages.'</span></span>
<span data-ttu-id="8c506-297">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c506-297">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c506-298">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c506-298">'Blazor'</span></span>
- <span data-ttu-id="8c506-299">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c506-299">'Identity'</span></span>
- <span data-ttu-id="8c506-300">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c506-300">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c506-301">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c506-301">'Razor'</span></span>
- <span data-ttu-id="8c506-302">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c506-302">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c506-303">title: "Hospedaje e implementación de ASP.NET Core Blazor WebAssembly" author: description: "Aprenda a hospedar e implementar una aplicación Blazor con ASP.NET Core, redes de entrega de contenido (CDN), servidores de archivos y páginas de GitHub".</span><span class="sxs-lookup"><span data-stu-id="8c506-303">title: 'Host and deploy ASP.NET Core Blazor WebAssembly' author: description: 'Learn how to host and deploy a Blazor app using ASP.NET Core, Content Delivery Networks (CDN), file servers, and GitHub Pages.'</span></span>
<span data-ttu-id="8c506-304">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c506-304">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c506-305">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c506-305">'Blazor'</span></span>
- <span data-ttu-id="8c506-306">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c506-306">'Identity'</span></span>
- <span data-ttu-id="8c506-307">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c506-307">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c506-308">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c506-308">'Razor'</span></span>
- <span data-ttu-id="8c506-309">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c506-309">'SignalR' uid:</span></span> 

<span data-ttu-id="8c506-310">------ | --- title: "Hospedaje e implementación de ASP.NET Core Blazor WebAssembly" author: description: "Aprenda a hospedar e implementar una aplicación Blazor con ASP.NET Core, redes de entrega de contenido (CDN), servidores de archivos y páginas de GitHub".</span><span class="sxs-lookup"><span data-stu-id="8c506-310">------ | --- title: 'Host and deploy ASP.NET Core Blazor WebAssembly' author: description: 'Learn how to host and deploy a Blazor app using ASP.NET Core, Content Delivery Networks (CDN), file servers, and GitHub Pages.'</span></span>
<span data-ttu-id="8c506-311">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c506-311">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c506-312">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c506-312">'Blazor'</span></span>
- <span data-ttu-id="8c506-313">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c506-313">'Identity'</span></span>
- <span data-ttu-id="8c506-314">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c506-314">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c506-315">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c506-315">'Razor'</span></span>
- <span data-ttu-id="8c506-316">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c506-316">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c506-317">title: "Hospedaje e implementación de ASP.NET Core Blazor WebAssembly" author: description: "Aprenda a hospedar e implementar una aplicación Blazor con ASP.NET Core, redes de entrega de contenido (CDN), servidores de archivos y páginas de GitHub".</span><span class="sxs-lookup"><span data-stu-id="8c506-317">title: 'Host and deploy ASP.NET Core Blazor WebAssembly' author: description: 'Learn how to host and deploy a Blazor app using ASP.NET Core, Content Delivery Networks (CDN), file servers, and GitHub Pages.'</span></span>
<span data-ttu-id="8c506-318">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c506-318">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c506-319">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c506-319">'Blazor'</span></span>
- <span data-ttu-id="8c506-320">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c506-320">'Identity'</span></span>
- <span data-ttu-id="8c506-321">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c506-321">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c506-322">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c506-322">'Razor'</span></span>
- <span data-ttu-id="8c506-323">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c506-323">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c506-324">title: "Hospedaje e implementación de ASP.NET Core Blazor WebAssembly" author: description: "Aprenda a hospedar e implementar una aplicación Blazor con ASP.NET Core, redes de entrega de contenido (CDN), servidores de archivos y páginas de GitHub".</span><span class="sxs-lookup"><span data-stu-id="8c506-324">title: 'Host and deploy ASP.NET Core Blazor WebAssembly' author: description: 'Learn how to host and deploy a Blazor app using ASP.NET Core, Content Delivery Networks (CDN), file servers, and GitHub Pages.'</span></span>
<span data-ttu-id="8c506-325">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c506-325">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c506-326">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c506-326">'Blazor'</span></span>
- <span data-ttu-id="8c506-327">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c506-327">'Identity'</span></span>
- <span data-ttu-id="8c506-328">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c506-328">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c506-329">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c506-329">'Razor'</span></span>
- <span data-ttu-id="8c506-330">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c506-330">'SignalR' uid:</span></span> 

<span data-ttu-id="8c506-331">------ | | `type`       | Tipo del recurso.</span><span class="sxs-lookup"><span data-stu-id="8c506-331">------ | | `type`       | The type of the resource.</span></span> <span data-ttu-id="8c506-332">Tipos permitidos: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`, `timezonedata` | | `name`       | El nombre del recurso.</span><span class="sxs-lookup"><span data-stu-id="8c506-332">Permissable types: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`, `timezonedata` | | `name`       | The name of the resource.</span></span> <span data-ttu-id="8c506-333">| | `defaultUri` | URI relativo o absoluto del recurso.</span><span class="sxs-lookup"><span data-stu-id="8c506-333">| | `defaultUri` | The relative or absolute URI of the resource.</span></span> <span data-ttu-id="8c506-334">| | `integrity`  | Cadena de integridad que representa el contenido esperado de la respuesta.</span><span class="sxs-lookup"><span data-stu-id="8c506-334">| | `integrity`  | The integrity string representing the expected content in the response.</span></span> |

<span data-ttu-id="8c506-335">`loadBootResource` devuelve cualquiera de los siguientes elementos para invalidar el proceso de carga:</span><span class="sxs-lookup"><span data-stu-id="8c506-335">`loadBootResource` returns any of the following to override the loading process:</span></span>

* <span data-ttu-id="8c506-336">Cadena URI.</span><span class="sxs-lookup"><span data-stu-id="8c506-336">URI string.</span></span> <span data-ttu-id="8c506-337">En el ejemplo siguiente (*wwwroot/index.html*), los siguientes archivos se sirven desde una red CDN en `https://my-awesome-cdn.com/`:</span><span class="sxs-lookup"><span data-stu-id="8c506-337">In the following example (*wwwroot/index.html*), the following files are served from a CDN at `https://my-awesome-cdn.com/`:</span></span>

  * <span data-ttu-id="8c506-338">*dotnet.\*.js*</span><span class="sxs-lookup"><span data-stu-id="8c506-338">*dotnet.\*.js*</span></span>
  * <span data-ttu-id="8c506-339">*dotnet.wasm*</span><span class="sxs-lookup"><span data-stu-id="8c506-339">*dotnet.wasm*</span></span>
  * <span data-ttu-id="8c506-340">Datos de zona horaria</span><span class="sxs-lookup"><span data-stu-id="8c506-340">Timezone data</span></span>

  ```html
  ...

  <script src="_framework/blazor.webassembly.js" autostart="false"></script>
  <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        console.log(`Loading: '${type}', '${name}', '${defaultUri}', '${integrity}'`);
        switch (type) {
          case 'dotnetjs':
          case 'dotnetwasm':
          case 'timezonedata':
            return `https://my-awesome-cdn.com/blazorwebassembly/3.2.0/${name}`;
        }
      }
    });
  </script>
  ```

* <span data-ttu-id="8c506-341">`Promise<Response>`.</span><span class="sxs-lookup"><span data-stu-id="8c506-341">`Promise<Response>`.</span></span> <span data-ttu-id="8c506-342">Pase el parámetro `integrity` en un encabezado para conservar el comportamiento de comprobación de integridad predeterminado.</span><span class="sxs-lookup"><span data-stu-id="8c506-342">Pass the `integrity` parameter in a header to retain the default integrity-checking behavior.</span></span>

  <span data-ttu-id="8c506-343">En el ejemplo siguiente (*wwwroot/index.html*) se agrega un encabezado HTTP personalizado a las solicitudes salientes y se pasa el parámetro `integrity` a la llamada a `fetch`:</span><span class="sxs-lookup"><span data-stu-id="8c506-343">The following example (*wwwroot/index.html*) adds a custom HTTP header to the outbound requests and passes the `integrity` parameter through to the `fetch` call:</span></span>
  
  ```html
  <script src="_framework/blazor.webassembly.js" autostart="false"></script>
  <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        return fetch(defaultUri, { 
          cache: 'no-cache',
          integrity: integrity,
          headers: { 'MyCustomHeader': 'My custom value' }
        });
      }
    });
  </script>
  ```

* <span data-ttu-id="8c506-344">`null`/`undefined`, que da lugar al comportamiento de carga predeterminado.</span><span class="sxs-lookup"><span data-stu-id="8c506-344">`null`/`undefined`, which results in the default loading behavior.</span></span>

<span data-ttu-id="8c506-345">Los orígenes externos deben devolver los encabezados CORS necesarios para que los exploradores permitan la carga de recursos entre orígenes.</span><span class="sxs-lookup"><span data-stu-id="8c506-345">External sources must return the required CORS headers for browsers to allow the cross-origin resource loading.</span></span> <span data-ttu-id="8c506-346">Normalmente, las redes CDN proporcionan los encabezados necesarios de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="8c506-346">CDNs usually provide the required headers by default.</span></span>

<span data-ttu-id="8c506-347">Solo tiene que especificar tipos para los comportamientos personalizados.</span><span class="sxs-lookup"><span data-stu-id="8c506-347">You only need to specify types for custom behaviors.</span></span> <span data-ttu-id="8c506-348">Los tipos no especificados en `loadBootResource` se cargan mediante el marco de trabajo según sus comportamientos de carga predeterminados.</span><span class="sxs-lookup"><span data-stu-id="8c506-348">Types not specified to `loadBootResource` are loaded by the framework per their default loading behaviors.</span></span>

## <a name="change-the-filename-extension-of-dll-files"></a><span data-ttu-id="8c506-349">Cambio de la extensión de nombre de archivo de los archivos DLL</span><span class="sxs-lookup"><span data-stu-id="8c506-349">Change the filename extension of DLL files</span></span>

<span data-ttu-id="8c506-350">En caso de que necesite cambiar las extensiones de nombre de archivo de los archivos *.dll* publicados de la aplicación, siga las instrucciones de esta sección.</span><span class="sxs-lookup"><span data-stu-id="8c506-350">In case you have a need to change the filename extensions of the app's published *.dll* files, follow the guidance in this section.</span></span>

<span data-ttu-id="8c506-351">Después de publicar la aplicación, use un script de shell o una canalización de compilación de DevOps para cambiar el nombre de los archivos *.dll* a fin de usar otra extensión de archivo.</span><span class="sxs-lookup"><span data-stu-id="8c506-351">After publishing the app, use a shell script or DevOps build pipeline to rename *.dll* files to use a different file extension.</span></span> <span data-ttu-id="8c506-352">Elija como destino los archivos *.dll* del directorio *wwwroot* de la salida publicada de la aplicación (por ejemplo, *{RAÍZ DE CONTENIDO}/bin/Release/netstandard2.1/publish/wwwroot*).</span><span class="sxs-lookup"><span data-stu-id="8c506-352">Target the *.dll* files in the *wwwroot* directory of the app's published output (for example, *{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot*).</span></span>

<span data-ttu-id="8c506-353">En los ejemplos siguientes se cambia el nombre de los archivos *.dll* para que usen la extensión de archivo *.bin*.</span><span class="sxs-lookup"><span data-stu-id="8c506-353">In the following examples, *.dll* files are renamed to use the *.bin* file extension.</span></span>

<span data-ttu-id="8c506-354">En Windows:</span><span class="sxs-lookup"><span data-stu-id="8c506-354">On Windows:</span></span>

```powershell
dir .\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content .\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content .\_framework\blazor.boot.json
```

<span data-ttu-id="8c506-355">Si los recursos de trabajo de servicio también están en uso, agregue el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="8c506-355">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content .\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content .\service-worker-assets.js
```

<span data-ttu-id="8c506-356">En Linux o macOS:</span><span class="sxs-lookup"><span data-stu-id="8c506-356">On Linux or macOS:</span></span>

```console
for f in _framework/_bin/*; do mv "$f" "`echo $f | sed -e 's/\.dll\b/.bin/g'`"; done
sed -i 's/\.dll"/.bin"/g' _framework/blazor.boot.json
```

<span data-ttu-id="8c506-357">Si los recursos de trabajo de servicio también están en uso, agregue el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="8c506-357">If service worker assets are also in use, add the following command:</span></span>

```console
sed -i 's/\.dll"/.bin"/g' service-worker-assets.js
```
   
<span data-ttu-id="8c506-358">Para usar una extensión de archivo diferente a *.bin*, reemplace *.bin* en los comandos anteriores.</span><span class="sxs-lookup"><span data-stu-id="8c506-358">To use a different file extension than *.bin*, replace *.bin* in the preceding commands.</span></span>

<span data-ttu-id="8c506-359">Para manipular los archivos comprimidos *blazor.boot.json.gz* y *blazor.boot.json.br*, adopte alguno de los enfoques siguientes:</span><span class="sxs-lookup"><span data-stu-id="8c506-359">To address the compressed *blazor.boot.json.gz* and *blazor.boot.json.br* files, adopt either of the following approaches:</span></span>

* <span data-ttu-id="8c506-360">Quite los archivos comprimidos *blazor.boot.json.gz* y *blazor.boot.json.br*.</span><span class="sxs-lookup"><span data-stu-id="8c506-360">Remove the compressed *blazor.boot.json.gz* and *blazor.boot.json.br* files.</span></span> <span data-ttu-id="8c506-361">Con este enfoque, la compresión está deshabilitada.</span><span class="sxs-lookup"><span data-stu-id="8c506-361">Compression is disabled with this approach.</span></span>
* <span data-ttu-id="8c506-362">Vuelva a comprimir el archivo *blazor.boot.json* actualizado.</span><span class="sxs-lookup"><span data-stu-id="8c506-362">Recompress the updated *blazor.boot.json* file.</span></span>

<span data-ttu-id="8c506-363">Las instrucciones anteriores también se aplican cuando se usan recursos de trabajo de servicio.</span><span class="sxs-lookup"><span data-stu-id="8c506-363">The preceding guidance also applies when service worker assets are in use.</span></span> <span data-ttu-id="8c506-364">Quite o vuelva a comprimir *wwwroot/service-worker-assets.js.br* y *wwwroot/service-worker-assets.js.gz*.</span><span class="sxs-lookup"><span data-stu-id="8c506-364">Remove or recompress *wwwroot/service-worker-assets.js.br* and *wwwroot/service-worker-assets.js.gz*.</span></span> <span data-ttu-id="8c506-365">De lo contrario, las comprobaciones de integridad de los archivos producirán errores en el explorador.</span><span class="sxs-lookup"><span data-stu-id="8c506-365">Otherwise, file integrity checks fail in the browser.</span></span>

<span data-ttu-id="8c506-366">En el siguiente ejemplo de Windows se usa un script de PowerShell colocado en la raíz del proyecto.</span><span class="sxs-lookup"><span data-stu-id="8c506-366">The following Windows example uses a PowerShell script placed at the root of the project.</span></span>

<span data-ttu-id="8c506-367">*ChangeDLLExtensions.ps1:* :</span><span class="sxs-lookup"><span data-stu-id="8c506-367">*ChangeDLLExtensions.ps1:*:</span></span>

```powershell
param([string]$filepath,[string]$tfm)
dir $filepath\bin\Release\$tfm\wwwroot\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json
Remove-Item $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json.gz
```

<span data-ttu-id="8c506-368">Si los recursos de trabajo de servicio también están en uso, agregue el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="8c506-368">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js
```

<span data-ttu-id="8c506-369">En el archivo del proyecto, el script se ejecuta después de publicar la aplicación:</span><span class="sxs-lookup"><span data-stu-id="8c506-369">In the project file, the script is run after publishing the app:</span></span>

```xml
<Target Name="ChangeDLLFileExtensions" AfterTargets="Publish" Condition="'$(Configuration)'=='Release'">
  <Exec Command="powershell.exe -command &quot;&amp; { .\ChangeDLLExtensions.ps1 '$(SolutionDir)' '$(TargetFramework)'}&quot;" />
</Target>
```

<span data-ttu-id="8c506-370">Para proporcionar comentarios, visite [aspnetcore/issues #5477](https://github.com/dotnet/aspnetcore/issues/5477).</span><span class="sxs-lookup"><span data-stu-id="8c506-370">To provide feedback, visit [aspnetcore/issues #5477](https://github.com/dotnet/aspnetcore/issues/5477).</span></span>
 