---
<span data-ttu-id="68977-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="68977-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68977-102">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="68977-102">'Blazor'</span></span>
- <span data-ttu-id="68977-103">"Identity"</span><span class="sxs-lookup"><span data-stu-id="68977-103">'Identity'</span></span>
- <span data-ttu-id="68977-104">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="68977-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="68977-105">"Razor"</span><span class="sxs-lookup"><span data-stu-id="68977-105">'Razor'</span></span>
- <span data-ttu-id="68977-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="68977-106">'SignalR' uid:</span></span> 

---
# <a name="background-tasks-with-hosted-services-in-aspnet-core"></a><span data-ttu-id="68977-107">Tareas en segundo plano con servicios hospedados en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="68977-107">Background tasks with hosted services in ASP.NET Core</span></span>

<span data-ttu-id="68977-108">Por [Jeow Li Huan](https://github.com/huan086)</span><span class="sxs-lookup"><span data-stu-id="68977-108">By [Jeow Li Huan](https://github.com/huan086)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="68977-109">En ASP.NET Core, las tareas en segundo plano se pueden implementar como *servicios hospedados*.</span><span class="sxs-lookup"><span data-stu-id="68977-109">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span> <span data-ttu-id="68977-110">Un servicio hospedado es una clase con lógica de tarea en segundo plano que implementa la interfaz <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="68977-110">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="68977-111">En este tema se incluyen tres ejemplos de servicio hospedado:</span><span class="sxs-lookup"><span data-stu-id="68977-111">This topic provides three hosted service examples:</span></span>

* <span data-ttu-id="68977-112">Una tarea en segundo plano que se ejecuta según un temporizador.</span><span class="sxs-lookup"><span data-stu-id="68977-112">Background task that runs on a timer.</span></span>
* <span data-ttu-id="68977-113">Un servicio hospedado que activa un [servicio con ámbito](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="68977-113">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="68977-114">El servicio con ámbito puede usar la [inserción de dependencias (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="68977-114">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="68977-115">Tareas en segundo plano en cola que se ejecutan en secuencia.</span><span class="sxs-lookup"><span data-stu-id="68977-115">Queued background tasks that run sequentially.</span></span>

<span data-ttu-id="68977-116">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="68977-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="worker-service-template"></a><span data-ttu-id="68977-117">Plantilla Worker Service</span><span class="sxs-lookup"><span data-stu-id="68977-117">Worker Service template</span></span>

<span data-ttu-id="68977-118">La plantilla Worker Service de ASP.NET Core sirve de punto de partida para escribir aplicaciones de servicio de larga duración.</span><span class="sxs-lookup"><span data-stu-id="68977-118">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="68977-119">Una aplicación creada a partir de la plantilla Worker Service especifica el SDK de trabajo en su archivo del proyecto:</span><span class="sxs-lookup"><span data-stu-id="68977-119">An app created from the Worker Service template specifies the Worker SDK in its project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

<span data-ttu-id="68977-120">Para usar la plantilla como base de una aplicación de servicios hospedados:</span><span class="sxs-lookup"><span data-stu-id="68977-120">To use the template as a basis for a hosted services app:</span></span>

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="package"></a><span data-ttu-id="68977-121">Package</span><span class="sxs-lookup"><span data-stu-id="68977-121">Package</span></span>

<span data-ttu-id="68977-122">Una aplicación basada en la plantilla Worker Service usa el SDK de `Microsoft.NET.Sdk.Worker` y tiene una referencia de paquete explícita al paquete [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting).</span><span class="sxs-lookup"><span data-stu-id="68977-122">An app based on the Worker Service template uses the `Microsoft.NET.Sdk.Worker` SDK and has an explicit package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package.</span></span> <span data-ttu-id="68977-123">Por ejemplo, consulte el archivo del proyecto de la aplicación de ejemplo (*BackgroundTasksSample.csproj*).</span><span class="sxs-lookup"><span data-stu-id="68977-123">For example, see the sample app's project file (*BackgroundTasksSample.csproj*).</span></span>

<span data-ttu-id="68977-124">En el caso de las aplicaciones web que usan el SDK de `Microsoft.NET.Sdk.Web`, desde el marco compartido se hace una referencia implícita al paquete [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting).</span><span class="sxs-lookup"><span data-stu-id="68977-124">For web apps that use the `Microsoft.NET.Sdk.Web` SDK, the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package is referenced implicitly from the shared framework.</span></span> <span data-ttu-id="68977-125">No se requiere una referencia de paquete explícita en el archivo del proyecto de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="68977-125">An explicit package reference in the app's project file isn't required.</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="68977-126">Interfaz IHostedService</span><span class="sxs-lookup"><span data-stu-id="68977-126">IHostedService interface</span></span>

<span data-ttu-id="68977-127">La interfaz <xref:Microsoft.Extensions.Hosting.IHostedService> define dos métodos para los objetos administrados por el host:</span><span class="sxs-lookup"><span data-stu-id="68977-127">The <xref:Microsoft.Extensions.Hosting.IHostedService> interface defines two methods for objects that are managed by the host:</span></span>

* <span data-ttu-id="68977-128">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*): `StartAsync` contiene la lógica para iniciar la tarea en segundo plano.</span><span class="sxs-lookup"><span data-stu-id="68977-128">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` contains the logic to start the background task.</span></span> <span data-ttu-id="68977-129">Se llama a `StartAsync`*antes de que*:</span><span class="sxs-lookup"><span data-stu-id="68977-129">`StartAsync` is called *before*:</span></span>

  * <span data-ttu-id="68977-130">La canalización de procesamiento de solicitudes de la aplicación está configurada (`Startup.Configure`).</span><span class="sxs-lookup"><span data-stu-id="68977-130">The app's request processing pipeline is configured (`Startup.Configure`).</span></span>
  * <span data-ttu-id="68977-131">El servidor se haya iniciado y [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) se haya activado.</span><span class="sxs-lookup"><span data-stu-id="68977-131">The server is started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span>

  <span data-ttu-id="68977-132">El comportamiento predeterminado se puede cambiar para que el `StartAsync` del servicio hospedado se ejecute después de que se haya configurado la canalización de la aplicación y se haya llamado a `ApplicationStarted`.</span><span class="sxs-lookup"><span data-stu-id="68977-132">The default behavior can be changed so that the hosted service's `StartAsync` runs after the app's pipeline has been configured and `ApplicationStarted` is called.</span></span> <span data-ttu-id="68977-133">Para cambiar el comportamiento predeterminado, agregue el servicio hospedado (`VideosWatcher` en el ejemplo siguiente) después de llamar a `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="68977-133">To change the default behavior, add the hosted service (`VideosWatcher` in the following example) after calling `ConfigureWebHostDefaults`:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Hosting;
  using Microsoft.Extensions.DependencyInjection;
  using Microsoft.Extensions.Hosting;

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
                  webBuilder.UseStartup<Startup>();
              })
              .ConfigureServices(services =>
              {
                  services.AddHostedService<VideosWatcher>();
              });
  }
  ```

* <span data-ttu-id="68977-134">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*): se activa cuando el host está realizando un cierre estable.</span><span class="sxs-lookup"><span data-stu-id="68977-134">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Triggered when the host is performing a graceful shutdown.</span></span> <span data-ttu-id="68977-135">`StopAsync` contiene la lógica para finalizar la tarea en segundo plano.</span><span class="sxs-lookup"><span data-stu-id="68977-135">`StopAsync` contains the logic to end the background task.</span></span> <span data-ttu-id="68977-136">Implemente <xref:System.IDisposable> y los [finalizadores (destructores)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) para desechar los recursos no administrados.</span><span class="sxs-lookup"><span data-stu-id="68977-136">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="68977-137">El token de cancelación tiene un tiempo de espera predeterminado de cinco segundos para indicar que el proceso de cierre ya no debería ser estable.</span><span class="sxs-lookup"><span data-stu-id="68977-137">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="68977-138">Cuando se solicita la cancelación en el token:</span><span class="sxs-lookup"><span data-stu-id="68977-138">When cancellation is requested on the token:</span></span>

  * <span data-ttu-id="68977-139">Se deben anular las operaciones restantes en segundo plano que realiza la aplicación.</span><span class="sxs-lookup"><span data-stu-id="68977-139">Any remaining background operations that the app is performing should be aborted.</span></span>
  * <span data-ttu-id="68977-140">Los métodos llamados en `StopAsync` deberían devolver contenido al momento.</span><span class="sxs-lookup"><span data-stu-id="68977-140">Any methods called in `StopAsync` should return promptly.</span></span>

  <span data-ttu-id="68977-141">No obstante, las tareas no se abandonan después de solicitar la cancelación, sino que el autor de la llamada espera a que se completen todas las tareas.</span><span class="sxs-lookup"><span data-stu-id="68977-141">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>

  <span data-ttu-id="68977-142">Si la aplicación se cierra inesperadamente (por ejemplo, porque se produzca un error en el proceso de la aplicación), puede que no sea posible llamar a `StopAsync`.</span><span class="sxs-lookup"><span data-stu-id="68977-142">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="68977-143">Por lo tanto, los métodos llamados o las operaciones llevadas a cabo en `StopAsync` podrían no producirse.</span><span class="sxs-lookup"><span data-stu-id="68977-143">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

  <span data-ttu-id="68977-144">Para ampliar el tiempo de espera predeterminado de apagado de 5 segundos, establezca:</span><span class="sxs-lookup"><span data-stu-id="68977-144">To extend the default five second shutdown timeout, set:</span></span>

  * <span data-ttu-id="68977-145"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> cuando se usa el host genérico.</span><span class="sxs-lookup"><span data-stu-id="68977-145"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span> <span data-ttu-id="68977-146">Para obtener más información, vea <xref:fundamentals/host/generic-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="68977-146">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>
  * <span data-ttu-id="68977-147">Configuración de los valores de host de tiempo de espera de apagado cuando se usa el host web.</span><span class="sxs-lookup"><span data-stu-id="68977-147">Shutdown timeout host configuration setting when using Web Host.</span></span> <span data-ttu-id="68977-148">Para obtener más información, vea <xref:fundamentals/host/web-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="68977-148">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span>

<span data-ttu-id="68977-149">El servicio hospedado se activa una vez al inicio de la aplicación y se cierra de manera estable cuando dicha aplicación se cierra.</span><span class="sxs-lookup"><span data-stu-id="68977-149">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span> <span data-ttu-id="68977-150">Si se produce un error durante la ejecución de una tarea en segundo plano, hay que llamar a `Dispose`, aun cuando no se haya llamado a `StopAsync`.</span><span class="sxs-lookup"><span data-stu-id="68977-150">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span>

## <a name="backgroundservice-base-class"></a><span data-ttu-id="68977-151">Clase base BackgroundService</span><span class="sxs-lookup"><span data-stu-id="68977-151">BackgroundService base class</span></span>

<span data-ttu-id="68977-152"><xref:Microsoft.Extensions.Hosting.BackgroundService> es una clase base para implementar un <xref:Microsoft.Extensions.Hosting.IHostedService> de larga duración.</span><span class="sxs-lookup"><span data-stu-id="68977-152"><xref:Microsoft.Extensions.Hosting.BackgroundService> is a base class for implementing a long running <xref:Microsoft.Extensions.Hosting.IHostedService>.</span></span>

<span data-ttu-id="68977-153">Se llama a [ExecuteAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.ExecuteAsync*) para ejecutar el servicio en segundo plano.</span><span class="sxs-lookup"><span data-stu-id="68977-153">[ExecuteAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.ExecuteAsync*) is called to run the background service.</span></span> <span data-ttu-id="68977-154">La implementación devuelve <xref:System.Threading.Tasks.Task>, que representa toda la duración del servicio en segundo plano.</span><span class="sxs-lookup"><span data-stu-id="68977-154">The implementation returns a <xref:System.Threading.Tasks.Task> that represents the entire lifetime of the background service.</span></span> <span data-ttu-id="68977-155">No se inicia ningún servicio hasta que [ExecuteAsync se convierte en asincrónico](https://github.com/dotnet/extensions/issues/2149), mediante una llamada a `await`.</span><span class="sxs-lookup"><span data-stu-id="68977-155">No further services are started until [ExecuteAsync becomes asynchronous](https://github.com/dotnet/extensions/issues/2149), such as by calling `await`.</span></span> <span data-ttu-id="68977-156">Evite realizar un trabajo de inicialización de bloqueo prolongado en `ExecuteAsync`.</span><span class="sxs-lookup"><span data-stu-id="68977-156">Avoid performing long, blocking initialization work in `ExecuteAsync`.</span></span> <span data-ttu-id="68977-157">El host se bloquea en [StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.StopAsync*) a la espera de que `ExecuteAsync` se complete.</span><span class="sxs-lookup"><span data-stu-id="68977-157">The host blocks in [StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.StopAsync*) waiting for `ExecuteAsync` to complete.</span></span>

<span data-ttu-id="68977-158">El token de cancelación se desencadena cuando se llama a [IHostedService.StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*).</span><span class="sxs-lookup"><span data-stu-id="68977-158">The cancellation token is triggered when [IHostedService.StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) is called.</span></span> <span data-ttu-id="68977-159">La implementación de `ExecuteAsync` debe finalizar rápidamente cuando se active el token de cancelación para cerrar correctamente el servicio.</span><span class="sxs-lookup"><span data-stu-id="68977-159">Your implementation of `ExecuteAsync` should finish promptly when the cancellation token is fired in order to gracefully shut down the service.</span></span> <span data-ttu-id="68977-160">De lo contrario, el servicio se cierra de manera abrupta durante el tiempo de expiración del cierre.</span><span class="sxs-lookup"><span data-stu-id="68977-160">Otherwise, the service ungracefully shuts down at the shutdown timeout.</span></span> <span data-ttu-id="68977-161">Para más información, consulte la sección sobre la [interfaz IHostedService](#ihostedservice-interface).</span><span class="sxs-lookup"><span data-stu-id="68977-161">For more information, see the [IHostedService interface](#ihostedservice-interface) section.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="68977-162">Tareas en segundo plano temporizadas</span><span class="sxs-lookup"><span data-stu-id="68977-162">Timed background tasks</span></span>

<span data-ttu-id="68977-163">Una tarea en segundo plano temporizada hace uso de la clase [System.Threading.Timer](xref:System.Threading.Timer).</span><span class="sxs-lookup"><span data-stu-id="68977-163">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span> <span data-ttu-id="68977-164">El temporizador activa el método `DoWork` de la tarea.</span><span class="sxs-lookup"><span data-stu-id="68977-164">The timer triggers the task's `DoWork` method.</span></span> <span data-ttu-id="68977-165">El temporizador está deshabilitado en `StopAsync` y se desecha cuando el contenedor de servicios se elimina en `Dispose`:</span><span class="sxs-lookup"><span data-stu-id="68977-165">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=16-17,34,41)]

<span data-ttu-id="68977-166"><xref:System.Threading.Timer> no espera a que finalicen las ejecuciones anteriores de `DoWork`, por lo que es posible que el enfoque mostrado no sea adecuado para todos los escenarios.</span><span class="sxs-lookup"><span data-stu-id="68977-166">The <xref:System.Threading.Timer> doesn't wait for previous executions of `DoWork` to finish, so the approach shown might not be suitable for every scenario.</span></span> <span data-ttu-id="68977-167">[Interlocked.Increment](xref:System.Threading.Interlocked.Increment*) se usa para incrementar el contador de ejecución como una operación atómica, lo que garantiza que varios subprocesos no actualicen `executionCount` simultáneamente.</span><span class="sxs-lookup"><span data-stu-id="68977-167">[Interlocked.Increment](xref:System.Threading.Interlocked.Increment*) is used to increment the execution counter as an atomic operation, which ensures that multiple threads don't update `executionCount` concurrently.</span></span>

<span data-ttu-id="68977-168">El servicio se registra en `IHostBuilder.ConfigureServices` (*Program.cs*) con el método de extensión `AddHostedService`:</span><span class="sxs-lookup"><span data-stu-id="68977-168">The service is registered in `IHostBuilder.ConfigureServices` (*Program.cs*) with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="68977-169">Consumir un servicio con ámbito en una tarea en segundo plano</span><span class="sxs-lookup"><span data-stu-id="68977-169">Consuming a scoped service in a background task</span></span>

<span data-ttu-id="68977-170">Para usar [servicios con ámbito](xref:fundamentals/dependency-injection#service-lifetimes) dentro de un [BackgroundService](#backgroundservice-base-class), cree un ámbito.</span><span class="sxs-lookup"><span data-stu-id="68977-170">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within a [BackgroundService](#backgroundservice-base-class), create a scope.</span></span> <span data-ttu-id="68977-171">No se crean ámbitos de forma predeterminada para los servicios hospedados.</span><span class="sxs-lookup"><span data-stu-id="68977-171">No scope is created for a hosted service by default.</span></span>

<span data-ttu-id="68977-172">El servicio de tareas en segundo plano con ámbito contiene la lógica de la tarea en segundo plano.</span><span class="sxs-lookup"><span data-stu-id="68977-172">The scoped background task service contains the background task's logic.</span></span> <span data-ttu-id="68977-173">En el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="68977-173">In the following example:</span></span>

* <span data-ttu-id="68977-174">El servicio es asincrónico.</span><span class="sxs-lookup"><span data-stu-id="68977-174">The service is asynchronous.</span></span> <span data-ttu-id="68977-175">El método `DoWork` devuelve un objeto `Task`.</span><span class="sxs-lookup"><span data-stu-id="68977-175">The `DoWork` method returns a `Task`.</span></span> <span data-ttu-id="68977-176">Para fines de demostración, se espera un retraso de diez segundos en el método `DoWork`.</span><span class="sxs-lookup"><span data-stu-id="68977-176">For demonstration purposes, a delay of ten seconds is awaited in the `DoWork` method.</span></span>
* <span data-ttu-id="68977-177"><xref:Microsoft.Extensions.Logging.ILogger> se inserta en el servicio.</span><span class="sxs-lookup"><span data-stu-id="68977-177">An <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="68977-178">El servicio hospedado crea un ámbito con el fin de resolver el servicio de tareas en segundo plano con ámbito para llamar a su método `DoWork`.</span><span class="sxs-lookup"><span data-stu-id="68977-178">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method.</span></span> <span data-ttu-id="68977-179">`DoWork` devuelve `Task`, que se espera en `ExecuteAsync`:</span><span class="sxs-lookup"><span data-stu-id="68977-179">`DoWork` returns a `Task`, which is awaited in `ExecuteAsync`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=19,22-35)]

<span data-ttu-id="68977-180">Los servicios se registran en `IHostBuilder.ConfigureServices` (*Program.cs*).</span><span class="sxs-lookup"><span data-stu-id="68977-180">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span></span> <span data-ttu-id="68977-181">El servicio hospedado se registra en con el método de extensión `AddHostedService`:</span><span class="sxs-lookup"><span data-stu-id="68977-181">The hosted service is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="68977-182">Tareas en segundo plano en cola</span><span class="sxs-lookup"><span data-stu-id="68977-182">Queued background tasks</span></span>

<span data-ttu-id="68977-183">La cola de tareas en segundo plano se basa en <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> de .NET 4.x ([está previsto que se integre en ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span><span class="sxs-lookup"><span data-stu-id="68977-183">A background task queue is based on the .NET 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([tentatively scheduled to be built-in for ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="68977-184">En el ejemplo `QueueHostedService` siguiente:</span><span class="sxs-lookup"><span data-stu-id="68977-184">In the following `QueueHostedService` example:</span></span>

* <span data-ttu-id="68977-185">El método `BackgroundProcessing` devuelve `Task`, que se espera en `ExecuteAsync`.</span><span class="sxs-lookup"><span data-stu-id="68977-185">The `BackgroundProcessing` method returns a `Task`, which is awaited in `ExecuteAsync`.</span></span>
* <span data-ttu-id="68977-186">Las tareas en segundo plano que están en cola se quitan de ella y se ejecutan en `BackgroundProcessing`.</span><span class="sxs-lookup"><span data-stu-id="68977-186">Background tasks in the queue are dequeued and executed in `BackgroundProcessing`.</span></span>
* <span data-ttu-id="68977-187">Se esperan elementos de trabajo antes de que el servicio se detenga en `StopAsync`.</span><span class="sxs-lookup"><span data-stu-id="68977-187">Work items are awaited before the service stops in `StopAsync`.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=28-29,33)]

<span data-ttu-id="68977-188">Un servicio `MonitorLoop` controla las tareas de puesta en cola para el servicio hospedado cada vez que se selecciona la tecla `w` en un dispositivo de entrada:</span><span class="sxs-lookup"><span data-stu-id="68977-188">A `MonitorLoop` service handles enqueuing tasks for the hosted service whenever the `w` key is selected on an input device:</span></span>

* <span data-ttu-id="68977-189">`IBackgroundTaskQueue` se inserta en el servicio `MonitorLoop`.</span><span class="sxs-lookup"><span data-stu-id="68977-189">The `IBackgroundTaskQueue` is injected into the `MonitorLoop` service.</span></span>
* <span data-ttu-id="68977-190">Se llama a `IBackgroundTaskQueue.QueueBackgroundWorkItem` para poner en cola el elemento de trabajo.</span><span class="sxs-lookup"><span data-stu-id="68977-190">`IBackgroundTaskQueue.QueueBackgroundWorkItem` is called to enqueue a work item.</span></span>
* <span data-ttu-id="68977-191">El elemento de trabajo simula una tarea en segundo plano de larga duración:</span><span class="sxs-lookup"><span data-stu-id="68977-191">The work item simulates a long-running background task:</span></span>
  * <span data-ttu-id="68977-192">Se ejecutan tres retrasos de 5 segundos (`Task.Delay`).</span><span class="sxs-lookup"><span data-stu-id="68977-192">Three 5-second delays are executed (`Task.Delay`).</span></span>
  * <span data-ttu-id="68977-193">Una instrucción `try-catch` captura <xref:System.OperationCanceledException> si se cancela la tarea.</span><span class="sxs-lookup"><span data-stu-id="68977-193">A `try-catch` statement traps <xref:System.OperationCanceledException> if the task is cancelled.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/MonitorLoop.cs?name=snippet_Monitor&highlight=7,33)]

<span data-ttu-id="68977-194">Los servicios se registran en `IHostBuilder.ConfigureServices` (*Program.cs*).</span><span class="sxs-lookup"><span data-stu-id="68977-194">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span></span> <span data-ttu-id="68977-195">El servicio hospedado se registra en con el método de extensión `AddHostedService`:</span><span class="sxs-lookup"><span data-stu-id="68977-195">The hosted service is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet3)]

<span data-ttu-id="68977-196">`MonitorLoop` se inicia en `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="68977-196">`MonitorLoop` is started in `Program.Main`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet4)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="68977-197">En ASP.NET Core, las tareas en segundo plano se pueden implementar como *servicios hospedados*.</span><span class="sxs-lookup"><span data-stu-id="68977-197">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span> <span data-ttu-id="68977-198">Un servicio hospedado es una clase con lógica de tarea en segundo plano que implementa la interfaz <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="68977-198">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="68977-199">En este tema se incluyen tres ejemplos de servicio hospedado:</span><span class="sxs-lookup"><span data-stu-id="68977-199">This topic provides three hosted service examples:</span></span>

