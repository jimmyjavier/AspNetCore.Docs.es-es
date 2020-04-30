---
title: Consideraciones de seguridad en ASP.NET CoreSignalR
author: bradygaster
description: Aprenda a usar la autenticación y autorización en ASP.NET Core SignalR.
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: mvc
ms.date: 01/16/2020
no-loc:
- SignalR
uid: signalr/security
ms.openlocfilehash: 5a804425752a26cc5f3185f2b43dd9219dec312d
ms.sourcegitcommit: f9a5069577e8f7c53f8bcec9e13e117950f4f033
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/29/2020
ms.locfileid: "82558980"
---
# <a name="security-considerations-in-aspnet-core-signalr"></a><span data-ttu-id="b018a-103">Consideraciones de seguridad en ASP.NET CoreSignalR</span><span class="sxs-lookup"><span data-stu-id="b018a-103">Security considerations in ASP.NET Core SignalR</span></span>

<span data-ttu-id="b018a-104">Por [Andrew Stanton-enfermera](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="b018a-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="b018a-105">En este artículo se proporciona información sobre SignalRcómo proteger.</span><span class="sxs-lookup"><span data-stu-id="b018a-105">This article provides information on securing SignalR.</span></span>

## <a name="cross-origin-resource-sharing"></a><span data-ttu-id="b018a-106">Uso compartido de recursos entre orígenes</span><span class="sxs-lookup"><span data-stu-id="b018a-106">Cross-origin resource sharing</span></span>

<span data-ttu-id="b018a-107">El [uso compartido de recursos entre orígenes (CORS)](https://www.w3.org/TR/cors/) se puede usar para permitir conexiones SignalR entre orígenes en el explorador.</span><span class="sxs-lookup"><span data-stu-id="b018a-107">[Cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/) can be used to allow cross-origin SignalR connections in the browser.</span></span> <span data-ttu-id="b018a-108">Si el código de JavaScript se hospeda en un dominio diferente SignalR de la aplicación, el [middleware de CORS](xref:security/cors) debe estar habilitado para permitir que SignalR JavaScript se conecte a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b018a-108">If JavaScript code is hosted on a different domain from the SignalR app, [CORS middleware](xref:security/cors) must be enabled to allow the JavaScript to connect to the SignalR app.</span></span> <span data-ttu-id="b018a-109">Permita solicitudes entre orígenes únicamente desde dominios en los que confíe o controle.</span><span class="sxs-lookup"><span data-stu-id="b018a-109">Allow cross-origin requests only from domains you trust or control.</span></span> <span data-ttu-id="b018a-110">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="b018a-110">For example:</span></span>

* <span data-ttu-id="b018a-111">Su sitio se hospeda en`http://www.example.com`</span><span class="sxs-lookup"><span data-stu-id="b018a-111">Your site is hosted on `http://www.example.com`</span></span>
* <span data-ttu-id="b018a-112">La SignalR aplicación se hospeda en`http://signalr.example.com`</span><span class="sxs-lookup"><span data-stu-id="b018a-112">Your SignalR app is hosted on `http://signalr.example.com`</span></span>

<span data-ttu-id="b018a-113">CORS debe configurarse en SignalR la aplicación para permitir solo el `www.example.com`origen.</span><span class="sxs-lookup"><span data-stu-id="b018a-113">CORS should be configured in the SignalR app to only allow the origin `www.example.com`.</span></span>

<span data-ttu-id="b018a-114">Para obtener más información sobre la configuración de CORS, consulte [habilitación de solicitudes entre orígenes (CORS)](xref:security/cors).</span><span class="sxs-lookup"><span data-stu-id="b018a-114">For more information on configuring CORS, see [Enable Cross-Origin Requests (CORS)](xref:security/cors).</span></span> SignalR<span data-ttu-id="b018a-115">**requiere** las siguientes directivas de CORS:</span><span class="sxs-lookup"><span data-stu-id="b018a-115"> **requires** the following CORS policies:</span></span>

* <span data-ttu-id="b018a-116">Permite los orígenes esperados específicos.</span><span class="sxs-lookup"><span data-stu-id="b018a-116">Allow the specific expected origins.</span></span> <span data-ttu-id="b018a-117">Permitir cualquier origen es posible, pero **no** es seguro ni recomendado.</span><span class="sxs-lookup"><span data-stu-id="b018a-117">Allowing any origin is possible but is **not** secure or recommended.</span></span>
* <span data-ttu-id="b018a-118">Los métodos `GET` http `POST` y deben ser permitidos.</span><span class="sxs-lookup"><span data-stu-id="b018a-118">HTTP methods `GET` and `POST` must be allowed.</span></span>
* <span data-ttu-id="b018a-119">Se deben permitir las credenciales para que las sesiones permanentes basadas en cookies funcionen correctamente.</span><span class="sxs-lookup"><span data-stu-id="b018a-119">Credentials must be allowed in order for cookie-based sticky sessions to work correctly.</span></span> <span data-ttu-id="b018a-120">Deben habilitarse incluso cuando no se utiliza la autenticación.</span><span class="sxs-lookup"><span data-stu-id="b018a-120">They must be enabled even when authentication isn't used.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="b018a-121">Sin embargo, en 5,0 hemos proporcionado una opción en el cliente de TypeScript para no usar credenciales.</span><span class="sxs-lookup"><span data-stu-id="b018a-121">However, in 5.0 we have provided an option in the TypeScript client to not use credentials.</span></span>
<span data-ttu-id="b018a-122">La opción de no usar credenciales solo se debe usar cuando se conoce el 100% de que no se necesitan credenciales como cookies en la aplicación (Azure App Service usa las cookies cuando se usan varios servidores para sesiones permanentes).</span><span class="sxs-lookup"><span data-stu-id="b018a-122">The option to not use credentials should only be used when you know 100% that credentials like Cookies are not needed in your app (cookies are used by azure app service when using multiple servers for sticky sessions).</span></span>

::: moniker-end

<span data-ttu-id="b018a-123">Por ejemplo, la siguiente directiva de CORS permite SignalR que un cliente de `https://example.com` explorador hospedado SignalR en acceda a `https://signalr.example.com`la aplicación hospedada en:</span><span class="sxs-lookup"><span data-stu-id="b018a-123">For example, the following CORS policy allows a SignalR browser client hosted on `https://example.com` to access the SignalR app hosted on `https://signalr.example.com`:</span></span>

::: moniker range=">= aspnetcore-3.0"

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    // ... other middleware ...

    // Make sure the CORS middleware is ahead of SignalR.
    app.UseCors(builder =>
    {
        builder.WithOrigins("https://example.com")
            .AllowAnyHeader()
            .WithMethods("GET", "POST")
            .AllowCredentials();
    });

    // ... other middleware ...
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chatHub");
    });

    // ... other middleware ...
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Main](security/sample/Startup.cs?name=snippet1)]

