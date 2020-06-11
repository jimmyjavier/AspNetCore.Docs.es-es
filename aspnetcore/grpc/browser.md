---
title: Uso de gRPC en aplicaciones de explorador
author: jamesnk
description: Obtenga información sobre cómo configurar servicios gRPC en ASP.NET Core a los que se puede llamar desde aplicaciones del explorador usando gRPC-Web.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 05/26/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/browser
ms.openlocfilehash: 6f66a94b41e6e13550396e2e19fdf48f9dc63d46
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/09/2020
ms.locfileid: "84106603"
---
# <a name="use-grpc-in-browser-apps"></a><span data-ttu-id="d0303-103">Uso de gRPC en aplicaciones de explorador</span><span class="sxs-lookup"><span data-stu-id="d0303-103">Use gRPC in browser apps</span></span>

<span data-ttu-id="d0303-104">Por [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="d0303-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="d0303-105">No se puede llamar a un servicio HTTP/2 gRPC desde una aplicación basada en el explorador.</span><span class="sxs-lookup"><span data-stu-id="d0303-105">It is not possible to call a HTTP/2 gRPC service from a browser-based app.</span></span> <span data-ttu-id="d0303-106">[gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) es un protocolo que permite a las aplicaciones de explorador de JavaScript y Blazor llamar a servicios gRPC.</span><span class="sxs-lookup"><span data-stu-id="d0303-106">[gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) is a protocol that allows browser JavaScript and Blazor apps to call gRPC services.</span></span> <span data-ttu-id="d0303-107">En este artículo se explica cómo usar gRPC-Web en .NET Core.</span><span class="sxs-lookup"><span data-stu-id="d0303-107">This article explains how to use gRPC-Web in .NET Core.</span></span>

## <a name="grpc-web-in-aspnet-core-vs-envoy"></a><span data-ttu-id="d0303-108">gRPC-Web en ASP.NET Core frente a Envoy</span><span class="sxs-lookup"><span data-stu-id="d0303-108">gRPC-Web in ASP.NET Core vs. Envoy</span></span>

<span data-ttu-id="d0303-109">Hay dos opciones para agregar gRPC-Web a una aplicación de ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="d0303-109">There are two choices for how to add gRPC-Web to an ASP.NET Core app:</span></span>

* <span data-ttu-id="d0303-110">Compatibilidad con gRPC-Web junto con HTTP/2 gRPC en ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d0303-110">Support gRPC-Web alongside gRPC HTTP/2 in ASP.NET Core.</span></span> <span data-ttu-id="d0303-111">Esta opción usa el middleware que el paquete `Grpc.AspNetCore.Web` proporciona.</span><span class="sxs-lookup"><span data-stu-id="d0303-111">This option uses middleware provided by the `Grpc.AspNetCore.Web` package.</span></span>
* <span data-ttu-id="d0303-112">Use la compatibilidad con gRPC-Web del [proxy de Envoy](https://www.envoyproxy.io/) para traducir gRPC-Web a HTTP/2 gRPC.</span><span class="sxs-lookup"><span data-stu-id="d0303-112">Use the [Envoy proxy's](https://www.envoyproxy.io/) gRPC-Web support to translate gRPC-Web to gRPC HTTP/2.</span></span> <span data-ttu-id="d0303-113">A continuación, la llamada traducida se reenvía a la aplicación ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d0303-113">The translated call is then forwarded onto the ASP.NET Core app.</span></span>

<span data-ttu-id="d0303-114">Cada enfoque tiene ventajas y desventajas.</span><span class="sxs-lookup"><span data-stu-id="d0303-114">There are pros and cons to each approach.</span></span> <span data-ttu-id="d0303-115">Si ya usa Envoy como proxy en el entorno de la aplicación, puede que tenga sentido usarlo para proporcionar compatibilidad con gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="d0303-115">If you're already using Envoy as a proxy in your app's environment, it might make sense to also use it to provide gRPC-Web support.</span></span> <span data-ttu-id="d0303-116">Si desea una solución sencilla para gRPC-Web que solo requiera ASP.NET Core, `Grpc.AspNetCore.Web` es una buena elección.</span><span class="sxs-lookup"><span data-stu-id="d0303-116">If you want a simple solution for gRPC-Web that only requires ASP.NET Core, `Grpc.AspNetCore.Web` is a good choice.</span></span>

## <a name="configure-grpc-web-in-aspnet-core"></a><span data-ttu-id="d0303-117">Configuración de gRPC-Web en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d0303-117">Configure gRPC-Web in ASP.NET Core</span></span>

<span data-ttu-id="d0303-118">Los servicios gRPC hospedados en ASP.NET Core se pueden configurar para admitir gRPC-Web junto con HTTP/2 gRPC.</span><span class="sxs-lookup"><span data-stu-id="d0303-118">gRPC services hosted in ASP.NET Core can be configured to support gRPC-Web alongside HTTP/2 gRPC.</span></span> <span data-ttu-id="d0303-119">gRPC-Web no requiere ningún cambio en los servicios.</span><span class="sxs-lookup"><span data-stu-id="d0303-119">gRPC-Web does not require any changes to services.</span></span> <span data-ttu-id="d0303-120">La única modificación es la configuración de inicio.</span><span class="sxs-lookup"><span data-stu-id="d0303-120">The only modification is startup configuration.</span></span>

<span data-ttu-id="d0303-121">Para habilitar gRPC-Web con un servicio gRPC de ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="d0303-121">To enable gRPC-Web with an ASP.NET Core gRPC service:</span></span>

* <span data-ttu-id="d0303-122">Agregue una referencia al paquete [Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web).</span><span class="sxs-lookup"><span data-stu-id="d0303-122">Add a reference to the [Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) package.</span></span>
* <span data-ttu-id="d0303-123">Configure la aplicación para que use gRPC-Web, agregando para ello `UseGrpcWeb` y `EnableGrpcWeb` a *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="d0303-123">Configure the app to use gRPC-Web by adding `UseGrpcWeb` and `EnableGrpcWeb` to *Startup.cs*:</span></span>

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=10,14)]

