---
title: Migración de ASP.NET MVC a ASP.NET Core MVC
author: ardalis
description: Obtenga información sobre cómo empezar a migrar un proyecto de ASP.NET MVC a ASP.NET Core MVC.
ms.author: riande
ms.date: 04/06/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/mvc
ms.openlocfilehash: 59a10c002958e5f719dbd59686f21df69da5f43e
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777051"
---
# <a name="migrate-from-aspnet-mvc-to-aspnet-core-mvc"></a>Migración de ASP.NET MVC a ASP.NET Core MVC

Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), [Steve Smith](https://ardalis.com/)y [Scott Addie](https://scottaddie.com)

En este artículo se muestra cómo empezar a migrar un proyecto de ASP.NET MVC a [ASP.net Core MVC](../mvc/overview.md). En el proceso, se resaltan muchas de las cosas que han cambiado de ASP.NET MVC. La migración de ASP.NET MVC es un proceso de varios pasos y en este artículo se trata la configuración inicial, los controladores y las vistas básicos, el contenido estático y las dependencias del lado cliente. Los artículos adicionales cubren la migración de la configuración y el código de identidad que se encuentran en muchos proyectos de MVC de ASP.NET.

> [!NOTE]
> Es posible que los números de versión de los ejemplos no estén actualizados. Es posible que tenga que actualizar los proyectos en consecuencia.

## <a name="create-the-starter-aspnet-mvc-project"></a>Crear el proyecto de MVC ASP.NET MVC

Para demostrar la actualización, comenzaremos por crear una aplicación de ASP.NET MVC. Créelo con el nombre *WebApp1* para que el espacio de nombres coincida con el ASP.net Core proyecto que creamos en el paso siguiente.

![Cuadro de diálogo Nuevo proyecto de Visual Studio](mvc/_static/new-project.png)

![Cuadro de diálogo Nueva aplicación web: plantilla de proyecto de MVC seleccionada en el panel de plantillas de ASP.NET](mvc/_static/new-project-select-mvc-template.png)

*Opcional:* Cambie el nombre de la solución de *WebApp1* a *Mvc5*. Visual Studio muestra el nuevo nombre de la solución (*Mvc5*), lo que facilita la información de este proyecto desde el proyecto siguiente.

## <a name="create-the-aspnet-core-project"></a>Crear el proyecto de ASP.NET Core

Cree una nueva aplicación Web *vacía* de ASP.net Core con el mismo nombre que el proyecto anterior (*WebApp1*) para que coincidan los espacios de nombres de los dos proyectos. Tener el mismo espacio de nombres facilita la copia de código entre los dos proyectos. Tendrá que crear este proyecto en un directorio diferente al del proyecto anterior para usar el mismo nombre.

![Cuadro de diálogo Nuevo proyecto](mvc/_static/new_core.png)

![Cuadro de diálogo Nueva aplicación Web de ASP.NET: plantilla de proyecto vacía seleccionada en el panel de plantillas de ASP.NET Core](mvc/_static/new-project-select-empty-aspnet5-template.png)

* *Opcional:* Cree una nueva aplicación de ASP.NET Core mediante la plantilla de proyecto de *aplicación web* . Asigne al proyecto el nombre *WebApp1*y seleccione una opción de autenticación de **cuentas de usuario individuales**. Cambie el nombre de esta aplicación a *FullAspNetCore*. Al crear este proyecto, se ahorra tiempo en la conversión. Puede examinar el código generado por la plantilla para ver el resultado final o copiar el código en el proyecto de conversión. También resulta útil cuando se queda bloqueado en un paso de conversión para compararlo con el proyecto generado por una plantilla.

## <a name="configure-the-site-to-use-mvc"></a>Configurar el sitio para usar MVC

::: moniker range=">= aspnetcore-2.1"

* Cuando el destino es .NET Core, se hace referencia al [metapaquete Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) de forma predeterminada. Este paquete contiene los paquetes que usan normalmente las aplicaciones MVC. Si el destino es .NET Framework, las referencias del paquete deben aparecer de forma individual en el archivo del proyecto.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* Cuando el destino es .NET Core, se hace referencia al [metapaquete Microsoft. AspNetCore. All](xref:fundamentals/metapackage) de forma predeterminada. Este paquete contiene paquetes usados comúnmente por aplicaciones MVC. Si el destino es .NET Framework, las referencias del paquete deben aparecer de forma individual en el archivo del proyecto.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

* Cuando el destino es .NET Core o .NET Framework, los paquetes usados comúnmente por las aplicaciones MVC se enumeran individualmente en el archivo del proyecto.

::: moniker-end

`Microsoft.AspNetCore.Mvc`es el marco de MVC ASP.NET Core. `Microsoft.AspNetCore.StaticFiles`es el controlador de archivos estático. El tiempo de ejecución de ASP.NET Core es modular y debe participar explícitamente para servir archivos estáticos (vea [archivos estáticos](xref:fundamentals/static-files)).

* Abra el archivo *Startup.CS* y cambie el código para que coincida con lo siguiente:

  [!code-csharp[](mvc/sample/Startup.cs?highlight=13,26-31)]

El `UseStaticFiles` método de extensión agrega el controlador de archivos estáticos. Como se mencionó anteriormente, el tiempo de ejecución de ASP.NET es modular y debe participar explícitamente para servir archivos estáticos. El `UseMvc` método de extensión agrega enrutamiento. Para obtener más información, consulte Inicio y [enrutamiento](xref:fundamentals/routing)de la [aplicación](xref:fundamentals/startup) .

## <a name="add-a-controller-and-view"></a>Agregar un controlador y una vista

En esta sección, agregará un controlador y una vista mínimos para que sirvan como marcadores de posición para el controlador ASP.NET MVC y las vistas que va a migrar en la sección siguiente.

* Agregue una carpeta *Controllers* .

* Agregue una **clase de controlador** denominada *HomeController.CS* a la carpeta *Controllers* .

![Cuadro de diálogo Agregar nuevo elemento](mvc/_static/add_mvc_ctl.png)

* Agregue una carpeta *views* .

* Agregue una carpeta *views/Home* .

* Agregue una ** Razor vista** denominada *index. cshtml* a la carpeta *views/Home* .

![Cuadro de diálogo Agregar nuevo elemento](mvc/_static/view.png)

La estructura del proyecto se muestra a continuación:

![Explorador de soluciones que muestran archivos y carpetas de WebApp1](mvc/_static/project-structure-controller-view.png)

Reemplace el contenido del archivo *views/home/index. cshtml* con lo siguiente:

```html
<h1>Hello world!</h1>
```

Ejecutar la aplicación.

![Aplicación web abierta en Microsoft Edge](mvc/_static/hello-world.png)

Consulte [Controladores](xref:mvc/controllers/actions) y [vistas](xref:mvc/views/overview) para obtener más información.

Ahora que tenemos un proyecto de ASP.NET Core de trabajo mínimo, podemos empezar a migrar la funcionalidad del proyecto ASP.NET MVC. Necesitamos trasladar lo siguiente:

* contenido del lado cliente (CSS, fuentes y scripts)

* controllers

* views

* modelos

* Unión

* filters

* Iniciar y cerrar sesión Identity (esto se hace en el siguiente tutorial).

## <a name="controllers-and-views"></a>Controladores y vistas

* Copie cada uno de los métodos de MVC `HomeController` de ASP.net al nuevo `HomeController`. Tenga en cuenta que en ASP.NET MVC, el tipo de valor devuelto del método de acción del controlador de la plantilla integrada es [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx). en ASP.NET Core MVC, los métodos de acción `IActionResult` devuelven en su lugar. `ActionResult`implementa `IActionResult`, por lo que no es necesario cambiar el tipo de valor devuelto de los métodos de acción.

* Copie los archivos de vista *About. cshtml*, *Contact. cshtml*e *index. cshtml* Razor del proyecto ASP.NET MVC en el proyecto ASP.net Core.

* Ejecute la aplicación ASP.NET Core y pruebe cada método. Todavía no se han migrado el archivo de diseño o los estilos, por lo que las vistas representadas solo contienen el contenido de los archivos de vista. No tendrá los vínculos generados por el archivo de `About` diseño `Contact` para las vistas y, por lo que tendrá que invocarlos desde el explorador (Reemplace **4492** por el número de Puerto usado en el proyecto).

  * `http://localhost:4492/home/about`

  * `http://localhost:4492/home/contact`

![Página de contacto](mvc/_static/contact-page.png)

Tenga en cuenta la falta de estilo y elementos de menú. Esto lo corregiremos en la sección siguiente.

## <a name="static-content"></a>Contenido estático

En versiones anteriores de ASP.NET MVC, el contenido estático se hospedaba en la raíz del proyecto web y se combinaba con los archivos del lado servidor. En ASP.NET Core, el contenido estático se hospeda en la carpeta *wwwroot* . Querrá copiar el contenido estático de la aplicación ASP.NET MVC antigua en la carpeta *wwwroot* del proyecto de ASP.net Core. En esta conversión de ejemplo:

* Copie el archivo *favicon. ico* del proyecto de MVC anterior en la carpeta *wwwroot* del proyecto ASP.net Core.

El antiguo proyecto ASP.NET MVC usa [bootstrap](https://getbootstrap.com/) para su estilo y almacena los archivos de arranque en las carpetas *contenido* y *scripts* . La plantilla, que generó el proyecto ASP.NET MVC anterior, hace referencia a bootstrap en el archivo de diseño (*views/Shared/_Layout. cshtml*). Puede copiar los archivos *bootstrap. js* y *bootstrap. css* del proyecto ASP.NET MVC en la carpeta *wwwroot* del nuevo proyecto. En su lugar, agregaremos compatibilidad con bootstrap (y otras bibliotecas del lado cliente) mediante redes CDN en la sección siguiente.

## <a name="migrate-the-layout-file"></a>Migrar el archivo de diseño

* Copie el archivo *_ViewStart. cshtml* de la carpeta *views* del proyecto de ASP.NET MVC antigua en la carpeta *views* del proyecto ASP.net Core. El archivo *_ViewStart. cshtml* no ha cambiado en ASP.net Core MVC.

* Cree una carpeta *views/Shared* .

* *Opcional:* Copie *_ViewImports. cshtml* de la carpeta *views* del proyecto de *FullAspNetCore* MVC en la carpeta *views* del proyecto ASP.net Core. Quite cualquier declaración de espacio de nombres del archivo *_ViewImports. cshtml* . El archivo *_ViewImports. cshtml* proporciona espacios de nombres para todos los archivos de vista y ofrece [aplicaciones auxiliares de etiquetas](xref:mvc/views/tag-helpers/intro). Las aplicaciones auxiliares de etiquetas se usan en el nuevo archivo de diseño. El archivo *_ViewImports. cshtml* es nuevo para ASP.net Core.

* Copie el archivo *_Layout. cshtml* de la carpeta de *vistas/compartidas* del proyecto de ASP.NET MVC anterior en la carpeta *views/Shared* del proyecto de ASP.net Core.

Abra *_Layout archivo. cshtml* y realice los cambios siguientes (el código completo se muestra a continuación):

* Reemplace `@Styles.Render("~/Content/css")` por un `<link>` elemento para cargar *bootstrap. CSS* (consulte a continuación).

* Quite `@Scripts.Render("~/bundles/modernizr")`.

* Comente la `@Html.Partial("_LoginPartial")` línea (rodee la línea con `@*...*@`). Para obtener más información [, vea migrar Identity la autenticación y ASP.net Core](xref:migration/identity)

* Reemplazar `@Scripts.Render("~/bundles/jquery")` por un `<script>` elemento (vea más abajo).

* Reemplazar `@Scripts.Render("~/bundles/bootstrap")` por un `<script>` elemento (vea más abajo).

El marcado de reemplazo para la inclusión de CSS de bootstrap:

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

El marcado de reemplazo para jQuery y bootstrap JavaScript incluirá:

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

El archivo *_Layout. cshtml* actualizado se muestra a continuación:

[!code-cshtml[](mvc/sample/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

Vea el sitio en el explorador. Ahora debería cargarse correctamente, con los estilos esperados en su lugar.

* *Opcional:* Es posible que desee intentar usar el nuevo archivo de diseño. Para este proyecto, puede copiar el archivo de diseño del proyecto *FullAspNetCore* . El nuevo archivo de diseño utiliza [aplicaciones auxiliares de etiquetas](xref:mvc/views/tag-helpers/intro) y otras mejoras.

## <a name="configure-bundling-and-minification"></a>Configuración de la unión y la minimización

Para obtener información acerca de cómo configurar la agrupación y la minificación, consulte [agrupación y minificación](../client-side/bundling-and-minification.md).

## <a name="solve-http-500-errors"></a>Solucionar errores HTTP 500

Hay muchos problemas que pueden provocar un mensaje de error HTTP 500 que no contiene información sobre el origen del problema. Por ejemplo, si el archivo *views/_ViewImports. cshtml* contiene un espacio de nombres que no existe en el proyecto, obtendrá un error http 500. De forma predeterminada, en ASP.NET Core aplicaciones `UseDeveloperExceptionPage` , la extensión se agrega `IApplicationBuilder` a y se ejecuta cuando la configuración es *desarrollo*. Esto se detalla en el código siguiente:

[!code-csharp[](mvc/sample/Startup.cs?highlight=19-22)]

ASP.NET Core convierte las excepciones no controladas en una aplicación web en respuestas de error HTTP 500. Normalmente, los detalles del error no se incluyen en estas respuestas para evitar la divulgación de información potencialmente confidencial sobre el servidor. Consulte **uso de la página de excepción para desarrolladores** en [controlar errores](../fundamentals/error-handling.md) para obtener más información.

## <a name="additional-resources"></a>Recursos adicionales

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>
