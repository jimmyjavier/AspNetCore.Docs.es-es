---
title: Registros en .NET Core y ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo usar la plataforma de registro proporcionada por el paquete NuGet Microsoft.Extensions.Logging.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/17/2020
uid: fundamentals/logging/index
ms.openlocfilehash: a3c63b738d3eaa51249475b88d78572038348a7a
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440745"
---
# <a name="logging-in-net-core-and-aspnet-core"></a><span data-ttu-id="f1527-103">Registros en .NET Core y ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f1527-103">Logging in .NET Core and ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f1527-104">Por [Tom Dykstra](https://github.com/tdykstra) y [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="f1527-104">By [Tom Dykstra](https://github.com/tdykstra) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="f1527-105">.NET Core es compatible con una API de registro que funciona con una gran variedad de proveedores de registro integrados y de terceros.</span><span class="sxs-lookup"><span data-stu-id="f1527-105">.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="f1527-106">En este artículo se muestra cómo usar las API de registro con proveedores integrados.</span><span class="sxs-lookup"><span data-stu-id="f1527-106">This article shows how to use the logging API with built-in providers.</span></span>

<span data-ttu-id="f1527-107">La mayoría de los ejemplos de código que se muestran en este artículo son de aplicaciones de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f1527-107">Most of the code examples shown in this article are from ASP.NET Core apps.</span></span> <span data-ttu-id="f1527-108">Las partes específicas de registro de estos fragmentos de código son válidas para cualquier aplicación de .NET Core que use el [host genérico](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="f1527-108">The logging-specific parts of these code snippets apply to any .NET Core app that uses the [Generic Host](xref:fundamentals/host/generic-host).</span></span> <span data-ttu-id="f1527-109">Para ver un ejemplo de cómo usar el host genérico en una aplicación de consola que no sea web, vea el archivo *Program.cs* de la [aplicación de ejemplo de tareas en segundo plano](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) (<xref:fundamentals/host/hosted-services>).</span><span class="sxs-lookup"><span data-stu-id="f1527-109">For an example of how to use the Generic Host in a non-web console app, see the *Program.cs* file of the [Background Tasks sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) (<xref:fundamentals/host/hosted-services>).</span></span>

<span data-ttu-id="f1527-110">El código de registro para las aplicaciones sin un host genérico es distinto en la forma en que se [agregan los proveedores](#add-providers) y [se crean los registradores](#create-logs).</span><span class="sxs-lookup"><span data-stu-id="f1527-110">Logging code for apps without Generic Host differs in the way [providers are added](#add-providers) and [loggers are created](#create-logs).</span></span> <span data-ttu-id="f1527-111">Los ejemplos de código que no sean de host se muestran en esas secciones del artículo.</span><span class="sxs-lookup"><span data-stu-id="f1527-111">Non-host code examples are shown in those sections of the article.</span></span>

<span data-ttu-id="f1527-112">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f1527-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="add-providers"></a><span data-ttu-id="f1527-113">Incorporación de proveedores</span><span class="sxs-lookup"><span data-stu-id="f1527-113">Add providers</span></span>

<span data-ttu-id="f1527-114">Un proveedor de registro muestra o almacena registros.</span><span class="sxs-lookup"><span data-stu-id="f1527-114">A logging provider displays or stores logs.</span></span> <span data-ttu-id="f1527-115">Por ejemplo, el proveedor de consola muestra los registros en la consola y el proveedor de Azure Application Insights los almacena en Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="f1527-115">For example, the Console provider displays logs on the console, and the Azure Application Insights provider stores them in Azure Application Insights.</span></span> <span data-ttu-id="f1527-116">Los registros se pueden enviar a varios destinos mediante la incorporación de varios proveedores.</span><span class="sxs-lookup"><span data-stu-id="f1527-116">Logs can be sent to multiple destinations by adding multiple providers.</span></span>

<span data-ttu-id="f1527-117">Para agregar un proveedor en una aplicación que use un host genérico, llame al método de extensión `Add{provider name}` del proveedor en *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="f1527-117">To add a provider in an app that uses Generic Host, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=6)]

<span data-ttu-id="f1527-118">En una aplicación de consola que no sea de host, llame al método de extensión `Add{provider name}` del proveedor al crear un elemento `LoggerFactory`:</span><span class="sxs-lookup"><span data-stu-id="f1527-118">In a non-host console app, call the provider's `Add{provider name}` extension method while creating a `LoggerFactory`:</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=1,7)]

<span data-ttu-id="f1527-119">`LoggerFactory` y `AddConsole` necesitan una instrucción `using` para `Microsoft.Extensions.Logging`.</span><span class="sxs-lookup"><span data-stu-id="f1527-119">`LoggerFactory` and `AddConsole` require a `using` statement for `Microsoft.Extensions.Logging`.</span></span>

<span data-ttu-id="f1527-120">Las plantillas de proyecto predeterminadas de ASP.NET Core llaman a <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>, que agrega los siguientes proveedores de registro:</span><span class="sxs-lookup"><span data-stu-id="f1527-120">The default ASP.NET Core project templates call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* [<span data-ttu-id="f1527-121">Consola</span><span class="sxs-lookup"><span data-stu-id="f1527-121">Console</span></span>](#console-provider)
* [<span data-ttu-id="f1527-122">Depurar</span><span class="sxs-lookup"><span data-stu-id="f1527-122">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="f1527-123">EventSource</span><span class="sxs-lookup"><span data-stu-id="f1527-123">EventSource</span></span>](#event-source-provider)
* <span data-ttu-id="f1527-124">[EventLog](#windows-eventlog-provider) (solo si se ejecuta en Windows)</span><span class="sxs-lookup"><span data-stu-id="f1527-124">[EventLog](#windows-eventlog-provider) (only when running on Windows)</span></span>

<span data-ttu-id="f1527-125">Puede reemplazar los proveedores predeterminados por sus propios valores.</span><span class="sxs-lookup"><span data-stu-id="f1527-125">You can replace the default providers with your own choices.</span></span> <span data-ttu-id="f1527-126">Llame a <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> y agregue los proveedores que desee.</span><span class="sxs-lookup"><span data-stu-id="f1527-126">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=5)]

<span data-ttu-id="f1527-127">Obtenga más información sobre los [proveedores de registro integrados](#built-in-logging-providers) y los [proveedores de registro de terceros](#third-party-logging-providers) más adelante en el artículo.</span><span class="sxs-lookup"><span data-stu-id="f1527-127">Learn more about [built-in logging providers](#built-in-logging-providers) and [third-party logging providers](#third-party-logging-providers) later in the article.</span></span>

## <a name="create-logs"></a><span data-ttu-id="f1527-128">Creación de registros</span><span class="sxs-lookup"><span data-stu-id="f1527-128">Create logs</span></span>

<span data-ttu-id="f1527-129">Para crear registros, use un objeto de <xref:Microsoft.Extensions.Logging.ILogger%601>.</span><span class="sxs-lookup"><span data-stu-id="f1527-129">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object.</span></span> <span data-ttu-id="f1527-130">En una aplicación web o servicio hospedado, obtenga un elemento `ILogger` de la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="f1527-130">In a web app or hosted service, get an `ILogger` from dependency injection (DI).</span></span> <span data-ttu-id="f1527-131">En aplicaciones de consola que no sean de host, use `LoggerFactory` para crear un elemento `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="f1527-131">In non-host console apps, use the `LoggerFactory` to create an `ILogger`.</span></span>

<span data-ttu-id="f1527-132">En el ejemplo siguiente de ASP.NET Core, se crea un registrador con `TodoApiSample.Pages.AboutModel` como la categoría.</span><span class="sxs-lookup"><span data-stu-id="f1527-132">The following ASP.NET Core example creates a logger with `TodoApiSample.Pages.AboutModel` as the category.</span></span> <span data-ttu-id="f1527-133">La *categoría* de registro es una cadena que está asociada con cada registro.</span><span class="sxs-lookup"><span data-stu-id="f1527-133">The log *category* is a string that is associated with each log.</span></span> <span data-ttu-id="f1527-134">La instancia de `ILogger<T>` proporcionada por la inserción de dependencias genera registros que tienen el nombre completo del tipo `T` como la categoría.</span><span class="sxs-lookup"><span data-stu-id="f1527-134">The `ILogger<T>` instance provided by DI creates logs that have the fully qualified name of type `T` as the category.</span></span> 

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

<span data-ttu-id="f1527-135">En el siguiente ejemplo de aplicación de consola que no es de host, se crea un registrador con `LoggingConsoleApp.Program` como la categoría.</span><span class="sxs-lookup"><span data-stu-id="f1527-135">The following non-host console app example creates a logger with `LoggingConsoleApp.Program` as the category.</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=10)]

<span data-ttu-id="f1527-136">En los siguientes ejemplos de aplicación de consola y ASP.NET Core, el registrador se usa para crear registros con `Information` como el nivel.</span><span class="sxs-lookup"><span data-stu-id="f1527-136">In the following ASP.NET Core and console app examples, the logger is used to create logs with `Information` as the level.</span></span> <span data-ttu-id="f1527-137">El *nivel* de registro indica la gravedad del evento registrado.</span><span class="sxs-lookup"><span data-stu-id="f1527-137">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=11)]

<span data-ttu-id="f1527-138">Los [niveles](#log-level) y las [categorías](#log-category) se explican detalladamente más adelante en este artículo.</span><span class="sxs-lookup"><span data-stu-id="f1527-138">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this article.</span></span>

### <a name="create-logs-in-the-program-class"></a><span data-ttu-id="f1527-139">Crear registros en la clase del programa</span><span class="sxs-lookup"><span data-stu-id="f1527-139">Create logs in the Program class</span></span>

<span data-ttu-id="f1527-140">Para escribir registros en la clase `Program` de una aplicación de ASP.NET Core, obtenga una instancia de `ILogger` desde la inserción de dependencias después de generar el host:</span><span class="sxs-lookup"><span data-stu-id="f1527-140">To write logs in the `Program` class of an ASP.NET Core app, get an `ILogger` instance from DI after building the host:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/TodoApiSample/Program.cs?highlight=9,10)]

<span data-ttu-id="f1527-141">No se admite directamente el registro durante la construcción del host.</span><span class="sxs-lookup"><span data-stu-id="f1527-141">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="f1527-142">Sin embargo, se puede usar un registrador independiente.</span><span class="sxs-lookup"><span data-stu-id="f1527-142">However, a separate logger can be used.</span></span> <span data-ttu-id="f1527-143">En el ejemplo siguiente, se usa un registrador [Serilog](https://serilog.net/) para registrarse en `CreateHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="f1527-143">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateHostBuilder`.</span></span> <span data-ttu-id="f1527-144">`AddSerilog` usa la configuración estática especificada en `Log.Logger`:</span><span class="sxs-lookup"><span data-stu-id="f1527-144">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

```csharp
using System;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Hosting;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args)
    {
        var builtConfig = new ConfigurationBuilder()
            .AddJsonFile("appsettings.json")
            .AddCommandLine(args)
            .Build();

        Log.Logger = new LoggerConfiguration()
            .WriteTo.Console()
            .WriteTo.File(builtConfig["Logging:FilePath"])
            .CreateLogger();

        try
        {
            return Host.CreateDefaultBuilder(args)
                .ConfigureServices((context, services) =>
                {
                    services.AddRazorPages();
                })
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    config.AddConfiguration(builtConfig);
                })
                .ConfigureLogging(logging =>
                {   
                    logging.AddSerilog();
                })
                .ConfigureWebHostDefaults(webBuilder =>
                {
                    webBuilder.UseStartup<Startup>();
                });
        }
        catch (Exception ex)
        {
            Log.Fatal(ex, "Host builder error");

            throw;
        }
        finally
        {
            Log.CloseAndFlush();
        }
    }
}
```

### <a name="create-logs-in-the-startup-class"></a><span data-ttu-id="f1527-145">Crea registros en la clase de inicio</span><span class="sxs-lookup"><span data-stu-id="f1527-145">Create logs in the Startup class</span></span>

<span data-ttu-id="f1527-146">Para escribir registros en el método `Startup.Configure` de una aplicación de ASP.NET Core, incluya un parámetro `ILogger` en la signatura del método:</span><span class="sxs-lookup"><span data-stu-id="f1527-146">To write logs in the `Startup.Configure` method of an ASP.NET Core app, include an `ILogger` parameter in the method signature:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_Configure&highlight=1,5)]

<span data-ttu-id="f1527-147">No se admite la escritura de registros antes de la finalización del contenedor de inserción de dependencias configurado en el método `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="f1527-147">Writing logs before completion of the DI container setup in the `Startup.ConfigureServices` method is not supported:</span></span>

* <span data-ttu-id="f1527-148">No se admite la inyección del registrador en el constructor `Startup`.</span><span class="sxs-lookup"><span data-stu-id="f1527-148">Logger injection into the `Startup` constructor is not supported.</span></span>
* <span data-ttu-id="f1527-149">No se admite la inyección del registrador en la signatura del método `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="f1527-149">Logger injection into the `Startup.ConfigureServices` method signature is not supported</span></span>

<span data-ttu-id="f1527-150">El motivo de esta restricción es que los registros dependen de la inserción de dependencias y de la configuración, que a su vez depende de la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="f1527-150">The reason for this restriction is that logging depends on DI and on configuration, which in turns depends on DI.</span></span> <span data-ttu-id="f1527-151">El contenedor de inserción de dependencias no se configura hasta que finaliza `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="f1527-151">The DI container isn't set up until `ConfigureServices` finishes.</span></span>

<span data-ttu-id="f1527-152">La inserción del constructor de un registrador en `Startup` funciona en versiones anteriores de ASP.NET Core, ya que se crea un contenedor de inserción de dependencias independiente para el host de web.</span><span class="sxs-lookup"><span data-stu-id="f1527-152">Constructor injection of a logger into `Startup` works in earlier versions of ASP.NET Core because a separate DI container is created for the Web Host.</span></span> <span data-ttu-id="f1527-153">Para conocer por qué solo se crea un contenedor para el host genérico, vea el [anuncio de cambios importantes](https://github.com/aspnet/Announcements/issues/353).</span><span class="sxs-lookup"><span data-stu-id="f1527-153">For information about why only one container is created for the Generic Host, see the [breaking change announcement](https://github.com/aspnet/Announcements/issues/353).</span></span>

<span data-ttu-id="f1527-154">Si necesita configurar un servicio que dependa de `ILogger<T>`, puede hacerlo si usa la inserción del constructor, o bien si proporciona un Factory Method.</span><span class="sxs-lookup"><span data-stu-id="f1527-154">If you need to configure a service that depends on `ILogger<T>`, you can still do that by using constructor injection or by providing a factory method.</span></span> <span data-ttu-id="f1527-155">Usar un Factory Method es la opción recomendada si no tiene otra alternativa.</span><span class="sxs-lookup"><span data-stu-id="f1527-155">The factory method approach is recommended only if there is no other option.</span></span> <span data-ttu-id="f1527-156">Por ejemplo, imagine que necesita rellenar una propiedad con un servicio desde la inserción de dependencias:</span><span class="sxs-lookup"><span data-stu-id="f1527-156">For example, suppose you need to fill a property with a service from DI:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

<span data-ttu-id="f1527-157">El código resaltado anterior es un elemento `Func` que se ejecuta la primera vez que el contenedor de inserción de dependencias necesita crear una instancia de `MyService`.</span><span class="sxs-lookup"><span data-stu-id="f1527-157">The preceding highlighted code is a `Func` that runs the first time the DI container needs to construct an instance of `MyService`.</span></span> <span data-ttu-id="f1527-158">Puede acceder a cualquiera de los servicios registrados de esta forma.</span><span class="sxs-lookup"><span data-stu-id="f1527-158">You can access any of the registered services in this way.</span></span>

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="f1527-159">No hay métodos de registrador asincrónicos</span><span class="sxs-lookup"><span data-stu-id="f1527-159">No asynchronous logger methods</span></span>

<span data-ttu-id="f1527-160">El registro debe ser tan rápido que no merezca la pena el costo de rendimiento del código asincrónico.</span><span class="sxs-lookup"><span data-stu-id="f1527-160">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="f1527-161">Si el almacén de datos de registro es lento, no escriba directamente en él.</span><span class="sxs-lookup"><span data-stu-id="f1527-161">If your logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="f1527-162">Considere la posibilidad de escribir los mensajes de registro en un almacén rápido inicialmente y luego moverlos a la tienda lenta.</span><span class="sxs-lookup"><span data-stu-id="f1527-162">Consider writing the log messages to a fast store initially, then move them to the slow store later.</span></span> <span data-ttu-id="f1527-163">Por ejemplo, si inicia sesión en SQL Server, no desea hacerlo directamente en un método `Log`, ya que los métodos `Log` son sincrónicos.</span><span class="sxs-lookup"><span data-stu-id="f1527-163">For example, if you're logging to SQL Server, you don't want to do that directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="f1527-164">En su lugar, agregue sincrónicamente mensajes de registro a una cola en memoria y haga que un trabajo en segundo plano extraiga los mensajes de la cola para realizar el trabajo asincrónico de insertar datos en SQL Server.</span><span class="sxs-lookup"><span data-stu-id="f1527-164">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span> <span data-ttu-id="f1527-165">Para obtener más información, vea [este](https://github.com/dotnet/AspNetCore.Docs/issues/11801) problema de GitHub.</span><span class="sxs-lookup"><span data-stu-id="f1527-165">For more information, see [this](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub issue.</span></span>

## <a name="configuration"></a><span data-ttu-id="f1527-166">Configuración</span><span class="sxs-lookup"><span data-stu-id="f1527-166">Configuration</span></span>

<span data-ttu-id="f1527-167">Uno o varios proveedores de configuración proporcionan la configuración del proveedor de registro:</span><span class="sxs-lookup"><span data-stu-id="f1527-167">Logging provider configuration is provided by one or more configuration providers:</span></span>

* <span data-ttu-id="f1527-168">Formatos de archivo (INI, JSON y XML).</span><span class="sxs-lookup"><span data-stu-id="f1527-168">File formats (INI, JSON, and XML).</span></span>
* <span data-ttu-id="f1527-169">Argumentos de la línea de comandos.</span><span class="sxs-lookup"><span data-stu-id="f1527-169">Command-line arguments.</span></span>
* <span data-ttu-id="f1527-170">Variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="f1527-170">Environment variables.</span></span>
* <span data-ttu-id="f1527-171">Objetos de .NET en memoria.</span><span class="sxs-lookup"><span data-stu-id="f1527-171">In-memory .NET objects.</span></span>
* <span data-ttu-id="f1527-172">El almacenamiento de [administrador secreto](xref:security/app-secrets) sin cifrar.</span><span class="sxs-lookup"><span data-stu-id="f1527-172">The unencrypted [Secret Manager](xref:security/app-secrets) storage.</span></span>
* <span data-ttu-id="f1527-173">Un almacén de usuario cifrado, como [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="f1527-173">An encrypted user store, such as [Azure Key Vault](xref:security/key-vault-configuration).</span></span>
* <span data-ttu-id="f1527-174">Proveedores personalizados (instalados o creados).</span><span class="sxs-lookup"><span data-stu-id="f1527-174">Custom providers (installed or created).</span></span>

<span data-ttu-id="f1527-175">Por ejemplo, la sección `Logging` de archivos de configuración de aplicación suele proporcionar la configuración de registro.</span><span class="sxs-lookup"><span data-stu-id="f1527-175">For example, logging configuration is commonly provided by the `Logging` section of app settings files.</span></span> <span data-ttu-id="f1527-176">En el ejemplo siguiente se muestra el contenido de un archivo *appsettings.Development.json* típico:</span><span class="sxs-lookup"><span data-stu-id="f1527-176">The following example shows the contents of a typical *appsettings.Development.json* file:</span></span>

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Debug",
      "System": "Information",
      "Microsoft": "Information"
    },
    "Console":
    {
      "IncludeScopes": true
    }
  }
}
```

<span data-ttu-id="f1527-177">La propiedad `Logging` puede tener `LogLevel` y propiedades del proveedor de registro (se muestra la consola).</span><span class="sxs-lookup"><span data-stu-id="f1527-177">The `Logging` property can have `LogLevel` and log provider properties (Console is shown).</span></span>

<span data-ttu-id="f1527-178">La propiedad `LogLevel` bajo `Logging` especifica el [nivel](#log-level) mínimo que se va a registrar para las categorías seleccionadas.</span><span class="sxs-lookup"><span data-stu-id="f1527-178">The `LogLevel` property under `Logging` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="f1527-179">En el ejemplo, las categorías `System` y `Microsoft` se registran en el nivel `Information` y todas las demás se registran en el nivel `Debug`.</span><span class="sxs-lookup"><span data-stu-id="f1527-179">In the example, `System` and `Microsoft` categories log at `Information` level, and all others log at `Debug` level.</span></span>

<span data-ttu-id="f1527-180">Otras propiedades bajo `Logging` especifican proveedores de registro.</span><span class="sxs-lookup"><span data-stu-id="f1527-180">Other properties under `Logging` specify logging providers.</span></span> <span data-ttu-id="f1527-181">El ejemplo es para el proveedor de consola.</span><span class="sxs-lookup"><span data-stu-id="f1527-181">The example is for the Console provider.</span></span> <span data-ttu-id="f1527-182">Si un proveedor admite [ámbitos de registro](#log-scopes), `IncludeScopes` indica si están habilitados.</span><span class="sxs-lookup"><span data-stu-id="f1527-182">If a provider supports [log scopes](#log-scopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="f1527-183">Una propiedad de proveedor (como `Console` en el ejemplo) también puede especificar una propiedad `LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="f1527-183">A provider property (such as `Console` in the example) may also specify a `LogLevel` property.</span></span> <span data-ttu-id="f1527-184">`LogLevel` en un proveedor especifica niveles de registro para ese proveedor.</span><span class="sxs-lookup"><span data-stu-id="f1527-184">`LogLevel` under a provider specifies levels to log for that provider.</span></span>

<span data-ttu-id="f1527-185">Si los niveles se especifican en `Logging.{providername}.LogLevel`, invalidan todo lo establecido en `Logging.LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="f1527-185">If levels are specified in `Logging.{providername}.LogLevel`, they override anything set in `Logging.LogLevel`.</span></span>

<span data-ttu-id="f1527-186">La API de registro no incluye un escenario que permita cambiar los niveles de registro mientras se ejecuta una aplicación.</span><span class="sxs-lookup"><span data-stu-id="f1527-186">The Logging API doesn't include a scenario to change log levels while an app is running.</span></span> <span data-ttu-id="f1527-187">No obstante, algunos proveedores de configuración pueden volver a cargar la configuración, lo que tiene efecto inmediato en la configuración del registro.</span><span class="sxs-lookup"><span data-stu-id="f1527-187">However, some configuration providers are capable of reloading configuration, which takes immediate effect on logging configuration.</span></span> <span data-ttu-id="f1527-188">Por ejemplo, [FileConfigurationProvider](xref:fundamentals/configuration/index#file-configuration-provider) —agregado por `CreateDefaultBuilder` para leer los archivos de configuración— vuelve a cargar la configuración de registro de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="f1527-188">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider), which is added by `CreateDefaultBuilder` to read settings files, reloads logging configuration by default.</span></span> <span data-ttu-id="f1527-189">Si se cambia la configuración en el código mientras se ejecuta una aplicación, la aplicación puede llamar a [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) para actualizar la configuración de registro de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f1527-189">If configuration is changed in code while an app is running, the app can call [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) to update the app's logging configuration.</span></span>

<span data-ttu-id="f1527-190">Para obtener información sobre cómo implementar proveedores de configuración, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="f1527-190">For information on implementing configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="sample-logging-output"></a><span data-ttu-id="f1527-191">Salida de registro de ejemplo</span><span class="sxs-lookup"><span data-stu-id="f1527-191">Sample logging output</span></span>

<span data-ttu-id="f1527-192">Con el código de ejemplo que se muestra en la sección anterior, los registros aparecen en la consola cuando la aplicación se ejecuta desde la línea de comandos.</span><span class="sxs-lookup"><span data-stu-id="f1527-192">With the sample code shown in the preceding section, logs appear in the console when the app is run from the command line.</span></span> <span data-ttu-id="f1527-193">Este es un ejemplo de salida de la consola:</span><span class="sxs-lookup"><span data-stu-id="f1527-193">Here's an example of console output:</span></span>

```console
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/1.1 GET http://localhost:5000/api/todo/0
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 84.26180000000001ms 307
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 GET https://localhost:5001/api/todo/0
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[3]
      Route matched with {action = "GetById", controller = "Todo", page = ""}. Executing controller action with signature Microsoft.AspNetCore.Mvc.IActionResult GetById(System.String) on controller TodoApiSample.Controllers.TodoController (TodoApiSample).