<span data-ttu-id="d0303-124">El código anterior:</span><span class="sxs-lookup"><span data-stu-id="d0303-124">The preceding code:</span></span>

* <span data-ttu-id="d0303-125">Agrega el middleware de gRPC-Web, `UseGrpcWeb`, después del enrutamiento y antes de los puntos de conexión.</span><span class="sxs-lookup"><span data-stu-id="d0303-125">Adds the gRPC-Web middleware, `UseGrpcWeb`, after routing and before endpoints.</span></span>
* <span data-ttu-id="d0303-126">Especifica que el método `endpoints.MapGrpcService<GreeterService>()` admite gRPC-Web con `EnableGrpcWeb`.</span><span class="sxs-lookup"><span data-stu-id="d0303-126">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports gRPC-Web with `EnableGrpcWeb`.</span></span> 

<span data-ttu-id="d0303-127">Como alternativa, se puede configurar el middleware gRPC-Web de forma que todos los servicios admitan gRPC-Web de forma predeterminada y no se necesite `EnableGrpcWeb`.</span><span class="sxs-lookup"><span data-stu-id="d0303-127">Alternatively, the gRPC-Web middleware can be configured so all services support gRPC-Web by default and `EnableGrpcWeb` isn't required.</span></span> <span data-ttu-id="d0303-128">Especifique `new GrpcWebOptions { DefaultEnabled = true }` cuando se agregue el middleware.</span><span class="sxs-lookup"><span data-stu-id="d0303-128">Specify `new GrpcWebOptions { DefaultEnabled = true }` when the middleware is added.</span></span>

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=12)]

> [!NOTE]
> <span data-ttu-id="d0303-129">Hay un problema conocido que hace que gRPC-Web genere un error cuando está [hospedado en Http.sys](xref:fundamentals/servers/httpsys) en .NET Core 3.x.</span><span class="sxs-lookup"><span data-stu-id="d0303-129">There is a known issue that causes gRPC-Web to fail when [hosted by Http.sys](xref:fundamentals/servers/httpsys) in .NET Core 3.x.</span></span>
>
> <span data-ttu-id="d0303-130">Una solución alternativa para que gRPC-Web funcione en Http.sys está disponible [aquí](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).</span><span class="sxs-lookup"><span data-stu-id="d0303-130">A workaround to get gRPC-Web working on Http.sys is available [here](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).</span></span>

### <a name="grpc-web-and-cors"></a><span data-ttu-id="d0303-131">gRPC-Web y CORS</span><span class="sxs-lookup"><span data-stu-id="d0303-131">gRPC-Web and CORS</span></span>

