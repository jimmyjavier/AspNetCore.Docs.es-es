---
title: Registros en .NET Core y ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo usar la plataforma de registro proporcionada por el paquete NuGet Microsoft.Extensions.Logging.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/17/2020
uid: fundamentals/logging/index
ms.openlocfilehash: b897d0d775da62a11f01a64f39b47b6c5abebc8b
ms.sourcegitcommit: c9d1208e86160615b2d914cce74a839ae41297a8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/22/2020
ms.locfileid: "81791563"
---
# <a name="logging-in-net-core-and-aspnet-core"></a>Registros en .NET Core y ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Por [Tom Dykstra](https://github.com/tdykstra) y [Steve Smith](https://ardalis.com/)

.NET Core es compatible con una API de registro que funciona con una gran variedad de proveedores de registro integrados y de terceros. En este artículo se muestra cómo usar las API de registro con proveedores integrados.

La mayoría de los ejemplos de código que se muestran en este artículo son de aplicaciones de ASP.NET Core. Las partes específicas de registro de estos fragmentos de código son válidas para cualquier aplicación de .NET Core que use el [host genérico](xref:fundamentals/host/generic-host). Para ver un ejemplo de cómo usar el host genérico en una aplicación de consola que no sea web, vea el archivo *Program.cs* de la [aplicación de ejemplo de tareas en segundo plano](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) (<xref:fundamentals/host/hosted-services>).

El código de registro para las aplicaciones sin un host genérico es distinto en la forma en que se [agregan los proveedores](#add-providers) y [se crean los registradores](#create-logs). Los ejemplos de código que no sean de host se muestran en esas secciones del artículo.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))

## <a name="add-providers"></a>Incorporación de proveedores

Un proveedor de registro muestra o almacena registros. Por ejemplo, el proveedor de consola muestra los registros en la consola y el proveedor de Azure Application Insights los almacena en Azure Application Insights. Los registros se pueden enviar a varios destinos mediante la incorporación de varios proveedores.

Para agregar un proveedor en una aplicación que use un host genérico, llame al método de extensión `Add{provider name}` del proveedor en *Program.cs*:

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=6)]

En una aplicación de consola que no sea de host, llame al método de extensión `Add{provider name}` del proveedor al crear un elemento `LoggerFactory`:

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=1,7)]

`LoggerFactory` y `AddConsole` necesitan una instrucción `using` para `Microsoft.Extensions.Logging`.

Las plantillas de proyecto predeterminadas de ASP.NET Core llaman a <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>, que agrega los siguientes proveedores de registro:

* [Consola](#console-provider)
* [Depurar](#debug-provider)
* [EventSource](#event-source-provider)
* [EventLog](#windows-eventlog-provider) (solo si se ejecuta en Windows)

Puede reemplazar los proveedores predeterminados por sus propios valores. Llame a <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> y agregue los proveedores que desee.

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=5)]

Obtenga más información sobre los [proveedores de registro integrados](#built-in-logging-providers) y los [proveedores de registro de terceros](#third-party-logging-providers) más adelante en el artículo.

## <a name="create-logs"></a>Creación de registros

Para crear registros, use un objeto de <xref:Microsoft.Extensions.Logging.ILogger%601>. En una aplicación web o servicio hospedado, obtenga un elemento `ILogger` de la inserción de dependencias. En aplicaciones de consola que no sean de host, use `LoggerFactory` para crear un elemento `ILogger`.

En el ejemplo siguiente de ASP.NET Core, se crea un registrador con `TodoApiSample.Pages.AboutModel` como la categoría. La *categoría* de registro es una cadena que está asociada con cada registro. La instancia de `ILogger<T>` proporcionada por la inserción de dependencias genera registros que tienen el nombre completo del tipo `T` como la categoría. 

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

En el siguiente ejemplo de aplicación de consola que no es de host, se crea un registrador con `LoggingConsoleApp.Program` como la categoría.

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=10)]

En los siguientes ejemplos de aplicación de consola y ASP.NET Core, el registrador se usa para crear registros con `Information` como el nivel. El *nivel* de registro indica la gravedad del evento registrado.

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=11)]

Los [niveles](#log-level) y las [categorías](#log-category) se explican detalladamente más adelante en este artículo.

### <a name="create-logs-in-the-program-class"></a>Crear registros en la clase del programa

Para escribir registros en la clase `Program` de una aplicación de ASP.NET Core, obtenga una instancia de `ILogger` desde la inserción de dependencias después de generar el host:

[!code-csharp[](index/samples_snapshot/3.x/TodoApiSample/Program.cs?highlight=9,10)]

No se admite directamente el registro durante la construcción del host. Sin embargo, se puede usar un registrador independiente. En el ejemplo siguiente, se usa un registrador [Serilog](https://serilog.net/) para registrarse en `CreateHostBuilder`. `AddSerilog` usa la configuración estática especificada en `Log.Logger`:

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

### <a name="create-logs-in-the-startup-class"></a>Crea registros en la clase de inicio

Para escribir registros en el método `Startup.Configure` de una aplicación de ASP.NET Core, incluya un parámetro `ILogger` en la signatura del método:

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_Configure&highlight=1,5)]

No se admite la escritura de registros antes de la finalización del contenedor de inserción de dependencias configurado en el método `Startup.ConfigureServices`:

* No se admite la inyección del registrador en el constructor `Startup`.
* No se admite la inyección del registrador en la signatura del método `Startup.ConfigureServices`.

El motivo de esta restricción es que los registros dependen de la inserción de dependencias y de la configuración, que a su vez depende de la inserción de dependencias. El contenedor de inserción de dependencias no se configura hasta que finaliza `ConfigureServices`.

