---
title: Middleware de almacenamiento en caché de respuesta en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo configurar y usar Middleware de almacenamiento en caché de respuestas en ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: performance/caching/middleware
ms.openlocfilehash: 360b85d642ad3104d2662a38acd8c86e3c56f292
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775055"
---
# <a name="response-caching-middleware-in-aspnet-core"></a>Middleware de almacenamiento en caché de respuesta en ASP.NET Core

Por [John Luo](https://github.com/JunTaoLuo)

::: moniker range=">= aspnetcore-3.0"

En este artículo se explica cómo configurar middleware de almacenamiento en caché de respuestas en una aplicación ASP.NET Core. El middleware determina cuándo se pueden almacenar en caché las respuestas, almacena las respuestas y atiende las respuestas de la memoria caché. Para obtener una introducción al almacenamiento en caché [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) de http y el atributo, consulte el artículo sobre el [almacenamiento en caché de respuestas](xref:performance/caching/response).

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/middleware/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))

## <a name="configuration"></a>Configuración

El middleware de almacenamiento en caché de respuestas está disponible implícitamente para ASP.NET Core aplicaciones a través del marco de trabajo compartido.

En `Startup.ConfigureServices`, agregue el middleware de almacenamiento en caché de respuesta a la colección de servicios:

[!code-csharp[](middleware/samples/3.x/ResponseCachingMiddleware/Startup.cs?name=snippet1&highlight=3)]

Configure la aplicación para usar el middleware con <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*> el método de extensión, que agrega el middleware a la canalización `Startup.Configure`de procesamiento de solicitudes en:

[!code-csharp[](middleware/samples/3.x/ResponseCachingMiddleware/Startup.cs?name=snippet2&highlight=16)]

La aplicación de ejemplo agrega encabezados para controlar el almacenamiento en caché en solicitudes posteriores:

