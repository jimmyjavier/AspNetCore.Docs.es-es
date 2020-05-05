---
title: Registro y diagnósticos en ASP.NET CoreSignalR
author: anurse
description: Obtenga información sobre cómo recopilar diagnósticos SignalR desde la aplicación ASP.net Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: signalr
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/diagnostics
ms.openlocfilehash: 5fda458c2418c3570d55d551ce5144730afd7f85
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767231"
---
# <a name="logging-and-diagnostics-in-aspnet-core-signalr"></a>Registro y diagnóstico en ASP.NET Core Signalr

Por [Andrew Stanton-enfermera](https://twitter.com/anurse)

En este artículo se proporcionan instrucciones para recopilar diagnósticos de la aplicación ASP.NET Core Signalr para ayudar a solucionar problemas.

## <a name="server-side-logging"></a>Registro del lado servidor

> [!WARNING]
> Los registros del lado servidor pueden contener información confidencial de la aplicación. No publique **nunca** los registros sin procesar de las aplicaciones de producción en foros públicos como github.

Como Signalr forma parte de ASP.NET Core, utiliza el sistema de registro de ASP.NET Core. En la configuración predeterminada, Signalr registra muy poca información, pero esto puede configurarse. Consulte la documentación sobre el [registro de ASP.net Core](xref:fundamentals/logging/index#configuration) para obtener más información sobre la configuración del registro de ASP.net Core.

Signalr usa dos categorías de registrador:

* `Microsoft.AspNetCore.SignalR`&ndash; para los registros relacionados con los protocolos del concentrador, activar los concentradores, invocar métodos y otras actividades relacionadas con el concentrador.
* `Microsoft.AspNetCore.Http.Connections`&ndash; para los registros relacionados con los transportes, como WebSockets, los eventos de sondeo largo y el servidor y la infraestructura de signalr de bajo nivel.

Para habilitar registros detallados de Signalr, configure los dos prefijos `Debug` anteriores en el nivel del archivo *appSettings. JSON* agregando los siguientes elementos `LogLevel` a la subsección `Logging`en:

[!code-json[](diagnostics/logging-config.json?highlight=7-8)]

También puede configurarlo en el `CreateWebHostBuilder` código del método:

[!code-csharp[](diagnostics/logging-config-code.cs?highlight=5-6)]

Si no está usando la configuración basada en JSON, establezca los siguientes valores de configuración en el sistema de configuración:

* `Logging:LogLevel:Microsoft.AspNetCore.SignalR` = `Debug`
* `Logging:LogLevel:Microsoft.AspNetCore.Http.Connections` = `Debug`

Consulte la documentación del sistema de configuración para determinar cómo especificar valores de configuración anidados. Por ejemplo, al usar variables de entorno, `_` se usan dos caracteres en lugar de `:` (por ejemplo, `Logging__LogLevel__Microsoft.AspNetCore.SignalR`).

Se recomienda usar el `Debug` nivel al recopilar diagnósticos más detallados para la aplicación. El `Trace` nivel genera diagnósticos de nivel muy bajo y rara vez es necesario para diagnosticar problemas en la aplicación.

## <a name="access-server-side-logs"></a>Acceder a los registros del lado servidor

La forma de acceder a los registros del lado servidor depende del entorno en el que se está ejecutando.

### <a name="as-a-console-app-outside-iis"></a>Como aplicación de consola fuera de IIS

Si está ejecutando en una aplicación de consola, el [registrador de consola](xref:fundamentals/logging/index#console-provider) debe estar habilitado de forma predeterminada. Los registros de signalr aparecerán en la consola de.

### <a name="within-iis-express-from-visual-studio"></a>Dentro de IIS Express desde Visual Studio

Visual Studio muestra la salida del registro en la ventana de **salida** . Seleccione la opción desplegable **servidor Web de ASP.net Core** .

### <a name="azure-app-service"></a>Azure App Service

Habilite la opción de **registro de aplicaciones (sistema de archivos)** en la sección **registros de diagnóstico** del portal de Azure App Service y configure el **nivel** en `Verbose`. Los registros deben estar disponibles en el servicio de **streaming de registros** y en los registros del sistema de archivos del App Service. Para más información, consulte [Azure log streaming](xref:fundamentals/logging/index#azure-log-streaming).

### <a name="other-environments"></a>Otros entornos

Si la aplicación se implementa en otro entorno (por ejemplo, Docker, Kubernetes o servicio de Windows), consulte <xref:fundamentals/logging/index> para obtener más información sobre cómo configurar los proveedores de registro adecuados para el entorno.

## <a name="javascript-client-logging"></a>Registro de cliente JavaScript

> [!WARNING]
> Los registros del lado cliente pueden contener información confidencial de la aplicación. No publique **nunca** los registros sin procesar de las aplicaciones de producción en foros públicos como github.

Al utilizar el cliente de JavaScript, puede configurar las opciones de registro `configureLogging` mediante el `HubConnectionBuilder`método de:

[!code-javascript[](diagnostics/logging-config-js.js?highlight=3)]

Para deshabilitar el registro por `signalR.LogLevel.None` completo, `configureLogging` especifique en el método.

En la tabla siguiente se muestran los niveles de registro disponibles para el cliente JavaScript. Al establecer el nivel de registro en uno de estos valores, se habilita el registro en ese nivel y en todos los niveles situados por encima de él en la tabla.

| Nivel | Descripción |
| ----- | ----------- |
| `None` | No se registra ningún mensaje. |
| `Critical` | Mensajes que indican un error en toda la aplicación. |
| `Error` | Mensajes que indican un error en la operación actual. |
| `Warning` | Mensajes que indican un problema no grave. |
| `Information` | Mensajes informativos. |
| `Debug` | Mensajes de diagnóstico útiles para la depuración. |
| `Trace` | Mensajes de diagnóstico muy detallados diseñados para diagnosticar problemas específicos. |

Una vez que haya configurado el nivel de detalle, los registros se escribirán en la consola del explorador (o la salida estándar en una aplicación de NodeJS).

Si desea enviar registros a un sistema de registro personalizado, puede proporcionar un objeto de JavaScript que implemente `ILogger` la interfaz. El único método que debe implementarse es `log`, que toma el nivel del evento y el mensaje asociado al evento. Por ejemplo:

[!code-typescript[](diagnostics/custom-logger.ts?highlight=3-7,13)]

## <a name="net-client-logging"></a>Registro de cliente .NET

> [!WARNING]
> Los registros del lado cliente pueden contener información confidencial de la aplicación. No publique **nunca** los registros sin procesar de las aplicaciones de producción en foros públicos como github.

Para obtener los registros del cliente .NET, puede usar el `ConfigureLogging` método en. `HubConnectionBuilder` Esto funciona de la misma manera que `ConfigureLogging` el método `WebHostBuilder` en `HostBuilder`y. Puede configurar los mismos proveedores de registro que utiliza en ASP.NET Core. Sin embargo, tiene que instalar y habilitar manualmente los paquetes de NuGet para los proveedores de registro individuales.

### <a name="console-logging"></a>Registro de consolas

Para habilitar el registro de la consola, agregue el paquete [Microsoft. Extensions. Logging. Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) . A continuación, use `AddConsole` el método para configurar el registrador de la consola:

[!code-csharp[](diagnostics/net-client-console-log.cs?highlight=6)]

### <a name="debug-output-window-logging"></a>Registro de la ventana de salida de depuración

También puede configurar los registros para ir a la ventana de **salida** de Visual Studio. Instale el paquete [Microsoft. Extensions. Logging. Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) y use `AddDebug` el método:

[!code-csharp[](diagnostics/net-client-debug-log.cs?highlight=6)]

### <a name="other-logging-providers"></a>Otros proveedores de registro

SignalRadmite otros proveedores de registro, como Serilog, SEQ, NLog o cualquier otro sistema de registro que se `Microsoft.Extensions.Logging`integre con. Si el sistema de registro proporciona `ILoggerProvider`un, puede registrarlo con `AddProvider`:

[!code-csharp[](diagnostics/net-client-custom-log.cs?highlight=6)]

### <a name="control-verbosity"></a>Nivel de detalle del control

Si está registrando desde otros lugares de la aplicación, cambiar el nivel predeterminado a `Debug` puede ser demasiado detallado. Puede usar un filtro para configurar el nivel de registro de SignalR los registros. Esto puede hacerse en el código, de la misma manera que en el servidor:

[!code-csharp[Controlling verbosity in .NET client](diagnostics/logging-config-client-code.cs?highlight=9-10)]

## <a name="network-traces"></a>Seguimientos de red

> [!WARNING]
> Un seguimiento de red contiene todo el contenido de todos los mensajes enviados por la aplicación. No exponga **nunca** los seguimientos de red sin procesar de las aplicaciones de producción en foros públicos como github.

Si se produce un problema, un seguimiento de red a veces puede proporcionar mucha información útil. Esto es especialmente útil si va a archivar un problema en nuestro seguimiento de problemas.

## <a name="collect-a-network-trace-with-fiddler-preferred-option"></a>Recopilar un seguimiento de red con Fiddler (opción preferida)

Este método funciona para todas las aplicaciones.

Fiddler es una herramienta muy eficaz para la recopilación de seguimientos HTTP. Instálelo desde [Telerik.com/Fiddler](https://www.telerik.com/fiddler), inícielo y, a continuación, ejecute la aplicación y reproduzca el problema. Fiddler está disponible para Windows y hay versiones beta para macOS y Linux.

Si se conecta mediante HTTPS, hay algunos pasos adicionales para asegurarse de que Fiddler puede descifrar el tráfico HTTPS. Para obtener más información, consulte la [documentación de Fiddler](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).

Una vez que haya recopilado el seguimiento, puede exportar el seguimiento eligiendo **archivo** > **Guardar** > **todas las sesiones** en la barra de menús.

![Exportar todas las sesiones de Fiddler](diagnostics/fiddler-export.png)

## <a name="collect-a-network-trace-with-tcpdump-macos-and-linux-only"></a>Recopilación de un seguimiento de red con tcpdump (solo macOS y Linux)

Este método funciona para todas las aplicaciones.

Puede recopilar seguimientos TCP sin formato mediante tcpdump ejecutando el siguiente comando desde un shell de comandos. Es posible que deba ser `root` o agregar el prefijo `sudo` al comando si obtiene un error de permisos:

```console
tcpdump -i [interface] -w trace.pcap
```

Reemplazar `[interface]` por la interfaz de red en la que desea realizar la captura. Normalmente, se trata de algo `/dev/eth0` parecido a (para la interfaz Ethernet estándar `/dev/lo0` ) o (para el tráfico localhost). Para obtener más información, consulte `tcpdump` la página Man en el sistema host.

## <a name="collect-a-network-trace-in-the-browser"></a>Recopilar un seguimiento de red en el explorador

Este método solo funciona para aplicaciones basadas en explorador.

La mayoría del explorador Herramientas de desarrollo tener una pestaña "red" que le permita capturar la actividad de red entre el explorador y el servidor. Sin embargo, estos seguimientos no incluyen mensajes de eventos WebSocket y enviados por el servidor. Si usa esos transportes, el mejor enfoque es usar una herramienta como Fiddler o TcpDump (descrita a continuación).

### <a name="microsoft-edge-and-internet-explorer"></a>Microsoft Edge e Internet Explorer

(Las instrucciones son las mismas para los perímetros e Internet Explorer)

1. Presione F12 para abrir las herramientas de desarrollo
2. Haga clic en la pestaña red.
3. Actualizar la página (si es necesario) y reproducir el problema
4. Haga clic en el icono guardar en la barra de herramientas para exportar el seguimiento como un archivo "HAR":

![El icono guardar en la pestaña red de herramientas de desarrollo de Microsoft Edge](diagnostics/ie-edge-har-export.png)

### <a name="google-chrome"></a>Google Chrome

1. Presione F12 para abrir las herramientas de desarrollo
2. Haga clic en la pestaña red.
3. Actualizar la página (si es necesario) y reproducir el problema
4. Haga clic con el botón derecho en cualquier parte de la lista de solicitudes y elija "guardar como HAR with Content":

![Opción "guardar como HAR con contenido" en la pestaña red de herramientas de desarrollo de Google Chrome](diagnostics/chrome-har-export.png)

### <a name="mozilla-firefox"></a>Mozilla Firefox

1. Presione F12 para abrir las herramientas de desarrollo
2. Haga clic en la pestaña red.
3. Actualizar la página (si es necesario) y reproducir el problema
4. Haga clic con el botón derecho en cualquier parte de la lista de solicitudes y elija "guardar todo como HAR"

![Opción "guardar todo como HAR" en Mozilla Firefox herramientas de desarrollo pestaña red](diagnostics/firefox-har-export.png)

## <a name="attach-diagnostics-files-to-github-issues"></a>Adjuntar archivos de diagnóstico a problemas de GitHub

Puede adjuntar archivos de diagnóstico a problemas de GitHub cambiando su nombre para que `.txt` tengan una extensión y arrastrándolos y colocándolos en el problema.

> [!NOTE]
> No pegue el contenido de los archivos de registro o los seguimientos de red en un problema de GitHub. Estos registros y seguimientos pueden ser bastante grandes y GitHub normalmente los trunca.

![Arrastrar archivos de registro a un problema de GitHub](diagnostics/attaching-diagnostics-files.png)

## <a name="additional-resources"></a>Recursos adicionales

* <xref:signalr/configuration>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