La inserción del constructor de un registrador en `Startup` funciona en versiones anteriores de ASP.NET Core, ya que se crea un contenedor de inserción de dependencias independiente para el host de web. Para conocer por qué solo se crea un contenedor para el host genérico, vea el [anuncio de cambios importantes](https://github.com/aspnet/Announcements/issues/353).

Si necesita configurar un servicio que dependa de `ILogger<T>`, puede hacerlo si usa la inserción del constructor, o bien si proporciona un Factory Method. Usar un Factory Method es la opción recomendada si no tiene otra alternativa. Por ejemplo, imagine que necesita rellenar una propiedad con un servicio desde la inserción de dependencias:

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

El código resaltado anterior es un elemento `Func` que se ejecuta la primera vez que el contenedor de inserción de dependencias necesita crear una instancia de `MyService`. Puede acceder a cualquiera de los servicios registrados de esta forma.

### <a name="create-logs-in-blazor-webassembly"></a>Creación de registros en WebAssembly de Blazor

Configure los registros en las aplicaciones de WebAssembly de Blazor con la propiedad `WebAssemblyHostBuilder.Logging` en `Program.Main`:

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;

...

var builder = WebAssemblyHostBuilder.CreateDefault(args);

builder.Logging.SetMinimumLevel(LogLevel.Debug);
builder.Logging.AddProvider(new CustomLoggingProvider());
```

La propiedad `Logging` es del tipo <xref:Microsoft.Extensions.Logging.ILoggingBuilder>, de modo que todos los métodos de extensión disponibles en <xref:Microsoft.Extensions.Logging.ILoggingBuilder> también lo están en `Logging`.

### <a name="no-asynchronous-logger-methods"></a>No hay métodos de registrador asincrónicos

El registro debe ser tan rápido que no merezca la pena el costo de rendimiento del código asincrónico. Si el almacén de datos de registro es lento, no escriba directamente en él. Considere la posibilidad de escribir los mensajes de registro en un almacén rápido inicialmente y luego moverlos a la tienda lenta. Por ejemplo, si inicia sesión en SQL Server, no desea hacerlo directamente en un método `Log`, ya que los métodos `Log` son sincrónicos. En su lugar, agregue sincrónicamente mensajes de registro a una cola en memoria y haga que un trabajo en segundo plano extraiga los mensajes de la cola para realizar el trabajo asincrónico de insertar datos en SQL Server. Para obtener más información, vea [este](https://github.com/dotnet/AspNetCore.Docs/issues/11801) problema de GitHub.

## <a name="configuration"></a>Configuración

Uno o varios proveedores de configuración proporcionan la configuración del proveedor de registro:

* Formatos de archivo (INI, JSON y XML).
* Argumentos de la línea de comandos.
* Variables de entorno.
* Objetos de .NET en memoria.
* El almacenamiento de [administrador secreto](xref:security/app-secrets) sin cifrar.
* Un almacén de usuario cifrado, como [Azure Key Vault](xref:security/key-vault-configuration).
* Proveedores personalizados (instalados o creados).

Por ejemplo, la sección `Logging` de archivos de configuración de aplicación suele proporcionar la configuración de registro. En el ejemplo siguiente se muestra el contenido de un archivo *appsettings.Development.json* típico:

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

La propiedad `Logging` puede tener `LogLevel` y propiedades del proveedor de registro (se muestra la consola).

La propiedad `LogLevel` bajo `Logging` especifica el [nivel](#log-level) mínimo que se va a registrar para las categorías seleccionadas. En el ejemplo, las categorías `System` y `Microsoft` se registran en el nivel `Information` y todas las demás se registran en el nivel `Debug`.

Otras propiedades bajo `Logging` especifican proveedores de registro. El ejemplo es para el proveedor de consola. Si un proveedor admite [ámbitos de registro](#log-scopes), `IncludeScopes` indica si están habilitados. Una propiedad de proveedor (como `Console` en el ejemplo) también puede especificar una propiedad `LogLevel`. `LogLevel` en un proveedor especifica niveles de registro para ese proveedor.

Si los niveles se especifican en `Logging.{providername}.LogLevel`, invalidan todo lo establecido en `Logging.LogLevel`.

La API de registro no incluye un escenario que permita cambiar los niveles de registro mientras se ejecuta una aplicación. No obstante, algunos proveedores de configuración pueden volver a cargar la configuración, lo que tiene efecto inmediato en la configuración del registro. Por ejemplo, [FileConfigurationProvider](xref:fundamentals/configuration/index#file-configuration-provider) —agregado por `CreateDefaultBuilder` para leer los archivos de configuración— vuelve a cargar la configuración de registro de forma predeterminada. Si se cambia la configuración en el código mientras se ejecuta una aplicación, la aplicación puede llamar a [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) para actualizar la configuración de registro de la aplicación.

Para obtener información sobre cómo implementar proveedores de configuración, consulte <xref:fundamentals/configuration/index>.

## <a name="sample-logging-output"></a>Salida de registro de ejemplo

Con el código de ejemplo que se muestra en la sección anterior, los registros aparecen en la consola cuando la aplicación se ejecuta desde la línea de comandos. Este es un ejemplo de salida de la consola:

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

Los registros anteriores se generaron mediante la realización de una solicitud HTTP GET a la aplicación de ejemplo en `http://localhost:5000/api/todo/0`.

Este es un ejemplo de los mismos registros tal y como aparecen en la ventana de depuración cuando se ejecuta la aplicación de ejemplo en Visual Studio:

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

Los registros creados por las llamadas de `ILogger` se muestran en la sección anterior, empezando por “TodoApiSample”. Los registros que comienzan por categorías de "Microsoft" son del código de marco de ASP.NET Core. ASP.NET Core y el código de la aplicación usan la misma API y los mismos proveedores de registro.

En el resto de este artículo se explican algunos detalles y opciones para el registro.

## <a name="nuget-packages"></a>Paquetes NuGet

Las interfaces `ILogger` e `ILoggerFactory` se encuentran en [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), y sus implementaciones predeterminadas en [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).

## <a name="log-category"></a>Categoría de registro

Cuando se crea un objeto `ILogger`, se ha especificado una *categoría* para él. Esa categoría se incluye con cada mensaje de registro creado por esa instancia de `ILogger`. La categoría puede ser cualquier cadena, pero la convención es usar el nombre de clase, como "TodoApi.Controllers.TodoController".

Use `ILogger<T>` para obtener una instancia `ILogger` que utiliza el nombre de tipo completo de `T` como la categoría:

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

Para especificar explícitamente la categoría, llame a `ILoggerFactory.CreateLogger`:

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

`ILogger<T>` es equivale a llamar a `CreateLogger` con el nombre de tipo completo de `T`.

## <a name="log-level"></a>Nivel de registro

Cara registro especifica un valor <xref:Microsoft.Extensions.Logging.LogLevel>. El nivel de registro indica la gravedad o importancia. Por ejemplo, podría escribir un registro `Information` cuando un método termina con normalidad y un registro `Warning` cuando un método devuelve un código de estado *404 No encontrado*.

El siguiente código crea los registros `Information` y `Warning`:

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

En el código anterior, el primer parámetro es el [id. de evento del registro](#log-event-id). El segundo parámetro es una plantilla de mensaje con marcadores de posición para los valores de argumento proporcionados por el resto de parámetros de método. Los parámetros de método se explican detalladamente en la [sección de la plantilla de mensaje](#log-message-template) más adelante en este artículo.

Los métodos de registro que incluyen el nivel en el nombre del método (por ejemplo `LogInformation` y `LogWarning`) son [métodos de extensión para ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions). Estos métodos llaman a un método `Log` que toma un parámetro `LogLevel`. Puede llamar directamente al método `Log` en lugar de a uno de estos métodos de extensión, pero la sintaxis es relativamente complicada. Para más información, vea la <xref:Microsoft.Extensions.Logging.ILogger> y el [código fuente de las extensiones de registrador](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).

ASP.NET Core define los niveles de registro siguientes, que aquí se ordenan de menor a mayor gravedad.

* Seguimiento = 0

  Para información que normalmente solo es útil para la depuración. Estos mensajes pueden contener datos confidenciales de la aplicación, por lo que no deben habilitarse en un entorno de producción. *Deshabilitado de forma predeterminada.*

* Depurar = 1

  Para información que puede ser útil para el desarrollo y la depuración. Ejemplo: `Entering method Configure with flag set to true.` Habilite los registros de nivel `Debug` en producción cuando esté solucionando un problema, debido al elevado volumen de registros.

* Información = 2

  Para realizar el seguimiento del flujo general de la aplicación. Estos registros suelen tener algún valor a largo plazo. Ejemplo: `Request received for path /api/todo`

* Advertencia = 3

  Para los eventos anómalos o inesperados en el flujo de la aplicación. Estos pueden incluir errores u otras condiciones que no hacen que la aplicación se detenga, pero que puede que sea necesario investigar. Las excepciones controladas son un lugar común para usar el nivel de registro `Warning`. Ejemplo: `FileNotFoundException for file quotes.txt.`

* Error = 4

  Para los errores y excepciones que no se pueden controlar. Estos mensajes indican un error en la actividad u operación actual (por ejemplo, la solicitud HTTP actual), no un error de toda la aplicación. Mensaje de registro de ejemplo: `Cannot insert record due to duplicate key violation.`

* Crítico = 5

  Para los errores que requieren atención inmediata. Ejemplos: escenarios de pérdida de datos, espacio en disco insuficiente.

Use el nivel de registro para controlar la cantidad de salida del registro que se escribe en un medio de almacenamiento determinado o ventana de presentación. Por ejemplo:

* En producción:
  * El registro en los niveles `Trace` a `Information` genera un gran volumen de mensajes de registro detallados. Para controlar los costos y no superar los límites de almacenamiento de datos, registre los mensajes de nivel `Trace` a `Information` en un almacén de datos de alto volumen y bajo costo.
  * El registro en los niveles `Warning` a `Critical` normalmente produce menos mensajes de registro y de menor tamaño. Por lo tanto, los costos y los límites de almacenamiento no suelen ser un problema, lo que da lugar a una mayor flexibilidad a la hora de elegir el almacén de datos.
* Durante el desarrollo:
  * Registre los mensajes `Warning` a `Critical` en la consola.
  * Agregue los mensajes `Trace` a `Information` al solucionar problemas.

En la sección [Filtrado del registro](#log-filtering) de este artículo se explica cómo controlar los niveles de registro que controla un proveedor.

ASP.NET Core escribe registros de eventos de marco. En los ejemplos de registro anteriores de este artículo se excluyeron los registros por debajo del nivel `Information`, por lo que no se crearon los registros de nivel `Debug` o `Trace`. Este es un ejemplo de registros de consola generados mediante la ejecución de la aplicación de ejemplo configurada para mostrar registros `Debug`:

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

## <a name="log-event-id"></a>Id. de evento del registro

Cada registro se puede especificar un *id. de evento*. La aplicación de ejemplo lo hace mediante una clase `LoggingEvents` definida de forma local:

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/3.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

Un id. de evento asocia un conjunto de eventos. Por ejemplo, todos los registros relacionados con la presentación de una lista de elementos en una página podrían ser 1001.

El proveedor de registro puede almacenar el id. de evento en un campo de identificador, en el mensaje de registro o no almacenarlo. El proveedor de depuración no muestra los identificadores de evento. El proveedor de consola muestra los identificadores de evento entre corchetes después de la categoría:

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a>Plantilla de mensaje de registro

Cada registro especifica una plantilla de mensaje. La plantilla de mensaje puede contener marcadores de posición para los que se proporcionan argumentos. Use los nombres de los marcadores de posición, no números.

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

El orden de los marcadores de posición, no sus nombres, determina qué parámetros se usan para proporcionar sus valores. En el código siguiente, tenga en cuenta que los nombres de parámetro están fuera de la secuencia en la plantilla de mensaje:

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

Este código crea un mensaje de registro con los valores de parámetro en secuencia:

```text
Parameter values: parm1, parm2
```

La plataforma de registro funciona de esta manera para que los proveedores de registro puedan implementar [el registro semántico, también conocido como registro estructurado](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging). Los propios argumentos se pasan al sistema de registro, no solo a la plantilla de mensaje con formato. Esta información permite a los proveedores de registro almacenar los valores de parámetro como campos. Por ejemplo, suponga que las llamadas del método del registrador tiene el aspecto siguiente:

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

Si envía los registros a Azure Table Storage, cada entidad de Azure Table puede tener propiedades `ID` y `RequestTime`, lo que simplifica las consultas en los datos de registro. Una consulta puede buscar todos los registros dentro de un intervalo `RequestTime` determinado sin analizar el tiempo de espera del mensaje de texto.

## <a name="logging-exceptions"></a>Excepciones de registro

Los métodos de registrador tienen sobrecargas que le permiten pasar una excepción, como en el ejemplo siguiente:

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

Cada proveedor controla la información de la excepción de maneras diferentes. Este es un ejemplo de salida del proveedor de depuración del código mostrado anteriormente.

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a>Filtrado del registro

Puede especificar un nivel de registro mínimo para un proveedor y una categoría específicos, o para todos los proveedores o todas las categorías. Los registros por debajo del nivel mínimo no se pasan a ese proveedor, de modo que no se muestran o almacenan.

Para suprimir todos los registros, especifique `LogLevel.None` como el nivel de registro mínimo. El valor entero de `LogLevel.None` es 6, que es superior a `LogLevel.Critical` (5).

### <a name="create-filter-rules-in-configuration"></a>Creación de reglas de filtro en la configuración

El código de la plantilla de proyecto llama a `CreateDefaultBuilder` para configurar el registro para los proveedores de Console, Debug y EventSource (ASP.NET Core 2.2 o versiones posteriores). El método `CreateDefaultBuilder` configura el registro para buscar la configuración en una sección de `Logging`, como se explica [anteriormente en este artículo](#configuration).

Los datos de configuración especifican niveles de registro mínimo por proveedor y categoría, como en el ejemplo siguiente:

[!code-json[](index/samples/3.x/TodoApiSample/appsettings.json)]

Este archivo JSON crea seis reglas de filtro, una para el proveedor de depuración, cuatro para el proveedor de la consola y una para todos los proveedores. Se elige una sola regla para cada proveedor cuando se crea un objeto `ILogger`.

### <a name="filter-rules-in-code"></a>Reglas de filtro en el código

En el siguiente ejemplo se muestra cómo registrar reglas de filtro en el código:

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=2-3)]

El segundo `AddFilter` especifica el proveedor de depuración mediante su nombre de tipo. El primer `AddFilter` se aplica a todos los proveedores, dado que no especifica un tipo de proveedor.

### <a name="how-filtering-rules-are-applied"></a>Cómo se aplican las reglas de filtro

Los datos de configuración y el código de `AddFilter` que se muestran en los ejemplos anteriores crean las reglas que se muestran en la tabla siguiente. Las seis primeras proceden del ejemplo de configuración y las dos últimas del ejemplo de código.

| número | Proveedor      | Categorías que comienzan por...          | Nivel de registro mínimo |
| :----: | ------------- | --------------------------------------- | ----------------- |
| 1      | Depuración         | Todas las categorías                          | Información       |
| 2      | Consola       | Microsoft.AspNetCore.Mvc.Razor.Internal | Advertencia           |
| 3      | Consola       | Microsoft.AspNetCore.Mvc.Razor.Razor    | Depuración             |
| 4      | Consola       | Microsoft.AspNetCore.Mvc.Razor          | Error             |
| 5      | Consola       | Todas las categorías                          | Información       |
| 6      | Todos los proveedores | Todas las categorías                          | Depuración             |
| 7      | Todos los proveedores | Sistema                                  | Depuración             |
| 8      | Depuración         | Microsoft                               | Seguimiento             |

Cuando se crea un objeto `ILogger`, el objeto `ILoggerFactory` selecciona una sola regla por proveedor para aplicar a ese registrador. Todos los mensajes escritos por una instancia `ILogger` se filtran según las reglas seleccionadas. De las reglas disponibles se selecciona la más específica posible para cada par de categoría y proveedor.

Cuando se crea un `ILogger` para una categoría determinada, se usa el algoritmo siguiente para cada proveedor:

* Se seleccionan todas las reglas que coinciden con el proveedor o su alias. Si no se encuentra ninguna coincidencia, se seleccionan todas las reglas con un proveedor vacío.
* Del resultado del paso anterior, se seleccionan las reglas con el prefijo de categoría coincidente más largo. Si no se encuentra ninguna coincidencia, se seleccionan todas las reglas que no especifican una categoría.
* Si se seleccionan varias reglas, se toma la **última**.
* Si no se selecciona ninguna regla, se usa `MinimumLevel`.

Con la lista de reglas anterior, supongamos que crea un objeto `ILogger` para la categoría "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":

* Para el proveedor de depuración, se aplican las reglas 1, 6 y 8. La regla 8 es la más específica, por lo que se selecciona.
* Para el proveedor de la consola, se aplican las reglas 3, 4, 5 y 6. La regla 3 es la más específica.

La instancia `ILogger` resultante envía los registros de nivel `Trace` y superiores al proveedor de depuración. Los registros de nivel `Debug` y superiores se envían al proveedor de consola.

### <a name="provider-aliases"></a>Alias de proveedor

Cada proveedor define un *alias* que se puede utilizar en la configuración en lugar del nombre de tipo completo.  Para los proveedores integrados, use los alias siguientes:

* Consola
* Depuración
* EventSource
* EventLog
* TraceSource
* AzureAppServicesFile
* AzureAppServicesBlob
* ApplicationInsights

### <a name="default-minimum-level"></a>Nivel mínimo predeterminado

Hay una configuración de nivel mínimo que solo tiene efecto si no se aplica ninguna regla de configuración o código para un proveedor y una categoría determinados. En el ejemplo siguiente se muestra cómo establecer el nivel mínimo:

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

Si no establece explícitamente el nivel mínimo, el valor predeterminado es `Information`, lo que significa que los registros `Trace` y `Debug` se omiten.

### <a name="filter-functions"></a>Funciones de filtro

Se invoca una función de filtro para todos los proveedores y categorías que no tienen reglas asignadas mediante configuración o código. El código de la función tiene acceso al tipo de proveedor, la categoría y el nivel de registro. Por ejemplo:

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=3-11)]

