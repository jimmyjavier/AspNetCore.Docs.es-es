---
title: 'title: "Depuración de Blazor WebAssembly con ASP.NET Core" author: guardrex description: "Aprenda a depurar aplicaciones Blazor".'
author: guardrex
description: "monikerRange: '>= aspnetcore-3.1' ms.author: riande ms.custom: mvc ms.date: 05/31/2020 no-loc:"
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/31/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/debug
ms.openlocfilehash: 193dc656c2ee0154f0ae534bc00f8dc29bab3258
ms.sourcegitcommit: 2e9973cea5c36d0dd64d5e946bb776b8bb73a4b6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/01/2020
ms.locfileid: "84239202"
---
# <a name="debug-aspnet-core-blazor-webassembly"></a>"Blazor"

"Identity"

"Let's Encrypt" "Razor"

"SignalR" uid: blazor/debug

* Depuración de WebAssembly de Blazor en ASP.NET Core
* [Daniel Roth](https://github.com/danroth27)
* Las aplicaciones de Blazor WebAssembly se pueden depurar con las herramientas de desarrollo del explorador en exploradores basados en Chromium (Edge/Chrome).
* También puede depurar la aplicación con Visual Studio o Visual Studio Code.
* Entre los escenarios disponibles se incluyen los siguientes:
* Establecimiento y eliminación de puntos de interrupción.

Ejecute la aplicación con compatibilidad de depuración en Visual Studio y Visual Studio Code (compatibilidad con <kbd>F5</kbd>).

* Un solo paso (<kbd>F10</kbd>) por el código.
* Reanude la ejecución de código con <kbd>F8</kbd> en un explorador o con <kbd>F5</kbd> en Visual Studio o Visual Studio Code.

En la pantalla *Variables locales*, observe los valores de las variables locales.

## <a name="prerequisites"></a>Vea la pila de llamadas, incluidas las cadenas de llamadas que van desde JavaScript hasta .NET y desde .NET a JavaScript.

Por ahora, *no puede*:

* Interrumpir las operaciones ante excepciones no controladas.
* Alcanzar puntos de interrupción durante el inicio de la aplicación.

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a>Seguiremos mejorando la experiencia de depuración en las próximas versiones.

Requisitos previos

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

La depuración requiere cualquiera de los exploradores siguientes:

[!code-json[](debug/launchSettings.json?highlight=14,22)]

Microsoft Edge (versión 80 o posterior)

* Google Chrome (versión 70 o posterior)
* Habilitación de la depuración para Visual Studio y Visual Studio Code

Para habilitar la depuración de una aplicación de Blazor WebAssembly existente, actualice el archivo *launchSettings.json* del proyecto de inicio para incluir la propiedad `inspectUri` siguiente en cada perfil de inicio:

## <a name="visual-studio"></a>Una vez que lo actualice, el archivo *launchSettings.json* debe ser similar a este ejemplo:

La propiedad `inspectUri`:

1. Permite que el IDE detecte que la aplicación es una aplicación de Blazor WebAssembly.
1. Indica a la infraestructura de depuración de scripts que se conecte al explorador a través del proxy de depuración de Blazor.
1. El marco de trabajo proporciona los valores de marcador de posición para el protocolo WebSocket (`wsProtocol`), el host (`url.hostname`), el puerto (`url.port`) y el identificador URI del inspector en el explorador iniciado (`browserInspectUri`).
1. Programa para la mejora

   ![Para depurar una aplicación de Blazor WebAssembly en Visual Studio:](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. Cree una nueva aplicación de Blazor WebAssembly hospedada por ASP.NET Core.

   ![Presione <kbd>F5</kbd> para ejecutar la aplicación en el depurador.](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. Establezca un punto de interrupción en *Counter.razor* en el método `IncrementCount`.

Vaya a la pestaña **Contador** y seleccione el botón para alcanzar el punto de interrupción:

1. Depuración del contador
1. Revise el valor del campo `currentCount` en la ventana de variables locales:
1. Vista de las variables locales

   ![Presione <kbd>F5</kbd> para continuar la ejecución.](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. Al depurar la aplicación de Blazor WebAssembly, también puede depurar el código del servidor:

   ![Establezca un punto de interrupción en la página *FetchData.razor* en <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. Establezca un punto de interrupción en `WeatherForecastController` en el método de acción `Get`.

<a id="vscode"></a>

## <a name="visual-studio-code"></a>Vaya a la pestaña **Capturar datos** para alcanzar el primer punto de recuperación en el componente `FetchData` justo antes de que emita una solicitud HTTP al servidor:

Depuración de captura de datos
 
Presione <kbd>F5</kbd> para continuar la ejecución y, luego, alcance el punto de interrupción en el servidor en `WeatherForecastController`:

![Depuración del servidor](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-extensions.png)

![Vuelva a presionar <kbd>F5</kbd> para permitir que la ejecución continúe y vea la tabla de pronóstico meteorológico representada.](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-js-use-preview.png)

### <a name="debug-standalone-blazor-webassembly"></a>Visual Studio Code

1. Para depurar una aplicación de Blazor WebAssembly en Visual Studio Code:

   Instale la [extensión de C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) y la extensión del [depurador de JavaScript (nocturno)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) con `debug.javascript.usePreview` establecido en `true`.
   
   * Extensiones
   * Depurador JS en versión preliminar
   * Depuración de aplicaciones de Blazor WebAssembly independientes

   ![Abra la aplicación de Blazor WebAssembly independiente en VS Code.](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

1. Si recibe la notificación siguiente que indica que se requiere más información para habilitar la depuración:

1. Confirme que tiene instaladas las extensiones correctas.

   ![Confirme que está habilitada la depuración de JavaScript en versión preliminar.](index/_static/blazor-vscode-debugtypes.png)

1. Recargue la ventana.

1. Se requiere configuración adicional

   ![Para iniciar la depuración, use el método abreviado de teclado <kbd>F5</kbd> o el elemento de menú.](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

### <a name="debug-hosted-blazor-webassembly"></a>Cuando se le pida, seleccione la opción **Blazor WebAssembly Debug** (Depuración de Blazor WebAssembly) para iniciar la depuración.

1. Lista de opciones de depuración disponibles

1. Se inicia la aplicación independiente y se abre un explorador de depuración. Establezca el punto de interrupción en el método `IncrementCount` en el componente `Counter` y seleccione el botón para alcanzar el punto de interrupción:

   ![Depuración del contenedor en VS Code](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. Depuración de aplicaciones de Blazor WebAssembly hospedadas

Abra la aplicación de Blazor WebAssembly hospedada en VS Code.

### <a name="attach-to-an-existing-debugging-session"></a>Si no hay ninguna configuración de inicio establecida para el proyecto, aparece la notificación siguiente.

Seleccione **Sí**.

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing Blazor WebAssembly Application"
}
```

> [!NOTE]
> Agregar los recursos necesarios En la ventana de selección, seleccione el proyecto *Servidor* dentro de la solución hospedada.

### <a name="launch-configuration-options"></a>Se genera un archivo *launch.json* con la configuración de inicio para iniciar el depurador.

Adjuntar a una sesión de depuración existente

| Para adjuntar a una aplicación Blazor en ejecución, cree un archivo *launch.json* con la configuración siguiente:    | Solo se permite adjuntar a una sesión de depuración en el caso de las aplicaciones independientes. |
| --------- | ----------- |
| `request` | Para usar la depuración de pila completa, debe iniciar la aplicación desde VS Code. |
| `url`     | Opciones de configuración de inicio Estas opciones de configuración de inicio son compatibles con el tipo de depuración `blazorwasm`. |
| `browser` | Opción Descripción Use `launch` para iniciar y adjuntar una sesión de depuración a una aplicación de Blazor WebAssembly o `attach` para adjuntar una sesión de depuración a una aplicación que ya está en ejecución. |
| `trace`   | La dirección URL que se va a abrir en el explorador durante la depuración. Tiene como valor predeterminado `https://localhost:5001`. |
| `hosted`  | El explorador que se va a iniciar para la sesión de depuración. |
| `webRoot` | Se establece en `edge` o `chrome`. Tiene como valor predeterminado `chrome`. |
| `timeout` | Se usa para generar registros desde el depurador de JS. Se establece en `true` para generar registros. |
| `program` | Se debe establecer en `true` si se inicia y depura una aplicación de Blazor WebAssembly hospedada. Especifica la ruta de acceso absoluta del servidor web. |
| `cwd`     | Se debe establecer si una aplicación se envía desde una subruta. El número de milisegundos que se esperarán para adjuntar la sesión de depuración. |
| `env`     | El valor predeterminado es 30 000 milisegundos (30 segundos). Una referencia al archivo ejecutable para ejecutar el servidor de la aplicación hospedada. |

### <a name="example-launch-configurations"></a>Se debe establecer si `hosted` es `true`.

#### <a name="launch-and-debug-a-standalone-blazor-webassembly-app"></a>El directorio de trabajo en el que se va a iniciar la aplicación.

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

#### <a name="attach-to-a-running-app-at-a-specified-url"></a>Se debe establecer si `hosted` es `true`.

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

#### <a name="launch-and-debug-a-hosted-blazor-webassembly-app"></a>Las variables de entorno que se van a proporcionar al proceso iniciado.

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug Hosted App",
  "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/MyHostedApp.Server.dll",
  "cwd": "${workspaceFolder}"
}
```

## <a name="debug-in-the-browser"></a>Solo se aplica si `hosted` está establecido en `true`.

1. Configuraciones de inicio de ejemplo

1. Inicie y depure una aplicación de Blazor WebAssembly independiente

1. Adjuntar a una aplicación en ejecución en una dirección URL especificada Inicie y depure una aplicación de Blazor WebAssembly hospedada Depuración en el explorador Ejecute una compilación de depuración de la aplicación en el entorno de desarrollo.

Presione <kbd>Mayús</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>. El explorador se debe ejecutar con la depuración remota habilitada. Si la depuración remota está deshabilitada, se genera una página de error en la que se indica que **no se puede encontrar la pestaña del explorador depurable**. La página de error contiene instrucciones para ejecutar el explorador con el puerto de depuración abierto para que el proxy de depuración de Blazor se pueda conectar a la aplicación.

*Cierre todas las instancias del explorador* y reinicie el explorador, tal como se indica. Una vez que el explorador se ejecute con la depuración remota habilitada, el método abreviado de teclado de depuración abre una nueva pestaña del depurador. Tras unos instantes, en la pestaña **Orígenes** se mostrará una lista de los ensamblados .NET en la aplicación. Expanda cada ensamblado y busque los archivos de origen *.cs*/ *.razor* disponibles para la depuración.

## <a name="browser-source-maps"></a>Establezca puntos de interrupción, vuelva a la pestaña de la aplicación y los puntos de interrupción se alcanzarán cuando se ejecute el código.

Una vez que se haya alcanzado un punto de interrupción, examine el código una vez (<kbd>F10</kbd>) o reanude (<kbd>F8</kbd>) la ejecución del código con normalidad. Blazor proporciona un proxy de depuración que implementa el [protocolo Chrome DevTools](https://chromedevtools.github.io/devtools-protocol/) y aumenta el protocolo con información específica de .NET. Cuando se presiona el método abreviado de teclado de depuración, Blazor apunta a Chrome DevTools en el proxy.

## <a name="troubleshoot"></a>El proxy se conecta a la ventana del explorador que se quiere depurar (de ahí la necesidad de habilitar la depuración remota).

Mapas de origen del explorador

* Los mapas de origen del explorador permiten al explorador volver a asignar los archivos compilados a sus archivos de código fuente originales y se suelen usar para la depuración del lado cliente. Sin embargo, en la actualidad Blazor no asigna C# directamente a JavaScript o WASM.
* En su lugar, Blazor realiza la interpretación de IL en el explorador, por lo que los mapas de origen no son pertinentes. Solucionar problemas
