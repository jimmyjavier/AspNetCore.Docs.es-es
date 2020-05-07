---
title: Depuración de WebAssembly de Blazor en ASP.NET Core
author: guardrex
description: Obtenga información sobre cómo depurar aplicaciones Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/16/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/debug
ms.openlocfilehash: 9acbb8e7b122a8d527d16ce33af01c2e7e7608bf
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767543"
---
# <a name="debug-aspnet-core-blazor-webassembly"></a>Depuración de WebAssembly de Blazor en ASP.NET Core

[Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Las aplicaciones de Blazor WebAssembly se pueden depurar con las herramientas de desarrollo del explorador en exploradores basados en Chromium (Edge/Chrome).  También puede depurar la aplicación con Visual Studio o Visual Studio Code.

Entre los escenarios disponibles se incluyen los siguientes:

* Establecimiento y eliminación de puntos de interrupción.
* Ejecute la aplicación con compatibilidad de depuración en Visual Studio y Visual Studio Code (compatibilidad con <kbd>F5</kbd>).
* Un solo paso (<kbd>F10</kbd>) por el código.
* Reanude la ejecución de código con <kbd>F8</kbd> en un explorador o con <kbd>F5</kbd> en Visual Studio o Visual Studio Code.
* En la pantalla *Variables locales*, observe los valores de las variables locales.
* Vea la pila de llamadas, incluidas las cadenas de llamadas que van desde JavaScript hasta .NET y desde .NET a JavaScript.

Por ahora, *no puede*:

* Inspeccionar las matrices.
* Mover el puntero para inspeccionar a los miembros.
* Depurar paso a paso por instrucciones o para salir del código administrado.
* Tener compatibilidad total para inspeccionar tipos de valor.
* Interrumpir las operaciones ante excepciones no controladas.
* Alcanzar puntos de interrupción durante el inicio de la aplicación.
* Depurar una aplicación con un trabajo de servicio.

Seguiremos mejorando la experiencia de depuración en las próximas versiones.

## <a name="prerequisites"></a>Requisitos previos

La depuración requiere cualquiera de los exploradores siguientes:

* Microsoft Edge (versión 80 o posterior)
* Google Chrome (versión 70 o posterior)

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a>Habilitación de la depuración para Visual Studio y Visual Studio Code

La depuración está habilitada de manera automática para los proyectos nuevos que se crean con la plantilla de proyecto de WebAssembly de Blazor de ASP.NET Core 3.2 Preview 3 o posterior ([la versión actual es 3.2 Preview 4](xref:blazor/get-started)).

Para habilitar la depuración de una aplicación de Blazor WebAssembly existente, actualice el archivo *launchSettings.json* del proyecto de inicio para incluir la propiedad `inspectUri` siguiente en cada perfil de inicio:

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

Una vez que lo actualice, el archivo *launchSettings.json* debe ser similar a este ejemplo:

[!code-json[](debug/launchSettings.json?highlight=14,22)]

La propiedad `inspectUri`:

* Permite que el IDE detecte que la aplicación es una aplicación de Blazor WebAssembly.
* Indica a la infraestructura de depuración de scripts que se conecte al explorador a través del proxy de depuración de Blazor.

## <a name="visual-studio"></a>Programa para la mejora

Para depurar una aplicación de Blazor WebAssembly en Visual Studio:

1. Asegúrese de haber [instalado la versión preliminar más reciente de Visual Studio 2019 16.6](https://visualstudio.com/preview) (Preview 2 o posterior).
1. Cree una nueva aplicación de Blazor WebAssembly hospedada por ASP.NET Core.
1. Presione <kbd>F5</kbd> para ejecutar la aplicación en el depurador.
1. Establezca un punto de interrupción en *Counter.razor* en el método `IncrementCount`.
1. Vaya a la pestaña **Contador** y seleccione el botón para alcanzar el punto de interrupción:

   ![Depuración del contador](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. Revise el valor del campo `currentCount` en la ventana de variables locales:

   ![Vista de las variables locales](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. Presione <kbd>F5</kbd> para continuar la ejecución.

Al depurar la aplicación de Blazor WebAssembly, también puede depurar el código del servidor:

1. Establezca un punto de interrupción en la página *FetchData.razor* en `OnInitializedAsync`.
1. Establezca un punto de interrupción en `WeatherForecastController` en el método de acción `Get`.
1. Vaya a la pestaña **Capturar datos** para alcanzar el primer punto de recuperación en el componente `FetchData` justo antes de que emita una solicitud HTTP al servidor:

   ![Depuración de captura de datos](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. Presione <kbd>F5</kbd> para continuar la ejecución y, luego, alcance el punto de interrupción en el servidor en `WeatherForecastController`:

   ![Depuración del servidor](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. Vuelva a presionar <kbd>F5</kbd> para permitir que la ejecución continúe y vea la tabla de pronóstico meteorológico representada.

<a id="vscode"></a>

## <a name="visual-studio-code"></a>Visual Studio Code

Para depurar una aplicación de Blazor WebAssembly en Visual Studio Code:
 
1. Instale la [extensión de C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) y la extensión del [depurador de JavaScript (nocturno)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) con `debug.javascript.usePreview` establecido en `true`.

   ![Extensiones](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-extensions.png)

   ![Depurador JS en versión preliminar](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-js-use-preview.png)

1. Abra una aplicación de Blazor WebAssembly existente con la depuración habilitada.

   * Si recibe la notificación siguiente que indica que se requiere una configuración adicional para habilitar la depuración, confirme que tiene instaladas las extensiones correctas y habilitada la depuración de JavaScript en versión preliminar y, luego, recargue la ventana:

     ![Se requiere configuración adicional](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

   * Una notificación ofrece agregar los recursos necesarios a la aplicación para realizar la compilación y la depuración. Seleccione **Sí**:

     ![Agregar los recursos necesarios](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. Iniciar la aplicación en el depurador es un proceso de dos pasos:

   1\. **En primer lugar**, inicie la aplicación con la configuración de inicio **Inicio de .NET Core (independiente de Blazor)** .

   2\. **Una vez que la aplicación se inicia**, inicie el explorador con la configuración de inicio **Blazor WebAssembly de depuración de .NET Core en Chrome** (requiere Chrome). Para usar Edge en lugar de Chrome, cambie el `type` de la configuración de inicio en *.vscode/launch.json* de `pwa-chrome` a `pwa-msedge`.

1. Establezca el punto de interrupción en el método `IncrementCount` en el componente `Counter` y seleccione el botón para alcanzar el punto de interrupción:

   ![Depuración del contenedor en VS Code](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

## <a name="debug-in-the-browser"></a>Depuración en el explorador

1. Ejecute una compilación de depuración de la aplicación en el entorno de desarrollo.

1. Presione <kbd>Mayús</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.

1. El explorador se debe ejecutar con la depuración remota habilitada. Si la depuración remota está deshabilitada, se genera una página de error en la que se indica que **no se puede encontrar la pestaña del explorador depurable**. La página de error contiene instrucciones para ejecutar el explorador con el puerto de depuración abierto para que el proxy de depuración de Blazor se pueda conectar a la aplicación. *Cierre todas las instancias del explorador* y reinicie el explorador, tal como se indica.

Una vez que el explorador se ejecute con la depuración remota habilitada, el método abreviado de teclado de depuración abre una nueva pestaña del depurador. Tras unos instantes, en la pestaña **Orígenes** se mostrará una lista de los ensamblados .NET en la aplicación. Expanda cada ensamblado y busque los archivos de origen *.cs*/ *.razor* disponibles para la depuración. Establezca puntos de interrupción, vuelva a la pestaña de la aplicación y los puntos de interrupción se alcanzarán cuando se ejecute el código. Una vez que se haya alcanzado un punto de interrupción, examine el código una vez (<kbd>F10</kbd>) o reanude (<kbd>F8</kbd>) la ejecución del código con normalidad.

Blazor proporciona un proxy de depuración que implementa el [protocolo Chrome DevTools](https://chromedevtools.github.io/devtools-protocol/) y aumenta el protocolo con información específica de .NET. Cuando se presiona el método abreviado de teclado de depuración, Blazor apunta a Chrome DevTools en el proxy. El proxy se conecta a la ventana del explorador que se quiere depurar (de ahí la necesidad de habilitar la depuración remota).

## <a name="browser-source-maps"></a>Mapas de origen del explorador

Los mapas de origen del explorador permiten al explorador volver a asignar los archivos compilados a sus archivos de código fuente originales y se suelen usar para la depuración del lado cliente. Sin embargo, en la actualidad Blazor no asigna C# directamente a JavaScript o WASM. En su lugar, Blazor realiza la interpretación de IL en el explorador, por lo que los mapas de origen no son pertinentes.

## <a name="troubleshoot"></a>Solucionar problemas

Si se encuentra con errores, la sugerencia siguiente puede ser útil:

En la pestaña **Depurador**, abra las herramientas para desarrolladores en el explorador. En la consola, ejecute `localStorage.clear()` para quitar los puntos de interrupción.
