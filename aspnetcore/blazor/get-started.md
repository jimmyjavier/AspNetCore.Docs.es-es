---
title: Introducción a ASP.NET Core Blazor
author: guardrex
description: Para empezar a usar Blazor, compile una aplicación Blazor con las herramientas que prefiera.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/16/2020
no-loc:
- Blazor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: 2f10b00adce31c020d46d107c087159c17341beb
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/24/2020
ms.locfileid: "82111076"
---
# <a name="get-started-with-aspnet-core-blazor"></a>Introducción a ASP.NET Core Blazor

Por [Daniel Roth](https://github.com/danroth27) y [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Para empezar a trabajar con Blazor, siga las instrucciones para las herramientas que prefiere:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Para crear aplicaciones de Blazor Server, instale la versión más reciente de [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) con la carga de trabajo **Desarrollo de ASP.NET y web**.

   Para crear aplicaciones de Blazor Server y Blazor WebAssembly, instale la versión preliminar más reciente de [Visual Studio 2019](https://visualstudio.microsoft.com/vs/preview/) con la carga de trabajo **Desarrollo de ASP.NET y web**.

   Para información sobre los dos modelos de hospedaje de Blazor, *Blazor WebAssembly* y *Blazor Server*, consulte <xref:blazor/hosting-models>.

1. Instale la plantilla de versión preliminar [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) mediante la ejecución del comando siguiente:

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview5.20216.8
   ```

1. Cree un nuevo proyecto.

1. Seleccione **Aplicación Blazor**. Seleccione **Siguiente**.

1. Proporcione un nombre para el proyecto en el campo **Nombre del proyecto** o acepte el predeterminado. Confirme que la entrada de **Ubicación** es correcta o proporcione una ubicación para el proyecto. Seleccione **Crear**.

1. Para disfrutar de una experiencia de Blazor WebAssembly (Visual Studio 16.6 Preview 2 o posterior), elija la plantilla **Aplicación de Blazor WebAssembly**. Para disfrutar de una experiencia de Blazor Server (Visual Studio 16.4 o posterior), elija la plantilla **Aplicación de Blazor Server**. Seleccione **Crear**.

1. Presione <kbd>Ctrl</kbd>+<kbd>F5</kbd> para ejecutar la aplicación.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Instale el [SDK de .NET Core 3.1](https://dotnet.microsoft.com/download/dotnet-core/3.1).

1. También puede instalar la plantilla de versión preliminar [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) si ejecuta el comando siguiente:

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview5.20216.8
   ```

   > [!NOTE]
   > El [SDK de .NET Core, versión 3.1.201 o posterior](https://dotnet.microsoft.com/download/dotnet-core/3.1), es **necesario** para usar la plantilla Blazor WebAssembly 3.2 Preview 4. Ejecute `dotnet --version` en un shell de comandos para confirmar la versión instalada del SDK de .NET Core.

1. Instale [Visual Studio Code](https://code.visualstudio.com/).

1. Instale la [extensión de C# para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) más reciente y la extensión del [depurador de JavaScript (nocturno)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) con `debug.javascript.usePreview` establecido en `true`.

1. Para disfrutar de una experiencia de Blazor Server, ejecute el comando siguiente en un shell de comandos:

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   Para disfrutar de una experiencia de Blazor WebAssembly, ejecute el comando siguiente en un shell de comandos:

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   Para obtener más información sobre los dos modelos de hospedaje de Blazor, *Blazor Server* y *Blazor WebAssembly*, vea <xref:blazor/hosting-models>.

1. Abra la carpeta *WebApplication1* en Visual Studio Code.

1. El IDE solicita que agregue recursos para compilar y depurar el proyecto. Seleccione **Sí**.

1. Con Blazor Server, ejecute la aplicación con el depurador de Visual Studio Code.

   Con Blazor WebAssembly, inicie la aplicación con la configuración de inicio **.NET Core Launch (independiente de Blazor)** e inicie el explorador con la configuración de inicio **Depuración de Blazor Web Assembly para .NET Core en Chrome** (requiere Chrome). Para obtener más información, vea <xref:blazor/debug#visual-studio-code>.

1. En un navegador, vaya a `https://localhost:5001`.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

Blazor Server es compatible con Visual Studio para Mac. Blazor WebAssembly no es compatible en este momento. Para compilar aplicaciones de Blazor WebAssembly en macOS, siga las instrucciones que aparecen en la pestaña **CLI de .NET Core**.

1. Instale [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/).

1. Seleccione **Archivo** > **Nueva solución** o cree un **Nuevo proyecto**.

1. En la barra lateral, seleccione **.NET Core** > **Aplicación**.

1. Seleccione la plantilla de **Aplicación Blazor Server**. Seleccione **Crear**.

   Para más información sobre el modelo de hospedaje de Blazor Server, consulte <xref:blazor/hosting-models>.

1. Establezca el **Marco de destino** en **.NET Core 3.1** y seleccione **Siguiente**.

1. En el campo **Nombre del proyecto**, asigne un nombre a la aplicación `WebApplication1`. Seleccione **Crear**.

1. Seleccione **Ejecutar** > **Ejecutar sin depuración** para ejecutar la aplicación *sin el depurador*. Ejecute la aplicación con **Iniciar depuración** para ejecutarla *con el depurador*.

Si aparece un mensaje para que confíe en el certificado de desarrollo, hágalo y continúe.

# <a name="net-core-cli"></a>[CLI de .NET Core](#tab/netcore-cli/)

1. Instale el [SDK de .NET Core 3.1](https://dotnet.microsoft.com/download/dotnet-core/3.1).

1. También puede instalar la plantilla de versión preliminar [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) si ejecuta el comando siguiente:

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview5.20216.8
   ```

   > [!NOTE]
   > El [SDK de .NET Core, versión 3.1.201 o posterior](https://dotnet.microsoft.com/download/dotnet-core/3.1), es **necesario** para usar la plantilla Blazor WebAssembly 3.2 Preview 4. Ejecute `dotnet --version` en un shell de comandos para confirmar la versión instalada del SDK de .NET Core.

1. Para disfrutar de una experiencia de Blazor Server, ejecute los comandos siguientes en un shell de comandos:

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   Para disfrutar de una experiencia Blazor WebAssembly, ejecute los comandos siguientes en un shell de comandos:

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   Para obtener más información sobre los dos modelos de hospedaje de Blazor, *Blazor Server* y *Blazor WebAssembly*, vea <xref:blazor/hosting-models>.

1. En un navegador, vaya a `https://localhost:5001`.

---

Hay varias páginas disponibles en las pestañas de la barra lateral:

* Página principal
* Contador
* Captura de datos

En la página Contador, seleccione el botón **Click me** para aumentar el contador sin una actualización de página. Para incrementar un contador en una página web suele ser necesario escribir JavaScript, pero con Blazor se puede usar C#.

*Pages/Counter.razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

Una solicitud de `/counter` en el explorador, tal y como se especifica en la directiva de `@page` en la parte superior, hace que el componente `Counter` represente su contenido. Los componentes se representan en una representación en memoria del árbol de representación que se puede usar para actualizar la interfaz de usuario de una manera eficaz y flexible.

Cada vez que se selecciona el botón **Hacer clic aquí**, ocurre lo siguiente:

* Se desencadena el evento `onclick`.
* Se llama al método `IncrementCount` .
* El elemento `currentCount` se incrementa.
* El componente se representa de nuevo.

El tiempo de ejecución compara el contenido nuevo con el anterior y solo aplica el contenido cambiado al Document Object Model (DOM).

Agregue un componente a otro mediante sintaxis HTML. Por ejemplo, para agregar el componente `Counter` a la página de inicio de la aplicación, incorpore un elemento `<Counter />` al componente `Index`.

*Pages/Index.razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

Ejecutar la aplicación. La página principal tiene su propio contador que proporciona el componente `Counter`.

Los parámetros del componente se especifican mediante atributos o [contenido secundario](xref:blazor/components#child-content), que permiten establecer propiedades en el componente secundario. Para agregar un parámetro al componente `Counter`, actualice el bloque `@code` del componente:

* Agregue una propiedad pública para `IncrementAmount` con un atributo `[Parameter]`.
* Cambie el método `IncrementCount` para usar `IncrementAmount` al aumentar el valor de `currentCount`.

*Pages/Counter.razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

Especifique `IncrementAmount` en el elemento `<Counter>` del componente `Index` mediante un atributo.

*Pages/Index.razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

Ejecutar la aplicación. El componente `Index` tiene su propio contador que se incrementa en diez cada vez que se selecciona el botón **Hacer clic aquí**. El componente `Counter` (*Counter.razor*) en `/counter` sigue incrementándose en uno.

## <a name="next-steps"></a>Pasos siguientes

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a>Recursos adicionales

* <xref:blazor/templates>
* <xref:signalr/introduction>
