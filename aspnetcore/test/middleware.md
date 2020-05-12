---
title: Prueba del middleware de ASP.NET Core
author: tratcher
description: Aprenda a probar middleware de ASP.NET Core con TestServer.
ms.author: riande
ms.custom: mvc
ms.date: 5/6/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: test/middleware
ms.openlocfilehash: 06ff7167e32fbd613c18709e31ecd078b3dfc926
ms.sourcegitcommit: 30fcf69556b6b6ec54a3879e280d5f61f018b48f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2020
ms.locfileid: "82876426"
---
# <a name="test-aspnet-core-middleware"></a>Prueba del middleware de ASP.NET Core

Por [Chris Ross](https://github.com/Tratcher)

El middleware se puede probar en aislamiento con <xref:Microsoft.AspNetCore.TestHost.TestServer>. Eso le permite lo siguiente:

* Crear una instancia de la canalización de una aplicación que contenga solo los componentes que se necesiten probar.
* Enviar solicitudes personalizadas para comprobar el comportamiento del middleware.

Ventajas:

* Las solicitudes se envían en memoria en lugar de serializarse a través de la red.
* Esto evita problemas adicionales, como la administración de puertos y los certificados HTTPS.
* Las excepciones del middleware pueden fluir directamente de vuelta a la prueba que llama.
* Es posible personalizar estructuras de datos del servidor, como <xref:Microsoft.AspNetCore.Http.HttpContext>, directamente en la prueba.

## <a name="set-up-the-testserver"></a>Configuración de TestServer

Cree una prueba en el proyecto de prueba:

* Compile e inicie un host que use <xref:Microsoft.AspNetCore.TestHost.TestServer>.
* Agregue los servicios necesarios que usa el middleware.
* Configure la canalización de procesamiento para usar el middleware para la prueba.

[!code-csharp[](middleware/samples_snapshot/3.x/setup.cs?highlight=4-18)]

## <a name="send-requests-with-httpclient"></a>Envío de solicitudes con HttpClient
Envíe una solicitud mediante <xref:System.Net.Http.HttpClient>:

[!code-csharp[](middleware/samples_snapshot/3.x/request.cs?highlight=20)]

Declare el resultado. En primer lugar, realice una aserción opuesta al resultado que espera. Una ejecución inicial con una aserción de falso positivo confirma que se produce un error en la prueba cuando el middleware funciona correctamente. Ejecute la prueba y confirme que se produce un error en ella.

En el ejemplo siguiente, el middleware debe devolver un código de estado 404 (*No encontrado*) cuando se solicita el punto de conexión raíz. Realice la primera serie de pruebas con `Assert.NotEqual( ... );`, lo que debería generar un error:

[!code-csharp[](middleware/samples_snapshot/3.x/false-failure-check.cs?highlight=22)]

Cambie la aserción para probar el middleware en condiciones de funcionamiento normales. La prueba final usa `Assert.Equal( ... );`. Vuelva a ejecutar la prueba para confirmar que se realiza correctamente.

[!code-csharp[](middleware/samples_snapshot/3.x/final-test.cs?highlight=22)]

## <a name="send-requests-with-httpcontext"></a>Envío de solicitudes con HttpContext

Una aplicación de prueba también puede enviar una solicitud mediante [SendAsync(Action\<HttpContext>, CancellationToken)](xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A). En el siguiente ejemplo, se realizan varias comprobaciones cuando el middleware procesa `https://example.com/A/Path/?and=query`:

```csharp
[Fact]
public async Task TestMiddleware_ExpectedResponse()
{
    using var host = await new HostBuilder()
        .ConfigureWebHost(webBuilder =>
        {
            webBuilder
                .UseTestServer()
                .ConfigureServices(services =>
                {
                    services.AddMyServices();
                })
                .Configure(app =>
                {
                    app.UseMiddleware<MyMiddleware>();
                });
        })
        .StartAsync();

    var server = host.GetTestServer();
    server.BaseAddress = new Uri("https://example.com/A/Path/");

    var context = await server.SendAsync(c =>
    {
        c.Request.Method = HttpMethods.Post;
        c.Request.Path = "/and/file.txt";
        c.Request.QueryString = new QueryString("?and=query");
    });

    Assert.True(context.RequestAborted.CanBeCanceled);
    Assert.Equal(HttpProtocol.Http11, context.Request.Protocol);
    Assert.Equal("POST", context.Request.Method);
    Assert.Equal("https", context.Request.Scheme);
    Assert.Equal("example.com", context.Request.Host.Value);
    Assert.Equal("/A/Path", context.Request.PathBase.Value);
    Assert.Equal("/and/file.txt", context.Request.Path.Value);
    Assert.Equal("?and=query", context.Request.QueryString.Value);
    Assert.NotNull(context.Request.Body);
    Assert.NotNull(context.Request.Headers);
    Assert.NotNull(context.Response.Headers);
    Assert.NotNull(context.Response.Body);
    Assert.Equal(404, context.Response.StatusCode);
    Assert.Null(context.Features.Get<IHttpResponseFeature>().ReasonPhrase);
}
```

<xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> permite la configuración directa de un objeto <xref:Microsoft.AspNetCore.Http.HttpContext> en lugar de usar las abstracciones de <xref:System.Net.Http.HttpClient>. Use <xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> para manipular estructuras que solo están disponibles en el servidor, como [HttpContext.Items](xref:Microsoft.AspNetCore.Http.HttpContext.Items) o [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features).

Como en el ejemplo anterior, en el que se intentaba obtener una respuesta *404 - No encontrado*, compruebe lo contrario a cada instrucción `Assert` de la prueba anterior. La comprobación confirma que se produce un error en la prueba cuando el middleware funciona con normalidad. Una vez que haya confirmado que la prueba de falso positivo funciona, establezca las instrucciones `Assert` finales para las condiciones y los valores esperados de la prueba. Vuelva a ejecutarla para confirmar que se realiza correctamente.
