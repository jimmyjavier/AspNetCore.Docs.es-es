---
<span data-ttu-id="3467f-101">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3467f-101">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="3467f-102">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="3467f-102">'Blazor'</span></span>
- <span data-ttu-id="3467f-103">"Identity"</span><span class="sxs-lookup"><span data-stu-id="3467f-103">'Identity'</span></span>
- <span data-ttu-id="3467f-104">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="3467f-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="3467f-105">"Razor"</span><span class="sxs-lookup"><span data-stu-id="3467f-105">'Razor'</span></span>
- <span data-ttu-id="3467f-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="3467f-106">'SignalR' uid:</span></span> 

---
# <a name="detect-changes-with-change-tokens-in-aspnet-core"></a><span data-ttu-id="3467f-107">Detección de cambios con tokens de cambio en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3467f-107">Detect changes with change tokens in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3467f-108">Un *token de cambio* es un bloque de creación de bajo nivel y uso general que se usa para realizar el seguimiento de los cambios de estado.</span><span class="sxs-lookup"><span data-stu-id="3467f-108">A *change token* is a general-purpose, low-level building block used to track state changes.</span></span>

<span data-ttu-id="3467f-109">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3467f-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ichangetoken-interface"></a><span data-ttu-id="3467f-110">Interfaz IChangeToken</span><span class="sxs-lookup"><span data-stu-id="3467f-110">IChangeToken interface</span></span>

<span data-ttu-id="3467f-111"><xref:Microsoft.Extensions.Primitives.IChangeToken> propaga notificaciones que indican que se ha producido un cambio.</span><span class="sxs-lookup"><span data-stu-id="3467f-111"><xref:Microsoft.Extensions.Primitives.IChangeToken> propagates notifications that a change has occurred.</span></span> <span data-ttu-id="3467f-112">`IChangeToken` reside en el espacio de nombres <xref:Microsoft.Extensions.Primitives?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="3467f-112">`IChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="3467f-113">El paquete de NuGet [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) se proporciona implícitamente con las aplicaciones de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3467f-113">The [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package is implicitly provided to the ASP.NET Core apps.</span></span>

<span data-ttu-id="3467f-114">`IChangeToken` tiene dos propiedades:</span><span class="sxs-lookup"><span data-stu-id="3467f-114">`IChangeToken` has two properties:</span></span>

* <span data-ttu-id="3467f-115"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indica si el token genera devoluciones de llamada de manera proactiva.</span><span class="sxs-lookup"><span data-stu-id="3467f-115"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indicate if the token proactively raises callbacks.</span></span> <span data-ttu-id="3467f-116">Si `ActiveChangedCallbacks` se establece en `false`, nunca se llama a una devolución de llamada y la aplicación debe sondear `HasChanged` en busca de cambios.</span><span class="sxs-lookup"><span data-stu-id="3467f-116">If `ActiveChangedCallbacks` is set to `false`, a callback is never called, and the app must poll `HasChanged` for changes.</span></span> <span data-ttu-id="3467f-117">También es posible que un token nunca se cancele si no se producen cambios o si se elimina o deshabilita el agente de escucha de cambios subyacente.</span><span class="sxs-lookup"><span data-stu-id="3467f-117">It's also possible for a token to never be cancelled if no changes occur or the underlying change listener is disposed or disabled.</span></span>
* <span data-ttu-id="3467f-118"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> recibe un valor que indica si se ha producido un cambio.</span><span class="sxs-lookup"><span data-stu-id="3467f-118"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> receives a value that indicates if a change has occurred.</span></span>

<span data-ttu-id="3467f-119">La interfaz `IChangeToken` incluye el método [RegisterChangeCallback(Acción\<Object>, Objeto)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*), que registra una devolución de llamada que se invoca cuando el token ha cambiado.</span><span class="sxs-lookup"><span data-stu-id="3467f-119">The `IChangeToken` interface includes the [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) method, which registers a callback that's invoked when the token has changed.</span></span> <span data-ttu-id="3467f-120">`HasChanged` se debe establecer antes de que se invoque la devolución de llamada.</span><span class="sxs-lookup"><span data-stu-id="3467f-120">`HasChanged` must be set before the callback is invoked.</span></span>

## <a name="changetoken-class"></a><span data-ttu-id="3467f-121">Clase ChangeToken</span><span class="sxs-lookup"><span data-stu-id="3467f-121">ChangeToken class</span></span>

<span data-ttu-id="3467f-122"><xref:Microsoft.Extensions.Primitives.ChangeToken> es una clase estática que se usa para propagar notificaciones que indican que se ha producido un cambio.</span><span class="sxs-lookup"><span data-stu-id="3467f-122"><xref:Microsoft.Extensions.Primitives.ChangeToken> is a static class used to propagate notifications that a change has occurred.</span></span> <span data-ttu-id="3467f-123">`ChangeToken` reside en el espacio de nombres <xref:Microsoft.Extensions.Primitives?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="3467f-123">`ChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="3467f-124">El paquete de NuGet [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) se proporciona implícitamente con las aplicaciones de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3467f-124">The [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package is implicitly provided to the ASP.NET Core apps.</span></span>

<span data-ttu-id="3467f-125">El método [ChangeToken.OnChange(Función\<IChangeToken>, Acción)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) registra una `Action` que se llama cada vez que cambia el token:</span><span class="sxs-lookup"><span data-stu-id="3467f-125">The [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) method registers an `Action` to call whenever the token changes:</span></span>

* <span data-ttu-id="3467f-126">`Func<IChangeToken>` genera el token.</span><span class="sxs-lookup"><span data-stu-id="3467f-126">`Func<IChangeToken>` produces the token.</span></span>
* <span data-ttu-id="3467f-127">Se llama a `Action` cuando cambia el token.</span><span class="sxs-lookup"><span data-stu-id="3467f-127">`Action` is called when the token changes.</span></span>

