---
title: 'title: "Hospedaje e implementación de ASP.NET Core Blazor WebAssembly" author: guardrex description: "Aprenda a hospedar e implementar una aplicación Blazor con ASP.NET Core, redes de entrega de contenido (CDN), servidores de archivos y páginas de GitHub".'
author: guardrex
description: "monikerRange: '>= aspnetcore-3.1' ms.author: riande ms.custom: mvc ms.date: 05/28/2020 no-loc:"
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/28/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/blazor/webassembly
ms.openlocfilehash: 09f74edaa3d1cb0d51e0ce8d0209383885b81f5f
ms.sourcegitcommit: 2e9973cea5c36d0dd64d5e946bb776b8bb73a4b6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/01/2020
ms.locfileid: "84239395"
---
# <a name="host-and-deploy-aspnet-core-blazor-webassembly"></a>"Blazor"

"Identity"

"Let's Encrypt"

* "Razor"
* "SignalR" uid: host-and-deploy/blazor/webassembly

Hospedaje e implementación de ASP.NET CoreBlazor WebAssembly

* Por [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams) y [Safia Abdalla](https://safia.rocks) Con el modelo de hospedaje de [Blazor WebAssembly ](xref:blazor/hosting-models#blazor-webassembly):
* La aplicación Blazor, sus dependencias y el entorno de ejecución de .NET se descargan en el explorador en paralelo. La aplicación se ejecuta directamente en el subproceso de interfaz de usuario del explorador.

## <a name="precompression"></a>Se admiten las estrategias de implementación siguientes:

Una aplicación ASP.NET Core suministra la aplicación Blazor. Esta estrategia se trata en la sección [Implementación hospedada con ASP.NET Core](#hosted-deployment-with-aspnet-core).

* La aplicación Blazor se coloca en un servicio o servidor web de hospedaje estático, donde no se usa .NET para suministrar la aplicación Blazor.
* Esta estrategia se trata en la sección [Implementación independiente](#standalone-deployment), que incluye información sobre cómo hospedar una aplicación Blazor WebAssembly como una subaplicación de IIS.

Precompresión

```xml
<PropertyGroup>
  <BlazorEnableCompression>false</BlazorEnableCompression>
</PropertyGroup>
```

Cuando se publica una aplicación de Blazor WebAssembly, la salida se comprime previamente para disminuir el tamaño de la aplicación y quitar la necesidad de compresión en tiempo de ejecución.

## <a name="rewrite-urls-for-correct-routing"></a>Se usan los algoritmos de compresión siguientes:

[Brotli](https://tools.ietf.org/html/rfc7932) (el nivel más alto) [Gzip](https://tools.ietf.org/html/rfc1952)

* Para deshabilitar la compresión, agregue la propiedad `BlazorEnableCompression` de MSBuild al archivo del proyecto de la aplicación y establezca el valor en `false`:
* Para ver la configuración de compresión de *web.config* de IIS, vea la sección [IIS: compresión Brotli y Gzip](#brotli-and-gzip-compression).

Reescritura de las URL para conseguir un enrutamiento correcto

1. Enrutar las solicitudes de los componentes de página de una aplicación Blazor WebAssembly no es tan sencillo como enrutar las solicitudes de una aplicación Blazor Server hospedada.
1. Se recomienda usar una aplicación Blazor WebAssembly con dos componentes:
1. *Main.razor*: se carga en la raíz de la aplicación y contiene un vínculo al componente `About` (`href="About"`).
1. *About.razor*: el componente `About`.

Cuando se solicita el documento predeterminado de la aplicación mediante la barra de direcciones del explorador (por ejemplo, `https://www.contoso.com/`): El explorador realiza una solicitud. Se devuelve la página predeterminada, que suele ser *index.html*.

*index.html* arranca la aplicación. Se carga el enrutador de Blazor y se representa el componente `Main` de Razor.

En la página principal, la selección del vínculo al componente `About` funciona en el cliente porque el enrutador de Blazor impide que el explorador haga una solicitud en Internet a `www.contoso.com` sobre `About` y presenta el propio componente `About` representado. Todas las solicitudes de puntos de conexión internos *dentro de la aplicación Blazor WebAssembly* funcionan del mismo modo: Las solicitudes no desencadenan solicitudes basadas en el explorador a recursos hospedados en el servidor en Internet.

El enrutador controla las solicitudes de forma interna. Si se realiza una solicitud mediante la barra de direcciones del explorador para `www.contoso.com/About`, se produce un error.

## <a name="hosted-deployment-with-aspnet-core"></a>Este recurso no existe en el host de Internet de la aplicación, por lo que se devuelve una respuesta *404 No encontrado*.

Dado que los exploradores realizan solicitudes a hosts basados en Internet para las páginas del lado cliente, los servidores web y los servicios de hospedaje deben reescribir todas las solicitudes de recursos que no estén físicamente en el servidor a la página *index.html*.

Cuando se devuelve *index.html*, el enrutador Blazor de la aplicación se hace cargo y responde con el recurso correcto. Al implementar en un servidor IIS, puede usar el módulo URL Rewrite con el archivo *web.config* publicado de la aplicación. Para más información, consulte la sección sobre [IIS](#iis). Implementación hospedada con ASP.NET Core

Una *implementación hospedada* se encarga de suministrar la aplicación Blazor WebAssembly a los exploradores desde una [aplicación ASP.NET Core](xref:index) que se ejecuta en un servidor web.

La aplicación WebAssembly del cliente Blazor se publica en la carpeta */bin/Release/{MARCO DE DESTINO}/publish/wwwroot* de la aplicación de servidor, junto con cualquier otro recurso web estático de la aplicación de servidor.

## <a name="standalone-deployment"></a>Las dos aplicaciones se implementan juntas.

Se requiere un servidor web que pueda hospedar una aplicación ASP.NET Core. En el caso de una implementación hospedada, Visual Studio incluye la plantilla de proyecto **Blazor WebAssembly App** (la plantilla `blazorwasm` al usar el comando [dotnet new](/dotnet/core/tools/dotnet-new)) con la opción **Hosted** (Hospedada) seleccionada (`-ho|--hosted` al usar el comando `dotnet new`).

Para obtener más información sobre la implementación y el hospedaje de aplicaciones de ASP.NET Core, consulte <xref:host-and-deploy/index>.

### <a name="azure-app-service"></a>Para obtener información sobre cómo implementar en Azure App Service, vea <xref:tutorials/publish-to-azure-webapp-using-vs>.

Implementación independiente

Una *implementación independiente* suministra la aplicación Blazor WebAssembly como un conjunto de archivos estáticos que los clientes solicitan directamente. Cualquier servidor de archivos estático es capaz de suministrar la aplicación Blazor. Los activos de implementación independientes se publican en la carpeta */bin/Release/{MARCO DE DESTINO}/publish/wwwroot*.

### <a name="iis"></a>Azure App Service

Las aplicaciones Blazor WebAssembly se pueden implementar en Azure App Service en Windows, que hospeda la aplicación en [IIS](#iis). Actualmente no se admite la implementación de una aplicación Blazor WebAssembly independiente en Azure App Service para Linux.

En este momento no hay disponible una imagen de servidor de Linux para hospedar la aplicación. Se está trabajando para habilitar este escenario.

#### <a name="webconfig"></a>IIS

IIS es un servidor de archivos estáticos compatible con las aplicaciones Blazor.

* Para configurar IIS para hospedar Blazor, consulte [Compilación de un sitio web estático en IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).
  * Los recursos publicados se crean en la carpeta */bin/Release/{TARGET FRAMEWORK}/publish*.
  * Hospede el contenido de la carpeta *publish* en el servidor web o el servicio de hospedaje.
  * web.config
  * Cuando se publica un proyecto de Blazor, se crea un archivo *web.config* con la siguiente configuración de IIS:
  * Se establecen los tipos MIME de las siguientes extensiones de archivo:
* *.dll*: `application/octet-stream`
  * `application/octet-stream`
  * `application/wasm`
* *.json*: `application/json`
  * *.wasm*: `application/wasm`
  * *.woff*: `application/font-woff`
  
#### <a name="use-a-custom-webconfig"></a>*.woff2*: `application/font-woff`

Se habilita la compresión HTTP de los siguientes tipos MIME:

#### <a name="install-the-url-rewrite-module"></a>Se establecen reglas del módulo URL Rewrite:

Proporcionar el subdirectorio donde residen los recursos estáticos de la aplicación (*wwwroot/{RUTA SOLICITADA}* ). Crear el enrutamiento de reserva de SPA para que las solicitudes de recursos que no sean archivos se redirijan al documento predeterminado de la aplicación en su carpeta de recursos estáticos (*wwwroot/index.html*). Uso de un archivo web.config personalizado Para usar un archivo *web.config* personalizado, coloque el archivo *web.config* personalizado en la raíz de la carpeta del proyecto y publique el proyecto.

1. Instalación del módulo URL Rewrite El [módulo URL Rewrite](https://www.iis.net/downloads/microsoft/url-rewrite) es necesario para reescribir las URL. El módulo no se instala de forma predeterminada y no está disponible para instalar como una característica de servicio de rol del servidor web (IIS).
1. El módulo se debe descargar desde el sitio web de IIS. Use el instalador de plataforma web para instalar el módulo: De forma local, vaya a la [página de descargas del módulo URL Rewrite](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads). En el caso de la versión en inglés, seleccione **WebPI** para descargar el instalador de WebPI.

#### <a name="configure-the-website"></a>En el caso de otros idiomas, seleccione la arquitectura adecuada del servidor (x86/x64) para descargar el instalador.

Copie el instalador en el servidor. Ejecute el instalador.

* Haga clic en el botón **Instalar** y acepte los términos de licencia.
* No es necesario reiniciar el servidor al finalizar la instalación.

#### <a name="host-as-an-iis-sub-app"></a>Configuración del sitio web

Configure la **ruta de acceso física** del sitio web a la carpeta de la aplicación.

* La carpeta contiene:

  El archivo *web.config* que usa IIS para configurar el sitio web, incluidas las reglas de redireccionamiento y los tipos de contenido de archivos necesarios.

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* La carpeta de recursos estáticos de la aplicación.

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <handlers>
          <add name="aspNetCore" ... />
        </handlers>
        <aspNetCore ... />
      </system.webServer>
    </location>
  </configuration>
  ```

Hospedaje como subaplicación de IIS Si una aplicación independiente se hospeda como una subaplicación de IIS, realice una de las siguientes acciones:

#### <a name="brotli-and-gzip-compression"></a>Deshabilite el controlador del módulo de ASP.NET Core heredado.

Para quitar el controlador del archivo Blazorweb.config*publicado de la aplicación*, agregue una sección `<handlers>` al archivo: Deshabilite la herencia de la sección `<system.webServer>` de la aplicación raíz (principal) mediante un elemento `<location>` con `inheritInChildApplications` establecido en `false`:

#### <a name="troubleshooting"></a>Además de [configurarse la ruta de acceso base de la aplicación](xref:host-and-deploy/blazor/index#app-base-path), se quita el controlador o se deshabilita la herencia.

Establezca la ruta de acceso base de la aplicación en el archivo *index.html* de la aplicación en el alias de IIS que ha usado al configurar la subaplicación en IIS. Compresión Brotli y Gzip IIS se puede configurar a través de *web.config* para dar servicio a activos de Blazor comprimidos con Brotli o Gzip.

Para ver una configuración de ejemplo, vea [web.config](webassembly/_samples/web.config?raw=true).

### <a name="azure-storage"></a>Solución de problemas

Si se recibe un error *500 Error interno del servidor* y el administrador de IIS produce errores al intentar acceder a la configuración del sitio web, confirme que el módulo URL Rewrite está instalado. Si no lo está, IIS no puede analizar el archivo *web.config*.

Esto impide que el Administrador de IIS cargue la configuración del sitio web y que el sitio web suministre los archivos estáticos de Blazor.

* Para obtener más información sobre cómo solucionar problemas de las implementaciones en IIS, vea <xref:test/troubleshoot-azure-iis>.
* Almacenamiento de Azure El hospedaje de archivos estáticos de [Azure Storage](/azure/storage/) permite el hospedaje de aplicaciones Blazor sin servidor. Se admiten nombres de dominio personalizados, Azure Content Delivery Network (CDN) y HTTPS. Cuando el servicio de blob está habilitado para el hospedaje de sitios web estáticos en una cuenta de almacenamiento:

Establece el **nombre de documento de índice** en `index.html`.

* Establece la **ruta de acceso del documento de error** en `index.html`.
* Los componentes Razor y otros puntos de conexión que no son de archivo no residen en las rutas de acceso físicas del contenido estático almacenado por el servicio de blob.

  Cuando se recibe una solicitud de uno de estos recursos que debe controlar el enrutador de Blazor, el error *404 - No encontrado* generado por el servicio de blob enruta la solicitud a la **ruta de acceso del documento de error**.
  
  1. Se devuelve el blob *index.html* y el enrutador de Blazor carga y procesa la ruta de acceso.
  1. Si los archivos no se cargan en tiempo de ejecución debido a tipos MIME inadecuados en los encabezados `Content-Type` de los archivos, haga algunas de las acciones siguientes:

Configure las herramientas para establecer los tipos MIME correctos (encabezados `Content-Type`) cuando se implementen los archivos.

### <a name="nginx"></a>Cambie los tipos MIME (encabezados `Content-Type`) de los archivos una vez que se implementa la aplicación.

En cada archivo del Explorador de Storage (Azure Portal) haga lo siguiente:

```
events { }
http {
    server {
        listen 80;

        location / {
            root /usr/share/nginx/html;
            try_files $uri $uri/ /index.html =404;
        }
    }
}
```

Haga clic con el botón derecho en el archivo y seleccione **Propiedades**.

### <a name="nginx-in-docker"></a>Establezca el valor de **ContentType** y seleccione el botón **Guardar**.

Para más información, consulte [Hospedaje de sitios web estáticos en Azure Storage](/azure/storage/blobs/storage-blob-static-website). Nginx

El siguiente archivo *nginx.conf* se ha simplificado para mostrar cómo configurar Nginx para enviar el archivo *index.html* siempre que no pueda encontrar un archivo correspondiente en el disco.

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a>Para obtener más información sobre la configuración del servidor web de producción de Nginx, consulte [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/) (Creación de archivos de configuración de NGINX y NGINX Plus).

Nginx en Docker

1. Para hospedar Blazor en Docker mediante Nginx, configure Dockerfile para usar la imagen de Nginx basada en Alpine. Actualice el Dockerfile para copiar el archivo *nginx.config* en el contenedor.

   ```
   <VirtualHost *:80>
       ServerName www.example.com
       ServerAlias *.example.com

       DocumentRoot "/var/www/blazorapp"
       ErrorDocument 404 /index.html

       AddType application/wasm .wasm
       AddType application/octet-stream .dll
   
       <Directory "/var/www/blazorapp">
           Options -Indexes
           AllowOverride None
       </Directory>

       <IfModule mod_deflate.c>
           AddOutputFilterByType DEFLATE text/css
           AddOutputFilterByType DEFLATE application/javascript
           AddOutputFilterByType DEFLATE text/html
           AddOutputFilterByType DEFLATE application/octet-stream
           AddOutputFilterByType DEFLATE application/wasm
           <IfModule mod_setenvif.c>
           BrowserMatch ^Mozilla/4 gzip-only-text/html
           BrowserMatch ^Mozilla/4.0[678] no-gzip
           BrowserMatch bMSIE !no-gzip !gzip-only-text/html
       </IfModule>
       </IfModule>

       ErrorLog /var/log/httpd/blazorapp-error.log
       CustomLog /var/log/httpd/blazorapp-access.log common
   </VirtualHost>
   ```

1. Agregue una línea al Dockerfile, como se muestra en el ejemplo siguiente:

1. Apache

1. Para implementar una aplicación Blazor WebAssembly en CentOS 7 o posterior:

Cree el archivo de configuración de Apache.

### <a name="github-pages"></a>El siguiente ejemplo es un archivo de configuración simplificado (*blazorapp.config*):

Coloque el archivo de configuración de Apache en el directorio `/etc/httpd/conf.d/`, que es el directorio de configuración de Apache predeterminado en CentOS 7. Coloque los archivos de la aplicación en el directorio `/var/www/blazorapp` (la ubicación especificada para `DocumentRoot` en el archivo de configuración). Reinicie el servicio de Apache.

Para obtener más información, vea [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) y [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html). GitHub Pages

## <a name="host-configuration-values"></a>Para controlar las reescrituras de URL, agregue un archivo *404.html* con un script que controle el redireccionamiento de la solicitud a la página *index.html*.

Para consultar una implementación de ejemplo que ha proporcionado la comunidad, vea [Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)) (Aplicaciones de página única para GitHub Pages [rafrex/spa-github-pages en GitHub]).

### <a name="content-root"></a>Se puede ver un ejemplo del enfoque de la comunidad en [blazor-demo/blazor-demo.github.io en GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([sitio activo](https://blazor-demo.github.io/)).

Al usar un sitio de proyecto en lugar de un sitio de la organización, agregue o actualice la etiqueta `<base>` en *index.html*. Defina el valor del atributo `href` con el nombre del repositorio de GitHub con una barra diagonal final (por ejemplo, `my-repository/`).

* Valores de configuración de host Las aplicaciones [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) pueden aceptar los siguientes valores de configuración de host como argumentos de línea de comandos en tiempo de ejecución en el entorno de desarrollo.

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* Raíz del contenido El argumento `--contentroot` establece la ruta de acceso absoluta al directorio que incluye los archivos de contenido de la aplicación ([raíz del contenido](xref:fundamentals/index#content-root)).

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* En los ejemplos siguientes, `/content-root-path` es la ruta de acceso raíz del contenido de la aplicación. Pase el argumento al ejecutar la aplicación de forma local en un símbolo del sistema.

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a>En el directorio de la aplicación, ejecute lo siguiente:

Agregue una entrada al archivo *launchSettings.json* de la aplicación en el perfil **IIS Express**. Esta configuración se utiliza cuando se ejecuta la aplicación mediante el depurador de Visual Studio y desde un símbolo del sistema con `dotnet run`. En Visual Studio, especifique el argumento en **Propiedades** > **Depuración** > **Argumentos de la aplicación**.

> [!IMPORTANT]
> Al establecer el argumento en la página de propiedades de Visual Studio, se agrega el argumento al archivo *launchSettings.json*. Ruta de acceso base

* El argumento `--pathbase` establece la ruta de acceso base de la aplicación para una aplicación que se ejecuta localmente con una ruta de acceso de URL relativa que no es raíz (el valor `href` de la etiqueta `<base>` se establece en una ruta de acceso que no sea `/` para ensayo y producción). En los ejemplos siguientes, `/relative-URL-path` es la ruta de acceso base de la aplicación.

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* Para obtener más información, vea [Ruta de acceso base de la aplicación](xref:host-and-deploy/blazor/index#app-base-path). A diferencia de la ruta de acceso proporcionada al valor `href` de la etiqueta `<base>`, no incluya una barra diagonal final (`/`) al pasar el valor del argumento `--pathbase`.

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* Si se proporciona la ruta de acceso base de la aplicación en la etiqueta `<base>` como `<base href="/CoolApp/">` (se incluye una barra diagonal final), se pasa el valor del argumento de línea de comandos como `--pathbase=/CoolApp` (sin barra diagonal final). Pase el argumento al ejecutar la aplicación de forma local en un símbolo del sistema.

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a>En el directorio de la aplicación, ejecute lo siguiente:

Agregue una entrada al archivo *launchSettings.json* de la aplicación en el perfil **IIS Express**.

* Esta configuración se utiliza cuando se ejecuta la aplicación mediante el depurador de Visual Studio y desde un símbolo del sistema con `dotnet run`. En Visual Studio, especifique el argumento en **Propiedades** > **Depuración** > **Argumentos de la aplicación**.

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* Al establecer el argumento en la página de propiedades de Visual Studio, se agrega el argumento al archivo *launchSettings.json*. Direcciones URL

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* El argumento `--urls` establece las direcciones IP o las direcciones de host con los puertos y protocolos en los que escuchar las solicitudes. Pase el argumento al ejecutar la aplicación de forma local en un símbolo del sistema.

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="configure-the-linker"></a>En el directorio de la aplicación, ejecute lo siguiente:

Agregue una entrada al archivo *launchSettings.json* de la aplicación en el perfil **IIS Express**. Esta configuración se utiliza cuando se ejecuta la aplicación mediante el depurador de Visual Studio y desde un símbolo del sistema con `dotnet run`.

## <a name="custom-boot-resource-loading"></a>En Visual Studio, especifique el argumento en **Propiedades** > **Depuración** > **Argumentos de la aplicación**.

Al establecer el argumento en la página de propiedades de Visual Studio, se agrega el argumento al archivo *launchSettings.json*. Configurar el enlazador

* Blazor realiza la vinculación de lenguaje intermedio (IL) en cada compilación de lanzamiento para quitar el IL innecesario de los ensamblados de salida.
* Para obtener más información, vea <xref:host-and-deploy/blazor/configure-linker>.
* Carga de recursos de arranque personalizados

Una aplicación Blazor WebAssembly se puede inicializar con la función `loadBootResource` para invalidar el mecanismo de carga de recursos de arranque integrado.

| Use `loadBootResource` en los siguientes escenarios:    | Para permitir a los usuarios cargar recursos estáticos, como datos de zona horaria o *dotnet.wasm* desde una red CDN. |
| ------------ | ----------- |
| `type`       | Para cargar los ensamblados comprimidos mediante una solicitud HTTP y descomprimirlos en el cliente para aquellos hosts que no admiten la captura de contenido comprimido del servidor. Para asignar otro alias a los recursos mediante el redireccionamiento de cada solicitud `fetch` a un nuevo nombre. |
| `name`       | Los parámetros `loadBootResource` aparecen en la tabla siguiente. |
| `defaultUri` | Parámetro |
| `integrity`  | Descripción |

Tipo de recurso.

* Tipos permitidos: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`, `timezonedata` Nombre del recurso.

  * URI relativo o absoluto del recurso.
  * Cadena de integridad que representa el contenido esperado de la respuesta.
  * `loadBootResource` devuelve cualquiera de los siguientes elementos para invalidar el proceso de carga:

  ```html
  ...

  <script src="_framework/blazor.webassembly.js" autostart="false"></script>
  <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        console.log(`Loading: '${type}', '${name}', '${defaultUri}', '${integrity}'`);
        switch (type) {
          case 'dotnetjs':
          case 'dotnetwasm':
          case 'timezonedata':
            return `https://my-awesome-cdn.com/blazorwebassembly/3.2.0/${name}`;
        }
      }
    });
  </script>
  ```

* Cadena URI. En el ejemplo siguiente (*wwwroot/index.html*), los siguientes archivos se sirven desde una red CDN en `https://my-awesome-cdn.com/`:

  *dotnet.\*.js*
  
  ```html
  <script src="_framework/blazor.webassembly.js" autostart="false"></script>
  <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        return fetch(defaultUri, { 
          cache: 'no-cache',
          integrity: integrity,
          headers: { 'MyCustomHeader': 'My custom value' }
        });
      }
    });
  </script>
  ```

* *dotnet.wasm*

Datos de zona horaria `Promise<Response>`.

Pase el parámetro `integrity` en un encabezado para conservar el comportamiento de comprobación de integridad predeterminado. En el ejemplo siguiente (*wwwroot/index.html*) se agrega un encabezado HTTP personalizado a las solicitudes salientes y se pasa el parámetro `integrity` a la llamada a `fetch`:

## <a name="change-the-filename-extension-of-dll-files"></a>`null`/`undefined`, que da lugar al comportamiento de carga predeterminado.

Los orígenes externos deben devolver los encabezados CORS necesarios para que los exploradores permitan la carga de recursos entre orígenes.

Normalmente, las redes CDN proporcionan los encabezados necesarios de forma predeterminada. Solo tiene que especificar tipos para los comportamientos personalizados.

Los tipos no especificados en `loadBootResource` se cargan mediante el marco de trabajo según sus comportamientos de carga predeterminados.

Cambio de la extensión de nombre de archivo de los archivos DLL

```powershell
dir .\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content .\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content .\_framework\blazor.boot.json
```

En caso de que necesite cambiar las extensiones de nombre de archivo de los archivos *.dll* publicados de la aplicación, siga las instrucciones de esta sección.

```powershell
((Get-Content .\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content .\service-worker-assets.js
```

Después de publicar la aplicación, use un script de shell o una canalización de compilación de DevOps para cambiar el nombre de los archivos *.dll* a fin de usar otra extensión de archivo.

```console
for f in _framework/_bin/*; do mv "$f" "`echo $f | sed -e 's/\.dll\b/.bin/g'`"; done
sed -i 's/\.dll"/.bin"/g' _framework/blazor.boot.json
```

Elija como destino los archivos *.dll* del directorio *wwwroot* de la salida publicada de la aplicación (por ejemplo, *{RAÍZ DE CONTENIDO}/bin/Release/netstandard2.1/publish/wwwroot*).

```console
sed -i 's/\.dll"/.bin"/g' service-worker-assets.js
```
   
En los ejemplos siguientes se cambia el nombre de los archivos *.dll* para que usen la extensión de archivo *.bin*.

En Windows:

* Si los recursos de trabajo de servicio también están en uso, agregue el siguiente comando: En Linux o macOS:
* Si los recursos de trabajo de servicio también están en uso, agregue el siguiente comando:

Para usar una extensión de archivo diferente a *.bin*, reemplace *.bin* en los comandos anteriores. Para manipular los archivos comprimidos *blazor.boot.json.gz* y *blazor.boot.json.br*, adopte alguno de los enfoques siguientes: Quite los archivos comprimidos *blazor.boot.json.gz* y *blazor.boot.json.br*.

Con este enfoque, la compresión está deshabilitada.

Vuelva a comprimir el archivo *blazor.boot.json* actualizado.

```powershell
param([string]$filepath,[string]$tfm)
dir $filepath\bin\Release\$tfm\wwwroot\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json
Remove-Item $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json.gz
```

Las instrucciones anteriores también se aplican cuando se usan recursos de trabajo de servicio.

```powershell
((Get-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js
```

Quite o vuelva a comprimir *wwwroot/service-worker-assets.js.br* y *wwwroot/service-worker-assets.js.gz*.

```xml
<Target Name="ChangeDLLFileExtensions" AfterTargets="Publish" Condition="'$(Configuration)'=='Release'">
  <Exec Command="powershell.exe -command &quot;&amp; { .\ChangeDLLExtensions.ps1 '$(SolutionDir)' '$(TargetFramework)'}&quot;" />
</Target>
```

De lo contrario, las comprobaciones de integridad de los archivos producirán errores en el explorador.
 
