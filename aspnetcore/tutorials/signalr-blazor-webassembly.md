---
title: Uso de ASP.NET Core SignalR con Blazor WebAssembly
author: guardrex
description: Cree una aplicación de chat que use ASP.NET Core SignalR con Blazor WebAssembly.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
- Blazor
- SignalR
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: c4843dc282e1978b39738e206ecc79ded87fcff9
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/06/2020
ms.locfileid: "80306567"
---
# <a name="use-aspnet-core-signalr-with-blazor-webassembly"></a>Uso de ASP.NET Core SignalR con Blazor WebAssembly

Por [Daniel Roth](https://github.com/danroth27) y [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Este tutorial le enseña los conceptos básicos de la creación de una aplicación en tiempo real mediante SignalR con Blazor WebAssembly. Aprenderá a:

> [!div class="checklist"]
> * Crear un proyecto de aplicación hospedado de Blazor WebAssembly
> * Agregar la biblioteca cliente de SignalR
> * Agregar un concentrador de SignalR
> * Agregar servicios de SignalR y un punto de conexión para el concentrador de SignalR
> * Agregar código de componente de Razor para el chat

Al final de este tutorial, tendrá una aplicación de chat funcional.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Requisitos previos

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

# <a name="net-core-cli"></a>[CLI de .NET Core](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

## <a name="create-a-hosted-blazor-webassembly-app-project"></a>Creación de un proyecto de aplicación de Blazor WebAssembly hospedado

Si no usa Visual Studio versión 16.6 Preview 2 o posterior, instale la plantilla [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly). El paquete [Microsoft.AspNetCore.Components.WebAssembly.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Templates/) tiene una versión preliminar, mientras que Blazor WebAssembly se encuentra en versión preliminar. En un shell de comandos, ejecute el siguiente comando:

```dotnetcli
dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview3.20168.3
```

Siga las instrucciones para su elección de herramientas:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Cree un nuevo proyecto.

1. Seleccione **Aplicación Blazor** y luego **Siguiente**.

1. Escriba "BlazorSignalRApp" en el campo **Nombre del proyecto**. Confirme que la entrada de **Ubicación** es correcta o proporcione una ubicación para el proyecto. Seleccione **Crear**.

1. Elija la plantilla **Aplicación WebAssembly de Blazor**.

1. En **Avanzado**, active la casilla **ASP.NET Core hospedado**.

1. Seleccione **Crear**.

> [!NOTE]
> Si ha actualizado o instalado una nueva versión de Visual Studio y la plantilla de Blazor WebAssembly no aparece en la interfaz de usuario de dicha aplciación, vuelva a instalar la plantilla con el comando de `dotnet new` que mostrado anteriormente.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. En un shell de comandos, ejecute el siguiente comando:

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. En Visual Studio Code, abra la carpeta de proyecto de la aplicación.

1. Cuando aparezca el cuadro de diálogo para agregar recursos para compilar y depurar la aplicación, seleccione **Sí**. Visual Studio Code agrega automáticamente la carpeta *.vscode* con los archivos *launch.json* y *tasks.json* generados.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

1. En un shell de comandos, ejecute el siguiente comando:

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. En Visual Studio para Mac, abra el proyecto; para ello, vaya a la carpeta del proyecto y abra el archivo de solución del proyecto ( *.sln*).

# <a name="net-core-cli"></a>[CLI de .NET Core](#tab/netcore-cli/)

En un shell de comandos, ejecute el siguiente comando:

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-signalr-client-library"></a>Agregar la biblioteca cliente de SignalR

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

1. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto **BlazorSignalRApp.Client** y seleccione **Administrar paquetes NuGet**.

1. En el cuadro de diálogo **Administrar paquetes NuGet**, confirme que **Origen del paquete** se ha establecido en *nuget.org*.

1. Con **Examinar** seleccionado, escriba "Microsoft.AspNetCore.SignalR.Client" en el cuadro de búsqueda.

1. En los resultados de la búsqueda, seleccione el paquete `Microsoft.AspNetCore.SignalR.Client` y luego **Instalar**.

1. Si aparece el cuadro de diálogo **Vista previa de los cambios**, seleccione **Aceptar**.

1. Si aparece el cuadro de diálogo **Aceptación de la licencia**, seleccione **Acepto** si está de acuerdo con los términos de la licencia.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

En **Terminal integrado** (**Ver** > **Terminal** en la barra de herramientas), ejecute los siguientes comandos:

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

1. En la barra lateral **Solución**, haga clic con el botón derecho en el proyecto **BlazorSignalRApp.Client** y seleccione **Administrar paquetes NuGet**.

1. En el cuadro de diálogo **Administrar paquetes NuGet**, confirme que la lista desplegable de origen se ha establecido en *nuget.org*.

1. Con **Examinar** seleccionado, escriba "Microsoft.AspNetCore.SignalR.Client" en el cuadro de búsqueda.

1. En los resultados de la búsqueda, active la casilla situada junto al paquete `Microsoft.AspNetCore.SignalR.Client` y seleccione **Agregar paquete**.

1. Si aparece el cuadro de diálogo **Aceptación de la licencia**, seleccione **Acepto** si está de acuerdo con los términos de la licencia.

# <a name="net-core-cli"></a>[CLI de .NET Core](#tab/netcore-cli/)

En un shell de comandos, ejecute los siguientes comandos:

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-signalr-hub"></a>Agregar un concentrador de SignalR

En el proyecto **BlazorSignalRApp.Server**, cree una carpeta de *Hubs* (plural) y agregue la siguiente clase `ChatHub` (*Hubs/ChatHub.cs*):

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

## <a name="add-signalr-services-and-an-endpoint-for-the-signalr-hub"></a>Agregar servicios de SignalR y un punto de conexión para el concentrador de SignalR

1. En el proyecto **BlazorSignalRApp.Server**, abra el archivo *Startup.cs*.

1. Agregue el espacio de nombres para la clase `ChatHub` en la parte superior del archivo:

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. Agregue los servicios de SignalR a `Startup.ConfigureServices`:

   ```csharp
   services.AddSignalR();
   ```

1. En `Startup.Configure` entre los puntos de conexión de la ruta del controlador predeterminada y la reserva del lado cliente, agregue un punto de conexión para el concentrador:

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet&highlight=4)]