## <a name="system-categories-and-levels"></a>Niveles y categorías del sistema

Estas son algunas categorías que ASP.NET Core y Entity Framework Core usan, con notas sobre lo que los registros de espera de ellas:

| Categoría                            | Notas |
| ----------------------------------- | ----- |
| Microsoft.AspNetCore                | Diagnósticos generales de ASP.NET Core. |
| Microsoft.AspNetCore.DataProtection | Qué claves se tuvieron en cuenta, encontraron y usaron. |
| Microsoft.AspNetCore.HostFiltering  | Hosts permitidos. |
| Microsoft.AspNetCore.Hosting        | Cuánto tiempo tardaron en completarse las solicitudes HTTP y a qué hora comenzaron. Qué ensamblados de inicio de hospedaje se cargaron. |
| Microsoft.AspNetCore.Mvc            | Diagnósticos de MVC y Razor. Enlace de modelos, ejecución de filtros, compilación de vistas y selección de acciones. |
| Microsoft.AspNetCore.Routing        | Información de coincidencia de ruta. |
| Microsoft.AspNetCore.Server         | Inicio y detención de conexión y mantener las respuestas activas. Información de certificado HTTPS. |
| Microsoft.AspNetCore.StaticFiles    | Archivos servidos. |
| Microsoft.EntityFrameworkCore       | Diagnósticos generales de Entity Framework Core. Actividad y la configuración de bases de datos, detección de cambios y migraciones. |

## <a name="log-scopes"></a>Ámbitos de registro

 Un *ámbito* puede agrupar un conjunto de operaciones lógicas. Esta agrupación se puede utilizar para adjuntar los mismos datos para cada registro que se crea como parte de un conjunto. Por ejemplo, cada registro creado como parte del procesamiento de una transacción puede incluir el identificador de dicha transacción.

Un ámbito es un tipo `IDisposable` devuelto por el método <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> y se conserva hasta que se elimina. Use un ámbito encapsulando las llamadas de registrador en un bloque `using`:

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

El código siguiente permite ámbitos para el proveedor de la consola:

*Program.cs*:

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=6)]

