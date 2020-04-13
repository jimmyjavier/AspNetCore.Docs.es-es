---
title: Plantillas de Blazor de ASP.NET Core
author: guardrex
description: Obtenga información sobre las plantillas de aplicación de Blazor de ASP.NET Core y la estructura de proyecto de Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
- Blazor
- SignalR
uid: blazor/templates
ms.openlocfilehash: 71a9d9eee8637dda0b3cecac82ff96a0c3bfedb5
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/06/2020
ms.locfileid: "80320975"
---
# <a name="aspnet-core-opno-locblazor-templates"></a>Plantillas de Blazor de ASP.NET Core

Por [Daniel Roth](https://github.com/danroth27) y [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

El marco Blazor proporciona plantillas que permiten desarrollar aplicaciones para cada uno de los modelos de hospedaje de Blazor:

* WebAssembly de Blazor (`blazorwasm`)
* Servidor Blazor (`blazorserver`)

Para más información sobre los modelos de hospedaje de Blazor, vea <xref:blazor/hosting-models>.

Para obtener instrucciones paso a paso sobre cómo crear una aplicación Blazor a partir de una plantilla, vea <xref:blazor/get-started>.

## <a name="opno-locblazor-project-structure"></a>Estructura de proyecto de Blazor

Los siguientes archivos y carpetas componen una aplicación Blazor generada a partir de una plantilla de Blazor:

* *Program.cs*: punto de entrada de la aplicación que instala lo siguiente:

  * [Host](xref:fundamentals/host/generic-host) de ASP.NET Core (servidor Blazor)
  * Host de WebAssembly (WebAssembly de Blazor): el código de este archivo es único en las aplicaciones creadas a partir de la plantilla de WebAssembly de Blazor (`blazorwasm`).
    * El componente `App`, que es el componente raíz de la aplicación, se especifica como el elemento DOM `app` en el método `Add`.
    * Los servicios se pueden configurar con el método `ConfigureServices` en el generador del host (por ejemplo, `builder.Services.AddSingleton<IMyDependency, MyDependency>();`).
    * La configuración se puede proporcionar a través del generador del host (`builder.Configuration`).

* *Startup.cs* (servidor Blazor): contiene la lógica de inicio de la aplicación. La clase `Startup` define dos métodos:

  * `ConfigureServices`: configura los servicios de [inserción de dependencias](xref:fundamentals/dependency-injection) de la aplicación. En las aplicaciones del servidor Blazor, los servicios se agregan llamando a <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor*>, y el elemento `WeatherForecastService` se agrega al contenedor de servicios para que lo use el componente `FetchData` de ejemplo.
  * `Configure`: configura la canalización de administración de solicitudes de la aplicación:
    * Se llama a <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub*> para configurar un punto de conexión para la conexión en tiempo real con el explorador. La conexión se crea con [SignalR](xref:signalr/introduction), que es un marco para agregar funcionalidad web a las aplicaciones en tiempo real.
    * Se llama a [MapFallbackToPage("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) para configurar la página raíz de la aplicación (*Pages/_Host.cshtml*) y habilitar la navegación.

* *wwwroot/index.html* (WebAssembly de Blazor): página raíz de la aplicación implementada como una página HTML:
  * Cuando se solicita inicialmente cualquier página de la aplicación, esta página se representa y se devuelve en la respuesta.
  * La página especifica dónde se va a representar el componente `App` raíz. El componente `App` (*App.Razor*) se especifica como el elemento DOM `app` del método `AddComponent` en `Startup.Configure`.
  * Se carga el archivo JavaScript `_framework/blazor.webassembly.js`, que hace lo siguiente:
    * Descarga el runtime de .NET, la aplicación y las dependencias de la aplicación.
    * Inicializa el runtime para ejecutar la aplicación.

* *App.razor*: componente raíz de la aplicación que configura el enrutamiento del lado cliente mediante el componente <xref:Microsoft.AspNetCore.Components.Routing.Router>. El componente `Router` intercepta la navegación del explorador y representa la página que coincide con la dirección solicitada.

* Carpeta *Pages*: contiene las páginas o componentes enrutables ( *.razor*) que conforman la aplicación de Blazor y la página raíz de Razor de una aplicación de Blazor Server. La ruta de cada página se especifica usando la directiva [`@page`](xref:mvc/views/razor#page). En la plantilla se incluye lo siguiente:
  * *_Host.cshtml* (Blazor Server): la página raíz de la aplicación implementada como una aplicación de Razor Pages:
    * Cuando se solicita inicialmente cualquier página de la aplicación, esta página se representa y se devuelve en la respuesta.
    * Se carga el archivo JavaScript `_framework/blazor.server.js`, que configura la conexión de SignalR en tiempo real entre el explorador y el servidor.
    * La página Host especifica dónde se va a representar el componente `App` raíz (*App.razor*).
  * `Counter` (*Counter.razor*): implementa la página Counter (Contador).
  * `Error` (*Error.razor*, solo aplicación de servidor Blazor): se representa cuando se produce una excepción no controlada en la aplicación.
  * `FetchData` (*FetchData.razor*): implementa la página Fetch data (Recuperar datos).
  * `Index` (*Index.razor*): implementa la página principal.

* Carpeta *Shared*: contiene otros componentes de interfaz de usuario ( *.razor*) que la aplicación usa:
  * `MainLayout` (*MainLayout.razor*): componente de diseño de la aplicación.
  * `NavMenu` (*NavMenu.razor*): implementa la navegación de barra lateral. Incluye el [componente NavLink](xref:blazor/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), que representa los vínculos de navegación a otros componentes de Razor. El componente `NavLink` indica automáticamente un estado seleccionado cuando su componente se carga, lo que ayuda al usuario a saber qué componente se está mostrando actualmente.

* *_Imports.razor*: engloba las directivas de Razor comunes que se incluirán en los componentes de la aplicación ( *.razor*), como las directivas [`@using`](xref:mvc/views/razor#using) de espacios de nombres.

* Carpeta *Data* (servidor Blazor): contiene la clase `WeatherForecast` y la implementación de `WeatherForecastService`, que proporcionan datos meteorológicos de ejemplo al componente `FetchData` de la aplicación.

* *wwwroot*: carpeta de [raíz web](xref:fundamentals/index#web-root) de la aplicación que contiene los activos estáticos públicos de la aplicación.

* *appsettings.json* (servidor Blazor): opciones de configuración de la aplicación.