<span data-ttu-id="d0303-132">La seguridad del explorador evita que una página web realice solicitudes a un dominio diferente del que atendió a dicha página web.</span><span class="sxs-lookup"><span data-stu-id="d0303-132">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="d0303-133">Esta restricción se aplica a la hora de realizar llamadas de gRPC-Web con aplicaciones de explorador.</span><span class="sxs-lookup"><span data-stu-id="d0303-133">This restriction applies to making gRPC-Web calls with browser apps.</span></span> <span data-ttu-id="d0303-134">Por ejemplo, una aplicación de explorador atendida por `https://www.contoso.com` no puede llamar a los servicios gRPC-Web hospedados en `https://services.contoso.com`.</span><span class="sxs-lookup"><span data-stu-id="d0303-134">For example, a browser app served by `https://www.contoso.com` is blocked from calling gRPC-Web services hosted on `https://services.contoso.com`.</span></span> <span data-ttu-id="d0303-135">Para suavizar esta restricción, podemos recurrir al uso compartido de recursos entre orígenes (CORS).</span><span class="sxs-lookup"><span data-stu-id="d0303-135">Cross Origin Resource Sharing (CORS) can be used to relax this restriction.</span></span>

<span data-ttu-id="d0303-136">Para permitir que la aplicación de explorador haga llamadas de gRPC-Web entre orígenes, configure [CORS en ASP.NET Core](xref:security/cors).</span><span class="sxs-lookup"><span data-stu-id="d0303-136">To allow your browser app to make cross-origin gRPC-Web calls, set up [CORS in ASP.NET Core](xref:security/cors).</span></span> <span data-ttu-id="d0303-137">Use la compatibilidad de CORS integrada y exponga los encabezados específicos de gRPC con <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>.</span><span class="sxs-lookup"><span data-stu-id="d0303-137">Use the built-in CORS support, and expose gRPC-specific headers with <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>.</span></span>

[!code-csharp[](~/grpc/browser/sample/CORS_Startup.cs?name=snippet_1&highlight=5-11,19,24)]

<span data-ttu-id="d0303-138">El código anterior:</span><span class="sxs-lookup"><span data-stu-id="d0303-138">The preceding code:</span></span>

* <span data-ttu-id="d0303-139">Llama a `AddCors` para agregar los servicios CORS y configura una directiva CORS que expone los encabezados específicos de gRPC.</span><span class="sxs-lookup"><span data-stu-id="d0303-139">Calls `AddCors` to add CORS services and configures a CORS policy that exposes gRPC-specific headers.</span></span>
* <span data-ttu-id="d0303-140">Llama a `UseCors` para agregar el middleware de CORS después del enrutamiento y antes de los puntos de conexión.</span><span class="sxs-lookup"><span data-stu-id="d0303-140">Calls `UseCors` to add the CORS middleware after routing and before endpoints.</span></span>
* <span data-ttu-id="d0303-141">Especifica que el método `endpoints.MapGrpcService<GreeterService>()` admite CORS con `RequiresCors`.</span><span class="sxs-lookup"><span data-stu-id="d0303-141">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports CORS with `RequiresCors`.</span></span>

## <a name="call-grpc-web-from-the-browser"></a><span data-ttu-id="d0303-142">Llamada a gRPC-Web desde el explorador</span><span class="sxs-lookup"><span data-stu-id="d0303-142">Call gRPC-Web from the browser</span></span>

<span data-ttu-id="d0303-143">Las aplicaciones de explorador pueden usar gRPC-Web para llamar a servicios gRPC.</span><span class="sxs-lookup"><span data-stu-id="d0303-143">Browser apps can use gRPC-Web to call gRPC services.</span></span> <span data-ttu-id="d0303-144">Existen algunos requisitos y limitaciones a la hora de llamar a servicios gRPC con gRPC-Web desde el explorador:</span><span class="sxs-lookup"><span data-stu-id="d0303-144">There are some requirements and limitations when calling gRPC services with gRPC-Web from the browser:</span></span>