info: TodoApiSample.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
```

<span data-ttu-id="f1527-194">Los registros anteriores se generaron mediante la realización de una solicitud HTTP GET a la aplicación de ejemplo en `http://localhost:5000/api/todo/0`.</span><span class="sxs-lookup"><span data-stu-id="f1527-194">The preceding logs were generated by making an HTTP Get request to the sample app at `http://localhost:5000/api/todo/0`.</span></span>

<span data-ttu-id="f1527-195">Este es un ejemplo de los mismos registros tal y como aparecen en la ventana de depuración cuando se ejecuta la aplicación de ejemplo en Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="f1527-195">Here's an example of the same logs as they appear in the Debug window when you run the sample app in Visual Studio:</span></span>

```console
Microsoft.AspNetCore.Hosting.Diagnostics: Information: Request starting HTTP/2.0 GET https://localhost:44328/api/todo/0  
Microsoft.AspNetCore.Routing.EndpointMiddleware: Information: Executing endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker: Information: Route matched with {action = "GetById", controller = "Todo", page = ""}. Executing controller action with signature Microsoft.AspNetCore.Mvc.IActionResult GetById(System.String) on controller TodoApiSample.Controllers.TodoController (TodoApiSample).
TodoApiSample.Controllers.TodoController: Information: Getting item 0
TodoApiSample.Controllers.TodoController: Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult: Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker: Information: Executed action TodoApiSample.Controllers.TodoController.GetById (TodoApiSample) in 34.167ms
Microsoft.AspNetCore.Routing.EndpointMiddleware: Information: Executed endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
Microsoft.AspNetCore.Hosting.Diagnostics: Information: Request finished in 98.41300000000001ms 404
```

<span data-ttu-id="f1527-196">Los registros creados por las llamadas de `ILogger` se muestran en la sección anterior, empezando por “TodoApiSample”.</span><span class="sxs-lookup"><span data-stu-id="f1527-196">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApiSample".</span></span> <span data-ttu-id="f1527-197">Los registros que comienzan por categorías de "Microsoft" son del código de marco de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f1527-197">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="f1527-198">ASP.NET Core y el código de la aplicación usan la misma API y los mismos proveedores de registro.</span><span class="sxs-lookup"><span data-stu-id="f1527-198">ASP.NET Core and application code are using the same logging API and providers.</span></span>

<span data-ttu-id="f1527-199">En el resto de este artículo se explican algunos detalles y opciones para el registro.</span><span class="sxs-lookup"><span data-stu-id="f1527-199">The remainder of this article explains some details and options for logging.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="f1527-200">Paquetes NuGet</span><span class="sxs-lookup"><span data-stu-id="f1527-200">NuGet packages</span></span>

<span data-ttu-id="f1527-201">Las interfaces `ILogger` e `ILoggerFactory` se encuentran en [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), y sus implementaciones predeterminadas en [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span><span class="sxs-lookup"><span data-stu-id="f1527-201">The `ILogger` and `ILoggerFactory` interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), and default implementations for them are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

## <a name="log-category"></a><span data-ttu-id="f1527-202">Categoría de registro</span><span class="sxs-lookup"><span data-stu-id="f1527-202">Log category</span></span>