> [!NOTE]
> Es necesario configurar la opción del registrador de consola `IncludeScopes` para habilitar el registro basado en el ámbito.
>
> Para obtener información sobre la configuración, consulte la sección [Configuración](#configuration).

Cada mensaje de registro incluye la información de ámbito:

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a>Proveedores de registro integrados

ASP.NET Core incluye los proveedores siguientes:

* [Consola](#console-provider)
* [Depurar](#debug-provider)
* [EventSource](#event-source-provider)
* [EventLog](#windows-eventlog-provider)
* [TraceSource](#tracesource-provider)
* [AzureAppServicesFile](#azure-app-service-provider)
* [AzureAppServicesBlob](#azure-app-service-provider)
* [ApplicationInsights](#azure-application-insights-trace-logging)

Para obtener información sobre stdout y el registro de depuración con el módulo ASP.NET Core, consulte <xref:test/troubleshoot-azure-iis> y <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.

### <a name="console-provider"></a>Proveedor de la consola

El paquete de proveedor [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) envía la salida del registro a la consola. 

```csharp
logging.AddConsole();
```

Para ver una salida de registro de la consola, abra un símbolo del sistema en la carpeta del proyecto y ejecute este comando:

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a>Proveedor de depuración

El paquete de proveedor [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) escribe la salida del registro mediante la clase [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) (llamadas a métodos `Debug.WriteLine`).

En Linux, este proveedor escribe registros en */var/log/message*.

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a>Proveedor de origen del evento

El paquete del proveedor [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) escribe en una multiplataforma de origen del evento con el nombre `Microsoft-Extensions-Logging`. En Windows, el proveedor utiliza [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).

```csharp
logging.AddEventSourceLogger();
```

El proveedor de origen del evento se agrega automáticamente cuando se llama a `CreateDefaultBuilder` para compilar el host.

#### <a name="dotnet-trace-tooling"></a>herramienta de seguimiento de dotnet

La herramienta de [seguimiento de dotnet](/dotnet/core/diagnostics/dotnet-trace) es una herramienta global de CLI multiplataforma que permite la recopilación de seguimientos de .NET Core de un proceso en ejecución. La herramienta recopila datos del proveedor <xref:Microsoft.Extensions.Logging.EventSource> mediante un <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>.

Instale la herramienta de seguimiento de dotnet con el siguiente comando:

```dotnetcli
dotnet tool install --global dotnet-trace
```

Use la herramienta de seguimiento de dotnet para recopilar un seguimiento de una aplicación:

1. Si la aplicación no compila el host con `CreateDefaultBuilder`, agregue el [proveedor de origen del evento](#event-source-provider) a la configuración de registro de la aplicación.

1. Ejecute la aplicación con el comando `dotnet run`.

1. Determine el identificador del proceso (PID) de la aplicación .NET Core:

   * En Windows, siga uno de estos procedimientos:
     * Administrador de tareas (Ctrl + Alt + Supr)
     * [Comando TaskList](/windows-server/administration/windows-commands/tasklist)
     * [Comando de PowerShell Get-Process](/powershell/module/microsoft.powershell.management/get-process)
   * En Linux, use el [comando pidof](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html).

   Busque el PID del proceso que tenga el mismo nombre que el ensamblado de la aplicación.

1. Ejecute el comando `dotnet trace`.

   Sintaxis general del comando:

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"
   ```

   Al usar un shell de comandos de PowerShell, incluya el valor `--providers` entre comillas simples (`'`):

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers 'Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"'
   ```

   En plataformas que no sean Windows, agregue la opción `-f speedscope` para cambiar el formato del archivo de seguimiento de salida a `speedscope`.

   | Palabra clave | Descripción |
   | :-----: | ----------- |
   | 1       | Registre los eventos meta sobre el elemento `LoggingEventSource`. No registre eventos de `ILogger`). |
   | 2       | Activa el evento `Message` cuando se llama a `ILogger.Log()`. Proporciona la información mediante programación (sin formato). |
   | 4       | Activa el evento `FormatMessage` cuando se llama a `ILogger.Log()`. Proporciona la versión de cadena con formato de la información. |
   | 8       | Activa el evento `MessageJson` cuando se llama a `ILogger.Log()`. Proporciona una representación JSON de los argumentos. |

   | Nivel de evento | Descripción     |
   | :---------: | --------------- |
   | 0           | `LogAlways`     |
   | 1           | `Critical`      |
   | 2           | `Error`         |
   | 3           | `Warning`       |
   | 4           | `Informational` |
   | 5           | `Verbose`       |

   Las entradas `FilterSpecs` de `{Logger Category}` y `{Event Level}` representan condiciones de filtrado de registros adicionales. Separe las entradas `FilterSpecs` con un punto y coma (`;`).

   Ejemplo de uso de un shell de comandos de Windows (**no** hay comillas simples alrededor del valor `--providers`):

   ```dotnetcli
   dotnet trace collect -p {PID} --providers Microsoft-Extensions-Logging:4:2:FilterSpecs=\"Microsoft.AspNetCore.Hosting*:4\"
   ```

   El comando anterior activa lo siguiente:

   * Registrador de origen del evento para generar cadenas con formato (`4`) de los errores (`2`).
   * Registro `Microsoft.AspNetCore.Hosting` en el nivel de registro `Informational` (`4`).

1. Presione la tecla Entrar o Ctrl + C para detener las herramientas de seguimiento de dotnet.

   El seguimiento se guarda con el nombre *trace.nettrace* en la carpeta en la que se ejecuta el comando `dotnet trace`.

1. Abra el seguimiento con [Perfview](#perfview). Abra el archivo *trace.nettrace* y explore los eventos de seguimiento.

Para obtener más información, consulte:

* [Seguimiento de la utilidad de análisis de rendimiento (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace) (documentación de .NET Core)
* [Seguimiento de la utilidad de análisis de rendimiento (dotnet-trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (documentación del repositorio de GitHub sobre diagnóstico y dotnet)
* [Clase LoggingEventSource](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (Explorador de API de .NET)
* <xref:System.Diagnostics.Tracing.EventLevel>
* [Origen de referencia LoggingEventSource (3.0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs): para obtener el origen de referencia de otra versión, cambie la rama a `release/{Version}`, donde `{Version}` corresponde a la versión de ASP.NET Core deseada.
* [Perfview](#perfview): es útil para ver los seguimientos de origen del evento.

#### <a name="perfview"></a>Perfview

Use la [utilidad PerfView](https://github.com/Microsoft/perfview) para recopilar y ver los registros. Hay otras herramientas para ver los registros ETW, pero PerfView proporciona la mejor experiencia para trabajar con los eventos ETW emitidos por ASP.NET Core.

Para configurar PerfView para la recopilación de eventos registrados por este proveedor, agregue la cadena `*Microsoft-Extensions-Logging` a la lista **Proveedores adicionales**. (No olvide el asterisco al principio de la cadena).

![Proveedores adicionales de Perfview](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a>Proveedor EventLog de Windows

El paquete de proveedor [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) envía la salida del registro al Registro de eventos de Windows.

```csharp
logging.AddEventLog();
```

Las [sobrecargas de AddEventLog](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) permiten pasar <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>. Si es `null` o no se especifica, se usa la siguiente configuración predeterminada:

* `LogName`: "Aplicación"
* `SourceName`: "Entorno de ejecución .NET"
* `MachineName`: equipo local

Los eventos se registran para el [Nivel de advertencia y superior](#log-level). Para registrar eventos inferiores a `Warning`, establezca el nivel de registro de forma explícita. Por ejemplo, agregue lo siguiente al archivo *appsettings.json*:

```json
"EventLog": {
  "LogLevel": {
    "Default": "Information"
  }
}
```

### <a name="tracesource-provider"></a>Proveedor TraceSource

El paquete de proveedor [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) usa las bibliotecas y proveedores de <xref:System.Diagnostics.TraceSource>.

```csharp
logging.AddTraceSource(sourceSwitchName);
```

[Las sobrecargas de AddTraceSource](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) permiten pasar un modificador de origen y un agente de escucha de seguimiento.

Para usar este proveedor, una aplicación debe ejecutarse en .NET Framework (en lugar de .NET Core). El proveedor puede enrutar mensajes a una variedad de [agentes de escucha](/dotnet/framework/debug-trace-profile/trace-listeners), como <xref:System.Diagnostics.TextWriterTraceListener> que se usa en la aplicación de ejemplo.

### <a name="azure-app-service-provider"></a>Proveedor Azure App Service

El paquete de proveedor [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) escribe los registros en archivos de texto en el sistema de archivos de una aplicación de Azure App Service y en [Blob Storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) en una cuenta de Azure Storage.

```csharp
logging.AddAzureWebAppDiagnostics();
```

El paquete del proveedor no se incluye en el marco compartido. Para usar el proveedor, agregue el paquete del proveedor al proyecto.

Para configurar las opciones de proveedor, use <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> y <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, tal y como se muestra en el ejemplo siguiente:

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AzLogOptions&highlight=17-28)]

Al realizar una implementación en una aplicación de App Service, esta respeta la configuración de la sección [Registros de App Service](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) de la página **App Service** de Azure Portal. Cuando se actualiza la configuración siguiente, los cambios se aplican de inmediato sin necesidad de reiniciar ni de volver a implementar la aplicación.

* **Registro de la aplicación (sistema de archivos)**
* **Registro de la aplicación (blob)**

La ubicación predeterminada de los archivos de registro es la carpeta *D:\\home\\LogFiles\\Application* y el nombre de archivo predeterminado es *diagnostics-aaaammdd.txt*. El límite de tamaño de archivo predeterminado es 10 MB, y el número máximo predeterminado de archivos que se conservan es 2. El nombre de blob predeterminado es *{nombre-de-la-aplicación}{marca de tiempo}/aaaa/mm/dd/hh/{guid}-applicationLog.txt*.

El proveedor solo funciona cuando el proyecto se ejecuta en el entorno de Azure. No tiene ningún efecto cuando el proyecto se ejecuta de manera local (no escribe en los archivos locales ni en el almacenamiento de desarrollo local de blobs).

#### <a name="azure-log-streaming"></a>Secuencias de registro de Azure

Las secuencias de registro de Azure permiten ver la actividad de registro en tiempo real desde:

* El servidor de aplicaciones
* El servidor web
* Error del seguimiento de solicitudes

Para configurar las secuencias de registro de Azure:

* Navegue hasta la página **Registros de App Service** desde la página de portal de la aplicación.
* Establezca **Registro de la aplicación (sistema de archivos)** en **Activado**.
* Elija el **Nivel** de registro. Este valor solo se aplica a las secuencias de registro de Azure, no a otros proveedores de registro de la aplicación.

Navegue hasta la página **Secuencia de registro** para consultar los mensajes de la aplicación. La aplicación los registra a través de la interfaz `ILogger`.

### <a name="azure-application-insights-trace-logging"></a>Registro de seguimiento de Azure Application Insights

El proveedor de paquete [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) escribe los registros en Azure Application Insights. Application Insights es un servicio que supervisa una aplicación web y proporciona herramientas para consultar y analizar los datos de telemetría. Si usa este proveedor, puede consultar y analizar los registros mediante las herramientas de Application Insights.

El proveedor de registro se incluye como dependencia de [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), que es el paquete que proporciona toda la telemetría disponible para ASP.NET Core. Si usa este paquete, no tiene que instalar el proveedor de paquete.

No use el paquete [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web)&mdash;que es para ASP.NET 4.x.

Para obtener más información, vea los siguientes recursos:

* [Información general de Application Insights](/azure/application-insights/app-insights-overview)
* [Application Insights para aplicaciones de ASP.NET Core](/azure/azure-monitor/app/asp-net-core): comience aquí si quiere implementar la variedad completa de telemetría de Application Insights junto con el registro.
* [ApplicationInsightsLoggerProvider para los registros de .NET Core ILogger](/azure/azure-monitor/app/ilogger): comience aquí si quiere implementar el proveedor de registro sin el resto de la telemetría de Application Insights.
* [Adaptadores de registro de Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs)
* [Instalación, configuración e inicialización del SDK de Application Insights](/learn/modules/instrument-web-app-code-with-application-insights): tutorial interactivo en el sitio de Microsoft Learn.

## <a name="third-party-logging-providers"></a>Proveedores de registro de terceros

Plataformas de registro de terceros que funcionan con ASP.NET Core:

* [elmah.io](https://elmah.io/) ([repositorio de GitHub](https://github.com/elmahio/Elmah.Io.Extensions.Logging))
* [Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([repositorio de GitHub](https://github.com/mattwcole/gelf-extensions-logging))
* [JSNLog](https://jsnlog.com/) ([repositorio de GitHub](https://github.com/mperdeck/jsnlog))
* [KissLog.net](https://kisslog.net/) ([repositorio de GitHub](https://github.com/catalingavan/KissLog-net))
* [Log4Net](https://logging.apache.org/log4net/) ([repositorio de GitHub](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))
* [Loggr](https://loggr.net/) ([repositorio de GitHub](https://github.com/imobile3/Loggr.Extensions.Logging))
* [NLog](https://nlog-project.org/) ([repositorio de GitHub](https://github.com/NLog/NLog.Extensions.Logging))
* [Sentry](https://sentry.io/welcome/) ([repositorio de GitHub](https://github.com/getsentry/sentry-dotnet))
* [Serilog](https://serilog.net/) ([repositorio de GitHub](https://github.com/serilog/serilog-aspnetcore))
* [Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([repositorio de GitHub](https://github.com/googleapis/google-cloud-dotnet))

Algunas plataformas de terceros pueden realizar [registro semántico, también conocido como registro estructurado](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

El uso de una plataforma de terceros es similar al uso de uno de los proveedores integrados:

1. Agregue un paquete NuGet al proyecto.
1. Llame a un método de extensión `ILoggerFactory` proporcionado por el marco de registro.

Para más información, vea la documentación de cada proveedor. Microsoft no admite los proveedores de registro de terceros.

## <a name="additional-resources"></a>Recursos adicionales

* <xref:fundamentals/logging/loggermessage>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

Por [Tom Dykstra](https://github.com/tdykstra) y [Steve Smith](https://ardalis.com/)

.NET Core es compatible con una API de registro que funciona con una gran variedad de proveedores de registro integrados y de terceros. En este artículo se muestra cómo usar las API de registro con proveedores integrados.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))

## <a name="add-providers"></a>Incorporación de proveedores

Un proveedor de registro muestra o almacena registros. Por ejemplo, el proveedor de consola muestra los registros en la consola y el proveedor de Azure Application Insights los almacena en Azure Application Insights. Los registros se pueden enviar a varios destinos mediante la incorporación de varios proveedores.

Para usar un proveedor, llame al método de extensión `Add{provider name}` del proveedor en *Program.cs*:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=18-20)]

El código anterior requiere referencias a `Microsoft.Extensions.Logging` y `Microsoft.Extensions.Configuration`.

La plantilla de proyecto predeterminada llama a <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, que agrega los siguientes proveedores de registro:

* Consola
* Depuración
* EventSource (a partir de ASP.NET Core 2.2)

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_TemplateCode&highlight=7)]

Si usa `CreateDefaultBuilder`, puede reemplazar los proveedores predeterminados por sus propios valores. Llame a <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> y agregue los proveedores que desee.

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=18-22)]

Obtenga más información sobre los [proveedores de registro integrados](#built-in-logging-providers) y los [proveedores de registro de terceros](#third-party-logging-providers) más adelante en el artículo.

## <a name="create-logs"></a>Creación de registros

Para crear registros, use un objeto de <xref:Microsoft.Extensions.Logging.ILogger%601>. En una aplicación web o servicio hospedado, obtenga un elemento `ILogger` de la inserción de dependencias. En aplicaciones de consola que no sean de host, use `LoggerFactory` para crear un elemento `ILogger`.

En el ejemplo siguiente de ASP.NET Core, se crea un registrador con `TodoApiSample.Pages.AboutModel` como la categoría. La *categoría* de registro es una cadena que está asociada con cada registro. La instancia de `ILogger<T>` proporcionada por la inserción de dependencias genera registros que tienen el nombre completo del tipo `T` como la categoría. 

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

En los siguientes ejemplos de aplicación de consola y ASP.NET Core, el registrador se usa para crear registros con `Information` como el nivel. El *nivel* de registro indica la gravedad del evento registrado.

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

Los [niveles](#log-level) y las [categorías](#log-category) se explican detalladamente más adelante en este artículo.

### <a name="create-logs-in-startup"></a>Creación de registros durante el inicio

Para escribir registros en la clase `Startup`, incluya un parámetro `ILogger` en la signatura de construcción:

[!code-csharp[](index/samples/2.x/TodoApiSample/Startup.cs?name=snippet_Startup&highlight=3,5,8,20,27)]

### <a name="create-logs-in-the-program-class"></a>Crear registros en la clase del programa

Para escribir registros la clase `Program`, obtenga una instancia `ILogger` de inserción de dependencias:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

No se admite directamente el registro durante la construcción del host. Sin embargo, se puede usar un registrador independiente. En el ejemplo siguiente, se usa un registrador [Serilog](https://serilog.net/) para registrarse en `CreateWebHostBuilder`. `AddSerilog` usa la configuración estática especificada en `Log.Logger`:

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

### <a name="no-asynchronous-logger-methods"></a>No hay métodos de registrador asincrónicos

El registro debe ser tan rápido que no merezca la pena el costo de rendimiento del código asincrónico. Si el almacén de datos de registro es lento, no escriba directamente en él. Considere la posibilidad de escribir los mensajes de registro en un almacén rápido inicialmente y luego moverlos a la tienda lenta. Por ejemplo, si inicia sesión en SQL Server, no desea hacerlo directamente en un método `Log`, ya que los métodos `Log` son sincrónicos. En su lugar, agregue sincrónicamente mensajes de registro a una cola en memoria y haga que un trabajo en segundo plano extraiga los mensajes de la cola para realizar el trabajo asincrónico de insertar datos en SQL Server. Para obtener más información, vea [este](https://github.com/dotnet/AspNetCore.Docs/issues/11801) problema de GitHub.

## <a name="configuration"></a>Configuración

Uno o varios proveedores de configuración proporcionan la configuración del proveedor de registro:

* Formatos de archivo (INI, JSON y XML).
* Argumentos de la línea de comandos.
* Variables de entorno.
* Objetos de .NET en memoria.
* El almacenamiento de [administrador secreto](xref:security/app-secrets) sin cifrar.
* Un almacén de usuario cifrado, como [Azure Key Vault](xref:security/key-vault-configuration).
* Proveedores personalizados (instalados o creados).

Por ejemplo, la sección `Logging` de archivos de configuración de aplicación suele proporcionar la configuración de registro. En el ejemplo siguiente se muestra el contenido de un archivo *appsettings.Development.json* típico:

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

La propiedad `Logging` puede tener `LogLevel` y propiedades del proveedor de registro (se muestra la consola).

La propiedad `LogLevel` bajo `Logging` especifica el [nivel](#log-level) mínimo que se va a registrar para las categorías seleccionadas. En el ejemplo, las categorías `System` y `Microsoft` se registran en el nivel `Information` y todas las demás se registran en el nivel `Debug`.

Otras propiedades bajo `Logging` especifican proveedores de registro. El ejemplo es para el proveedor de consola. Si un proveedor admite [ámbitos de registro](#log-scopes), `IncludeScopes` indica si están habilitados. Una propiedad de proveedor (como `Console` en el ejemplo) también puede especificar una propiedad `LogLevel`. `LogLevel` en un proveedor especifica niveles de registro para ese proveedor.

Si los niveles se especifican en `Logging.{providername}.LogLevel`, invalidan todo lo establecido en `Logging.LogLevel`.

La API de registro no incluye un escenario que permita cambiar los niveles de registro mientras se ejecuta una aplicación. No obstante, algunos proveedores de configuración pueden volver a cargar la configuración, lo que tiene efecto inmediato en la configuración del registro. Por ejemplo, [FileConfigurationProvider](xref:fundamentals/configuration/index#file-configuration-provider) —agregado por `CreateDefaultBuilder` para leer los archivos de configuración— vuelve a cargar la configuración de registro de forma predeterminada. Si se cambia la configuración en el código mientras se ejecuta una aplicación, la aplicación puede llamar a [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) para actualizar la configuración de registro de la aplicación.

Para obtener información sobre cómo implementar proveedores de configuración, consulte <xref:fundamentals/configuration/index>.

## <a name="sample-logging-output"></a>Salida de registro de ejemplo

Con el código de ejemplo que se muestra en la sección anterior, los registros aparecen en la consola cuando la aplicación se ejecuta desde la línea de comandos. Este es un ejemplo de salida de la consola:

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

Los registros anteriores se generaron mediante la realización de una solicitud HTTP GET a la aplicación de ejemplo en `http://localhost:5000/api/todo/0`.

Este es un ejemplo de los mismos registros tal y como aparecen en la ventana de depuración cuando se ejecuta la aplicación de ejemplo en Visual Studio:

```console
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:53104/api/todo/0  
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item 0
TodoApi.Controllers.TodoController:Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 152.5657ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 316.3195ms 404
```

Los registros creados por las llamadas de `ILogger` se muestran en la sección anterior, empezando por “TodoApi”. Los registros que comienzan por categorías de "Microsoft" son del código de marco de ASP.NET Core. ASP.NET Core y el código de la aplicación usan la misma API y los mismos proveedores de registro.

En el resto de este artículo se explican algunos detalles y opciones para el registro.

## <a name="nuget-packages"></a>Paquetes NuGet

Las interfaces `ILogger` e `ILoggerFactory` se encuentran en [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), y sus implementaciones predeterminadas en [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).

## <a name="log-category"></a>Categoría de registro

Cuando se crea un objeto `ILogger`, se ha especificado una *categoría* para él. Esa categoría se incluye con cada mensaje de registro creado por esa instancia de `ILogger`. La categoría puede ser cualquier cadena, pero la convención es usar el nombre de clase, como "TodoApi.Controllers.TodoController".

Use `ILogger<T>` para obtener una instancia `ILogger` que utiliza el nombre de tipo completo de `T` como la categoría:

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

Para especificar explícitamente la categoría, llame a `ILoggerFactory.CreateLogger`:

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

`ILogger<T>` es equivale a llamar a `CreateLogger` con el nombre de tipo completo de `T`.

## <a name="log-level"></a>Nivel de registro

Cara registro especifica un valor <xref:Microsoft.Extensions.Logging.LogLevel>. El nivel de registro indica la gravedad o importancia. Por ejemplo, podría escribir un registro `Information` cuando un método termina con normalidad y un registro `Warning` cuando un método devuelve un código de estado *404 No encontrado*.

El siguiente código crea los registros `Information` y `Warning`:

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

En el código anterior, el primer parámetro es el [id. de evento del registro](#log-event-id). El segundo parámetro es una plantilla de mensaje con marcadores de posición para los valores de argumento proporcionados por el resto de parámetros de método. Los parámetros de método se explican detalladamente en la [sección de la plantilla de mensaje](#log-message-template) más adelante en este artículo.

Los métodos de registro que incluyen el nivel en el nombre del método (por ejemplo `LogInformation` y `LogWarning`) son [métodos de extensión para ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions). Estos métodos llaman a un método `Log` que toma un parámetro `LogLevel`. Puede llamar directamente al método `Log` en lugar de a uno de estos métodos de extensión, pero la sintaxis es relativamente complicada. Para más información, vea la <xref:Microsoft.Extensions.Logging.ILogger> y el [código fuente de las extensiones de registrador](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).

ASP.NET Core define los niveles de registro siguientes, que aquí se ordenan de menor a mayor gravedad.

* Seguimiento = 0

  Para información que normalmente solo es útil para la depuración. Estos mensajes pueden contener datos confidenciales de la aplicación, por lo que no deben habilitarse en un entorno de producción. *Deshabilitado de forma predeterminada.*

* Depurar = 1

  Para información que puede ser útil para el desarrollo y la depuración. Ejemplo: `Entering method Configure with flag set to true.` Habilite los registros de nivel `Debug` en producción cuando esté solucionando un problema, debido al elevado volumen de registros.

* Información = 2

  Para realizar el seguimiento del flujo general de la aplicación. Estos registros suelen tener algún valor a largo plazo. Ejemplo: `Request received for path /api/todo`

* Advertencia = 3

  Para los eventos anómalos o inesperados en el flujo de la aplicación. Estos pueden incluir errores u otras condiciones que no hacen que la aplicación se detenga, pero que puede que sea necesario investigar. Las excepciones controladas son un lugar común para usar el nivel de registro `Warning`. Ejemplo: `FileNotFoundException for file quotes.txt.`

* Error = 4

  Para los errores y excepciones que no se pueden controlar. Estos mensajes indican un error en la actividad u operación actual (por ejemplo, la solicitud HTTP actual), no un error de toda la aplicación. Mensaje de registro de ejemplo: `Cannot insert record due to duplicate key violation.`

* Crítico = 5

  Para los errores que requieren atención inmediata. Ejemplos: escenarios de pérdida de datos, espacio en disco insuficiente.

Use el nivel de registro para controlar la cantidad de salida del registro que se escribe en un medio de almacenamiento determinado o ventana de presentación. Por ejemplo:

* En producción:
  * El registro en los niveles `Trace` a `Information` genera un gran volumen de mensajes de registro detallados. Para controlar los costos y no superar los límites de almacenamiento de datos, registre los mensajes de nivel `Trace` a `Information` en un almacén de datos de alto volumen y bajo costo.
  * El registro en los niveles `Warning` a `Critical` normalmente produce menos mensajes de registro y de menor tamaño. Por lo tanto, los costos y los límites de almacenamiento no suelen ser un problema, lo que da lugar a una mayor flexibilidad a la hora de elegir el almacén de datos.
* Durante el desarrollo:
  * Registre los mensajes `Warning` a `Critical` en la consola.
  * Agregue los mensajes `Trace` a `Information` al solucionar problemas.

En la sección [Filtrado del registro](#log-filtering) de este artículo se explica cómo controlar los niveles de registro que controla un proveedor.

ASP.NET Core escribe registros de eventos de marco. En los ejemplos de registro anteriores de este artículo se excluyeron los registros por debajo del nivel `Information`, por lo que no se crearon los registros de nivel `Debug` o `Trace`. Este es un ejemplo de registros de consola generados mediante la ejecución de la aplicación de ejemplo configurada para mostrar registros `Debug`:

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

## <a name="log-event-id"></a>Id. de evento del registro

Cada registro se puede especificar un *id. de evento*. La aplicación de ejemplo lo hace mediante una clase `LoggingEvents` definida de forma local:

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

Un id. de evento asocia un conjunto de eventos. Por ejemplo, todos los registros relacionados con la presentación de una lista de elementos en una página podrían ser 1001.

El proveedor de registro puede almacenar el id. de evento en un campo de identificador, en el mensaje de registro o no almacenarlo. El proveedor de depuración no muestra los identificadores de evento. El proveedor de consola muestra los identificadores de evento entre corchetes después de la categoría:

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a>Plantilla de mensaje de registro

Cada registro especifica una plantilla de mensaje. La plantilla de mensaje puede contener marcadores de posición para los que se proporcionan argumentos. Use los nombres de los marcadores de posición, no números.

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

El orden de los marcadores de posición, no sus nombres, determina qué parámetros se usan para proporcionar sus valores. En el código siguiente, tenga en cuenta que los nombres de parámetro están fuera de la secuencia en la plantilla de mensaje:

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

Este código crea un mensaje de registro con los valores de parámetro en secuencia:

```text
Parameter values: parm1, parm2
```

La plataforma de registro funciona de esta manera para que los proveedores de registro puedan implementar [el registro semántico, también conocido como registro estructurado](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging). Los propios argumentos se pasan al sistema de registro, no solo a la plantilla de mensaje con formato. Esta información permite a los proveedores de registro almacenar los valores de parámetro como campos. Por ejemplo, suponga que las llamadas del método del registrador tiene el aspecto siguiente:

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

Si envía los registros a Azure Table Storage, cada entidad de Azure Table puede tener propiedades `ID` y `RequestTime`, lo que simplifica las consultas en los datos de registro. Una consulta puede buscar todos los registros dentro de un intervalo `RequestTime` determinado sin analizar el tiempo de espera del mensaje de texto.

## <a name="logging-exceptions"></a>Excepciones de registro

Los métodos de registrador tienen sobrecargas que le permiten pasar una excepción, como en el ejemplo siguiente:

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

Cada proveedor controla la información de la excepción de maneras diferentes. Este es un ejemplo de salida del proveedor de depuración del código mostrado anteriormente.

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a>Filtrado del registro

Puede especificar un nivel de registro mínimo para un proveedor y una categoría específicos, o para todos los proveedores o todas las categorías. Los registros por debajo del nivel mínimo no se pasan a ese proveedor, de modo que no se muestran o almacenan.

Para suprimir todos los registros, especifique `LogLevel.None` como el nivel de registro mínimo. El valor entero de `LogLevel.None` es 6, que es superior a `LogLevel.Critical` (5).

### <a name="create-filter-rules-in-configuration"></a>Creación de reglas de filtro en la configuración

El código de la plantilla de proyecto llama a `CreateDefaultBuilder` para configurar el registro para los proveedores de Console, Debug y EventSource (ASP.NET Core 2.2 o versiones posteriores). El método `CreateDefaultBuilder` configura el registro para buscar la configuración en una sección de `Logging`, como se explica [anteriormente en este artículo](#configuration).

Los datos de configuración especifican niveles de registro mínimo por proveedor y categoría, como en el ejemplo siguiente:

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

Este archivo JSON crea seis reglas de filtro, una para el proveedor de depuración, cuatro para el proveedor de la consola y una para todos los proveedores. Se elige una sola regla para cada proveedor cuando se crea un objeto `ILogger`.

### <a name="filter-rules-in-code"></a>Reglas de filtro en el código

En el siguiente ejemplo se muestra cómo registrar reglas de filtro en el código:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

El segundo `AddFilter` especifica el proveedor de depuración mediante su nombre de tipo. El primer `AddFilter` se aplica a todos los proveedores, dado que no especifica un tipo de proveedor.

### <a name="how-filtering-rules-are-applied"></a>Cómo se aplican las reglas de filtro

Los datos de configuración y el código de `AddFilter` que se muestran en los ejemplos anteriores crean las reglas que se muestran en la tabla siguiente. Las seis primeras proceden del ejemplo de configuración y las dos últimas del ejemplo de código.

| número | Proveedor      | Categorías que comienzan por...          | Nivel de registro mínimo |
| :----: | ------------- | --------------------------------------- | ----------------- |
| 1      | Depuración         | Todas las categorías                          | Información       |
| 2      | Consola       | Microsoft.AspNetCore.Mvc.Razor.Internal | Advertencia           |
| 3      | Consola       | Microsoft.AspNetCore.Mvc.Razor.Razor    | Depuración             |
| 4      | Consola       | Microsoft.AspNetCore.Mvc.Razor          | Error             |
| 5      | Consola       | Todas las categorías                          | Información       |
| 6      | Todos los proveedores | Todas las categorías                          | Depuración             |
| 7      | Todos los proveedores | Sistema                                  | Depuración             |
| 8      | Depuración         | Microsoft                               | Seguimiento             |

Cuando se crea un objeto `ILogger`, el objeto `ILoggerFactory` selecciona una sola regla por proveedor para aplicar a ese registrador. Todos los mensajes escritos por una instancia `ILogger` se filtran según las reglas seleccionadas. De las reglas disponibles se selecciona la más específica posible para cada par de categoría y proveedor.

Cuando se crea un `ILogger` para una categoría determinada, se usa el algoritmo siguiente para cada proveedor:

* Se seleccionan todas las reglas que coinciden con el proveedor o su alias. Si no se encuentra ninguna coincidencia, se seleccionan todas las reglas con un proveedor vacío.
* Del resultado del paso anterior, se seleccionan las reglas con el prefijo de categoría coincidente más largo. Si no se encuentra ninguna coincidencia, se seleccionan todas las reglas que no especifican una categoría.
* Si se seleccionan varias reglas, se toma la **última**.
* Si no se selecciona ninguna regla, se usa `MinimumLevel`.

Con la lista de reglas anterior, supongamos que crea un objeto `ILogger` para la categoría "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":

* Para el proveedor de depuración, se aplican las reglas 1, 6 y 8. La regla 8 es la más específica, por lo que se selecciona.
* Para el proveedor de la consola, se aplican las reglas 3, 4, 5 y 6. La regla 3 es la más específica.

La instancia `ILogger` resultante envía los registros de nivel `Trace` y superiores al proveedor de depuración. Los registros de nivel `Debug` y superiores se envían al proveedor de consola.

### <a name="provider-aliases"></a>Alias de proveedor

Cada proveedor define un *alias* que se puede utilizar en la configuración en lugar del nombre de tipo completo.  Para los proveedores integrados, use los alias siguientes:

* Consola
* Depuración
* EventSource
* EventLog
* TraceSource
* AzureAppServicesFile
* AzureAppServicesBlob
* ApplicationInsights

### <a name="default-minimum-level"></a>Nivel mínimo predeterminado

Hay una configuración de nivel mínimo que solo tiene efecto si no se aplica ninguna regla de configuración o código para un proveedor y una categoría determinados. En el ejemplo siguiente se muestra cómo establecer el nivel mínimo:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

Si no establece explícitamente el nivel mínimo, el valor predeterminado es `Information`, lo que significa que los registros `Trace` y `Debug` se omiten.

### <a name="filter-functions"></a>Funciones de filtro

Se invoca una función de filtro para todos los proveedores y categorías que no tienen reglas asignadas mediante configuración o código. El código de la función tiene acceso al tipo de proveedor, la categoría y el nivel de registro. Por ejemplo:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

## <a name="system-categories-and-levels"></a>Niveles y categorías del sistema

Estas son algunas categorías que ASP.NET Core y Entity Framework Core usan, con notas sobre lo que los registros de espera de ellas:

| Categoría                            | Notas |
| ----------------------------------- | ----- |
| Microsoft.AspNetCore                | Diagnósticos generales de ASP.NET Core. |
| Microsoft.AspNetCore.DataProtection | Qué claves se tuvieron en cuenta, encontraron y usaron. |
| Microsoft.AspNetCore.HostFiltering  | Hosts permitidos. |
| Microsoft.AspNetCore.Hosting        | Cuánto tiempo tardaron en completarse las solicitudes HTTP y a qué hora comenzaron. Qué ensamblados de inicio de hospedaje se cargaron. |
| Microsoft.AspNetCore.Mvc            | Diagnósticos de MVC y Razor. Enlace de modelos, ejecución de filtros, compilación de vistas y selección de acciones. |
| Microsoft.AspNetCore.Routing        | Información de coincidencia de ruta. |
| Microsoft.AspNetCore.Server         | Inicio y detención de conexión y mantener las respuestas activas. Información de certificado HTTPS. |
| Microsoft.AspNetCore.StaticFiles    | Archivos servidos. |
| Microsoft.EntityFrameworkCore       | Diagnósticos generales de Entity Framework Core. Actividad y la configuración de bases de datos, detección de cambios y migraciones. |

## <a name="log-scopes"></a>Ámbitos de registro

 Un *ámbito* puede agrupar un conjunto de operaciones lógicas. Esta agrupación se puede utilizar para adjuntar los mismos datos para cada registro que se crea como parte de un conjunto. Por ejemplo, cada registro creado como parte del procesamiento de una transacción puede incluir el identificador de dicha transacción.

Un ámbito es un tipo `IDisposable` devuelto por el método <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> y se conserva hasta que se elimina. Use un ámbito encapsulando las llamadas de registrador en un bloque `using`:

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

El código siguiente permite ámbitos para el proveedor de la consola:

*Program.cs*:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

> [!NOTE]
> Es necesario configurar la opción del registrador de consola `IncludeScopes` para habilitar el registro basado en el ámbito.
>
> Para obtener información sobre la configuración, consulte la sección [Configuración](#configuration).

Cada mensaje de registro incluye la información de ámbito:

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a>Proveedores de registro integrados

ASP.NET Core incluye los proveedores siguientes:

* [Consola](#console-provider)
* [Depurar](#debug-provider)
* [EventSource](#event-source-provider)
* [EventLog](#windows-eventlog-provider)
* [TraceSource](#tracesource-provider)
* [AzureAppServicesFile](#azure-app-service-provider)
* [AzureAppServicesBlob](#azure-app-service-provider)
* [ApplicationInsights](#azure-application-insights-trace-logging)

Para obtener información sobre stdout y el registro de depuración con el módulo ASP.NET Core, consulte <xref:test/troubleshoot-azure-iis> y <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.

### <a name="console-provider"></a>Proveedor de la consola

El paquete de proveedor [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) envía la salida del registro a la consola. 

```csharp
logging.AddConsole();
```

Para ver una salida de registro de la consola, abra un símbolo del sistema en la carpeta del proyecto y ejecute este comando:

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a>Proveedor de depuración

El paquete de proveedor [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) escribe la salida del registro mediante la clase [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) (llamadas a métodos `Debug.WriteLine`).

En Linux, este proveedor escribe registros en */var/log/message*.

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a>Proveedor de origen del evento

El paquete del proveedor [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) escribe en una multiplataforma de origen del evento con el nombre `Microsoft-Extensions-Logging`. En Windows, el proveedor utiliza [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).

```csharp
logging.AddEventSourceLogger();
```

El proveedor de origen del evento se agrega automáticamente cuando se llama a `CreateDefaultBuilder` para compilar el host.

Use la [utilidad PerfView](https://github.com/Microsoft/perfview) para recopilar y ver los registros. Hay otras herramientas para ver los registros ETW, pero PerfView proporciona la mejor experiencia para trabajar con los eventos ETW emitidos por ASP.NET Core.

Para configurar PerfView para la recopilación de eventos registrados por este proveedor, agregue la cadena `*Microsoft-Extensions-Logging` a la lista **Proveedores adicionales**. (No olvide el asterisco al principio de la cadena).

![Proveedores adicionales de Perfview](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a>Proveedor EventLog de Windows

El paquete de proveedor [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) envía la salida del registro al Registro de eventos de Windows.

```csharp
logging.AddEventLog();
```

Las [sobrecargas de AddEventLog](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) permiten pasar <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>. Si es `null` o no se especifica, se usa la siguiente configuración predeterminada:

* `LogName`: "Aplicación"
* `SourceName`: "Entorno de ejecución .NET"
* `MachineName`: equipo local

Los eventos se registran para el [Nivel de advertencia y superior](#log-level). Para registrar eventos inferiores a `Warning`, establezca el nivel de registro de forma explícita. Por ejemplo, agregue lo siguiente al archivo *appsettings.json*:

```json
"EventLog": {
  "LogLevel": {
    "Default": "Information"
  }
}
```

### <a name="tracesource-provider"></a>Proveedor TraceSource

El paquete de proveedor [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) usa las bibliotecas y proveedores de <xref:System.Diagnostics.TraceSource>.

```csharp
logging.AddTraceSource(sourceSwitchName);
```

[Las sobrecargas de AddTraceSource](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) permiten pasar un modificador de origen y un agente de escucha de seguimiento.

Para usar este proveedor, una aplicación debe ejecutarse en .NET Framework (en lugar de .NET Core). El proveedor puede enrutar mensajes a una variedad de [agentes de escucha](/dotnet/framework/debug-trace-profile/trace-listeners), como <xref:System.Diagnostics.TextWriterTraceListener> que se usa en la aplicación de ejemplo.

### <a name="azure-app-service-provider"></a>Proveedor Azure App Service

El paquete de proveedor [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) escribe los registros en archivos de texto en el sistema de archivos de una aplicación de Azure App Service y en [Blob Storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) en una cuenta de Azure Storage.

```csharp
logging.AddAzureWebAppDiagnostics();
```

El paquete de proveedor no está incluido en el [metapaquete Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app). Si el destino es .NET Framework o hace referencia al metapaquete `Microsoft.AspNetCore.App`, agregue el paquete del proveedor al proyecto. 

Una sobrecarga <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> permite pasar <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>. El objeto de configuración puede invalidar la configuración predeterminada, como la plantilla de salida de registro, el nombre de blob y el límite de tamaño de archivo. (La *plantilla salida* es una plantilla de mensaje que se aplica a todos los registros además de que se proporciona con una llamada de método `ILogger`).

Al realizar una implementación en una aplicación de App Service, esta respeta la configuración de la sección [Registros de App Service](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) de la página **App Service** de Azure Portal. Cuando se actualiza la configuración siguiente, los cambios se aplican de inmediato sin necesidad de reiniciar ni de volver a implementar la aplicación.

* **Registro de la aplicación (sistema de archivos)**
* **Registro de la aplicación (blob)**

La ubicación predeterminada de los archivos de registro es la carpeta *D:\\home\\LogFiles\\Application* y el nombre de archivo predeterminado es *diagnostics-aaaammdd.txt*. El límite de tamaño de archivo predeterminado es 10 MB, y el número máximo predeterminado de archivos que se conservan es 2. El nombre de blob predeterminado es *{nombre-de-la-aplicación}{marca de tiempo}/aaaa/mm/dd/hh/{guid}-applicationLog.txt*.

El proveedor solo funciona cuando el proyecto se ejecuta en el entorno de Azure. No tiene ningún efecto cuando el proyecto se ejecuta de manera local (no escribe en los archivos locales ni en el almacenamiento de desarrollo local de blobs).

#### <a name="azure-log-streaming"></a>Secuencias de registro de Azure

Las secuencias de registro de Azure permiten ver la actividad de registro en tiempo real desde:

* El servidor de aplicaciones
* El servidor web
* Error del seguimiento de solicitudes

Para configurar las secuencias de registro de Azure:

* Navegue hasta la página **Registros de App Service** desde la página de portal de la aplicación.
* Establezca **Registro de la aplicación (sistema de archivos)** en **Activado**.
* Elija el **Nivel** de registro. Este valor solo se aplica a las secuencias de registro de Azure, no a otros proveedores de registro de la aplicación.

Navegue hasta la página **Secuencia de registro** para consultar los mensajes de la aplicación. La aplicación los registra a través de la interfaz `ILogger`.

### <a name="azure-application-insights-trace-logging"></a>Registro de seguimiento de Azure Application Insights

El proveedor de paquete [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) escribe los registros en Azure Application Insights. Application Insights es un servicio que supervisa una aplicación web y proporciona herramientas para consultar y analizar los datos de telemetría. Si usa este proveedor, puede consultar y analizar los registros mediante las herramientas de Application Insights.

El proveedor de registro se incluye como dependencia de [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), que es el paquete que proporciona toda la telemetría disponible para ASP.NET Core. Si usa este paquete, no tiene que instalar el proveedor de paquete.

No use el paquete [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web)&mdash;que es para ASP.NET 4.x.

Para obtener más información, vea los siguientes recursos:

* [Información general de Application Insights](/azure/application-insights/app-insights-overview)
* [Application Insights para aplicaciones de ASP.NET Core](/azure/azure-monitor/app/asp-net-core): comience aquí si quiere implementar la variedad completa de telemetría de Application Insights junto con el registro.
* [ApplicationInsightsLoggerProvider para los registros de .NET Core ILogger](/azure/azure-monitor/app/ilogger): comience aquí si quiere implementar el proveedor de registro sin el resto de la telemetría de Application Insights.
* [Adaptadores de registro de Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs)
* [Instalación, configuración e inicialización del SDK de Application Insights](/learn/modules/instrument-web-app-code-with-application-insights): tutorial interactivo en el sitio de Microsoft Learn.

## <a name="third-party-logging-providers"></a>Proveedores de registro de terceros

Plataformas de registro de terceros que funcionan con ASP.NET Core:

* [elmah.io](https://elmah.io/) ([repositorio de GitHub](https://github.com/elmahio/Elmah.Io.Extensions.Logging))
* [Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([repositorio de GitHub](https://github.com/mattwcole/gelf-extensions-logging))
* [JSNLog](https://jsnlog.com/) ([repositorio de GitHub](https://github.com/mperdeck/jsnlog))
* [KissLog.net](https://kisslog.net/) ([repositorio de GitHub](https://github.com/catalingavan/KissLog-net))
* [Log4Net](https://logging.apache.org/log4net/) ([repositorio de GitHub](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))
* [Loggr](https://loggr.net/) ([repositorio de GitHub](https://github.com/imobile3/Loggr.Extensions.Logging))
* [NLog](https://nlog-project.org/) ([repositorio de GitHub](https://github.com/NLog/NLog.Extensions.Logging))
* [Sentry](https://sentry.io/welcome/) ([repositorio de GitHub](https://github.com/getsentry/sentry-dotnet))
* [Serilog](https://serilog.net/) ([repositorio de GitHub](https://github.com/serilog/serilog-aspnetcore))
* [Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([repositorio de GitHub](https://github.com/googleapis/google-cloud-dotnet))

Algunas plataformas de terceros pueden realizar [registro semántico, también conocido como registro estructurado](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

El uso de una plataforma de terceros es similar al uso de uno de los proveedores integrados:

1. Agregue un paquete NuGet al proyecto.
1. Llame a un método de extensión `ILoggerFactory` proporcionado por el marco de registro.

Para más información, vea la documentación de cada proveedor. Microsoft no admite los proveedores de registro de terceros.

## <a name="additional-resources"></a>Recursos adicionales

* <xref:fundamentals/logging/loggermessage>

::: moniker-end
