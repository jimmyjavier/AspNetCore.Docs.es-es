---
<span data-ttu-id="253ab-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="253ab-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="253ab-102">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="253ab-102">'Blazor'</span></span>
- <span data-ttu-id="253ab-103">"Identity"</span><span class="sxs-lookup"><span data-stu-id="253ab-103">'Identity'</span></span>
- <span data-ttu-id="253ab-104">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="253ab-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="253ab-105">"Razor"</span><span class="sxs-lookup"><span data-stu-id="253ab-105">'Razor'</span></span>
- <span data-ttu-id="253ab-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="253ab-106">'SignalR' uid:</span></span> 

---
# <a name="development-time-iis-support-in-visual-studio-for-aspnet-core"></a><span data-ttu-id="253ab-107">Compatibilidad de IIS de tiempo de desarrollo en Visual Studio para ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="253ab-107">Development-time IIS support in Visual Studio for ASP.NET Core</span></span>

<span data-ttu-id="253ab-108">Por [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span><span class="sxs-lookup"><span data-stu-id="253ab-108">By [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="253ab-109">En este artículo se describe la compatibilidad de [Visual Studio](https://visualstudio.microsoft.com) con la depuración de aplicaciones ASP.NET Core que se ejecutan con IIS en Windows Server.</span><span class="sxs-lookup"><span data-stu-id="253ab-109">This article describes [Visual Studio](https://visualstudio.microsoft.com) support for debugging ASP.NET Core apps running with IIS on Windows Server.</span></span> <span data-ttu-id="253ab-110">En este tema se explica cómo habilitar este escenario y configurar un proyecto.</span><span class="sxs-lookup"><span data-stu-id="253ab-110">This topic walks through enabling this scenario and setting up a project.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="253ab-111">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="253ab-111">Prerequisites</span></span>

* <span data-ttu-id="253ab-112">[Visual Studio para Windows](https://visualstudio.microsoft.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="253ab-112">[Visual Studio for Windows](https://visualstudio.microsoft.com/downloads/)</span></span>
* <span data-ttu-id="253ab-113">Carga de trabajo de **ASP.NET y desarrollo web**</span><span class="sxs-lookup"><span data-stu-id="253ab-113">**ASP.NET and web development** workload</span></span>
* <span data-ttu-id="253ab-114">Carga de trabajo **Desarrollo multiplataforma de .NET Core**</span><span class="sxs-lookup"><span data-stu-id="253ab-114">**.NET Core cross-platform development** workload</span></span>
* <span data-ttu-id="253ab-115">Certificado de seguridad X.509 (para compatibilidad con HTTPS)</span><span class="sxs-lookup"><span data-stu-id="253ab-115">X.509 security certificate (for HTTPS support)</span></span>

## <a name="enable-iis"></a><span data-ttu-id="253ab-116">Habilitar IIS</span><span class="sxs-lookup"><span data-stu-id="253ab-116">Enable IIS</span></span>

1. <span data-ttu-id="253ab-117">En Windows, navegue a **Panel de control** > **Programas** > **Programas y características** > **Activar o desactivar las características de Windows** (lado izquierdo de la pantalla).</span><span class="sxs-lookup"><span data-stu-id="253ab-117">In Windows, navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="253ab-118">Active la casilla **Internet Information Services**.</span><span class="sxs-lookup"><span data-stu-id="253ab-118">Select the **Internet Information Services** check box.</span></span> <span data-ttu-id="253ab-119">Seleccione **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="253ab-119">Select **OK**.</span></span>

<span data-ttu-id="253ab-120">La instalación de IIS puede requerir un reinicio del sistema.</span><span class="sxs-lookup"><span data-stu-id="253ab-120">The IIS installation may require a system restart.</span></span>

## <a name="configure-iis"></a><span data-ttu-id="253ab-121">Configurar IIS</span><span class="sxs-lookup"><span data-stu-id="253ab-121">Configure IIS</span></span>

<span data-ttu-id="253ab-122">IIS debe tener un sitio web configurado con lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="253ab-122">IIS must have a website configured with the following:</span></span>

* <span data-ttu-id="253ab-123">**Nombre de host**: por lo general, el **sitio web predeterminado** se usa con un **nombre de host** de `localhost`.</span><span class="sxs-lookup"><span data-stu-id="253ab-123">**Host name**: Typically, the **Default Web Site** is used with a **Host name** of `localhost`.</span></span> <span data-ttu-id="253ab-124">Sin embargo, sirve cualquier sitio web de IIS válido con un nombre de host único.</span><span class="sxs-lookup"><span data-stu-id="253ab-124">However, any valid IIS website with a unique host name works.</span></span>
* <span data-ttu-id="253ab-125">**Enlace de sitio**</span><span class="sxs-lookup"><span data-stu-id="253ab-125">**Site Binding**</span></span>
  * <span data-ttu-id="253ab-126">Para las aplicaciones que requieran HTTPS, cree un enlace al puerto 443 con un certificado.</span><span class="sxs-lookup"><span data-stu-id="253ab-126">For apps that require HTTPS, create a binding to port 443 with a certificate.</span></span> <span data-ttu-id="253ab-127">Por lo general, se usa el **certificado de desarrollo de IIS Express**, pero cualquier certificado válido sirve.</span><span class="sxs-lookup"><span data-stu-id="253ab-127">Typically, the **IIS Express Development Certificate** is used, but any valid certificate works.</span></span>
  * <span data-ttu-id="253ab-128">Para las aplicaciones que usan HTTP, confirme la existencia de un enlace al puerto 80 o cree un enlace a dicho puerto si se trata de un sitio nuevo.</span><span class="sxs-lookup"><span data-stu-id="253ab-128">For apps that use HTTP, confirm the existence of a binding to post 80 or create a binding to port 80 for a new site.</span></span>
  * <span data-ttu-id="253ab-129">Utilice un enlace único para HTTP o HTTPS.</span><span class="sxs-lookup"><span data-stu-id="253ab-129">Use a single binding for either HTTP or HTTPS.</span></span> <span data-ttu-id="253ab-130">**No se admite el enlace al mismo tiempo a los puertos HTTP y HTTPS.**</span><span class="sxs-lookup"><span data-stu-id="253ab-130">**Binding to both HTTP and HTTPS ports simultaneously isn't supported.**</span></span>

## <a name="enable-development-time-iis-support-in-visual-studio"></a><span data-ttu-id="253ab-131">Habilitación de la compatibilidad con IIS en tiempo de desarrollo en Visual Studio</span><span class="sxs-lookup"><span data-stu-id="253ab-131">Enable development-time IIS support in Visual Studio</span></span>

1. <span data-ttu-id="253ab-132">Inicie el instalador de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="253ab-132">Launch the Visual Studio installer.</span></span>
1. <span data-ttu-id="253ab-133">Seleccione **Modificar** para la instalación de Visual Studio que se pretende usar para la compatibilidad con IIS en tiempo de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="253ab-133">Select **Modify** for the Visual Studio installation that you plan to use for IIS development-time support.</span></span>
1. <span data-ttu-id="253ab-134">Para la carga de trabajo de **Desarrollo de ASP.NET y web** , busque e instale el componente **Compatibilidad con IIS en tiempo de desarrollo**.</span><span class="sxs-lookup"><span data-stu-id="253ab-134">For the **ASP.NET and web development** workload, locate and install the **Development time IIS support** component.</span></span>

   <span data-ttu-id="253ab-135">El componente se enumera en la sección **Opcional**, en **Compatibilidad con IIS en tiempo de desarrollo**, dentro del panel **Detalles de instalación** a la derecha de las cargas de trabajo.</span><span class="sxs-lookup"><span data-stu-id="253ab-135">The component is listed in the **Optional** section under **Development time IIS support** in the **Installation details** panel to the right of the workloads.</span></span> <span data-ttu-id="253ab-136">El componente instala el [módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module), que es un módulo de IIS nativo necesario para ejecutar aplicaciones de ASP.NET Core con IIS.</span><span class="sxs-lookup"><span data-stu-id="253ab-136">The component installs the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), which is a native IIS module required to run ASP.NET Core apps with IIS.</span></span>

## <a name="configure-the-project"></a><span data-ttu-id="253ab-137">Configuración del proyecto</span><span class="sxs-lookup"><span data-stu-id="253ab-137">Configure the project</span></span>

### <a name="https-redirection"></a><span data-ttu-id="253ab-138">Redireccionamiento de HTTPS</span><span class="sxs-lookup"><span data-stu-id="253ab-138">HTTPS redirection</span></span>

<span data-ttu-id="253ab-139">Para un nuevo proyecto que requiere HTTPS, seleccione la casilla **Configurar para HTTPS** en la ventana **Crear una aplicación web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="253ab-139">For a new project that requires HTTPS, select the check box to **Configure for HTTPS** in the **Create a new ASP.NET Core Web Application** window.</span></span> <span data-ttu-id="253ab-140">Al seleccionar la casilla, se agrega [redireccionamiento HTTPS y middleware HSTS](xref:security/enforcing-ssl) a la aplicación cuando esta se crea.</span><span class="sxs-lookup"><span data-stu-id="253ab-140">Selecting the check box adds [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) to the app when it's created.</span></span>

<span data-ttu-id="253ab-141">Para un proyecto existente que requiere HTTPS, use el redireccionamiento HTTPS y el middleware HSTS en `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="253ab-141">For an existing project that requires HTTPS, use HTTPS Redirection and HSTS Middleware in `Startup.Configure`.</span></span> <span data-ttu-id="253ab-142">Para obtener más información, vea <xref:security/enforcing-ssl>.</span><span class="sxs-lookup"><span data-stu-id="253ab-142">For more information, see <xref:security/enforcing-ssl>.</span></span>

<span data-ttu-id="253ab-143">Para un proyecto que usa HTTP, el [redireccionamiento HTTPS y el middleware HSTS](xref:security/enforcing-ssl) no se agregan a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="253ab-143">For a project that uses HTTP, [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) aren't added to the app.</span></span> <span data-ttu-id="253ab-144">No se requiere ninguna configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="253ab-144">No app configuration is required.</span></span>

### <a name="iis-launch-profile"></a><span data-ttu-id="253ab-145">Perfil de inicio de IIS</span><span class="sxs-lookup"><span data-stu-id="253ab-145">IIS launch profile</span></span>

<span data-ttu-id="253ab-146">Cree un nuevo perfil de inicio para agregar la compatibilidad con IIS en tiempo de desarrollo:</span><span class="sxs-lookup"><span data-stu-id="253ab-146">Create a new launch profile to add development-time IIS support:</span></span>

1. <span data-ttu-id="253ab-147">Haga clic con el botón derecho en el **Explorador de soluciones**.</span><span class="sxs-lookup"><span data-stu-id="253ab-147">Right-click the project in **Solution Explorer**.</span></span> <span data-ttu-id="253ab-148">Haga clic en **Propiedades**.</span><span class="sxs-lookup"><span data-stu-id="253ab-148">Select **Properties**.</span></span> <span data-ttu-id="253ab-149">Abra la pestaña **Depurar**.</span><span class="sxs-lookup"><span data-stu-id="253ab-149">Open the **Debug** tab.</span></span>
1. <span data-ttu-id="253ab-150">En **Perfil**, seleccione el botón **Nuevo**.</span><span class="sxs-lookup"><span data-stu-id="253ab-150">For **Profile**, select the **New** button.</span></span> <span data-ttu-id="253ab-151">Asigne el perfil el nombre "IIS" en la ventana emergente.</span><span class="sxs-lookup"><span data-stu-id="253ab-151">Name the profile "IIS" in the popup window.</span></span> <span data-ttu-id="253ab-152">Seleccione **Aceptar** para crear el perfil.</span><span class="sxs-lookup"><span data-stu-id="253ab-152">Select **OK** to create the profile.</span></span>
1. <span data-ttu-id="253ab-153">En **Iniciar**, seleccione **IIS** en la lista.</span><span class="sxs-lookup"><span data-stu-id="253ab-153">For the **Launch** setting, select **IIS** from the list.</span></span>
1. <span data-ttu-id="253ab-154">Active la casilla **Iniciar explorador** y proporcione la dirección URL del punto de conexión.</span><span class="sxs-lookup"><span data-stu-id="253ab-154">Select the check box for **Launch browser** and provide the endpoint URL.</span></span>

   <span data-ttu-id="253ab-155">Si la aplicación requiere HTTPS, use un punto de conexión HTTPS (`https://`).</span><span class="sxs-lookup"><span data-stu-id="253ab-155">When the app requires HTTPS, use an HTTPS endpoint (`https://`).</span></span> <span data-ttu-id="253ab-156">Para HTTP, use un punto de conexión HTTP (`http://`).</span><span class="sxs-lookup"><span data-stu-id="253ab-156">For HTTP, use an HTTP (`http://`) endpoint.</span></span>

   <span data-ttu-id="253ab-157">Proporcione el mismo nombre de host y puerto especificados en la [configuración de IIS establecida en usos anteriores](#configure-iis), normalmente `localhost`.</span><span class="sxs-lookup"><span data-stu-id="253ab-157">Provide the same host name and port as the [IIS configuration specified earlier uses](#configure-iis), typically `localhost`.</span></span>

   <span data-ttu-id="253ab-158">Proporcione el nombre de la aplicación al final de la dirección URL.</span><span class="sxs-lookup"><span data-stu-id="253ab-158">Provide the name of the app at the end of the URL.</span></span>

   <span data-ttu-id="253ab-159">Por ejemplo, `https://localhost/WebApplication1` (HTTPS) o `http://localhost/WebApplication1` (HTTP) son direcciones URL de punto de conexión válidas.</span><span class="sxs-lookup"><span data-stu-id="253ab-159">For example, `https://localhost/WebApplication1` (HTTPS) or `http://localhost/WebApplication1` (HTTP) are valid endpoint URLs.</span></span>
1. <span data-ttu-id="253ab-160">En la sección **Variables de entorno**, seleccione el botón **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="253ab-160">In the **Environment variables** section, select the **Add** button.</span></span> <span data-ttu-id="253ab-161">Proporcione una variable de entorno con un **Nombre** de `ASPNETCORE_ENVIRONMENT` y un **Valor** de `Development`.</span><span class="sxs-lookup"><span data-stu-id="253ab-161">Provide an environment variable with a **Name** of `ASPNETCORE_ENVIRONMENT` and a **Value** of `Development`.</span></span>
1. <span data-ttu-id="253ab-162">En el área **Configuración del servidor web**, defina la **Dirección URL de la aplicación** con el mismo valor utilizado para la dirección URL de punto de conexión de **Iniciar el explorador**.</span><span class="sxs-lookup"><span data-stu-id="253ab-162">In the **Web Server Settings** area, set the **App URL** to the same value used for the **Launch browser** endpoint URL.</span></span>
1. <span data-ttu-id="253ab-163">Para la configuración del **Modelo de hospedaje** de Visual Studio 2019 o posterior, seleccione **Predeterminado** para usar el modelo de hospedaje utilizado por el proyecto.</span><span class="sxs-lookup"><span data-stu-id="253ab-163">For the **Hosting Model** setting in Visual Studio 2019 or later, select **Default** to use the hosting model used by the project.</span></span> <span data-ttu-id="253ab-164">Si el proyecto establece la propiedad `<AspNetCoreHostingModel>` en su archivo del proyecto, se usa el valor de la propiedad (`InProcess` o `OutOfProcess`).</span><span class="sxs-lookup"><span data-stu-id="253ab-164">If the project sets the `<AspNetCoreHostingModel>` property in its project file, the value of the property (`InProcess` or `OutOfProcess`) is used.</span></span> <span data-ttu-id="253ab-165">Si la propiedad no existe, se usa el modelo de hospedaje predeterminado de la aplicación, que está en proceso.</span><span class="sxs-lookup"><span data-stu-id="253ab-165">If the property isn't present, the default hosting model of the app is used, which is in-process.</span></span> <span data-ttu-id="253ab-166">Si la aplicación requiere una configuración del modelo de hospedaje distinta a la del modelo de hospedaje habitual de la aplicación, defina el **Modelo de hospedaje** como `In Process` o `Out Of Process`, según proceda.</span><span class="sxs-lookup"><span data-stu-id="253ab-166">If the app requires an explicit hosting model setting different from the app's normal hosting model, set the **Hosting Model** to either `In Process` or `Out Of Process` as needed.</span></span>
1. <span data-ttu-id="253ab-167">Guarde el perfil.</span><span class="sxs-lookup"><span data-stu-id="253ab-167">Save the profile.</span></span>

<span data-ttu-id="253ab-168">Si no se usa Visual Studio, agregue manualmente un perfil de inicio al archivo [launchSettings.json](https://json.schemastore.org/launchsettings) en la carpeta *Propiedades*.</span><span class="sxs-lookup"><span data-stu-id="253ab-168">When not using Visual Studio, manually add a launch profile to the [launchSettings.json](https://json.schemastore.org/launchsettings) file in the *Properties* folder.</span></span> <span data-ttu-id="253ab-169">El siguiente ejemplo configura el perfil para usar el protocolo HTTPS:</span><span class="sxs-lookup"><span data-stu-id="253ab-169">The following example configures the profile to use the HTTPS protocol:</span></span>

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iis": {
      "applicationUrl": "https://localhost/WebApplication1",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS": {
      "commandName": "IIS",
      "launchBrowser": true,
      "launchUrl": "https://localhost/WebApplication1",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    }
  }
}
```

<span data-ttu-id="253ab-170">Confirme que los puntos de conexión `applicationUrl` y `launchUrl` coinciden y use el mismo protocolo que la configuración de enlace de IIS, es decir, HTTP o HTTPS.</span><span class="sxs-lookup"><span data-stu-id="253ab-170">Confirm that the `applicationUrl` and `launchUrl` endpoints match and use the same protocol as the IIS binding configuration, either HTTP or HTTPS.</span></span>

## <a name="run-the-project"></a><span data-ttu-id="253ab-171">Ejecución del proyecto</span><span class="sxs-lookup"><span data-stu-id="253ab-171">Run the project</span></span>

<span data-ttu-id="253ab-172">Ejecute Visual Studio como administrador:</span><span class="sxs-lookup"><span data-stu-id="253ab-172">Run Visual Studio as an administrator:</span></span>

* <span data-ttu-id="253ab-173">Confirme que la lista desplegable de configuración de compilación está configurada como **Depurar**.</span><span class="sxs-lookup"><span data-stu-id="253ab-173">Confirm that the build configuration drop-down list is set to **Debug**.</span></span>
* <span data-ttu-id="253ab-174">Establezca el botón [Iniciar depuración](/visualstudio/debugger/debugger-feature-tour) en el perfil de **IIS** y seleccione el botón para iniciar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="253ab-174">Set the [Start Debugging button](/visualstudio/debugger/debugger-feature-tour) to the **IIS** profile and select the button to start the app.</span></span>

<span data-ttu-id="253ab-175">Visual Studio puede solicitar un reinicio si no se ejecuta como administrador.</span><span class="sxs-lookup"><span data-stu-id="253ab-175">Visual Studio may prompt a restart if not running as an administrator.</span></span> <span data-ttu-id="253ab-176">Si es así, reinicie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="253ab-176">If prompted, restart Visual Studio.</span></span>

<span data-ttu-id="253ab-177">Si se usa un certificado de desarrollo que no es de confianza, el explorador puede pedirle que cree una excepción para un certificado de esta clase.</span><span class="sxs-lookup"><span data-stu-id="253ab-177">If an untrusted development certificate is used, the browser may require you to create an exception for the untrusted certificate.</span></span>

> [!NOTE]
> <span data-ttu-id="253ab-178">La depuración de una configuración de compilación de versión con [Solo mi código](/visualstudio/debugger/just-my-code) y las optimizaciones del compilador degradan el rendimiento.</span><span class="sxs-lookup"><span data-stu-id="253ab-178">Debugging a Release build configuration with [Just My Code](/visualstudio/debugger/just-my-code) and compiler optimizations results in a degraded experience.</span></span> <span data-ttu-id="253ab-179">Por ejemplo, no se alcanzan los puntos de interrupción.</span><span class="sxs-lookup"><span data-stu-id="253ab-179">For example, break points aren't hit.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="253ab-180">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="253ab-180">Additional resources</span></span>

* [<span data-ttu-id="253ab-181">Introducción al Administrador de IIS en IIS</span><span class="sxs-lookup"><span data-stu-id="253ab-181">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* <xref:security/enforcing-ssl>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="253ab-182">En este artículo se describe la compatibilidad de [Visual Studio](https://visualstudio.microsoft.com) con la depuración de aplicaciones ASP.NET Core que se ejecutan con IIS en Windows Server.</span><span class="sxs-lookup"><span data-stu-id="253ab-182">This article describes [Visual Studio](https://visualstudio.microsoft.com) support for debugging ASP.NET Core apps running with IIS on Windows Server.</span></span> <span data-ttu-id="253ab-183">En este tema se explica cómo habilitar este escenario y configurar un proyecto.</span><span class="sxs-lookup"><span data-stu-id="253ab-183">This topic walks through enabling this scenario and setting up a project.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="253ab-184">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="253ab-184">Prerequisites</span></span>

* <span data-ttu-id="253ab-185">[Visual Studio para Windows](https://visualstudio.microsoft.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="253ab-185">[Visual Studio for Windows](https://visualstudio.microsoft.com/downloads/)</span></span>
* <span data-ttu-id="253ab-186">Carga de trabajo de **ASP.NET y desarrollo web**</span><span class="sxs-lookup"><span data-stu-id="253ab-186">**ASP.NET and web development** workload</span></span>
* <span data-ttu-id="253ab-187">Carga de trabajo **Desarrollo multiplataforma de .NET Core**</span><span class="sxs-lookup"><span data-stu-id="253ab-187">**.NET Core cross-platform development** workload</span></span>
* <span data-ttu-id="253ab-188">Certificado de seguridad X.509 (para compatibilidad con HTTPS)</span><span class="sxs-lookup"><span data-stu-id="253ab-188">X.509 security certificate (for HTTPS support)</span></span>

## <a name="enable-iis"></a><span data-ttu-id="253ab-189">Habilitar IIS</span><span class="sxs-lookup"><span data-stu-id="253ab-189">Enable IIS</span></span>

1. <span data-ttu-id="253ab-190">En Windows, navegue a **Panel de control** > **Programas** > **Programas y características** > **Activar o desactivar las características de Windows** (lado izquierdo de la pantalla).</span><span class="sxs-lookup"><span data-stu-id="253ab-190">In Windows, navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="253ab-191">Active la casilla **Internet Information Services**.</span><span class="sxs-lookup"><span data-stu-id="253ab-191">Select the **Internet Information Services** check box.</span></span> <span data-ttu-id="253ab-192">Seleccione **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="253ab-192">Select **OK**.</span></span>

<span data-ttu-id="253ab-193">La instalación de IIS puede requerir un reinicio del sistema.</span><span class="sxs-lookup"><span data-stu-id="253ab-193">The IIS installation may require a system restart.</span></span>

## <a name="configure-iis"></a><span data-ttu-id="253ab-194">Configurar IIS</span><span class="sxs-lookup"><span data-stu-id="253ab-194">Configure IIS</span></span>

<span data-ttu-id="253ab-195">IIS debe tener un sitio web configurado con lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="253ab-195">IIS must have a website configured with the following:</span></span>

* <span data-ttu-id="253ab-196">**Nombre de host**: por lo general, el **sitio web predeterminado** se usa con un **nombre de host** de `localhost`.</span><span class="sxs-lookup"><span data-stu-id="253ab-196">**Host name**: Typically, the **Default Web Site** is used with a **Host name** of `localhost`.</span></span> <span data-ttu-id="253ab-197">Sin embargo, sirve cualquier sitio web de IIS válido con un nombre de host único.</span><span class="sxs-lookup"><span data-stu-id="253ab-197">However, any valid IIS website with a unique host name works.</span></span>
* <span data-ttu-id="253ab-198">**Enlace de sitio**</span><span class="sxs-lookup"><span data-stu-id="253ab-198">**Site Binding**</span></span>
  * <span data-ttu-id="253ab-199">Para las aplicaciones que requieran HTTPS, cree un enlace al puerto 443 con un certificado.</span><span class="sxs-lookup"><span data-stu-id="253ab-199">For apps that require HTTPS, create a binding to port 443 with a certificate.</span></span> <span data-ttu-id="253ab-200">Por lo general, se usa el **certificado de desarrollo de IIS Express**, pero cualquier certificado válido sirve.</span><span class="sxs-lookup"><span data-stu-id="253ab-200">Typically, the **IIS Express Development Certificate** is used, but any valid certificate works.</span></span>
  * <span data-ttu-id="253ab-201">Para las aplicaciones que usan HTTP, confirme la existencia de un enlace al puerto 80 o cree un enlace a dicho puerto si se trata de un sitio nuevo.</span><span class="sxs-lookup"><span data-stu-id="253ab-201">For apps that use HTTP, confirm the existence of a binding to post 80 or create a binding to port 80 for a new site.</span></span>
  * <span data-ttu-id="253ab-202">Utilice un enlace único para HTTP o HTTPS.</span><span class="sxs-lookup"><span data-stu-id="253ab-202">Use a single binding for either HTTP or HTTPS.</span></span> <span data-ttu-id="253ab-203">**No se admite el enlace al mismo tiempo a los puertos HTTP y HTTPS.**</span><span class="sxs-lookup"><span data-stu-id="253ab-203">**Binding to both HTTP and HTTPS ports simultaneously isn't supported.**</span></span>

## <a name="enable-development-time-iis-support-in-visual-studio"></a><span data-ttu-id="253ab-204">Habilitación de la compatibilidad con IIS en tiempo de desarrollo en Visual Studio</span><span class="sxs-lookup"><span data-stu-id="253ab-204">Enable development-time IIS support in Visual Studio</span></span>

1. <span data-ttu-id="253ab-205">Inicie el instalador de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="253ab-205">Launch the Visual Studio installer.</span></span>
1. <span data-ttu-id="253ab-206">Seleccione **Modificar** para la instalación de Visual Studio que se pretende usar para la compatibilidad con IIS en tiempo de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="253ab-206">Select **Modify** for the Visual Studio installation that you plan to use for IIS development-time support.</span></span>
1. <span data-ttu-id="253ab-207">Para la carga de trabajo de **Desarrollo de ASP.NET y web** , busque e instale el componente **Compatibilidad con IIS en tiempo de desarrollo**.</span><span class="sxs-lookup"><span data-stu-id="253ab-207">For the **ASP.NET and web development** workload, locate and install the **Development time IIS support** component.</span></span>

   <span data-ttu-id="253ab-208">El componente se enumera en la sección **Opcional**, en **Compatibilidad con IIS en tiempo de desarrollo**, dentro del panel **Detalles de instalación** a la derecha de las cargas de trabajo.</span><span class="sxs-lookup"><span data-stu-id="253ab-208">The component is listed in the **Optional** section under **Development time IIS support** in the **Installation details** panel to the right of the workloads.</span></span> <span data-ttu-id="253ab-209">El componente instala el [módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module), que es un módulo de IIS nativo necesario para ejecutar aplicaciones de ASP.NET Core con IIS.</span><span class="sxs-lookup"><span data-stu-id="253ab-209">The component installs the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), which is a native IIS module required to run ASP.NET Core apps with IIS.</span></span>

## <a name="configure-the-project"></a><span data-ttu-id="253ab-210">Configuración del proyecto</span><span class="sxs-lookup"><span data-stu-id="253ab-210">Configure the project</span></span>

### <a name="https-redirection"></a><span data-ttu-id="253ab-211">Redireccionamiento de HTTPS</span><span class="sxs-lookup"><span data-stu-id="253ab-211">HTTPS redirection</span></span>

<span data-ttu-id="253ab-212">Para un nuevo proyecto que requiere HTTPS, seleccione la casilla **Configurar para HTTPS** en la ventana **Crear una aplicación web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="253ab-212">For a new project that requires HTTPS, select the check box to **Configure for HTTPS** in the **Create a new ASP.NET Core Web Application** window.</span></span> <span data-ttu-id="253ab-213">Al seleccionar la casilla, se agrega [redireccionamiento HTTPS y middleware HSTS](xref:security/enforcing-ssl) a la aplicación cuando esta se crea.</span><span class="sxs-lookup"><span data-stu-id="253ab-213">Selecting the check box adds [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) to the app when it's created.</span></span>

<span data-ttu-id="253ab-214">Para un proyecto existente que requiere HTTPS, use el redireccionamiento HTTPS y el middleware HSTS en `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="253ab-214">For an existing project that requires HTTPS, use HTTPS Redirection and HSTS Middleware in `Startup.Configure`.</span></span> <span data-ttu-id="253ab-215">Para obtener más información, vea <xref:security/enforcing-ssl>.</span><span class="sxs-lookup"><span data-stu-id="253ab-215">For more information, see <xref:security/enforcing-ssl>.</span></span>

<span data-ttu-id="253ab-216">Para un proyecto que usa HTTP, el [redireccionamiento HTTPS y el middleware HSTS](xref:security/enforcing-ssl) no se agregan a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="253ab-216">For a project that uses HTTP, [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) aren't added to the app.</span></span> <span data-ttu-id="253ab-217">No se requiere ninguna configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="253ab-217">No app configuration is required.</span></span>

### <a name="iis-launch-profile"></a><span data-ttu-id="253ab-218">Perfil de inicio de IIS</span><span class="sxs-lookup"><span data-stu-id="253ab-218">IIS launch profile</span></span>

<span data-ttu-id="253ab-219">Cree un nuevo perfil de inicio para agregar la compatibilidad con IIS en tiempo de desarrollo:</span><span class="sxs-lookup"><span data-stu-id="253ab-219">Create a new launch profile to add development-time IIS support:</span></span>

1. <span data-ttu-id="253ab-220">Haga clic con el botón derecho en el **Explorador de soluciones**.</span><span class="sxs-lookup"><span data-stu-id="253ab-220">Right-click the project in **Solution Explorer**.</span></span> <span data-ttu-id="253ab-221">Haga clic en **Propiedades**.</span><span class="sxs-lookup"><span data-stu-id="253ab-221">Select **Properties**.</span></span> <span data-ttu-id="253ab-222">Abra la pestaña **Depurar**.</span><span class="sxs-lookup"><span data-stu-id="253ab-222">Open the **Debug** tab.</span></span>
1. <span data-ttu-id="253ab-223">En **Perfil**, seleccione el botón **Nuevo**.</span><span class="sxs-lookup"><span data-stu-id="253ab-223">For **Profile**, select the **New** button.</span></span> <span data-ttu-id="253ab-224">Asigne el perfil el nombre "IIS" en la ventana emergente.</span><span class="sxs-lookup"><span data-stu-id="253ab-224">Name the profile "IIS" in the popup window.</span></span> <span data-ttu-id="253ab-225">Seleccione **Aceptar** para crear el perfil.</span><span class="sxs-lookup"><span data-stu-id="253ab-225">Select **OK** to create the profile.</span></span>
1. <span data-ttu-id="253ab-226">En **Iniciar**, seleccione **IIS** en la lista.</span><span class="sxs-lookup"><span data-stu-id="253ab-226">For the **Launch** setting, select **IIS** from the list.</span></span>
1. <span data-ttu-id="253ab-227">Active la casilla **Iniciar explorador** y proporcione la dirección URL del punto de conexión.</span><span class="sxs-lookup"><span data-stu-id="253ab-227">Select the check box for **Launch browser** and provide the endpoint URL.</span></span>

   <span data-ttu-id="253ab-228">Si la aplicación requiere HTTPS, use un punto de conexión HTTPS (`https://`).</span><span class="sxs-lookup"><span data-stu-id="253ab-228">When the app requires HTTPS, use an HTTPS endpoint (`https://`).</span></span> <span data-ttu-id="253ab-229">Para HTTP, use un punto de conexión HTTP (`http://`).</span><span class="sxs-lookup"><span data-stu-id="253ab-229">For HTTP, use an HTTP (`http://`) endpoint.</span></span>

   <span data-ttu-id="253ab-230">Proporcione el mismo nombre de host y puerto especificados en la [configuración de IIS establecida en usos anteriores](#configure-iis), normalmente `localhost`.</span><span class="sxs-lookup"><span data-stu-id="253ab-230">Provide the same host name and port as the [IIS configuration specified earlier uses](#configure-iis), typically `localhost`.</span></span>

   <span data-ttu-id="253ab-231">Proporcione el nombre de la aplicación al final de la dirección URL.</span><span class="sxs-lookup"><span data-stu-id="253ab-231">Provide the name of the app at the end of the URL.</span></span>

   <span data-ttu-id="253ab-232">Por ejemplo, `https://localhost/WebApplication1` (HTTPS) o `http://localhost/WebApplication1` (HTTP) son direcciones URL de punto de conexión válidas.</span><span class="sxs-lookup"><span data-stu-id="253ab-232">For example, `https://localhost/WebApplication1` (HTTPS) or `http://localhost/WebApplication1` (HTTP) are valid endpoint URLs.</span></span>
1. <span data-ttu-id="253ab-233">En la sección **Variables de entorno**, seleccione el botón **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="253ab-233">In the **Environment variables** section, select the **Add** button.</span></span> <span data-ttu-id="253ab-234">Proporcione una variable de entorno con un **Nombre** de `ASPNETCORE_ENVIRONMENT` y un **Valor** de `Development`.</span><span class="sxs-lookup"><span data-stu-id="253ab-234">Provide an environment variable with a **Name** of `ASPNETCORE_ENVIRONMENT` and a **Value** of `Development`.</span></span>
1. <span data-ttu-id="253ab-235">En el área **Configuración del servidor web**, defina la **Dirección URL de la aplicación** con el mismo valor utilizado para la dirección URL de punto de conexión de **Iniciar el explorador**.</span><span class="sxs-lookup"><span data-stu-id="253ab-235">In the **Web Server Settings** area, set the **App URL** to the same value used for the **Launch browser** endpoint URL.</span></span>
1. <span data-ttu-id="253ab-236">Para la configuración del **Modelo de hospedaje** de Visual Studio 2019 o posterior, seleccione **Predeterminado** para usar el modelo de hospedaje utilizado por el proyecto.</span><span class="sxs-lookup"><span data-stu-id="253ab-236">For the **Hosting Model** setting in Visual Studio 2019 or later, select **Default** to use the hosting model used by the project.</span></span> <span data-ttu-id="253ab-237">Si el proyecto establece la propiedad `<AspNetCoreHostingModel>` en su archivo del proyecto, se usa el valor de la propiedad (`InProcess` o `OutOfProcess`).</span><span class="sxs-lookup"><span data-stu-id="253ab-237">If the project sets the `<AspNetCoreHostingModel>` property in its project file, the value of the property (`InProcess` or `OutOfProcess`) is used.</span></span> <span data-ttu-id="253ab-238">Si la propiedad no existe, se usa el modelo de hospedaje predeterminado de la aplicación, que está fuera de proceso.</span><span class="sxs-lookup"><span data-stu-id="253ab-238">If the property isn't present, the default hosting model of the app is used, which is out-of-process.</span></span> <span data-ttu-id="253ab-239">Si la aplicación requiere una configuración del modelo de hospedaje distinta a la del modelo de hospedaje habitual de la aplicación, defina el **Modelo de hospedaje** como `In Process` o `Out Of Process`, según proceda.</span><span class="sxs-lookup"><span data-stu-id="253ab-239">If the app requires an explicit hosting model setting different from the app's normal hosting model, set the **Hosting Model** to either `In Process` or `Out Of Process` as needed.</span></span>
1. <span data-ttu-id="253ab-240">Guarde el perfil.</span><span class="sxs-lookup"><span data-stu-id="253ab-240">Save the profile.</span></span>

<span data-ttu-id="253ab-241">Si no se usa Visual Studio, agregue manualmente un perfil de inicio al archivo [launchSettings.json](https://json.schemastore.org/launchsettings) en la carpeta *Propiedades*.</span><span class="sxs-lookup"><span data-stu-id="253ab-241">When not using Visual Studio, manually add a launch profile to the [launchSettings.json](https://json.schemastore.org/launchsettings) file in the *Properties* folder.</span></span> <span data-ttu-id="253ab-242">El siguiente ejemplo configura el perfil para usar el protocolo HTTPS:</span><span class="sxs-lookup"><span data-stu-id="253ab-242">The following example configures the profile to use the HTTPS protocol:</span></span>

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iis": {
      "applicationUrl": "https://localhost/WebApplication1",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS": {
      "commandName": "IIS",
      "launchBrowser": true,
      "launchUrl": "https://localhost/WebApplication1",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    }
  }
}
```

<span data-ttu-id="253ab-243">Confirme que los puntos de conexión `applicationUrl` y `launchUrl` coinciden y use el mismo protocolo que la configuración de enlace de IIS, es decir, HTTP o HTTPS.</span><span class="sxs-lookup"><span data-stu-id="253ab-243">Confirm that the `applicationUrl` and `launchUrl` endpoints match and use the same protocol as the IIS binding configuration, either HTTP or HTTPS.</span></span>

## <a name="run-the-project"></a><span data-ttu-id="253ab-244">Ejecución del proyecto</span><span class="sxs-lookup"><span data-stu-id="253ab-244">Run the project</span></span>

<span data-ttu-id="253ab-245">Ejecute Visual Studio como administrador:</span><span class="sxs-lookup"><span data-stu-id="253ab-245">Run Visual Studio as an administrator:</span></span>

* <span data-ttu-id="253ab-246">Confirme que la lista desplegable de configuración de compilación está configurada como **Depurar**.</span><span class="sxs-lookup"><span data-stu-id="253ab-246">Confirm that the build configuration drop-down list is set to **Debug**.</span></span>
* <span data-ttu-id="253ab-247">Establezca el botón [Iniciar depuración](/visualstudio/debugger/debugger-feature-tour) en el perfil de **IIS** y seleccione el botón para iniciar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="253ab-247">Set the [Start Debugging button](/visualstudio/debugger/debugger-feature-tour) to the **IIS** profile and select the button to start the app.</span></span>

<span data-ttu-id="253ab-248">Visual Studio puede solicitar un reinicio si no se ejecuta como administrador.</span><span class="sxs-lookup"><span data-stu-id="253ab-248">Visual Studio may prompt a restart if not running as an administrator.</span></span> <span data-ttu-id="253ab-249">Si es así, reinicie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="253ab-249">If prompted, restart Visual Studio.</span></span>

<span data-ttu-id="253ab-250">Si se usa un certificado de desarrollo que no es de confianza, el explorador puede pedirle que cree una excepción para un certificado de esta clase.</span><span class="sxs-lookup"><span data-stu-id="253ab-250">If an untrusted development certificate is used, the browser may require you to create an exception for the untrusted certificate.</span></span>

> [!NOTE]
> <span data-ttu-id="253ab-251">La depuración de una configuración de compilación de versión con [Solo mi código](/visualstudio/debugger/just-my-code) y las optimizaciones del compilador degradan el rendimiento.</span><span class="sxs-lookup"><span data-stu-id="253ab-251">Debugging a Release build configuration with [Just My Code](/visualstudio/debugger/just-my-code) and compiler optimizations results in a degraded experience.</span></span> <span data-ttu-id="253ab-252">Por ejemplo, no se alcanzan los puntos de interrupción.</span><span class="sxs-lookup"><span data-stu-id="253ab-252">For example, break points aren't hit.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="253ab-253">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="253ab-253">Additional resources</span></span>

* [<span data-ttu-id="253ab-254">Introducción al Administrador de IIS en IIS</span><span class="sxs-lookup"><span data-stu-id="253ab-254">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* <xref:security/enforcing-ssl>

::: moniker-end