::: moniker-end

## <a name="websocket-origin-restriction"></a><span data-ttu-id="b018a-124">Restricción de origen de WebSocket</span><span class="sxs-lookup"><span data-stu-id="b018a-124">WebSocket Origin Restriction</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="b018a-125">Las protecciones proporcionadas por CORS no se aplican a WebSockets.</span><span class="sxs-lookup"><span data-stu-id="b018a-125">The protections provided by CORS don't apply to WebSockets.</span></span> <span data-ttu-id="b018a-126">En el caso de la restricción de origen en WebSockets, lea [restricción de origen de WebSockets](xref:fundamentals/websockets#websocket-origin-restriction).</span><span class="sxs-lookup"><span data-stu-id="b018a-126">For origin restriction on WebSockets, read [WebSockets origin restriction](xref:fundamentals/websockets#websocket-origin-restriction).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="b018a-127">Las protecciones proporcionadas por CORS no se aplican a WebSockets.</span><span class="sxs-lookup"><span data-stu-id="b018a-127">The protections provided by CORS don't apply to WebSockets.</span></span> <span data-ttu-id="b018a-128">Los exploradores **no** hacen lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="b018a-128">Browsers do **not**:</span></span>

* <span data-ttu-id="b018a-129">Efectúan solicitudes preparatorias CORS.</span><span class="sxs-lookup"><span data-stu-id="b018a-129">Perform CORS pre-flight requests.</span></span>
* <span data-ttu-id="b018a-130">Respetan las restricciones especificadas en los encabezados `Access-Control` al efectuar solicitudes de WebSocket.</span><span class="sxs-lookup"><span data-stu-id="b018a-130">Respect the restrictions specified in `Access-Control` headers when making WebSocket requests.</span></span>

<span data-ttu-id="b018a-131">En cambio, sí que envían el encabezado `Origin` al emitir solicitudes de WebSocket.</span><span class="sxs-lookup"><span data-stu-id="b018a-131">However, browsers do send the `Origin` header when issuing WebSocket requests.</span></span> <span data-ttu-id="b018a-132">Las aplicaciones deben configurarse para validar estos encabezados a fin de garantizar que solo se permitan los WebSockets procedentes de los orígenes esperados.</span><span class="sxs-lookup"><span data-stu-id="b018a-132">Applications should be configured to validate these headers to ensure that only WebSockets coming from the expected origins are allowed.</span></span>

