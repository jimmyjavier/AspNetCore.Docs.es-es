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
# <a name="test-aspnet-core-middleware"></a><span data-ttu-id="79d5e-103">Prueba del middleware de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="79d5e-103">Test ASP.NET Core middleware</span></span>

<span data-ttu-id="79d5e-104">Por [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="79d5e-104">By [Chris Ross](https://github.com/Tratcher)</span></span>

<span data-ttu-id="79d5e-105">El middleware se puede probar en aislamiento con <xref:Microsoft.AspNetCore.TestHost.TestServer>.</span><span class="sxs-lookup"><span data-stu-id="79d5e-105">Middleware can be tested in isolation with <xref:Microsoft.AspNetCore.TestHost.TestServer>.</span></span> <span data-ttu-id="79d5e-106">Eso le permite lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="79d5e-106">It allows you to:</span></span>

* <span data-ttu-id="79d5e-107">Crear una instancia de la canalización de una aplicación que contenga solo los componentes que se necesiten probar.</span><span class="sxs-lookup"><span data-stu-id="79d5e-107">Instantiate an app pipeline containing only the components that you need to test.</span></span>
* <span data-ttu-id="79d5e-108">Enviar solicitudes personalizadas para comprobar el comportamiento del middleware.</span><span class="sxs-lookup"><span data-stu-id="79d5e-108">Send custom requests to verify middleware behavior.</span></span>

<span data-ttu-id="79d5e-109">Ventajas:</span><span class="sxs-lookup"><span data-stu-id="79d5e-109">Advantages:</span></span>

* <span data-ttu-id="79d5e-110">Las solicitudes se envían en memoria en lugar de serializarse a través de la red.</span><span class="sxs-lookup"><span data-stu-id="79d5e-110">Requests are sent in-memory rather than being serialized over the network.</span></span>
* <span data-ttu-id="79d5e-111">Esto evita problemas adicionales, como la administración de puertos y los certificados HTTPS.</span><span class="sxs-lookup"><span data-stu-id="79d5e-111">This avoids additional concerns, such as port management and HTTPS certificates.</span></span>
* <span data-ttu-id="79d5e-112">Las excepciones del middleware pueden fluir directamente de vuelta a la prueba que llama.</span><span class="sxs-lookup"><span data-stu-id="79d5e-112">Exceptions in the middleware can flow directly back to the calling test.</span></span>
* <span data-ttu-id="79d5e-113">Es posible personalizar estructuras de datos del servidor, como <xref:Microsoft.AspNetCore.Http.HttpContext>, directamente en la prueba.</span><span class="sxs-lookup"><span data-stu-id="79d5e-113">It's possible to customize server data structures, such as <xref:Microsoft.AspNetCore.Http.HttpContext>, directly in the test.</span></span>

## <a name="set-up-the-testserver"></a><span data-ttu-id="79d5e-114">Configuración de TestServer</span><span class="sxs-lookup"><span data-stu-id="79d5e-114">Set up the TestServer</span></span>

<span data-ttu-id="79d5e-115">Cree una prueba en el proyecto de prueba:</span><span class="sxs-lookup"><span data-stu-id="79d5e-115">In the test project, create a test:</span></span>

* <span data-ttu-id="79d5e-116">Compile e inicie un host que use <xref:Microsoft.AspNetCore.TestHost.TestServer>.</span><span class="sxs-lookup"><span data-stu-id="79d5e-116">Build and start a host that uses <xref:Microsoft.AspNetCore.TestHost.TestServer>.</span></span>
* <span data-ttu-id="79d5e-117">Agregue los servicios necesarios que usa el middleware.</span><span class="sxs-lookup"><span data-stu-id="79d5e-117">Add any required services that the middleware uses.</span></span>
* <span data-ttu-id="79d5e-118">Configure la canalización de procesamiento para usar el middleware para la prueba.</span><span class="sxs-lookup"><span data-stu-id="79d5e-118">Configure the processing pipeline to use the middleware for the test.</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/setup.cs?highlight=4-18)]

## <a name="send-requests-with-httpclient"></a><span data-ttu-id="79d5e-119">Envío de solicitudes con HttpClient</span><span class="sxs-lookup"><span data-stu-id="79d5e-119">Send requests with HttpClient</span></span>
<span data-ttu-id="79d5e-120">Envíe una solicitud mediante <xref:System.Net.Http.HttpClient>:</span><span class="sxs-lookup"><span data-stu-id="79d5e-120">Send a request using <xref:System.Net.Http.HttpClient>:</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/request.cs?highlight=20)]

<span data-ttu-id="79d5e-121">Declare el resultado.</span><span class="sxs-lookup"><span data-stu-id="79d5e-121">Assert the result.</span></span> <span data-ttu-id="79d5e-122">En primer lugar, realice una aserción opuesta al resultado que espera.</span><span class="sxs-lookup"><span data-stu-id="79d5e-122">First, make an assertion the opposite of the result that you expect.</span></span> <span data-ttu-id="79d5e-123">Una ejecución inicial con una aserción de falso positivo confirma que se produce un error en la prueba cuando el middleware funciona correctamente.</span><span class="sxs-lookup"><span data-stu-id="79d5e-123">An initial run with a false positive assertion confirms that the test fails when the middleware is performing correctly.</span></span> <span data-ttu-id="79d5e-124">Ejecute la prueba y confirme que se produce un error en ella.</span><span class="sxs-lookup"><span data-stu-id="79d5e-124">Run the test and confirm that the test fails.</span></span>