* [Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2) &ndash; almacena en caché las respuestas que se almacenan en caché durante 10 segundos.
* [Vary](https://tools.ietf.org/html/rfc7231#section-7.1.4) &ndash; configura el middleware para que sirva una respuesta almacenada en caché solo si el encabezado [Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4) de las solicitudes posteriores coincide con el de la solicitud original.

[!code-csharp[](middleware/samples_snippets/3.x/AddHeaders.cs)]

El middleware de almacenamiento en caché de respuestas solo almacena en caché las respuestas del servidor que dan como resultado un código de estado 200 (correcto). El middleware omite cualquier otra respuesta, incluidas [las páginas de error](xref:fundamentals/error-handling).

> [!WARNING]
> Las respuestas que contienen contenido para clientes autenticados deben marcarse como no almacenables en caché para evitar que el middleware almacene y proporcione dichas respuestas. Consulte [condiciones para el almacenamiento en caché](#conditions-for-caching) para obtener más información sobre cómo el middleware determina si una respuesta se almacena en caché.

## <a name="options"></a>Opciones

En la tabla siguiente se muestran las opciones de almacenamiento en caché de respuestas.

| Opción | Descripción |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize> | Tamaño de almacenamiento en caché más grande para el cuerpo de respuesta en bytes. El valor predeterminado es `64 * 1024 * 1024` (64 MB). |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit> | Límite de tamaño para el middleware de la caché de respuesta en bytes. El valor predeterminado es `100 * 1024 * 1024` (100 MB). |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.UseCaseSensitivePaths> | Determina si las respuestas se almacenan en caché en rutas de acceso que distinguen mayúsculas de minúsculas. El valor predeterminado es `false`. |

En el ejemplo siguiente se configura el middleware para:

* Almacenar en caché las respuestas con un tamaño de cuerpo menor o igual que 1.024 bytes.
* Almacenar las respuestas por rutas de acceso que distinguen mayúsculas de minúsculas. Por ejemplo, `/page1` y `/Page1` se almacenan por separado.

```csharp
services.AddResponseCaching(options =>
{
    options.MaximumBodySize = 1024;
    options.UseCaseSensitivePaths = true;
});
```

## <a name="varybyquerykeys"></a>VaryByQueryKeys

Al usar los controladores de páginas de MVC/Web API o Razor Pages [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) modelos de página, el atributo especifica los parámetros necesarios para establecer los encabezados adecuados para el almacenamiento en caché de respuestas. El único parámetro del `[ResponseCache]` atributo que requiere estrictamente el middleware es, <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute.VaryByQueryKeys>que no se corresponde con un encabezado HTTP real. Para obtener más información, vea <xref:performance/caching/response#responsecache-attribute>.

Cuando no se usa `[ResponseCache]` el atributo, el almacenamiento en caché de `VaryByQueryKeys`respuestas puede variar con. Use <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingFeature> directamente desde [HttpContext. Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features):

```csharp
var responseCachingFeature = context.HttpContext.Features.Get<IResponseCachingFeature>();

if (responseCachingFeature != null)
{
    responseCachingFeature.VaryByQueryKeys = new[] { "MyKey" };
}
```

El uso de un valor único `*` igual `VaryByQueryKeys` a en varía la caché por todos los parámetros de consulta de solicitud.

## <a name="http-headers-used-by-response-caching-middleware"></a>Encabezados HTTP usados por middleware de almacenamiento en caché de respuestas

En la tabla siguiente se proporciona información sobre los encabezados HTTP que afectan al almacenamiento en caché de las respuestas.

| Encabezado | Detalles |
| ------ | ------- |
| `Authorization` | Si el encabezado existe, la respuesta no se almacena en caché. |
| `Cache-Control` | El middleware solo tiene en cuenta las respuestas de `public` almacenamiento en caché marcadas con la Directiva de caché. Controlar el almacenamiento en caché con los parámetros siguientes:<ul><li>Max-Age</li><li>Max:&#8224; obsoleto</li><li>mín. actualizado</li><li>must-revalidate</li><li>sin caché</li><li>sin almacén</li><li>solo si se almacena en caché</li><li>privado</li><li>público</li><li>s-maxage</li><li>proxy: revalidar&#8225;</li></ul>&#8224;si no se especifica ningún límite `max-stale`en, el middleware no realiza ninguna acción.<br>&#8225;`proxy-revalidate` tiene el mismo efecto que `must-revalidate`.<br><br>Para obtener más información, vea [RFC 7231: directivas de control de caché de solicitudes](https://tools.ietf.org/html/rfc7234#section-5.2.1). |
| `Pragma` | Un `Pragma: no-cache` encabezado en la solicitud produce el mismo efecto que `Cache-Control: no-cache`. Este encabezado es invalidado por las directivas pertinentes en `Cache-Control` el encabezado, si está presente. Se tiene en cuenta para la compatibilidad con versiones anteriores con HTTP/1.0. |
| `Set-Cookie` | Si el encabezado existe, la respuesta no se almacena en caché. Cualquier middleware de la canalización de procesamiento de solicitudes que establece una o más cookies impide que el middleware de almacenamiento en caché de la respuesta almacene en caché la respuesta (por ejemplo, el [proveedor TempData basado en cookies](xref:fundamentals/app-state#tempdata)).  |
| `Vary` | El `Vary` encabezado se usa para modificar la respuesta almacenada en caché por otro encabezado. Por ejemplo, almacenar en caché las respuestas mediante la codificación `Vary: Accept-Encoding` incluyendo el encabezado, que almacena en caché las respuestas de `Accept-Encoding: gzip` las `Accept-Encoding: text/plain` solicitudes con encabezados y por separado. Nunca se almacena una respuesta con un `*` valor de encabezado de. |
| `Expires` | Una respuesta considerada obsoleta por este encabezado no se almacena ni se recupera a menos que otros `Cache-Control` encabezados lo invalide. |
| `If-None-Match` | La respuesta completa se sirve desde la memoria caché si el `*` valor no `ETag` es y el de la respuesta no coincide con ninguno de los valores proporcionados. De lo contrario, se proporciona una respuesta 304 (no modificada). |
| `If-Modified-Since` | Si el `If-None-Match` encabezado no está presente, se proporciona una respuesta completa desde la memoria caché si la fecha de respuesta almacenada en caché es más reciente que el valor proporcionado. De lo contrario, se proporciona una respuesta *304 no modificada* . |
| `Date` | Cuando se atiende desde la memoria `Date` caché, el software intermedio establece el encabezado si no se proporcionó en la respuesta original. |
| `Content-Length` | Cuando se atiende desde la memoria `Content-Length` caché, el software intermedio establece el encabezado si no se proporcionó en la respuesta original. |
| `Age` | Se `Age` omite el encabezado enviado en la respuesta original. El middleware calcula un nuevo valor cuando se atiende a una respuesta almacenada en caché. |

## <a name="caching-respects-request-cache-control-directives"></a>El almacenamiento en caché respeta las directivas de control de caché de solicitudes

El middleware respeta las reglas de la [especificación de almacenamiento en caché HTTP 1,1](https://tools.ietf.org/html/rfc7234#section-5.2). Las reglas requieren una memoria caché para respetar un `Cache-Control` encabezado válido enviado por el cliente. En la especificación, un cliente puede realizar solicitudes con un `no-cache` valor de encabezado y forzar al servidor a generar una nueva respuesta para cada solicitud. Actualmente, no hay ningún control de desarrollador sobre este comportamiento de almacenamiento en caché al usar el middleware porque el middleware se adhiere a la especificación oficial de almacenamiento en caché.

Para obtener más control sobre el comportamiento del almacenamiento en caché, explore otras características de almacenamiento en caché de ASP.NET Core. Vea los siguientes temas:

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

## <a name="troubleshooting"></a>Solución de problemas

Si el comportamiento de almacenamiento en caché no es el esperado, confirme que las respuestas se pueden almacenar en caché y que se puede atender desde la memoria caché. Examine los encabezados entrantes de la solicitud y los encabezados de salida de la respuesta. Habilitar el [registro](xref:fundamentals/logging/index) para ayudar con la depuración.

Al probar y solucionar problemas de comportamiento de almacenamiento en caché, un explorador puede establecer encabezados de solicitud que afectan al almacenamiento en caché de maneras no deseadas. Por ejemplo, un explorador puede establecer el `Cache-Control` encabezado en `no-cache` o `max-age=0` cuando se actualiza una página. Las herramientas siguientes pueden establecer explícitamente encabezados de solicitud y son preferibles para probar el almacenamiento en caché:

* [Fiddler](https://www.telerik.com/fiddler)
* [Postman](https://www.getpostman.com/)

### <a name="conditions-for-caching"></a>Condiciones para el almacenamiento en caché

* La solicitud debe dar como resultado una respuesta del servidor con un código de estado 200 (correcto).
* El método de solicitud debe ser GET o HEAD.
* En `Startup.Configure`, el middleware de almacenamiento en caché de respuestas debe colocarse antes que el middleware que requiere almacenamiento en caché. Para obtener más información, vea <xref:fundamentals/middleware/index>.
* El `Authorization` encabezado no debe estar presente.
* `Cache-Control`los parámetros de encabezado deben ser válidos y la respuesta debe `public` estar marcada y `private`no marcada.
* El `Pragma: no-cache` encabezado no debe estar presente si el `Cache-Control` encabezado no está presente, ya `Cache-Control` que el encabezado invalida el `Pragma` encabezado cuando está presente.
* El `Set-Cookie` encabezado no debe estar presente.
* `Vary`los parámetros de encabezado deben ser válidos y `*`no ser iguales a.
* El `Content-Length` valor del encabezado (si se establece) debe coincidir con el tamaño del cuerpo de la respuesta.
* <xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature> No se utiliza.
* La respuesta no debe estar obsoleta según lo especificado por `Expires` el encabezado y `max-age` las `s-maxage` directivas de caché y.
* El almacenamiento en búfer de respuesta debe ser correcto. El tamaño de la respuesta debe ser menor que el valor configurado o <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit>predeterminado. El tamaño del cuerpo de la respuesta debe ser menor que el valor configurado <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize>o predeterminado.
* La respuesta debe almacenarse en caché de acuerdo con las especificaciones [RFC 7234](https://tools.ietf.org/html/rfc7234) . Por ejemplo, la `no-store` Directiva no debe existir en los campos de encabezado de solicitud o respuesta. Vea la *sección 3: almacenar respuestas en cachés* de [RFC 7234](https://tools.ietf.org/html/rfc7234) para obtener más información.

> [!NOTE]
> El sistema antifalsificación para generar tokens seguros para evitar ataques de falsificación de solicitud entre sitios (CSRF) establece `Cache-Control` los `Pragma` encabezados y para `no-cache` que las respuestas no estén almacenadas en caché. Para obtener información sobre cómo deshabilitar los tokens antifalsificación para los elementos de <xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration>formulario HTML, vea.

## <a name="additional-resources"></a>Recursos adicionales

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

En este artículo se explica cómo configurar middleware de almacenamiento en caché de respuestas en una aplicación ASP.NET Core. El middleware determina cuándo se pueden almacenar en caché las respuestas, almacena las respuestas y atiende las respuestas de la memoria caché. Para obtener una introducción al almacenamiento en caché [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) de http y el atributo, consulte el artículo sobre el [almacenamiento en caché de respuestas](xref:performance/caching/response).

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/middleware/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))

## <a name="configuration"></a>Configuración

Use el [metapaquete Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) o agregue una referencia de paquete al paquete [Microsoft. AspNetCore. ResponseCaching](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/) .

En `Startup.ConfigureServices`, agregue el middleware de almacenamiento en caché de respuesta a la colección de servicios:

[!code-csharp[](middleware/samples/2.x/ResponseCachingMiddleware/Startup.cs?name=snippet1&highlight=3)]

Configure la aplicación para usar el middleware con <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*> el método de extensión, que agrega el middleware a la canalización `Startup.Configure`de procesamiento de solicitudes en:

[!code-csharp[](middleware/samples/2.x/ResponseCachingMiddleware/Startup.cs?name=snippet2&highlight=14)]

La aplicación de ejemplo agrega encabezados para controlar el almacenamiento en caché en solicitudes posteriores:

* [Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2) &ndash; almacena en caché las respuestas que se almacenan en caché durante 10 segundos.
* [Vary](https://tools.ietf.org/html/rfc7231#section-7.1.4) &ndash; configura el middleware para que sirva una respuesta almacenada en caché solo si el encabezado [Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4) de las solicitudes posteriores coincide con el de la solicitud original.

[!code-csharp[](middleware/samples_snippets/2.x/AddHeaders.cs)]

El middleware de almacenamiento en caché de respuestas solo almacena en caché las respuestas del servidor que dan como resultado un código de estado 200 (correcto). El middleware omite cualquier otra respuesta, incluidas [las páginas de error](xref:fundamentals/error-handling).

> [!WARNING]
> Las respuestas que contienen contenido para clientes autenticados deben marcarse como no almacenables en caché para evitar que el middleware almacene y proporcione dichas respuestas. Consulte [condiciones para el almacenamiento en caché](#conditions-for-caching) para obtener más información sobre cómo el middleware determina si una respuesta se almacena en caché.

## <a name="options"></a>Opciones

En la tabla siguiente se muestran las opciones de almacenamiento en caché de respuestas.

| Opción | Descripción |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize> | Tamaño de almacenamiento en caché más grande para el cuerpo de respuesta en bytes. El valor predeterminado es `64 * 1024 * 1024` (64 MB). |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit> | Límite de tamaño para el middleware de la caché de respuesta en bytes. El valor predeterminado es `100 * 1024 * 1024` (100 MB). |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.UseCaseSensitivePaths> | Determina si las respuestas se almacenan en caché en rutas de acceso que distinguen mayúsculas de minúsculas. El valor predeterminado es `false`. |

En el ejemplo siguiente se configura el middleware para:

* Almacenar en caché las respuestas con un tamaño de cuerpo menor o igual que 1.024 bytes.
* Almacenar las respuestas por rutas de acceso que distinguen mayúsculas de minúsculas. Por ejemplo, `/page1` y `/Page1` se almacenan por separado.

```csharp
services.AddResponseCaching(options =>
{
    options.MaximumBodySize = 1024;
    options.UseCaseSensitivePaths = true;
});
```

## <a name="varybyquerykeys"></a>VaryByQueryKeys

Al usar los controladores de páginas de MVC/Web API o Razor Pages [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) modelos de página, el atributo especifica los parámetros necesarios para establecer los encabezados adecuados para el almacenamiento en caché de respuestas. El único parámetro del `[ResponseCache]` atributo que requiere estrictamente el middleware es, <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute.VaryByQueryKeys>que no se corresponde con un encabezado HTTP real. Para obtener más información, vea <xref:performance/caching/response#responsecache-attribute>.

Cuando no se usa `[ResponseCache]` el atributo, el almacenamiento en caché de `VaryByQueryKeys`respuestas puede variar con. Use <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingFeature> directamente desde [HttpContext. Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features):

```csharp
var responseCachingFeature = context.HttpContext.Features.Get<IResponseCachingFeature>();

if (responseCachingFeature != null)
{
    responseCachingFeature.VaryByQueryKeys = new[] { "MyKey" };
}
```

El uso de un valor único `*` igual `VaryByQueryKeys` a en varía la caché por todos los parámetros de consulta de solicitud.

## <a name="http-headers-used-by-response-caching-middleware"></a>Encabezados HTTP usados por middleware de almacenamiento en caché de respuestas

En la tabla siguiente se proporciona información sobre los encabezados HTTP que afectan al almacenamiento en caché de las respuestas.

| Encabezado | Detalles |
| ------ | ------- |
| `Authorization` | Si el encabezado existe, la respuesta no se almacena en caché. |
| `Cache-Control` | El middleware solo tiene en cuenta las respuestas de `public` almacenamiento en caché marcadas con la Directiva de caché. Controlar el almacenamiento en caché con los parámetros siguientes:<ul><li>Max-Age</li><li>Max:&#8224; obsoleto</li><li>mín. actualizado</li><li>must-revalidate</li><li>sin caché</li><li>sin almacén</li><li>solo si se almacena en caché</li><li>privado</li><li>público</li><li>s-maxage</li><li>proxy: revalidar&#8225;</li></ul>&#8224;si no se especifica ningún límite `max-stale`en, el middleware no realiza ninguna acción.<br>&#8225;`proxy-revalidate` tiene el mismo efecto que `must-revalidate`.<br><br>Para obtener más información, vea [RFC 7231: directivas de control de caché de solicitudes](https://tools.ietf.org/html/rfc7234#section-5.2.1). |
| `Pragma` | Un `Pragma: no-cache` encabezado en la solicitud produce el mismo efecto que `Cache-Control: no-cache`. Este encabezado es invalidado por las directivas pertinentes en `Cache-Control` el encabezado, si está presente. Se tiene en cuenta para la compatibilidad con versiones anteriores con HTTP/1.0. |
| `Set-Cookie` | Si el encabezado existe, la respuesta no se almacena en caché. Cualquier middleware de la canalización de procesamiento de solicitudes que establece una o más cookies impide que el middleware de almacenamiento en caché de la respuesta almacene en caché la respuesta (por ejemplo, el [proveedor TempData basado en cookies](xref:fundamentals/app-state#tempdata)).  |
| `Vary` | El `Vary` encabezado se usa para modificar la respuesta almacenada en caché por otro encabezado. Por ejemplo, almacenar en caché las respuestas mediante la codificación `Vary: Accept-Encoding` incluyendo el encabezado, que almacena en caché las respuestas de `Accept-Encoding: gzip` las `Accept-Encoding: text/plain` solicitudes con encabezados y por separado. Nunca se almacena una respuesta con un `*` valor de encabezado de. |
| `Expires` | Una respuesta considerada obsoleta por este encabezado no se almacena ni se recupera a menos que otros `Cache-Control` encabezados lo invalide. |
| `If-None-Match` | La respuesta completa se sirve desde la memoria caché si el `*` valor no `ETag` es y el de la respuesta no coincide con ninguno de los valores proporcionados. De lo contrario, se proporciona una respuesta 304 (no modificada). |
| `If-Modified-Since` | Si el `If-None-Match` encabezado no está presente, se proporciona una respuesta completa desde la memoria caché si la fecha de respuesta almacenada en caché es más reciente que el valor proporcionado. De lo contrario, se proporciona una respuesta *304 no modificada* . |
| `Date` | Cuando se atiende desde la memoria `Date` caché, el software intermedio establece el encabezado si no se proporcionó en la respuesta original. |
| `Content-Length` | Cuando se atiende desde la memoria `Content-Length` caché, el software intermedio establece el encabezado si no se proporcionó en la respuesta original. |
| `Age` | Se `Age` omite el encabezado enviado en la respuesta original. El middleware calcula un nuevo valor cuando se atiende a una respuesta almacenada en caché. |

## <a name="caching-respects-request-cache-control-directives"></a>El almacenamiento en caché respeta las directivas de control de caché de solicitudes

El middleware respeta las reglas de la [especificación de almacenamiento en caché HTTP 1,1](https://tools.ietf.org/html/rfc7234#section-5.2). Las reglas requieren una memoria caché para respetar un `Cache-Control` encabezado válido enviado por el cliente. En la especificación, un cliente puede realizar solicitudes con un `no-cache` valor de encabezado y forzar al servidor a generar una nueva respuesta para cada solicitud. Actualmente, no hay ningún control de desarrollador sobre este comportamiento de almacenamiento en caché al usar el middleware porque el middleware se adhiere a la especificación oficial de almacenamiento en caché.

Para obtener más control sobre el comportamiento del almacenamiento en caché, explore otras características de almacenamiento en caché de ASP.NET Core. Vea los siguientes temas:

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

## <a name="troubleshooting"></a>Solución de problemas

Si el comportamiento de almacenamiento en caché no es el esperado, confirme que las respuestas se pueden almacenar en caché y que se puede atender desde la memoria caché. Examine los encabezados entrantes de la solicitud y los encabezados de salida de la respuesta. Habilitar el [registro](xref:fundamentals/logging/index) para ayudar con la depuración.

Al probar y solucionar problemas de comportamiento de almacenamiento en caché, un explorador puede establecer encabezados de solicitud que afectan al almacenamiento en caché de maneras no deseadas. Por ejemplo, un explorador puede establecer el `Cache-Control` encabezado en `no-cache` o `max-age=0` cuando se actualiza una página. Las herramientas siguientes pueden establecer explícitamente encabezados de solicitud y son preferibles para probar el almacenamiento en caché:

* [Fiddler](https://www.telerik.com/fiddler)
* [Postman](https://www.getpostman.com/)

### <a name="conditions-for-caching"></a>Condiciones para el almacenamiento en caché

* La solicitud debe dar como resultado una respuesta del servidor con un código de estado 200 (correcto).
* El método de solicitud debe ser GET o HEAD.
* En `Startup.Configure`, el middleware de almacenamiento en caché de respuestas debe colocarse antes que el middleware que requiere almacenamiento en caché. Para obtener más información, vea <xref:fundamentals/middleware/index>.
* El `Authorization` encabezado no debe estar presente.
* `Cache-Control`los parámetros de encabezado deben ser válidos y la respuesta debe `public` estar marcada y `private`no marcada.
* El `Pragma: no-cache` encabezado no debe estar presente si el `Cache-Control` encabezado no está presente, ya `Cache-Control` que el encabezado invalida el `Pragma` encabezado cuando está presente.
* El `Set-Cookie` encabezado no debe estar presente.
* `Vary`los parámetros de encabezado deben ser válidos y `*`no ser iguales a.
* El `Content-Length` valor del encabezado (si se establece) debe coincidir con el tamaño del cuerpo de la respuesta.
* <xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature> No se utiliza.
* La respuesta no debe estar obsoleta según lo especificado por `Expires` el encabezado y `max-age` las `s-maxage` directivas de caché y.
* El almacenamiento en búfer de respuesta debe ser correcto. El tamaño de la respuesta debe ser menor que el valor configurado o <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit>predeterminado. El tamaño del cuerpo de la respuesta debe ser menor que el valor configurado <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize>o predeterminado.
* La respuesta debe almacenarse en caché de acuerdo con las especificaciones [RFC 7234](https://tools.ietf.org/html/rfc7234) . Por ejemplo, la `no-store` Directiva no debe existir en los campos de encabezado de solicitud o respuesta. Vea la *sección 3: almacenar respuestas en cachés* de [RFC 7234](https://tools.ietf.org/html/rfc7234) para obtener más información.

> [!NOTE]
> El sistema antifalsificación para generar tokens seguros para evitar ataques de falsificación de solicitud entre sitios (CSRF) establece `Cache-Control` los `Pragma` encabezados y para `no-cache` que las respuestas no estén almacenadas en caché. Para obtener información sobre cómo deshabilitar los tokens antifalsificación para los elementos de <xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration>formulario HTML, vea.

## <a name="additional-resources"></a>Recursos adicionales

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

::: moniker-end