<span data-ttu-id="3467f-128">La sobrecarga [ChangeToken.OnChange\<TState>(Función\<IChangeToken>, Acción\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) toma un parámetro `TState` adicional que se pasa a la `Action` de consumidor de token.</span><span class="sxs-lookup"><span data-stu-id="3467f-128">The [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) overload takes an additional `TState` parameter that's passed into the token consumer `Action`.</span></span>

<span data-ttu-id="3467f-129">`OnChange` devuelve un valor de <xref:System.IDisposable>.</span><span class="sxs-lookup"><span data-stu-id="3467f-129">`OnChange` returns an <xref:System.IDisposable>.</span></span> <span data-ttu-id="3467f-130">Al llamar a <xref:System.IDisposable.Dispose*> se detiene la escucha del token de futuras modificaciones y se liberan sus recursos.</span><span class="sxs-lookup"><span data-stu-id="3467f-130">Calling <xref:System.IDisposable.Dispose*> stops the token from listening for further changes and releases the token's resources.</span></span>

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a><span data-ttu-id="3467f-131">Ejemplos de uso de tokens de cambio en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3467f-131">Example uses of change tokens in ASP.NET Core</span></span>

<span data-ttu-id="3467f-132">Los tokens de cambio se usan en áreas principales de ASP.NET Core para supervisar los cambios en los objetos:</span><span class="sxs-lookup"><span data-stu-id="3467f-132">Change tokens are used in prominent areas of ASP.NET Core to monitor for changes to objects:</span></span>

* <span data-ttu-id="3467f-133">Para supervisar los cambios en los archivos, el método <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> de <xref:Microsoft.Extensions.FileProviders.IFileProvider> crea un `IChangeToken` para los archivos especificados o la carpeta que se va a supervisar.</span><span class="sxs-lookup"><span data-stu-id="3467f-133">For monitoring changes to files, <xref:Microsoft.Extensions.FileProviders.IFileProvider>'s <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> method creates an `IChangeToken` for the specified files or folder to watch.</span></span>
* <span data-ttu-id="3467f-134">Se pueden agregar tokens `IChangeToken` a las entradas de caché para desencadenar expulsiones de caché al producirse un cambio.</span><span class="sxs-lookup"><span data-stu-id="3467f-134">`IChangeToken` tokens can be added to cache entries to trigger cache evictions on change.</span></span>
* <span data-ttu-id="3467f-135">Para los cambios de `TOptions`, la implementación predeterminada <xref:Microsoft.Extensions.Options.OptionsMonitor`1> de <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> tiene una sobrecarga que acepta una o varias instancias de <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1>.</span><span class="sxs-lookup"><span data-stu-id="3467f-135">For `TOptions` changes, the default <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementation of <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> has an overload that accepts one or more <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> instances.</span></span> <span data-ttu-id="3467f-136">Cada instancia devuelve un `IChangeToken` para registrar una devolución de llamada de notificación de cambio a fin de realizar el seguimiento de los cambios en las opciones.</span><span class="sxs-lookup"><span data-stu-id="3467f-136">Each instance returns an `IChangeToken` to register a change notification callback for tracking options changes.</span></span>

## <a name="monitor-for-configuration-changes"></a><span data-ttu-id="3467f-137">Supervisión de los cambios de configuración</span><span class="sxs-lookup"><span data-stu-id="3467f-137">Monitor for configuration changes</span></span>

<span data-ttu-id="3467f-138">De forma predeterminada, las plantillas de ASP.NET Core usan [archivos de configuración de JSON](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json* y *appsettings.Production.json*) para cargar parámetros de configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="3467f-138">By default, ASP.NET Core templates use [JSON configuration files](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json*, and *appsettings.Production.json*) to load app configuration settings.</span></span>

<span data-ttu-id="3467f-139">Estos archivos se configuran mediante el método de extensión [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) en <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> que acepta un parámetro `reloadOnChange`.</span><span class="sxs-lookup"><span data-stu-id="3467f-139">These files are configured using the [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) extension method on <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> that accepts a `reloadOnChange` parameter.</span></span> <span data-ttu-id="3467f-140">`reloadOnChange` indica si la configuración se debe recargar en los cambios de archivo.</span><span class="sxs-lookup"><span data-stu-id="3467f-140">`reloadOnChange` indicates if configuration should be reloaded on file changes.</span></span> <span data-ttu-id="3467f-141">Esta configuración aparece en el método <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> de <xref:Microsoft.Extensions.Hosting.Host>:</span><span class="sxs-lookup"><span data-stu-id="3467f-141">This setting appears in the <xref:Microsoft.Extensions.Hosting.Host> convenience method <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>:</span></span>

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

<span data-ttu-id="3467f-142">La configuración basada en archivo se presenta por medio de <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="3467f-142">File-based configuration is represented by <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span></span> <span data-ttu-id="3467f-143">`FileConfigurationSource` use <xref:Microsoft.Extensions.FileProviders.IFileProvider> para supervisar los archivos.</span><span class="sxs-lookup"><span data-stu-id="3467f-143">`FileConfigurationSource` uses <xref:Microsoft.Extensions.FileProviders.IFileProvider> to monitor files.</span></span>

<span data-ttu-id="3467f-144"><xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> proporciona `IFileMonitor` de manera predeterminada, que usa <xref:System.IO.FileSystemWatcher> para supervisar los cambios del archivo de configuración.</span><span class="sxs-lookup"><span data-stu-id="3467f-144">By default, the `IFileMonitor` is provided by a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, which uses <xref:System.IO.FileSystemWatcher> to monitor for configuration file changes.</span></span>

<span data-ttu-id="3467f-145">En la aplicación de ejemplo se muestran dos implementaciones para supervisar los cambios de configuración.</span><span class="sxs-lookup"><span data-stu-id="3467f-145">The sample app demonstrates two implementations for monitoring configuration changes.</span></span> <span data-ttu-id="3467f-146">Si se modifica cualquiera de los archivos *appsettings*, ambas implementaciones de supervisión de los archivos ejecutan un código personalizado&mdash;la aplicación de ejemplo escribe un mensaje en la consola.</span><span class="sxs-lookup"><span data-stu-id="3467f-146">If any of the *appsettings* files change, both of the file monitoring implementations execute custom code&mdash;the sample app writes a message to the console.</span></span>

<span data-ttu-id="3467f-147">El `FileSystemWatcher` de un archivo de configuración puede desencadenar varias devoluciones de llamada de token para un único cambio del archivo de configuración.</span><span class="sxs-lookup"><span data-stu-id="3467f-147">A configuration file's `FileSystemWatcher` can trigger multiple token callbacks for a single configuration file change.</span></span> <span data-ttu-id="3467f-148">Para garantizar que el código personalizado se ejecute solo una vez cuando se desencadenan varias devoluciones de llamada de token, la implementación del ejemplo comprueba los hashes de archivo.</span><span class="sxs-lookup"><span data-stu-id="3467f-148">To ensure that the custom code is only run once when multiple token callbacks are triggered, the sample's implementation checks file hashes.</span></span> <span data-ttu-id="3467f-149">El ejemplo usa el algoritmo hash seguro 1.</span><span class="sxs-lookup"><span data-stu-id="3467f-149">The sample uses SHA1 file hashing.</span></span> <span data-ttu-id="3467f-150">Se implementa un reintento con una interrupción exponencial.</span><span class="sxs-lookup"><span data-stu-id="3467f-150">A retry is implemented with an exponential back-off.</span></span> <span data-ttu-id="3467f-151">El reintento aparece porque se puede producir un bloqueo de archivos que impida temporalmente calcular un hash nuevo en un archivo.</span><span class="sxs-lookup"><span data-stu-id="3467f-151">The retry is present because file locking may occur that temporarily prevents computing a new hash on a file.</span></span>

<span data-ttu-id="3467f-152">*Utilities/Utilities.cs*:</span><span class="sxs-lookup"><span data-stu-id="3467f-152">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a><span data-ttu-id="3467f-153">Token de cambio de inicio simple</span><span class="sxs-lookup"><span data-stu-id="3467f-153">Simple startup change token</span></span>

<span data-ttu-id="3467f-154">Registre una devolución de llamada de `Action` del consumidor de token para las notificaciones de cambio en el token de recarga de configuración.</span><span class="sxs-lookup"><span data-stu-id="3467f-154">Register a token consumer `Action` callback for change notifications to the configuration reload token.</span></span>

<span data-ttu-id="3467f-155">En `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="3467f-155">In `Startup.Configure`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="3467f-156">`config.GetReloadToken()` proporciona el token.</span><span class="sxs-lookup"><span data-stu-id="3467f-156">`config.GetReloadToken()` provides the token.</span></span> <span data-ttu-id="3467f-157">La devolución de llamada es el método `InvokeChanged`:</span><span class="sxs-lookup"><span data-stu-id="3467f-157">The callback is the `InvokeChanged` method:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="3467f-158">El `state` de la devolución de llamada se usa para pasar el `IWebHostEnvironment`, que resulta útil para especificar el archivo de configuración *appsettings* que se va a supervisar (por ejemplo, *appsettings.Development.json* cuando se está en el entorno de desarrollo).</span><span class="sxs-lookup"><span data-stu-id="3467f-158">The `state` of the callback is used to pass in the `IWebHostEnvironment`, which is useful for specifying the correct *appsettings* configuration file to monitor (for example, *appsettings.Development.json* when in the Development environment).</span></span> <span data-ttu-id="3467f-159">Se usa el hash de archivo para evitar que se ejecute varias veces la instrucción `WriteConsole` debido a varias devoluciones de llamada de token cuando el archivo de configuración solo ha cambiado una vez.</span><span class="sxs-lookup"><span data-stu-id="3467f-159">File hashes are used to prevent the `WriteConsole` statement from running multiple times due to multiple token callbacks when the configuration file has only changed once.</span></span>

<span data-ttu-id="3467f-160">Este sistema se ejecuta siempre que la aplicación esté en ejecución y el usuario no lo puede deshabilitar.</span><span class="sxs-lookup"><span data-stu-id="3467f-160">This system runs as long as the app is running and can't be disabled by the user.</span></span>

### <a name="monitor-configuration-changes-as-a-service"></a><span data-ttu-id="3467f-161">Supervisión de los cambios de configuración como servicio</span><span class="sxs-lookup"><span data-stu-id="3467f-161">Monitor configuration changes as a service</span></span>

<span data-ttu-id="3467f-162">En el ejemplo se implementa lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="3467f-162">The sample implements:</span></span>

* <span data-ttu-id="3467f-163">La supervisión del token de inicio básico.</span><span class="sxs-lookup"><span data-stu-id="3467f-163">Basic startup token monitoring.</span></span>
* <span data-ttu-id="3467f-164">La supervisión como servicio.</span><span class="sxs-lookup"><span data-stu-id="3467f-164">Monitoring as a service.</span></span>
* <span data-ttu-id="3467f-165">Un mecanismo para habilitar y deshabilitar la supervisión.</span><span class="sxs-lookup"><span data-stu-id="3467f-165">A mechanism to enable and disable monitoring.</span></span>

<span data-ttu-id="3467f-166">El ejemplo establece una interfaz `IConfigurationMonitor`.</span><span class="sxs-lookup"><span data-stu-id="3467f-166">The sample establishes an `IConfigurationMonitor` interface.</span></span>

<span data-ttu-id="3467f-167">*Extensions/ConfigurationMonitor.cs*:</span><span class="sxs-lookup"><span data-stu-id="3467f-167">*Extensions/ConfigurationMonitor.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

<span data-ttu-id="3467f-168">El constructor de la clase implementada, `ConfigurationMonitor`, registra una devolución de llamada para las notificaciones de cambio:</span><span class="sxs-lookup"><span data-stu-id="3467f-168">The constructor of the implemented class, `ConfigurationMonitor`, registers a callback for change notifications:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

<span data-ttu-id="3467f-169">`config.GetReloadToken()` proporciona el token.</span><span class="sxs-lookup"><span data-stu-id="3467f-169">`config.GetReloadToken()` supplies the token.</span></span> <span data-ttu-id="3467f-170">`InvokeChanged` es el método de devolución de llamada.</span><span class="sxs-lookup"><span data-stu-id="3467f-170">`InvokeChanged` is the callback method.</span></span> <span data-ttu-id="3467f-171">El elemento `state` de esta instancia es una referencia a la instancia de `IConfigurationMonitor` que se usa para tener acceso al estado de supervisión.</span><span class="sxs-lookup"><span data-stu-id="3467f-171">The `state` in this instance is a reference to the `IConfigurationMonitor` instance that's used to access the monitoring state.</span></span> <span data-ttu-id="3467f-172">Se usan dos propiedades:</span><span class="sxs-lookup"><span data-stu-id="3467f-172">Two properties are used:</span></span>

* <span data-ttu-id="3467f-173">`MonitoringEnabled`: indica si la devolución de llamada debe ejecutar su código personalizado.</span><span class="sxs-lookup"><span data-stu-id="3467f-173">`MonitoringEnabled`: Indicates if the callback should run its custom code.</span></span>
* <span data-ttu-id="3467f-174">`CurrentState`: describe el estado de supervisión actual para su uso en la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="3467f-174">`CurrentState`: Describes the current monitoring state for use in the UI.</span></span>

<span data-ttu-id="3467f-175">El método `InvokeChanged` es similar al enfoque anterior, excepto en que:</span><span class="sxs-lookup"><span data-stu-id="3467f-175">The `InvokeChanged` method is similar to the earlier approach, except that it:</span></span>

* <span data-ttu-id="3467f-176">No ejecuta su código, a menos que `MonitoringEnabled` sea `true`.</span><span class="sxs-lookup"><span data-stu-id="3467f-176">Doesn't run its code unless `MonitoringEnabled` is `true`.</span></span>
* <span data-ttu-id="3467f-177">Genera el `state` actual en su salida de `WriteConsole`.</span><span class="sxs-lookup"><span data-stu-id="3467f-177">Outputs the current `state` in its `WriteConsole` output.</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

<span data-ttu-id="3467f-178">Una instancia de `ConfigurationMonitor` se registra como servicio en `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="3467f-178">An instance `ConfigurationMonitor` is registered as a service in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="3467f-179">En la página Index se ofrece al usuario el control sobre la supervisión de la configuración.</span><span class="sxs-lookup"><span data-stu-id="3467f-179">The Index page offers the user control over configuration monitoring.</span></span> <span data-ttu-id="3467f-180">La instancia de `IConfigurationMonitor` se inserta en `IndexModel`.</span><span class="sxs-lookup"><span data-stu-id="3467f-180">The instance of `IConfigurationMonitor` is injected into the `IndexModel`.</span></span>

<span data-ttu-id="3467f-181">*Páginas/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="3467f-181">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="3467f-182">El monitor de configuración (`_monitor`) se usa para habilitar o deshabilitar la supervisión y establecer el estado actual de los comentarios de la interfaz de usuario:</span><span class="sxs-lookup"><span data-stu-id="3467f-182">The configuration monitor (`_monitor`) is used to enable or disable monitoring and set the current state for UI feedback:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="3467f-183">Cuando se desencadena `OnPostStartMonitoring`, se habilita la supervisión y se borra el estado actual.</span><span class="sxs-lookup"><span data-stu-id="3467f-183">When `OnPostStartMonitoring` is triggered, monitoring is enabled, and the current state is cleared.</span></span> <span data-ttu-id="3467f-184">Cuando se desencadena `OnPostStopMonitoring`, se deshabilita la supervisión y se establece el estado para reflejar que no se está realizando la supervisión.</span><span class="sxs-lookup"><span data-stu-id="3467f-184">When `OnPostStopMonitoring` is triggered, monitoring is disabled, and the state is set to reflect that monitoring isn't occurring.</span></span>

<span data-ttu-id="3467f-185">Los botones de la interfaz de usuario habilitan y deshabilitan la supervisión.</span><span class="sxs-lookup"><span data-stu-id="3467f-185">Buttons in the UI enable and disable monitoring.</span></span>

<span data-ttu-id="3467f-186">*Páginas/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="3467f-186">*Pages/Index.cshtml*:</span></span>

[!code-cshtml[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a><span data-ttu-id="3467f-187">Supervisión de los cambios de archivos en caché</span><span class="sxs-lookup"><span data-stu-id="3467f-187">Monitor cached file changes</span></span>

<span data-ttu-id="3467f-188">El contenido de los archivos se puede almacenar en caché en memoria mediante <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span><span class="sxs-lookup"><span data-stu-id="3467f-188">File content can be cached in-memory using <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span></span> <span data-ttu-id="3467f-189">El almacenamiento en caché en memoria se describe en el tema [Cache in-memory](xref:performance/caching/memory) (Almacenamiento en caché en memoria).</span><span class="sxs-lookup"><span data-stu-id="3467f-189">In-memory caching is described in the [Cache in-memory](xref:performance/caching/memory) topic.</span></span> <span data-ttu-id="3467f-190">Sin realizar pasos adicionales, como la implementación que se describe a continuación, si los datos de origen cambian, se devuelven datos *obsoletos* (no actualizados) de la caché.</span><span class="sxs-lookup"><span data-stu-id="3467f-190">Without taking additional steps, such as the implementation described below, *stale* (outdated) data is returned from a cache if the source data changes.</span></span>

<span data-ttu-id="3467f-191">Por ejemplo, si no se tiene en cuenta el estado de un archivo de origen en caché cuando se renueva un período de [vencimiento variable](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration), se pueden crear datos de archivo en caché obsoletos.</span><span class="sxs-lookup"><span data-stu-id="3467f-191">For example, not taking into account the status of a cached source file when renewing a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period leads to stale cached file data.</span></span> <span data-ttu-id="3467f-192">En cada solicitud de los datos se renueva el período de vencimiento variable, pero el archivo nunca se vuelve a cargar en la caché.</span><span class="sxs-lookup"><span data-stu-id="3467f-192">Each request for the data renews the sliding expiration period, but the file is never reloaded into the cache.</span></span> <span data-ttu-id="3467f-193">Las características de la aplicación que usen el contenido en caché del archivo están sujetas a la posible recepción de contenido obsoleto.</span><span class="sxs-lookup"><span data-stu-id="3467f-193">Any app features that use the file's cached content are subject to possibly receiving stale content.</span></span>

<span data-ttu-id="3467f-194">El uso de tokens de cambio en un escenario de almacenamiento en caché de archivos evita la presencia de contenido de archivos obsoletos en la caché.</span><span class="sxs-lookup"><span data-stu-id="3467f-194">Using change tokens in a file caching scenario prevents the presence of stale file content in the cache.</span></span> <span data-ttu-id="3467f-195">En la aplicación de ejemplo se muestra una implementación del enfoque.</span><span class="sxs-lookup"><span data-stu-id="3467f-195">The sample app demonstrates an implementation of the approach.</span></span>

<span data-ttu-id="3467f-196">En el ejemplo se usa `GetFileContent` para:</span><span class="sxs-lookup"><span data-stu-id="3467f-196">The sample uses `GetFileContent` to:</span></span>

* <span data-ttu-id="3467f-197">Devolver el contenido del archivo.</span><span class="sxs-lookup"><span data-stu-id="3467f-197">Return file content.</span></span>
* <span data-ttu-id="3467f-198">Implementar un algoritmo de reintento con interrupción exponencial para casos en los que un bloqueo de archivo impide temporalmente leer un archivo.</span><span class="sxs-lookup"><span data-stu-id="3467f-198">Implement a retry algorithm with exponential back-off to cover cases where a file lock temporarily prevents reading a file.</span></span>

<span data-ttu-id="3467f-199">*Utilities/Utilities.cs*:</span><span class="sxs-lookup"><span data-stu-id="3467f-199">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

<span data-ttu-id="3467f-200">Se crea un `FileService` para administrar las búsquedas de archivos en caché.</span><span class="sxs-lookup"><span data-stu-id="3467f-200">A `FileService` is created to handle cached file lookups.</span></span> <span data-ttu-id="3467f-201">La llamada al método `GetFileContent` del servicio intenta obtener el contenido de archivo de la caché en memoria y devolverlo al autor de la llamada (*Services/FileService.cs*).</span><span class="sxs-lookup"><span data-stu-id="3467f-201">The `GetFileContent` method call of the service attempts to obtain file content from the in-memory cache and return it to the caller (*Services/FileService.cs*).</span></span>

<span data-ttu-id="3467f-202">Si el contenido en caché no se encuentra mediante la clave de caché, se realizan las acciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="3467f-202">If cached content isn't found using the cache key, the following actions are taken:</span></span>

1. <span data-ttu-id="3467f-203">El contenido del archivo se obtiene mediante `GetFileContent`.</span><span class="sxs-lookup"><span data-stu-id="3467f-203">The file content is obtained using `GetFileContent`.</span></span>
1. <span data-ttu-id="3467f-204">Se obtiene un token de cambio del proveedor de archivos con [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span><span class="sxs-lookup"><span data-stu-id="3467f-204">A change token is obtained from the file provider with [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span></span> <span data-ttu-id="3467f-205">La devolución de llamada del token se desencadena cuando se modifica el archivo.</span><span class="sxs-lookup"><span data-stu-id="3467f-205">The token's callback is triggered when the file is modified.</span></span>
1. <span data-ttu-id="3467f-206">El contenido del archivo se almacena en caché con un período de [vencimiento variable](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration).</span><span class="sxs-lookup"><span data-stu-id="3467f-206">The file content is cached with a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period.</span></span> <span data-ttu-id="3467f-207">El token de cambio se adjunta con [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) para expulsar la entrada de caché si el archivo cambia mientras está almacenado en caché.</span><span class="sxs-lookup"><span data-stu-id="3467f-207">The change token is attached with [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) to evict the cache entry if the file changes while it's cached.</span></span>

<span data-ttu-id="3467f-208">En el ejemplo siguiente, los archivos se almacenan en la [raíz del contenido](xref:fundamentals/index#content-root) de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="3467f-208">In the following example, files are stored in the app's [content root](xref:fundamentals/index#content-root).</span></span> <span data-ttu-id="3467f-209">`IWebHostEnvironment.ContentRootFileProvider` se usa para obtener un <xref:Microsoft.Extensions.FileProviders.IFileProvider> que apunte a `IWebHostEnvironment.ContentRootPath` de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="3467f-209">`IWebHostEnvironment.ContentRootFileProvider` is used to obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> pointing at the app's `IWebHostEnvironment.ContentRootPath`.</span></span> <span data-ttu-id="3467f-210">La `filePath` se obtiene con [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span><span class="sxs-lookup"><span data-stu-id="3467f-210">The `filePath` is obtained with [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Services/FileService.cs?name=snippet1)]

<span data-ttu-id="3467f-211">El `FileService` se registra en el contenedor de servicios junto con el servicio de almacenamiento en caché.</span><span class="sxs-lookup"><span data-stu-id="3467f-211">The `FileService` is registered in the service container along with the memory caching service.</span></span>

<span data-ttu-id="3467f-212">En `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="3467f-212">In `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="3467f-213">El modelo de página carga el contenido del archivo mediante el servicio.</span><span class="sxs-lookup"><span data-stu-id="3467f-213">The page model loads the file's content using the service.</span></span>

<span data-ttu-id="3467f-214">En el método `OnGet` de la página de índice (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="3467f-214">In the Index page's `OnGet` method (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a><span data-ttu-id="3467f-215">Clase CompositeChangeToken</span><span class="sxs-lookup"><span data-stu-id="3467f-215">CompositeChangeToken class</span></span>

<span data-ttu-id="3467f-216">Para representar una o varias instancias de `IChangeToken` en un solo objeto, use la clase <xref:Microsoft.Extensions.Primitives.CompositeChangeToken>.</span><span class="sxs-lookup"><span data-stu-id="3467f-216">For representing one or more `IChangeToken` instances in a single object, use the <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> class.</span></span>

```csharp
var firstCancellationTokenSource = new CancellationTokenSource();
var secondCancellationTokenSource = new CancellationTokenSource();

var firstCancellationToken = firstCancellationTokenSource.Token;
var secondCancellationToken = secondCancellationTokenSource.Token;

var firstCancellationChangeToken = new CancellationChangeToken(firstCancellationToken);
var secondCancellationChangeToken = new CancellationChangeToken(secondCancellationToken);

var compositeChangeToken = 
    new CompositeChangeToken(
        new List<IChangeToken> 
        {
            firstCancellationChangeToken, 
            secondCancellationChangeToken
        });
```

<span data-ttu-id="3467f-217">En el token compuesto, `HasChanged` notifica `true` si algún token representado `HasChanged` es `true`.</span><span class="sxs-lookup"><span data-stu-id="3467f-217">`HasChanged` on the composite token reports `true` if any represented token `HasChanged` is `true`.</span></span> <span data-ttu-id="3467f-218">En el token compuesto, `ActiveChangeCallbacks` notifica `true` si algún token representado `ActiveChangeCallbacks` es `true`.</span><span class="sxs-lookup"><span data-stu-id="3467f-218">`ActiveChangeCallbacks` on the composite token reports `true` if any represented token `ActiveChangeCallbacks` is `true`.</span></span> <span data-ttu-id="3467f-219">Si se producen varios eventos de cambio simultáneos, la devolución de llamada de cambio compuesto se invoca una vez.</span><span class="sxs-lookup"><span data-stu-id="3467f-219">If multiple concurrent change events occur, the composite change callback is invoked one time.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3467f-220">Un *token de cambio* es un bloque de creación de bajo nivel y uso general que se usa para realizar el seguimiento de los cambios de estado.</span><span class="sxs-lookup"><span data-stu-id="3467f-220">A *change token* is a general-purpose, low-level building block used to track state changes.</span></span>

<span data-ttu-id="3467f-221">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3467f-221">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ichangetoken-interface"></a><span data-ttu-id="3467f-222">Interfaz IChangeToken</span><span class="sxs-lookup"><span data-stu-id="3467f-222">IChangeToken interface</span></span>

<span data-ttu-id="3467f-223"><xref:Microsoft.Extensions.Primitives.IChangeToken> propaga notificaciones que indican que se ha producido un cambio.</span><span class="sxs-lookup"><span data-stu-id="3467f-223"><xref:Microsoft.Extensions.Primitives.IChangeToken> propagates notifications that a change has occurred.</span></span> <span data-ttu-id="3467f-224">`IChangeToken` reside en el espacio de nombres <xref:Microsoft.Extensions.Primitives?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="3467f-224">`IChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="3467f-225">En el caso de las aplicaciones que no usan el [metapaquete Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), cree una referencia al paquete NuGet [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/).</span><span class="sxs-lookup"><span data-stu-id="3467f-225">For apps that don't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference for the [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package.</span></span>

<span data-ttu-id="3467f-226">`IChangeToken` tiene dos propiedades:</span><span class="sxs-lookup"><span data-stu-id="3467f-226">`IChangeToken` has two properties:</span></span>

* <span data-ttu-id="3467f-227"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indica si el token genera devoluciones de llamada de manera proactiva.</span><span class="sxs-lookup"><span data-stu-id="3467f-227"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indicate if the token proactively raises callbacks.</span></span> <span data-ttu-id="3467f-228">Si `ActiveChangedCallbacks` se establece en `false`, nunca se llama a una devolución de llamada y la aplicación debe sondear `HasChanged` en busca de cambios.</span><span class="sxs-lookup"><span data-stu-id="3467f-228">If `ActiveChangedCallbacks` is set to `false`, a callback is never called, and the app must poll `HasChanged` for changes.</span></span> <span data-ttu-id="3467f-229">También es posible que un token nunca se cancele si no se producen cambios o si se elimina o deshabilita el agente de escucha de cambios subyacente.</span><span class="sxs-lookup"><span data-stu-id="3467f-229">It's also possible for a token to never be cancelled if no changes occur or the underlying change listener is disposed or disabled.</span></span>
* <span data-ttu-id="3467f-230"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> recibe un valor que indica si se ha producido un cambio.</span><span class="sxs-lookup"><span data-stu-id="3467f-230"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> receives a value that indicates if a change has occurred.</span></span>

<span data-ttu-id="3467f-231">La interfaz `IChangeToken` incluye el método [RegisterChangeCallback(Acción\<Object>, Objeto)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*), que registra una devolución de llamada que se invoca cuando el token ha cambiado.</span><span class="sxs-lookup"><span data-stu-id="3467f-231">The `IChangeToken` interface includes the [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) method, which registers a callback that's invoked when the token has changed.</span></span> <span data-ttu-id="3467f-232">`HasChanged` se debe establecer antes de que se invoque la devolución de llamada.</span><span class="sxs-lookup"><span data-stu-id="3467f-232">`HasChanged` must be set before the callback is invoked.</span></span>

## <a name="changetoken-class"></a><span data-ttu-id="3467f-233">Clase ChangeToken</span><span class="sxs-lookup"><span data-stu-id="3467f-233">ChangeToken class</span></span>

<span data-ttu-id="3467f-234"><xref:Microsoft.Extensions.Primitives.ChangeToken> es una clase estática que se usa para propagar notificaciones que indican que se ha producido un cambio.</span><span class="sxs-lookup"><span data-stu-id="3467f-234"><xref:Microsoft.Extensions.Primitives.ChangeToken> is a static class used to propagate notifications that a change has occurred.</span></span> <span data-ttu-id="3467f-235">`ChangeToken` reside en el espacio de nombres <xref:Microsoft.Extensions.Primitives?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="3467f-235">`ChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="3467f-236">En el caso de las aplicaciones que no usan el [metapaquete Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), cree una referencia al paquete NuGet [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/).</span><span class="sxs-lookup"><span data-stu-id="3467f-236">For apps that don't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference for the [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package.</span></span>

<span data-ttu-id="3467f-237">El método [ChangeToken.OnChange(Función\<IChangeToken>, Acción)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) registra una `Action` que se llama cada vez que cambia el token:</span><span class="sxs-lookup"><span data-stu-id="3467f-237">The [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) method registers an `Action` to call whenever the token changes:</span></span>

* <span data-ttu-id="3467f-238">`Func<IChangeToken>` genera el token.</span><span class="sxs-lookup"><span data-stu-id="3467f-238">`Func<IChangeToken>` produces the token.</span></span>
* <span data-ttu-id="3467f-239">Se llama a `Action` cuando cambia el token.</span><span class="sxs-lookup"><span data-stu-id="3467f-239">`Action` is called when the token changes.</span></span>

<span data-ttu-id="3467f-240">La sobrecarga [ChangeToken.OnChange\<TState>(Función\<IChangeToken>, Acción\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) toma un parámetro `TState` adicional que se pasa a la `Action` de consumidor de token.</span><span class="sxs-lookup"><span data-stu-id="3467f-240">The [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) overload takes an additional `TState` parameter that's passed into the token consumer `Action`.</span></span>

<span data-ttu-id="3467f-241">`OnChange` devuelve un valor de <xref:System.IDisposable>.</span><span class="sxs-lookup"><span data-stu-id="3467f-241">`OnChange` returns an <xref:System.IDisposable>.</span></span> <span data-ttu-id="3467f-242">Al llamar a <xref:System.IDisposable.Dispose*> se detiene la escucha del token de futuras modificaciones y se liberan sus recursos.</span><span class="sxs-lookup"><span data-stu-id="3467f-242">Calling <xref:System.IDisposable.Dispose*> stops the token from listening for further changes and releases the token's resources.</span></span>

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a><span data-ttu-id="3467f-243">Ejemplos de uso de tokens de cambio en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3467f-243">Example uses of change tokens in ASP.NET Core</span></span>

<span data-ttu-id="3467f-244">Los tokens de cambio se usan en áreas principales de ASP.NET Core para supervisar los cambios en los objetos:</span><span class="sxs-lookup"><span data-stu-id="3467f-244">Change tokens are used in prominent areas of ASP.NET Core to monitor for changes to objects:</span></span>

* <span data-ttu-id="3467f-245">Para supervisar los cambios en los archivos, el método <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> de <xref:Microsoft.Extensions.FileProviders.IFileProvider> crea un `IChangeToken` para los archivos especificados o la carpeta que se va a supervisar.</span><span class="sxs-lookup"><span data-stu-id="3467f-245">For monitoring changes to files, <xref:Microsoft.Extensions.FileProviders.IFileProvider>'s <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> method creates an `IChangeToken` for the specified files or folder to watch.</span></span>
* <span data-ttu-id="3467f-246">Se pueden agregar tokens `IChangeToken` a las entradas de caché para desencadenar expulsiones de caché al producirse un cambio.</span><span class="sxs-lookup"><span data-stu-id="3467f-246">`IChangeToken` tokens can be added to cache entries to trigger cache evictions on change.</span></span>
* <span data-ttu-id="3467f-247">Para los cambios de `TOptions`, la implementación predeterminada <xref:Microsoft.Extensions.Options.OptionsMonitor`1> de <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> tiene una sobrecarga que acepta una o varias instancias de <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1>.</span><span class="sxs-lookup"><span data-stu-id="3467f-247">For `TOptions` changes, the default <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementation of <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> has an overload that accepts one or more <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> instances.</span></span> <span data-ttu-id="3467f-248">Cada instancia devuelve un `IChangeToken` para registrar una devolución de llamada de notificación de cambio a fin de realizar el seguimiento de los cambios en las opciones.</span><span class="sxs-lookup"><span data-stu-id="3467f-248">Each instance returns an `IChangeToken` to register a change notification callback for tracking options changes.</span></span>

## <a name="monitor-for-configuration-changes"></a><span data-ttu-id="3467f-249">Supervisión de los cambios de configuración</span><span class="sxs-lookup"><span data-stu-id="3467f-249">Monitor for configuration changes</span></span>

<span data-ttu-id="3467f-250">De forma predeterminada, las plantillas de ASP.NET Core usan [archivos de configuración de JSON](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json* y *appsettings.Production.json*) para cargar parámetros de configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="3467f-250">By default, ASP.NET Core templates use [JSON configuration files](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json*, and *appsettings.Production.json*) to load app configuration settings.</span></span>

<span data-ttu-id="3467f-251">Estos archivos se configuran mediante el método de extensión [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) en <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> que acepta un parámetro `reloadOnChange`.</span><span class="sxs-lookup"><span data-stu-id="3467f-251">These files are configured using the [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) extension method on <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> that accepts a `reloadOnChange` parameter.</span></span> <span data-ttu-id="3467f-252">`reloadOnChange` indica si la configuración se debe recargar en los cambios de archivo.</span><span class="sxs-lookup"><span data-stu-id="3467f-252">`reloadOnChange` indicates if configuration should be reloaded on file changes.</span></span> <span data-ttu-id="3467f-253">Esta configuración aparece en el método <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> de <xref:Microsoft.AspNetCore.WebHost>:</span><span class="sxs-lookup"><span data-stu-id="3467f-253">This setting appears in the <xref:Microsoft.AspNetCore.WebHost> convenience method <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>:</span></span>

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

<span data-ttu-id="3467f-254">La configuración basada en archivo se presenta por medio de <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="3467f-254">File-based configuration is represented by <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span></span> <span data-ttu-id="3467f-255">`FileConfigurationSource` use <xref:Microsoft.Extensions.FileProviders.IFileProvider> para supervisar los archivos.</span><span class="sxs-lookup"><span data-stu-id="3467f-255">`FileConfigurationSource` uses <xref:Microsoft.Extensions.FileProviders.IFileProvider> to monitor files.</span></span>

<span data-ttu-id="3467f-256"><xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> proporciona `IFileMonitor` de manera predeterminada, que usa <xref:System.IO.FileSystemWatcher> para supervisar los cambios del archivo de configuración.</span><span class="sxs-lookup"><span data-stu-id="3467f-256">By default, the `IFileMonitor` is provided by a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, which uses <xref:System.IO.FileSystemWatcher> to monitor for configuration file changes.</span></span>

<span data-ttu-id="3467f-257">En la aplicación de ejemplo se muestran dos implementaciones para supervisar los cambios de configuración.</span><span class="sxs-lookup"><span data-stu-id="3467f-257">The sample app demonstrates two implementations for monitoring configuration changes.</span></span> <span data-ttu-id="3467f-258">Si se modifica cualquiera de los archivos *appsettings*, ambas implementaciones de supervisión de los archivos ejecutan un código personalizado&mdash;la aplicación de ejemplo escribe un mensaje en la consola.</span><span class="sxs-lookup"><span data-stu-id="3467f-258">If any of the *appsettings* files change, both of the file monitoring implementations execute custom code&mdash;the sample app writes a message to the console.</span></span>

<span data-ttu-id="3467f-259">El `FileSystemWatcher` de un archivo de configuración puede desencadenar varias devoluciones de llamada de token para un único cambio del archivo de configuración.</span><span class="sxs-lookup"><span data-stu-id="3467f-259">A configuration file's `FileSystemWatcher` can trigger multiple token callbacks for a single configuration file change.</span></span> <span data-ttu-id="3467f-260">Para garantizar que el código personalizado se ejecute solo una vez cuando se desencadenan varias devoluciones de llamada de token, la implementación del ejemplo comprueba los hashes de archivo.</span><span class="sxs-lookup"><span data-stu-id="3467f-260">To ensure that the custom code is only run once when multiple token callbacks are triggered, the sample's implementation checks file hashes.</span></span> <span data-ttu-id="3467f-261">El ejemplo usa el algoritmo hash seguro 1.</span><span class="sxs-lookup"><span data-stu-id="3467f-261">The sample uses SHA1 file hashing.</span></span> <span data-ttu-id="3467f-262">Se implementa un reintento con una interrupción exponencial.</span><span class="sxs-lookup"><span data-stu-id="3467f-262">A retry is implemented with an exponential back-off.</span></span> <span data-ttu-id="3467f-263">El reintento aparece porque se puede producir un bloqueo de archivos que impida temporalmente calcular un hash nuevo en un archivo.</span><span class="sxs-lookup"><span data-stu-id="3467f-263">The retry is present because file locking may occur that temporarily prevents computing a new hash on a file.</span></span>

<span data-ttu-id="3467f-264">*Utilities/Utilities.cs*:</span><span class="sxs-lookup"><span data-stu-id="3467f-264">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a><span data-ttu-id="3467f-265">Token de cambio de inicio simple</span><span class="sxs-lookup"><span data-stu-id="3467f-265">Simple startup change token</span></span>

<span data-ttu-id="3467f-266">Registre una devolución de llamada de `Action` del consumidor de token para las notificaciones de cambio en el token de recarga de configuración.</span><span class="sxs-lookup"><span data-stu-id="3467f-266">Register a token consumer `Action` callback for change notifications to the configuration reload token.</span></span>

<span data-ttu-id="3467f-267">En `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="3467f-267">In `Startup.Configure`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="3467f-268">`config.GetReloadToken()` proporciona el token.</span><span class="sxs-lookup"><span data-stu-id="3467f-268">`config.GetReloadToken()` provides the token.</span></span> <span data-ttu-id="3467f-269">La devolución de llamada es el método `InvokeChanged`:</span><span class="sxs-lookup"><span data-stu-id="3467f-269">The callback is the `InvokeChanged` method:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="3467f-270">El `state` de la devolución de llamada se usa para pasar el `IHostingEnvironment`, que resulta útil para especificar el archivo de configuración *appsettings* que se va a supervisar (por ejemplo, *appsettings.Development.json* cuando se está en el entorno de desarrollo).</span><span class="sxs-lookup"><span data-stu-id="3467f-270">The `state` of the callback is used to pass in the `IHostingEnvironment`, which is useful for specifying the correct *appsettings* configuration file to monitor (for example, *appsettings.Development.json* when in the Development environment).</span></span> <span data-ttu-id="3467f-271">Se usa el hash de archivo para evitar que se ejecute varias veces la instrucción `WriteConsole` debido a varias devoluciones de llamada de token cuando el archivo de configuración solo ha cambiado una vez.</span><span class="sxs-lookup"><span data-stu-id="3467f-271">File hashes are used to prevent the `WriteConsole` statement from running multiple times due to multiple token callbacks when the configuration file has only changed once.</span></span>

<span data-ttu-id="3467f-272">Este sistema se ejecuta siempre que la aplicación esté en ejecución y el usuario no lo puede deshabilitar.</span><span class="sxs-lookup"><span data-stu-id="3467f-272">This system runs as long as the app is running and can't be disabled by the user.</span></span>

### <a name="monitor-configuration-changes-as-a-service"></a><span data-ttu-id="3467f-273">Supervisión de los cambios de configuración como servicio</span><span class="sxs-lookup"><span data-stu-id="3467f-273">Monitor configuration changes as a service</span></span>

<span data-ttu-id="3467f-274">En el ejemplo se implementa lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="3467f-274">The sample implements:</span></span>

* <span data-ttu-id="3467f-275">La supervisión del token de inicio básico.</span><span class="sxs-lookup"><span data-stu-id="3467f-275">Basic startup token monitoring.</span></span>
* <span data-ttu-id="3467f-276">La supervisión como servicio.</span><span class="sxs-lookup"><span data-stu-id="3467f-276">Monitoring as a service.</span></span>
* <span data-ttu-id="3467f-277">Un mecanismo para habilitar y deshabilitar la supervisión.</span><span class="sxs-lookup"><span data-stu-id="3467f-277">A mechanism to enable and disable monitoring.</span></span>

<span data-ttu-id="3467f-278">El ejemplo establece una interfaz `IConfigurationMonitor`.</span><span class="sxs-lookup"><span data-stu-id="3467f-278">The sample establishes an `IConfigurationMonitor` interface.</span></span>

<span data-ttu-id="3467f-279">*Extensions/ConfigurationMonitor.cs*:</span><span class="sxs-lookup"><span data-stu-id="3467f-279">*Extensions/ConfigurationMonitor.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

<span data-ttu-id="3467f-280">El constructor de la clase implementada, `ConfigurationMonitor`, registra una devolución de llamada para las notificaciones de cambio:</span><span class="sxs-lookup"><span data-stu-id="3467f-280">The constructor of the implemented class, `ConfigurationMonitor`, registers a callback for change notifications:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

<span data-ttu-id="3467f-281">`config.GetReloadToken()` proporciona el token.</span><span class="sxs-lookup"><span data-stu-id="3467f-281">`config.GetReloadToken()` supplies the token.</span></span> <span data-ttu-id="3467f-282">`InvokeChanged` es el método de devolución de llamada.</span><span class="sxs-lookup"><span data-stu-id="3467f-282">`InvokeChanged` is the callback method.</span></span> <span data-ttu-id="3467f-283">El elemento `state` de esta instancia es una referencia a la instancia de `IConfigurationMonitor` que se usa para tener acceso al estado de supervisión.</span><span class="sxs-lookup"><span data-stu-id="3467f-283">The `state` in this instance is a reference to the `IConfigurationMonitor` instance that's used to access the monitoring state.</span></span> <span data-ttu-id="3467f-284">Se usan dos propiedades:</span><span class="sxs-lookup"><span data-stu-id="3467f-284">Two properties are used:</span></span>

* <span data-ttu-id="3467f-285">`MonitoringEnabled`: indica si la devolución de llamada debe ejecutar su código personalizado.</span><span class="sxs-lookup"><span data-stu-id="3467f-285">`MonitoringEnabled`: Indicates if the callback should run its custom code.</span></span>
* <span data-ttu-id="3467f-286">`CurrentState`: describe el estado de supervisión actual para su uso en la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="3467f-286">`CurrentState`: Describes the current monitoring state for use in the UI.</span></span>

<span data-ttu-id="3467f-287">El método `InvokeChanged` es similar al enfoque anterior, excepto en que:</span><span class="sxs-lookup"><span data-stu-id="3467f-287">The `InvokeChanged` method is similar to the earlier approach, except that it:</span></span>

* <span data-ttu-id="3467f-288">No ejecuta su código, a menos que `MonitoringEnabled` sea `true`.</span><span class="sxs-lookup"><span data-stu-id="3467f-288">Doesn't run its code unless `MonitoringEnabled` is `true`.</span></span>
* <span data-ttu-id="3467f-289">Genera el `state` actual en su salida de `WriteConsole`.</span><span class="sxs-lookup"><span data-stu-id="3467f-289">Outputs the current `state` in its `WriteConsole` output.</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

<span data-ttu-id="3467f-290">Una instancia de `ConfigurationMonitor` se registra como servicio en `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="3467f-290">An instance `ConfigurationMonitor` is registered as a service in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="3467f-291">En la página Index se ofrece al usuario el control sobre la supervisión de la configuración.</span><span class="sxs-lookup"><span data-stu-id="3467f-291">The Index page offers the user control over configuration monitoring.</span></span> <span data-ttu-id="3467f-292">La instancia de `IConfigurationMonitor` se inserta en `IndexModel`.</span><span class="sxs-lookup"><span data-stu-id="3467f-292">The instance of `IConfigurationMonitor` is injected into the `IndexModel`.</span></span>

<span data-ttu-id="3467f-293">*Páginas/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="3467f-293">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="3467f-294">El monitor de configuración (`_monitor`) se usa para habilitar o deshabilitar la supervisión y establecer el estado actual de los comentarios de la interfaz de usuario:</span><span class="sxs-lookup"><span data-stu-id="3467f-294">The configuration monitor (`_monitor`) is used to enable or disable monitoring and set the current state for UI feedback:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="3467f-295">Cuando se desencadena `OnPostStartMonitoring`, se habilita la supervisión y se borra el estado actual.</span><span class="sxs-lookup"><span data-stu-id="3467f-295">When `OnPostStartMonitoring` is triggered, monitoring is enabled, and the current state is cleared.</span></span> <span data-ttu-id="3467f-296">Cuando se desencadena `OnPostStopMonitoring`, se deshabilita la supervisión y se establece el estado para reflejar que no se está realizando la supervisión.</span><span class="sxs-lookup"><span data-stu-id="3467f-296">When `OnPostStopMonitoring` is triggered, monitoring is disabled, and the state is set to reflect that monitoring isn't occurring.</span></span>

<span data-ttu-id="3467f-297">Los botones de la interfaz de usuario habilitan y deshabilitan la supervisión.</span><span class="sxs-lookup"><span data-stu-id="3467f-297">Buttons in the UI enable and disable monitoring.</span></span>

<span data-ttu-id="3467f-298">*Páginas/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="3467f-298">*Pages/Index.cshtml*:</span></span>

[!code-cshtml[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a><span data-ttu-id="3467f-299">Supervisión de los cambios de archivos en caché</span><span class="sxs-lookup"><span data-stu-id="3467f-299">Monitor cached file changes</span></span>

<span data-ttu-id="3467f-300">El contenido de los archivos se puede almacenar en caché en memoria mediante <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span><span class="sxs-lookup"><span data-stu-id="3467f-300">File content can be cached in-memory using <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span></span> <span data-ttu-id="3467f-301">El almacenamiento en caché en memoria se describe en el tema [Cache in-memory](xref:performance/caching/memory) (Almacenamiento en caché en memoria).</span><span class="sxs-lookup"><span data-stu-id="3467f-301">In-memory caching is described in the [Cache in-memory](xref:performance/caching/memory) topic.</span></span> <span data-ttu-id="3467f-302">Sin realizar pasos adicionales, como la implementación que se describe a continuación, si los datos de origen cambian, se devuelven datos *obsoletos* (no actualizados) de la caché.</span><span class="sxs-lookup"><span data-stu-id="3467f-302">Without taking additional steps, such as the implementation described below, *stale* (outdated) data is returned from a cache if the source data changes.</span></span>

<span data-ttu-id="3467f-303">Por ejemplo, si no se tiene en cuenta el estado de un archivo de origen en caché cuando se renueva un período de [vencimiento variable](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration), se pueden crear datos de archivo en caché obsoletos.</span><span class="sxs-lookup"><span data-stu-id="3467f-303">For example, not taking into account the status of a cached source file when renewing a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period leads to stale cached file data.</span></span> <span data-ttu-id="3467f-304">En cada solicitud de los datos se renueva el período de vencimiento variable, pero el archivo nunca se vuelve a cargar en la caché.</span><span class="sxs-lookup"><span data-stu-id="3467f-304">Each request for the data renews the sliding expiration period, but the file is never reloaded into the cache.</span></span> <span data-ttu-id="3467f-305">Las características de la aplicación que usen el contenido en caché del archivo están sujetas a la posible recepción de contenido obsoleto.</span><span class="sxs-lookup"><span data-stu-id="3467f-305">Any app features that use the file's cached content are subject to possibly receiving stale content.</span></span>

<span data-ttu-id="3467f-306">El uso de tokens de cambio en un escenario de almacenamiento en caché de archivos evita la presencia de contenido de archivos obsoletos en la caché.</span><span class="sxs-lookup"><span data-stu-id="3467f-306">Using change tokens in a file caching scenario prevents the presence of stale file content in the cache.</span></span> <span data-ttu-id="3467f-307">En la aplicación de ejemplo se muestra una implementación del enfoque.</span><span class="sxs-lookup"><span data-stu-id="3467f-307">The sample app demonstrates an implementation of the approach.</span></span>

<span data-ttu-id="3467f-308">En el ejemplo se usa `GetFileContent` para:</span><span class="sxs-lookup"><span data-stu-id="3467f-308">The sample uses `GetFileContent` to:</span></span>

* <span data-ttu-id="3467f-309">Devolver el contenido del archivo.</span><span class="sxs-lookup"><span data-stu-id="3467f-309">Return file content.</span></span>
* <span data-ttu-id="3467f-310">Implementar un algoritmo de reintento con interrupción exponencial para casos en los que un bloqueo de archivo impide temporalmente leer un archivo.</span><span class="sxs-lookup"><span data-stu-id="3467f-310">Implement a retry algorithm with exponential back-off to cover cases where a file lock temporarily prevents reading a file.</span></span>

<span data-ttu-id="3467f-311">*Utilities/Utilities.cs*:</span><span class="sxs-lookup"><span data-stu-id="3467f-311">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

<span data-ttu-id="3467f-312">Se crea un `FileService` para administrar las búsquedas de archivos en caché.</span><span class="sxs-lookup"><span data-stu-id="3467f-312">A `FileService` is created to handle cached file lookups.</span></span> <span data-ttu-id="3467f-313">La llamada al método `GetFileContent` del servicio intenta obtener el contenido de archivo de la caché en memoria y devolverlo al autor de la llamada (*Services/FileService.cs*).</span><span class="sxs-lookup"><span data-stu-id="3467f-313">The `GetFileContent` method call of the service attempts to obtain file content from the in-memory cache and return it to the caller (*Services/FileService.cs*).</span></span>

<span data-ttu-id="3467f-314">Si el contenido en caché no se encuentra mediante la clave de caché, se realizan las acciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="3467f-314">If cached content isn't found using the cache key, the following actions are taken:</span></span>

1. <span data-ttu-id="3467f-315">El contenido del archivo se obtiene mediante `GetFileContent`.</span><span class="sxs-lookup"><span data-stu-id="3467f-315">The file content is obtained using `GetFileContent`.</span></span>
1. <span data-ttu-id="3467f-316">Se obtiene un token de cambio del proveedor de archivos con [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span><span class="sxs-lookup"><span data-stu-id="3467f-316">A change token is obtained from the file provider with [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span></span> <span data-ttu-id="3467f-317">La devolución de llamada del token se desencadena cuando se modifica el archivo.</span><span class="sxs-lookup"><span data-stu-id="3467f-317">The token's callback is triggered when the file is modified.</span></span>
1. <span data-ttu-id="3467f-318">El contenido del archivo se almacena en caché con un período de [vencimiento variable](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration).</span><span class="sxs-lookup"><span data-stu-id="3467f-318">The file content is cached with a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period.</span></span> <span data-ttu-id="3467f-319">El token de cambio se adjunta con [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) para expulsar la entrada de caché si el archivo cambia mientras está almacenado en caché.</span><span class="sxs-lookup"><span data-stu-id="3467f-319">The change token is attached with [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) to evict the cache entry if the file changes while it's cached.</span></span>

<span data-ttu-id="3467f-320">En el ejemplo siguiente, los archivos se almacenan en la [raíz del contenido](xref:fundamentals/index#content-root) de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="3467f-320">In the following example, files are stored in the app's [content root](xref:fundamentals/index#content-root).</span></span> <span data-ttu-id="3467f-321">[IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider) se usa para obtener un elemento <xref:Microsoft.Extensions.FileProviders.IFileProvider> que apunta a la <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath> de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="3467f-321">[IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider) is used to obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> pointing at the app's <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>.</span></span> <span data-ttu-id="3467f-322">La `filePath` se obtiene con [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span><span class="sxs-lookup"><span data-stu-id="3467f-322">The `filePath` is obtained with [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Services/FileService.cs?name=snippet1)]

<span data-ttu-id="3467f-323">El `FileService` se registra en el contenedor de servicios junto con el servicio de almacenamiento en caché.</span><span class="sxs-lookup"><span data-stu-id="3467f-323">The `FileService` is registered in the service container along with the memory caching service.</span></span>

<span data-ttu-id="3467f-324">En `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="3467f-324">In `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="3467f-325">El modelo de página carga el contenido del archivo mediante el servicio.</span><span class="sxs-lookup"><span data-stu-id="3467f-325">The page model loads the file's content using the service.</span></span>

<span data-ttu-id="3467f-326">En el método `OnGet` de la página de índice (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="3467f-326">In the Index page's `OnGet` method (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a><span data-ttu-id="3467f-327">Clase CompositeChangeToken</span><span class="sxs-lookup"><span data-stu-id="3467f-327">CompositeChangeToken class</span></span>

<span data-ttu-id="3467f-328">Para representar una o varias instancias de `IChangeToken` en un solo objeto, use la clase <xref:Microsoft.Extensions.Primitives.CompositeChangeToken>.</span><span class="sxs-lookup"><span data-stu-id="3467f-328">For representing one or more `IChangeToken` instances in a single object, use the <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> class.</span></span>

```csharp
var firstCancellationTokenSource = new CancellationTokenSource();
var secondCancellationTokenSource = new CancellationTokenSource();

var firstCancellationToken = firstCancellationTokenSource.Token;
var secondCancellationToken = secondCancellationTokenSource.Token;

var firstCancellationChangeToken = new CancellationChangeToken(firstCancellationToken);
var secondCancellationChangeToken = new CancellationChangeToken(secondCancellationToken);

var compositeChangeToken = 
    new CompositeChangeToken(
        new List<IChangeToken> 
        {
            firstCancellationChangeToken, 
            secondCancellationChangeToken
        });
```

<span data-ttu-id="3467f-329">En el token compuesto, `HasChanged` notifica `true` si algún token representado `HasChanged` es `true`.</span><span class="sxs-lookup"><span data-stu-id="3467f-329">`HasChanged` on the composite token reports `true` if any represented token `HasChanged` is `true`.</span></span> <span data-ttu-id="3467f-330">En el token compuesto, `ActiveChangeCallbacks` notifica `true` si algún token representado `ActiveChangeCallbacks` es `true`.</span><span class="sxs-lookup"><span data-stu-id="3467f-330">`ActiveChangeCallbacks` on the composite token reports `true` if any represented token `ActiveChangeCallbacks` is `true`.</span></span> <span data-ttu-id="3467f-331">Si se producen varios eventos de cambio simultáneos, la devolución de llamada de cambio compuesto se invoca una vez.</span><span class="sxs-lookup"><span data-stu-id="3467f-331">If multiple concurrent change events occur, the composite change callback is invoked one time.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="3467f-332">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="3467f-332">Additional resources</span></span>

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