<span data-ttu-id="f1527-203">Cuando se crea un objeto `ILogger`, se ha especificado una *categoría* para él.</span><span class="sxs-lookup"><span data-stu-id="f1527-203">When an `ILogger` object is created, a *category* is specified for it.</span></span> <span data-ttu-id="f1527-204">Esa categoría se incluye con cada mensaje de registro creado por esa instancia de `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="f1527-204">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="f1527-205">La categoría puede ser cualquier cadena, pero la convención es usar el nombre de clase, como "TodoApi.Controllers.TodoController".</span><span class="sxs-lookup"><span data-stu-id="f1527-205">The category may be any string, but the convention is to use the class name, such as "TodoApi.Controllers.TodoController".</span></span>

<span data-ttu-id="f1527-206">Use `ILogger<T>` para obtener una instancia `ILogger` que utiliza el nombre de tipo completo de `T` como la categoría:</span><span class="sxs-lookup"><span data-stu-id="f1527-206">Use `ILogger<T>` to get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

<span data-ttu-id="f1527-207">Para especificar explícitamente la categoría, llame a `ILoggerFactory.CreateLogger`:</span><span class="sxs-lookup"><span data-stu-id="f1527-207">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

<span data-ttu-id="f1527-208">`ILogger<T>` es equivale a llamar a `CreateLogger` con el nombre de tipo completo de `T`.</span><span class="sxs-lookup"><span data-stu-id="f1527-208">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

## <a name="log-level"></a><span data-ttu-id="f1527-209">Nivel de registro</span><span class="sxs-lookup"><span data-stu-id="f1527-209">Log level</span></span>

<span data-ttu-id="f1527-210">Cara registro especifica un valor <xref:Microsoft.Extensions.Logging.LogLevel>.</span><span class="sxs-lookup"><span data-stu-id="f1527-210">Every log specifies a <xref:Microsoft.Extensions.Logging.LogLevel> value.</span></span> <span data-ttu-id="f1527-211">El nivel de registro indica la gravedad o importancia.</span><span class="sxs-lookup"><span data-stu-id="f1527-211">The log level indicates the severity or importance.</span></span> <span data-ttu-id="f1527-212">Por ejemplo, podría escribir un registro `Information` cuando un método termina con normalidad y un registro `Warning` cuando un método devuelve un código de estado *404 No encontrado*.</span><span class="sxs-lookup"><span data-stu-id="f1527-212">For example, you might write an `Information` log when a method ends normally and a `Warning` log when a method returns a *404 Not Found* status code.</span></span>

<span data-ttu-id="f1527-213">El siguiente código crea los registros `Information` y `Warning`:</span><span class="sxs-lookup"><span data-stu-id="f1527-213">The following code creates `Information` and `Warning` logs:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="f1527-214">En el código anterior, el primer parámetro es el [id. de evento del registro](#log-event-id).</span><span class="sxs-lookup"><span data-stu-id="f1527-214">In the preceding code, the first parameter is the [Log event ID](#log-event-id).</span></span> <span data-ttu-id="f1527-215">El segundo parámetro es una plantilla de mensaje con marcadores de posición para los valores de argumento proporcionados por el resto de parámetros de método.</span><span class="sxs-lookup"><span data-stu-id="f1527-215">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="f1527-216">Los parámetros de método se explican detalladamente en la [sección de la plantilla de mensaje](#log-message-template) más adelante en este artículo.</span><span class="sxs-lookup"><span data-stu-id="f1527-216">The method parameters are explained in the [message template section](#log-message-template) later in this article.</span></span>

<span data-ttu-id="f1527-217">Los métodos de registro que incluyen el nivel en el nombre del método (por ejemplo `LogInformation` y `LogWarning`) son [métodos de extensión para ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span><span class="sxs-lookup"><span data-stu-id="f1527-217">Log methods that include the level in the method name (for example, `LogInformation` and `LogWarning`) are [extension methods for ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span></span> <span data-ttu-id="f1527-218">Estos métodos llaman a un método `Log` que toma un parámetro `LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="f1527-218">These methods call a `Log` method that takes a `LogLevel` parameter.</span></span> <span data-ttu-id="f1527-219">Puede llamar directamente al método `Log` en lugar de a uno de estos métodos de extensión, pero la sintaxis es relativamente complicada.</span><span class="sxs-lookup"><span data-stu-id="f1527-219">You can call the `Log` method directly rather than one of these extension methods, but the syntax is relatively complicated.</span></span> <span data-ttu-id="f1527-220">Para más información, vea la <xref:Microsoft.Extensions.Logging.ILogger> y el [código fuente de las extensiones de registrador](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="f1527-220">For more information, see <xref:Microsoft.Extensions.Logging.ILogger> and the [logger extensions source code](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span>

<span data-ttu-id="f1527-221">ASP.NET Core define los niveles de registro siguientes, que aquí se ordenan de menor a mayor gravedad.</span><span class="sxs-lookup"><span data-stu-id="f1527-221">ASP.NET Core defines the following log levels, ordered here from lowest to highest severity.</span></span>

* <span data-ttu-id="f1527-222">Seguimiento = 0</span><span class="sxs-lookup"><span data-stu-id="f1527-222">Trace = 0</span></span>

  <span data-ttu-id="f1527-223">Para información que normalmente solo es útil para la depuración.</span><span class="sxs-lookup"><span data-stu-id="f1527-223">For information that's typically valuable only for debugging.</span></span> <span data-ttu-id="f1527-224">Estos mensajes pueden contener datos confidenciales de la aplicación, por lo que no deben habilitarse en un entorno de producción.</span><span class="sxs-lookup"><span data-stu-id="f1527-224">These messages may contain sensitive application data and so shouldn't be enabled in a production environment.</span></span> <span data-ttu-id="f1527-225">*Deshabilitado de forma predeterminada.*</span><span class="sxs-lookup"><span data-stu-id="f1527-225">*Disabled by default.*</span></span>

* <span data-ttu-id="f1527-226">Depurar = 1</span><span class="sxs-lookup"><span data-stu-id="f1527-226">Debug = 1</span></span>

  <span data-ttu-id="f1527-227">Para información que puede ser útil para el desarrollo y la depuración.</span><span class="sxs-lookup"><span data-stu-id="f1527-227">For information that may be useful in development and debugging.</span></span> <span data-ttu-id="f1527-228">Ejemplo: `Entering method Configure with flag set to true.` Habilite los registros de nivel `Debug` en producción cuando esté solucionando un problema, debido al elevado volumen de registros.</span><span class="sxs-lookup"><span data-stu-id="f1527-228">Example: `Entering method Configure with flag set to true.` Enable `Debug` level logs in production only when troubleshooting, due to the high volume of logs.</span></span>

* <span data-ttu-id="f1527-229">Información = 2</span><span class="sxs-lookup"><span data-stu-id="f1527-229">Information = 2</span></span>

  <span data-ttu-id="f1527-230">Para realizar el seguimiento del flujo general de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f1527-230">For tracking the general flow of the app.</span></span> <span data-ttu-id="f1527-231">Estos registros suelen tener algún valor a largo plazo.</span><span class="sxs-lookup"><span data-stu-id="f1527-231">These logs typically have some long-term value.</span></span> <span data-ttu-id="f1527-232">Ejemplo: `Request received for path /api/todo`</span><span class="sxs-lookup"><span data-stu-id="f1527-232">Example: `Request received for path /api/todo`</span></span>

* <span data-ttu-id="f1527-233">Advertencia = 3</span><span class="sxs-lookup"><span data-stu-id="f1527-233">Warning = 3</span></span>

  <span data-ttu-id="f1527-234">Para los eventos anómalos o inesperados en el flujo de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f1527-234">For abnormal or unexpected events in the app flow.</span></span> <span data-ttu-id="f1527-235">Estos pueden incluir errores u otras condiciones que no hacen que la aplicación se detenga, pero que puede que sea necesario investigar.</span><span class="sxs-lookup"><span data-stu-id="f1527-235">These may include errors or other conditions that don't cause the app to stop but might need to be investigated.</span></span> <span data-ttu-id="f1527-236">Las excepciones controladas son un lugar común para usar el nivel de registro `Warning`.</span><span class="sxs-lookup"><span data-stu-id="f1527-236">Handled exceptions are a common place to use the `Warning` log level.</span></span> <span data-ttu-id="f1527-237">Ejemplo: `FileNotFoundException for file quotes.txt.`</span><span class="sxs-lookup"><span data-stu-id="f1527-237">Example: `FileNotFoundException for file quotes.txt.`</span></span>

* <span data-ttu-id="f1527-238">Error = 4</span><span class="sxs-lookup"><span data-stu-id="f1527-238">Error = 4</span></span>

  <span data-ttu-id="f1527-239">Para los errores y excepciones que no se pueden controlar.</span><span class="sxs-lookup"><span data-stu-id="f1527-239">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="f1527-240">Estos mensajes indican un error en la actividad u operación actual (por ejemplo, la solicitud HTTP actual), no un error de toda la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f1527-240">These messages indicate a failure in the current activity or operation (such as the current HTTP request), not an app-wide failure.</span></span> <span data-ttu-id="f1527-241">Mensaje de registro de ejemplo: `Cannot insert record due to duplicate key violation.`</span><span class="sxs-lookup"><span data-stu-id="f1527-241">Example log message: `Cannot insert record due to duplicate key violation.`</span></span>

* <span data-ttu-id="f1527-242">Crítico = 5</span><span class="sxs-lookup"><span data-stu-id="f1527-242">Critical = 5</span></span>

  <span data-ttu-id="f1527-243">Para los errores que requieren atención inmediata.</span><span class="sxs-lookup"><span data-stu-id="f1527-243">For failures that require immediate attention.</span></span> <span data-ttu-id="f1527-244">Ejemplos: escenarios de pérdida de datos, espacio en disco insuficiente.</span><span class="sxs-lookup"><span data-stu-id="f1527-244">Examples: data loss scenarios, out of disk space.</span></span>

<span data-ttu-id="f1527-245">Use el nivel de registro para controlar la cantidad de salida del registro que se escribe en un medio de almacenamiento determinado o ventana de presentación.</span><span class="sxs-lookup"><span data-stu-id="f1527-245">Use the log level to control how much log output is written to a particular storage medium or display window.</span></span> <span data-ttu-id="f1527-246">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="f1527-246">For example:</span></span>

* <span data-ttu-id="f1527-247">En producción:</span><span class="sxs-lookup"><span data-stu-id="f1527-247">In production:</span></span>
  * <span data-ttu-id="f1527-248">El registro en los niveles `Trace` a `Information` genera un gran volumen de mensajes de registro detallados.</span><span class="sxs-lookup"><span data-stu-id="f1527-248">Logging at the `Trace` through `Information` levels produces a high-volume of detailed log messages.</span></span> <span data-ttu-id="f1527-249">Para controlar los costos y no superar los límites de almacenamiento de datos, registre los mensajes de nivel `Trace` a `Information` en un almacén de datos de alto volumen y bajo costo.</span><span class="sxs-lookup"><span data-stu-id="f1527-249">To control costs and not exceed data storage limits, log `Trace` through `Information` level messages to a high-volume, low-cost data store.</span></span>
  * <span data-ttu-id="f1527-250">El registro en los niveles `Warning` a `Critical` normalmente produce menos mensajes de registro y de menor tamaño.</span><span class="sxs-lookup"><span data-stu-id="f1527-250">Logging at `Warning` through `Critical` levels typically produces fewer, smaller log messages.</span></span> <span data-ttu-id="f1527-251">Por lo tanto, los costos y los límites de almacenamiento no suelen ser un problema, lo que da lugar a una mayor flexibilidad a la hora de elegir el almacén de datos.</span><span class="sxs-lookup"><span data-stu-id="f1527-251">Therefore, costs and storage limits usually aren't a concern, which results in greater flexibility of data store choice.</span></span>
* <span data-ttu-id="f1527-252">Durante el desarrollo:</span><span class="sxs-lookup"><span data-stu-id="f1527-252">During development:</span></span>
  * <span data-ttu-id="f1527-253">Registre los mensajes `Warning` a `Critical` en la consola.</span><span class="sxs-lookup"><span data-stu-id="f1527-253">Log `Warning` through `Critical` messages to the console.</span></span>
  * <span data-ttu-id="f1527-254">Agregue los mensajes `Trace` a `Information` al solucionar problemas.</span><span class="sxs-lookup"><span data-stu-id="f1527-254">Add `Trace` through `Information` messages when troubleshooting.</span></span>

<span data-ttu-id="f1527-255">En la sección [Filtrado del registro](#log-filtering) de este artículo se explica cómo controlar los niveles de registro que controla un proveedor.</span><span class="sxs-lookup"><span data-stu-id="f1527-255">The [Log filtering](#log-filtering) section later in this article explains how to control which log levels a provider handles.</span></span>

<span data-ttu-id="f1527-256">ASP.NET Core escribe registros de eventos de marco.</span><span class="sxs-lookup"><span data-stu-id="f1527-256">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="f1527-257">En los ejemplos de registro anteriores de este artículo se excluyeron los registros por debajo del nivel `Information`, por lo que no se crearon los registros de nivel `Debug` o `Trace`.</span><span class="sxs-lookup"><span data-stu-id="f1527-257">The log examples earlier in this article excluded logs below `Information` level, so no `Debug` or `Trace` level logs were created.</span></span> <span data-ttu-id="f1527-258">Este es un ejemplo de registros de consola generados mediante la ejecución de la aplicación de ejemplo configurada para mostrar registros `Debug`:</span><span class="sxs-lookup"><span data-stu-id="f1527-258">Here's an example of console logs produced by running the sample app configured to show `Debug` logs:</span></span>

```console
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[3]
      Route matched with {action = "GetById", controller = "Todo", page = ""}. Executing controller action with signature Microsoft.AspNetCore.Mvc.IActionResult GetById(System.String) on controller TodoApiSample.Controllers.TodoController (TodoApiSample).
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of authorization filters (in the following order): None
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of resource filters (in the following order): Microsoft.AspNetCore.Mvc.ViewFeatures.Filters.SaveTempDataFilter
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of action filters (in the following order): Microsoft.AspNetCore.Mvc.Filters.ControllerActionFilter (Order: -2147483648), Microsoft.AspNetCore.Mvc.ModelBinding.UnsupportedContentTypeFilter (Order: -3000)
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of exception filters (in the following order): None
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of result filters (in the following order): Microsoft.AspNetCore.Mvc.ViewFeatures.Filters.SaveTempDataFilter
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[22]
      Attempting to bind parameter 'id' of type 'System.String' ...
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.Binders.SimpleTypeModelBinder[44]
      Attempting to bind parameter 'id' of type 'System.String' using the name 'id' in request data ...
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.Binders.SimpleTypeModelBinder[45]
      Done attempting to bind parameter 'id' of type 'System.String'.
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[23]
      Done attempting to bind parameter 'id' of type 'System.String'.
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[26]
      Attempting to validate the bound parameter 'id' of type 'System.String' ...
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[27]
      Done attempting to validate the bound parameter 'id' of type 'System.String'.
info: TodoApiSample.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[2]
      Executed action TodoApiSample.Controllers.TodoController.GetById (TodoApiSample) in 32.690400000000004ms
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 176.9103ms 404
```

## <a name="log-event-id"></a><span data-ttu-id="f1527-259">Id. de evento del registro</span><span class="sxs-lookup"><span data-stu-id="f1527-259">Log event ID</span></span>

<span data-ttu-id="f1527-260">Cada registro se puede especificar un *id. de evento*.</span><span class="sxs-lookup"><span data-stu-id="f1527-260">Each log can specify an *event ID*.</span></span> <span data-ttu-id="f1527-261">La aplicación de ejemplo lo hace mediante una clase `LoggingEvents` definida de forma local:</span><span class="sxs-lookup"><span data-stu-id="f1527-261">The sample app does this by using a locally defined `LoggingEvents` class:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/3.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

<span data-ttu-id="f1527-262">Un id. de evento asocia un conjunto de eventos.</span><span class="sxs-lookup"><span data-stu-id="f1527-262">An event ID associates a set of events.</span></span> <span data-ttu-id="f1527-263">Por ejemplo, todos los registros relacionados con la presentación de una lista de elementos en una página podrían ser 1001.</span><span class="sxs-lookup"><span data-stu-id="f1527-263">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="f1527-264">El proveedor de registro puede almacenar el id. de evento en un campo de identificador, en el mensaje de registro o no almacenarlo.</span><span class="sxs-lookup"><span data-stu-id="f1527-264">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="f1527-265">El proveedor de depuración no muestra los identificadores de evento.</span><span class="sxs-lookup"><span data-stu-id="f1527-265">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="f1527-266">El proveedor de consola muestra los identificadores de evento entre corchetes después de la categoría:</span><span class="sxs-lookup"><span data-stu-id="f1527-266">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a><span data-ttu-id="f1527-267">Plantilla de mensaje de registro</span><span class="sxs-lookup"><span data-stu-id="f1527-267">Log message template</span></span>

<span data-ttu-id="f1527-268">Cada registro especifica una plantilla de mensaje.</span><span class="sxs-lookup"><span data-stu-id="f1527-268">Each log specifies a message template.</span></span> <span data-ttu-id="f1527-269">La plantilla de mensaje puede contener marcadores de posición para los que se proporcionan argumentos.</span><span class="sxs-lookup"><span data-stu-id="f1527-269">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="f1527-270">Use los nombres de los marcadores de posición, no números.</span><span class="sxs-lookup"><span data-stu-id="f1527-270">Use names for the placeholders, not numbers.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="f1527-271">El orden de los marcadores de posición, no sus nombres, determina qué parámetros se usan para proporcionar sus valores.</span><span class="sxs-lookup"><span data-stu-id="f1527-271">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="f1527-272">En el código siguiente, tenga en cuenta que los nombres de parámetro están fuera de la secuencia en la plantilla de mensaje:</span><span class="sxs-lookup"><span data-stu-id="f1527-272">In the following code, notice that the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="f1527-273">Este código crea un mensaje de registro con los valores de parámetro en secuencia:</span><span class="sxs-lookup"><span data-stu-id="f1527-273">This code creates a log message with the parameter values in sequence:</span></span>

```text
Parameter values: parm1, parm2
```

<span data-ttu-id="f1527-274">La plataforma de registro funciona de esta manera para que los proveedores de registro puedan implementar [el registro semántico, también conocido como registro estructurado](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="f1527-274">The logging framework works this way so that logging providers can implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span> <span data-ttu-id="f1527-275">Los propios argumentos se pasan al sistema de registro, no solo a la plantilla de mensaje con formato.</span><span class="sxs-lookup"><span data-stu-id="f1527-275">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="f1527-276">Esta información permite a los proveedores de registro almacenar los valores de parámetro como campos.</span><span class="sxs-lookup"><span data-stu-id="f1527-276">This information enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="f1527-277">Por ejemplo, suponga que las llamadas del método del registrador tiene el aspecto siguiente:</span><span class="sxs-lookup"><span data-stu-id="f1527-277">For example, suppose logger method calls look like this:</span></span>

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="f1527-278">Si envía los registros a Azure Table Storage, cada entidad de Azure Table puede tener propiedades `ID` y `RequestTime`, lo que simplifica las consultas en los datos de registro.</span><span class="sxs-lookup"><span data-stu-id="f1527-278">If you're sending the logs to Azure Table Storage, each Azure Table entity can have `ID` and `RequestTime` properties, which simplifies queries on log data.</span></span> <span data-ttu-id="f1527-279">Una consulta puede buscar todos los registros dentro de un intervalo `RequestTime` determinado sin analizar el tiempo de espera del mensaje de texto.</span><span class="sxs-lookup"><span data-stu-id="f1527-279">A query can find all logs within a particular `RequestTime` range without parsing the time out of the text message.</span></span>

## <a name="logging-exceptions"></a><span data-ttu-id="f1527-280">Excepciones de registro</span><span class="sxs-lookup"><span data-stu-id="f1527-280">Logging exceptions</span></span>

<span data-ttu-id="f1527-281">Los métodos de registrador tienen sobrecargas que le permiten pasar una excepción, como en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="f1527-281">The logger methods have overloads that let you pass in an exception, as in the following example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

<span data-ttu-id="f1527-282">Cada proveedor controla la información de la excepción de maneras diferentes.</span><span class="sxs-lookup"><span data-stu-id="f1527-282">Different providers handle the exception information in different ways.</span></span> <span data-ttu-id="f1527-283">Este es un ejemplo de salida del proveedor de depuración del código mostrado anteriormente.</span><span class="sxs-lookup"><span data-stu-id="f1527-283">Here's an example of Debug provider output from the code shown above.</span></span>

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a><span data-ttu-id="f1527-284">Filtrado del registro</span><span class="sxs-lookup"><span data-stu-id="f1527-284">Log filtering</span></span>

<span data-ttu-id="f1527-285">Puede especificar un nivel de registro mínimo para un proveedor y una categoría específicos, o para todos los proveedores o todas las categorías.</span><span class="sxs-lookup"><span data-stu-id="f1527-285">You can specify a minimum log level for a specific provider and category or for all providers or all categories.</span></span> <span data-ttu-id="f1527-286">Los registros por debajo del nivel mínimo no se pasan a ese proveedor, de modo que no se muestran o almacenan.</span><span class="sxs-lookup"><span data-stu-id="f1527-286">Any logs below the minimum level aren't passed to that provider, so they don't get displayed or stored.</span></span>

<span data-ttu-id="f1527-287">Para suprimir todos los registros, especifique `LogLevel.None` como el nivel de registro mínimo.</span><span class="sxs-lookup"><span data-stu-id="f1527-287">To suppress all logs, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="f1527-288">El valor entero de `LogLevel.None` es 6, que es superior a `LogLevel.Critical` (5).</span><span class="sxs-lookup"><span data-stu-id="f1527-288">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

### <a name="create-filter-rules-in-configuration"></a><span data-ttu-id="f1527-289">Creación de reglas de filtro en la configuración</span><span class="sxs-lookup"><span data-stu-id="f1527-289">Create filter rules in configuration</span></span>

<span data-ttu-id="f1527-290">El código de la plantilla de proyecto llama a `CreateDefaultBuilder` para configurar el registro para los proveedores de Console, Debug y EventSource (ASP.NET Core 2.2 o versiones posteriores).</span><span class="sxs-lookup"><span data-stu-id="f1527-290">The project template code calls `CreateDefaultBuilder` to set up logging for the Console, Debug, and EventSource (ASP.NET Core 2.2 or later) providers.</span></span> <span data-ttu-id="f1527-291">El método `CreateDefaultBuilder` configura el registro para buscar la configuración en una sección de `Logging`, como se explica [anteriormente en este artículo](#configuration).</span><span class="sxs-lookup"><span data-stu-id="f1527-291">The `CreateDefaultBuilder` method sets up logging to look for configuration in a `Logging` section, as explained [earlier in this article](#configuration).</span></span>

<span data-ttu-id="f1527-292">Los datos de configuración especifican niveles de registro mínimo por proveedor y categoría, como en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="f1527-292">The configuration data specifies minimum log levels by provider and category, as in the following example:</span></span>

[!code-json[](index/samples/3.x/TodoApiSample/appsettings.json)]

<span data-ttu-id="f1527-293">Este archivo JSON crea seis reglas de filtro, una para el proveedor de depuración, cuatro para el proveedor de la consola y una para todos los proveedores.</span><span class="sxs-lookup"><span data-stu-id="f1527-293">This JSON creates six filter rules: one for the Debug provider, four for the Console provider, and one for all providers.</span></span> <span data-ttu-id="f1527-294">Se elige una sola regla para cada proveedor cuando se crea un objeto `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="f1527-294">A single rule is chosen for each provider when an `ILogger` object is created.</span></span>

### <a name="filter-rules-in-code"></a><span data-ttu-id="f1527-295">Reglas de filtro en el código</span><span class="sxs-lookup"><span data-stu-id="f1527-295">Filter rules in code</span></span>

<span data-ttu-id="f1527-296">En el siguiente ejemplo se muestra cómo registrar reglas de filtro en el código:</span><span class="sxs-lookup"><span data-stu-id="f1527-296">The following example shows how to register filter rules in code:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=2-3)]

<span data-ttu-id="f1527-297">El segundo `AddFilter` especifica el proveedor de depuración mediante su nombre de tipo.</span><span class="sxs-lookup"><span data-stu-id="f1527-297">The second `AddFilter` specifies the Debug provider by using its type name.</span></span> <span data-ttu-id="f1527-298">El primer `AddFilter` se aplica a todos los proveedores, dado que no especifica un tipo de proveedor.</span><span class="sxs-lookup"><span data-stu-id="f1527-298">The first `AddFilter` applies to all providers because it doesn't specify a provider type.</span></span>

### <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="f1527-299">Cómo se aplican las reglas de filtro</span><span class="sxs-lookup"><span data-stu-id="f1527-299">How filtering rules are applied</span></span>

<span data-ttu-id="f1527-300">Los datos de configuración y el código de `AddFilter` que se muestran en los ejemplos anteriores crean las reglas que se muestran en la tabla siguiente.</span><span class="sxs-lookup"><span data-stu-id="f1527-300">The configuration data and the `AddFilter` code shown in the preceding examples create the rules shown in the following table.</span></span> <span data-ttu-id="f1527-301">Las seis primeras proceden del ejemplo de configuración y las dos últimas del ejemplo de código.</span><span class="sxs-lookup"><span data-stu-id="f1527-301">The first six come from the configuration example and the last two come from the code example.</span></span>

| <span data-ttu-id="f1527-302">número</span><span class="sxs-lookup"><span data-stu-id="f1527-302">Number</span></span> | <span data-ttu-id="f1527-303">Proveedor</span><span class="sxs-lookup"><span data-stu-id="f1527-303">Provider</span></span>      | <span data-ttu-id="f1527-304">Categorías que comienzan por...</span><span class="sxs-lookup"><span data-stu-id="f1527-304">Categories that begin with ...</span></span>          | <span data-ttu-id="f1527-305">Nivel de registro mínimo</span><span class="sxs-lookup"><span data-stu-id="f1527-305">Minimum log level</span></span> |
| :----: | ------------- | --------------------------------------- | ----------------- |
| <span data-ttu-id="f1527-306">1</span><span class="sxs-lookup"><span data-stu-id="f1527-306">1</span></span>      | <span data-ttu-id="f1527-307">Depuración</span><span class="sxs-lookup"><span data-stu-id="f1527-307">Debug</span></span>         | <span data-ttu-id="f1527-308">Todas las categorías</span><span class="sxs-lookup"><span data-stu-id="f1527-308">All categories</span></span>                          | <span data-ttu-id="f1527-309">Información</span><span class="sxs-lookup"><span data-stu-id="f1527-309">Information</span></span>       |
| <span data-ttu-id="f1527-310">2</span><span class="sxs-lookup"><span data-stu-id="f1527-310">2</span></span>      | <span data-ttu-id="f1527-311">Consola</span><span class="sxs-lookup"><span data-stu-id="f1527-311">Console</span></span>       | <span data-ttu-id="f1527-312">Microsoft.AspNetCore.Mvc.Razor.Internal</span><span class="sxs-lookup"><span data-stu-id="f1527-312">Microsoft.AspNetCore.Mvc.Razor.Internal</span></span> | <span data-ttu-id="f1527-313">Advertencia</span><span class="sxs-lookup"><span data-stu-id="f1527-313">Warning</span></span>           |
| <span data-ttu-id="f1527-314">3</span><span class="sxs-lookup"><span data-stu-id="f1527-314">3</span></span>      | <span data-ttu-id="f1527-315">Consola</span><span class="sxs-lookup"><span data-stu-id="f1527-315">Console</span></span>       | <span data-ttu-id="f1527-316">Microsoft.AspNetCore.Mvc.Razor.Razor</span><span class="sxs-lookup"><span data-stu-id="f1527-316">Microsoft.AspNetCore.Mvc.Razor.Razor</span></span>    | <span data-ttu-id="f1527-317">Depuración</span><span class="sxs-lookup"><span data-stu-id="f1527-317">Debug</span></span>             |
| <span data-ttu-id="f1527-318">4</span><span class="sxs-lookup"><span data-stu-id="f1527-318">4</span></span>      | <span data-ttu-id="f1527-319">Consola</span><span class="sxs-lookup"><span data-stu-id="f1527-319">Console</span></span>       | <span data-ttu-id="f1527-320">Microsoft.AspNetCore.Mvc.Razor</span><span class="sxs-lookup"><span data-stu-id="f1527-320">Microsoft.AspNetCore.Mvc.Razor</span></span>          | <span data-ttu-id="f1527-321">Error</span><span class="sxs-lookup"><span data-stu-id="f1527-321">Error</span></span>             |
| <span data-ttu-id="f1527-322">5</span><span class="sxs-lookup"><span data-stu-id="f1527-322">5</span></span>      | <span data-ttu-id="f1527-323">Consola</span><span class="sxs-lookup"><span data-stu-id="f1527-323">Console</span></span>       | <span data-ttu-id="f1527-324">Todas las categorías</span><span class="sxs-lookup"><span data-stu-id="f1527-324">All categories</span></span>                          | <span data-ttu-id="f1527-325">Información</span><span class="sxs-lookup"><span data-stu-id="f1527-325">Information</span></span>       |
| <span data-ttu-id="f1527-326">6</span><span class="sxs-lookup"><span data-stu-id="f1527-326">6</span></span>      | <span data-ttu-id="f1527-327">Todos los proveedores</span><span class="sxs-lookup"><span data-stu-id="f1527-327">All providers</span></span> | <span data-ttu-id="f1527-328">Todas las categorías</span><span class="sxs-lookup"><span data-stu-id="f1527-328">All categories</span></span>                          | <span data-ttu-id="f1527-329">Depuración</span><span class="sxs-lookup"><span data-stu-id="f1527-329">Debug</span></span>             |
| <span data-ttu-id="f1527-330">7</span><span class="sxs-lookup"><span data-stu-id="f1527-330">7</span></span>      | <span data-ttu-id="f1527-331">Todos los proveedores</span><span class="sxs-lookup"><span data-stu-id="f1527-331">All providers</span></span> | <span data-ttu-id="f1527-332">Sistema</span><span class="sxs-lookup"><span data-stu-id="f1527-332">System</span></span>                                  | <span data-ttu-id="f1527-333">Depuración</span><span class="sxs-lookup"><span data-stu-id="f1527-333">Debug</span></span>             |
| <span data-ttu-id="f1527-334">8</span><span class="sxs-lookup"><span data-stu-id="f1527-334">8</span></span>      | <span data-ttu-id="f1527-335">Depuración</span><span class="sxs-lookup"><span data-stu-id="f1527-335">Debug</span></span>         | <span data-ttu-id="f1527-336">Microsoft</span><span class="sxs-lookup"><span data-stu-id="f1527-336">Microsoft</span></span>                               | <span data-ttu-id="f1527-337">Seguimiento</span><span class="sxs-lookup"><span data-stu-id="f1527-337">Trace</span></span>             |

<span data-ttu-id="f1527-338">Cuando se crea un objeto `ILogger`, el objeto `ILoggerFactory` selecciona una sola regla por proveedor para aplicar a ese registrador.</span><span class="sxs-lookup"><span data-stu-id="f1527-338">When an `ILogger` object is created, the `ILoggerFactory` object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="f1527-339">Todos los mensajes escritos por una instancia `ILogger` se filtran según las reglas seleccionadas.</span><span class="sxs-lookup"><span data-stu-id="f1527-339">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="f1527-340">De las reglas disponibles se selecciona la más específica posible para cada par de categoría y proveedor.</span><span class="sxs-lookup"><span data-stu-id="f1527-340">The most specific rule possible for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="f1527-341">Cuando se crea un `ILogger` para una categoría determinada, se usa el algoritmo siguiente para cada proveedor:</span><span class="sxs-lookup"><span data-stu-id="f1527-341">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="f1527-342">Se seleccionan todas las reglas que coinciden con el proveedor o su alias.</span><span class="sxs-lookup"><span data-stu-id="f1527-342">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="f1527-343">Si no se encuentra ninguna coincidencia, se seleccionan todas las reglas con un proveedor vacío.</span><span class="sxs-lookup"><span data-stu-id="f1527-343">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="f1527-344">Del resultado del paso anterior, se seleccionan las reglas con el prefijo de categoría coincidente más largo.</span><span class="sxs-lookup"><span data-stu-id="f1527-344">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="f1527-345">Si no se encuentra ninguna coincidencia, se seleccionan todas las reglas que no especifican una categoría.</span><span class="sxs-lookup"><span data-stu-id="f1527-345">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="f1527-346">Si se seleccionan varias reglas, se toma la **última**.</span><span class="sxs-lookup"><span data-stu-id="f1527-346">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="f1527-347">Si no se selecciona ninguna regla, se usa `MinimumLevel`.</span><span class="sxs-lookup"><span data-stu-id="f1527-347">If no rules are selected, use `MinimumLevel`.</span></span>

<span data-ttu-id="f1527-348">Con la lista de reglas anterior, supongamos que crea un objeto `ILogger` para la categoría "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span><span class="sxs-lookup"><span data-stu-id="f1527-348">With the preceding list of rules, suppose you create an `ILogger` object for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span></span>

* <span data-ttu-id="f1527-349">Para el proveedor de depuración, se aplican las reglas 1, 6 y 8.</span><span class="sxs-lookup"><span data-stu-id="f1527-349">For the Debug provider, rules 1, 6, and 8 apply.</span></span> <span data-ttu-id="f1527-350">La regla 8 es la más específica, por lo que se selecciona.</span><span class="sxs-lookup"><span data-stu-id="f1527-350">Rule 8 is most specific, so that's the one selected.</span></span>
* <span data-ttu-id="f1527-351">Para el proveedor de la consola, se aplican las reglas 3, 4, 5 y 6.</span><span class="sxs-lookup"><span data-stu-id="f1527-351">For the Console provider, rules 3, 4, 5, and 6 apply.</span></span> <span data-ttu-id="f1527-352">La regla 3 es la más específica.</span><span class="sxs-lookup"><span data-stu-id="f1527-352">Rule 3 is most specific.</span></span>

<span data-ttu-id="f1527-353">La instancia `ILogger` resultante envía los registros de nivel `Trace` y superiores al proveedor de depuración.</span><span class="sxs-lookup"><span data-stu-id="f1527-353">The resulting `ILogger` instance sends logs of `Trace` level and above to the Debug provider.</span></span> <span data-ttu-id="f1527-354">Los registros de nivel `Debug` y superiores se envían al proveedor de consola.</span><span class="sxs-lookup"><span data-stu-id="f1527-354">Logs of `Debug` level and above are sent to the Console provider.</span></span>

### <a name="provider-aliases"></a><span data-ttu-id="f1527-355">Alias de proveedor</span><span class="sxs-lookup"><span data-stu-id="f1527-355">Provider aliases</span></span>

<span data-ttu-id="f1527-356">Cada proveedor define un *alias* que se puede utilizar en la configuración en lugar del nombre de tipo completo.</span><span class="sxs-lookup"><span data-stu-id="f1527-356">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span>  <span data-ttu-id="f1527-357">Para los proveedores integrados, use los alias siguientes:</span><span class="sxs-lookup"><span data-stu-id="f1527-357">For the built-in providers, use the following aliases:</span></span>

* <span data-ttu-id="f1527-358">Consola</span><span class="sxs-lookup"><span data-stu-id="f1527-358">Console</span></span>
* <span data-ttu-id="f1527-359">Depuración</span><span class="sxs-lookup"><span data-stu-id="f1527-359">Debug</span></span>
* <span data-ttu-id="f1527-360">EventSource</span><span class="sxs-lookup"><span data-stu-id="f1527-360">EventSource</span></span>
* <span data-ttu-id="f1527-361">EventLog</span><span class="sxs-lookup"><span data-stu-id="f1527-361">EventLog</span></span>
* <span data-ttu-id="f1527-362">TraceSource</span><span class="sxs-lookup"><span data-stu-id="f1527-362">TraceSource</span></span>
* <span data-ttu-id="f1527-363">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="f1527-363">AzureAppServicesFile</span></span>
* <span data-ttu-id="f1527-364">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="f1527-364">AzureAppServicesBlob</span></span>
* <span data-ttu-id="f1527-365">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="f1527-365">ApplicationInsights</span></span>

### <a name="default-minimum-level"></a><span data-ttu-id="f1527-366">Nivel mínimo predeterminado</span><span class="sxs-lookup"><span data-stu-id="f1527-366">Default minimum level</span></span>

<span data-ttu-id="f1527-367">Hay una configuración de nivel mínimo que solo tiene efecto si no se aplica ninguna regla de configuración o código para un proveedor y una categoría determinados.</span><span class="sxs-lookup"><span data-stu-id="f1527-367">There's a minimum level setting that takes effect only if no rules from configuration or code apply for a given provider and category.</span></span> <span data-ttu-id="f1527-368">En el ejemplo siguiente se muestra cómo establecer el nivel mínimo:</span><span class="sxs-lookup"><span data-stu-id="f1527-368">The following example shows how to set the minimum level:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

<span data-ttu-id="f1527-369">Si no establece explícitamente el nivel mínimo, el valor predeterminado es `Information`, lo que significa que los registros `Trace` y `Debug` se omiten.</span><span class="sxs-lookup"><span data-stu-id="f1527-369">If you don't explicitly set the minimum level, the default value is `Information`, which means that `Trace` and `Debug` logs are ignored.</span></span>

### <a name="filter-functions"></a><span data-ttu-id="f1527-370">Funciones de filtro</span><span class="sxs-lookup"><span data-stu-id="f1527-370">Filter functions</span></span>

<span data-ttu-id="f1527-371">Se invoca una función de filtro para todos los proveedores y categorías que no tienen reglas asignadas mediante configuración o código.</span><span class="sxs-lookup"><span data-stu-id="f1527-371">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code.</span></span> <span data-ttu-id="f1527-372">El código de la función tiene acceso al tipo de proveedor, la categoría y el nivel de registro.</span><span class="sxs-lookup"><span data-stu-id="f1527-372">Code in the function has access to the provider type, category, and log level.</span></span> <span data-ttu-id="f1527-373">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="f1527-373">For example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=3-11)]

## <a name="system-categories-and-levels"></a><span data-ttu-id="f1527-374">Niveles y categorías del sistema</span><span class="sxs-lookup"><span data-stu-id="f1527-374">System categories and levels</span></span>

<span data-ttu-id="f1527-375">Estas son algunas categorías que ASP.NET Core y Entity Framework Core usan, con notas sobre lo que los registros de espera de ellas:</span><span class="sxs-lookup"><span data-stu-id="f1527-375">Here are some categories used by ASP.NET Core and Entity Framework Core, with notes about what logs to expect from them:</span></span>

| <span data-ttu-id="f1527-376">Categoría</span><span class="sxs-lookup"><span data-stu-id="f1527-376">Category</span></span>                            | <span data-ttu-id="f1527-377">Notas</span><span class="sxs-lookup"><span data-stu-id="f1527-377">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="f1527-378">Microsoft.AspNetCore</span><span class="sxs-lookup"><span data-stu-id="f1527-378">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="f1527-379">Diagnósticos generales de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f1527-379">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="f1527-380">Microsoft.AspNetCore.DataProtection</span><span class="sxs-lookup"><span data-stu-id="f1527-380">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="f1527-381">Qué claves se tuvieron en cuenta, encontraron y usaron.</span><span class="sxs-lookup"><span data-stu-id="f1527-381">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="f1527-382">Microsoft.AspNetCore.HostFiltering</span><span class="sxs-lookup"><span data-stu-id="f1527-382">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="f1527-383">Hosts permitidos.</span><span class="sxs-lookup"><span data-stu-id="f1527-383">Hosts allowed.</span></span> |
| <span data-ttu-id="f1527-384">Microsoft.AspNetCore.Hosting</span><span class="sxs-lookup"><span data-stu-id="f1527-384">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="f1527-385">Cuánto tiempo tardaron en completarse las solicitudes HTTP y a qué hora comenzaron.</span><span class="sxs-lookup"><span data-stu-id="f1527-385">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="f1527-386">Qué ensamblados de inicio de hospedaje se cargaron.</span><span class="sxs-lookup"><span data-stu-id="f1527-386">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="f1527-387">Microsoft.AspNetCore.Mvc</span><span class="sxs-lookup"><span data-stu-id="f1527-387">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="f1527-388">Diagnósticos de MVC y Razor.</span><span class="sxs-lookup"><span data-stu-id="f1527-388">MVC and Razor diagnostics.</span></span> <span data-ttu-id="f1527-389">Enlace de modelos, ejecución de filtros, compilación de vistas y selección de acciones.</span><span class="sxs-lookup"><span data-stu-id="f1527-389">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="f1527-390">Microsoft.AspNetCore.Routing</span><span class="sxs-lookup"><span data-stu-id="f1527-390">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="f1527-391">Información de coincidencia de ruta.</span><span class="sxs-lookup"><span data-stu-id="f1527-391">Route matching information.</span></span> |
| <span data-ttu-id="f1527-392">Microsoft.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="f1527-392">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="f1527-393">Inicio y detención de conexión y mantener las respuestas activas.</span><span class="sxs-lookup"><span data-stu-id="f1527-393">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="f1527-394">Información de certificado HTTPS.</span><span class="sxs-lookup"><span data-stu-id="f1527-394">HTTPS certificate information.</span></span> |
| <span data-ttu-id="f1527-395">Microsoft.AspNetCore.StaticFiles</span><span class="sxs-lookup"><span data-stu-id="f1527-395">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="f1527-396">Archivos servidos.</span><span class="sxs-lookup"><span data-stu-id="f1527-396">Files served.</span></span> |
| <span data-ttu-id="f1527-397">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="f1527-397">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="f1527-398">Diagnósticos generales de Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="f1527-398">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="f1527-399">Actividad y la configuración de bases de datos, detección de cambios y migraciones.</span><span class="sxs-lookup"><span data-stu-id="f1527-399">Database activity and configuration, change detection, migrations.</span></span> |

## <a name="log-scopes"></a><span data-ttu-id="f1527-400">Ámbitos de registro</span><span class="sxs-lookup"><span data-stu-id="f1527-400">Log scopes</span></span>

 <span data-ttu-id="f1527-401">Un *ámbito* puede agrupar un conjunto de operaciones lógicas.</span><span class="sxs-lookup"><span data-stu-id="f1527-401">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="f1527-402">Esta agrupación se puede utilizar para adjuntar los mismos datos para cada registro que se crea como parte de un conjunto.</span><span class="sxs-lookup"><span data-stu-id="f1527-402">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="f1527-403">Por ejemplo, cada registro creado como parte del procesamiento de una transacción puede incluir el identificador de dicha transacción.</span><span class="sxs-lookup"><span data-stu-id="f1527-403">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="f1527-404">Un ámbito es un tipo `IDisposable` devuelto por el método <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> y se conserva hasta que se elimina.</span><span class="sxs-lookup"><span data-stu-id="f1527-404">A scope is an `IDisposable` type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method and lasts until it's disposed.</span></span> <span data-ttu-id="f1527-405">Use un ámbito encapsulando las llamadas de registrador en un bloque `using`:</span><span class="sxs-lookup"><span data-stu-id="f1527-405">Use a scope by wrapping logger calls in a `using` block:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

<span data-ttu-id="f1527-406">El código siguiente permite ámbitos para el proveedor de la consola:</span><span class="sxs-lookup"><span data-stu-id="f1527-406">The following code enables scopes for the console provider:</span></span>

<span data-ttu-id="f1527-407">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="f1527-407">*Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=6)]

> [!NOTE]
> <span data-ttu-id="f1527-408">Es necesario configurar la opción del registrador de consola `IncludeScopes` para habilitar el registro basado en el ámbito.</span><span class="sxs-lookup"><span data-stu-id="f1527-408">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>
>
> <span data-ttu-id="f1527-409">Para obtener información sobre la configuración, consulte la sección [Configuración](#configuration).</span><span class="sxs-lookup"><span data-stu-id="f1527-409">For information on configuration, see the [Configuration](#configuration) section.</span></span>

<span data-ttu-id="f1527-410">Cada mensaje de registro incluye la información de ámbito:</span><span class="sxs-lookup"><span data-stu-id="f1527-410">Each log message includes the scoped information:</span></span>

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a><span data-ttu-id="f1527-411">Proveedores de registro integrados</span><span class="sxs-lookup"><span data-stu-id="f1527-411">Built-in logging providers</span></span>

<span data-ttu-id="f1527-412">ASP.NET Core incluye los proveedores siguientes:</span><span class="sxs-lookup"><span data-stu-id="f1527-412">ASP.NET Core ships the following providers:</span></span>

* [<span data-ttu-id="f1527-413">Consola</span><span class="sxs-lookup"><span data-stu-id="f1527-413">Console</span></span>](#console-provider)
* [<span data-ttu-id="f1527-414">Depurar</span><span class="sxs-lookup"><span data-stu-id="f1527-414">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="f1527-415">EventSource</span><span class="sxs-lookup"><span data-stu-id="f1527-415">EventSource</span></span>](#event-source-provider)
* [<span data-ttu-id="f1527-416">EventLog</span><span class="sxs-lookup"><span data-stu-id="f1527-416">EventLog</span></span>](#windows-eventlog-provider)
* [<span data-ttu-id="f1527-417">TraceSource</span><span class="sxs-lookup"><span data-stu-id="f1527-417">TraceSource</span></span>](#tracesource-provider)
* [<span data-ttu-id="f1527-418">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="f1527-418">AzureAppServicesFile</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="f1527-419">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="f1527-419">AzureAppServicesBlob</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="f1527-420">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="f1527-420">ApplicationInsights</span></span>](#azure-application-insights-trace-logging)

<span data-ttu-id="f1527-421">Para obtener información sobre stdout y el registro de depuración con el módulo ASP.NET Core, consulte <xref:test/troubleshoot-azure-iis> y <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="f1527-421">For information on stdout and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console-provider"></a><span data-ttu-id="f1527-422">Proveedor de la consola</span><span class="sxs-lookup"><span data-stu-id="f1527-422">Console provider</span></span>

<span data-ttu-id="f1527-423">El paquete de proveedor [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) envía la salida del registro a la consola.</span><span class="sxs-lookup"><span data-stu-id="f1527-423">The [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) provider package sends log output to the console.</span></span> 

```csharp
logging.AddConsole();
```

<span data-ttu-id="f1527-424">Para ver una salida de registro de la consola, abra un símbolo del sistema en la carpeta del proyecto y ejecute este comando:</span><span class="sxs-lookup"><span data-stu-id="f1527-424">To see console logging output, open a command prompt in the project folder and run the following command:</span></span>

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a><span data-ttu-id="f1527-425">Proveedor de depuración</span><span class="sxs-lookup"><span data-stu-id="f1527-425">Debug provider</span></span>

<span data-ttu-id="f1527-426">El paquete de proveedor [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) escribe la salida del registro mediante la clase [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) (llamadas a métodos `Debug.WriteLine`).</span><span class="sxs-lookup"><span data-stu-id="f1527-426">The [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) provider package writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class (`Debug.WriteLine` method calls).</span></span>

<span data-ttu-id="f1527-427">En Linux, este proveedor escribe registros en */var/log/message*.</span><span class="sxs-lookup"><span data-stu-id="f1527-427">On Linux, this provider writes logs to */var/log/message*.</span></span>

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a><span data-ttu-id="f1527-428">Proveedor de origen del evento</span><span class="sxs-lookup"><span data-stu-id="f1527-428">Event Source provider</span></span>

<span data-ttu-id="f1527-429">El paquete del proveedor [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) escribe en una multiplataforma de origen del evento con el nombre `Microsoft-Extensions-Logging`.</span><span class="sxs-lookup"><span data-stu-id="f1527-429">The [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) provider package writes to an Event Source cross-platform with the name `Microsoft-Extensions-Logging`.</span></span> <span data-ttu-id="f1527-430">En Windows, el proveedor utiliza [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span><span class="sxs-lookup"><span data-stu-id="f1527-430">On Windows, the provider uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span>

```csharp
logging.AddEventSourceLogger();
```

<span data-ttu-id="f1527-431">El proveedor de origen del evento se agrega automáticamente cuando se llama a `CreateDefaultBuilder` para compilar el host.</span><span class="sxs-lookup"><span data-stu-id="f1527-431">The Event Source provider is added automatically when `CreateDefaultBuilder` is called to build the host.</span></span>

#### <a name="dotnet-trace-tooling"></a><span data-ttu-id="f1527-432">herramienta de seguimiento de dotnet</span><span class="sxs-lookup"><span data-stu-id="f1527-432">dotnet trace tooling</span></span>

<span data-ttu-id="f1527-433">La herramienta de [seguimiento de dotnet](/dotnet/core/diagnostics/dotnet-trace) es una herramienta global de CLI multiplataforma que permite la recopilación de seguimientos de .NET Core de un proceso en ejecución.</span><span class="sxs-lookup"><span data-stu-id="f1527-433">The [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) tool is a cross-platform CLI global tool that enables the collection of .NET Core traces of a running process.</span></span> <span data-ttu-id="f1527-434">La herramienta recopila datos del proveedor <xref:Microsoft.Extensions.Logging.EventSource> mediante un <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>.</span><span class="sxs-lookup"><span data-stu-id="f1527-434">The tool collects <xref:Microsoft.Extensions.Logging.EventSource> provider data using a <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>.</span></span>

<span data-ttu-id="f1527-435">Instale la herramienta de seguimiento de dotnet con el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="f1527-435">Install the dotnet trace tooling with the following command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-trace
```

<span data-ttu-id="f1527-436">Use la herramienta de seguimiento de dotnet para recopilar un seguimiento de una aplicación:</span><span class="sxs-lookup"><span data-stu-id="f1527-436">Use the dotnet trace tooling to collect a trace from an app:</span></span>

1. <span data-ttu-id="f1527-437">Si la aplicación no compila el host con `CreateDefaultBuilder`, agregue el [proveedor de origen del evento](#event-source-provider) a la configuración de registro de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f1527-437">If the app doesn't build the host with `CreateDefaultBuilder`, add the [Event Source provider](#event-source-provider) to the app's logging configuration.</span></span>

1. <span data-ttu-id="f1527-438">Ejecute la aplicación con el comando `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="f1527-438">Run the app with the `dotnet run` command.</span></span>

1. <span data-ttu-id="f1527-439">Determine el identificador del proceso (PID) de la aplicación .NET Core:</span><span class="sxs-lookup"><span data-stu-id="f1527-439">Determine the process identifier (PID) of the .NET Core app:</span></span>

   * <span data-ttu-id="f1527-440">En Windows, siga uno de estos procedimientos:</span><span class="sxs-lookup"><span data-stu-id="f1527-440">On Windows, use one of the following approaches:</span></span>
     * <span data-ttu-id="f1527-441">Administrador de tareas (Ctrl + Alt + Supr)</span><span class="sxs-lookup"><span data-stu-id="f1527-441">Task Manager (Ctrl+Alt+Del)</span></span>
     * [<span data-ttu-id="f1527-442">Comando TaskList</span><span class="sxs-lookup"><span data-stu-id="f1527-442">tasklist command</span></span>](/windows-server/administration/windows-commands/tasklist)
     * [<span data-ttu-id="f1527-443">Comando de PowerShell Get-Process</span><span class="sxs-lookup"><span data-stu-id="f1527-443">Get-Process Powershell command</span></span>](/powershell/module/microsoft.powershell.management/get-process)
   * <span data-ttu-id="f1527-444">En Linux, use el [comando pidof](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html).</span><span class="sxs-lookup"><span data-stu-id="f1527-444">On Linux, use the [pidof command](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html).</span></span>

   <span data-ttu-id="f1527-445">Busque el PID del proceso que tenga el mismo nombre que el ensamblado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f1527-445">Find the PID for the process that has the same name as the app's assembly.</span></span>

1. <span data-ttu-id="f1527-446">Ejecute el comando `dotnet trace`.</span><span class="sxs-lookup"><span data-stu-id="f1527-446">Execute the `dotnet trace` command.</span></span>

   <span data-ttu-id="f1527-447">Sintaxis general del comando:</span><span class="sxs-lookup"><span data-stu-id="f1527-447">General command syntax:</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"
   ```

   <span data-ttu-id="f1527-448">Al usar un shell de comandos de PowerShell, incluya el valor `--providers` entre comillas simples (`'`):</span><span class="sxs-lookup"><span data-stu-id="f1527-448">When using a PowerShell command shell, enclose the `--providers` value in single quotes (`'`):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers 'Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"'
   ```

   <span data-ttu-id="f1527-449">En plataformas que no sean Windows, agregue la opción `-f speedscope` para cambiar el formato del archivo de seguimiento de salida a `speedscope`.</span><span class="sxs-lookup"><span data-stu-id="f1527-449">On non-Windows platforms, add the `-f speedscope` option to change the format of the output trace file to `speedscope`.</span></span>

   | <span data-ttu-id="f1527-450">Palabra clave</span><span class="sxs-lookup"><span data-stu-id="f1527-450">Keyword</span></span> | <span data-ttu-id="f1527-451">Descripción</span><span class="sxs-lookup"><span data-stu-id="f1527-451">Description</span></span> |
   | :-----: | ----------- |
   | <span data-ttu-id="f1527-452">1</span><span class="sxs-lookup"><span data-stu-id="f1527-452">1</span></span>       | <span data-ttu-id="f1527-453">Registre los eventos meta sobre el elemento `LoggingEventSource`.</span><span class="sxs-lookup"><span data-stu-id="f1527-453">Log meta events about the `LoggingEventSource`.</span></span> <span data-ttu-id="f1527-454">No registre eventos de `ILogger`).</span><span class="sxs-lookup"><span data-stu-id="f1527-454">Doesn't log events from `ILogger`).</span></span> |
   | <span data-ttu-id="f1527-455">2</span><span class="sxs-lookup"><span data-stu-id="f1527-455">2</span></span>       | <span data-ttu-id="f1527-456">Activa el evento `Message` cuando se llama a `ILogger.Log()`.</span><span class="sxs-lookup"><span data-stu-id="f1527-456">Turns on the `Message` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="f1527-457">Proporciona la información mediante programación (sin formato).</span><span class="sxs-lookup"><span data-stu-id="f1527-457">Provides information in a programmatic (not formatted) way.</span></span> |
   | <span data-ttu-id="f1527-458">4</span><span class="sxs-lookup"><span data-stu-id="f1527-458">4</span></span>       | <span data-ttu-id="f1527-459">Activa el evento `FormatMessage` cuando se llama a `ILogger.Log()`.</span><span class="sxs-lookup"><span data-stu-id="f1527-459">Turns on the `FormatMessage` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="f1527-460">Proporciona la versión de cadena con formato de la información.</span><span class="sxs-lookup"><span data-stu-id="f1527-460">Provides the formatted string version of the information.</span></span> |
   | <span data-ttu-id="f1527-461">8</span><span class="sxs-lookup"><span data-stu-id="f1527-461">8</span></span>       | <span data-ttu-id="f1527-462">Activa el evento `MessageJson` cuando se llama a `ILogger.Log()`.</span><span class="sxs-lookup"><span data-stu-id="f1527-462">Turns on the `MessageJson` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="f1527-463">Proporciona una representación JSON de los argumentos.</span><span class="sxs-lookup"><span data-stu-id="f1527-463">Provides a JSON representation of the arguments.</span></span> |

   | <span data-ttu-id="f1527-464">Nivel de evento</span><span class="sxs-lookup"><span data-stu-id="f1527-464">Event Level</span></span> | <span data-ttu-id="f1527-465">Descripción</span><span class="sxs-lookup"><span data-stu-id="f1527-465">Description</span></span>     |
   | :---------: | --------------- |
   | <span data-ttu-id="f1527-466">0</span><span class="sxs-lookup"><span data-stu-id="f1527-466">0</span></span>           | `LogAlways`     |
   | <span data-ttu-id="f1527-467">1</span><span class="sxs-lookup"><span data-stu-id="f1527-467">1</span></span>           | `Critical`      |
   | <span data-ttu-id="f1527-468">2</span><span class="sxs-lookup"><span data-stu-id="f1527-468">2</span></span>           | `Error`         |
   | <span data-ttu-id="f1527-469">3</span><span class="sxs-lookup"><span data-stu-id="f1527-469">3</span></span>           | `Warning`       |
   | <span data-ttu-id="f1527-470">4</span><span class="sxs-lookup"><span data-stu-id="f1527-470">4</span></span>           | `Informational` |
   | <span data-ttu-id="f1527-471">5</span><span class="sxs-lookup"><span data-stu-id="f1527-471">5</span></span>           | `Verbose`       |

   <span data-ttu-id="f1527-472">Las entradas `FilterSpecs` de `{Logger Category}` y `{Event Level}` representan condiciones de filtrado de registros adicionales.</span><span class="sxs-lookup"><span data-stu-id="f1527-472">`FilterSpecs` entries for `{Logger Category}` and `{Event Level}` represent additional log filtering conditions.</span></span> <span data-ttu-id="f1527-473">Separe las entradas `FilterSpecs` con un punto y coma (`;`).</span><span class="sxs-lookup"><span data-stu-id="f1527-473">Separate `FilterSpecs` entries with a semicolon (`;`).</span></span>

   <span data-ttu-id="f1527-474">Ejemplo de uso de un shell de comandos de Windows (**no** hay comillas simples alrededor del valor `--providers`):</span><span class="sxs-lookup"><span data-stu-id="f1527-474">Example using a Windows command shell (**no** single quotes around the `--providers` value):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} --providers Microsoft-Extensions-Logging:4:2:FilterSpecs=\"Microsoft.AspNetCore.Hosting*:4\"
   ```

   <span data-ttu-id="f1527-475">El comando anterior activa lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="f1527-475">The preceding command activates:</span></span>

   * <span data-ttu-id="f1527-476">Registrador de origen del evento para generar cadenas con formato (`4`) de los errores (`2`).</span><span class="sxs-lookup"><span data-stu-id="f1527-476">The Event Source logger to produce formatted strings (`4`) for errors (`2`).</span></span>
   * <span data-ttu-id="f1527-477">Registro `Microsoft.AspNetCore.Hosting` en el nivel de registro `Informational` (`4`).</span><span class="sxs-lookup"><span data-stu-id="f1527-477">`Microsoft.AspNetCore.Hosting` logging at the `Informational` logging level (`4`).</span></span>

1. <span data-ttu-id="f1527-478">Presione la tecla Entrar o Ctrl + C para detener las herramientas de seguimiento de dotnet.</span><span class="sxs-lookup"><span data-stu-id="f1527-478">Stop the dotnet trace tooling by pressing the Enter key or Ctrl+C.</span></span>

   <span data-ttu-id="f1527-479">El seguimiento se guarda con el nombre *trace.nettrace* en la carpeta en la que se ejecuta el comando `dotnet trace`.</span><span class="sxs-lookup"><span data-stu-id="f1527-479">The trace is saved with the name *trace.nettrace* in the folder where the `dotnet trace` command is executed.</span></span>

1. <span data-ttu-id="f1527-480">Abra el seguimiento con [Perfview](#perfview).</span><span class="sxs-lookup"><span data-stu-id="f1527-480">Open the trace with [Perfview](#perfview).</span></span> <span data-ttu-id="f1527-481">Abra el archivo *trace.nettrace* y explore los eventos de seguimiento.</span><span class="sxs-lookup"><span data-stu-id="f1527-481">Open the *trace.nettrace* file and explore the trace events.</span></span>

<span data-ttu-id="f1527-482">Para obtener más información, consulte:</span><span class="sxs-lookup"><span data-stu-id="f1527-482">For more information, see:</span></span>

* <span data-ttu-id="f1527-483">[Seguimiento de la utilidad de análisis de rendimiento (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace) (documentación de .NET Core)</span><span class="sxs-lookup"><span data-stu-id="f1527-483">[Trace for performance analysis utility (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace) (.NET Core documentation)</span></span>
* <span data-ttu-id="f1527-484">[Seguimiento de la utilidad de análisis de rendimiento (dotnet-trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (documentación del repositorio de GitHub sobre diagnóstico y dotnet)</span><span class="sxs-lookup"><span data-stu-id="f1527-484">[Trace for performance analysis utility (dotnet-trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (dotnet/diagnostics GitHub repository documentation)</span></span>
* <span data-ttu-id="f1527-485">[Clase LoggingEventSource](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (Explorador de API de .NET)</span><span class="sxs-lookup"><span data-stu-id="f1527-485">[LoggingEventSource Class](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (.NET API Browser)</span></span>
* <xref:System.Diagnostics.Tracing.EventLevel>
* <span data-ttu-id="f1527-486">[Origen de referencia LoggingEventSource (3.0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs): para obtener el origen de referencia de otra versión, cambie la rama a `release/{Version}`, donde `{Version}` corresponde a la versión de ASP.NET Core deseada.</span><span class="sxs-lookup"><span data-stu-id="f1527-486">[LoggingEventSource reference source (3.0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs) &ndash; To obtain reference source for a different version, change the branch to `release/{Version}`, where `{Version}` is the version of ASP.NET Core desired.</span></span>
* <span data-ttu-id="f1527-487">[Perfview](#perfview): es útil para ver los seguimientos de origen del evento.</span><span class="sxs-lookup"><span data-stu-id="f1527-487">[Perfview](#perfview) &ndash; Useful for viewing Event Source traces.</span></span>

#### <a name="perfview"></a><span data-ttu-id="f1527-488">Perfview</span><span class="sxs-lookup"><span data-stu-id="f1527-488">Perfview</span></span>

<span data-ttu-id="f1527-489">Use la [utilidad PerfView](https://github.com/Microsoft/perfview) para recopilar y ver los registros.</span><span class="sxs-lookup"><span data-stu-id="f1527-489">Use the [PerfView utility](https://github.com/Microsoft/perfview) to collect and view logs.</span></span> <span data-ttu-id="f1527-490">Hay otras herramientas para ver los registros ETW, pero PerfView proporciona la mejor experiencia para trabajar con los eventos ETW emitidos por ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f1527-490">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET Core.</span></span>

<span data-ttu-id="f1527-491">Para configurar PerfView para la recopilación de eventos registrados por este proveedor, agregue la cadena `*Microsoft-Extensions-Logging` a la lista **Proveedores adicionales**.</span><span class="sxs-lookup"><span data-stu-id="f1527-491">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="f1527-492">(No olvide el asterisco al principio de la cadena).</span><span class="sxs-lookup"><span data-stu-id="f1527-492">(Don't miss the asterisk at the start of the string.)</span></span>

![Proveedores adicionales de Perfview](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a><span data-ttu-id="f1527-494">Proveedor EventLog de Windows</span><span class="sxs-lookup"><span data-stu-id="f1527-494">Windows EventLog provider</span></span>

<span data-ttu-id="f1527-495">El paquete de proveedor [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) envía la salida del registro al Registro de eventos de Windows.</span><span class="sxs-lookup"><span data-stu-id="f1527-495">The [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) provider package sends log output to the Windows Event Log.</span></span>

```csharp
logging.AddEventLog();
```

<span data-ttu-id="f1527-496">Las [sobrecargas de AddEventLog](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) permiten pasar <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span><span class="sxs-lookup"><span data-stu-id="f1527-496">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) let you pass in <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span></span> <span data-ttu-id="f1527-497">Si es `null` o no se especifica, se usa la siguiente configuración predeterminada:</span><span class="sxs-lookup"><span data-stu-id="f1527-497">If `null` or not specified, the following default settings are used:</span></span>

* <span data-ttu-id="f1527-498">`LogName`: "Aplicación"</span><span class="sxs-lookup"><span data-stu-id="f1527-498">`LogName` &ndash; "Application"</span></span>
* <span data-ttu-id="f1527-499">`SourceName`: "Entorno de ejecución .NET"</span><span class="sxs-lookup"><span data-stu-id="f1527-499">`SourceName` &ndash; ".NET Runtime"</span></span>
* <span data-ttu-id="f1527-500">`MachineName`: equipo local</span><span class="sxs-lookup"><span data-stu-id="f1527-500">`MachineName` &ndash; local machine</span></span>

<span data-ttu-id="f1527-501">Los eventos se registran para el [Nivel de advertencia y superior](#log-level).</span><span class="sxs-lookup"><span data-stu-id="f1527-501">Events are logged for [Warning level and higher](#log-level).</span></span> <span data-ttu-id="f1527-502">Para registrar eventos inferiores a `Warning`, establezca el nivel de registro de forma explícita.</span><span class="sxs-lookup"><span data-stu-id="f1527-502">To log events lower than `Warning`, explicitly set the log level.</span></span> <span data-ttu-id="f1527-503">Por ejemplo, agregue lo siguiente al archivo *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="f1527-503">For example, add the following to the *appsettings.json* file:</span></span>

```json
"EventLog": {
  "LogLevel": {
    "Default": "Information"
  }
}
```

### <a name="tracesource-provider"></a><span data-ttu-id="f1527-504">Proveedor TraceSource</span><span class="sxs-lookup"><span data-stu-id="f1527-504">TraceSource provider</span></span>

<span data-ttu-id="f1527-505">El paquete de proveedor [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) usa las bibliotecas y proveedores de <xref:System.Diagnostics.TraceSource>.</span><span class="sxs-lookup"><span data-stu-id="f1527-505">The [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) provider package uses the <xref:System.Diagnostics.TraceSource> libraries and providers.</span></span>

```csharp
logging.AddTraceSource(sourceSwitchName);
```

<span data-ttu-id="f1527-506">[Las sobrecargas de AddTraceSource](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) permiten pasar un modificador de origen y un agente de escucha de seguimiento.</span><span class="sxs-lookup"><span data-stu-id="f1527-506">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) let you pass in a source switch and a trace listener.</span></span>

<span data-ttu-id="f1527-507">Para usar este proveedor, una aplicación debe ejecutarse en .NET Framework (en lugar de .NET Core).</span><span class="sxs-lookup"><span data-stu-id="f1527-507">To use this provider, an app has to run on the .NET Framework (rather than .NET Core).</span></span> <span data-ttu-id="f1527-508">El proveedor puede enrutar mensajes a una variedad de [agentes de escucha](/dotnet/framework/debug-trace-profile/trace-listeners), como <xref:System.Diagnostics.TextWriterTraceListener> que se usa en la aplicación de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="f1527-508">The provider can route messages to a variety of [listeners](/dotnet/framework/debug-trace-profile/trace-listeners), such as the <xref:System.Diagnostics.TextWriterTraceListener> used in the sample app.</span></span>

### <a name="azure-app-service-provider"></a><span data-ttu-id="f1527-509">Proveedor Azure App Service</span><span class="sxs-lookup"><span data-stu-id="f1527-509">Azure App Service provider</span></span>

<span data-ttu-id="f1527-510">El paquete de proveedor [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) escribe los registros en archivos de texto en el sistema de archivos de una aplicación de Azure App Service y en [Blob Storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) en una cuenta de Azure Storage.</span><span class="sxs-lookup"><span data-stu-id="f1527-510">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span>

```csharp
logging.AddAzureWebAppDiagnostics();
```

<span data-ttu-id="f1527-511">El paquete del proveedor no se incluye en el marco compartido.</span><span class="sxs-lookup"><span data-stu-id="f1527-511">The provider package isn't included in the shared framework.</span></span> <span data-ttu-id="f1527-512">Para usar el proveedor, agregue el paquete del proveedor al proyecto.</span><span class="sxs-lookup"><span data-stu-id="f1527-512">To use the provider, add the provider package to the project.</span></span>

<span data-ttu-id="f1527-513">Para configurar las opciones de proveedor, use <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> y <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, tal y como se muestra en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="f1527-513">To configure provider settings, use <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> and <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, as shown in the following example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AzLogOptions&highlight=17-28)]

<span data-ttu-id="f1527-514">Al realizar una implementación en una aplicación de App Service, esta respeta la configuración de la sección [Registros de App Service](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) de la página **App Service** de Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="f1527-514">When you deploy to an App Service app, the application honors the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="f1527-515">Cuando se actualiza la configuración siguiente, los cambios se aplican de inmediato sin necesidad de reiniciar ni de volver a implementar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f1527-515">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="f1527-516">**Registro de la aplicación (sistema de archivos)**</span><span class="sxs-lookup"><span data-stu-id="f1527-516">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="f1527-517">**Registro de la aplicación (blob)**</span><span class="sxs-lookup"><span data-stu-id="f1527-517">**Application Logging (Blob)**</span></span>

<span data-ttu-id="f1527-518">La ubicación predeterminada de los archivos de registro es la carpeta *D:\\home\\LogFiles\\Application* y el nombre de archivo predeterminado es *diagnostics-aaaammdd.txt*.</span><span class="sxs-lookup"><span data-stu-id="f1527-518">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="f1527-519">El límite de tamaño de archivo predeterminado es 10 MB, y el número máximo predeterminado de archivos que se conservan es 2.</span><span class="sxs-lookup"><span data-stu-id="f1527-519">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="f1527-520">El nombre de blob predeterminado es *{nombre-de-la-aplicación}{marca de tiempo}/aaaa/mm/dd/hh/{guid}-applicationLog.txt*.</span><span class="sxs-lookup"><span data-stu-id="f1527-520">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="f1527-521">El proveedor solo funciona cuando el proyecto se ejecuta en el entorno de Azure.</span><span class="sxs-lookup"><span data-stu-id="f1527-521">The provider only works when the project runs in the Azure environment.</span></span> <span data-ttu-id="f1527-522">No tiene ningún efecto cuando el proyecto se ejecuta de manera local (no escribe en los archivos locales ni en el almacenamiento de desarrollo local de blobs).</span><span class="sxs-lookup"><span data-stu-id="f1527-522">It has no effect when the project is run locally&mdash;it doesn't write to local files or local development storage for blobs.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="f1527-523">Secuencias de registro de Azure</span><span class="sxs-lookup"><span data-stu-id="f1527-523">Azure log streaming</span></span>

<span data-ttu-id="f1527-524">Las secuencias de registro de Azure permiten ver la actividad de registro en tiempo real desde:</span><span class="sxs-lookup"><span data-stu-id="f1527-524">Azure log streaming lets you view log activity in real time from:</span></span>

* <span data-ttu-id="f1527-525">El servidor de aplicaciones</span><span class="sxs-lookup"><span data-stu-id="f1527-525">The app server</span></span>
* <span data-ttu-id="f1527-526">El servidor web</span><span class="sxs-lookup"><span data-stu-id="f1527-526">The web server</span></span>
* <span data-ttu-id="f1527-527">Error del seguimiento de solicitudes</span><span class="sxs-lookup"><span data-stu-id="f1527-527">Failed request tracing</span></span>

<span data-ttu-id="f1527-528">Para configurar las secuencias de registro de Azure:</span><span class="sxs-lookup"><span data-stu-id="f1527-528">To configure Azure log streaming:</span></span>

* <span data-ttu-id="f1527-529">Navegue hasta la página **Registros de App Service** desde la página de portal de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f1527-529">Navigate to the **App Service logs** page from your app's portal page.</span></span>
* <span data-ttu-id="f1527-530">Establezca **Registro de la aplicación (sistema de archivos)** en **Activado**.</span><span class="sxs-lookup"><span data-stu-id="f1527-530">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="f1527-531">Elija el **Nivel** de registro.</span><span class="sxs-lookup"><span data-stu-id="f1527-531">Choose the log **Level**.</span></span> <span data-ttu-id="f1527-532">Este valor solo se aplica a las secuencias de registro de Azure, no a otros proveedores de registro de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f1527-532">This setting only applies to Azure log streaming, not other logging providers in the app.</span></span>

<span data-ttu-id="f1527-533">Navegue hasta la página **Secuencia de registro** para consultar los mensajes de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f1527-533">Navigate to the **Log Stream** page to view app messages.</span></span> <span data-ttu-id="f1527-534">La aplicación los registra a través de la interfaz `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="f1527-534">They're logged by the app through the `ILogger` interface.</span></span>

### <a name="azure-application-insights-trace-logging"></a><span data-ttu-id="f1527-535">Registro de seguimiento de Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="f1527-535">Azure Application Insights trace logging</span></span>

<span data-ttu-id="f1527-536">El proveedor de paquete [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) escribe los registros en Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="f1527-536">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to Azure Application Insights.</span></span> <span data-ttu-id="f1527-537">Application Insights es un servicio que supervisa una aplicación web y proporciona herramientas para consultar y analizar los datos de telemetría.</span><span class="sxs-lookup"><span data-stu-id="f1527-537">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="f1527-538">Si usa este proveedor, puede consultar y analizar los registros mediante las herramientas de Application Insights.</span><span class="sxs-lookup"><span data-stu-id="f1527-538">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="f1527-539">El proveedor de registro se incluye como dependencia de [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), que es el paquete que proporciona toda la telemetría disponible para ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f1527-539">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="f1527-540">Si usa este paquete, no tiene que instalar el proveedor de paquete.</span><span class="sxs-lookup"><span data-stu-id="f1527-540">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="f1527-541">No use el paquete [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web)&mdash;que es para ASP.NET 4.x.</span><span class="sxs-lookup"><span data-stu-id="f1527-541">Don't use the [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;that's for ASP.NET 4.x.</span></span>

<span data-ttu-id="f1527-542">Para obtener más información, vea los siguientes recursos:</span><span class="sxs-lookup"><span data-stu-id="f1527-542">For more information, see the following resources:</span></span>

* [<span data-ttu-id="f1527-543">Información general de Application Insights</span><span class="sxs-lookup"><span data-stu-id="f1527-543">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="f1527-544">[Application Insights para aplicaciones de ASP.NET Core](/azure/azure-monitor/app/asp-net-core): comience aquí si quiere implementar la variedad completa de telemetría de Application Insights junto con el registro.</span><span class="sxs-lookup"><span data-stu-id="f1527-544">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="f1527-545">[ApplicationInsightsLoggerProvider para los registros de .NET Core ILogger](/azure/azure-monitor/app/ilogger): comience aquí si quiere implementar el proveedor de registro sin el resto de la telemetría de Application Insights.</span><span class="sxs-lookup"><span data-stu-id="f1527-545">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="f1527-546">[Adaptadores de registro de Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs)</span><span class="sxs-lookup"><span data-stu-id="f1527-546">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="f1527-547">[Instalación, configuración e inicialización del SDK de Application Insights](/learn/modules/instrument-web-app-code-with-application-insights): tutorial interactivo en el sitio de Microsoft Learn.</span><span class="sxs-lookup"><span data-stu-id="f1527-547">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="f1527-548">Proveedores de registro de terceros</span><span class="sxs-lookup"><span data-stu-id="f1527-548">Third-party logging providers</span></span>

<span data-ttu-id="f1527-549">Plataformas de registro de terceros que funcionan con ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="f1527-549">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="f1527-550">[elmah.io](https://elmah.io/) ([repositorio de GitHub](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="f1527-550">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="f1527-551">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([repositorio de GitHub](https://github.com/mattwcole/gelf-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="f1527-551">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="f1527-552">[JSNLog](https://jsnlog.com/) ([repositorio de GitHub](https://github.com/mperdeck/jsnlog))</span><span class="sxs-lookup"><span data-stu-id="f1527-552">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="f1527-553">[KissLog.net](https://kisslog.net/) ([repositorio de GitHub](https://github.com/catalingavan/KissLog-net))</span><span class="sxs-lookup"><span data-stu-id="f1527-553">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="f1527-554">[Log4Net](https://logging.apache.org/log4net/) ([repositorio de GitHub](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span><span class="sxs-lookup"><span data-stu-id="f1527-554">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span></span>
* <span data-ttu-id="f1527-555">[Loggr](https://loggr.net/) ([repositorio de GitHub](https://github.com/imobile3/Loggr.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="f1527-555">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="f1527-556">[NLog](https://nlog-project.org/) ([repositorio de GitHub](https://github.com/NLog/NLog.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="f1527-556">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="f1527-557">[Sentry](https://sentry.io/welcome/) ([repositorio de GitHub](https://github.com/getsentry/sentry-dotnet))</span><span class="sxs-lookup"><span data-stu-id="f1527-557">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="f1527-558">[Serilog](https://serilog.net/) ([repositorio de GitHub](https://github.com/serilog/serilog-aspnetcore))</span><span class="sxs-lookup"><span data-stu-id="f1527-558">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="f1527-559">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([repositorio de GitHub](https://github.com/googleapis/google-cloud-dotnet))</span><span class="sxs-lookup"><span data-stu-id="f1527-559">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="f1527-560">Algunas plataformas de terceros pueden realizar [registro semántico, también conocido como registro estructurado](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="f1527-560">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="f1527-561">El uso de una plataforma de terceros es similar al uso de uno de los proveedores integrados:</span><span class="sxs-lookup"><span data-stu-id="f1527-561">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="f1527-562">Agregue un paquete NuGet al proyecto.</span><span class="sxs-lookup"><span data-stu-id="f1527-562">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="f1527-563">Llame a un método de extensión `ILoggerFactory` proporcionado por el marco de registro.</span><span class="sxs-lookup"><span data-stu-id="f1527-563">Call an `ILoggerFactory` extension method provided by the logging framework.</span></span>

<span data-ttu-id="f1527-564">Para más información, vea la documentación de cada proveedor.</span><span class="sxs-lookup"><span data-stu-id="f1527-564">For more information, see each provider's documentation.</span></span> <span data-ttu-id="f1527-565">Microsoft no admite los proveedores de registro de terceros.</span><span class="sxs-lookup"><span data-stu-id="f1527-565">Third-party logging providers aren't supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f1527-566">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="f1527-566">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f1527-567">Por [Tom Dykstra](https://github.com/tdykstra) y [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="f1527-567">By [Tom Dykstra](https://github.com/tdykstra) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="f1527-568">.NET Core es compatible con una API de registro que funciona con una gran variedad de proveedores de registro integrados y de terceros.</span><span class="sxs-lookup"><span data-stu-id="f1527-568">.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="f1527-569">En este artículo se muestra cómo usar las API de registro con proveedores integrados.</span><span class="sxs-lookup"><span data-stu-id="f1527-569">This article shows how to use the logging API with built-in providers.</span></span>

<span data-ttu-id="f1527-570">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f1527-570">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="add-providers"></a><span data-ttu-id="f1527-571">Incorporación de proveedores</span><span class="sxs-lookup"><span data-stu-id="f1527-571">Add providers</span></span>

<span data-ttu-id="f1527-572">Un proveedor de registro muestra o almacena registros.</span><span class="sxs-lookup"><span data-stu-id="f1527-572">A logging provider displays or stores logs.</span></span> <span data-ttu-id="f1527-573">Por ejemplo, el proveedor de consola muestra los registros en la consola y el proveedor de Azure Application Insights los almacena en Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="f1527-573">For example, the Console provider displays logs on the console, and the Azure Application Insights provider stores them in Azure Application Insights.</span></span> <span data-ttu-id="f1527-574">Los registros se pueden enviar a varios destinos mediante la incorporación de varios proveedores.</span><span class="sxs-lookup"><span data-stu-id="f1527-574">Logs can be sent to multiple destinations by adding multiple providers.</span></span>

<span data-ttu-id="f1527-575">Para usar un proveedor, llame al método de extensión `Add{provider name}` del proveedor en *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="f1527-575">To add a provider, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=18-20)]

<span data-ttu-id="f1527-576">El código anterior requiere referencias a `Microsoft.Extensions.Logging` y `Microsoft.Extensions.Configuration`.</span><span class="sxs-lookup"><span data-stu-id="f1527-576">The preceding code requires references to `Microsoft.Extensions.Logging` and `Microsoft.Extensions.Configuration`.</span></span>

<span data-ttu-id="f1527-577">La plantilla de proyecto predeterminada llama a <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, que agrega los siguientes proveedores de registro:</span><span class="sxs-lookup"><span data-stu-id="f1527-577">The default project template calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* <span data-ttu-id="f1527-578">Consola</span><span class="sxs-lookup"><span data-stu-id="f1527-578">Console</span></span>
* <span data-ttu-id="f1527-579">Depuración</span><span class="sxs-lookup"><span data-stu-id="f1527-579">Debug</span></span>
* <span data-ttu-id="f1527-580">EventSource (a partir de ASP.NET Core 2.2)</span><span class="sxs-lookup"><span data-stu-id="f1527-580">EventSource (starting in ASP.NET Core 2.2)</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_TemplateCode&highlight=7)]

<span data-ttu-id="f1527-581">Si usa `CreateDefaultBuilder`, puede reemplazar los proveedores predeterminados por sus propios valores.</span><span class="sxs-lookup"><span data-stu-id="f1527-581">If you use `CreateDefaultBuilder`, you can replace the default providers with your own choices.</span></span> <span data-ttu-id="f1527-582">Llame a <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> y agregue los proveedores que desee.</span><span class="sxs-lookup"><span data-stu-id="f1527-582">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=18-22)]

<span data-ttu-id="f1527-583">Obtenga más información sobre los [proveedores de registro integrados](#built-in-logging-providers) y los [proveedores de registro de terceros](#third-party-logging-providers) más adelante en el artículo.</span><span class="sxs-lookup"><span data-stu-id="f1527-583">Learn more about [built-in logging providers](#built-in-logging-providers) and [third-party logging providers](#third-party-logging-providers) later in the article.</span></span>

## <a name="create-logs"></a><span data-ttu-id="f1527-584">Creación de registros</span><span class="sxs-lookup"><span data-stu-id="f1527-584">Create logs</span></span>

<span data-ttu-id="f1527-585">Para crear registros, use un objeto de <xref:Microsoft.Extensions.Logging.ILogger%601>.</span><span class="sxs-lookup"><span data-stu-id="f1527-585">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object.</span></span> <span data-ttu-id="f1527-586">En una aplicación web o servicio hospedado, obtenga un elemento `ILogger` de la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="f1527-586">In a web app or hosted service, get an `ILogger` from dependency injection (DI).</span></span> <span data-ttu-id="f1527-587">En aplicaciones de consola que no sean de host, use `LoggerFactory` para crear un elemento `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="f1527-587">In non-host console apps, use the `LoggerFactory` to create an `ILogger`.</span></span>

<span data-ttu-id="f1527-588">En el ejemplo siguiente de ASP.NET Core, se crea un registrador con `TodoApiSample.Pages.AboutModel` como la categoría.</span><span class="sxs-lookup"><span data-stu-id="f1527-588">The following ASP.NET Core example creates a logger with `TodoApiSample.Pages.AboutModel` as the category.</span></span> <span data-ttu-id="f1527-589">La *categoría* de registro es una cadena que está asociada con cada registro.</span><span class="sxs-lookup"><span data-stu-id="f1527-589">The log *category* is a string that is associated with each log.</span></span> <span data-ttu-id="f1527-590">La instancia de `ILogger<T>` proporcionada por la inserción de dependencias genera registros que tienen el nombre completo del tipo `T` como la categoría.</span><span class="sxs-lookup"><span data-stu-id="f1527-590">The `ILogger<T>` instance provided by DI creates logs that have the fully qualified name of type `T` as the category.</span></span> 

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

<span data-ttu-id="f1527-591">En los siguientes ejemplos de aplicación de consola y ASP.NET Core, el registrador se usa para crear registros con `Information` como el nivel.</span><span class="sxs-lookup"><span data-stu-id="f1527-591">In the following ASP.NET Core and console app examples, the logger is used to create logs with `Information` as the level.</span></span> <span data-ttu-id="f1527-592">El *nivel* de registro indica la gravedad del evento registrado.</span><span class="sxs-lookup"><span data-stu-id="f1527-592">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

<span data-ttu-id="f1527-593">Los [niveles](#log-level) y las [categorías](#log-category) se explican detalladamente más adelante en este artículo.</span><span class="sxs-lookup"><span data-stu-id="f1527-593">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this article.</span></span>

### <a name="create-logs-in-startup"></a><span data-ttu-id="f1527-594">Creación de registros durante el inicio</span><span class="sxs-lookup"><span data-stu-id="f1527-594">Create logs in Startup</span></span>

<span data-ttu-id="f1527-595">Para escribir registros en la clase `Startup`, incluya un parámetro `ILogger` en la signatura de construcción:</span><span class="sxs-lookup"><span data-stu-id="f1527-595">To write logs in the `Startup` class, include an `ILogger` parameter in the constructor signature:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Startup.cs?name=snippet_Startup&highlight=3,5,8,20,27)]

### <a name="create-logs-in-the-program-class"></a><span data-ttu-id="f1527-596">Crear registros en la clase del programa</span><span class="sxs-lookup"><span data-stu-id="f1527-596">Create logs in the Program class</span></span>

<span data-ttu-id="f1527-597">Para escribir registros la clase `Program`, obtenga una instancia `ILogger` de inserción de dependencias:</span><span class="sxs-lookup"><span data-stu-id="f1527-597">To write logs in the `Program` class, get an `ILogger` instance from DI:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

<span data-ttu-id="f1527-598">No se admite directamente el registro durante la construcción del host.</span><span class="sxs-lookup"><span data-stu-id="f1527-598">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="f1527-599">Sin embargo, se puede usar un registrador independiente.</span><span class="sxs-lookup"><span data-stu-id="f1527-599">However, a separate logger can be used.</span></span> <span data-ttu-id="f1527-600">En el ejemplo siguiente, se usa un registrador [Serilog](https://serilog.net/) para registrarse en `CreateWebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="f1527-600">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateWebHostBuilder`.</span></span> <span data-ttu-id="f1527-601">`AddSerilog` usa la configuración estática especificada en `Log.Logger`:</span><span class="sxs-lookup"><span data-stu-id="f1527-601">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

```csharp
using System;
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var builtConfig = new ConfigurationBuilder()
            .AddJsonFile("appsettings.json")
            .AddCommandLine(args)
            .Build();

        Log.Logger = new LoggerConfiguration()
            .WriteTo.Console()
            .WriteTo.File(builtConfig["Logging:FilePath"])
            .CreateLogger();

        try
        {
            return WebHost.CreateDefaultBuilder(args)
                .ConfigureServices((context, services) =>
                {
                    services.AddMvc();
                })
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    config.AddConfiguration(builtConfig);
                })
                .ConfigureLogging(logging =>
                {
                    logging.AddSerilog();
                })
                .UseStartup<Startup>();
        }
        catch (Exception ex)
        {
            Log.Fatal(ex, "Host builder error");

            throw;
        }
        finally
        {
            Log.CloseAndFlush();
        }
    }
}
```

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="f1527-602">No hay métodos de registrador asincrónicos</span><span class="sxs-lookup"><span data-stu-id="f1527-602">No asynchronous logger methods</span></span>

<span data-ttu-id="f1527-603">El registro debe ser tan rápido que no merezca la pena el costo de rendimiento del código asincrónico.</span><span class="sxs-lookup"><span data-stu-id="f1527-603">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="f1527-604">Si el almacén de datos de registro es lento, no escriba directamente en él.</span><span class="sxs-lookup"><span data-stu-id="f1527-604">If your logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="f1527-605">Considere la posibilidad de escribir los mensajes de registro en un almacén rápido inicialmente y luego moverlos a la tienda lenta.</span><span class="sxs-lookup"><span data-stu-id="f1527-605">Consider writing the log messages to a fast store initially, then move them to the slow store later.</span></span> <span data-ttu-id="f1527-606">Por ejemplo, si inicia sesión en SQL Server, no desea hacerlo directamente en un método `Log`, ya que los métodos `Log` son sincrónicos.</span><span class="sxs-lookup"><span data-stu-id="f1527-606">For example, if you're logging to SQL Server, you don't want to do that directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="f1527-607">En su lugar, agregue sincrónicamente mensajes de registro a una cola en memoria y haga que un trabajo en segundo plano extraiga los mensajes de la cola para realizar el trabajo asincrónico de insertar datos en SQL Server.</span><span class="sxs-lookup"><span data-stu-id="f1527-607">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span> <span data-ttu-id="f1527-608">Para obtener más información, vea [este](https://github.com/dotnet/AspNetCore.Docs/issues/11801) problema de GitHub.</span><span class="sxs-lookup"><span data-stu-id="f1527-608">For more information, see [this](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub issue.</span></span>

## <a name="configuration"></a><span data-ttu-id="f1527-609">Configuración</span><span class="sxs-lookup"><span data-stu-id="f1527-609">Configuration</span></span>

<span data-ttu-id="f1527-610">Uno o varios proveedores de configuración proporcionan la configuración del proveedor de registro:</span><span class="sxs-lookup"><span data-stu-id="f1527-610">Logging provider configuration is provided by one or more configuration providers:</span></span>

* <span data-ttu-id="f1527-611">Formatos de archivo (INI, JSON y XML).</span><span class="sxs-lookup"><span data-stu-id="f1527-611">File formats (INI, JSON, and XML).</span></span>
* <span data-ttu-id="f1527-612">Argumentos de la línea de comandos.</span><span class="sxs-lookup"><span data-stu-id="f1527-612">Command-line arguments.</span></span>
* <span data-ttu-id="f1527-613">Variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="f1527-613">Environment variables.</span></span>
* <span data-ttu-id="f1527-614">Objetos de .NET en memoria.</span><span class="sxs-lookup"><span data-stu-id="f1527-614">In-memory .NET objects.</span></span>
* <span data-ttu-id="f1527-615">El almacenamiento de [administrador secreto](xref:security/app-secrets) sin cifrar.</span><span class="sxs-lookup"><span data-stu-id="f1527-615">The unencrypted [Secret Manager](xref:security/app-secrets) storage.</span></span>
* <span data-ttu-id="f1527-616">Un almacén de usuario cifrado, como [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="f1527-616">An encrypted user store, such as [Azure Key Vault](xref:security/key-vault-configuration).</span></span>
* <span data-ttu-id="f1527-617">Proveedores personalizados (instalados o creados).</span><span class="sxs-lookup"><span data-stu-id="f1527-617">Custom providers (installed or created).</span></span>

<span data-ttu-id="f1527-618">Por ejemplo, la sección `Logging` de archivos de configuración de aplicación suele proporcionar la configuración de registro.</span><span class="sxs-lookup"><span data-stu-id="f1527-618">For example, logging configuration is commonly provided by the `Logging` section of app settings files.</span></span> <span data-ttu-id="f1527-619">En el ejemplo siguiente se muestra el contenido de un archivo *appsettings.Development.json* típico:</span><span class="sxs-lookup"><span data-stu-id="f1527-619">The following example shows the contents of a typical *appsettings.Development.json* file:</span></span>

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Debug",
      "System": "Information",
      "Microsoft": "Information"
    },
    "Console":
    {
      "IncludeScopes": true
    }
  }
}
```

<span data-ttu-id="f1527-620">La propiedad `Logging` puede tener `LogLevel` y propiedades del proveedor de registro (se muestra la consola).</span><span class="sxs-lookup"><span data-stu-id="f1527-620">The `Logging` property can have `LogLevel` and log provider properties (Console is shown).</span></span>

<span data-ttu-id="f1527-621">La propiedad `LogLevel` bajo `Logging` especifica el [nivel](#log-level) mínimo que se va a registrar para las categorías seleccionadas.</span><span class="sxs-lookup"><span data-stu-id="f1527-621">The `LogLevel` property under `Logging` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="f1527-622">En el ejemplo, las categorías `System` y `Microsoft` se registran en el nivel `Information` y todas las demás se registran en el nivel `Debug`.</span><span class="sxs-lookup"><span data-stu-id="f1527-622">In the example, `System` and `Microsoft` categories log at `Information` level, and all others log at `Debug` level.</span></span>

<span data-ttu-id="f1527-623">Otras propiedades bajo `Logging` especifican proveedores de registro.</span><span class="sxs-lookup"><span data-stu-id="f1527-623">Other properties under `Logging` specify logging providers.</span></span> <span data-ttu-id="f1527-624">El ejemplo es para el proveedor de consola.</span><span class="sxs-lookup"><span data-stu-id="f1527-624">The example is for the Console provider.</span></span> <span data-ttu-id="f1527-625">Si un proveedor admite [ámbitos de registro](#log-scopes), `IncludeScopes` indica si están habilitados.</span><span class="sxs-lookup"><span data-stu-id="f1527-625">If a provider supports [log scopes](#log-scopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="f1527-626">Una propiedad de proveedor (como `Console` en el ejemplo) también puede especificar una propiedad `LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="f1527-626">A provider property (such as `Console` in the example) may also specify a `LogLevel` property.</span></span> <span data-ttu-id="f1527-627">`LogLevel` en un proveedor especifica niveles de registro para ese proveedor.</span><span class="sxs-lookup"><span data-stu-id="f1527-627">`LogLevel` under a provider specifies levels to log for that provider.</span></span>

<span data-ttu-id="f1527-628">Si los niveles se especifican en `Logging.{providername}.LogLevel`, invalidan todo lo establecido en `Logging.LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="f1527-628">If levels are specified in `Logging.{providername}.LogLevel`, they override anything set in `Logging.LogLevel`.</span></span>

<span data-ttu-id="f1527-629">La API de registro no incluye un escenario que permita cambiar los niveles de registro mientras se ejecuta una aplicación.</span><span class="sxs-lookup"><span data-stu-id="f1527-629">The Logging API doesn't include a scenario to change log levels while an app is running.</span></span> <span data-ttu-id="f1527-630">No obstante, algunos proveedores de configuración pueden volver a cargar la configuración, lo que tiene efecto inmediato en la configuración del registro.</span><span class="sxs-lookup"><span data-stu-id="f1527-630">However, some configuration providers are capable of reloading configuration, which takes immediate effect on logging configuration.</span></span> <span data-ttu-id="f1527-631">Por ejemplo, [FileConfigurationProvider](xref:fundamentals/configuration/index#file-configuration-provider) —agregado por `CreateDefaultBuilder` para leer los archivos de configuración— vuelve a cargar la configuración de registro de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="f1527-631">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider), which is added by `CreateDefaultBuilder` to read settings files, reloads logging configuration by default.</span></span> <span data-ttu-id="f1527-632">Si se cambia la configuración en el código mientras se ejecuta una aplicación, la aplicación puede llamar a [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) para actualizar la configuración de registro de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f1527-632">If configuration is changed in code while an app is running, the app can call [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) to update the app's logging configuration.</span></span>

<span data-ttu-id="f1527-633">Para obtener información sobre cómo implementar proveedores de configuración, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="f1527-633">For information on implementing configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="sample-logging-output"></a><span data-ttu-id="f1527-634">Salida de registro de ejemplo</span><span class="sxs-lookup"><span data-stu-id="f1527-634">Sample logging output</span></span>

<span data-ttu-id="f1527-635">Con el código de ejemplo que se muestra en la sección anterior, los registros aparecen en la consola cuando la aplicación se ejecuta desde la línea de comandos.</span><span class="sxs-lookup"><span data-stu-id="f1527-635">With the sample code shown in the preceding section, logs appear in the console when the app is run from the command line.</span></span> <span data-ttu-id="f1527-636">Este es un ejemplo de salida de la consola:</span><span class="sxs-lookup"><span data-stu-id="f1527-636">Here's an example of console output:</span></span>

```console
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/api/todo/0
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
info: TodoApi.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 42.9286ms
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[2]
      Request finished in 148.889ms 404
```

<span data-ttu-id="f1527-637">Los registros anteriores se generaron mediante la realización de una solicitud HTTP GET a la aplicación de ejemplo en `http://localhost:5000/api/todo/0`.</span><span class="sxs-lookup"><span data-stu-id="f1527-637">The preceding logs were generated by making an HTTP Get request to the sample app at `http://localhost:5000/api/todo/0`.</span></span>

<span data-ttu-id="f1527-638">Este es un ejemplo de los mismos registros tal y como aparecen en la ventana de depuración cuando se ejecuta la aplicación de ejemplo en Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="f1527-638">Here's an example of the same logs as they appear in the Debug window when you run the sample app in Visual Studio:</span></span>

```console
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:53104/api/todo/0  
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item 0
TodoApi.Controllers.TodoController:Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 152.5657ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 316.3195ms 404
```

<span data-ttu-id="f1527-639">Los registros creados por las llamadas de `ILogger` se muestran en la sección anterior, empezando por “TodoApi”.</span><span class="sxs-lookup"><span data-stu-id="f1527-639">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApi".</span></span> <span data-ttu-id="f1527-640">Los registros que comienzan por categorías de "Microsoft" son del código de marco de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f1527-640">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="f1527-641">ASP.NET Core y el código de la aplicación usan la misma API y los mismos proveedores de registro.</span><span class="sxs-lookup"><span data-stu-id="f1527-641">ASP.NET Core and application code are using the same logging API and providers.</span></span>

<span data-ttu-id="f1527-642">En el resto de este artículo se explican algunos detalles y opciones para el registro.</span><span class="sxs-lookup"><span data-stu-id="f1527-642">The remainder of this article explains some details and options for logging.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="f1527-643">Paquetes NuGet</span><span class="sxs-lookup"><span data-stu-id="f1527-643">NuGet packages</span></span>

<span data-ttu-id="f1527-644">Las interfaces `ILogger` e `ILoggerFactory` se encuentran en [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), y sus implementaciones predeterminadas en [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span><span class="sxs-lookup"><span data-stu-id="f1527-644">The `ILogger` and `ILoggerFactory` interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), and default implementations for them are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

## <a name="log-category"></a><span data-ttu-id="f1527-645">Categoría de registro</span><span class="sxs-lookup"><span data-stu-id="f1527-645">Log category</span></span>

<span data-ttu-id="f1527-646">Cuando se crea un objeto `ILogger`, se ha especificado una *categoría* para él.</span><span class="sxs-lookup"><span data-stu-id="f1527-646">When an `ILogger` object is created, a *category* is specified for it.</span></span> <span data-ttu-id="f1527-647">Esa categoría se incluye con cada mensaje de registro creado por esa instancia de `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="f1527-647">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="f1527-648">La categoría puede ser cualquier cadena, pero la convención es usar el nombre de clase, como "TodoApi.Controllers.TodoController".</span><span class="sxs-lookup"><span data-stu-id="f1527-648">The category may be any string, but the convention is to use the class name, such as "TodoApi.Controllers.TodoController".</span></span>

<span data-ttu-id="f1527-649">Use `ILogger<T>` para obtener una instancia `ILogger` que utiliza el nombre de tipo completo de `T` como la categoría:</span><span class="sxs-lookup"><span data-stu-id="f1527-649">Use `ILogger<T>` to get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

<span data-ttu-id="f1527-650">Para especificar explícitamente la categoría, llame a `ILoggerFactory.CreateLogger`:</span><span class="sxs-lookup"><span data-stu-id="f1527-650">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

<span data-ttu-id="f1527-651">`ILogger<T>` es equivale a llamar a `CreateLogger` con el nombre de tipo completo de `T`.</span><span class="sxs-lookup"><span data-stu-id="f1527-651">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

## <a name="log-level"></a><span data-ttu-id="f1527-652">Nivel de registro</span><span class="sxs-lookup"><span data-stu-id="f1527-652">Log level</span></span>

<span data-ttu-id="f1527-653">Cara registro especifica un valor <xref:Microsoft.Extensions.Logging.LogLevel>.</span><span class="sxs-lookup"><span data-stu-id="f1527-653">Every log specifies a <xref:Microsoft.Extensions.Logging.LogLevel> value.</span></span> <span data-ttu-id="f1527-654">El nivel de registro indica la gravedad o importancia.</span><span class="sxs-lookup"><span data-stu-id="f1527-654">The log level indicates the severity or importance.</span></span> <span data-ttu-id="f1527-655">Por ejemplo, podría escribir un registro `Information` cuando un método termina con normalidad y un registro `Warning` cuando un método devuelve un código de estado *404 No encontrado*.</span><span class="sxs-lookup"><span data-stu-id="f1527-655">For example, you might write an `Information` log when a method ends normally and a `Warning` log when a method returns a *404 Not Found* status code.</span></span>

<span data-ttu-id="f1527-656">El siguiente código crea los registros `Information` y `Warning`:</span><span class="sxs-lookup"><span data-stu-id="f1527-656">The following code creates `Information` and `Warning` logs:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="f1527-657">En el código anterior, el primer parámetro es el [id. de evento del registro](#log-event-id).</span><span class="sxs-lookup"><span data-stu-id="f1527-657">In the preceding code, the first parameter is the [Log event ID](#log-event-id).</span></span> <span data-ttu-id="f1527-658">El segundo parámetro es una plantilla de mensaje con marcadores de posición para los valores de argumento proporcionados por el resto de parámetros de método.</span><span class="sxs-lookup"><span data-stu-id="f1527-658">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="f1527-659">Los parámetros de método se explican detalladamente en la [sección de la plantilla de mensaje](#log-message-template) más adelante en este artículo.</span><span class="sxs-lookup"><span data-stu-id="f1527-659">The method parameters are explained in the [message template section](#log-message-template) later in this article.</span></span>

<span data-ttu-id="f1527-660">Los métodos de registro que incluyen el nivel en el nombre del método (por ejemplo `LogInformation` y `LogWarning`) son [métodos de extensión para ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span><span class="sxs-lookup"><span data-stu-id="f1527-660">Log methods that include the level in the method name (for example, `LogInformation` and `LogWarning`) are [extension methods for ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span></span> <span data-ttu-id="f1527-661">Estos métodos llaman a un método `Log` que toma un parámetro `LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="f1527-661">These methods call a `Log` method that takes a `LogLevel` parameter.</span></span> <span data-ttu-id="f1527-662">Puede llamar directamente al método `Log` en lugar de a uno de estos métodos de extensión, pero la sintaxis es relativamente complicada.</span><span class="sxs-lookup"><span data-stu-id="f1527-662">You can call the `Log` method directly rather than one of these extension methods, but the syntax is relatively complicated.</span></span> <span data-ttu-id="f1527-663">Para más información, vea la <xref:Microsoft.Extensions.Logging.ILogger> y el [código fuente de las extensiones de registrador](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="f1527-663">For more information, see <xref:Microsoft.Extensions.Logging.ILogger> and the [logger extensions source code](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span>

<span data-ttu-id="f1527-664">ASP.NET Core define los niveles de registro siguientes, que aquí se ordenan de menor a mayor gravedad.</span><span class="sxs-lookup"><span data-stu-id="f1527-664">ASP.NET Core defines the following log levels, ordered here from lowest to highest severity.</span></span>

* <span data-ttu-id="f1527-665">Seguimiento = 0</span><span class="sxs-lookup"><span data-stu-id="f1527-665">Trace = 0</span></span>

  <span data-ttu-id="f1527-666">Para información que normalmente solo es útil para la depuración.</span><span class="sxs-lookup"><span data-stu-id="f1527-666">For information that's typically valuable only for debugging.</span></span> <span data-ttu-id="f1527-667">Estos mensajes pueden contener datos confidenciales de la aplicación, por lo que no deben habilitarse en un entorno de producción.</span><span class="sxs-lookup"><span data-stu-id="f1527-667">These messages may contain sensitive application data and so shouldn't be enabled in a production environment.</span></span> <span data-ttu-id="f1527-668">*Deshabilitado de forma predeterminada.*</span><span class="sxs-lookup"><span data-stu-id="f1527-668">*Disabled by default.*</span></span>

* <span data-ttu-id="f1527-669">Depurar = 1</span><span class="sxs-lookup"><span data-stu-id="f1527-669">Debug = 1</span></span>

  <span data-ttu-id="f1527-670">Para información que puede ser útil para el desarrollo y la depuración.</span><span class="sxs-lookup"><span data-stu-id="f1527-670">For information that may be useful in development and debugging.</span></span> <span data-ttu-id="f1527-671">Ejemplo: `Entering method Configure with flag set to true.` Habilite los registros de nivel `Debug` en producción cuando esté solucionando un problema, debido al elevado volumen de registros.</span><span class="sxs-lookup"><span data-stu-id="f1527-671">Example: `Entering method Configure with flag set to true.` Enable `Debug` level logs in production only when troubleshooting, due to the high volume of logs.</span></span>

* <span data-ttu-id="f1527-672">Información = 2</span><span class="sxs-lookup"><span data-stu-id="f1527-672">Information = 2</span></span>

  <span data-ttu-id="f1527-673">Para realizar el seguimiento del flujo general de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f1527-673">For tracking the general flow of the app.</span></span> <span data-ttu-id="f1527-674">Estos registros suelen tener algún valor a largo plazo.</span><span class="sxs-lookup"><span data-stu-id="f1527-674">These logs typically have some long-term value.</span></span> <span data-ttu-id="f1527-675">Ejemplo: `Request received for path /api/todo`</span><span class="sxs-lookup"><span data-stu-id="f1527-675">Example: `Request received for path /api/todo`</span></span>

* <span data-ttu-id="f1527-676">Advertencia = 3</span><span class="sxs-lookup"><span data-stu-id="f1527-676">Warning = 3</span></span>

  <span data-ttu-id="f1527-677">Para los eventos anómalos o inesperados en el flujo de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f1527-677">For abnormal or unexpected events in the app flow.</span></span> <span data-ttu-id="f1527-678">Estos pueden incluir errores u otras condiciones que no hacen que la aplicación se detenga, pero que puede que sea necesario investigar.</span><span class="sxs-lookup"><span data-stu-id="f1527-678">These may include errors or other conditions that don't cause the app to stop but might need to be investigated.</span></span> <span data-ttu-id="f1527-679">Las excepciones controladas son un lugar común para usar el nivel de registro `Warning`.</span><span class="sxs-lookup"><span data-stu-id="f1527-679">Handled exceptions are a common place to use the `Warning` log level.</span></span> <span data-ttu-id="f1527-680">Ejemplo: `FileNotFoundException for file quotes.txt.`</span><span class="sxs-lookup"><span data-stu-id="f1527-680">Example: `FileNotFoundException for file quotes.txt.`</span></span>

* <span data-ttu-id="f1527-681">Error = 4</span><span class="sxs-lookup"><span data-stu-id="f1527-681">Error = 4</span></span>

  <span data-ttu-id="f1527-682">Para los errores y excepciones que no se pueden controlar.</span><span class="sxs-lookup"><span data-stu-id="f1527-682">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="f1527-683">Estos mensajes indican un error en la actividad u operación actual (por ejemplo, la solicitud HTTP actual), no un error de toda la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f1527-683">These messages indicate a failure in the current activity or operation (such as the current HTTP request), not an app-wide failure.</span></span> <span data-ttu-id="f1527-684">Mensaje de registro de ejemplo: `Cannot insert record due to duplicate key violation.`</span><span class="sxs-lookup"><span data-stu-id="f1527-684">Example log message: `Cannot insert record due to duplicate key violation.`</span></span>

* <span data-ttu-id="f1527-685">Crítico = 5</span><span class="sxs-lookup"><span data-stu-id="f1527-685">Critical = 5</span></span>

  <span data-ttu-id="f1527-686">Para los errores que requieren atención inmediata.</span><span class="sxs-lookup"><span data-stu-id="f1527-686">For failures that require immediate attention.</span></span> <span data-ttu-id="f1527-687">Ejemplos: escenarios de pérdida de datos, espacio en disco insuficiente.</span><span class="sxs-lookup"><span data-stu-id="f1527-687">Examples: data loss scenarios, out of disk space.</span></span>

<span data-ttu-id="f1527-688">Use el nivel de registro para controlar la cantidad de salida del registro que se escribe en un medio de almacenamiento determinado o ventana de presentación.</span><span class="sxs-lookup"><span data-stu-id="f1527-688">Use the log level to control how much log output is written to a particular storage medium or display window.</span></span> <span data-ttu-id="f1527-689">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="f1527-689">For example:</span></span>

* <span data-ttu-id="f1527-690">En producción:</span><span class="sxs-lookup"><span data-stu-id="f1527-690">In production:</span></span>
  * <span data-ttu-id="f1527-691">El registro en los niveles `Trace` a `Information` genera un gran volumen de mensajes de registro detallados.</span><span class="sxs-lookup"><span data-stu-id="f1527-691">Logging at the `Trace` through `Information` levels produces a high-volume of detailed log messages.</span></span> <span data-ttu-id="f1527-692">Para controlar los costos y no superar los límites de almacenamiento de datos, registre los mensajes de nivel `Trace` a `Information` en un almacén de datos de alto volumen y bajo costo.</span><span class="sxs-lookup"><span data-stu-id="f1527-692">To control costs and not exceed data storage limits, log `Trace` through `Information` level messages to a high-volume, low-cost data store.</span></span>
  * <span data-ttu-id="f1527-693">El registro en los niveles `Warning` a `Critical` normalmente produce menos mensajes de registro y de menor tamaño.</span><span class="sxs-lookup"><span data-stu-id="f1527-693">Logging at `Warning` through `Critical` levels typically produces fewer, smaller log messages.</span></span> <span data-ttu-id="f1527-694">Por lo tanto, los costos y los límites de almacenamiento no suelen ser un problema, lo que da lugar a una mayor flexibilidad a la hora de elegir el almacén de datos.</span><span class="sxs-lookup"><span data-stu-id="f1527-694">Therefore, costs and storage limits usually aren't a concern, which results in greater flexibility of data store choice.</span></span>
* <span data-ttu-id="f1527-695">Durante el desarrollo:</span><span class="sxs-lookup"><span data-stu-id="f1527-695">During development:</span></span>
  * <span data-ttu-id="f1527-696">Registre los mensajes `Warning` a `Critical` en la consola.</span><span class="sxs-lookup"><span data-stu-id="f1527-696">Log `Warning` through `Critical` messages to the console.</span></span>
  * <span data-ttu-id="f1527-697">Agregue los mensajes `Trace` a `Information` al solucionar problemas.</span><span class="sxs-lookup"><span data-stu-id="f1527-697">Add `Trace` through `Information` messages when troubleshooting.</span></span>

<span data-ttu-id="f1527-698">En la sección [Filtrado del registro](#log-filtering) de este artículo se explica cómo controlar los niveles de registro que controla un proveedor.</span><span class="sxs-lookup"><span data-stu-id="f1527-698">The [Log filtering](#log-filtering) section later in this article explains how to control which log levels a provider handles.</span></span>

<span data-ttu-id="f1527-699">ASP.NET Core escribe registros de eventos de marco.</span><span class="sxs-lookup"><span data-stu-id="f1527-699">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="f1527-700">En los ejemplos de registro anteriores de este artículo se excluyeron los registros por debajo del nivel `Information`, por lo que no se crearon los registros de nivel `Debug` o `Trace`.</span><span class="sxs-lookup"><span data-stu-id="f1527-700">The log examples earlier in this article excluded logs below `Information` level, so no `Debug` or `Trace` level logs were created.</span></span> <span data-ttu-id="f1527-701">Este es un ejemplo de registros de consola generados mediante la ejecución de la aplicación de ejemplo configurada para mostrar registros `Debug`:</span><span class="sxs-lookup"><span data-stu-id="f1527-701">Here's an example of console logs produced by running the sample app configured to show `Debug` logs:</span></span>

```console
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:62555/api/todo/0
dbug: Microsoft.AspNetCore.Routing.Tree.TreeRouter[1]
      Request successfully matched the route with name 'GetTodo' and template 'api/Todo/{id}'.
dbug: Microsoft.AspNetCore.Mvc.Internal.ActionSelector[2]
      Action 'TodoApi.Controllers.TodoController.Update (TodoApi)' with id '089d59b6-92ec-472d-b552-cc613dfd625d' did not match the constraint 'Microsoft.AspNetCore.Mvc.Internal.HttpMethodActionConstraint'
dbug: Microsoft.AspNetCore.Mvc.Internal.ActionSelector[2]
      Action 'TodoApi.Controllers.TodoController.Delete (TodoApi)' with id 'f3476abe-4bd9-4ad3-9261-3ead09607366' did not match the constraint 'Microsoft.AspNetCore.Mvc.Internal.HttpMethodActionConstraint'
dbug: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action TodoApi.Controllers.TodoController.GetById (TodoApi)
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
info: TodoApi.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
dbug: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action method TodoApi.Controllers.TodoController.GetById (TodoApi), returned result Microsoft.AspNetCore.Mvc.NotFoundResult.
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 0.8788ms
dbug: Microsoft.AspNetCore.Server.Kestrel[9]
      Connection id "0HL6L7NEFF2QD" completed keep alive response.
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[2]
      Request finished in 2.7286ms 404
```

## <a name="log-event-id"></a><span data-ttu-id="f1527-702">Id. de evento del registro</span><span class="sxs-lookup"><span data-stu-id="f1527-702">Log event ID</span></span>

<span data-ttu-id="f1527-703">Cada registro se puede especificar un *id. de evento*.</span><span class="sxs-lookup"><span data-stu-id="f1527-703">Each log can specify an *event ID*.</span></span> <span data-ttu-id="f1527-704">La aplicación de ejemplo lo hace mediante una clase `LoggingEvents` definida de forma local:</span><span class="sxs-lookup"><span data-stu-id="f1527-704">The sample app does this by using a locally defined `LoggingEvents` class:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

<span data-ttu-id="f1527-705">Un id. de evento asocia un conjunto de eventos.</span><span class="sxs-lookup"><span data-stu-id="f1527-705">An event ID associates a set of events.</span></span> <span data-ttu-id="f1527-706">Por ejemplo, todos los registros relacionados con la presentación de una lista de elementos en una página podrían ser 1001.</span><span class="sxs-lookup"><span data-stu-id="f1527-706">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="f1527-707">El proveedor de registro puede almacenar el id. de evento en un campo de identificador, en el mensaje de registro o no almacenarlo.</span><span class="sxs-lookup"><span data-stu-id="f1527-707">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="f1527-708">El proveedor de depuración no muestra los identificadores de evento.</span><span class="sxs-lookup"><span data-stu-id="f1527-708">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="f1527-709">El proveedor de consola muestra los identificadores de evento entre corchetes después de la categoría:</span><span class="sxs-lookup"><span data-stu-id="f1527-709">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a><span data-ttu-id="f1527-710">Plantilla de mensaje de registro</span><span class="sxs-lookup"><span data-stu-id="f1527-710">Log message template</span></span>

<span data-ttu-id="f1527-711">Cada registro especifica una plantilla de mensaje.</span><span class="sxs-lookup"><span data-stu-id="f1527-711">Each log specifies a message template.</span></span> <span data-ttu-id="f1527-712">La plantilla de mensaje puede contener marcadores de posición para los que se proporcionan argumentos.</span><span class="sxs-lookup"><span data-stu-id="f1527-712">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="f1527-713">Use los nombres de los marcadores de posición, no números.</span><span class="sxs-lookup"><span data-stu-id="f1527-713">Use names for the placeholders, not numbers.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="f1527-714">El orden de los marcadores de posición, no sus nombres, determina qué parámetros se usan para proporcionar sus valores.</span><span class="sxs-lookup"><span data-stu-id="f1527-714">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="f1527-715">En el código siguiente, tenga en cuenta que los nombres de parámetro están fuera de la secuencia en la plantilla de mensaje:</span><span class="sxs-lookup"><span data-stu-id="f1527-715">In the following code, notice that the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="f1527-716">Este código crea un mensaje de registro con los valores de parámetro en secuencia:</span><span class="sxs-lookup"><span data-stu-id="f1527-716">This code creates a log message with the parameter values in sequence:</span></span>

```text
Parameter values: parm1, parm2
```

<span data-ttu-id="f1527-717">La plataforma de registro funciona de esta manera para que los proveedores de registro puedan implementar [el registro semántico, también conocido como registro estructurado](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="f1527-717">The logging framework works this way so that logging providers can implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span> <span data-ttu-id="f1527-718">Los propios argumentos se pasan al sistema de registro, no solo a la plantilla de mensaje con formato.</span><span class="sxs-lookup"><span data-stu-id="f1527-718">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="f1527-719">Esta información permite a los proveedores de registro almacenar los valores de parámetro como campos.</span><span class="sxs-lookup"><span data-stu-id="f1527-719">This information enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="f1527-720">Por ejemplo, suponga que las llamadas del método del registrador tiene el aspecto siguiente:</span><span class="sxs-lookup"><span data-stu-id="f1527-720">For example, suppose logger method calls look like this:</span></span>

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="f1527-721">Si envía los registros a Azure Table Storage, cada entidad de Azure Table puede tener propiedades `ID` y `RequestTime`, lo que simplifica las consultas en los datos de registro.</span><span class="sxs-lookup"><span data-stu-id="f1527-721">If you're sending the logs to Azure Table Storage, each Azure Table entity can have `ID` and `RequestTime` properties, which simplifies queries on log data.</span></span> <span data-ttu-id="f1527-722">Una consulta puede buscar todos los registros dentro de un intervalo `RequestTime` determinado sin analizar el tiempo de espera del mensaje de texto.</span><span class="sxs-lookup"><span data-stu-id="f1527-722">A query can find all logs within a particular `RequestTime` range without parsing the time out of the text message.</span></span>

## <a name="logging-exceptions"></a><span data-ttu-id="f1527-723">Excepciones de registro</span><span class="sxs-lookup"><span data-stu-id="f1527-723">Logging exceptions</span></span>

<span data-ttu-id="f1527-724">Los métodos de registrador tienen sobrecargas que le permiten pasar una excepción, como en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="f1527-724">The logger methods have overloads that let you pass in an exception, as in the following example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

<span data-ttu-id="f1527-725">Cada proveedor controla la información de la excepción de maneras diferentes.</span><span class="sxs-lookup"><span data-stu-id="f1527-725">Different providers handle the exception information in different ways.</span></span> <span data-ttu-id="f1527-726">Este es un ejemplo de salida del proveedor de depuración del código mostrado anteriormente.</span><span class="sxs-lookup"><span data-stu-id="f1527-726">Here's an example of Debug provider output from the code shown above.</span></span>

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a><span data-ttu-id="f1527-727">Filtrado del registro</span><span class="sxs-lookup"><span data-stu-id="f1527-727">Log filtering</span></span>

<span data-ttu-id="f1527-728">Puede especificar un nivel de registro mínimo para un proveedor y una categoría específicos, o para todos los proveedores o todas las categorías.</span><span class="sxs-lookup"><span data-stu-id="f1527-728">You can specify a minimum log level for a specific provider and category or for all providers or all categories.</span></span> <span data-ttu-id="f1527-729">Los registros por debajo del nivel mínimo no se pasan a ese proveedor, de modo que no se muestran o almacenan.</span><span class="sxs-lookup"><span data-stu-id="f1527-729">Any logs below the minimum level aren't passed to that provider, so they don't get displayed or stored.</span></span>

<span data-ttu-id="f1527-730">Para suprimir todos los registros, especifique `LogLevel.None` como el nivel de registro mínimo.</span><span class="sxs-lookup"><span data-stu-id="f1527-730">To suppress all logs, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="f1527-731">El valor entero de `LogLevel.None` es 6, que es superior a `LogLevel.Critical` (5).</span><span class="sxs-lookup"><span data-stu-id="f1527-731">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

### <a name="create-filter-rules-in-configuration"></a><span data-ttu-id="f1527-732">Creación de reglas de filtro en la configuración</span><span class="sxs-lookup"><span data-stu-id="f1527-732">Create filter rules in configuration</span></span>

<span data-ttu-id="f1527-733">El código de la plantilla de proyecto llama a `CreateDefaultBuilder` para configurar el registro para los proveedores de Console, Debug y EventSource (ASP.NET Core 2.2 o versiones posteriores).</span><span class="sxs-lookup"><span data-stu-id="f1527-733">The project template code calls `CreateDefaultBuilder` to set up logging for the Console, Debug, and EventSource (ASP.NET Core 2.2 or later) providers.</span></span> <span data-ttu-id="f1527-734">El método `CreateDefaultBuilder` configura el registro para buscar la configuración en una sección de `Logging`, como se explica [anteriormente en este artículo](#configuration).</span><span class="sxs-lookup"><span data-stu-id="f1527-734">The `CreateDefaultBuilder` method sets up logging to look for configuration in a `Logging` section, as explained [earlier in this article](#configuration).</span></span>

<span data-ttu-id="f1527-735">Los datos de configuración especifican niveles de registro mínimo por proveedor y categoría, como en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="f1527-735">The configuration data specifies minimum log levels by provider and category, as in the following example:</span></span>

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

<span data-ttu-id="f1527-736">Este archivo JSON crea seis reglas de filtro, una para el proveedor de depuración, cuatro para el proveedor de la consola y una para todos los proveedores.</span><span class="sxs-lookup"><span data-stu-id="f1527-736">This JSON creates six filter rules: one for the Debug provider, four for the Console provider, and one for all providers.</span></span> <span data-ttu-id="f1527-737">Se elige una sola regla para cada proveedor cuando se crea un objeto `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="f1527-737">A single rule is chosen for each provider when an `ILogger` object is created.</span></span>

### <a name="filter-rules-in-code"></a><span data-ttu-id="f1527-738">Reglas de filtro en el código</span><span class="sxs-lookup"><span data-stu-id="f1527-738">Filter rules in code</span></span>

<span data-ttu-id="f1527-739">En el siguiente ejemplo se muestra cómo registrar reglas de filtro en el código:</span><span class="sxs-lookup"><span data-stu-id="f1527-739">The following example shows how to register filter rules in code:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

<span data-ttu-id="f1527-740">El segundo `AddFilter` especifica el proveedor de depuración mediante su nombre de tipo.</span><span class="sxs-lookup"><span data-stu-id="f1527-740">The second `AddFilter` specifies the Debug provider by using its type name.</span></span> <span data-ttu-id="f1527-741">El primer `AddFilter` se aplica a todos los proveedores, dado que no especifica un tipo de proveedor.</span><span class="sxs-lookup"><span data-stu-id="f1527-741">The first `AddFilter` applies to all providers because it doesn't specify a provider type.</span></span>

### <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="f1527-742">Cómo se aplican las reglas de filtro</span><span class="sxs-lookup"><span data-stu-id="f1527-742">How filtering rules are applied</span></span>

<span data-ttu-id="f1527-743">Los datos de configuración y el código de `AddFilter` que se muestran en los ejemplos anteriores crean las reglas que se muestran en la tabla siguiente.</span><span class="sxs-lookup"><span data-stu-id="f1527-743">The configuration data and the `AddFilter` code shown in the preceding examples create the rules shown in the following table.</span></span> <span data-ttu-id="f1527-744">Las seis primeras proceden del ejemplo de configuración y las dos últimas del ejemplo de código.</span><span class="sxs-lookup"><span data-stu-id="f1527-744">The first six come from the configuration example and the last two come from the code example.</span></span>

| <span data-ttu-id="f1527-745">número</span><span class="sxs-lookup"><span data-stu-id="f1527-745">Number</span></span> | <span data-ttu-id="f1527-746">Proveedor</span><span class="sxs-lookup"><span data-stu-id="f1527-746">Provider</span></span>      | <span data-ttu-id="f1527-747">Categorías que comienzan por...</span><span class="sxs-lookup"><span data-stu-id="f1527-747">Categories that begin with ...</span></span>          | <span data-ttu-id="f1527-748">Nivel de registro mínimo</span><span class="sxs-lookup"><span data-stu-id="f1527-748">Minimum log level</span></span> |
| :----: | ------------- | --------------------------------------- | ----------------- |
| <span data-ttu-id="f1527-749">1</span><span class="sxs-lookup"><span data-stu-id="f1527-749">1</span></span>      | <span data-ttu-id="f1527-750">Depuración</span><span class="sxs-lookup"><span data-stu-id="f1527-750">Debug</span></span>         | <span data-ttu-id="f1527-751">Todas las categorías</span><span class="sxs-lookup"><span data-stu-id="f1527-751">All categories</span></span>                          | <span data-ttu-id="f1527-752">Información</span><span class="sxs-lookup"><span data-stu-id="f1527-752">Information</span></span>       |
| <span data-ttu-id="f1527-753">2</span><span class="sxs-lookup"><span data-stu-id="f1527-753">2</span></span>      | <span data-ttu-id="f1527-754">Consola</span><span class="sxs-lookup"><span data-stu-id="f1527-754">Console</span></span>       | <span data-ttu-id="f1527-755">Microsoft.AspNetCore.Mvc.Razor.Internal</span><span class="sxs-lookup"><span data-stu-id="f1527-755">Microsoft.AspNetCore.Mvc.Razor.Internal</span></span> | <span data-ttu-id="f1527-756">Advertencia</span><span class="sxs-lookup"><span data-stu-id="f1527-756">Warning</span></span>           |
| <span data-ttu-id="f1527-757">3</span><span class="sxs-lookup"><span data-stu-id="f1527-757">3</span></span>      | <span data-ttu-id="f1527-758">Consola</span><span class="sxs-lookup"><span data-stu-id="f1527-758">Console</span></span>       | <span data-ttu-id="f1527-759">Microsoft.AspNetCore.Mvc.Razor.Razor</span><span class="sxs-lookup"><span data-stu-id="f1527-759">Microsoft.AspNetCore.Mvc.Razor.Razor</span></span>    | <span data-ttu-id="f1527-760">Depuración</span><span class="sxs-lookup"><span data-stu-id="f1527-760">Debug</span></span>             |
| <span data-ttu-id="f1527-761">4</span><span class="sxs-lookup"><span data-stu-id="f1527-761">4</span></span>      | <span data-ttu-id="f1527-762">Consola</span><span class="sxs-lookup"><span data-stu-id="f1527-762">Console</span></span>       | <span data-ttu-id="f1527-763">Microsoft.AspNetCore.Mvc.Razor</span><span class="sxs-lookup"><span data-stu-id="f1527-763">Microsoft.AspNetCore.Mvc.Razor</span></span>          | <span data-ttu-id="f1527-764">Error</span><span class="sxs-lookup"><span data-stu-id="f1527-764">Error</span></span>             |
| <span data-ttu-id="f1527-765">5</span><span class="sxs-lookup"><span data-stu-id="f1527-765">5</span></span>      | <span data-ttu-id="f1527-766">Consola</span><span class="sxs-lookup"><span data-stu-id="f1527-766">Console</span></span>       | <span data-ttu-id="f1527-767">Todas las categorías</span><span class="sxs-lookup"><span data-stu-id="f1527-767">All categories</span></span>                          | <span data-ttu-id="f1527-768">Información</span><span class="sxs-lookup"><span data-stu-id="f1527-768">Information</span></span>       |
| <span data-ttu-id="f1527-769">6</span><span class="sxs-lookup"><span data-stu-id="f1527-769">6</span></span>      | <span data-ttu-id="f1527-770">Todos los proveedores</span><span class="sxs-lookup"><span data-stu-id="f1527-770">All providers</span></span> | <span data-ttu-id="f1527-771">Todas las categorías</span><span class="sxs-lookup"><span data-stu-id="f1527-771">All categories</span></span>                          | <span data-ttu-id="f1527-772">Depuración</span><span class="sxs-lookup"><span data-stu-id="f1527-772">Debug</span></span>             |
| <span data-ttu-id="f1527-773">7</span><span class="sxs-lookup"><span data-stu-id="f1527-773">7</span></span>      | <span data-ttu-id="f1527-774">Todos los proveedores</span><span class="sxs-lookup"><span data-stu-id="f1527-774">All providers</span></span> | <span data-ttu-id="f1527-775">Sistema</span><span class="sxs-lookup"><span data-stu-id="f1527-775">System</span></span>                                  | <span data-ttu-id="f1527-776">Depuración</span><span class="sxs-lookup"><span data-stu-id="f1527-776">Debug</span></span>             |
| <span data-ttu-id="f1527-777">8</span><span class="sxs-lookup"><span data-stu-id="f1527-777">8</span></span>      | <span data-ttu-id="f1527-778">Depuración</span><span class="sxs-lookup"><span data-stu-id="f1527-778">Debug</span></span>         | <span data-ttu-id="f1527-779">Microsoft</span><span class="sxs-lookup"><span data-stu-id="f1527-779">Microsoft</span></span>                               | <span data-ttu-id="f1527-780">Seguimiento</span><span class="sxs-lookup"><span data-stu-id="f1527-780">Trace</span></span>             |

<span data-ttu-id="f1527-781">Cuando se crea un objeto `ILogger`, el objeto `ILoggerFactory` selecciona una sola regla por proveedor para aplicar a ese registrador.</span><span class="sxs-lookup"><span data-stu-id="f1527-781">When an `ILogger` object is created, the `ILoggerFactory` object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="f1527-782">Todos los mensajes escritos por una instancia `ILogger` se filtran según las reglas seleccionadas.</span><span class="sxs-lookup"><span data-stu-id="f1527-782">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="f1527-783">De las reglas disponibles se selecciona la más específica posible para cada par de categoría y proveedor.</span><span class="sxs-lookup"><span data-stu-id="f1527-783">The most specific rule possible for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="f1527-784">Cuando se crea un `ILogger` para una categoría determinada, se usa el algoritmo siguiente para cada proveedor:</span><span class="sxs-lookup"><span data-stu-id="f1527-784">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="f1527-785">Se seleccionan todas las reglas que coinciden con el proveedor o su alias.</span><span class="sxs-lookup"><span data-stu-id="f1527-785">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="f1527-786">Si no se encuentra ninguna coincidencia, se seleccionan todas las reglas con un proveedor vacío.</span><span class="sxs-lookup"><span data-stu-id="f1527-786">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="f1527-787">Del resultado del paso anterior, se seleccionan las reglas con el prefijo de categoría coincidente más largo.</span><span class="sxs-lookup"><span data-stu-id="f1527-787">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="f1527-788">Si no se encuentra ninguna coincidencia, se seleccionan todas las reglas que no especifican una categoría.</span><span class="sxs-lookup"><span data-stu-id="f1527-788">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="f1527-789">Si se seleccionan varias reglas, se toma la **última**.</span><span class="sxs-lookup"><span data-stu-id="f1527-789">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="f1527-790">Si no se selecciona ninguna regla, se usa `MinimumLevel`.</span><span class="sxs-lookup"><span data-stu-id="f1527-790">If no rules are selected, use `MinimumLevel`.</span></span>

<span data-ttu-id="f1527-791">Con la lista de reglas anterior, supongamos que crea un objeto `ILogger` para la categoría "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span><span class="sxs-lookup"><span data-stu-id="f1527-791">With the preceding list of rules, suppose you create an `ILogger` object for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span></span>

* <span data-ttu-id="f1527-792">Para el proveedor de depuración, se aplican las reglas 1, 6 y 8.</span><span class="sxs-lookup"><span data-stu-id="f1527-792">For the Debug provider, rules 1, 6, and 8 apply.</span></span> <span data-ttu-id="f1527-793">La regla 8 es la más específica, por lo que se selecciona.</span><span class="sxs-lookup"><span data-stu-id="f1527-793">Rule 8 is most specific, so that's the one selected.</span></span>
* <span data-ttu-id="f1527-794">Para el proveedor de la consola, se aplican las reglas 3, 4, 5 y 6.</span><span class="sxs-lookup"><span data-stu-id="f1527-794">For the Console provider, rules 3, 4, 5, and 6 apply.</span></span> <span data-ttu-id="f1527-795">La regla 3 es la más específica.</span><span class="sxs-lookup"><span data-stu-id="f1527-795">Rule 3 is most specific.</span></span>

<span data-ttu-id="f1527-796">La instancia `ILogger` resultante envía los registros de nivel `Trace` y superiores al proveedor de depuración.</span><span class="sxs-lookup"><span data-stu-id="f1527-796">The resulting `ILogger` instance sends logs of `Trace` level and above to the Debug provider.</span></span> <span data-ttu-id="f1527-797">Los registros de nivel `Debug` y superiores se envían al proveedor de consola.</span><span class="sxs-lookup"><span data-stu-id="f1527-797">Logs of `Debug` level and above are sent to the Console provider.</span></span>

### <a name="provider-aliases"></a><span data-ttu-id="f1527-798">Alias de proveedor</span><span class="sxs-lookup"><span data-stu-id="f1527-798">Provider aliases</span></span>

<span data-ttu-id="f1527-799">Cada proveedor define un *alias* que se puede utilizar en la configuración en lugar del nombre de tipo completo.</span><span class="sxs-lookup"><span data-stu-id="f1527-799">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span>  <span data-ttu-id="f1527-800">Para los proveedores integrados, use los alias siguientes:</span><span class="sxs-lookup"><span data-stu-id="f1527-800">For the built-in providers, use the following aliases:</span></span>

* <span data-ttu-id="f1527-801">Consola</span><span class="sxs-lookup"><span data-stu-id="f1527-801">Console</span></span>
* <span data-ttu-id="f1527-802">Depuración</span><span class="sxs-lookup"><span data-stu-id="f1527-802">Debug</span></span>
* <span data-ttu-id="f1527-803">EventSource</span><span class="sxs-lookup"><span data-stu-id="f1527-803">EventSource</span></span>
* <span data-ttu-id="f1527-804">EventLog</span><span class="sxs-lookup"><span data-stu-id="f1527-804">EventLog</span></span>
* <span data-ttu-id="f1527-805">TraceSource</span><span class="sxs-lookup"><span data-stu-id="f1527-805">TraceSource</span></span>
* <span data-ttu-id="f1527-806">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="f1527-806">AzureAppServicesFile</span></span>
* <span data-ttu-id="f1527-807">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="f1527-807">AzureAppServicesBlob</span></span>
* <span data-ttu-id="f1527-808">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="f1527-808">ApplicationInsights</span></span>

### <a name="default-minimum-level"></a><span data-ttu-id="f1527-809">Nivel mínimo predeterminado</span><span class="sxs-lookup"><span data-stu-id="f1527-809">Default minimum level</span></span>

<span data-ttu-id="f1527-810">Hay una configuración de nivel mínimo que solo tiene efecto si no se aplica ninguna regla de configuración o código para un proveedor y una categoría determinados.</span><span class="sxs-lookup"><span data-stu-id="f1527-810">There's a minimum level setting that takes effect only if no rules from configuration or code apply for a given provider and category.</span></span> <span data-ttu-id="f1527-811">En el ejemplo siguiente se muestra cómo establecer el nivel mínimo:</span><span class="sxs-lookup"><span data-stu-id="f1527-811">The following example shows how to set the minimum level:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

<span data-ttu-id="f1527-812">Si no establece explícitamente el nivel mínimo, el valor predeterminado es `Information`, lo que significa que los registros `Trace` y `Debug` se omiten.</span><span class="sxs-lookup"><span data-stu-id="f1527-812">If you don't explicitly set the minimum level, the default value is `Information`, which means that `Trace` and `Debug` logs are ignored.</span></span>

### <a name="filter-functions"></a><span data-ttu-id="f1527-813">Funciones de filtro</span><span class="sxs-lookup"><span data-stu-id="f1527-813">Filter functions</span></span>

<span data-ttu-id="f1527-814">Se invoca una función de filtro para todos los proveedores y categorías que no tienen reglas asignadas mediante configuración o código.</span><span class="sxs-lookup"><span data-stu-id="f1527-814">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code.</span></span> <span data-ttu-id="f1527-815">El código de la función tiene acceso al tipo de proveedor, la categoría y el nivel de registro.</span><span class="sxs-lookup"><span data-stu-id="f1527-815">Code in the function has access to the provider type, category, and log level.</span></span> <span data-ttu-id="f1527-816">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="f1527-816">For example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

## <a name="system-categories-and-levels"></a><span data-ttu-id="f1527-817">Niveles y categorías del sistema</span><span class="sxs-lookup"><span data-stu-id="f1527-817">System categories and levels</span></span>

<span data-ttu-id="f1527-818">Estas son algunas categorías que ASP.NET Core y Entity Framework Core usan, con notas sobre lo que los registros de espera de ellas:</span><span class="sxs-lookup"><span data-stu-id="f1527-818">Here are some categories used by ASP.NET Core and Entity Framework Core, with notes about what logs to expect from them:</span></span>

| <span data-ttu-id="f1527-819">Categoría</span><span class="sxs-lookup"><span data-stu-id="f1527-819">Category</span></span>                            | <span data-ttu-id="f1527-820">Notas</span><span class="sxs-lookup"><span data-stu-id="f1527-820">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="f1527-821">Microsoft.AspNetCore</span><span class="sxs-lookup"><span data-stu-id="f1527-821">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="f1527-822">Diagnósticos generales de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f1527-822">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="f1527-823">Microsoft.AspNetCore.DataProtection</span><span class="sxs-lookup"><span data-stu-id="f1527-823">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="f1527-824">Qué claves se tuvieron en cuenta, encontraron y usaron.</span><span class="sxs-lookup"><span data-stu-id="f1527-824">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="f1527-825">Microsoft.AspNetCore.HostFiltering</span><span class="sxs-lookup"><span data-stu-id="f1527-825">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="f1527-826">Hosts permitidos.</span><span class="sxs-lookup"><span data-stu-id="f1527-826">Hosts allowed.</span></span> |
| <span data-ttu-id="f1527-827">Microsoft.AspNetCore.Hosting</span><span class="sxs-lookup"><span data-stu-id="f1527-827">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="f1527-828">Cuánto tiempo tardaron en completarse las solicitudes HTTP y a qué hora comenzaron.</span><span class="sxs-lookup"><span data-stu-id="f1527-828">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="f1527-829">Qué ensamblados de inicio de hospedaje se cargaron.</span><span class="sxs-lookup"><span data-stu-id="f1527-829">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="f1527-830">Microsoft.AspNetCore.Mvc</span><span class="sxs-lookup"><span data-stu-id="f1527-830">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="f1527-831">Diagnósticos de MVC y Razor.</span><span class="sxs-lookup"><span data-stu-id="f1527-831">MVC and Razor diagnostics.</span></span> <span data-ttu-id="f1527-832">Enlace de modelos, ejecución de filtros, compilación de vistas y selección de acciones.</span><span class="sxs-lookup"><span data-stu-id="f1527-832">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="f1527-833">Microsoft.AspNetCore.Routing</span><span class="sxs-lookup"><span data-stu-id="f1527-833">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="f1527-834">Información de coincidencia de ruta.</span><span class="sxs-lookup"><span data-stu-id="f1527-834">Route matching information.</span></span> |
| <span data-ttu-id="f1527-835">Microsoft.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="f1527-835">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="f1527-836">Inicio y detención de conexión y mantener las respuestas activas.</span><span class="sxs-lookup"><span data-stu-id="f1527-836">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="f1527-837">Información de certificado HTTPS.</span><span class="sxs-lookup"><span data-stu-id="f1527-837">HTTPS certificate information.</span></span> |
| <span data-ttu-id="f1527-838">Microsoft.AspNetCore.StaticFiles</span><span class="sxs-lookup"><span data-stu-id="f1527-838">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="f1527-839">Archivos servidos.</span><span class="sxs-lookup"><span data-stu-id="f1527-839">Files served.</span></span> |
| <span data-ttu-id="f1527-840">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="f1527-840">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="f1527-841">Diagnósticos generales de Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="f1527-841">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="f1527-842">Actividad y la configuración de bases de datos, detección de cambios y migraciones.</span><span class="sxs-lookup"><span data-stu-id="f1527-842">Database activity and configuration, change detection, migrations.</span></span> |

## <a name="log-scopes"></a><span data-ttu-id="f1527-843">Ámbitos de registro</span><span class="sxs-lookup"><span data-stu-id="f1527-843">Log scopes</span></span>

 <span data-ttu-id="f1527-844">Un *ámbito* puede agrupar un conjunto de operaciones lógicas.</span><span class="sxs-lookup"><span data-stu-id="f1527-844">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="f1527-845">Esta agrupación se puede utilizar para adjuntar los mismos datos para cada registro que se crea como parte de un conjunto.</span><span class="sxs-lookup"><span data-stu-id="f1527-845">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="f1527-846">Por ejemplo, cada registro creado como parte del procesamiento de una transacción puede incluir el identificador de dicha transacción.</span><span class="sxs-lookup"><span data-stu-id="f1527-846">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="f1527-847">Un ámbito es un tipo `IDisposable` devuelto por el método <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> y se conserva hasta que se elimina.</span><span class="sxs-lookup"><span data-stu-id="f1527-847">A scope is an `IDisposable` type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method and lasts until it's disposed.</span></span> <span data-ttu-id="f1527-848">Use un ámbito encapsulando las llamadas de registrador en un bloque `using`:</span><span class="sxs-lookup"><span data-stu-id="f1527-848">Use a scope by wrapping logger calls in a `using` block:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

<span data-ttu-id="f1527-849">El código siguiente permite ámbitos para el proveedor de la consola:</span><span class="sxs-lookup"><span data-stu-id="f1527-849">The following code enables scopes for the console provider:</span></span>

<span data-ttu-id="f1527-850">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="f1527-850">*Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

> [!NOTE]
> <span data-ttu-id="f1527-851">Es necesario configurar la opción del registrador de consola `IncludeScopes` para habilitar el registro basado en el ámbito.</span><span class="sxs-lookup"><span data-stu-id="f1527-851">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>
>
> <span data-ttu-id="f1527-852">Para obtener información sobre la configuración, consulte la sección [Configuración](#configuration).</span><span class="sxs-lookup"><span data-stu-id="f1527-852">For information on configuration, see the [Configuration](#configuration) section.</span></span>

<span data-ttu-id="f1527-853">Cada mensaje de registro incluye la información de ámbito:</span><span class="sxs-lookup"><span data-stu-id="f1527-853">Each log message includes the scoped information:</span></span>

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a><span data-ttu-id="f1527-854">Proveedores de registro integrados</span><span class="sxs-lookup"><span data-stu-id="f1527-854">Built-in logging providers</span></span>

<span data-ttu-id="f1527-855">ASP.NET Core incluye los proveedores siguientes:</span><span class="sxs-lookup"><span data-stu-id="f1527-855">ASP.NET Core ships the following providers:</span></span>

* [<span data-ttu-id="f1527-856">Consola</span><span class="sxs-lookup"><span data-stu-id="f1527-856">Console</span></span>](#console-provider)
* [<span data-ttu-id="f1527-857">Depurar</span><span class="sxs-lookup"><span data-stu-id="f1527-857">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="f1527-858">EventSource</span><span class="sxs-lookup"><span data-stu-id="f1527-858">EventSource</span></span>](#event-source-provider)
* [<span data-ttu-id="f1527-859">EventLog</span><span class="sxs-lookup"><span data-stu-id="f1527-859">EventLog</span></span>](#windows-eventlog-provider)
* [<span data-ttu-id="f1527-860">TraceSource</span><span class="sxs-lookup"><span data-stu-id="f1527-860">TraceSource</span></span>](#tracesource-provider)
* [<span data-ttu-id="f1527-861">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="f1527-861">AzureAppServicesFile</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="f1527-862">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="f1527-862">AzureAppServicesBlob</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="f1527-863">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="f1527-863">ApplicationInsights</span></span>](#azure-application-insights-trace-logging)

<span data-ttu-id="f1527-864">Para obtener información sobre stdout y el registro de depuración con el módulo ASP.NET Core, consulte <xref:test/troubleshoot-azure-iis> y <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="f1527-864">For information on stdout and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console-provider"></a><span data-ttu-id="f1527-865">Proveedor de la consola</span><span class="sxs-lookup"><span data-stu-id="f1527-865">Console provider</span></span>

<span data-ttu-id="f1527-866">El paquete de proveedor [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) envía la salida del registro a la consola.</span><span class="sxs-lookup"><span data-stu-id="f1527-866">The [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) provider package sends log output to the console.</span></span> 

```csharp
logging.AddConsole();
```

<span data-ttu-id="f1527-867">Para ver una salida de registro de la consola, abra un símbolo del sistema en la carpeta del proyecto y ejecute este comando:</span><span class="sxs-lookup"><span data-stu-id="f1527-867">To see console logging output, open a command prompt in the project folder and run the following command:</span></span>

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a><span data-ttu-id="f1527-868">Proveedor de depuración</span><span class="sxs-lookup"><span data-stu-id="f1527-868">Debug provider</span></span>

<span data-ttu-id="f1527-869">El paquete de proveedor [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) escribe la salida del registro mediante la clase [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) (llamadas a métodos `Debug.WriteLine`).</span><span class="sxs-lookup"><span data-stu-id="f1527-869">The [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) provider package writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class (`Debug.WriteLine` method calls).</span></span>

<span data-ttu-id="f1527-870">En Linux, este proveedor escribe registros en */var/log/message*.</span><span class="sxs-lookup"><span data-stu-id="f1527-870">On Linux, this provider writes logs to */var/log/message*.</span></span>

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a><span data-ttu-id="f1527-871">Proveedor de origen del evento</span><span class="sxs-lookup"><span data-stu-id="f1527-871">Event Source provider</span></span>

<span data-ttu-id="f1527-872">El paquete del proveedor [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) escribe en una multiplataforma de origen del evento con el nombre `Microsoft-Extensions-Logging`.</span><span class="sxs-lookup"><span data-stu-id="f1527-872">The [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) provider package writes to an Event Source cross-platform with the name `Microsoft-Extensions-Logging`.</span></span> <span data-ttu-id="f1527-873">En Windows, el proveedor utiliza [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span><span class="sxs-lookup"><span data-stu-id="f1527-873">On Windows, the provider uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span>

```csharp
logging.AddEventSourceLogger();
```

<span data-ttu-id="f1527-874">El proveedor de origen del evento se agrega automáticamente cuando se llama a `CreateDefaultBuilder` para compilar el host.</span><span class="sxs-lookup"><span data-stu-id="f1527-874">The Event Source provider is added automatically when `CreateDefaultBuilder` is called to build the host.</span></span>

<span data-ttu-id="f1527-875">Use la [utilidad PerfView](https://github.com/Microsoft/perfview) para recopilar y ver los registros.</span><span class="sxs-lookup"><span data-stu-id="f1527-875">Use the [PerfView utility](https://github.com/Microsoft/perfview) to collect and view logs.</span></span> <span data-ttu-id="f1527-876">Hay otras herramientas para ver los registros ETW, pero PerfView proporciona la mejor experiencia para trabajar con los eventos ETW emitidos por ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f1527-876">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET Core.</span></span>

<span data-ttu-id="f1527-877">Para configurar PerfView para la recopilación de eventos registrados por este proveedor, agregue la cadena `*Microsoft-Extensions-Logging` a la lista **Proveedores adicionales**.</span><span class="sxs-lookup"><span data-stu-id="f1527-877">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="f1527-878">(No olvide el asterisco al principio de la cadena).</span><span class="sxs-lookup"><span data-stu-id="f1527-878">(Don't miss the asterisk at the start of the string.)</span></span>

![Proveedores adicionales de Perfview](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a><span data-ttu-id="f1527-880">Proveedor EventLog de Windows</span><span class="sxs-lookup"><span data-stu-id="f1527-880">Windows EventLog provider</span></span>

<span data-ttu-id="f1527-881">El paquete de proveedor [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) envía la salida del registro al Registro de eventos de Windows.</span><span class="sxs-lookup"><span data-stu-id="f1527-881">The [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) provider package sends log output to the Windows Event Log.</span></span>

```csharp
logging.AddEventLog();
```

<span data-ttu-id="f1527-882">Las [sobrecargas de AddEventLog](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) permiten pasar <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span><span class="sxs-lookup"><span data-stu-id="f1527-882">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) let you pass in <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span></span> <span data-ttu-id="f1527-883">Si es `null` o no se especifica, se usa la siguiente configuración predeterminada:</span><span class="sxs-lookup"><span data-stu-id="f1527-883">If `null` or not specified, the following default settings are used:</span></span>

* <span data-ttu-id="f1527-884">`LogName`: "Aplicación"</span><span class="sxs-lookup"><span data-stu-id="f1527-884">`LogName` &ndash; "Application"</span></span>
* <span data-ttu-id="f1527-885">`SourceName`: "Entorno de ejecución .NET"</span><span class="sxs-lookup"><span data-stu-id="f1527-885">`SourceName` &ndash; ".NET Runtime"</span></span>
* <span data-ttu-id="f1527-886">`MachineName`: equipo local</span><span class="sxs-lookup"><span data-stu-id="f1527-886">`MachineName` &ndash; local machine</span></span>

<span data-ttu-id="f1527-887">Los eventos se registran para el [Nivel de advertencia y superior](#log-level).</span><span class="sxs-lookup"><span data-stu-id="f1527-887">Events are logged for [Warning level and higher](#log-level).</span></span> <span data-ttu-id="f1527-888">Para registrar eventos inferiores a `Warning`, establezca el nivel de registro de forma explícita.</span><span class="sxs-lookup"><span data-stu-id="f1527-888">To log events lower than `Warning`, explicitly set the log level.</span></span> <span data-ttu-id="f1527-889">Por ejemplo, agregue lo siguiente al archivo *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="f1527-889">For example, add the following to the *appsettings.json* file:</span></span>

```json
"EventLog": {
  "LogLevel": {
    "Default": "Information"
  }
}
```

### <a name="tracesource-provider"></a><span data-ttu-id="f1527-890">Proveedor TraceSource</span><span class="sxs-lookup"><span data-stu-id="f1527-890">TraceSource provider</span></span>

<span data-ttu-id="f1527-891">El paquete de proveedor [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) usa las bibliotecas y proveedores de <xref:System.Diagnostics.TraceSource>.</span><span class="sxs-lookup"><span data-stu-id="f1527-891">The [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) provider package uses the <xref:System.Diagnostics.TraceSource> libraries and providers.</span></span>

```csharp
logging.AddTraceSource(sourceSwitchName);
```

<span data-ttu-id="f1527-892">[Las sobrecargas de AddTraceSource](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) permiten pasar un modificador de origen y un agente de escucha de seguimiento.</span><span class="sxs-lookup"><span data-stu-id="f1527-892">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) let you pass in a source switch and a trace listener.</span></span>

<span data-ttu-id="f1527-893">Para usar este proveedor, una aplicación debe ejecutarse en .NET Framework (en lugar de .NET Core).</span><span class="sxs-lookup"><span data-stu-id="f1527-893">To use this provider, an app has to run on the .NET Framework (rather than .NET Core).</span></span> <span data-ttu-id="f1527-894">El proveedor puede enrutar mensajes a una variedad de [agentes de escucha](/dotnet/framework/debug-trace-profile/trace-listeners), como <xref:System.Diagnostics.TextWriterTraceListener> que se usa en la aplicación de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="f1527-894">The provider can route messages to a variety of [listeners](/dotnet/framework/debug-trace-profile/trace-listeners), such as the <xref:System.Diagnostics.TextWriterTraceListener> used in the sample app.</span></span>

### <a name="azure-app-service-provider"></a><span data-ttu-id="f1527-895">Proveedor Azure App Service</span><span class="sxs-lookup"><span data-stu-id="f1527-895">Azure App Service provider</span></span>

<span data-ttu-id="f1527-896">El paquete de proveedor [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) escribe los registros en archivos de texto en el sistema de archivos de una aplicación de Azure App Service y en [Blob Storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) en una cuenta de Azure Storage.</span><span class="sxs-lookup"><span data-stu-id="f1527-896">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span>

```csharp
logging.AddAzureWebAppDiagnostics();
```

<span data-ttu-id="f1527-897">El paquete de proveedor no está incluido en el [metapaquete Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="f1527-897">The provider package isn't included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="f1527-898">Si el destino es .NET Framework o hace referencia al metapaquete `Microsoft.AspNetCore.App`, agregue el paquete del proveedor al proyecto.</span><span class="sxs-lookup"><span data-stu-id="f1527-898">When targeting .NET Framework or referencing the `Microsoft.AspNetCore.App` metapackage, add the provider package to the project.</span></span> 

<span data-ttu-id="f1527-899">Una sobrecarga <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> permite pasar <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span><span class="sxs-lookup"><span data-stu-id="f1527-899">An <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> overload lets you pass in <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span></span> <span data-ttu-id="f1527-900">El objeto de configuración puede invalidar la configuración predeterminada, como la plantilla de salida de registro, el nombre de blob y el límite de tamaño de archivo.</span><span class="sxs-lookup"><span data-stu-id="f1527-900">The settings object can override default settings, such as the logging output template, blob name, and file size limit.</span></span> <span data-ttu-id="f1527-901">(La *plantilla salida* es una plantilla de mensaje que se aplica a todos los registros además de que se proporciona con una llamada de método `ILogger`).</span><span class="sxs-lookup"><span data-stu-id="f1527-901">(*Output template* is a message template that's applied to all logs in addition to what's provided with an `ILogger` method call.)</span></span>

<span data-ttu-id="f1527-902">Al realizar una implementación en una aplicación de App Service, esta respeta la configuración de la sección [Registros de App Service](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) de la página **App Service** de Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="f1527-902">When you deploy to an App Service app, the application honors the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="f1527-903">Cuando se actualiza la configuración siguiente, los cambios se aplican de inmediato sin necesidad de reiniciar ni de volver a implementar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f1527-903">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="f1527-904">**Registro de la aplicación (sistema de archivos)**</span><span class="sxs-lookup"><span data-stu-id="f1527-904">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="f1527-905">**Registro de la aplicación (blob)**</span><span class="sxs-lookup"><span data-stu-id="f1527-905">**Application Logging (Blob)**</span></span>

<span data-ttu-id="f1527-906">La ubicación predeterminada de los archivos de registro es la carpeta *D:\\home\\LogFiles\\Application* y el nombre de archivo predeterminado es *diagnostics-aaaammdd.txt*.</span><span class="sxs-lookup"><span data-stu-id="f1527-906">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="f1527-907">El límite de tamaño de archivo predeterminado es 10 MB, y el número máximo predeterminado de archivos que se conservan es 2.</span><span class="sxs-lookup"><span data-stu-id="f1527-907">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="f1527-908">El nombre de blob predeterminado es *{nombre-de-la-aplicación}{marca de tiempo}/aaaa/mm/dd/hh/{guid}-applicationLog.txt*.</span><span class="sxs-lookup"><span data-stu-id="f1527-908">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="f1527-909">El proveedor solo funciona cuando el proyecto se ejecuta en el entorno de Azure.</span><span class="sxs-lookup"><span data-stu-id="f1527-909">The provider only works when the project runs in the Azure environment.</span></span> <span data-ttu-id="f1527-910">No tiene ningún efecto cuando el proyecto se ejecuta de manera local (no escribe en los archivos locales ni en el almacenamiento de desarrollo local de blobs).</span><span class="sxs-lookup"><span data-stu-id="f1527-910">It has no effect when the project is run locally&mdash;it doesn't write to local files or local development storage for blobs.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="f1527-911">Secuencias de registro de Azure</span><span class="sxs-lookup"><span data-stu-id="f1527-911">Azure log streaming</span></span>

<span data-ttu-id="f1527-912">Las secuencias de registro de Azure permiten ver la actividad de registro en tiempo real desde:</span><span class="sxs-lookup"><span data-stu-id="f1527-912">Azure log streaming lets you view log activity in real time from:</span></span>

* <span data-ttu-id="f1527-913">El servidor de aplicaciones</span><span class="sxs-lookup"><span data-stu-id="f1527-913">The app server</span></span>
* <span data-ttu-id="f1527-914">El servidor web</span><span class="sxs-lookup"><span data-stu-id="f1527-914">The web server</span></span>
* <span data-ttu-id="f1527-915">Error del seguimiento de solicitudes</span><span class="sxs-lookup"><span data-stu-id="f1527-915">Failed request tracing</span></span>

<span data-ttu-id="f1527-916">Para configurar las secuencias de registro de Azure:</span><span class="sxs-lookup"><span data-stu-id="f1527-916">To configure Azure log streaming:</span></span>

* <span data-ttu-id="f1527-917">Navegue hasta la página **Registros de App Service** desde la página de portal de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f1527-917">Navigate to the **App Service logs** page from your app's portal page.</span></span>
* <span data-ttu-id="f1527-918">Establezca **Registro de la aplicación (sistema de archivos)** en **Activado**.</span><span class="sxs-lookup"><span data-stu-id="f1527-918">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="f1527-919">Elija el **Nivel** de registro.</span><span class="sxs-lookup"><span data-stu-id="f1527-919">Choose the log **Level**.</span></span> <span data-ttu-id="f1527-920">Este valor solo se aplica a las secuencias de registro de Azure, no a otros proveedores de registro de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f1527-920">This setting only applies to Azure log streaming, not other logging providers in the app.</span></span>

<span data-ttu-id="f1527-921">Navegue hasta la página **Secuencia de registro** para consultar los mensajes de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f1527-921">Navigate to the **Log Stream** page to view app messages.</span></span> <span data-ttu-id="f1527-922">La aplicación los registra a través de la interfaz `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="f1527-922">They're logged by the app through the `ILogger` interface.</span></span>

### <a name="azure-application-insights-trace-logging"></a><span data-ttu-id="f1527-923">Registro de seguimiento de Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="f1527-923">Azure Application Insights trace logging</span></span>

<span data-ttu-id="f1527-924">El proveedor de paquete [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) escribe los registros en Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="f1527-924">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to Azure Application Insights.</span></span> <span data-ttu-id="f1527-925">Application Insights es un servicio que supervisa una aplicación web y proporciona herramientas para consultar y analizar los datos de telemetría.</span><span class="sxs-lookup"><span data-stu-id="f1527-925">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="f1527-926">Si usa este proveedor, puede consultar y analizar los registros mediante las herramientas de Application Insights.</span><span class="sxs-lookup"><span data-stu-id="f1527-926">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="f1527-927">El proveedor de registro se incluye como dependencia de [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), que es el paquete que proporciona toda la telemetría disponible para ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f1527-927">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="f1527-928">Si usa este paquete, no tiene que instalar el proveedor de paquete.</span><span class="sxs-lookup"><span data-stu-id="f1527-928">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="f1527-929">No use el paquete [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web)&mdash;que es para ASP.NET 4.x.</span><span class="sxs-lookup"><span data-stu-id="f1527-929">Don't use the [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;that's for ASP.NET 4.x.</span></span>

<span data-ttu-id="f1527-930">Para obtener más información, vea los siguientes recursos:</span><span class="sxs-lookup"><span data-stu-id="f1527-930">For more information, see the following resources:</span></span>

* [<span data-ttu-id="f1527-931">Información general de Application Insights</span><span class="sxs-lookup"><span data-stu-id="f1527-931">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="f1527-932">[Application Insights para aplicaciones de ASP.NET Core](/azure/azure-monitor/app/asp-net-core): comience aquí si quiere implementar la variedad completa de telemetría de Application Insights junto con el registro.</span><span class="sxs-lookup"><span data-stu-id="f1527-932">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="f1527-933">[ApplicationInsightsLoggerProvider para los registros de .NET Core ILogger](/azure/azure-monitor/app/ilogger): comience aquí si quiere implementar el proveedor de registro sin el resto de la telemetría de Application Insights.</span><span class="sxs-lookup"><span data-stu-id="f1527-933">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="f1527-934">[Adaptadores de registro de Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs)</span><span class="sxs-lookup"><span data-stu-id="f1527-934">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="f1527-935">[Instalación, configuración e inicialización del SDK de Application Insights](/learn/modules/instrument-web-app-code-with-application-insights): tutorial interactivo en el sitio de Microsoft Learn.</span><span class="sxs-lookup"><span data-stu-id="f1527-935">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="f1527-936">Proveedores de registro de terceros</span><span class="sxs-lookup"><span data-stu-id="f1527-936">Third-party logging providers</span></span>

<span data-ttu-id="f1527-937">Plataformas de registro de terceros que funcionan con ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="f1527-937">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="f1527-938">[elmah.io](https://elmah.io/) ([repositorio de GitHub](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="f1527-938">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="f1527-939">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([repositorio de GitHub](https://github.com/mattwcole/gelf-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="f1527-939">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="f1527-940">[JSNLog](https://jsnlog.com/) ([repositorio de GitHub](https://github.com/mperdeck/jsnlog))</span><span class="sxs-lookup"><span data-stu-id="f1527-940">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="f1527-941">[KissLog.net](https://kisslog.net/) ([repositorio de GitHub](https://github.com/catalingavan/KissLog-net))</span><span class="sxs-lookup"><span data-stu-id="f1527-941">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="f1527-942">[Log4Net](https://logging.apache.org/log4net/) ([repositorio de GitHub](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span><span class="sxs-lookup"><span data-stu-id="f1527-942">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span></span>
* <span data-ttu-id="f1527-943">[Loggr](https://loggr.net/) ([repositorio de GitHub](https://github.com/imobile3/Loggr.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="f1527-943">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="f1527-944">[NLog](https://nlog-project.org/) ([repositorio de GitHub](https://github.com/NLog/NLog.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="f1527-944">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="f1527-945">[Sentry](https://sentry.io/welcome/) ([repositorio de GitHub](https://github.com/getsentry/sentry-dotnet))</span><span class="sxs-lookup"><span data-stu-id="f1527-945">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="f1527-946">[Serilog](https://serilog.net/) ([repositorio de GitHub](https://github.com/serilog/serilog-aspnetcore))</span><span class="sxs-lookup"><span data-stu-id="f1527-946">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="f1527-947">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([repositorio de GitHub](https://github.com/googleapis/google-cloud-dotnet))</span><span class="sxs-lookup"><span data-stu-id="f1527-947">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="f1527-948">Algunas plataformas de terceros pueden realizar [registro semántico, también conocido como registro estructurado](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="f1527-948">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="f1527-949">El uso de una plataforma de terceros es similar al uso de uno de los proveedores integrados:</span><span class="sxs-lookup"><span data-stu-id="f1527-949">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="f1527-950">Agregue un paquete NuGet al proyecto.</span><span class="sxs-lookup"><span data-stu-id="f1527-950">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="f1527-951">Llame a un método de extensión `ILoggerFactory` proporcionado por el marco de registro.</span><span class="sxs-lookup"><span data-stu-id="f1527-951">Call an `ILoggerFactory` extension method provided by the logging framework.</span></span>

<span data-ttu-id="f1527-952">Para más información, vea la documentación de cada proveedor.</span><span class="sxs-lookup"><span data-stu-id="f1527-952">For more information, see each provider's documentation.</span></span> <span data-ttu-id="f1527-953">Microsoft no admite los proveedores de registro de terceros.</span><span class="sxs-lookup"><span data-stu-id="f1527-953">Third-party logging providers aren't supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f1527-954">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="f1527-954">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>

::: moniker-end
