---
title: Archivos estáticos en ASP.NET Core
author: rick-anderson
description: Aprenda a proporcionar y proteger los archivos estáticos y a configurar los comportamientos de middleware de hospedaje de archivos estáticos en una aplicación web de ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/07/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/static-files
ms.openlocfilehash: f7697260e6ab29d0e9ba955dfdf0c8c81e4e1130
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775497"
---
# <a name="static-files-in-aspnet-core"></a>Archivos estáticos en ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT) y [Scott Addie](https://twitter.com/Scott_Addie)

Los archivos estáticos, como HTML, CSS, imágenes y JavaScript, son activos que una aplicación de ASP.NET Core proporciona directamente a los clientes. Se necesita alguna configuración para habilitar el servicio de estos archivos.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))

## <a name="serve-static-files"></a>Proporcionar archivos estáticos

Los archivos estáticos se almacenan en el directorio [raíz web](xref:fundamentals/index#web-root) del proyecto. El directorio predeterminado es *{raíz del contenido}/wwwroot*, pero se puede cambiar con el método [UseWebRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usewebroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseWebRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_). Vea [Raíz del contenido](xref:fundamentals/index#content-root) y [Raíz web](xref:fundamentals/index#web-root) para obtener más información.

El host de web de la aplicación debe tener conocimiento del directorio raíz del contenido.

::: moniker range=">= aspnetcore-2.0"

El método `WebHost.CreateDefaultBuilder` establece la raíz de contenido en el directorio actual:

[!code-csharp[](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main&highlight=9)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Establezca la raíz de contenido en el directorio actual invocando a [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseContentRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) dentro de `Program.Main`:

[!code-csharp[](static-files/samples/1x/Program.cs?name=snippet_ProgramClass&highlight=7)]

::: moniker-end

Se puede acceder a los archivos estáticos a través de una ruta de acceso relativa a la [raíz web](xref:fundamentals/index#web-root). Por ejemplo, la plantilla de proyecto **Aplicación web** contiene varias carpetas dentro de la carpeta *wwwroot*:

* **wwwroot**
  * **css**
  * **images**
  * **js**

El formato de URI para acceder a un archivo en la subcarpeta *images* es *http://\<dirección_servidor>/images/\<nombre_archivo_imagen>* . Por ejemplo, *http://localhost:9189/images/banner3.svg* .

::: moniker range=">= aspnetcore-2.1"

Si el destino es .NET Framework, agregue el paquete [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) al proyecto. Si el destino es .NET Core, el [metapaquete Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) incluye este paquete.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Si el destino es .NET Framework, agregue el paquete [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) al proyecto. Si el destino es .NET Core, el [metapaquete Microsoft.AspNetCore.All](xref:fundamentals/metapackage) incluye este paquete.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Agregue el paquete [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) al proyecto.

::: moniker-end

Configure el [middleware](xref:fundamentals/middleware/index), que permite proporcionar archivos estáticos.

### <a name="serve-files-inside-of-web-root"></a>Proporcionar archivos dentro de la raíz web

Invoque el método [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) dentro de `Startup.Configure`:

[!code-csharp[](static-files/samples/1x/StartupStaticFiles.cs?name=snippet_ConfigureMethod&highlight=3)]

La sobrecarga del método `UseStaticFiles` sin parámetros marca los archivos en la [raíz web](xref:fundamentals/index#web-root) como que se pueden proporcionar. El siguiente marcado hace referencia a *wwwroot/images/banner1.svg*:

[!code-cshtml[](static-files/samples/1x/Views/Home/Index.cshtml?name=snippet_static_file_wwwroot)]

En el código anterior, el carácter de tilde de la ñ `~/` apunta a la [raíz web](xref:fundamentals/index#web-root).

### <a name="serve-files-outside-of-web-root"></a>Proporcionar archivos fuera de la raíz web

Considere una jerarquía de directorios en la que residen fuera de la [raíz web](xref:fundamentals/index#web-root) los archivos estáticos que se van a proporcionar:

* **wwwroot**
  * **css**
  * **images**
  * **js**
* **MyStaticFiles**
  * **images**
    * *banner1.svg*

Una solicitud puede acceder al archivo *banner1.svg* configurando el middleware de archivos estáticos como se muestra a continuación:

[!code-csharp[](static-files/samples/1x/StartupTwoStaticFiles.cs?name=snippet_ConfigureMethod&highlight=5-10)]

En el código anterior, la jerarquía del directorio *MyStaticFiles* se expone públicamente a través del segmento de URI *StaticFiles*. Una solicitud a *http://\<dirección_servidor>/StaticFiles/images/banner1.svg* proporciona el archivo *banner1.svg*.

El siguiente marcado hace referencia a *MyStaticFiles/images/banner1.svg*:

[!code-cshtml[](static-files/samples/1x/Views/Home/Index.cshtml?name=snippet_static_file_outside)]

### <a name="set-http-response-headers"></a>Establecer encabezados de respuesta HTTP

Se puede usar un objeto [StaticFileOptions](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions) para establecer encabezados de respuesta HTTP. Además de configurar el servicio de archivos estáticos desde la [raíz web](xref:fundamentals/index#web-root), el código siguiente establece el encabezado `Cache-Control`:

[!code-csharp[](static-files/samples/1x/StartupAddHeader.cs?name=snippet_ConfigureMethod)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

El método [HeaderDictionaryExtensions.Append](/dotnet/api/microsoft.aspnetcore.http.headerdictionaryextensions.append) existe en el paquete [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/).

Los archivos se han hecho públicamente almacenables en caché durante 10 minutos (600 segundos) en el entorno de desarrollo:

![Se han agregado encabezados de respuesta que muestran el encabezado Cache-Control](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a>Autorización de archivos estáticos

El middleware de archivos estáticos no proporciona comprobaciones de autorización. Los archivos que proporciona, incluidos los de *wwwroot*, están accesibles de forma pública. Para proporcionar archivos según su autorización:

* Almacénelos fuera de *wwwroot* y cualquier directorio al que el middleware de archivos estáticos tenga acceso.
* Proporciónelos a través de un método de acción al que se aplica la autorización. Devuelva un objeto [FileResult](/dotnet/api/microsoft.aspnetcore.mvc.fileresult):

  [!code-csharp[](static-files/samples/1x/Controllers/HomeController.cs?name=snippet_BannerImageAction)]

## <a name="enable-directory-browsing"></a>Habilite el examen de directorios

El examen de directorios permite a los usuarios de su aplicación web ver una lista de directorios y archivos contenidos en un directorio especificado. Por motivos de seguridad, el examen de directorios está deshabilitado de forma predeterminada (consulte [Consideraciones](#considerations)). Habilitar el examen de directorios invocando el método [UseDirectoryBrowser](/dotnet/api/microsoft.aspnetcore.builder.directorybrowserextensions.usedirectorybrowser#Microsoft_AspNetCore_Builder_DirectoryBrowserExtensions_UseDirectoryBrowser_Microsoft_AspNetCore_Builder_IApplicationBuilder_Microsoft_AspNetCore_Builder_DirectoryBrowserOptions_) en `Startup.Configure`:

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=12-17)]

Agregar servicios requeridos invocando el método [AddDirectoryBrowser](/dotnet/api/microsoft.extensions.dependencyinjection.directorybrowserserviceextensions.adddirectorybrowser#Microsoft_Extensions_DependencyInjection_DirectoryBrowserServiceExtensions_AddDirectoryBrowser_Microsoft_Extensions_DependencyInjection_IServiceCollection_) desde `Startup.ConfigureServices`:

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureServicesMethod&highlight=3)]

El código anterior permite el examen de directorios de la carpeta *wwwroot/images* usando la dirección URL *http://\<dirección_servidor>/MyImages*, con vínculos a cada archivo y carpeta:

![examen de directorios](static-files/_static/dir-browse.png)

Vea [consideraciones](#considerations) sobre los riesgos de seguridad al habilitar el examen.

Tenga en cuenta las dos llamadas a `UseStaticFiles` en el ejemplo siguiente. La primera llamada permite proporcionar archivos estáticos en la carpeta *wwwroot*. La segunda llamada habilita el examen de directorios de la carpeta *wwwroot/images* usando la dirección URL *http://\<dirección_servidor>/MyImages*:

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=3,5)]

## <a name="serve-a-default-document"></a>Proporcionar un documento predeterminado

Establecer una página principal predeterminada proporciona a los visitantes un punto de partida lógico cuando visitan su sitio. Para proporcionar una página predeterminada sin que el usuario cumpla por completo los requisitos del URI, llame al método [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) desde `Startup.Configure`:

[!code-csharp[](static-files/samples/1x/StartupEmpty.cs?name=snippet_ConfigureMethod&highlight=3)]

> [!IMPORTANT]
> Debe llamarse a `UseDefaultFiles` antes de a `UseStaticFiles` para proporcionar el archivo predeterminado. `UseDefaultFiles` es un sistema de reescritura de direcciones URL que no proporciona realmente el archivo. Habilite el middleware de archivos estáticos a través de `UseStaticFiles` para proporcionar el archivo.

Con `UseDefaultFiles`, las solicitudes a una carpeta buscan:

* *default.htm*
* *default.html*
* *index.htm*
* *index.html*

El primer archivo que se encuentra en la lista se proporciona como si la solicitud fuera el URI completo. La dirección URL del explorador sigue reflejando el URI solicitado.

El código siguiente cambia el nombre de archivo predeterminado a *mydefault.html*:

[!code-csharp[](static-files/samples/1x/StartupDefault.cs?name=snippet_ConfigureMethod)]

## <a name="usefileserver"></a>UseFileServer

<xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> combina la funcionalidad de `UseStaticFiles` y `UseDefaultFiles` y, opcionalmente, la de `UseDirectoryBrowser`.

El código siguiente permite proporcionar archivos estáticos y el archivo predeterminado. El examen de directorios no está habilitado.

```csharp
app.UseFileServer();
```

El código siguiente refuerza la sobrecarga sin parámetros habilitando el examen de directorios:

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

Tenga en cuenta la siguiente jerarquía de directorios:

* **wwwroot**
  * **css**
  * **images**
  * **js**
* **MyStaticFiles**
  * **images**
    * *banner1.svg*
  * *default.html*

El código siguiente permite los archivos estáticos, los archivos predeterminados y el examen de directorios de `MyStaticFiles`:

[!code-csharp[](static-files/samples/1x/StartupUseFileServer.cs?name=snippet_ConfigureMethod&highlight=5-11)]

Se debe llamar a `AddDirectoryBrowser` cuando el valor de la propiedad `EnableDirectoryBrowsing` es `true`:

[!code-csharp[](static-files/samples/1x/StartupUseFileServer.cs?name=snippet_ConfigureServicesMethod)]

Al usar la jerarquía de archivos y el código anterior, las direcciones URL se resuelven como se indica a continuación:

| Identificador URI            |                             Respuesta  |
| ------- | ------|
| *http://\<dirección_servidor>/StaticFiles/images/banner1.svg*    |      MyStaticFiles/images/banner1.svg |
| *http://\<dirección_servidor>/StaticFiles*             |     MyStaticFiles/default.html |

Si no existe ningún archivo con el nombre predeterminado en el directorio *MyStaticFiles*, *http://\<dirección_servidor>/StaticFiles* devuelve la lista de directorios con vínculos activos:

![Lista de archivos estáticos](static-files/_static/db2.png)

> [!NOTE]
> <xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> y <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> realizan un redireccionamiento del lado cliente desde `http://{SERVER ADDRESS}/StaticFiles` (sin una barra diagonal final) hasta `http://{SERVER ADDRESS}/StaticFiles/` (con una barra diagonal final). Las direcciones URL relativas dentro del directorio *StaticFiles* no son válidas sin una barra diagonal final.

## <a name="fileextensioncontenttypeprovider"></a>FileExtensionContentTypeProvider

La clase [FileExtensionContentTypeProvider](/dotnet/api/microsoft.aspnetcore.staticfiles.fileextensioncontenttypeprovider) contiene una propiedad `Mappings` que actúa como una asignación de extensiones de archivo para tipos de contenido MIME. En el ejemplo siguiente, se registran varias extensiones de archivo a los tipos MIME conocidos. Se reemplaza la extensión *.rtf* y se quita *.mp4*.

[!code-csharp[](static-files/samples/1x/StartupFileExtensionContentTypeProvider.cs?name=snippet_ConfigureMethod&highlight=3-12,19)]

Vea [Tipos de contenido MIME](https://www.iana.org/assignments/media-types/media-types.xhtml).

## <a name="non-standard-content-types"></a>Tipos de contenido no estándar

El middleware de archivos estáticos entiende casi 400 tipos de contenido de archivo conocidos. Si el usuario solicita un archivo con un tipo de archivo desconocido, el middleware de archivos estáticos pasa la solicitud al siguiente middleware de la canalización. Si ningún middleware se ocupa de la solicitud, se devuelve una respuesta *404 No encontrado*. Si se habilita la exploración de directorios, se muestra un vínculo al archivo en una lista de directorios.

El código siguiente permite proporcionar tipos desconocidos y procesa el archivo desconocido como una imagen:

[!code-csharp[](static-files/samples/1x/StartupServeUnknownFileTypes.cs?name=snippet_ConfigureMethod)]

Con el código anterior, una solicitud para un archivo con un tipo de contenido desconocido se devuelve como una imagen.

> [!WARNING]
> Habilitar [ServeUnknownFileTypes](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions.serveunknownfiletypes#Microsoft_AspNetCore_Builder_StaticFileOptions_ServeUnknownFileTypes) supone un riesgo para la seguridad. Está deshabilitado de forma predeterminada y no se recomienda su uso. [FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) proporciona una alternativa más segura a ofrecer archivos con extensiones no estándar.

## <a name="serve-files-from-multiple-locations"></a>Entrega de archivos desde varias ubicaciones

`UseStaticFiles` y `UseFileServer` tienen como valor predeterminado el proveedor de archivos que apunta a *wwwroot*. Puede proporcionar instancias adicionales de `UseStaticFiles` y `UseFileServer` con otros proveedores de archivos para proporcionar archivos desde otras ubicaciones. Para más información, consulte [este problema de GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/15578).

### <a name="considerations"></a>Consideraciones

> [!WARNING]
> `UseDirectoryBrowser` y `UseStaticFiles` pueden producir pérdidas de información confidencial. Se recomienda deshabilitar el examen de directorios en producción. Revise cuidadosamente los directorios que se habilitan mediante `UseStaticFiles` o `UseDirectoryBrowser`. Todo el directorio y sus subdirectorios pasan a ser accesibles públicamente. Almacene los archivos adecuados para proporcionarlos al público en un directorio dedicado, como *\<raíz_contenido>/wwwroot*. Separe estos archivos de las vistas MVC, las instancias de Razor Pages (solo 2.x), los archivos de configuración, etc.

* Las direcciones URL para el contenido que se expone a través de `UseDirectoryBrowser` y `UseStaticFiles` están sujetas a la distinción entre mayúsculas y minúsculas, y a restricciones de caracteres del sistema de archivos subyacente. Por ejemplo, Windows no distingue entre mayúsculas y minúsculas, pero macOS y Linux sí.

* Las aplicaciones de ASP.NET Core hospedadas en IIS usan el [módulo de ASP.NET Core](xref:host-and-deploy/aspnet-core-module) para reenviar todas las solicitudes a la aplicación, incluidas las solicitudes de archivos estáticos. No se usa el controlador de archivos estáticos de IIS. No tiene ninguna posibilidad de controlar las solicitudes antes de que las controle el módulo.

* Complete los pasos siguientes en el Administrador de IIS para quitar el controlador de archivos estáticos de IIS en el nivel de servidor o de sitio web:
    1. Navegue hasta la característica **Módulos**.
    1. En la lista, seleccione **StaticFileModule**.
    1. Haga clic en **Quitar** en la barra lateral **Acciones**.

> [!WARNING]
> Si el controlador de archivos estáticos de IIS está habilitado **y** el módulo de ASP.NET Core no está configurado correctamente, se proporcionan archivos estáticos. Esto sucede, por ejemplo, si el archivo *web.config* no está implementado.

* Coloque los archivos de código (incluidos *.cs* y *.cshtml*) fuera de la [raíz web](xref:fundamentals/index#web-root) del proyecto de la aplicación. Por lo tanto, se crea una separación lógica entre el contenido del lado cliente de la aplicación y el código basado en servidor. Esto impide que se filtre el código del lado servidor.

## <a name="additional-resources"></a>Recursos adicionales

* [Middleware](xref:fundamentals/middleware/index)
* [Introducción a ASP.NET Core](xref:index)