<span data-ttu-id="b018a-133">En ASP.NET Core 2,1 y versiones posteriores, la validación de encabezados se puede lograr mediante un middleware personalizado colocado **antes `UseSignalR`y el middleware de autenticación** en: `Configure`</span><span class="sxs-lookup"><span data-stu-id="b018a-133">In ASP.NET Core 2.1 and later, header validation can be achieved using a custom middleware placed **before `UseSignalR`, and authentication middleware** in `Configure`:</span></span>

[!code-csharp[Main](security/sample/Startup.cs?name=snippet2)]

> [!NOTE]
> <span data-ttu-id="b018a-134">El encabezado `Origin` está controlado por el cliente y, al igual que el encabezado `Referer`, se puede falsificar.</span><span class="sxs-lookup"><span data-stu-id="b018a-134">The `Origin` header is controlled by the client and, like the `Referer` header, can be faked.</span></span> <span data-ttu-id="b018a-135">Estos encabezados **no** deben usarse como mecanismo de autenticación.</span><span class="sxs-lookup"><span data-stu-id="b018a-135">These headers should **not** be used as an authentication mechanism.</span></span>

::: moniker-end

## <a name="connectionid"></a><span data-ttu-id="b018a-136">ConnectionId</span><span class="sxs-lookup"><span data-stu-id="b018a-136">ConnectionId</span></span>

<span data-ttu-id="b018a-137">La exposición `ConnectionId` puede provocar la suplantación malintencionada si la SignalR versión del servidor o del cliente es ASP.net Core 2,2 o una versión anterior.</span><span class="sxs-lookup"><span data-stu-id="b018a-137">Exposing `ConnectionId` can lead to malicious impersonation if the SignalR server or client version is ASP.NET Core 2.2 or earlier.</span></span> <span data-ttu-id="b018a-138">Si la SignalR versión del servidor y del cliente es ASP.net Core 3,0 o posterior `ConnectionToken` , en lugar `ConnectionId` de debe mantenerse el secreto.</span><span class="sxs-lookup"><span data-stu-id="b018a-138">If the SignalR server and client version are ASP.NET Core 3.0 or later, the `ConnectionToken` rather than the `ConnectionId` must be kept secret.</span></span> <span data-ttu-id="b018a-139">No `ConnectionToken` se expone intencionadamente en ninguna API.</span><span class="sxs-lookup"><span data-stu-id="b018a-139">The `ConnectionToken` is purposely not exposed in any API.</span></span>  <span data-ttu-id="b018a-140">Puede ser difícil asegurarse de que los clientes SignalR más antiguos no se conecten al servidor, por lo SignalR que, incluso si la versión del servidor es `ConnectionId` ASP.net Core 3,0 o posterior, el no debe estar expuesto.</span><span class="sxs-lookup"><span data-stu-id="b018a-140">It can be difficult to ensure that older SignalR clients aren't connecting to the server, so even if your SignalR server version is ASP.NET Core 3.0 or later, the `ConnectionId` shouldn't be exposed.</span></span>

## <a name="access-token-logging"></a><span data-ttu-id="b018a-141">Registro de tokens de acceso</span><span class="sxs-lookup"><span data-stu-id="b018a-141">Access token logging</span></span>

<span data-ttu-id="b018a-142">Al utilizar WebSockets o eventos enviados por el servidor, el cliente del explorador envía el token de acceso en la cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="b018a-142">When using WebSockets or Server-Sent Events, the browser client sends the access token in the query string.</span></span> <span data-ttu-id="b018a-143">La recepción del token de acceso a través de la cadena de consulta suele `Authorization` ser segura con el encabezado estándar.</span><span class="sxs-lookup"><span data-stu-id="b018a-143">Receiving the access token via query string is generally secure as using the standard `Authorization` header.</span></span> <span data-ttu-id="b018a-144">Use siempre HTTPS para garantizar una conexión segura de un extremo a otro entre el cliente y el servidor.</span><span class="sxs-lookup"><span data-stu-id="b018a-144">Always use HTTPS to ensure a secure end-to-end connection between the client and the server.</span></span> <span data-ttu-id="b018a-145">Muchos servidores web registran la dirección URL para cada solicitud, incluida la cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="b018a-145">Many web servers log the URL for each request, including the query string.</span></span> <span data-ttu-id="b018a-146">El registro de las direcciones URL puede registrar el token de acceso.</span><span class="sxs-lookup"><span data-stu-id="b018a-146">Logging the URLs may log the access token.</span></span> <span data-ttu-id="b018a-147">ASP.NET Core registra la dirección URL de cada solicitud de forma predeterminada, que incluirá la cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="b018a-147">ASP.NET Core logs the URL for each request by default, which will include the query string.</span></span> <span data-ttu-id="b018a-148">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="b018a-148">For example:</span></span>