* <span data-ttu-id="d0303-145">El servidor debe estar configurado para admitir gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="d0303-145">The server must have been configured to support gRPC-Web.</span></span>
* <span data-ttu-id="d0303-146">No se pueden usar llamadas de streaming de cliente ni de streaming bidireccional.</span><span class="sxs-lookup"><span data-stu-id="d0303-146">Client streaming and bidirectional streaming calls aren't supported.</span></span> <span data-ttu-id="d0303-147">Sí se puede usar el streaming de servidor.</span><span class="sxs-lookup"><span data-stu-id="d0303-147">Server streaming is supported.</span></span>
* <span data-ttu-id="d0303-148">Para llamar a servicios gRPC en otro dominio, hay que configurar [CORS](xref:security/cors) en el servidor.</span><span class="sxs-lookup"><span data-stu-id="d0303-148">Calling gRPC services on a different domain requires [CORS](xref:security/cors) to be configured on the server.</span></span>

### <a name="javascript-grpc-web-client"></a><span data-ttu-id="d0303-149">Cliente gRPC-Web de JavaScript</span><span class="sxs-lookup"><span data-stu-id="d0303-149">JavaScript gRPC-Web client</span></span>

<span data-ttu-id="d0303-150">Existe un cliente gRPC-Web de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="d0303-150">There is a JavaScript gRPC-Web client.</span></span> <span data-ttu-id="d0303-151">Para obtener instrucciones sobre cómo usar gRPC-Web en JavaScript, vea [Escribir código de cliente de JavaScript con gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span><span class="sxs-lookup"><span data-stu-id="d0303-151">For instructions on how to use gRPC-Web from JavaScript, see [write JavaScript client code with gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span></span>

### <a name="configure-grpc-web-with-the-net-grpc-client"></a><span data-ttu-id="d0303-152">Configuración de gRPC-Web con el cliente gRPC de .NET</span><span class="sxs-lookup"><span data-stu-id="d0303-152">Configure gRPC-Web with the .NET gRPC client</span></span>

<span data-ttu-id="d0303-153">El cliente gRPC de .NET se puede configurar para realizar llamadas de gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="d0303-153">The .NET gRPC client can be configured to make gRPC-Web calls.</span></span> <span data-ttu-id="d0303-154">Esto resulta útil en las aplicaciones de [Blazor WebAssembly](xref:blazor/index#blazor-webassembly), que se hospedan en el explorador y tienen las mismas limitaciones HTTP de código JavaScript.</span><span class="sxs-lookup"><span data-stu-id="d0303-154">This is useful for [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) apps, which are hosted in the browser and have the same HTTP limitations of JavaScript code.</span></span> <span data-ttu-id="d0303-155">Llamar a gRPC-Web con un cliente .NET es [igual que HTTP/2 gRPC](xref:grpc/client).</span><span class="sxs-lookup"><span data-stu-id="d0303-155">Calling gRPC-Web with a .NET client is [the same as HTTP/2 gRPC](xref:grpc/client).</span></span> <span data-ttu-id="d0303-156">La única modificación es cómo se crea el canal.</span><span class="sxs-lookup"><span data-stu-id="d0303-156">The only modification is how the channel is created.</span></span>

<span data-ttu-id="d0303-157">Para usar gRPC-Web:</span><span class="sxs-lookup"><span data-stu-id="d0303-157">To use gRPC-Web:</span></span>

* <span data-ttu-id="d0303-158">Agregue una referencia al paquete [Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web).</span><span class="sxs-lookup"><span data-stu-id="d0303-158">Add a reference to the [Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) package.</span></span>
* <span data-ttu-id="d0303-159">Asegúrese de que la referencia al paquete [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) sea 2.29.0 o superior.</span><span class="sxs-lookup"><span data-stu-id="d0303-159">Ensure the reference to [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) package is 2.29.0 or greater.</span></span>
* <span data-ttu-id="d0303-160">Configure el canal para que use `GrpcWebHandler`:</span><span class="sxs-lookup"><span data-stu-id="d0303-160">Configure the channel to use the `GrpcWebHandler`:</span></span>

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

<span data-ttu-id="d0303-161">El código anterior:</span><span class="sxs-lookup"><span data-stu-id="d0303-161">The preceding code:</span></span>

* <span data-ttu-id="d0303-162">Configura un canal para que use gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="d0303-162">Configures a channel to use gRPC-Web.</span></span>
* <span data-ttu-id="d0303-163">Crea un cliente y realiza una llamada usando el canal.</span><span class="sxs-lookup"><span data-stu-id="d0303-163">Creates a client and makes a call using the channel.</span></span>

<span data-ttu-id="d0303-164">`GrpcWebHandler` tiene las siguientes opciones de configuración:</span><span class="sxs-lookup"><span data-stu-id="d0303-164">`GrpcWebHandler` has the following configuration options:</span></span>

* <span data-ttu-id="d0303-165">**InnerHandler**: elemento <xref:System.Net.Http.HttpMessageHandler> subyacente que realiza la solicitud HTTP de gRPC, por ejemplo, `HttpClientHandler`.</span><span class="sxs-lookup"><span data-stu-id="d0303-165">**InnerHandler**: The underlying <xref:System.Net.Http.HttpMessageHandler> that makes the gRPC HTTP request, for example, `HttpClientHandler`.</span></span>
* <span data-ttu-id="d0303-166">**GrpcWebMode**: tipo de enumeración que especifica si el `Content-Type` de la solicitud HTTP gRPC es `application/grpc-web` o `application/grpc-web-text`.</span><span class="sxs-lookup"><span data-stu-id="d0303-166">**GrpcWebMode**: An enumeration type that specifies whether the gRPC HTTP request `Content-Type` is `application/grpc-web` or `application/grpc-web-text`.</span></span>
    * <span data-ttu-id="d0303-167">`GrpcWebMode.GrpcWeb` configura el contenido que se va a enviar sin codificación.</span><span class="sxs-lookup"><span data-stu-id="d0303-167">`GrpcWebMode.GrpcWeb` configures content to be sent without encoding.</span></span> <span data-ttu-id="d0303-168">Valor predeterminado.</span><span class="sxs-lookup"><span data-stu-id="d0303-168">Default value.</span></span>
    * <span data-ttu-id="d0303-169">`GrpcWebMode.GrpcWebText` configura el contenido para que tenga codificación Base64.</span><span class="sxs-lookup"><span data-stu-id="d0303-169">`GrpcWebMode.GrpcWebText` configures content to be base64 encoded.</span></span> <span data-ttu-id="d0303-170">Esto es necesario en las llamadas de streaming de servidor en los exploradores.</span><span class="sxs-lookup"><span data-stu-id="d0303-170">Required for server streaming calls in browsers.</span></span>
* <span data-ttu-id="d0303-171">**HttpVersion**: elemento `Version` del protocolo HTTP que se usa para establecer [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version) en la solicitud HTTP de gRPC subyacente.</span><span class="sxs-lookup"><span data-stu-id="d0303-171">**HttpVersion**: HTTP protocol `Version` used to set [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version) on the underlying gRPC HTTP request.</span></span> <span data-ttu-id="d0303-172">gRPC-Web no requiere que haya una versión específica y no invalida el valor predeterminado a menos que así se especifique.</span><span class="sxs-lookup"><span data-stu-id="d0303-172">gRPC-Web doesn't require a specific version and doesn't override the default unless specified.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="d0303-173">Los clientes de gRPC generados tienen métodos sincrónicos y asincrónicos para llamar a métodos unarios.</span><span class="sxs-lookup"><span data-stu-id="d0303-173">Generated gRPC clients have sync and async methods for calling unary methods.</span></span> <span data-ttu-id="d0303-174">Así, `SayHello` es sincrónico y `SayHelloAsync`, asincrónico.</span><span class="sxs-lookup"><span data-stu-id="d0303-174">For example, `SayHello` is sync and `SayHelloAsync` is async.</span></span> <span data-ttu-id="d0303-175">La llamada a un método sincrónico en una aplicación de Blazor WebAssembly hace que la aplicación deje de responder.</span><span class="sxs-lookup"><span data-stu-id="d0303-175">Calling a sync method in a Blazor WebAssembly app will cause the app to become unresponsive.</span></span> <span data-ttu-id="d0303-176">En Blazor WebAssembly, siempre se deben usar métodos asincrónicos.</span><span class="sxs-lookup"><span data-stu-id="d0303-176">Async methods must always be used in Blazor WebAssembly.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d0303-177">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="d0303-177">Additional resources</span></span>

* [<span data-ttu-id="d0303-178">Proyecto de GitHub de gRPC para clientes web</span><span class="sxs-lookup"><span data-stu-id="d0303-178">gRPC for Web Clients GitHub project</span></span>](https://github.com/grpc/grpc-web)
* <xref:security/cors>
