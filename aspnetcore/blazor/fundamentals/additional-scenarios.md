---
title: Configuración del modelo de hospedaje de Blazor en ASP.NET Core
author: guardrex
description: Obtenga información sobre más escenarios de configuración del modelo de hospedaje de Blazor en ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/additional-scenarios
ms.openlocfilehash: 72a8b59b06e40f6f85abe41217ae564f82c8d89c
ms.sourcegitcommit: 1833870ad0845326fb764fef1b530a07b9b5b099
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/25/2020
ms.locfileid: "85347078"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="f2b8c-103">Configuración del modelo de hospedaje de Blazor en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f2b8c-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="f2b8c-104">Por [Daniel Roth](https://github.com/danroth27) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="f2b8c-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="f2b8c-105">En este artículo se describe la configuración del modelo de hospedaje.</span><span class="sxs-lookup"><span data-stu-id="f2b8c-105">This article covers hosting model configuration.</span></span>

### <a name="signalr-cross-origin-negotiation-for-authentication"></a><span data-ttu-id="f2b8c-106">Negociación entre orígenes de SignalR para la autenticación</span><span class="sxs-lookup"><span data-stu-id="f2b8c-106">SignalR cross-origin negotiation for authentication</span></span>

<span data-ttu-id="f2b8c-107">*Esta sección es aplicable a WebAssembly de Blazor.*</span><span class="sxs-lookup"><span data-stu-id="f2b8c-107">*This section applies to Blazor WebAssembly.*</span></span>

<span data-ttu-id="f2b8c-108">Para configurar el cliente subyacente de SignalR para que envíe credenciales, como cookies o encabezados de autenticación HTTP:</span><span class="sxs-lookup"><span data-stu-id="f2b8c-108">To configure SignalR's underlying client to send credentials, such as cookies or HTTP authentication headers:</span></span>

* <span data-ttu-id="f2b8c-109">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> para establecer <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> en solicitudes de [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) entre orígenes:</span><span class="sxs-lookup"><span data-stu-id="f2b8c-109">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> to set <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> on cross-origin [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) requests:</span></span>

  ```csharp
  public class IncludeRequestCredentialsMessageHandler : DelegatingHandler
  {
      protected override Task<HttpResponseMessage> SendAsync(
          HttpRequestMessage request, CancellationToken cancellationToken)
      {
          request.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
          return base.SendAsync(request, cancellationToken);
      }
  }
  ```

* <span data-ttu-id="f2b8c-110">Asigne <xref:System.Net.Http.HttpMessageHandler> a la opción <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory>:</span><span class="sxs-lookup"><span data-stu-id="f2b8c-110">Assign the <xref:System.Net.Http.HttpMessageHandler> to the <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> option:</span></span>

  ```csharp
  var connection = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessageHandler { InnerHandler = innerHandler };
      }).Build();
  ```

<span data-ttu-id="f2b8c-111">Para obtener más información, vea <xref:signalr/configuration#configure-additional-options>.</span><span class="sxs-lookup"><span data-stu-id="f2b8c-111">For more information, see <xref:signalr/configuration#configure-additional-options>.</span></span>

## <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="f2b8c-112">Reflejo del estado de la conexión en la interfaz de usuario</span><span class="sxs-lookup"><span data-stu-id="f2b8c-112">Reflect the connection state in the UI</span></span>

<span data-ttu-id="f2b8c-113">*Esta sección es aplicable a Blazor Server.*</span><span class="sxs-lookup"><span data-stu-id="f2b8c-113">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="f2b8c-114">Cuando el cliente detecta que se ha perdido la conexión, se muestra al usuario una interfaz de usuario predeterminada mientras el cliente intenta volver a conectarse.</span><span class="sxs-lookup"><span data-stu-id="f2b8c-114">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="f2b8c-115">Si se produce un error en la reconexión, se proporciona al usuario la opción de volver a intentarlo.</span><span class="sxs-lookup"><span data-stu-id="f2b8c-115">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="f2b8c-116">Para personalizar la interfaz de usuario, defina un elemento con un valor de `id` de `components-reconnect-modal` en el elemento `<body>` de la página de `_Host.cshtml` Razor:</span><span class="sxs-lookup"><span data-stu-id="f2b8c-116">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the `_Host.cshtml` Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="f2b8c-117">En la tabla siguiente se describen las clases de CSS aplicadas al elemento `components-reconnect-modal`.</span><span class="sxs-lookup"><span data-stu-id="f2b8c-117">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="f2b8c-118">Clase de CSS</span><span class="sxs-lookup"><span data-stu-id="f2b8c-118">CSS class</span></span>                       | <span data-ttu-id="f2b8c-119">Indica&hellip;</span><span class="sxs-lookup"><span data-stu-id="f2b8c-119">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="f2b8c-120">Una conexión perdida.</span><span class="sxs-lookup"><span data-stu-id="f2b8c-120">A lost connection.</span></span> <span data-ttu-id="f2b8c-121">El cliente intenta volver a conectarse.</span><span class="sxs-lookup"><span data-stu-id="f2b8c-121">The client is attempting to reconnect.</span></span> <span data-ttu-id="f2b8c-122">Se muestra el modal.</span><span class="sxs-lookup"><span data-stu-id="f2b8c-122">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="f2b8c-123">Se restablece una conexión activa con el servidor.</span><span class="sxs-lookup"><span data-stu-id="f2b8c-123">An active connection is re-established to the server.</span></span> <span data-ttu-id="f2b8c-124">Se oculta el modal.</span><span class="sxs-lookup"><span data-stu-id="f2b8c-124">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="f2b8c-125">Error de reconexión, probablemente debido a un error de la red.</span><span class="sxs-lookup"><span data-stu-id="f2b8c-125">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="f2b8c-126">Para intentar la reconexión, llame a `window.Blazor.reconnect()`.</span><span class="sxs-lookup"><span data-stu-id="f2b8c-126">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="f2b8c-127">Reconexión rechazada.</span><span class="sxs-lookup"><span data-stu-id="f2b8c-127">Reconnection rejected.</span></span> <span data-ttu-id="f2b8c-128">Se ha alcanzado el servidor pero se ha rechazado la conexión y se ha perdido el estado del usuario en el servidor.</span><span class="sxs-lookup"><span data-stu-id="f2b8c-128">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="f2b8c-129">Para volver a cargar la aplicación, llame a `location.reload()`.</span><span class="sxs-lookup"><span data-stu-id="f2b8c-129">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="f2b8c-130">Este estado de conexión se puede producir cuando:</span><span class="sxs-lookup"><span data-stu-id="f2b8c-130">This connection state may result when:</span></span><ul><li><span data-ttu-id="f2b8c-131">Se produce un bloqueo en el circuito del lado servidor.</span><span class="sxs-lookup"><span data-stu-id="f2b8c-131">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="f2b8c-132">El cliente se desconecta el tiempo suficiente para que el servidor quite el estado del usuario.</span><span class="sxs-lookup"><span data-stu-id="f2b8c-132">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="f2b8c-133">Se eliminan las instancias de los componentes con las que el usuario interactúa.</span><span class="sxs-lookup"><span data-stu-id="f2b8c-133">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="f2b8c-134">El servidor se reinicia o se recicla el proceso de trabajo de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f2b8c-134">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

## <a name="render-mode"></a><span data-ttu-id="f2b8c-135">Modo de representación</span><span class="sxs-lookup"><span data-stu-id="f2b8c-135">Render mode</span></span>

<span data-ttu-id="f2b8c-136">*Esta sección es aplicable a Blazor Server.*</span><span class="sxs-lookup"><span data-stu-id="f2b8c-136">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="f2b8c-137">Las aplicaciones Blazor Server se configuran de forma predeterminada para realizar una representación previa de la interfaz de usuario en el servidor antes de que se establezca la conexión del cliente con él.</span><span class="sxs-lookup"><span data-stu-id="f2b8c-137">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="f2b8c-138">Esto se configura en la página `_Host.cshtml` Razor:</span><span class="sxs-lookup"><span data-stu-id="f2b8c-138">This is set up in the `_Host.cshtml` Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="f2b8c-139"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> configura si el componente:</span><span class="sxs-lookup"><span data-stu-id="f2b8c-139"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="f2b8c-140">Se representa previamente en la página.</span><span class="sxs-lookup"><span data-stu-id="f2b8c-140">Is prerendered into the page.</span></span>
* <span data-ttu-id="f2b8c-141">Se representa como HTML estático en la página o si incluye la información necesaria para arrancar una aplicación Blazor desde el agente de usuario.</span><span class="sxs-lookup"><span data-stu-id="f2b8c-141">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> | <span data-ttu-id="f2b8c-142">Descripción</span><span class="sxs-lookup"><span data-stu-id="f2b8c-142">Description</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="f2b8c-143">Representa el componente en código HTML estático e incluye un marcador para una aplicación Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="f2b8c-143">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="f2b8c-144">Cuando se inicia el agente de usuario, este marcador se usa para arrancar una aplicación Blazor.</span><span class="sxs-lookup"><span data-stu-id="f2b8c-144">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="f2b8c-145">Representa un marcador para una aplicación Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="f2b8c-145">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="f2b8c-146">La salida del componente no está incluida.</span><span class="sxs-lookup"><span data-stu-id="f2b8c-146">Output from the component isn't included.</span></span> <span data-ttu-id="f2b8c-147">Cuando se inicia el agente de usuario, este marcador se usa para arrancar una aplicación Blazor.</span><span class="sxs-lookup"><span data-stu-id="f2b8c-147">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="f2b8c-148">Representa el componente en HTML estático.</span><span class="sxs-lookup"><span data-stu-id="f2b8c-148">Renders the component into static HTML.</span></span> |

<span data-ttu-id="f2b8c-149">No se admite la representación de componentes de servidor desde una página HTML estática.</span><span class="sxs-lookup"><span data-stu-id="f2b8c-149">Rendering server components from a static HTML page isn't supported.</span></span>

## <a name="configure-the-signalr-client-for-blazor-server-apps"></a><span data-ttu-id="f2b8c-150">Configuración del cliente de SignalR para aplicaciones de servidor Blazor</span><span class="sxs-lookup"><span data-stu-id="f2b8c-150">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="f2b8c-151">*Esta sección es aplicable a Blazor Server.*</span><span class="sxs-lookup"><span data-stu-id="f2b8c-151">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="f2b8c-152">En ocasiones tendrá que configurar el cliente de SignalR que usan las aplicaciones de servidor Blazor.</span><span class="sxs-lookup"><span data-stu-id="f2b8c-152">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="f2b8c-153">Por ejemplo, es posible que quiera configurar el registro en el cliente de SignalR para diagnosticar un problema de conexión.</span><span class="sxs-lookup"><span data-stu-id="f2b8c-153">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="f2b8c-154">Para configurar el cliente de SignalR en el archivo `Pages/_Host.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="f2b8c-154">To configure the SignalR client in the `Pages/_Host.cshtml` file:</span></span>

* <span data-ttu-id="f2b8c-155">Agregue un atributo `autostart="false"` a la etiqueta `<script>` para el script `blazor.server.js`.</span><span class="sxs-lookup"><span data-stu-id="f2b8c-155">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="f2b8c-156">Llame a `Blazor.start` y pase un objeto de configuración que especifique el generador de SignalR.</span><span class="sxs-lookup"><span data-stu-id="f2b8c-156">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

```html
<script src="_framework/blazor.server.js" autostart="false"></script>
<script>
  Blazor.start({
    configureSignalR: function (builder) {
      builder.configureLogging("information"); // LogLevel.Information
    }
  });
</script>
```

## <a name="additional-resources"></a><span data-ttu-id="f2b8c-157">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="f2b8c-157">Additional resources</span></span>

* <xref:fundamentals/logging/index>