```
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/myhub?access_token=1234
```

<span data-ttu-id="b018a-149">Si tiene dudas sobre el registro de estos datos con los registros del servidor, puede deshabilitar este registro por completo `Microsoft.AspNetCore.Hosting` configurando `Warning` el registrador en el nivel o superior ( `Info` estos mensajes se escriben en el nivel).</span><span class="sxs-lookup"><span data-stu-id="b018a-149">If you have concerns about logging this data with your server logs, you can disable this logging entirely by configuring the `Microsoft.AspNetCore.Hosting` logger to the `Warning` level or above (these messages are written at `Info` level).</span></span> <span data-ttu-id="b018a-150">Para obtener más información, consulte [filtrado de registros](xref:fundamentals/logging/index#log-filtering) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="b018a-150">For more information, see [Log Filtering](xref:fundamentals/logging/index#log-filtering) for more information.</span></span> <span data-ttu-id="b018a-151">Si todavía desea registrar determinada información de la solicitud, puede [escribir un middleware](xref:fundamentals/middleware/write) para registrar los datos que necesita y filtrar el valor `access_token` de la cadena de consulta (si está presente).</span><span class="sxs-lookup"><span data-stu-id="b018a-151">If you still want to log certain request information, you can [write a middleware](xref:fundamentals/middleware/write) to log the data you require and filter out the `access_token` query string value (if present).</span></span>

## <a name="exceptions"></a><span data-ttu-id="b018a-152">Excepciones</span><span class="sxs-lookup"><span data-stu-id="b018a-152">Exceptions</span></span>

<span data-ttu-id="b018a-153">Los mensajes de excepción generalmente se consideran datos confidenciales que no se deben revelar a un cliente.</span><span class="sxs-lookup"><span data-stu-id="b018a-153">Exception messages are generally considered sensitive data that shouldn't be revealed to a client.</span></span> <span data-ttu-id="b018a-154">De forma predeterminada SignalR , no envía al cliente los detalles de una excepción producida por un método de concentrador.</span><span class="sxs-lookup"><span data-stu-id="b018a-154">By default, SignalR doesn't send the details of an exception thrown by a hub method to the client.</span></span> <span data-ttu-id="b018a-155">En su lugar, el cliente recibe un mensaje genérico que indica que se ha producido un error.</span><span class="sxs-lookup"><span data-stu-id="b018a-155">Instead, the client receives a generic message indicating an error occurred.</span></span> <span data-ttu-id="b018a-156">La entrega de mensajes de excepción al cliente se puede invalidar (por ejemplo, en desarrollo o prueba) con [EnableDetailedErrors](xref:signalr/configuration#configure-server-options).</span><span class="sxs-lookup"><span data-stu-id="b018a-156">Exception message delivery to the client can be overridden (for example in development or test) with [EnableDetailedErrors](xref:signalr/configuration#configure-server-options).</span></span> <span data-ttu-id="b018a-157">Los mensajes de excepción no deben exponerse al cliente en las aplicaciones de producción.</span><span class="sxs-lookup"><span data-stu-id="b018a-157">Exception messages should not be exposed to the client in production apps.</span></span>

## <a name="buffer-management"></a><span data-ttu-id="b018a-158">Administración de búfer</span><span class="sxs-lookup"><span data-stu-id="b018a-158">Buffer management</span></span>

SignalR<span data-ttu-id="b018a-159">utiliza búferes por conexión para administrar los mensajes entrantes y salientes.</span><span class="sxs-lookup"><span data-stu-id="b018a-159"> uses per-connection buffers to manage incoming and outgoing messages.</span></span> <span data-ttu-id="b018a-160">De forma predeterminada SignalR , limita estos búferes a 32 KB.</span><span class="sxs-lookup"><span data-stu-id="b018a-160">By default, SignalR limits these buffers to 32 KB.</span></span> <span data-ttu-id="b018a-161">El mensaje más grande que un cliente o servidor puede enviar es 32 KB.</span><span class="sxs-lookup"><span data-stu-id="b018a-161">The largest message a client or server can send is 32 KB.</span></span> <span data-ttu-id="b018a-162">La memoria máxima consumida por una conexión para los mensajes es de 32 KB.</span><span class="sxs-lookup"><span data-stu-id="b018a-162">The maximum memory consumed by a connection for messages is 32 KB.</span></span> <span data-ttu-id="b018a-163">Si los mensajes son siempre menores que 32 KB, puede reducir el límite, que:</span><span class="sxs-lookup"><span data-stu-id="b018a-163">If your messages are always smaller than 32 KB, you can reduce the limit, which:</span></span>

* <span data-ttu-id="b018a-164">Impide que un cliente pueda enviar un mensaje más grande.</span><span class="sxs-lookup"><span data-stu-id="b018a-164">Prevents a client from being able to send a larger message.</span></span>
* <span data-ttu-id="b018a-165">El servidor nunca tendrá que asignar búferes grandes para aceptar mensajes.</span><span class="sxs-lookup"><span data-stu-id="b018a-165">The server will never need to allocate large buffers to accept messages.</span></span>

<span data-ttu-id="b018a-166">Si los mensajes son mayores de 32 KB, puede aumentar el límite.</span><span class="sxs-lookup"><span data-stu-id="b018a-166">If your messages are larger than 32 KB, you can increase the limit.</span></span> <span data-ttu-id="b018a-167">Aumentar este límite significa:</span><span class="sxs-lookup"><span data-stu-id="b018a-167">Increasing this limit means:</span></span>

* <span data-ttu-id="b018a-168">El cliente puede hacer que el servidor asigne grandes búferes de memoria.</span><span class="sxs-lookup"><span data-stu-id="b018a-168">The client can cause the server to allocate large memory buffers.</span></span>
* <span data-ttu-id="b018a-169">La asignación de servidores de búferes de gran tamaño puede reducir el número de conexiones simultáneas.</span><span class="sxs-lookup"><span data-stu-id="b018a-169">Server allocation of large buffers may reduce the number of concurrent connections.</span></span>

<span data-ttu-id="b018a-170">Hay límites para los mensajes entrantes y salientes, ambos se pueden configurar [HttpConnectionDispatcherOptions](xref:signalr/configuration#configure-server-options) en el objeto HttpConnectionDispatcherOptions `MapHub`configurado en:</span><span class="sxs-lookup"><span data-stu-id="b018a-170">There are limits for incoming and outgoing messages, both can be configured on the [HttpConnectionDispatcherOptions](xref:signalr/configuration#configure-server-options) object configured in `MapHub`:</span></span>

* <span data-ttu-id="b018a-171">`ApplicationMaxBufferSize`representa el número máximo de bytes del cliente que el servidor almacena en búfer.</span><span class="sxs-lookup"><span data-stu-id="b018a-171">`ApplicationMaxBufferSize` represents the maximum number of bytes from the client that the server buffers.</span></span> <span data-ttu-id="b018a-172">Si el cliente intenta enviar un mensaje mayor que este límite, es posible que se cierre la conexión.</span><span class="sxs-lookup"><span data-stu-id="b018a-172">If the client attempts to send a message larger than this limit, the connection may be closed.</span></span>
* <span data-ttu-id="b018a-173">`TransportMaxBufferSize`representa el número máximo de bytes que el servidor puede enviar.</span><span class="sxs-lookup"><span data-stu-id="b018a-173">`TransportMaxBufferSize` represents the maximum number of bytes the server can send.</span></span> <span data-ttu-id="b018a-174">Si el servidor intenta enviar un mensaje (incluidos los valores devueltos de los métodos de concentrador) más grande que este límite, se producirá una excepción.</span><span class="sxs-lookup"><span data-stu-id="b018a-174">If the server attempts to send a message (including return values from hub methods) larger than this limit, an exception will be thrown.</span></span>

<span data-ttu-id="b018a-175">Establecer el límite en `0` deshabilita el límite.</span><span class="sxs-lookup"><span data-stu-id="b018a-175">Setting the limit to `0` disables the limit.</span></span> <span data-ttu-id="b018a-176">Al quitar el límite, un cliente puede enviar un mensaje de cualquier tamaño.</span><span class="sxs-lookup"><span data-stu-id="b018a-176">Removing the limit allows a client to send a message of any size.</span></span> <span data-ttu-id="b018a-177">Los clientes malintencionados que envían mensajes grandes pueden provocar que se asigne exceso de memoria.</span><span class="sxs-lookup"><span data-stu-id="b018a-177">Malicious clients sending large messages can cause excess memory to be allocated.</span></span> <span data-ttu-id="b018a-178">El uso excesivo de memoria puede reducir significativamente el número de conexiones simultáneas.</span><span class="sxs-lookup"><span data-stu-id="b018a-178">Excess memory usage can significantly reduce the number of concurrent connections.</span></span>
