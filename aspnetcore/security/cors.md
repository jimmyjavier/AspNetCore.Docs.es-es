---
title: Habilitar solicitudes entre orígenes (CORS) en ASP.NET Core
author: rick-anderson
description: Obtén información sobre cómo CORS como estándar para permitir o rechazar solicitudes entre orígenes en una aplicación ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
uid: security/cors
ms.openlocfilehash: 56a339d9018f619af38aecc6f4c2ff40c3c43d2f
ms.sourcegitcommit: 3d07e21868dafc503530ecae2cfa18a7490b58a6
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/18/2020
ms.locfileid: "81642702"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a>Habilitar solicitudes entre orígenes (CORS) en ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Por [Rick Anderson](https://twitter.com/RickAndMSFT) y Kirk [Larkin](https://twitter.com/serpent5)

En este artículo se muestra cómo habilitar CORS en una aplicación de ASP.NET Core.

La seguridad del explorador impide que una página web realice solicitudes a un dominio diferente al que sirvió a la página web. Esta restricción se denomina *directiva del mismo origen.* La directiva del mismo origen impide que un sitio malintencionado lea datos confidenciales de otro sitio. A veces, es posible que desee permitir que otros sitios realicen solicitudes entre orígenes a la aplicación. Para obtener más información, consulte el [artículo de Mozilla CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS).

Uso compartido de recursos entre [orígenes](https://www.w3.org/TR/cors/) (CORS):

* Es un estándar W3C que permite a un servidor relajar la directiva del mismo origen.
* **No** es una característica de seguridad, CORS relaja la seguridad. Una API no es más segura al permitir CORS. Para obtener más información, consulte [Cómo funciona CORS.](#how-cors)
* Permite que un servidor permita explícitamente algunas solicitudes entre orígenes mientras rechaza otras.
* Es más seguro y flexible que las técnicas anteriores, como [JSONP](/dotnet/framework/wcf/samples/jsonp).

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([cómo descargarlo](xref:index#how-to-download-a-sample))

## <a name="same-origin"></a>Mismo origen

Dos direcciones URL tienen el mismo origen si tienen esquemas, hosts y puertos idénticos ([RFC 6454](https://tools.ietf.org/html/rfc6454)).

Estas dos direcciones URL tienen el mismo origen:

* `https://example.com/foo.html`
* `https://example.com/bar.html`

Estas direcciones URL tienen orígenes diferentes a los dos URL anteriores:

* `https://example.net`&ndash; Dominio diferente
* `https://www.example.com/foo.html`&ndash; Subdominio diferente
* `http://example.com/foo.html`&ndash; Esquema diferente
* `https://example.com:9000/foo.html`&ndash; Puerto diferente

## <a name="enable-cors"></a>Habilitación de CORS

Hay tres maneras de habilitar CORS:

* En middleware mediante una [directiva con nombre](#np) o una directiva [predeterminada.](#dp)
* Uso del [enrutamiento de punto final](#ecors).
* Con el atributo [[EnableCors].](#attr)

El uso del atributo [[EnableCors]](#attr) con una directiva con nombre proporciona el control más preciso para limitar los puntos de conexión que admiten CORS.

Cada enfoque se detalla en las secciones siguientes.

<a name="np"></a>

## <a name="cors-with-named-policy-and-middleware"></a>CORS con política y middleware con nombre

CORS Middleware controla las solicitudes entre orígenes. El código siguiente aplica una directiva CORS a todos los puntos de conexión de la aplicación con los orígenes especificados:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=3,9,31)]

El código anterior:

* Establece el nombre `_myAllowSpecificOrigins`de la directiva en . El nombre de la directiva es arbitrario.
* Llama <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> al método de `_myAllowSpecificOrigins` extensión y especifica la directiva CORS. `UseCors`agrega el middleware CORS. La llamada `UseCors` a ser `UseRouting`colocada `UseAuthorization`después de , pero antes . Para obtener más información, consulte Pedido de [Middleware](xref:fundamentals/middleware/index#middleware-order).
* Llamadas <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> con una [expresión lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions). La expresión <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> lambda toma un objeto. [Las opciones](#cors-policy-options)de `WithOrigins`configuración, como , se describen más adelante en este artículo.
* Habilita `_myAllowSpecificOrigins` la directiva CORS para todos los extremos del controlador. Consulte Enrutamiento de [puntos](#ecors) de conexión para aplicar una directiva CORS a puntos de conexión específicos.

Con el enrutamiento de ***must*** punto final, el middleware `UseRouting` CORS debe configurarse para ejecutarse entre las llamadas a y `UseEndpoints`.

Consulte [Probar CORS](#testc) para obtener instrucciones sobre cómo probar código similar al código anterior.

La <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> llamada al método agrega servicios CORS al contenedor de servicios de la aplicación:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet2)]

Para obtener más información, consulte Opciones de [directiva corS](#cpo) en este documento.

Los <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> métodos se pueden encadenar, como se muestra en el código siguiente:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup2.cs?name=snippet)]

Nota: La dirección URL especificada **no** debe`/`contener una barra diagonal final ( ). Si la dirección `/`URL termina `false` con , se devuelve la comparación y no se devuelve ningún encabezado.

<a name="dp"></a>

### <a name="cors-with-default-policy-and-middleware"></a>CORS con política y middleware predeterminados

El siguiente código resaltado habilita la directiva CORS predeterminada:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupDefaultPolicy.cs?name=snippet2&highlight=7,29)]

El código anterior aplica la directiva CORS predeterminada a todos los extremos del controlador.

<a name="ecors"></a>

## <a name="enable-cors-with-endpoint-routing"></a>Habilitación de CORS con enrutamiento de punto de conexión

Habilitar CORS por punto final `RequireCors` mediante actualmente ***no*** admite [solicitudes de comprobación previa automáticas.](#apf) Para obtener más información, consulte este problema de [GitHub](https://github.com/dotnet/aspnetcore/issues/20709) y Probar CORS con enrutamiento de puntos de conexión [y [HttpOptions]](#tcer).

Con el enrutamiento de punto final, CORS se <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> puede habilitar por punto final mediante el conjunto de métodos de extensión:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPt.cs?name=snippet2&highlight=3,7-15,32,40,43)]

En el código anterior:

* `app.UseCors`habilita el middleware CORS. Dado que no se ha `app.UseCors()` configurado una directiva predeterminada, no habilita CORS por sí sola.
* Los `/echo` puntos de conexión y los puntos de conexión de controlador permiten solicitudes entre orígenes mediante la directiva especificada.
* Los `/echo2` puntos de conexión y Razor Pages ***no*** permiten solicitudes entre orígenes porque no se especificó ninguna directiva predeterminada.

El atributo [[DisableCors]](#dc) ***no*** deshabilita CORS que ha `RequireCors`sido habilitado por el enrutamiento de punto final con .

Consulte Probar CORS con enrutamiento de punto de conexión [y [HttpOptions]](#tcer) para obtener instrucciones sobre cómo probar código similar al anterior.

<a name="attr"></a>

## <a name="enable-cors-with-attributes"></a>Habilitar CORS con atributos

Habilitar CORS con el atributo [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) y aplicar una directiva con nombre solo a los puntos de conexión que requieren CORS proporciona el mejor control.

El atributo [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) proporciona una alternativa a la aplicación de CORS globalmente. El `[EnableCors]` atributo habilita CORS para los puntos finales seleccionados, en lugar de todos los puntos finales:

* `[EnableCors]`especifica la directiva predeterminada.
* `[EnableCors("{Policy String}")]`especifica una directiva con nombre.

El `[EnableCors]` atributo se puede aplicar a:

* Página de Razor`PageModel`
* Controlador
* Método de acción del controlador

Se pueden aplicar diferentes directivas a controladores, `[EnableCors]` modelos de página o métodos de acción con el atributo. Cuando `[EnableCors]` el atributo se aplica a un controlador, modelo de página o método de acción y CORS está habilitado en middleware, se aplican ***ambas*** directivas. ***Se recomienda no combinar directivas. Use el*** `[EnableCors]` ***atributo o middleware, no ambos en la misma aplicación.***

El código siguiente aplica una directiva diferente a cada método:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

El código siguiente crea dos directivas CORS:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup3.cs?name=snippet&highlight=12-28,44)]

Para el mejor control de las solicitudes CORS limitantes:

* Utilícelo `[EnableCors("MyPolicy")]` con una directiva con nombre.
* No defina una directiva predeterminada.
* No utilice el enrutamiento de [punto](#ecors)final.

El código de la siguiente sección cumple con la lista anterior.

Consulte [Probar CORS](#testc) para obtener instrucciones sobre cómo probar código similar al código anterior.

<a name="dc"></a>

### <a name="disable-cors"></a>Desactivar CORS

El atributo [[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) ***no*** deshabilita CORS habilitado por el enrutamiento de [punto final.](#ecors)

El código siguiente define `"MyPolicy"`la directiva CORS:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTestMyPolicy.cs?name=snippet)]

El código siguiente deshabilita CORS para la `GetValues2` acción:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet&highlight=1,23)]

El código anterior:

* No habilita CORS con enrutamiento de [punto](#ecors)final.
* No define una [directiva CORS predeterminada.](#dp)
* Utiliza [[EnableCors("MyPolicy")]](#attr) para `"MyPolicy"` habilitar la directiva CORS para el controlador.
* Deshabilita CORS para `GetValues2` el método.

Consulte [Probar CORS](#testc) para obtener instrucciones sobre cómo probar el código anterior.

<a name="cpo"></a>

## <a name="cors-policy-options"></a>Opciones de política CORS

Esta sección describe las diversas opciones que se pueden fijar en una directiva CORS:

* [Establecer los orígenes permitidos](#set-the-allowed-origins)
* [Establecer los métodos HTTP permitidos](#set-the-allowed-http-methods)
* [Establecer los encabezados de solicitud permitidos](#set-the-allowed-request-headers)
* [Establecer los encabezados de respuesta expuestos](#set-the-exposed-response-headers)
* [Credenciales en solicitudes entre orígenes](#credentials-in-cross-origin-requests)
* [Establecer el tiempo de expiración de la comprobación preliminar](#set-the-preflight-expiration-time)

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>se llama `Startup.ConfigureServices`en . Para algunas opciones, puede ser útil leer primero la sección [Cómo funciona CORS.](#how-cors)

## <a name="set-the-allowed-origins"></a>Establecer los orígenes permitidos

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>&ndash; Permite solicitudes CORS de todos los`http` `https`orígenes con cualquier esquema ( o ). `AllowAnyOrigin`es inseguro porque *cualquier sitio web* puede hacer solicitudes entre orígenes a la aplicación.

> [!NOTE]
> Especificar `AllowAnyOrigin` y `AllowCredentials` es una configuración insegura y puede dar lugar a la falsificación de solicitudes entre sitios. El servicio CORS devuelve una respuesta CORS no válida cuando una aplicación está configurada con ambos métodos.

`AllowAnyOrigin`afecta a las solicitudes de comprobación previa y al `Access-Control-Allow-Origin` encabezado. Para obtener más información, consulte la sección Solicitudes de [comprobación preliminar.](#preflight-requests)

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash; Establece <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> la propiedad de la directiva como una función que permite que los orígenes coincidan con un dominio comodín configurado al evaluar si se permite el origen.

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet)]

### <a name="set-the-allowed-http-methods"></a>Establecer los métodos HTTP permitidos

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:

* Permite cualquier método HTTP:
* Afecta a las solicitudes de comprobación previa y al `Access-Control-Allow-Methods` encabezado. Para obtener más información, consulte la sección Solicitudes de [comprobación preliminar.](#preflight-requests)

### <a name="set-the-allowed-request-headers"></a>Establecer los encabezados de solicitud permitidos

Para permitir que se envíen encabezados específicos en una solicitud <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> CORS, [denominadaencabezados](https://xhr.spec.whatwg.org/#request)de solicitud de autor, llame y especifique los encabezados permitidos:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

Para permitir todos los encabezados de solicitud de [autor,](https://www.w3.org/TR/cors/#author-request-headers)llame a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

`AllowAnyHeader`afecta a las solicitudes de comprobación previa y el encabezado [Access-Control-Request-Headers.](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) Para obtener más información, consulte la sección Solicitudes de [comprobación preliminar.](#preflight-requests)

Una directiva de Middleware de CORS `WithHeaders` coincide con los encabezados `Access-Control-Request-Headers` específicos especificados por `WithHeaders`sólo es posible cuando los encabezados enviados exactamente coinciden con los encabezados indicados en .

Por ejemplo, considere una aplicación configurada de la siguiente manera:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet4)]

CorS Middleware rechaza una solicitud de comprobación `Content-Language` previa con el siguiente encabezado de `WithHeaders`solicitud porque ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) no aparece en:

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

La aplicación devuelve una respuesta *200 OK,* pero no devuelve los encabezados CORS. Por lo tanto, el explorador no intenta la solicitud entre orígenes.

### <a name="set-the-exposed-response-headers"></a>Establecer los encabezados de respuesta expuestos

De forma predeterminada, el explorador no expone todos los encabezados de respuesta a la aplicación. Para obtener más información, consulte Uso compartido de recursos entre orígenes [(terminología): Encabezado](https://www.w3.org/TR/cors/#simple-response-header)de respuesta simple .

Los encabezados de respuesta que están disponibles de forma predeterminada son:

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

La especificación CORS llama a estos *encabezados de respuesta simples.* Para que otros encabezados estén <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>disponibles para la aplicación, llame a:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet5)]
### <a name="credentials-in-cross-origin-requests"></a>Credenciales en solicitudes entre orígenes

Las credenciales requieren un control especial en una solicitud CORS. De forma predeterminada, el explorador no envía credenciales con una solicitud entre orígenes. Las credenciales incluyen cookies y esquemas de autenticación HTTP. Para enviar credenciales con una solicitud entre `XMLHttpRequest.withCredentials` orígenes, el cliente debe establecerse en `true`.

Usando `XMLHttpRequest` directamente:

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

Uso de jQuery:

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

Uso de la [API Fetch:](https://developer.mozilla.org/docs/Web/API/Fetch_API)

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

El servidor debe permitir las credenciales. Para permitir credenciales entre <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>orígenes, llame a:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet6)]

La respuesta HTTP `Access-Control-Allow-Credentials` incluye un encabezado, que indica al explorador que el servidor permite credenciales para una solicitud entre orígenes.

Si el explorador envía credenciales pero la `Access-Control-Allow-Credentials` respuesta no incluye un encabezado válido, el explorador no expone la respuesta a la aplicación y se produce un error en la solicitud entre orígenes.

Permitir credenciales entre orígenes es un riesgo para la seguridad. Un sitio web de otro dominio puede enviar las credenciales de un usuario que ha iniciado sesión a la aplicación en nombre del usuario sin el conocimiento del usuario. <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

La especificación CORS también indica `"*"` que establecer los orígenes `Access-Control-Allow-Credentials` en (todos los orígenes) no es válido si el encabezado está presente.

<a name="pref"></a>

## <a name="preflight-requests"></a>Solicitudes de comprobación previa

Para algunas solicitudes CORS, el explorador envía una solicitud [OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) adicional antes de realizar la solicitud real. Esta solicitud se denomina solicitud de [comprobación previa.](https://developer.mozilla.org/docs/Glossary/Preflight_request) El navegador puede omitir la solicitud de comprobación previa si se cumplen ***todas*** las condiciones siguientes:

* El método de solicitud es GET, HEAD o POST.
* La aplicación no establece encabezados `Accept`de `Accept-Language` `Content-Language`solicitud `Content-Type`que `Last-Event-ID`no sean , , , o .
* El `Content-Type` encabezado, si se establece, tiene uno de los siguientes valores:
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

La regla en los encabezados de solicitud establecidos para la `setRequestHeader` solicitud `XMLHttpRequest` de cliente se aplica a los encabezados que establece la aplicación mediante una llamada en el objeto. La especificación CORS llama a estos encabezados de solicitud de autor de [encabezados](https://www.w3.org/TR/cors/#author-request-headers). La regla no se aplica a los encabezados `User-Agent` `Host`que `Content-Length`el explorador puede establecer, como , , o .

A continuación se muestra una respuesta de ejemplo similar a la solicitud de comprobación previa realizada desde el botón **[Put test]** en la sección [Test CORS](#testc) de este documento.

```
General:
Request URL: https://cors3.azurewebsites.net/api/values/5
Request Method: OPTIONS
Status Code: 204 No Content

Response Headers:
Access-Control-Allow-Methods: PUT,DELETE,GET
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f8...8;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Vary: Origin

Request Headers:
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Access-Control-Request-Method: PUT
Connection: keep-alive
Host: cors3.azurewebsites.net
Origin: https://cors1.azurewebsites.net
Referer: https://cors1.azurewebsites.net/
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0
```

La solicitud de comprobación previa utiliza el método [HTTP OPTIONS.](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) Puede incluir los siguientes encabezados:

* [Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): El método HTTP que se utilizará para la solicitud real.
* [Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): Una lista de encabezados de solicitud que la aplicación establece en la solicitud real. Como se indicó anteriormente, esto no incluye encabezados `User-Agent`que el explorador establece, como .
* [Access-Control-Allow-Methods](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Methods)

Si se deniega la solicitud de `200 OK` comprobación preliminar, la aplicación devuelve una respuesta pero no establece los encabezados CORS. Por lo tanto, el explorador no intenta la solicitud entre orígenes. Para obtener un ejemplo de una solicitud de comprobación previa denegada, consulte la sección [Test CORS](#testc) de este documento.

Con las herramientas F12, la aplicación de consola muestra un error similar a uno de los siguientes, dependiendo del explorador:

* Firefox: Solicitud entre orígenes bloqueada: la misma directiva de `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`origen no permite leer el recurso remoto en . (Motivo: la solicitud CORS no se realizó correctamente). [Más información](https://developer.mozilla.org/docs/Web/HTTP/CORS/Errors/CORSDidNotSucceed)
* Basado en cromo: Elhttps://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5acceso ahttps://cors3.azurewebsites.netla captura en ' ' desde el origen ' ' ha sido bloqueado por la directiva CORS: La respuesta a la solicitud de comprobación previa no pasa la comprobación de control de acceso: No hay encabezado 'Access-Control-Allow-Origin' en el recurso solicitado. Si una respuesta opaca sirve a sus necesidades, establezca el modo de la solicitud en 'no-cors' para obtener el recurso con CORS deshabilitado.

Para permitir encabezados <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>específicos, llame a:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

Para permitir todos los encabezados de solicitud de [autor,](https://www.w3.org/TR/cors/#author-request-headers)llame a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

Los navegadores no son `Access-Control-Request-Headers`coherentes en la forma en que establecen . Si cualquiera de los dos:

* Los encabezados se establecen en cualquier otra cosa que no sea`"*"`
* <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>se llama: Incluir `Accept` `Content-Type`al `Origin`menos , , y , además de los encabezados personalizados que desee admitir.

<a name="apf"></a>

### <a name="automatic-preflight-request-code"></a>Código de solicitud de comprobación previa automática

Cuando se aplica la directiva CORS:

* Globalmente `app.UseCors` llamando `Startup.Configure`a .
* Usando `[EnableCors]` el atributo.

ASP.NET Core responde a la solicitud OPTIONS de comprobación preliminar.

Habilitar CORS por punto final `RequireCors` mediante actualmente ***no*** admite solicitudes de comprobación previa automáticas.

La sección [de prueba CORS](#testc) de este documento demuestra este comportamiento.

<a name="pro"></a>

### <a name="httpoptions-attribute-for-preflight-requests"></a>Atributo [HttpOptions] para solicitudes de comprobación previa

Cuando CORS está habilitado con la directiva adecuada, ASP.NET Core suele responder automáticamente a las solicitudes de comprobación previa de CORS. En algunos escenarios, este puede no ser el caso. Por ejemplo, el uso de [CORS con el enrutamiento](#ecors)de punto final .

El código siguiente utiliza el atributo [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) para crear extremos para las solicitudes OPTIONS:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet&highlight=5-17)]

Consulte Probar CORS con enrutamiento de puntos de conexión [y [HttpOptions]](#tcer) para obtener instrucciones sobre cómo probar el código anterior.

### <a name="set-the-preflight-expiration-time"></a>Establecer el tiempo de expiración de la comprobación preliminar

El `Access-Control-Max-Age` encabezado especifica cuánto tiempo se puede almacenar en caché la respuesta a la solicitud de comprobación previa. Para establecer este <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>encabezado, llame a:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet7)]
<a name="how-cors"></a>

## <a name="how-cors-works"></a>Cómo funciona CORS

En esta sección se describe lo que sucede en una solicitud [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) en el nivel de los mensajes HTTP.

* CORS **no** es una característica de seguridad. CORS es un estándar W3C que permite a un servidor relajar la directiva del mismo origen.
  * Por ejemplo, un actor malintencionado podría usar Secuencias de comandos entre sitios [(XSS)](xref:security/cross-site-scripting) en su sitio y ejecutar una solicitud entre sitios a su sitio habilitado para CORS para robar información.
* Una API no es más segura al permitir CORS.
  * Depende del cliente (navegador) aplicar CORS. El servidor ejecuta la solicitud y devuelve la respuesta, es el cliente el que devuelve un error y bloquea la respuesta. Por ejemplo, cualquiera de las siguientes herramientas mostrará la respuesta del servidor:
    * [Fiddler](https://www.telerik.com/fiddler)
    * [Postman](https://www.getpostman.com/)
    * [.NET HttpClient](/dotnet/csharp/tutorials/console-webapiclient)
    * Un explorador web introduciendo la dirección URL en la barra de direcciones.
* Es una forma de que un servidor permita a los navegadores ejecutar una solicitud de API [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) o [Fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API) de origen cruzado que, de lo contrario, estaría prohibida.
  * Los navegadores sin CORS no pueden realizar solicitudes entre orígenes. Antes de CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) se usaba para eludir esta restricción. JSONP no usa XHR, usa `<script>` la etiqueta para recibir la respuesta. Los scripts se pueden cargar entre orígenes.

La [especificación CORS](https://www.w3.org/TR/cors/) introdujo varios encabezados HTTP nuevos que habilitan las solicitudes entre orígenes. Si un explorador admite CORS, establece estos encabezados automáticamente para las solicitudes entre orígenes. El código JavaScript personalizado no es necesario para habilitar CORS.

El [botón de prueba PUT](https://cors3.azurewebsites.net/test) en el [ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) implementado

A continuación se muestra un ejemplo de [Values](https://cors3.azurewebsites.net/) una solicitud `https://cors1.azurewebsites.net/api/values`entre orígenes desde el botón de prueba Valores a . El `Origin` encabezado:

* Proporciona el dominio del sitio que realiza la solicitud.
* Es necesario y debe ser diferente del host.

**Encabezados generales**

```
Request URL: https://cors1.azurewebsites.net/api/values
Request Method: GET
Status Code: 200 OK
```

**Encabezados de respuesta**

```
Content-Encoding: gzip
Content-Type: text/plain; charset=utf-8
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Transfer-Encoding: chunked
Vary: Accept-Encoding
X-Powered-By: ASP.NET
```

**Encabezados de solicitud**

```
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Connection: keep-alive
Host: cors1.azurewebsites.net
Origin: https://cors3.azurewebsites.net
Referer: https://cors3.azurewebsites.net/
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0 ...
```

En `OPTIONS` las solicitudes, el servidor establece el encabezado **Response headers** `Access-Control-Allow-Origin: {allowed origin}` en la respuesta. Por ejemplo, la solicitud de [botón](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) `OPTIONS` de ejemplo implementada, [Delete [EnableCors]](https://cors1.azurewebsites.net/test?number=2) contiene los siguientes encabezados:

**Encabezados generales**

```
Request URL: https://cors3.azurewebsites.net/api/TodoItems2/MyDelete2/5
Request Method: OPTIONS
Status Code: 204 No Content
```

**Encabezados de respuesta**

```
Access-Control-Allow-Headers: Content-Type,x-custom-header
Access-Control-Allow-Methods: PUT,DELETE,GET,OPTIONS
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors3.azurewebsites.net
Vary: Origin
X-Powered-By: ASP.NET
```

**Encabezados de solicitud**

```
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Access-Control-Request-Headers: content-type
Access-Control-Request-Method: DELETE
Connection: keep-alive
Host: cors3.azurewebsites.net
Origin: https://cors1.azurewebsites.net
Referer: https://cors1.azurewebsites.net/test?number=2
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0
```

En los **encabezados Response anteriores,** el servidor establece el encabezado [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) en la respuesta. El `https://cors1.azurewebsites.net` valor de este `Origin` encabezado coincide con el encabezado de la solicitud.

Si <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> se llama, se devuelve el `Access-Control-Allow-Origin: *`valor comodín . `AllowAnyOrigin`permite cualquier origen.

Si la respuesta no `Access-Control-Allow-Origin` incluye el encabezado, se produce un error en la solicitud entre orígenes. Específicamente, el navegador no permite la solicitud. Incluso si el servidor devuelve una respuesta correcta, el explorador no hace que la respuesta esté disponible para la aplicación cliente.

<a name="options"></a>

### <a name="display-options-requests"></a>Mostrar solicitudes OPTIONS

De forma predeterminada, los navegadores Chrome y Edge no muestran solicitudes OPTIONS en la pestaña de red de las herramientas F12. Para mostrar solicitudes OPTIONS en estos navegadores:

* `chrome://flags/#out-of-blink-cors` o `edge://flags/#out-of-blink-cors`
* desactivar la bandera.
* Reiniciar.

Firefox muestra las solicitudes OPTIONS de forma predeterminada.

## <a name="cors-in-iis"></a>CORS en IIS

Al implementar en IIS, CORS tiene que ejecutarse antes de la autenticación de Windows si el servidor no está configurado para permitir el acceso anónimo. Para admitir este escenario, el [módulo CORS](https://www.iis.net/downloads/microsoft/iis-cors-module) de IIS debe instalarse y configurarse para la aplicación.

<a name="testc"></a>

## <a name="test-cors"></a>Prueba de CORS

La [descarga de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) tiene código para probar CORS. Vea [cómo descargarlo](xref:index#how-to-download-a-sample). El ejemplo es un proyecto de API con Razor Pages agregado:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTest2.cs?name=snippet2)]

  > [!WARNING]
  > `WithOrigins("https://localhost:<port>");`solo se debe usar para probar una aplicación de ejemplo similar al código de ejemplo de [descarga.](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors)

A `ValuesController` continuación se proporcionan los puntos de conexión para las pruebas:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet)]

[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) lo proporciona el paquete NuGet [Rick.Docs.Samples.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) y muestra información de ruta.

Pruebe el código de ejemplo anterior mediante uno de los enfoques siguientes:

* Use la aplicación de [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/)ejemplo implementada en . No es necesario descargar el ejemplo.
* Ejecute el `dotnet run` ejemplo con la `https://localhost:5001`dirección URL predeterminada de .
* Ejecute el ejemplo de Visual Studio con el puerto establecido `https://localhost:44398`en 44398 para una dirección URL de .

Uso de un navegador con las herramientas F12:

* Seleccione el botón **Valores** y revise los encabezados en la pestaña **Red.**
* Seleccione el botón **PUT test.** Consulte Solicitudes de opciones de [visualización](#options) para obtener instrucciones sobre cómo mostrar la solicitud OPTIONS. La **prueba PUT** crea dos solicitudes, una solicitud de comprobación previa OPTIONS y la solicitud PUT.
* Seleccione **`GetValues2 [DisableCors]`** el botón para desencadenar una solicitud CORS con errores. Como se menciona en el documento, la respuesta devuelve 200 éxito, pero no se realiza la solicitud CORS. Seleccione la pestaña **Consola** para ver el error CORS. Dependiendo del navegador, se muestra un error similar al siguiente:

     El acceso `'https://cors1.azurewebsites.net/api/values/GetValues2'` a `'https://cors3.azurewebsites.net'` la captura en origen se ha bloqueado mediante la directiva CORS: No hay ningún encabezado 'Access-Control-Allow-Origin' en el recurso solicitado. Si una respuesta opaca sirve a sus necesidades, establezca el modo de la solicitud en 'no-cors' para obtener el recurso con CORS deshabilitado.
     
Los puntos finales habilitados para CORS se pueden probar con una herramienta, como [curl,](https://curl.haxx.se/) [Fiddler](https://www.telerik.com/fiddler)o [Postman.](https://www.getpostman.com/) Cuando se utiliza una herramienta, el origen `Origin` de la solicitud especificada por el encabezado debe diferir del host que recibe la solicitud. Si la solicitud no es *de origen* cruzado `Origin` en función del valor del encabezado:

* No es necesario que CORS Middleware procese la solicitud.
* Los encabezados CORS no se devuelven en la respuesta.

El siguiente `curl` comando utiliza para emitir una solicitud OPTIONS con información:

```bash
curl -X OPTIONS https://cors3.azurewebsites.net/api/TodoItems2/5 -i
```

<!--
curl come with Git. Add to path variable
C:\Program Files\Git\mingw64\bin\
-->

<a name="tcer"></a>

### <a name="test-cors-with-endpoint-routing-and-httpoptions"></a>Pruebe CORS con enrutamiento de punto final y [HttpOptions]

Habilitar CORS por punto final `RequireCors` mediante actualmente ***no*** admite [solicitudes de comprobación previa automáticas.](#apf) Considere el código siguiente que utiliza el enrutamiento de [punto sensal para habilitar CORS:](#ecors)

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPointBugTest.cs?name=snippet2)]

A `TodoItems1Controller` continuación se proporcionan puntos de conexión para las pruebas:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems1Controller.cs?name=snippet2)]

Pruebe el código anterior de la página de [prueba](https://cors1.azurewebsites.net/test?number=1) del [ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)implementado.

Los **botones Delete [EnableCors]** y **GET [EnableCors]** se realizan correctamente, porque los puntos de conexión tienen `[EnableCors]` y responden a las solicitudes de comprobación previa. Se produce un error en los otros puntos de conexión. Se produce un error en el botón **GET,** porque [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) envía:

```javascript
 headers: {
      "Content-Type": "x-custom-header"
 },
```

A `TodoItems2Controller` continuación se proporcionan puntos de conexión similares, pero incluye código explícito para responder a las solicitudes OPTIONS:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet2)]

Pruebe el código anterior de la página de [prueba](https://cors1.azurewebsites.net/test?number=2) del ejemplo implementado. En la lista desplegable **Controlador,** seleccione **Comprobación previa** y, a continuación, **Establecer controlador**. Todas las llamadas `TodoItems2Controller` CORS a los puntos de conexión se realizan correctamente.

## <a name="additional-resources"></a>Recursos adicionales

* [Uso compartido de recursos entre orígenes (CORS)](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [Introducción al módulo CorS de IIS](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

En este artículo se muestra cómo habilitar CORS en una aplicación de ASP.NET Core.

La seguridad del explorador impide que una página web realice solicitudes a un dominio diferente al que sirvió a la página web. Esta restricción se denomina *directiva del mismo origen.* La directiva del mismo origen impide que un sitio malintencionado lea datos confidenciales de otro sitio. A veces, es posible que desee permitir que otros sitios realicen solicitudes entre orígenes a la aplicación. Para obtener más información, consulte el [artículo de Mozilla CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS).

Uso compartido de recursos entre [orígenes](https://www.w3.org/TR/cors/) (CORS):

* Es un estándar W3C que permite a un servidor relajar la directiva del mismo origen.
* **No** es una característica de seguridad, CORS relaja la seguridad. Una API no es más segura al permitir CORS. Para obtener más información, consulte [Cómo funciona CORS.](#how-cors)
* Permite que un servidor permita explícitamente algunas solicitudes entre orígenes mientras rechaza otras.
* Es más seguro y flexible que las técnicas anteriores, como [JSONP](/dotnet/framework/wcf/samples/jsonp).

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([cómo descargarlo](xref:index#how-to-download-a-sample))

## <a name="same-origin"></a>Mismo origen

Dos direcciones URL tienen el mismo origen si tienen esquemas, hosts y puertos idénticos ([RFC 6454](https://tools.ietf.org/html/rfc6454)).

Estas dos direcciones URL tienen el mismo origen:

* `https://example.com/foo.html`
* `https://example.com/bar.html`

Estas direcciones URL tienen orígenes diferentes a los dos URL anteriores:

* `https://example.net`&ndash; Dominio diferente
* `https://www.example.com/foo.html`&ndash; Subdominio diferente
* `http://example.com/foo.html`&ndash; Esquema diferente
* `https://example.com:9000/foo.html`&ndash; Puerto diferente

Internet Explorer no tiene en cuenta el puerto al comparar orígenes.

## <a name="cors-with-named-policy-and-middleware"></a>CORS con política y middleware con nombre

CORS Middleware controla las solicitudes entre orígenes. El código siguiente habilita CORS para toda la aplicación con el origen especificado:

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

El código anterior:

* Establece el nombre\_de la directiva en " myAllowSpecificOrigins". El nombre de la directiva es arbitrario.
* Llama <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> al método de extensión, que habilita CORS.
* Llamadas <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> con una [expresión lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions). La expresión <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> lambda toma un objeto. [Las opciones](#cors-policy-options)de `WithOrigins`configuración, como , se describen más adelante en este artículo.

La <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> llamada al método agrega servicios CORS al contenedor de servicios de la aplicación:

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

Para obtener más información, consulte Opciones de [directiva corS](#cpo) en este documento.

El <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> método puede encadenar métodos, como se muestra en el código siguiente:

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

Nota: La dirección URL **no** debe`/`contener una barra diagonal final ( ). Si la dirección `/`URL termina `false` con , se devuelve la comparación y no se devuelve ningún encabezado.

El código siguiente aplica directivas CORS a todos los puntos de conexión de aplicaciones a través de CORS Middleware:
```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseHsts();
    }

    app.UseCors();

    app.UseHttpsRedirection();
    app.UseMvc();
}
```
Nota: `UseCors` se debe `UseMvc`llamar antes de .

Consulte [Habilitar CORS en páginas de Razor, controladores y métodos](#ecors) de acción para aplicar la directiva CORS en el nivel de página/controlador/acción.

Consulte [Probar CORS](#test) para obtener instrucciones sobre cómo probar código similar al código anterior.

## <a name="enable-cors-with-attributes"></a>Habilitar CORS con atributos

El atributo [ &lbrack;EnableCors&rbrack; ](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) proporciona una alternativa a la aplicación de CORS globalmente. El `[EnableCors]` atributo habilita CORS para los puntos finales seleccionados, en lugar de todos los puntos finales.

Se `[EnableCors]` utiliza para especificar `[EnableCors("{Policy String}")]` la directiva predeterminada y para especificar una directiva.

El `[EnableCors]` atributo se puede aplicar a:

* Página de Razor`PageModel`
* Controlador
* Método de acción del controlador

Puede aplicar diferentes directivas al controlador/modelo de `[EnableCors]` página/acción con el atributo. Cuando `[EnableCors]` el atributo se aplica a un método de acción/modelo de página/controladores/páginas y CORS está habilitado en middleware, se aplican ***ambas*** directivas. Se recomienda ***no*** combinar directivas. Utilice `[EnableCors]` el atributo o middleware, ***no ambos**. Cuando `[EnableCors]`utilice , **no** defina una directiva predeterminada.

El código siguiente aplica una directiva diferente a cada método:

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

El código siguiente crea una directiva predeterminada `"AnotherPolicy"`de CORS y una directiva denominada:

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a>Desactivar CORS

El atributo [ &lbrack;DisableCors&rbrack; ](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) deshabilita CORS para el controlador/modelo de página/acción.

<a name="cpo"></a>

## <a name="cors-policy-options"></a>Opciones de política CORS

Esta sección describe las diversas opciones que se pueden fijar en una directiva CORS:

* [Establecer los orígenes permitidos](#set-the-allowed-origins)
* [Establecer los métodos HTTP permitidos](#set-the-allowed-http-methods)
* [Establecer los encabezados de solicitud permitidos](#set-the-allowed-request-headers)
* [Establecer los encabezados de respuesta expuestos](#set-the-exposed-response-headers)
* [Credenciales en solicitudes entre orígenes](#credentials-in-cross-origin-requests)
* [Establecer el tiempo de expiración de la comprobación preliminar](#set-the-preflight-expiration-time)

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>se llama `Startup.ConfigureServices`en . Para algunas opciones, puede ser útil leer primero la sección [Cómo funciona CORS.](#how-cors)

## <a name="set-the-allowed-origins"></a>Establecer los orígenes permitidos

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>&ndash; Permite solicitudes CORS de todos los`http` `https`orígenes con cualquier esquema ( o ). `AllowAnyOrigin`es inseguro porque *cualquier sitio web* puede hacer solicitudes entre orígenes a la aplicación.

> [!NOTE]
> Especificar `AllowAnyOrigin` y `AllowCredentials` es una configuración insegura y puede dar lugar a la falsificación de solicitudes entre sitios. Para una aplicación segura, especifique una lista exacta de orígenes si el cliente debe autorizarse a sí mismo para tener acceso a los recursos del servidor.

`AllowAnyOrigin`afecta a las solicitudes de comprobación previa y al `Access-Control-Allow-Origin` encabezado. Para obtener más información, consulte la sección Solicitudes de [comprobación preliminar.](#preflight-requests)

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash; Establece <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> la propiedad de la directiva como una función que permite que los orígenes coincidan con un dominio comodín configurado al evaluar si se permite el origen.

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

### <a name="set-the-allowed-http-methods"></a>Establecer los métodos HTTP permitidos

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:

* Permite cualquier método HTTP:
* Afecta a las solicitudes de comprobación previa y al `Access-Control-Allow-Methods` encabezado. Para obtener más información, consulte la sección Solicitudes de [comprobación preliminar.](#preflight-requests)

### <a name="set-the-allowed-request-headers"></a>Establecer los encabezados de solicitud permitidos

Para permitir que se envíen encabezados específicos en una solicitud <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> CORS, *denominadaencabezados*de solicitud de autor, llame y especifique los encabezados permitidos:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

Para permitir todos los encabezados de solicitud de autor, llame a: <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

Esta configuración afecta a las `Access-Control-Request-Headers` solicitudes de comprobación previa y al encabezado. Para obtener más información, consulte la sección Solicitudes de [comprobación preliminar.](#preflight-requests)

CORS Middleware siempre permite que `Access-Control-Request-Headers` se envíen cuatro encabezados en el que se enviarán independientemente de los valores configurados en CorsPolicy.Headers. Esta lista de encabezados incluye:

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

Por ejemplo, considere una aplicación configurada de la siguiente manera:

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

CorS Middleware responde correctamente a una solicitud de comprobación `Content-Language` previa con el siguiente encabezado de solicitud porque siempre está en la lista blanca:

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

### <a name="set-the-exposed-response-headers"></a>Establecer los encabezados de respuesta expuestos

De forma predeterminada, el explorador no expone todos los encabezados de respuesta a la aplicación. Para obtener más información, consulte Uso compartido de recursos entre orígenes [(terminología): Encabezado](https://www.w3.org/TR/cors/#simple-response-header)de respuesta simple .

Los encabezados de respuesta que están disponibles de forma predeterminada son:

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

La especificación CORS llama a estos *encabezados de respuesta simples.* Para que otros encabezados estén <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>disponibles para la aplicación, llame a:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a>Credenciales en solicitudes entre orígenes

Las credenciales requieren un control especial en una solicitud CORS. De forma predeterminada, el explorador no envía credenciales con una solicitud entre orígenes. Las credenciales incluyen cookies y esquemas de autenticación HTTP. Para enviar credenciales con una solicitud entre `XMLHttpRequest.withCredentials` orígenes, el cliente debe establecerse en `true`.

Usando `XMLHttpRequest` directamente:

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

Uso de jQuery:

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

Uso de la [API Fetch:](https://developer.mozilla.org/docs/Web/API/Fetch_API)

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

El servidor debe permitir las credenciales. Para permitir credenciales entre <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>orígenes, llame a:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

La respuesta HTTP `Access-Control-Allow-Credentials` incluye un encabezado, que indica al explorador que el servidor permite credenciales para una solicitud entre orígenes.

Si el explorador envía credenciales pero la `Access-Control-Allow-Credentials` respuesta no incluye un encabezado válido, el explorador no expone la respuesta a la aplicación y se produce un error en la solicitud entre orígenes.

Permitir credenciales entre orígenes es un riesgo para la seguridad. Un sitio web de otro dominio puede enviar las credenciales de un usuario que ha iniciado sesión a la aplicación en nombre del usuario sin el conocimiento del usuario. <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

La especificación CORS también indica `"*"` que establecer los orígenes `Access-Control-Allow-Credentials` en (todos los orígenes) no es válido si el encabezado está presente.

### <a name="preflight-requests"></a>Solicitudes de comprobación previa

Para algunas solicitudes CORS, el explorador envía una solicitud adicional antes de realizar la solicitud real. Esta solicitud se denomina solicitud de *comprobación previa.* El navegador puede omitir la solicitud de comprobación previa si se cumplen las siguientes condiciones:

* El método de solicitud es GET, HEAD o POST.
* La aplicación no establece encabezados `Accept`de `Accept-Language` `Content-Language`solicitud `Content-Type`que `Last-Event-ID`no sean , , , o .
* El `Content-Type` encabezado, si se establece, tiene uno de los siguientes valores:
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

La regla en los encabezados de solicitud establecidos para la `setRequestHeader` solicitud `XMLHttpRequest` de cliente se aplica a los encabezados que establece la aplicación mediante una llamada en el objeto. La especificación CORS llama a estos encabezados de solicitud de autor de *encabezados*. La regla no se aplica a los encabezados `User-Agent` `Host`que `Content-Length`el explorador puede establecer, como , , o .

A continuación se muestra un ejemplo de una solicitud de comprobación previa:

```
OPTIONS https://myservice.azurewebsites.net/api/test HTTP/1.1
Accept: */*
Origin: https://myclient.azurewebsites.net
Access-Control-Request-Method: PUT
Access-Control-Request-Headers: accept, x-my-custom-header
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
Content-Length: 0
```

La solicitud de pre-vuelo utiliza el método HTTP OPTIONS. Incluye dos encabezados especiales:

* `Access-Control-Request-Method`: el método HTTP que se utilizará para la solicitud real.
* `Access-Control-Request-Headers`: una lista de encabezados de solicitud que la aplicación establece en la solicitud real. Como se indicó anteriormente, esto no incluye encabezados `User-Agent`que el explorador establece, como .

<!-- I think this needs to be removed, was put here accidently -->

Cuando CORS está habilitado con la directiva adecuada, ASP.NET Core generalmente responde automáticamente a las solicitudes de comprobación previa de CORS. Consulte el [atributo [HttpOptions] para ver las solicitudes](#pro)de comprobación previa.

Una solicitud de comprobación previa `Access-Control-Request-Headers` de CORS puede incluir un encabezado, que indica al servidor los encabezados que se envían con la solicitud real.

Para permitir encabezados <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>específicos, llame a:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

Para permitir todos los encabezados de solicitud de autor, llame a: <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

Los navegadores no son del `Access-Control-Request-Headers`todo coherentes en la forma en que establecen . Si establece encabezados en `"*"` cualquier otro <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>valor que no `Accept` `Content-Type`sea `Origin`(o use ), debe incluir al menos , , y , además de los encabezados personalizados que desee admitir.

A continuación se muestra una respuesta de ejemplo a la solicitud de comprobación previa (suponiendo que el servidor permite la solicitud):

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 0
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Access-Control-Allow-Headers: x-my-custom-header
Access-Control-Allow-Methods: PUT
Date: Wed, 20 May 2015 06:33:22 GMT
```

La respuesta `Access-Control-Allow-Methods` incluye un encabezado que enumera `Access-Control-Allow-Headers` los métodos permitidos y, opcionalmente, un encabezado, que enumera los encabezados permitidos. Si la solicitud de comprobación previa se realiza correctamente, el explorador envía la solicitud real.

Si se deniega la solicitud de comprobación previa, la aplicación devuelve una respuesta *200 OK* pero no devuelve los encabezados CORS. Por lo tanto, el explorador no intenta la solicitud entre orígenes.

### <a name="set-the-preflight-expiration-time"></a>Establecer el tiempo de expiración de la comprobación preliminar

El `Access-Control-Max-Age` encabezado especifica cuánto tiempo se puede almacenar en caché la respuesta a la solicitud de comprobación previa. Para establecer este <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>encabezado, llame a:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a>Cómo funciona CORS

En esta sección se describe lo que sucede en una solicitud [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) en el nivel de los mensajes HTTP.

* CORS **no** es una característica de seguridad. CORS es un estándar W3C que permite a un servidor relajar la directiva del mismo origen.
  * Por ejemplo, un actor malintencionado podría usar [Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) en su sitio y ejecutar una solicitud entre sitios a su sitio habilitado para CORS para robar información.
* Su API no es más segura al permitir CORS.
  * Depende del cliente (navegador) aplicar CORS. El servidor ejecuta la solicitud y devuelve la respuesta, es el cliente el que devuelve un error y bloquea la respuesta. Por ejemplo, cualquiera de las siguientes herramientas mostrará la respuesta del servidor:
    * [Fiddler](https://www.telerik.com/fiddler)
    * [Postman](https://www.getpostman.com/)
    * [.NET HttpClient](/dotnet/csharp/tutorials/console-webapiclient)
    * Un explorador web introduciendo la dirección URL en la barra de direcciones.
* Es una forma de que un servidor permita a los navegadores ejecutar una solicitud de API [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) o [Fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API) de origen cruzado que, de lo contrario, estaría prohibida.
  * Los navegadores (sin CORS) no pueden realizar solicitudes entre orígenes. Antes de CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) se usaba para eludir esta restricción. JSONP no usa XHR, usa `<script>` la etiqueta para recibir la respuesta. Los scripts se pueden cargar entre orígenes.

La [especificación CORS](https://www.w3.org/TR/cors/) introdujo varios encabezados HTTP nuevos que habilitan las solicitudes entre orígenes. Si un explorador admite CORS, establece estos encabezados automáticamente para las solicitudes entre orígenes. El código JavaScript personalizado no es necesario para habilitar CORS.

A continuación se muestra un ejemplo de una solicitud entre orígenes. El `Origin` encabezado proporciona el dominio del sitio que realiza la solicitud. El `Origin` encabezado es obligatorio y debe ser diferente del host.

```
GET https://myservice.azurewebsites.net/api/test HTTP/1.1
Referer: https://myclient.azurewebsites.net/
Accept: */*
Accept-Language: en-US
Origin: https://myclient.azurewebsites.net
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
```

Si el servidor permite la `Access-Control-Allow-Origin` solicitud, establece el encabezado en la respuesta. El valor de este `Origin` encabezado coincide con el encabezado `"*"`de la solicitud o es el valor comodín, lo que significa que se permite cualquier origen:

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Type: text/plain; charset=utf-8
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Date: Wed, 20 May 2015 06:27:30 GMT
Content-Length: 12

Test message
```

Si la respuesta no `Access-Control-Allow-Origin` incluye el encabezado, se produce un error en la solicitud entre orígenes. Específicamente, el navegador no permite la solicitud. Incluso si el servidor devuelve una respuesta correcta, el explorador no hace que la respuesta esté disponible para la aplicación cliente.

<a name="test"></a>

## <a name="test-cors"></a>Prueba de CORS

Para probar CORS:

1. [Cree un proyecto](xref:tutorials/first-web-api)de API. Como alternativa, puede [descargar el ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).
1. Habilite CORS utilizando uno de los enfoques de este documento. Por ejemplo:

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > `WithOrigins("https://localhost:<port>");`solo se debe usar para probar una aplicación de ejemplo similar al código de ejemplo de [descarga.](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors)

1. Crear un proyecto de aplicación web (Razor Pages o MVC). El ejemplo utiliza Razor Pages. Puede crear la aplicación web en la misma solución que el proyecto de API.
1. Agregue el siguiente código resaltado al archivo *Index.cshtml:*

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. En el código anterior, reemplace `url: 'https://<web app>.azurewebsites.net/api/values/1',` por la dirección URL de la aplicación implementada.
1. Implemente el proyecto de API. Por ejemplo, [implemente en Azure](xref:host-and-deploy/azure-apps/index).
1. Ejecute la aplicación Razor Pages o MVC desde el escritorio y haga clic en el botón **Probar.** Utilice las herramientas F12 para revisar los mensajes de error.
1. Quite el origen `WithOrigins` localhost de la aplicación e implemente la aplicación. Como alternativa, ejecute la aplicación cliente con un puerto diferente. Por ejemplo, ejecute desde Visual Studio.
1. Pruebe con la aplicación cliente. Los errores de CORS devuelven un error, pero el mensaje de error no está disponible para JavaScript. Utilice la pestaña de la consola de las herramientas F12 para ver el error. Dependiendo del navegador, aparece un error (en la consola de herramientas F12) similar al siguiente:

   * Uso de Microsoft Edge:

     **SEC7120: [CORS] `https://localhost:44375` El origen `https://localhost:44375` no se encontró en el encabezado de respuesta Access-Control-Allow-Origin para el recurso entre orígenes en`https://webapi.azurewebsites.net/api/values/1`**

   * Uso de Chrome:

     **El acceso a `https://webapi.azurewebsites.net/api/values/1` XMLHttpRequest desde el origen `https://localhost:44375` se ha bloqueado mediante la directiva CORS: No hay ningún encabezado 'Access-Control-Allow-Origin' en el recurso solicitado.**
     
Los puntos finales habilitados para CORS se pueden probar con una herramienta, como [Fiddler](https://www.telerik.com/fiddler) o [Postman](https://www.getpostman.com/). Cuando se utiliza una herramienta, el origen `Origin` de la solicitud especificada por el encabezado debe diferir del host que recibe la solicitud. Si la solicitud no es *de origen* cruzado `Origin` en función del valor del encabezado:

* No es necesario que CORS Middleware procese la solicitud.
* Los encabezados CORS no se devuelven en la respuesta.

## <a name="cors-in-iis"></a>CORS en IIS

Al implementar en IIS, CORS tiene que ejecutarse antes de la autenticación de Windows si el servidor no está configurado para permitir el acceso anónimo. Para admitir este escenario, el [módulo CORS](https://www.iis.net/downloads/microsoft/iis-cors-module) de IIS debe instalarse y configurarse para la aplicación.

## <a name="additional-resources"></a>Recursos adicionales

* [Uso compartido de recursos entre orígenes (CORS)](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [Introducción al módulo CorS de IIS](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end