* <span data-ttu-id="68977-200">Una tarea en segundo plano que se ejecuta según un temporizador.</span><span class="sxs-lookup"><span data-stu-id="68977-200">Background task that runs on a timer.</span></span>
* <span data-ttu-id="68977-201">Un servicio hospedado que activa un [servicio con ámbito](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="68977-201">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="68977-202">El servicio con ámbito puede usar la [inserción de dependencias (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="68977-202">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection)</span></span>
* <span data-ttu-id="68977-203">Tareas en segundo plano en cola que se ejecutan en secuencia.</span><span class="sxs-lookup"><span data-stu-id="68977-203">Queued background tasks that run sequentially.</span></span>

<span data-ttu-id="68977-204">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="68977-204">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="package"></a><span data-ttu-id="68977-205">Package</span><span class="sxs-lookup"><span data-stu-id="68977-205">Package</span></span>

<span data-ttu-id="68977-206">Haga referencia al [metapaquete Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) o agregue una referencia de paquete al paquete [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting).</span><span class="sxs-lookup"><span data-stu-id="68977-206">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package.</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="68977-207">Interfaz IHostedService</span><span class="sxs-lookup"><span data-stu-id="68977-207">IHostedService interface</span></span>

<span data-ttu-id="68977-208">Los servicios hospedados implementan la interfaz <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="68977-208">Hosted services implement the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="68977-209">Esta interfaz define dos métodos para los objetos administrados por el host:</span><span class="sxs-lookup"><span data-stu-id="68977-209">The interface defines two methods for objects that are managed by the host:</span></span>

* <span data-ttu-id="68977-210">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*): `StartAsync` contiene la lógica para iniciar la tarea en segundo plano.</span><span class="sxs-lookup"><span data-stu-id="68977-210">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` contains the logic to start the background task.</span></span> <span data-ttu-id="68977-211">Al utilizar el [host web](xref:fundamentals/host/web-host), se llama a `StartAsync` después de que el servidor se haya iniciado y se haya activado [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*).</span><span class="sxs-lookup"><span data-stu-id="68977-211">When using the [Web Host](xref:fundamentals/host/web-host), `StartAsync` is called after the server has started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span> <span data-ttu-id="68977-212">Al utilizar el [host genérico](xref:fundamentals/host/generic-host), se llama a `StartAsync` antes de que se desencadene `ApplicationStarted`.</span><span class="sxs-lookup"><span data-stu-id="68977-212">When using the [Generic Host](xref:fundamentals/host/generic-host), `StartAsync` is called before `ApplicationStarted` is triggered.</span></span>

* <span data-ttu-id="68977-213">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*): se activa cuando el host está realizando un cierre estable.</span><span class="sxs-lookup"><span data-stu-id="68977-213">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Triggered when the host is performing a graceful shutdown.</span></span> <span data-ttu-id="68977-214">`StopAsync` contiene la lógica para finalizar la tarea en segundo plano.</span><span class="sxs-lookup"><span data-stu-id="68977-214">`StopAsync` contains the logic to end the background task.</span></span> <span data-ttu-id="68977-215">Implemente <xref:System.IDisposable> y los [finalizadores (destructores)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) para desechar los recursos no administrados.</span><span class="sxs-lookup"><span data-stu-id="68977-215">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="68977-216">El token de cancelación tiene un tiempo de espera predeterminado de cinco segundos para indicar que el proceso de cierre ya no debería ser estable.</span><span class="sxs-lookup"><span data-stu-id="68977-216">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="68977-217">Cuando se solicita la cancelación en el token:</span><span class="sxs-lookup"><span data-stu-id="68977-217">When cancellation is requested on the token:</span></span>

  * <span data-ttu-id="68977-218">Se deben anular las operaciones restantes en segundo plano que realiza la aplicación.</span><span class="sxs-lookup"><span data-stu-id="68977-218">Any remaining background operations that the app is performing should be aborted.</span></span>
  * <span data-ttu-id="68977-219">Los métodos llamados en `StopAsync` deberían devolver contenido al momento.</span><span class="sxs-lookup"><span data-stu-id="68977-219">Any methods called in `StopAsync` should return promptly.</span></span>

  <span data-ttu-id="68977-220">No obstante, las tareas no se abandonan después de solicitar la cancelación, sino que el autor de la llamada espera a que se completen todas las tareas.</span><span class="sxs-lookup"><span data-stu-id="68977-220">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>

  <span data-ttu-id="68977-221">Si la aplicación se cierra inesperadamente (por ejemplo, porque se produzca un error en el proceso de la aplicación), puede que no sea posible llamar a `StopAsync`.</span><span class="sxs-lookup"><span data-stu-id="68977-221">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="68977-222">Por lo tanto, los métodos llamados o las operaciones llevadas a cabo en `StopAsync` podrían no producirse.</span><span class="sxs-lookup"><span data-stu-id="68977-222">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

  <span data-ttu-id="68977-223">Para ampliar el tiempo de espera predeterminado de apagado de 5 segundos, establezca:</span><span class="sxs-lookup"><span data-stu-id="68977-223">To extend the default five second shutdown timeout, set:</span></span>

  * <span data-ttu-id="68977-224"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> cuando se usa el host genérico.</span><span class="sxs-lookup"><span data-stu-id="68977-224"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span> <span data-ttu-id="68977-225">Para obtener más información, vea <xref:fundamentals/host/generic-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="68977-225">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>
  * <span data-ttu-id="68977-226">Configuración de los valores de host de tiempo de espera de apagado cuando se usa el host web.</span><span class="sxs-lookup"><span data-stu-id="68977-226">Shutdown timeout host configuration setting when using Web Host.</span></span> <span data-ttu-id="68977-227">Para obtener más información, vea <xref:fundamentals/host/web-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="68977-227">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span>

<span data-ttu-id="68977-228">El servicio hospedado se activa una vez al inicio de la aplicación y se cierra de manera estable cuando dicha aplicación se cierra.</span><span class="sxs-lookup"><span data-stu-id="68977-228">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span> <span data-ttu-id="68977-229">Si se produce un error durante la ejecución de una tarea en segundo plano, hay que llamar a `Dispose`, aun cuando no se haya llamado a `StopAsync`.</span><span class="sxs-lookup"><span data-stu-id="68977-229">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="68977-230">Tareas en segundo plano temporizadas</span><span class="sxs-lookup"><span data-stu-id="68977-230">Timed background tasks</span></span>

<span data-ttu-id="68977-231">Una tarea en segundo plano temporizada hace uso de la clase [System.Threading.Timer](xref:System.Threading.Timer).</span><span class="sxs-lookup"><span data-stu-id="68977-231">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span> <span data-ttu-id="68977-232">El temporizador activa el método `DoWork` de la tarea.</span><span class="sxs-lookup"><span data-stu-id="68977-232">The timer triggers the task's `DoWork` method.</span></span> <span data-ttu-id="68977-233">El temporizador está deshabilitado en `StopAsync` y se desecha cuando el contenedor de servicios se elimina en `Dispose`:</span><span class="sxs-lookup"><span data-stu-id="68977-233">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=15-16,30,37)]

<span data-ttu-id="68977-234"><xref:System.Threading.Timer> no espera a que finalicen las ejecuciones anteriores de `DoWork`, por lo que es posible que el enfoque mostrado no sea adecuado para todos los escenarios.</span><span class="sxs-lookup"><span data-stu-id="68977-234">The <xref:System.Threading.Timer> doesn't wait for previous executions of `DoWork` to finish, so the approach shown might not be suitable for every scenario.</span></span>

<span data-ttu-id="68977-235">El servicio se registra en `Startup.ConfigureServices` con el método de extensión `AddHostedService`:</span><span class="sxs-lookup"><span data-stu-id="68977-235">The service is registered in `Startup.ConfigureServices` with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="68977-236">Consumir un servicio con ámbito en una tarea en segundo plano</span><span class="sxs-lookup"><span data-stu-id="68977-236">Consuming a scoped service in a background task</span></span>

<span data-ttu-id="68977-237">Para usar [servicios con ámbito](xref:fundamentals/dependency-injection#service-lifetimes) en un elemento `IHostedService`, cree un ámbito.</span><span class="sxs-lookup"><span data-stu-id="68977-237">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within an `IHostedService`, create a scope.</span></span> <span data-ttu-id="68977-238">No se crean ámbitos de forma predeterminada para los servicios hospedados.</span><span class="sxs-lookup"><span data-stu-id="68977-238">No scope is created for a hosted service by default.</span></span>

<span data-ttu-id="68977-239">El servicio de tareas en segundo plano con ámbito contiene la lógica de la tarea en segundo plano.</span><span class="sxs-lookup"><span data-stu-id="68977-239">The scoped background task service contains the background task's logic.</span></span> <span data-ttu-id="68977-240">En el siguiente ejemplo, <xref:Microsoft.Extensions.Logging.ILogger> se inserta en el servicio:</span><span class="sxs-lookup"><span data-stu-id="68977-240">In the following example, an <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="68977-241">El servicio hospedado crea un ámbito con objeto de resolver el servicio de tareas en segundo plano con ámbito para llamar a su método `DoWork`:</span><span class="sxs-lookup"><span data-stu-id="68977-241">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=29-36)]

<span data-ttu-id="68977-242">Los servicios se registran en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="68977-242">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="68977-243">La implementación `IHostedService` se registra con el método de extensión `AddHostedService`:</span><span class="sxs-lookup"><span data-stu-id="68977-243">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="68977-244">Tareas en segundo plano en cola</span><span class="sxs-lookup"><span data-stu-id="68977-244">Queued background tasks</span></span>

<span data-ttu-id="68977-245">La cola de tareas en segundo plano se basa en <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> de .NET Framework 4.x ([está previsto que se integre en ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span><span class="sxs-lookup"><span data-stu-id="68977-245">A background task queue is based on the .NET Framework 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([tentatively scheduled to be built-in for ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="68977-246">En `QueueHostedService`, las tareas en segundo plano en la cola se quitan de la cola y se ejecutan como un servicio [BackgroundService](#backgroundservice-base-class), que es una clase base para implementar `IHostedService` de ejecución prolongada:</span><span class="sxs-lookup"><span data-stu-id="68977-246">In `QueueHostedService`, background tasks in the queue are dequeued and executed as a [BackgroundService](#backgroundservice-base-class), which is a base class for implementing a long running `IHostedService`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=21,25)]

<span data-ttu-id="68977-247">Los servicios se registran en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="68977-247">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="68977-248">La implementación `IHostedService` se registra con el método de extensión `AddHostedService`:</span><span class="sxs-lookup"><span data-stu-id="68977-248">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet3)]

<span data-ttu-id="68977-249">En la clase de modelo de página de índice:</span><span class="sxs-lookup"><span data-stu-id="68977-249">In the Index page model class:</span></span>

* <span data-ttu-id="68977-250">Se inserta `IBackgroundTaskQueue` en el constructor y se asigna a `Queue`.</span><span class="sxs-lookup"><span data-stu-id="68977-250">The `IBackgroundTaskQueue` is injected into the constructor and assigned to `Queue`.</span></span>
* <span data-ttu-id="68977-251">Se inserta <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> y se asigna a `_serviceScopeFactory`.</span><span class="sxs-lookup"><span data-stu-id="68977-251">An <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> is injected and assigned to `_serviceScopeFactory`.</span></span> <span data-ttu-id="68977-252">Se usa el generador se para crear instancias de <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>, que se usa para crear servicios dentro de un ámbito.</span><span class="sxs-lookup"><span data-stu-id="68977-252">The factory is used to create instances of <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>, which is used to create services within a scope.</span></span> <span data-ttu-id="68977-253">Se crea un ámbito para poder usar el elemento `AppDbContext` de la aplicación (un [servicio con ámbito](xref:fundamentals/dependency-injection#service-lifetimes)) para escribir registros de base de datos en `IBackgroundTaskQueue` (un servicio de singleton).</span><span class="sxs-lookup"><span data-stu-id="68977-253">A scope is created in order to use the app's `AppDbContext` (a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes)) to write database records in the `IBackgroundTaskQueue` (a singleton service).</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="68977-254">Cuando se hace clic en el botón **Agregar tarea** en la página de índice, se ejecuta el método `OnPostAddTask`.</span><span class="sxs-lookup"><span data-stu-id="68977-254">When the **Add Task** button is selected on the Index page, the `OnPostAddTask` method is executed.</span></span> <span data-ttu-id="68977-255">Se llama a `QueueBackgroundWorkItem` para poner en cola un elemento de trabajo:</span><span class="sxs-lookup"><span data-stu-id="68977-255">`QueueBackgroundWorkItem` is called to enqueue a work item:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet2)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="68977-256">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="68977-256">Additional resources</span></span>

* [<span data-ttu-id="68977-257">Implementar tareas en segundo plano en microservicios con IHostedService y la clase BackgroundService</span><span class="sxs-lookup"><span data-stu-id="68977-257">Implement background tasks in microservices with IHostedService and the BackgroundService class</span></span>](/dotnet/standard/microservices-architecture/multi-container-microservice-net-applications/background-tasks-with-ihostedservice)
* [<span data-ttu-id="68977-258">Ejecución de tareas en segundo plano con WebJobs en Azure App Service</span><span class="sxs-lookup"><span data-stu-id="68977-258">Run background tasks with WebJobs in Azure App Service</span></span>](/azure/app-service/webjobs-create)
* <xref:System.Threading.Timer>