## <a name="add-razor-component-code-for-chat"></a>Agregar código de componente de Razor para el chat

1. En el proyecto **BlazorSignalRApp.Client**, abra el archivo *Pages/Index.razor*.

1. Reemplace el marcado con el código siguiente:

[!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

## <a name="run-the-app"></a>Ejecutar la aplicación

1. Siga las instrucciones para las herramientas:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. En **Explorador de soluciones**, seleccione el proyecto **BlazorSignalRApp.Server**. Presione **CTRL+F5** para ejecutar la aplicación sin depurar.

1. Copie la dirección URL de la barra de direcciones, abra otra instancia o pestaña del explorador, y pegue la dirección URL en la barra de direcciones.

1. Elija cualquier explorador, escriba un nombre y un mensaje, y haga clic en el botón **Enviar**. El nombre y el mensaje se muestran en ambas páginas al instante:

   ![Aplicación de ejemplo de SignalR Blazor WebAssembly abierta en dos ventanas del explorador que muestran mensajes intercambiados.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Citas: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Seleccione **Depurar** > **Ejecutar sin depuración** en la barra de herramientas.

1. Copie la dirección URL de la barra de direcciones, abra otra instancia o pestaña del explorador, y pegue la dirección URL en la barra de direcciones.

1. Elija cualquier explorador, escriba un nombre y un mensaje, y haga clic en el botón **Enviar**. El nombre y el mensaje se muestran en ambas páginas al instante:

   ![Aplicación de ejemplo de SignalR Blazor WebAssembly abierta en dos ventanas del explorador que muestran mensajes intercambiados.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Citas: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

1. En la barra lateral **Solución**, seleccione el proyecto **BlazorSignalRApp.Server**. En el menú, seleccione **Ejecutar** > **Iniciar sin depuración**.

1. Copie la dirección URL de la barra de direcciones, abra otra instancia o pestaña del explorador, y pegue la dirección URL en la barra de direcciones.

1. Elija cualquier explorador, escriba un nombre y un mensaje, y haga clic en el botón **Enviar**. El nombre y el mensaje se muestran en ambas páginas al instante:

   ![Aplicación de ejemplo de SignalR Blazor WebAssembly abierta en dos ventanas del explorador que muestran mensajes intercambiados.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Citas: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="net-core-cli"></a>[CLI de .NET Core](#tab/netcore-cli/)

1. En un shell de comandos, ejecute los siguientes comandos:

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. Copie la dirección URL de la barra de direcciones, abra otra instancia o pestaña del explorador, y pegue la dirección URL en la barra de direcciones.

1. Elija cualquier explorador, escriba un nombre y un mensaje, y haga clic en el botón **Enviar**. El nombre y el mensaje se muestran en ambas páginas al instante:

   ![Aplicación de ejemplo de SignalR Blazor WebAssembly abierta en dos ventanas del explorador que muestran mensajes intercambiados.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Citas: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

---

## <a name="next-steps"></a>Pasos siguientes

En este tutorial ha aprendido a:

> [!div class="checklist"]
> * Crear un proyecto de aplicación hospedado de Blazor WebAssembly
> * Adición de la biblioteca cliente de SignalR
> * Agregar un concentrador de SignalR
> * Agregar servicios de SignalR y un punto de conexión para el concentrador de SignalR
> * Agregar código de componente de Razor para el chat

Para más información sobre la creación de aplicaciones de Blazor, consulte la documentación de Blazor:

> [!div class="nextstepaction"]
> <xref:blazor/index>

## <a name="additional-resources"></a>Recursos adicionales

* <xref:signalr/introduction>
