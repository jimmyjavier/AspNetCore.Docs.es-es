---
title: Introducción a ASP.NET Core Blazor
author: guardrex
description: Para empezar a usar Blazor, compile una aplicación Blazor con las herramientas que prefiera.
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
uid: blazor/get-started
ms.openlocfilehash: 63fee0b6a3152640a5483c2a682eec7d04742145
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/23/2020
ms.locfileid: "85243608"
---
# <a name="get-started-with-aspnet-core-blazor"></a>Introducción a ASP.NET Core Blazor

Por [Daniel Roth](https://github.com/danroth27) y [Luke Latham](https://github.com/guardrex)

Para empezar a trabajar con Blazor, siga las instrucciones correspondientes a la herramienta de su elección:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Instale la versión más reciente de [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) con la carga de trabajo **Desarrollo de ASP.NET y web**.

1. Cree un nuevo proyecto.

1. Seleccione **Aplicación Blazor** . Seleccione **Siguiente**.

1. Proporcione un nombre para el proyecto en el campo **Nombre del proyecto** o acepte el predeterminado. Confirme que la entrada de **Ubicación** es correcta o proporcione una ubicación para el proyecto. Seleccione **Crear**.

1. Para disfrutar de una experiencia de WebAssembly de Blazor, elija la plantilla **Aplicación WebAssembly de Blazor** . Para disfrutar de una experiencia de Blazor Server, elija la plantilla **Aplicación Blazor Server**. Seleccione **Crear**.

   Para más información sobre los dos modelos de hospedaje de Blazor (*WebAssembly de Blazor*  y *Blazor Server*), vea <xref:blazor/hosting-models>.

1. Presione <kbd>Ctrl</kbd>+<kbd>F5</kbd> para ejecutar la aplicación.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Instale la última versión del [SDK de .NET Core 3.1](https://dotnet.microsoft.com/download/dotnet-core/3.1). Si ya ha instalado el SDK anteriormente, puede averiguar la versión instalada ejecutando el siguiente comando en un shell de comandos:

   ```dotnetcli
   dotnet --version
   ```

1. Instale la versión más reciente de [Visual Studio Code](https://code.visualstudio.com/).

1. Instale la [extensión de C# para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) más reciente y la extensión del [depurador de JavaScript (nocturno)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) con `debug.javascript.usePreview` establecido en `true`.

  Para establecer `debug.javascript.usePreview` en `true` con la interfaz de usuario de VS Code, abra **Archivo** > **Preferencias** > **Configuración** y busque `debug javascript use preview`. Active la casilla para **usar el nuevo depurador de JavaScript en versión preliminar para Node.js y Chrome**.

1. Para disfrutar de una experiencia de WebAssembly de Blazor, ejecute el siguiente comando en un shell de comandos:

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   Para disfrutar de una experiencia de Blazor Server, ejecute el siguiente comando en un shell de comandos:

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   Para más información sobre los dos modelos de hospedaje de Blazor (*WebAssembly de Blazor*  y *Blazor Server*), vea <xref:blazor/hosting-models>.

1. Abra la carpeta `WebApplication1` en Visual Studio Code.

1. El IDE solicita que agregue recursos para compilar y depurar el proyecto. Seleccione **Sí**.

1. Presione <kbd>Ctrl</kbd>+<kbd>F5</kbd> para ejecutar la aplicación.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

1. Instale [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/).

1. Seleccione **Archivo** > **Nueva solución** o cree un proyecto **Nuevo** desde **Ventana de inicio**.

1. En la barra lateral, seleccione **Web y consola** > **Aplicación**.

   Para disfrutar de una experiencia de WebAssembly de Blazor, elija la plantilla **Aplicación WebAssembly de Blazor** . Para disfrutar de una experiencia de Blazor Server, elija la plantilla **Aplicación Blazor Server**. Seleccione **Siguiente**.

   Para más información sobre los dos modelos de hospedaje de Blazor (*WebAssembly de Blazor*  y *Blazor Server*), vea <xref:blazor/hosting-models>.

1. Confirme las configuraciones siguientes:

   * **Plataforma de destino** establecida en **.NET Core 3.1**.
   * **Autenticación** establecida en **Sin autenticación**.
   
   Seleccione **Siguiente**.

1. En el campo **Nombre del proyecto**, asigne un nombre a la aplicación `WebApplication1`. Seleccione **Crear**.

1. Seleccione **Ejecutar** > **Iniciar sin depurar** para ejecutar la aplicación *sin el depurador*. Ejecute la aplicación con **Ejecutar** > **Iniciar depuración** o usando el botón Ejecutar (&#9654;) para ejecutar la aplicación *con el depurador*.

Si aparece un mensaje para que confíe en el certificado de desarrollo, hágalo y continúe. Las contraseñas de usuario y de cadena de claves son necesarias para confiar en el certificado.

# <a name="net-core-cli"></a>[CLI de .NET Core](#tab/netcore-cli/)

1. Instale la última versión del [SDK de .NET Core 3.1](https://dotnet.microsoft.com/download/dotnet-core/3.1). Si ya ha instalado el SDK anteriormente, puede averiguar la versión instalada ejecutando el siguiente comando en un shell de comandos:

   ```dotnetcli
   dotnet --version
   ```

1. Para disfrutar de una experiencia de WebAssembly de Blazor, ejecute los siguientes comandos en un shell de comandos:

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   Para disfrutar de una experiencia de Blazor Server, ejecute los siguientes comandos en un shell de comandos:

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   Para más información sobre los dos modelos de hospedaje de Blazor (*WebAssembly de Blazor*  y *Blazor Server*), vea <xref:blazor/hosting-models>.

1. En un navegador, vaya a `https://localhost:5001`.

---

Hay varias páginas disponibles en las pestañas de la barra lateral:

* Página principal
* Contador
* Captura de datos

En la página Contador, seleccione el botón para aumentar el contador sin una actualización de página. Para incrementar un contador en una página web suele ser necesario escribir JavaScript, pero con Blazor se puede usar C#.

`Pages/Counter.razor`:

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

Una solicitud de `/counter` en el explorador, tal y como se especifica en la directiva de `@page` en la parte superior, hace que el componente `Counter` represente su contenido. Los componentes se representan en una representación en memoria del árbol de representación que se puede usar para actualizar la interfaz de usuario de una manera eficaz y flexible.

Cada vez que se selecciona el botón, ocurre lo siguiente:

* Se desencadena el evento `onclick`.
* Se llama al método `IncrementCount` .
* El elemento `currentCount` se incrementa.
* El componente se representa de nuevo.

El tiempo de ejecución compara el contenido nuevo con el anterior y solo aplica el contenido cambiado al Document Object Model (DOM).

Agregue un componente a otro mediante sintaxis HTML. Por ejemplo, para agregar el componente `Counter` a la página de inicio de la aplicación, incorpore un elemento `<Counter />` al componente `Index`.

`Pages/Index.razor`:

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

Ejecutar la aplicación. La página principal tiene su propio contador que proporciona el componente `Counter`.

Los parámetros del componente se especifican mediante atributos o [contenido secundario](xref:blazor/components/index#child-content), que permiten establecer propiedades en el componente secundario. Para agregar un parámetro al componente `Counter`, actualice el bloque `@code` del componente:

* Agregue una propiedad pública para `IncrementAmount` con un atributo [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute).
* Cambie el método `IncrementCount` para usar `IncrementAmount` al aumentar el valor de `currentCount`.

`Pages/Counter.razor`:

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

Especifique `IncrementAmount` en el elemento `<Counter>` del componente `Index` mediante un atributo.

`Pages/Index.razor`:

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

Ejecutar la aplicación. El componente `Index` tiene su propio contador que se incrementa en diez cada vez que se selecciona el botón. El componente `Counter` (`Pages/Counter.razor`) en `/counter` sigue incrementándose en uno.

## <a name="next-steps"></a>Pasos siguientes

Cree una aplicación Blazor paso a paso:

> [!div class="nextstepaction"]
> <xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a>Recursos adicionales

* <xref:blazor/templates>
* <xref:signalr/introduction>