<span data-ttu-id="79d5e-125">En el ejemplo siguiente, el middleware debe devolver un código de estado 404 (*No encontrado*) cuando se solicita el punto de conexión raíz.</span><span class="sxs-lookup"><span data-stu-id="79d5e-125">In the following example, the middleware should return a 404 status code (*Not Found*) when the root endpoint is requested.</span></span> <span data-ttu-id="79d5e-126">Realice la primera serie de pruebas con `Assert.NotEqual( ... );`, lo que debería generar un error:</span><span class="sxs-lookup"><span data-stu-id="79d5e-126">Make the first test run with `Assert.NotEqual( ... );`, which should fail:</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/false-failure-check.cs?highlight=22)]

<span data-ttu-id="79d5e-127">Cambie la aserción para probar el middleware en condiciones de funcionamiento normales.</span><span class="sxs-lookup"><span data-stu-id="79d5e-127">Change the assertion to test the middleware under normal operating conditions.</span></span> <span data-ttu-id="79d5e-128">La prueba final usa `Assert.Equal( ... );`.</span><span class="sxs-lookup"><span data-stu-id="79d5e-128">The final test uses `Assert.Equal( ... );`.</span></span> <span data-ttu-id="79d5e-129">Vuelva a ejecutar la prueba para confirmar que se realiza correctamente.</span><span class="sxs-lookup"><span data-stu-id="79d5e-129">Run the test again to confirm that it passes.</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/final-test.cs?highlight=22)]

## <a name="send-requests-with-httpcontext"></a><span data-ttu-id="79d5e-130">Envío de solicitudes con HttpContext</span><span class="sxs-lookup"><span data-stu-id="79d5e-130">Send requests with HttpContext</span></span>

<span data-ttu-id="79d5e-131">Una aplicación de prueba también puede enviar una solicitud mediante [SendAsync(Action\<HttpContext>, CancellationToken)](xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A).</span><span class="sxs-lookup"><span data-stu-id="79d5e-131">A test app can also send a request using [SendAsync(Action\<HttpContext>, CancellationToken)](xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A).</span></span> <span data-ttu-id="79d5e-132">En el siguiente ejemplo, se realizan varias comprobaciones cuando el middleware procesa `https://example.com/A/Path/?and=query`:</span><span class="sxs-lookup"><span data-stu-id="79d5e-132">In the following example, several checks are made when `https://example.com/A/Path/?and=query` is processed by the middleware:</span></span>

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

<span data-ttu-id="79d5e-133"><xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> permite la configuración directa de un objeto <xref:Microsoft.AspNetCore.Http.HttpContext> en lugar de usar las abstracciones de <xref:System.Net.Http.HttpClient>.</span><span class="sxs-lookup"><span data-stu-id="79d5e-133"><xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> permits direct configuration of an <xref:Microsoft.AspNetCore.Http.HttpContext> object rather than using the <xref:System.Net.Http.HttpClient> abstractions.</span></span> <span data-ttu-id="79d5e-134">Use <xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> para manipular estructuras que solo están disponibles en el servidor, como [HttpContext.Items](xref:Microsoft.AspNetCore.Http.HttpContext.Items) o [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features).</span><span class="sxs-lookup"><span data-stu-id="79d5e-134">Use <xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> to manipulate structures only available on the server, such as [HttpContext.Items](xref:Microsoft.AspNetCore.Http.HttpContext.Items) or [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features).</span></span>

<span data-ttu-id="79d5e-135">Como en el ejemplo anterior, en el que se intentaba obtener una respuesta *404 - No encontrado*, compruebe lo contrario a cada instrucción `Assert` de la prueba anterior.</span><span class="sxs-lookup"><span data-stu-id="79d5e-135">As with the earlier example that tested for a *404 - Not Found* response, check the opposite for each `Assert` statement in the preceding test.</span></span> <span data-ttu-id="79d5e-136">La comprobación confirma que se produce un error en la prueba cuando el middleware funciona con normalidad.</span><span class="sxs-lookup"><span data-stu-id="79d5e-136">The check confirms that the test fails correctly when the middleware is operating normally.</span></span> <span data-ttu-id="79d5e-137">Una vez que haya confirmado que la prueba de falso positivo funciona, establezca las instrucciones `Assert` finales para las condiciones y los valores esperados de la prueba.</span><span class="sxs-lookup"><span data-stu-id="79d5e-137">After you've confirmed that the false positive test works, set the final `Assert` statements for the expected conditions and values of the test.</span></span> <span data-ttu-id="79d5e-138">Vuelva a ejecutarla para confirmar que se realiza correctamente.</span><span class="sxs-lookup"><span data-stu-id="79d5e-138">Run it again to confirm that the test passes.</span></span>
