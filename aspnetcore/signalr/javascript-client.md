---
title: ASP.NET SignalR cliente JavaScript principal
author: bradygaster
description: Descripción general SignalR de ASP.NET cliente JavaScript principal.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- SignalR
uid: signalr/javascript-client
ms.openlocfilehash: 43b2cacf9f415ec422a00b28246f30c8ad74de29
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440862"
---
# <a name="aspnet-core-opno-locsignalr-javascript-client"></a><span data-ttu-id="d7bc0-103">ASP.NET SignalR cliente JavaScript principal</span><span class="sxs-lookup"><span data-stu-id="d7bc0-103">ASP.NET Core SignalR JavaScript client</span></span>

<span data-ttu-id="d7bc0-104">Por [Rachel Appel](https://twitter.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="d7bc0-104">By [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="d7bc0-105">La biblioteca SignalR de cliente JavaScript de ASP.NET Core permite a los desarrolladores llamar a código de concentrador del lado del servidor.</span><span class="sxs-lookup"><span data-stu-id="d7bc0-105">The ASP.NET Core SignalR JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="d7bc0-106">[Ver o descargar código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ( cómo[descargar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d7bc0-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-opno-locsignalr-client-package"></a><span data-ttu-id="d7bc0-107">Instale SignalR el paquete de cliente</span><span class="sxs-lookup"><span data-stu-id="d7bc0-107">Install the SignalR client package</span></span>

<span data-ttu-id="d7bc0-108">La SignalR biblioteca de cliente de JavaScript se entrega como un paquete [npm.](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="d7bc0-108">The SignalR JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="d7bc0-109">En las secciones siguientes se describen diferentes formas de instalar la biblioteca cliente.</span><span class="sxs-lookup"><span data-stu-id="d7bc0-109">The following sections outline different ways to install the client library.</span></span>

### <a name="install-with-npm"></a><span data-ttu-id="d7bc0-110">Instalar con npm</span><span class="sxs-lookup"><span data-stu-id="d7bc0-110">Install with npm</span></span>

<span data-ttu-id="d7bc0-111">Si usa Visual Studio, ejecute los siguientes comandos desde la consola del Administrador de **paquetes** mientras se encuentra en la carpeta raíz.</span><span class="sxs-lookup"><span data-stu-id="d7bc0-111">If using Visual Studio, run the following commands from **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="d7bc0-112">Para Visual Studio Code, ejecute los siguientes comandos desde el **terminal integrado**.</span><span class="sxs-lookup"><span data-stu-id="d7bc0-112">For Visual Studio Code, run the following commands from the **Integrated Terminal**.</span></span>

::: moniker range=">= aspnetcore-3.0"

```bash
npm init -y
npm install @microsoft/signalr
```

<span data-ttu-id="d7bc0-113">npm instala el contenido del paquete en la carpeta \*node_modules.\\ \*</span><span class="sxs-lookup"><span data-stu-id="d7bc0-113">npm installs the package contents in the *node_modules\\@microsoft\signalr\dist\browser* folder.</span></span> <span data-ttu-id="d7bc0-114">Cree una nueva carpeta denominada *signalr* en la carpeta *wwwroot\\lib.*</span><span class="sxs-lookup"><span data-stu-id="d7bc0-114">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="d7bc0-115">Copie el archivo *signalr.js* en la carpeta *wwwroot-lib-signalr.*</span><span class="sxs-lookup"><span data-stu-id="d7bc0-115">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```bash
npm init -y
npm install @aspnet/signalr
```

<span data-ttu-id="d7bc0-116">npm instala el contenido del paquete en la carpeta \*node_modules.\\ \*</span><span class="sxs-lookup"><span data-stu-id="d7bc0-116">npm installs the package contents in the *node_modules\\@aspnet\signalr\dist\browser* folder.</span></span> <span data-ttu-id="d7bc0-117">Cree una nueva carpeta denominada *signalr* en la carpeta *wwwroot\\lib.*</span><span class="sxs-lookup"><span data-stu-id="d7bc0-117">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="d7bc0-118">Copie el archivo *signalr.js* en la carpeta *wwwroot-lib-signalr.*</span><span class="sxs-lookup"><span data-stu-id="d7bc0-118">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

::: moniker-end

<span data-ttu-id="d7bc0-119">Haga SignalR referencia al cliente `<script>` JavaScript en el elemento.</span><span class="sxs-lookup"><span data-stu-id="d7bc0-119">Reference the SignalR JavaScript client in the `<script>` element.</span></span> <span data-ttu-id="d7bc0-120">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="d7bc0-120">For example:</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a><span data-ttu-id="d7bc0-121">Usar una red de entrega de contenido (CDN)</span><span class="sxs-lookup"><span data-stu-id="d7bc0-121">Use a Content Delivery Network (CDN)</span></span>

<span data-ttu-id="d7bc0-122">Para utilizar la biblioteca de cliente sin el requisito previo npm, haga referencia a una copia hospedada en CDN de la biblioteca de cliente.</span><span class="sxs-lookup"><span data-stu-id="d7bc0-122">To use the client library without the npm prerequisite, reference a CDN-hosted copy of the client library.</span></span> <span data-ttu-id="d7bc0-123">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="d7bc0-123">For example:</span></span>

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/microsoft-signalr/3.1.3/signalr.min.js"></script>
```

<span data-ttu-id="d7bc0-124">La biblioteca cliente está disponible en las siguientes CDN:</span><span class="sxs-lookup"><span data-stu-id="d7bc0-124">The client library is available on the following CDNs:</span></span>

::: moniker range=">= aspnetcore-3.0"

* [<span data-ttu-id="d7bc0-125">cdnjs</span><span class="sxs-lookup"><span data-stu-id="d7bc0-125">cdnjs</span></span>](https://cdnjs.com/libraries/microsoft-signalr)
* [<span data-ttu-id="d7bc0-126">jsDelivr</span><span class="sxs-lookup"><span data-stu-id="d7bc0-126">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@microsoft/signalr)
* [<span data-ttu-id="d7bc0-127">unpkg</span><span class="sxs-lookup"><span data-stu-id="d7bc0-127">unpkg</span></span>](https://unpkg.com/@microsoft/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* [<span data-ttu-id="d7bc0-128">cdnjs</span><span class="sxs-lookup"><span data-stu-id="d7bc0-128">cdnjs</span></span>](https://cdnjs.com/libraries/aspnet-signalr)
* [<span data-ttu-id="d7bc0-129">jsDelivr</span><span class="sxs-lookup"><span data-stu-id="d7bc0-129">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@aspnet/signalr)
* [<span data-ttu-id="d7bc0-130">unpkg</span><span class="sxs-lookup"><span data-stu-id="d7bc0-130">unpkg</span></span>](https://unpkg.com/@aspnet/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

### <a name="install-with-libman"></a><span data-ttu-id="d7bc0-131">Instalar con LibMan</span><span class="sxs-lookup"><span data-stu-id="d7bc0-131">Install with LibMan</span></span>

<span data-ttu-id="d7bc0-132">[LibMan](xref:client-side/libman/index) se puede utilizar para instalar archivos de biblioteca de cliente específicos desde la biblioteca de cliente hospedada en CDN.</span><span class="sxs-lookup"><span data-stu-id="d7bc0-132">[LibMan](xref:client-side/libman/index) can be used to install specific client library files from the CDN-hosted client library.</span></span> <span data-ttu-id="d7bc0-133">Por ejemplo, solo agregue el archivo JavaScript minimizado al proyecto.</span><span class="sxs-lookup"><span data-stu-id="d7bc0-133">For example, only add the minified JavaScript file to the project.</span></span> <span data-ttu-id="d7bc0-134">Para obtener más información sobre este enfoque, consulte [Agregar la SignalR biblioteca de cliente.](xref:tutorials/signalr#add-the-signalr-client-library)</span><span class="sxs-lookup"><span data-stu-id="d7bc0-134">For details on that approach, see [Add the SignalR client library](xref:tutorials/signalr#add-the-signalr-client-library).</span></span>

## <a name="connect-to-a-hub"></a><span data-ttu-id="d7bc0-135">Conéctese a un concentrador</span><span class="sxs-lookup"><span data-stu-id="d7bc0-135">Connect to a hub</span></span>

<span data-ttu-id="d7bc0-136">El código siguiente crea e inicia una conexión.</span><span class="sxs-lookup"><span data-stu-id="d7bc0-136">The following code creates and starts a connection.</span></span> <span data-ttu-id="d7bc0-137">El nombre del concentrador no distingue mayúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="d7bc0-137">The hub's name is case insensitive.</span></span>

[!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=9-13,43-45)]

### <a name="cross-origin-connections"></a><span data-ttu-id="d7bc0-138">Conexiones entre orígenes</span><span class="sxs-lookup"><span data-stu-id="d7bc0-138">Cross-origin connections</span></span>

<span data-ttu-id="d7bc0-139">Normalmente, los exploradores cargan conexiones desde el mismo dominio que la página solicitada.</span><span class="sxs-lookup"><span data-stu-id="d7bc0-139">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="d7bc0-140">Sin embargo, hay ocasiones en las que se requiere una conexión a otro dominio.</span><span class="sxs-lookup"><span data-stu-id="d7bc0-140">However, there are occasions when a connection to another domain is required.</span></span>

<span data-ttu-id="d7bc0-141">Para evitar que un sitio malintencionado lea datos confidenciales de otro sitio, [las conexiones entre orígenes](xref:security/cors) están deshabilitadas de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="d7bc0-141">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="d7bc0-142">Para permitir una solicitud entre orígenes, habilitela en la `Startup` clase.</span><span class="sxs-lookup"><span data-stu-id="d7bc0-142">To allow a cross-origin request, enable it in the `Startup` class.</span></span>

[!code-csharp[Cross-origin connections](javascript-client/sample/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="d7bc0-143">Métodos de concentrador de llamadas desde el cliente</span><span class="sxs-lookup"><span data-stu-id="d7bc0-143">Call hub methods from client</span></span>

<span data-ttu-id="d7bc0-144">Los clientes JavaScript llaman a métodos públicos en concentradores a través del método [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) de [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span><span class="sxs-lookup"><span data-stu-id="d7bc0-144">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) method of the [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span></span> <span data-ttu-id="d7bc0-145">El `invoke` método acepta dos argumentos:</span><span class="sxs-lookup"><span data-stu-id="d7bc0-145">The `invoke` method accepts two arguments:</span></span>

* <span data-ttu-id="d7bc0-146">El nombre del método hub.</span><span class="sxs-lookup"><span data-stu-id="d7bc0-146">The name of the hub method.</span></span> <span data-ttu-id="d7bc0-147">En el ejemplo siguiente, el nombre `SendMessage`del método en el concentrador es .</span><span class="sxs-lookup"><span data-stu-id="d7bc0-147">In the following example, the method name on the hub is `SendMessage`.</span></span>
* <span data-ttu-id="d7bc0-148">Cualquier argumento definido en el método de concentrador.</span><span class="sxs-lookup"><span data-stu-id="d7bc0-148">Any arguments defined in the hub method.</span></span> <span data-ttu-id="d7bc0-149">En el ejemplo siguiente, `message`el nombre del argumento es .</span><span class="sxs-lookup"><span data-stu-id="d7bc0-149">In the following example, the argument name is `message`.</span></span> <span data-ttu-id="d7bc0-150">El código de ejemplo utiliza la sintaxis de función de flecha que se admite en las versiones actuales de todos los exploradores principales, excepto Internet Explorer.</span><span class="sxs-lookup"><span data-stu-id="d7bc0-150">The example code uses arrow function syntax that is supported in current versions of all major browsers except Internet Explorer.</span></span>

  [!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> <span data-ttu-id="d7bc0-151">Si usa Azure SignalR Service en modo *sin servidor,* no puede llamar a métodos de concentrador desde un cliente.</span><span class="sxs-lookup"><span data-stu-id="d7bc0-151">If you're using Azure SignalR Service in *Serverless mode*, you cannot call hub methods from a client.</span></span> <span data-ttu-id="d7bc0-152">Para obtener más información, consulte la [ SignalR documentación del servicio](/azure/azure-signalr/signalr-concept-serverless-development-config).</span><span class="sxs-lookup"><span data-stu-id="d7bc0-152">For more information, see the [SignalR Service documentation](/azure/azure-signalr/signalr-concept-serverless-development-config).</span></span>

<span data-ttu-id="d7bc0-153">El `invoke` método devuelve un JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span><span class="sxs-lookup"><span data-stu-id="d7bc0-153">The `invoke` method returns a JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span></span> <span data-ttu-id="d7bc0-154">Se `Promise` resuelve con el valor devuelto (si existe) cuando devuelve el método en el servidor.</span><span class="sxs-lookup"><span data-stu-id="d7bc0-154">The `Promise` is resolved with the return value (if any) when the method on the server returns.</span></span> <span data-ttu-id="d7bc0-155">Si el método en el servidor `Promise` produce un error, se rechaza con el mensaje de error.</span><span class="sxs-lookup"><span data-stu-id="d7bc0-155">If the method on the server throws an error, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="d7bc0-156">Utilice `then` los `catch` métodos `Promise` y en sí `await` mismo para controlar estos casos (o sintaxis).</span><span class="sxs-lookup"><span data-stu-id="d7bc0-156">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

<span data-ttu-id="d7bc0-157">El `send` método devuelve `Promise`un archivo JavaScript .</span><span class="sxs-lookup"><span data-stu-id="d7bc0-157">The `send` method returns a JavaScript `Promise`.</span></span> <span data-ttu-id="d7bc0-158">El `Promise` se resuelve cuando el mensaje se ha enviado al servidor.</span><span class="sxs-lookup"><span data-stu-id="d7bc0-158">The `Promise` is resolved when the message has been sent to the server.</span></span> <span data-ttu-id="d7bc0-159">Si hay un error al `Promise` enviar el mensaje, se rechaza con el mensaje de error.</span><span class="sxs-lookup"><span data-stu-id="d7bc0-159">If there is an error sending the message, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="d7bc0-160">Utilice `then` los `catch` métodos `Promise` y en sí `await` mismo para controlar estos casos (o sintaxis).</span><span class="sxs-lookup"><span data-stu-id="d7bc0-160">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

> [!NOTE]
> <span data-ttu-id="d7bc0-161">El `send` uso no espera hasta que el servidor haya recibido el mensaje.</span><span class="sxs-lookup"><span data-stu-id="d7bc0-161">Using `send` doesn't wait until the server has received the message.</span></span> <span data-ttu-id="d7bc0-162">Por lo tanto, no es posible devolver datos o errores del servidor.</span><span class="sxs-lookup"><span data-stu-id="d7bc0-162">Consequently, it's not possible to return data or errors from the server.</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="d7bc0-163">Llamar a métodos de cliente desde el concentrador</span><span class="sxs-lookup"><span data-stu-id="d7bc0-163">Call client methods from hub</span></span>

<span data-ttu-id="d7bc0-164">Para recibir mensajes del concentrador, defina [on](/javascript/api/%40aspnet/signalr/hubconnection#on) un método `HubConnection`mediante el método on del archivo .</span><span class="sxs-lookup"><span data-stu-id="d7bc0-164">To receive messages from the hub, define a method using the [on](/javascript/api/%40aspnet/signalr/hubconnection#on) method of the `HubConnection`.</span></span>

* <span data-ttu-id="d7bc0-165">El nombre del método de cliente JavaScript.</span><span class="sxs-lookup"><span data-stu-id="d7bc0-165">The name of the JavaScript client method.</span></span> <span data-ttu-id="d7bc0-166">En el ejemplo siguiente, `ReceiveMessage`el nombre del método es .</span><span class="sxs-lookup"><span data-stu-id="d7bc0-166">In the following example, the method name is `ReceiveMessage`.</span></span>
* <span data-ttu-id="d7bc0-167">Argumentos que el concentrador pasa al método.</span><span class="sxs-lookup"><span data-stu-id="d7bc0-167">Arguments the hub passes to the method.</span></span> <span data-ttu-id="d7bc0-168">En el ejemplo siguiente, `message`el valor del argumento es .</span><span class="sxs-lookup"><span data-stu-id="d7bc0-168">In the following example, the argument value is `message`.</span></span>

[!code-javascript[Receive calls from hub](javascript-client/sample/wwwroot/js/chat.js?range=14-19)]

<span data-ttu-id="d7bc0-169">El código anterior `connection.on` en se ejecuta cuando el código del lado servidor lo llama mediante el [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) método.</span><span class="sxs-lookup"><span data-stu-id="d7bc0-169">The preceding code in `connection.on` runs when server-side code calls it using the [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) method.</span></span>

[!code-csharp[Call client-side](javascript-client/sample/hubs/chathub.cs?range=8-11)]

SignalR<span data-ttu-id="d7bc0-170">determina qué método de cliente llamar haciendo coincidir el `SendAsync` `connection.on`nombre del método y los argumentos definidos en y .</span><span class="sxs-lookup"><span data-stu-id="d7bc0-170"> determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="d7bc0-171">Como práctica recomendada, llame al `HubConnection` método `on` [start](/javascript/api/%40aspnet/signalr/hubconnection#start) en el archivo .</span><span class="sxs-lookup"><span data-stu-id="d7bc0-171">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="d7bc0-172">Al hacerlo, se garantiza que los controladores se registren antes de recibir los mensajes.</span><span class="sxs-lookup"><span data-stu-id="d7bc0-172">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="d7bc0-173">Registro y control de errores</span><span class="sxs-lookup"><span data-stu-id="d7bc0-173">Error handling and logging</span></span>

<span data-ttu-id="d7bc0-174">Encadenar un `catch` método `start` al final del método para controlar los errores del lado cliente.</span><span class="sxs-lookup"><span data-stu-id="d7bc0-174">Chain a `catch` method to the end of the `start` method to handle client-side errors.</span></span> <span data-ttu-id="d7bc0-175">Se `console.error` utiliza para generar errores en la consola del navegador.</span><span class="sxs-lookup"><span data-stu-id="d7bc0-175">Use `console.error` to output errors to the browser's console.</span></span>

[!code-javascript[Error handling](javascript-client/sample/wwwroot/js/chat.js?range=50)]

<span data-ttu-id="d7bc0-176">Configure el seguimiento de registros del lado cliente pasando un registrador y el tipo de evento para registrar cuando se realiza la conexión.</span><span class="sxs-lookup"><span data-stu-id="d7bc0-176">Set up client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="d7bc0-177">Los mensajes se registran con el nivel de registro especificado y superior.</span><span class="sxs-lookup"><span data-stu-id="d7bc0-177">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="d7bc0-178">Los niveles de registro disponibles son los siguientes:</span><span class="sxs-lookup"><span data-stu-id="d7bc0-178">Available log levels are as follows:</span></span>

* <span data-ttu-id="d7bc0-179">`signalR.LogLevel.Error`&ndash; Mensajes de error.</span><span class="sxs-lookup"><span data-stu-id="d7bc0-179">`signalR.LogLevel.Error` &ndash; Error messages.</span></span> <span data-ttu-id="d7bc0-180">Solo `Error` registra mensajes.</span><span class="sxs-lookup"><span data-stu-id="d7bc0-180">Logs `Error` messages only.</span></span>
* <span data-ttu-id="d7bc0-181">`signalR.LogLevel.Warning`&ndash; Mensajes de advertencia sobre posibles errores.</span><span class="sxs-lookup"><span data-stu-id="d7bc0-181">`signalR.LogLevel.Warning` &ndash; Warning messages about potential errors.</span></span> <span data-ttu-id="d7bc0-182">Registros `Warning` `Error` y mensajes.</span><span class="sxs-lookup"><span data-stu-id="d7bc0-182">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="d7bc0-183">`signalR.LogLevel.Information`&ndash; Mensajes de estado sin errores.</span><span class="sxs-lookup"><span data-stu-id="d7bc0-183">`signalR.LogLevel.Information` &ndash; Status messages without errors.</span></span> <span data-ttu-id="d7bc0-184">`Information`Registros `Warning`, `Error` , y mensajes.</span><span class="sxs-lookup"><span data-stu-id="d7bc0-184">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="d7bc0-185">`signalR.LogLevel.Trace`&ndash; Mensajes de seguimiento.</span><span class="sxs-lookup"><span data-stu-id="d7bc0-185">`signalR.LogLevel.Trace` &ndash; Trace messages.</span></span> <span data-ttu-id="d7bc0-186">Registra todo, incluidos los datos transportados entre el concentrador y el cliente.</span><span class="sxs-lookup"><span data-stu-id="d7bc0-186">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="d7bc0-187">Utilice el método [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) en [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) para configurar el nivel de registro.</span><span class="sxs-lookup"><span data-stu-id="d7bc0-187">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="d7bc0-188">Los mensajes se registran en la consola del explorador.</span><span class="sxs-lookup"><span data-stu-id="d7bc0-188">Messages are logged to the browser console.</span></span>

[!code-javascript[Logging levels](javascript-client/sample/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a><span data-ttu-id="d7bc0-189">Reconectar clientes</span><span class="sxs-lookup"><span data-stu-id="d7bc0-189">Reconnect clients</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a><span data-ttu-id="d7bc0-190">Reconexión automática</span><span class="sxs-lookup"><span data-stu-id="d7bc0-190">Automatically reconnect</span></span>

<span data-ttu-id="d7bc0-191">El cliente JavaScript para SignalR se puede configurar `withAutomaticReconnect` para volver a conectarse automáticamente mediante el método en [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="d7bc0-191">The JavaScript client for SignalR can be configured to automatically reconnect using the `withAutomaticReconnect` method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span></span> <span data-ttu-id="d7bc0-192">No se volverá a conectar automáticamente de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="d7bc0-192">It won't automatically reconnect by default.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="d7bc0-193">Sin ningún `withAutomaticReconnect()` parámetro, configura al cliente para esperar 0, 2, 10, y 30 segundos respectivamente antes de intentar cada intento de reconexión, deteniendo después de cuatro intentos fallidos.</span><span class="sxs-lookup"><span data-stu-id="d7bc0-193">Without any parameters, `withAutomaticReconnect()` configures the client to wait 0, 2, 10, and 30 seconds respectively before trying each reconnect attempt, stopping after four failed attempts.</span></span>

<span data-ttu-id="d7bc0-194">Antes de iniciar cualquier `HubConnection` intento de `HubConnectionState.Reconnecting` reconexión, `onreconnecting` el will pasará al `Disconnected` estado y disparar `onclose` sus devoluciones de llamada en lugar de pasar al estado y desencadenar sus devoluciones de llamada como un `HubConnection` sin reconexión automática configurada.</span><span class="sxs-lookup"><span data-stu-id="d7bc0-194">Before starting any reconnect attempts, the `HubConnection` will transition to the `HubConnectionState.Reconnecting` state and fire its `onreconnecting` callbacks instead of transitioning to the `Disconnected` state and triggering its `onclose` callbacks like a `HubConnection` without automatic reconnect configured.</span></span> <span data-ttu-id="d7bc0-195">Esto proporciona una oportunidad para advertir a los usuarios de que se ha perdido la conexión y deshabilitar los elementos de la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="d7bc0-195">This provides an opportunity to warn users that the connection has been lost and to disable UI elements.</span></span>

```javascript
connection.onreconnecting(error => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="d7bc0-196">Si el cliente se vuelve a conectar `HubConnection` correctamente dentro de `Connected` sus primeros `onreconnected` cuatro intentos, volverá a pasar al estado y desencadenará sus devoluciones de llamada.</span><span class="sxs-lookup"><span data-stu-id="d7bc0-196">If the client successfully reconnects within its first four attempts, the `HubConnection` will transition back to the `Connected` state and fire its `onreconnected` callbacks.</span></span> <span data-ttu-id="d7bc0-197">Esto proporciona una oportunidad para informar a los usuarios de que se ha restablecido la conexión.</span><span class="sxs-lookup"><span data-stu-id="d7bc0-197">This provides an opportunity to inform users the connection has been reestablished.</span></span>

<span data-ttu-id="d7bc0-198">Puesto que la conexión parece totalmente `connectionId` nueva en el `onreconnected` servidor, se proporcionará un nuevo a la devolución de llamada.</span><span class="sxs-lookup"><span data-stu-id="d7bc0-198">Since the connection looks entirely new to the server, a new `connectionId` will be provided to the `onreconnected` callback.</span></span>

> [!WARNING]
> <span data-ttu-id="d7bc0-199">El `onreconnected` parámetro `connectionId` de la devolución de `HubConnection` llamada no estará definido si se configuró para omitir la [negociación.](xref:signalr/configuration#configure-client-options)</span><span class="sxs-lookup"><span data-stu-id="d7bc0-199">The `onreconnected` callback's `connectionId` parameter will be undefined if the `HubConnection` was configured to [skip negotiation](xref:signalr/configuration#configure-client-options).</span></span>

```javascript
connection.onreconnected(connectionId => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="d7bc0-200">`withAutomaticReconnect()`no configurará `HubConnection` el para reintentar los errores de inicio iniciales, por lo que los errores de inicio deben controlarse manualmente:</span><span class="sxs-lookup"><span data-stu-id="d7bc0-200">`withAutomaticReconnect()` won't configure the `HubConnection` to retry initial start failures, so start failures need to be handled manually:</span></span>

```javascript
async function start() {
    try {
        await connection.start();
        console.assert(connection.state === signalR.HubConnectionState.Connected);
        console.log("connected");
    } catch (err) {
        console.assert(connection.state === signalR.HubConnectionState.Disconnected);
        console.log(err);
        setTimeout(() => start(), 5000);
    }
};
```

<span data-ttu-id="d7bc0-201">Si el cliente no se vuelve a conectar correctamente `HubConnection` dentro de `Disconnected` sus primeros cuatro intentos, el pasará al estado y desencadenará sus devoluciones de llamada [onclose.](/javascript/api/%40aspnet/signalr/hubconnection#onclose)</span><span class="sxs-lookup"><span data-stu-id="d7bc0-201">If the client doesn't successfully reconnect within its first four attempts, the `HubConnection` will transition to the `Disconnected` state and fire its [onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) callbacks.</span></span> <span data-ttu-id="d7bc0-202">Esto proporciona una oportunidad para informar a los usuarios de que la conexión se ha perdido permanentemente y recomienda actualizar la página:</span><span class="sxs-lookup"><span data-stu-id="d7bc0-202">This provides an opportunity to inform users the connection has been permanently lost and recommend refreshing the page:</span></span>

```javascript
connection.onclose(error => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="d7bc0-203">Para configurar un número personalizado de intentos de reconexión antes `withAutomaticReconnect` de desconectar o cambiar la sincronización de reconexión, valida una matriz de números que representan el retardo en milisegundos para esperar antes de comenzar cada intento de reconexión.</span><span class="sxs-lookup"><span data-stu-id="d7bc0-203">In order to configure a custom number of reconnect attempts before disconnecting or change the reconnect timing, `withAutomaticReconnect` accepts an array of numbers representing the delay in milliseconds to wait before starting each reconnect attempt.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

<span data-ttu-id="d7bc0-204">En el ejemplo anterior `HubConnection` se configura el para comenzar a intentar volver a conectarse inmediatamente después de que se pierda la conexión.</span><span class="sxs-lookup"><span data-stu-id="d7bc0-204">The preceding example configures the `HubConnection` to start attempting reconnects immediately after the connection is lost.</span></span> <span data-ttu-id="d7bc0-205">Esto también es cierto para la configuración predeterminada.</span><span class="sxs-lookup"><span data-stu-id="d7bc0-205">This is also true for the default configuration.</span></span>

<span data-ttu-id="d7bc0-206">Si el primer intento de reconexión falla, el segundo intento de reconexión también se iniciará inmediatamente en lugar de esperar 2 segundos como lo haría en la configuración predeterminada.</span><span class="sxs-lookup"><span data-stu-id="d7bc0-206">If the first reconnect attempt fails, the second reconnect attempt will also start immediately instead of waiting 2 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="d7bc0-207">Si el segundo intento de reconexión falla, el tercer intento de reconexión comenzará en 10 segundos que es otra vez como la configuración predeterminada.</span><span class="sxs-lookup"><span data-stu-id="d7bc0-207">If the second reconnect attempt fails, the third reconnect attempt will start in 10 seconds which is again like the default configuration.</span></span>

<span data-ttu-id="d7bc0-208">A continuación, el comportamiento personalizado vuelve a divergir del comportamiento predeterminado deteniéndose después del tercer error del intento de reconexión en lugar de intentar un intento de reconexión más en otros 30 segundos como lo haría en la configuración predeterminada.</span><span class="sxs-lookup"><span data-stu-id="d7bc0-208">The custom behavior then diverges again from the default behavior by stopping after the third reconnect attempt failure instead of trying one more reconnect attempt in another 30 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="d7bc0-209">Si desea un mayor control sobre la sincronización `withAutomaticReconnect` y el número `IRetryPolicy` de intentos de reconexión automática, acepta un objeto que implementa la interfaz, que tiene un único método denominado `nextRetryDelayInMilliseconds`.</span><span class="sxs-lookup"><span data-stu-id="d7bc0-209">If you want even more control over the timing and number of automatic reconnect attempts, `withAutomaticReconnect` accepts an object implementing the `IRetryPolicy` interface, which has a single method named `nextRetryDelayInMilliseconds`.</span></span>

<span data-ttu-id="d7bc0-210">`nextRetryDelayInMilliseconds`toma un único argumento `RetryContext`con el tipo .</span><span class="sxs-lookup"><span data-stu-id="d7bc0-210">`nextRetryDelayInMilliseconds` takes a single argument with the type `RetryContext`.</span></span> <span data-ttu-id="d7bc0-211">Tiene `RetryContext` tres `previousRetryCount`propiedades: `elapsedMilliseconds` `retryReason` , y `number`que `number` son `Error` un , a y un respectivamente.</span><span class="sxs-lookup"><span data-stu-id="d7bc0-211">The `RetryContext` has three properties: `previousRetryCount`, `elapsedMilliseconds` and `retryReason` which are a `number`, a `number` and an `Error` respectively.</span></span> <span data-ttu-id="d7bc0-212">Antes del primer intento `previousRetryCount` de `elapsedMilliseconds` reconexión, ambos `retryReason` y serán cero, y el será el error que causó la conexión que se perdió.</span><span class="sxs-lookup"><span data-stu-id="d7bc0-212">Before the first reconnect attempt, both `previousRetryCount` and `elapsedMilliseconds` will be zero, and the `retryReason` will be the Error that caused the connection to be lost.</span></span> <span data-ttu-id="d7bc0-213">Después de cada `previousRetryCount` intento fallido de reintento, se incrementará en uno, `elapsedMilliseconds` se actualizará para reflejar `retryReason` la cantidad de tiempo invertido en volver a conectarse hasta ahora en milisegundos, y el será el error que causó el último intento de reconexión fallar.</span><span class="sxs-lookup"><span data-stu-id="d7bc0-213">After each failed retry attempt, `previousRetryCount` will be incremented by one, `elapsedMilliseconds` will be updated to reflect the amount of time spent reconnecting so far in milliseconds, and the `retryReason` will be the Error that caused the last reconnect attempt to fail.</span></span>

<span data-ttu-id="d7bc0-214">`nextRetryDelayInMilliseconds`debe devolver un número que represente el número de milisegundos para esperar antes del siguiente intento de reconexión o `null` si debe `HubConnection` dejar de conectarse.</span><span class="sxs-lookup"><span data-stu-id="d7bc0-214">`nextRetryDelayInMilliseconds` must return either a number representing the number of milliseconds to wait before the next reconnect attempt or `null` if the `HubConnection` should stop reconnecting.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect({
        nextRetryDelayInMilliseconds: retryContext => {
            if (retryContext.elapsedMilliseconds < 60000) {
                // If we've been reconnecting for less than 60 seconds so far,
                // wait between 0 and 10 seconds before the next reconnect attempt.
                return Math.random() * 10000;
            } else {
                // If we've been reconnecting for more than 60 seconds so far, stop reconnecting.
                return null;
            }
        }
    })
    .build();
```

<span data-ttu-id="d7bc0-215">Como alternativa, puede escribir código que vuelva a conectar el cliente manualmente como se muestra en [Volver a conectar manualmente.](#manually-reconnect)</span><span class="sxs-lookup"><span data-stu-id="d7bc0-215">Alternatively, you can write code that will reconnect your client manually as demonstrated in [Manually reconnect](#manually-reconnect).</span></span>

::: moniker-end

### <a name="manually-reconnect"></a><span data-ttu-id="d7bc0-216">Reconexión manual</span><span class="sxs-lookup"><span data-stu-id="d7bc0-216">Manually reconnect</span></span>

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="d7bc0-217">Antes de 3.0, el SignalR cliente JavaScript para no se vuelve a conectar automáticamente.</span><span class="sxs-lookup"><span data-stu-id="d7bc0-217">Prior to 3.0, the JavaScript client for SignalR doesn't automatically reconnect.</span></span> <span data-ttu-id="d7bc0-218">Debe escribir código que vuelva a conectar el cliente manualmente.</span><span class="sxs-lookup"><span data-stu-id="d7bc0-218">You must write code that will reconnect your client manually.</span></span>

::: moniker-end

<span data-ttu-id="d7bc0-219">El código siguiente muestra un enfoque típico de reconexión manual:</span><span class="sxs-lookup"><span data-stu-id="d7bc0-219">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="d7bc0-220">Se crea una función `start` (en este caso, la función) para iniciar la conexión.</span><span class="sxs-lookup"><span data-stu-id="d7bc0-220">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="d7bc0-221">Llame `start` a la función `onclose` en el controlador de eventos de la conexión.</span><span class="sxs-lookup"><span data-stu-id="d7bc0-221">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[Reconnect the JavaScript client](javascript-client/sample/wwwroot/js/chat.js?range=28-40)]

<span data-ttu-id="d7bc0-222">Una implementación del mundo real usaría un retroceso exponencial o volvería a intentarlo un número especificado de veces antes de darse por vencido.</span><span class="sxs-lookup"><span data-stu-id="d7bc0-222">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d7bc0-223">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="d7bc0-223">Additional resources</span></span>

* [<span data-ttu-id="d7bc0-224">Referencia de API de JavaScript</span><span class="sxs-lookup"><span data-stu-id="d7bc0-224">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest)
* [<span data-ttu-id="d7bc0-225">Tutorial de JavaScript</span><span class="sxs-lookup"><span data-stu-id="d7bc0-225">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="d7bc0-226">Tutorial webPack y TypeScript</span><span class="sxs-lookup"><span data-stu-id="d7bc0-226">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="d7bc0-227">Centros</span><span class="sxs-lookup"><span data-stu-id="d7bc0-227">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="d7bc0-228">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="d7bc0-228">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="d7bc0-229">Publicar en Azure</span><span class="sxs-lookup"><span data-stu-id="d7bc0-229">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="d7bc0-230">Solicitudes entre orígenes (CORS)</span><span class="sxs-lookup"><span data-stu-id="d7bc0-230">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* <span data-ttu-id="d7bc0-231">[Documentación SignalR sin servidor de Azure Service](/azure/azure-signalr/signalr-concept-serverless-development-config)</span><span class="sxs-lookup"><span data-stu-id="d7bc0-231">[Azure SignalR Service serverless documentation](/azure/azure-signalr/signalr-concept-serverless-development-config)</span></span>
