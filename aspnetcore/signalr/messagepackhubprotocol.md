---
title: Use el protocolo SignalR de concentrador de MessagePack en ASP.NET Core
author: bradygaster
description: Agregue el protocolo de SignalRconcentrador de MessagePack a ASP.NET Core .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/13/2020
no-loc:
- SignalR
uid: signalr/messagepackhubprotocol
ms.openlocfilehash: bbc34d790387a96bb3b6f75e841b45685eb137ce
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277241"
---
# <a name="use-messagepack-hub-protocol-in-opno-locsignalr-for-aspnet-core"></a><span data-ttu-id="203c5-103">Use el protocolo SignalR de concentrador de MessagePack en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="203c5-103">Use MessagePack Hub Protocol in SignalR for ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="203c5-104">En este artículo se supone que el lector está familiarizado con los temas tratados en [Introducción](xref:tutorials/signalr).</span><span class="sxs-lookup"><span data-stu-id="203c5-104">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="203c5-105">¿Qué es MessagePack?</span><span class="sxs-lookup"><span data-stu-id="203c5-105">What is MessagePack?</span></span>

<span data-ttu-id="203c5-106">[MessagePack](https://msgpack.org/index.html) es un formato de serialización binaria rápido y compacto.</span><span class="sxs-lookup"><span data-stu-id="203c5-106">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="203c5-107">Es útil cuando el rendimiento y el ancho de banda son una preocupación porque crea mensajes más pequeños en comparación con [JSON.](https://www.json.org/)</span><span class="sxs-lookup"><span data-stu-id="203c5-107">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="203c5-108">Los mensajes binarios son ilegibles cuando se examinan los seguimientos de red y los registros a menos que los bytes se pasen a través de un analizador MessagePack.</span><span class="sxs-lookup"><span data-stu-id="203c5-108">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> SignalR<span data-ttu-id="203c5-109">tiene compatibilidad integrada con el formato MessagePack y proporciona API para que el cliente y el servidor las usen.</span><span class="sxs-lookup"><span data-stu-id="203c5-109"> has built-in support for the MessagePack format and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="203c5-110">Configurar MessagePack en el servidor</span><span class="sxs-lookup"><span data-stu-id="203c5-110">Configure MessagePack on the server</span></span>

<span data-ttu-id="203c5-111">Para habilitar el protocolo de concentrador de `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` MessagePack en el servidor, instale el paquete en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="203c5-111">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="203c5-112">En `Startup.ConfigureServices` el método, `AddMessagePackProtocol` `AddSignalR` agregue a la llamada para habilitar la compatibilidad con MessagePack en el servidor.</span><span class="sxs-lookup"><span data-stu-id="203c5-112">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="203c5-113">JSON está habilitado de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="203c5-113">JSON is enabled by default.</span></span> <span data-ttu-id="203c5-114">Agregar MessagePack permite la compatibilidad con clientes JSON y MessagePack.</span><span class="sxs-lookup"><span data-stu-id="203c5-114">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="203c5-115">Para personalizar cómo MessagePack dará `AddMessagePackProtocol` formato a los datos, toma un delegado para configurar opciones.</span><span class="sxs-lookup"><span data-stu-id="203c5-115">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="203c5-116">En ese delegado, la `SerializerOptions` propiedad se puede usar para configurar las opciones de serialización de MessagePack.</span><span class="sxs-lookup"><span data-stu-id="203c5-116">In that delegate, the `SerializerOptions` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="203c5-117">Para obtener más información sobre cómo funcionan los solucionadores, visite la biblioteca MessagePack en [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="203c5-117">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="203c5-118">Los atributos se pueden utilizar en los objetos que desea serializar para definir cómo se deben controlar.</span><span class="sxs-lookup"><span data-stu-id="203c5-118">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.SerializerOptions = MessagePackSerializerOptions.Standard
            .WithResolver(new CustomResolver())
            .WithSecurity(MessagePackSecurity.UntrustedData);
    });
```

> [!WARNING]
> <span data-ttu-id="203c5-119">Recomendamos encarecidamente revisar [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) y aplicar los parches recomendados.</span><span class="sxs-lookup"><span data-stu-id="203c5-119">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="203c5-120">Por ejemplo, `.WithSecurity(MessagePackSecurity.UntrustedData)` al llamar `SerializerOptions`al reemplazar el archivo .</span><span class="sxs-lookup"><span data-stu-id="203c5-120">For example, calling `.WithSecurity(MessagePackSecurity.UntrustedData)` when replacing the `SerializerOptions`.</span></span>

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="203c5-121">Configurar MessagePack en el cliente</span><span class="sxs-lookup"><span data-stu-id="203c5-121">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="203c5-122">JSON está habilitado de forma predeterminada para los clientes admitidos.</span><span class="sxs-lookup"><span data-stu-id="203c5-122">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="203c5-123">Los clientes solo pueden admitir un único protocolo.</span><span class="sxs-lookup"><span data-stu-id="203c5-123">Clients can only support a single protocol.</span></span> <span data-ttu-id="203c5-124">Agregar compatibilidad con MessagePack reemplazará los protocolos configurados anteriormente.</span><span class="sxs-lookup"><span data-stu-id="203c5-124">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="203c5-125">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="203c5-125">.NET client</span></span>

<span data-ttu-id="203c5-126">Para habilitar MessagePack en el cliente `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` .NET, instale el paquete y llame a `AddMessagePackProtocol` `HubConnectionBuilder`.</span><span class="sxs-lookup"><span data-stu-id="203c5-126">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="203c5-127">Esta `AddMessagePackProtocol` llamada toma un delegado para configurar opciones al igual que el servidor.</span><span class="sxs-lookup"><span data-stu-id="203c5-127">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="203c5-128">Cliente de JavaScript</span><span class="sxs-lookup"><span data-stu-id="203c5-128">JavaScript client</span></span>

<span data-ttu-id="203c5-129">El paquete [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm proporciona compatibilidad con MessagePack para el cliente JavaScript.</span><span class="sxs-lookup"><span data-stu-id="203c5-129">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="203c5-130">Instale el paquete ejecutando el siguiente comando en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="203c5-130">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="203c5-131">Después de instalar el paquete npm, el módulo se puede utilizar directamente a través de un cargador de módulos JavaScript o importarse en el navegador haciendo referencia al siguiente archivo:</span><span class="sxs-lookup"><span data-stu-id="203c5-131">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="203c5-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="203c5-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="203c5-133">En un explorador, también se debe hacer referencia a la `msgpack5` biblioteca.</span><span class="sxs-lookup"><span data-stu-id="203c5-133">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="203c5-134">Utilice `<script>` una etiqueta para crear una referencia.</span><span class="sxs-lookup"><span data-stu-id="203c5-134">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="203c5-135">La biblioteca se puede encontrar en *node_modules.msgpack5-dist-msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="203c5-135">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="203c5-136">Cuando se `<script>` utiliza el elemento, el orden es importante.</span><span class="sxs-lookup"><span data-stu-id="203c5-136">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="203c5-137">Si se hace referencia a *signalr-protocol-msgpack.js* antes de *msgpack5.js*, se produce un error al intentar conectarse con MessagePack.</span><span class="sxs-lookup"><span data-stu-id="203c5-137">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="203c5-138">*signalr.js* también es necesario antes *de signalr-protocol-msgpack.js*.</span><span class="sxs-lookup"><span data-stu-id="203c5-138">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="203c5-139">Agregar `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` a `HubConnectionBuilder` la configurará el cliente para usar el protocolo MessagePack al conectarse a un servidor.</span><span class="sxs-lookup"><span data-stu-id="203c5-139">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="203c5-140">En este momento, no hay opciones de configuración para el protocolo MessagePack en el cliente JavaScript.</span><span class="sxs-lookup"><span data-stu-id="203c5-140">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="203c5-141">Peculiaridades MessagePack</span><span class="sxs-lookup"><span data-stu-id="203c5-141">MessagePack quirks</span></span>

<span data-ttu-id="203c5-142">Hay algunos problemas que debe tener en cuenta al usar el protocolo de concentrador de MessagePack.</span><span class="sxs-lookup"><span data-stu-id="203c5-142">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="203c5-143">MessagePack distingue mayúsculas de minúsculas</span><span class="sxs-lookup"><span data-stu-id="203c5-143">MessagePack is case-sensitive</span></span>

<span data-ttu-id="203c5-144">El protocolo MessagePack distingue mayúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="203c5-144">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="203c5-145">Por ejemplo, considere la siguiente clase de C-:</span><span class="sxs-lookup"><span data-stu-id="203c5-145">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="203c5-146">Al enviar desde el cliente de `PascalCased` JavaScript, debe usar nombres de propiedad, ya que las mayúsculas y minúsculas deben coincidir exactamente con la clase de C.</span><span class="sxs-lookup"><span data-stu-id="203c5-146">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="203c5-147">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="203c5-147">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="203c5-148">El `camelCased` uso de nombres no se enlazará correctamente a la clase de C.</span><span class="sxs-lookup"><span data-stu-id="203c5-148">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="203c5-149">Puede evitar esto mediante `Key` el uso del atributo para especificar un nombre diferente para el MessagePack propiedad.</span><span class="sxs-lookup"><span data-stu-id="203c5-149">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="203c5-150">Para obtener más información, consulte la documentación de [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="203c5-150">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="203c5-151">DateTime.Kind no se conserva al serializar/deserializar</span><span class="sxs-lookup"><span data-stu-id="203c5-151">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="203c5-152">El protocolo MessagePack no proporciona una manera `Kind` de `DateTime`codificar el valor de un archivo .</span><span class="sxs-lookup"><span data-stu-id="203c5-152">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="203c5-153">Como resultado, al deserializar una fecha, el protocolo de concentrador `DateTime.Kind` de `DateTimeKind.Local` MessagePack se convertirá al formato UTC si lo que es, de lo contrario, no tocará la hora y la pasará tal cual.</span><span class="sxs-lookup"><span data-stu-id="203c5-153">As a result, when deserializing a date, the MessagePack Hub Protocol will convert to the UTC format if the `DateTime.Kind` is `DateTimeKind.Local` otherwise it will not touch the time and pass it as is.</span></span> <span data-ttu-id="203c5-154">Si está trabajando `DateTime` con valores, le recomendamos que se convierta a UTC antes de enviarlos.</span><span class="sxs-lookup"><span data-stu-id="203c5-154">If you're working with `DateTime` values, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="203c5-155">Conviértalos de UTC a la hora local cuando los reciba.</span><span class="sxs-lookup"><span data-stu-id="203c5-155">Convert them from UTC to local time when you receive them.</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="203c5-156">DateTime.MinValue no es compatible con MessagePack en JavaScript</span><span class="sxs-lookup"><span data-stu-id="203c5-156">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="203c5-157">La biblioteca [msgpack5](https://github.com/mcollina/msgpack5) SignalR utilizada por el cliente `timestamp96` JavaScript no admite el tipo en MessagePack.</span><span class="sxs-lookup"><span data-stu-id="203c5-157">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="203c5-158">Este tipo se utiliza para codificar valores de fecha muy grandes (muy temprano en el pasado o muy lejos en el futuro).</span><span class="sxs-lookup"><span data-stu-id="203c5-158">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="203c5-159">El valor `DateTime.MinValue` `January 1, 0001`de es , que `timestamp96` debe codificarse en un valor.</span><span class="sxs-lookup"><span data-stu-id="203c5-159">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="203c5-160">Debido a esto, no se admite el envío `DateTime.MinValue` a un cliente JavaScript.</span><span class="sxs-lookup"><span data-stu-id="203c5-160">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="203c5-161">Cuando `DateTime.MinValue` el cliente JavaScript lo recibe, se produce el siguiente error:</span><span class="sxs-lookup"><span data-stu-id="203c5-161">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="203c5-162">Por `DateTime.MinValue` lo general, se utiliza para `null` codificar un valor o "falta".</span><span class="sxs-lookup"><span data-stu-id="203c5-162">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="203c5-163">Si necesita codificar ese valor en MessagePack, `DateTime` utilice`DateTime?`un valor que `bool` acepta valores NULL ( ) o codifique un valor independiente que indique si la fecha está presente.</span><span class="sxs-lookup"><span data-stu-id="203c5-163">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="203c5-164">Para obtener más información sobre esta limitación, consulte problema de GitHub [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="203c5-164">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="203c5-165">Compatibilidad con MessagePack en un entorno de compilación "de avanzada"</span><span class="sxs-lookup"><span data-stu-id="203c5-165">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="203c5-166">La biblioteca [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) utilizada por el cliente y el servidor de .NET usa la generación de código para optimizar la serialización.</span><span class="sxs-lookup"><span data-stu-id="203c5-166">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="203c5-167">Como resultado, no se admite de forma predeterminada en entornos que usan la compilación "anticipada de tiempo" (como Xamarin iOS o Unity).</span><span class="sxs-lookup"><span data-stu-id="203c5-167">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="203c5-168">Es posible utilizar MessagePack en estos entornos mediante la "pregeneración" del código serializador/deserializador.</span><span class="sxs-lookup"><span data-stu-id="203c5-168">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="203c5-169">Para obtener más información, consulte la documentación de [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span><span class="sxs-lookup"><span data-stu-id="203c5-169">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span></span> <span data-ttu-id="203c5-170">Una vez que haya generado previamente los serializadores, puede `AddMessagePackProtocol`registrarlos utilizando el delegado de configuración pasado a:</span><span class="sxs-lookup"><span data-stu-id="203c5-170">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        StaticCompositeResolver.Instance.Register(
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        );
        options.SerializerOptions = MessagePackSerializerOptions.Standard
            .WithResolver(StaticCompositeResolver.Instance)
            .WithSecurity(MessagePackSecurity.UntrustedData);
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="203c5-171">Las comprobaciones de tipo son más estrictas en MessagePack</span><span class="sxs-lookup"><span data-stu-id="203c5-171">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="203c5-172">El protocolo de concentrador JSON realizará conversiones de tipos durante la deserialización.</span><span class="sxs-lookup"><span data-stu-id="203c5-172">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="203c5-173">Por ejemplo, si el objeto entrante tiene un`{ foo: 42 }`valor de propiedad que es un `string`número ( ) pero la propiedad de la clase .NET es de tipo , el valor se convertirá.</span><span class="sxs-lookup"><span data-stu-id="203c5-173">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="203c5-174">Sin embargo, MessagePack no realiza esta conversión y producirá una excepción que se puede ver en los registros del lado del servidor (y en la consola):</span><span class="sxs-lookup"><span data-stu-id="203c5-174">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="203c5-175">Para obtener más información sobre esta limitación, consulte problema de GitHub [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="203c5-175">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="203c5-176">Recursos relacionados</span><span class="sxs-lookup"><span data-stu-id="203c5-176">Related resources</span></span>

* [<span data-ttu-id="203c5-177">Primeros pasos</span><span class="sxs-lookup"><span data-stu-id="203c5-177">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="203c5-178">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="203c5-178">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="203c5-179">Cliente de JavaScript</span><span class="sxs-lookup"><span data-stu-id="203c5-179">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="203c5-180">En este artículo se supone que el lector está familiarizado con los temas tratados en [Introducción](xref:tutorials/signalr).</span><span class="sxs-lookup"><span data-stu-id="203c5-180">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="203c5-181">¿Qué es MessagePack?</span><span class="sxs-lookup"><span data-stu-id="203c5-181">What is MessagePack?</span></span>

<span data-ttu-id="203c5-182">[MessagePack](https://msgpack.org/index.html) es un formato de serialización binaria rápido y compacto.</span><span class="sxs-lookup"><span data-stu-id="203c5-182">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="203c5-183">Es útil cuando el rendimiento y el ancho de banda son una preocupación porque crea mensajes más pequeños en comparación con [JSON.](https://www.json.org/)</span><span class="sxs-lookup"><span data-stu-id="203c5-183">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="203c5-184">Los mensajes binarios son ilegibles cuando se examinan los seguimientos de red y los registros a menos que los bytes se pasen a través de un analizador MessagePack.</span><span class="sxs-lookup"><span data-stu-id="203c5-184">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> SignalR<span data-ttu-id="203c5-185">tiene compatibilidad integrada con el formato MessagePack y proporciona API para que el cliente y el servidor las usen.</span><span class="sxs-lookup"><span data-stu-id="203c5-185"> has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="203c5-186">Configurar MessagePack en el servidor</span><span class="sxs-lookup"><span data-stu-id="203c5-186">Configure MessagePack on the server</span></span>

<span data-ttu-id="203c5-187">Para habilitar el protocolo de concentrador de `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` MessagePack en el servidor, instale el paquete en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="203c5-187">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="203c5-188">En `Startup.ConfigureServices` el método, `AddMessagePackProtocol` `AddSignalR` agregue a la llamada para habilitar la compatibilidad con MessagePack en el servidor.</span><span class="sxs-lookup"><span data-stu-id="203c5-188">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="203c5-189">JSON está habilitado de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="203c5-189">JSON is enabled by default.</span></span> <span data-ttu-id="203c5-190">Agregar MessagePack permite la compatibilidad con clientes JSON y MessagePack.</span><span class="sxs-lookup"><span data-stu-id="203c5-190">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="203c5-191">Para personalizar cómo MessagePack dará `AddMessagePackProtocol` formato a los datos, toma un delegado para configurar opciones.</span><span class="sxs-lookup"><span data-stu-id="203c5-191">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="203c5-192">En ese delegado, la `FormatterResolvers` propiedad se puede usar para configurar las opciones de serialización de MessagePack.</span><span class="sxs-lookup"><span data-stu-id="203c5-192">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="203c5-193">Para obtener más información sobre cómo funcionan los solucionadores, visite la biblioteca MessagePack en [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="203c5-193">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="203c5-194">Los atributos se pueden utilizar en los objetos que desea serializar para definir cómo se deben controlar.</span><span class="sxs-lookup"><span data-stu-id="203c5-194">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

> [!WARNING]
> <span data-ttu-id="203c5-195">Recomendamos encarecidamente revisar [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) y aplicar los parches recomendados.</span><span class="sxs-lookup"><span data-stu-id="203c5-195">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="203c5-196">Por ejemplo, `MessagePackSecurity.Active` establecer la `MessagePackSecurity.UntrustedData`propiedad estática en .</span><span class="sxs-lookup"><span data-stu-id="203c5-196">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="203c5-197">Establecer `MessagePackSecurity.Active` la instalación manual de una [versión 1.9.x de MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span><span class="sxs-lookup"><span data-stu-id="203c5-197">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="203c5-198">La `MessagePack` instalación de 1.9.x actualiza el uso de la versión. SignalR</span><span class="sxs-lookup"><span data-stu-id="203c5-198">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="203c5-199">Cuando `MessagePackSecurity.Active` no se `MessagePackSecurity.UntrustedData`establece en , un cliente malintencionado podría provocar una denegación de servicio.</span><span class="sxs-lookup"><span data-stu-id="203c5-199">When `MessagePackSecurity.Active` is not set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="203c5-200">Establecer `MessagePackSecurity.Active` `Program.Main`en , como se muestra en el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="203c5-200">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="203c5-201">Configurar MessagePack en el cliente</span><span class="sxs-lookup"><span data-stu-id="203c5-201">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="203c5-202">JSON está habilitado de forma predeterminada para los clientes admitidos.</span><span class="sxs-lookup"><span data-stu-id="203c5-202">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="203c5-203">Los clientes solo pueden admitir un único protocolo.</span><span class="sxs-lookup"><span data-stu-id="203c5-203">Clients can only support a single protocol.</span></span> <span data-ttu-id="203c5-204">Agregar compatibilidad con MessagePack reemplazará los protocolos configurados anteriormente.</span><span class="sxs-lookup"><span data-stu-id="203c5-204">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="203c5-205">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="203c5-205">.NET client</span></span>

<span data-ttu-id="203c5-206">Para habilitar MessagePack en el cliente `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` .NET, instale el paquete y llame a `AddMessagePackProtocol` `HubConnectionBuilder`.</span><span class="sxs-lookup"><span data-stu-id="203c5-206">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="203c5-207">Esta `AddMessagePackProtocol` llamada toma un delegado para configurar opciones al igual que el servidor.</span><span class="sxs-lookup"><span data-stu-id="203c5-207">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="203c5-208">Cliente de JavaScript</span><span class="sxs-lookup"><span data-stu-id="203c5-208">JavaScript client</span></span>

<span data-ttu-id="203c5-209">El paquete [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm proporciona compatibilidad con MessagePack para el cliente JavaScript.</span><span class="sxs-lookup"><span data-stu-id="203c5-209">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="203c5-210">Instale el paquete ejecutando el siguiente comando en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="203c5-210">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="203c5-211">Después de instalar el paquete npm, el módulo se puede utilizar directamente a través de un cargador de módulos JavaScript o importarse en el navegador haciendo referencia al siguiente archivo:</span><span class="sxs-lookup"><span data-stu-id="203c5-211">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="203c5-212">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="203c5-212">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="203c5-213">En un explorador, también se debe hacer referencia a la `msgpack5` biblioteca.</span><span class="sxs-lookup"><span data-stu-id="203c5-213">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="203c5-214">Utilice `<script>` una etiqueta para crear una referencia.</span><span class="sxs-lookup"><span data-stu-id="203c5-214">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="203c5-215">La biblioteca se puede encontrar en *node_modules.msgpack5-dist-msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="203c5-215">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="203c5-216">Cuando se `<script>` utiliza el elemento, el orden es importante.</span><span class="sxs-lookup"><span data-stu-id="203c5-216">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="203c5-217">Si se hace referencia a *signalr-protocol-msgpack.js* antes de *msgpack5.js*, se produce un error al intentar conectarse con MessagePack.</span><span class="sxs-lookup"><span data-stu-id="203c5-217">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="203c5-218">*signalr.js* también es necesario antes *de signalr-protocol-msgpack.js*.</span><span class="sxs-lookup"><span data-stu-id="203c5-218">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="203c5-219">Agregar `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` a `HubConnectionBuilder` la configurará el cliente para usar el protocolo MessagePack al conectarse a un servidor.</span><span class="sxs-lookup"><span data-stu-id="203c5-219">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="203c5-220">En este momento, no hay opciones de configuración para el protocolo MessagePack en el cliente JavaScript.</span><span class="sxs-lookup"><span data-stu-id="203c5-220">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="203c5-221">Peculiaridades MessagePack</span><span class="sxs-lookup"><span data-stu-id="203c5-221">MessagePack quirks</span></span>

<span data-ttu-id="203c5-222">Hay algunos problemas que debe tener en cuenta al usar el protocolo de concentrador de MessagePack.</span><span class="sxs-lookup"><span data-stu-id="203c5-222">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="203c5-223">MessagePack distingue mayúsculas de minúsculas</span><span class="sxs-lookup"><span data-stu-id="203c5-223">MessagePack is case-sensitive</span></span>

<span data-ttu-id="203c5-224">El protocolo MessagePack distingue mayúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="203c5-224">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="203c5-225">Por ejemplo, considere la siguiente clase de C-:</span><span class="sxs-lookup"><span data-stu-id="203c5-225">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="203c5-226">Al enviar desde el cliente de `PascalCased` JavaScript, debe usar nombres de propiedad, ya que las mayúsculas y minúsculas deben coincidir exactamente con la clase de C.</span><span class="sxs-lookup"><span data-stu-id="203c5-226">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="203c5-227">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="203c5-227">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="203c5-228">El `camelCased` uso de nombres no se enlazará correctamente a la clase de C.</span><span class="sxs-lookup"><span data-stu-id="203c5-228">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="203c5-229">Puede evitar esto mediante `Key` el uso del atributo para especificar un nombre diferente para el MessagePack propiedad.</span><span class="sxs-lookup"><span data-stu-id="203c5-229">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="203c5-230">Para obtener más información, consulte la documentación de [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="203c5-230">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="203c5-231">DateTime.Kind no se conserva al serializar/deserializar</span><span class="sxs-lookup"><span data-stu-id="203c5-231">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="203c5-232">El protocolo MessagePack no proporciona una manera `Kind` de `DateTime`codificar el valor de un archivo .</span><span class="sxs-lookup"><span data-stu-id="203c5-232">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="203c5-233">Como resultado, al deserializar una fecha, el protocolo de concentrador de MessagePack supone que la fecha entrante está en formato UTC.</span><span class="sxs-lookup"><span data-stu-id="203c5-233">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="203c5-234">Si está trabajando `DateTime` con valores en la hora local, se recomienda convertir a UTC antes de enviarlos.</span><span class="sxs-lookup"><span data-stu-id="203c5-234">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="203c5-235">Conviértalos de UTC a la hora local cuando los reciba.</span><span class="sxs-lookup"><span data-stu-id="203c5-235">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="203c5-236">Para obtener más información sobre esta limitación, consulte problema de GitHub [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span><span class="sxs-lookup"><span data-stu-id="203c5-236">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="203c5-237">DateTime.MinValue no es compatible con MessagePack en JavaScript</span><span class="sxs-lookup"><span data-stu-id="203c5-237">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="203c5-238">La biblioteca [msgpack5](https://github.com/mcollina/msgpack5) SignalR utilizada por el cliente `timestamp96` JavaScript no admite el tipo en MessagePack.</span><span class="sxs-lookup"><span data-stu-id="203c5-238">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="203c5-239">Este tipo se utiliza para codificar valores de fecha muy grandes (muy temprano en el pasado o muy lejos en el futuro).</span><span class="sxs-lookup"><span data-stu-id="203c5-239">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="203c5-240">El valor `DateTime.MinValue` `January 1, 0001`de es , que `timestamp96` debe codificarse en un valor.</span><span class="sxs-lookup"><span data-stu-id="203c5-240">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="203c5-241">Debido a esto, no se admite el envío `DateTime.MinValue` a un cliente JavaScript.</span><span class="sxs-lookup"><span data-stu-id="203c5-241">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="203c5-242">Cuando `DateTime.MinValue` el cliente JavaScript lo recibe, se produce el siguiente error:</span><span class="sxs-lookup"><span data-stu-id="203c5-242">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="203c5-243">Por `DateTime.MinValue` lo general, se utiliza para `null` codificar un valor o "falta".</span><span class="sxs-lookup"><span data-stu-id="203c5-243">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="203c5-244">Si necesita codificar ese valor en MessagePack, `DateTime` utilice`DateTime?`un valor que `bool` acepta valores NULL ( ) o codifique un valor independiente que indique si la fecha está presente.</span><span class="sxs-lookup"><span data-stu-id="203c5-244">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="203c5-245">Para obtener más información sobre esta limitación, consulte problema de GitHub [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="203c5-245">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="203c5-246">Compatibilidad con MessagePack en un entorno de compilación "de avanzada"</span><span class="sxs-lookup"><span data-stu-id="203c5-246">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="203c5-247">La biblioteca [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) utilizada por el cliente y el servidor de .NET usa la generación de código para optimizar la serialización.</span><span class="sxs-lookup"><span data-stu-id="203c5-247">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="203c5-248">Como resultado, no se admite de forma predeterminada en entornos que usan la compilación "anticipada de tiempo" (como Xamarin iOS o Unity).</span><span class="sxs-lookup"><span data-stu-id="203c5-248">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="203c5-249">Es posible utilizar MessagePack en estos entornos mediante la "pregeneración" del código serializador/deserializador.</span><span class="sxs-lookup"><span data-stu-id="203c5-249">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="203c5-250">Para obtener más información, consulte la documentación de [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span><span class="sxs-lookup"><span data-stu-id="203c5-250">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="203c5-251">Una vez que haya generado previamente los serializadores, puede `AddMessagePackProtocol`registrarlos utilizando el delegado de configuración pasado a:</span><span class="sxs-lookup"><span data-stu-id="203c5-251">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="203c5-252">Las comprobaciones de tipo son más estrictas en MessagePack</span><span class="sxs-lookup"><span data-stu-id="203c5-252">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="203c5-253">El protocolo de concentrador JSON realizará conversiones de tipos durante la deserialización.</span><span class="sxs-lookup"><span data-stu-id="203c5-253">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="203c5-254">Por ejemplo, si el objeto entrante tiene un`{ foo: 42 }`valor de propiedad que es un `string`número ( ) pero la propiedad de la clase .NET es de tipo , el valor se convertirá.</span><span class="sxs-lookup"><span data-stu-id="203c5-254">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="203c5-255">Sin embargo, MessagePack no realiza esta conversión y producirá una excepción que se puede ver en los registros del lado del servidor (y en la consola):</span><span class="sxs-lookup"><span data-stu-id="203c5-255">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="203c5-256">Para obtener más información sobre esta limitación, consulte problema de GitHub [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="203c5-256">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="203c5-257">Recursos relacionados</span><span class="sxs-lookup"><span data-stu-id="203c5-257">Related resources</span></span>

* [<span data-ttu-id="203c5-258">Primeros pasos</span><span class="sxs-lookup"><span data-stu-id="203c5-258">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="203c5-259">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="203c5-259">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="203c5-260">Cliente de JavaScript</span><span class="sxs-lookup"><span data-stu-id="203c5-260">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="203c5-261">En este artículo se supone que el lector está familiarizado con los temas tratados en [Introducción](xref:tutorials/signalr).</span><span class="sxs-lookup"><span data-stu-id="203c5-261">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="203c5-262">¿Qué es MessagePack?</span><span class="sxs-lookup"><span data-stu-id="203c5-262">What is MessagePack?</span></span>

<span data-ttu-id="203c5-263">[MessagePack](https://msgpack.org/index.html) es un formato de serialización binaria rápido y compacto.</span><span class="sxs-lookup"><span data-stu-id="203c5-263">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="203c5-264">Es útil cuando el rendimiento y el ancho de banda son una preocupación porque crea mensajes más pequeños en comparación con [JSON.](https://www.json.org/)</span><span class="sxs-lookup"><span data-stu-id="203c5-264">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="203c5-265">Los mensajes binarios son ilegibles cuando se examinan los seguimientos de red y los registros a menos que los bytes se pasen a través de un analizador MessagePack.</span><span class="sxs-lookup"><span data-stu-id="203c5-265">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> SignalR<span data-ttu-id="203c5-266">tiene compatibilidad integrada con el formato MessagePack y proporciona API para que el cliente y el servidor las usen.</span><span class="sxs-lookup"><span data-stu-id="203c5-266"> has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="203c5-267">Configurar MessagePack en el servidor</span><span class="sxs-lookup"><span data-stu-id="203c5-267">Configure MessagePack on the server</span></span>

<span data-ttu-id="203c5-268">Para habilitar el protocolo de concentrador de `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` MessagePack en el servidor, instale el paquete en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="203c5-268">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="203c5-269">En `Startup.ConfigureServices` el método, `AddMessagePackProtocol` `AddSignalR` agregue a la llamada para habilitar la compatibilidad con MessagePack en el servidor.</span><span class="sxs-lookup"><span data-stu-id="203c5-269">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="203c5-270">JSON está habilitado de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="203c5-270">JSON is enabled by default.</span></span> <span data-ttu-id="203c5-271">Agregar MessagePack permite la compatibilidad con clientes JSON y MessagePack.</span><span class="sxs-lookup"><span data-stu-id="203c5-271">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="203c5-272">Para personalizar cómo MessagePack dará `AddMessagePackProtocol` formato a los datos, toma un delegado para configurar opciones.</span><span class="sxs-lookup"><span data-stu-id="203c5-272">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="203c5-273">En ese delegado, la `FormatterResolvers` propiedad se puede usar para configurar las opciones de serialización de MessagePack.</span><span class="sxs-lookup"><span data-stu-id="203c5-273">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="203c5-274">Para obtener más información sobre cómo funcionan los solucionadores, visite la biblioteca MessagePack en [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="203c5-274">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="203c5-275">Los atributos se pueden utilizar en los objetos que desea serializar para definir cómo se deben controlar.</span><span class="sxs-lookup"><span data-stu-id="203c5-275">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

> [!WARNING]
> <span data-ttu-id="203c5-276">Recomendamos encarecidamente revisar [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) y aplicar los parches recomendados.</span><span class="sxs-lookup"><span data-stu-id="203c5-276">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="203c5-277">Por ejemplo, `MessagePackSecurity.Active` establecer la `MessagePackSecurity.UntrustedData`propiedad estática en .</span><span class="sxs-lookup"><span data-stu-id="203c5-277">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="203c5-278">Establecer `MessagePackSecurity.Active` la instalación manual de una [versión 1.9.x de MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span><span class="sxs-lookup"><span data-stu-id="203c5-278">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="203c5-279">La `MessagePack` instalación de 1.9.x actualiza el uso de la versión. SignalR</span><span class="sxs-lookup"><span data-stu-id="203c5-279">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="203c5-280">Cuando `MessagePackSecurity.Active` no se `MessagePackSecurity.UntrustedData`establece en , un cliente malintencionado podría provocar una denegación de servicio.</span><span class="sxs-lookup"><span data-stu-id="203c5-280">When `MessagePackSecurity.Active` is not set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="203c5-281">Establecer `MessagePackSecurity.Active` `Program.Main`en , como se muestra en el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="203c5-281">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="203c5-282">Configurar MessagePack en el cliente</span><span class="sxs-lookup"><span data-stu-id="203c5-282">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="203c5-283">JSON está habilitado de forma predeterminada para los clientes admitidos.</span><span class="sxs-lookup"><span data-stu-id="203c5-283">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="203c5-284">Los clientes solo pueden admitir un único protocolo.</span><span class="sxs-lookup"><span data-stu-id="203c5-284">Clients can only support a single protocol.</span></span> <span data-ttu-id="203c5-285">Agregar compatibilidad con MessagePack reemplazará los protocolos configurados anteriormente.</span><span class="sxs-lookup"><span data-stu-id="203c5-285">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="203c5-286">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="203c5-286">.NET client</span></span>

<span data-ttu-id="203c5-287">Para habilitar MessagePack en el cliente `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` .NET, instale el paquete y llame a `AddMessagePackProtocol` `HubConnectionBuilder`.</span><span class="sxs-lookup"><span data-stu-id="203c5-287">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="203c5-288">Esta `AddMessagePackProtocol` llamada toma un delegado para configurar opciones al igual que el servidor.</span><span class="sxs-lookup"><span data-stu-id="203c5-288">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="203c5-289">Cliente de JavaScript</span><span class="sxs-lookup"><span data-stu-id="203c5-289">JavaScript client</span></span>

<span data-ttu-id="203c5-290">El paquete [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) npm proporciona compatibilidad con MessagePack para el cliente JavaScript.</span><span class="sxs-lookup"><span data-stu-id="203c5-290">MessagePack support for the JavaScript client is provided by the [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="203c5-291">Instale el paquete ejecutando el siguiente comando en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="203c5-291">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @aspnet/signalr-protocol-msgpack
```

<span data-ttu-id="203c5-292">Después de instalar el paquete npm, el módulo se puede utilizar directamente a través de un cargador de módulos JavaScript o importarse en el navegador haciendo referencia al siguiente archivo:</span><span class="sxs-lookup"><span data-stu-id="203c5-292">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="203c5-293">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="203c5-293">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span>

<span data-ttu-id="203c5-294">En un explorador, también se debe hacer referencia a la `msgpack5` biblioteca.</span><span class="sxs-lookup"><span data-stu-id="203c5-294">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="203c5-295">Utilice `<script>` una etiqueta para crear una referencia.</span><span class="sxs-lookup"><span data-stu-id="203c5-295">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="203c5-296">La biblioteca se puede encontrar en *node_modules.msgpack5-dist-msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="203c5-296">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="203c5-297">Cuando se `<script>` utiliza el elemento, el orden es importante.</span><span class="sxs-lookup"><span data-stu-id="203c5-297">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="203c5-298">Si se hace referencia a *signalr-protocol-msgpack.js* antes de *msgpack5.js*, se produce un error al intentar conectarse con MessagePack.</span><span class="sxs-lookup"><span data-stu-id="203c5-298">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="203c5-299">*signalr.js* también es necesario antes *de signalr-protocol-msgpack.js*.</span><span class="sxs-lookup"><span data-stu-id="203c5-299">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="203c5-300">Agregar `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` a `HubConnectionBuilder` la configurará el cliente para usar el protocolo MessagePack al conectarse a un servidor.</span><span class="sxs-lookup"><span data-stu-id="203c5-300">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="203c5-301">En este momento, no hay opciones de configuración para el protocolo MessagePack en el cliente JavaScript.</span><span class="sxs-lookup"><span data-stu-id="203c5-301">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="203c5-302">Peculiaridades MessagePack</span><span class="sxs-lookup"><span data-stu-id="203c5-302">MessagePack quirks</span></span>

<span data-ttu-id="203c5-303">Hay algunos problemas que debe tener en cuenta al usar el protocolo de concentrador de MessagePack.</span><span class="sxs-lookup"><span data-stu-id="203c5-303">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="203c5-304">MessagePack distingue mayúsculas de minúsculas</span><span class="sxs-lookup"><span data-stu-id="203c5-304">MessagePack is case-sensitive</span></span>

<span data-ttu-id="203c5-305">El protocolo MessagePack distingue mayúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="203c5-305">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="203c5-306">Por ejemplo, considere la siguiente clase de C-:</span><span class="sxs-lookup"><span data-stu-id="203c5-306">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="203c5-307">Al enviar desde el cliente de `PascalCased` JavaScript, debe usar nombres de propiedad, ya que las mayúsculas y minúsculas deben coincidir exactamente con la clase de C.</span><span class="sxs-lookup"><span data-stu-id="203c5-307">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="203c5-308">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="203c5-308">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="203c5-309">El `camelCased` uso de nombres no se enlazará correctamente a la clase de C.</span><span class="sxs-lookup"><span data-stu-id="203c5-309">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="203c5-310">Puede evitar esto mediante `Key` el uso del atributo para especificar un nombre diferente para el MessagePack propiedad.</span><span class="sxs-lookup"><span data-stu-id="203c5-310">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="203c5-311">Para obtener más información, consulte la documentación de [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="203c5-311">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="203c5-312">DateTime.Kind no se conserva al serializar/deserializar</span><span class="sxs-lookup"><span data-stu-id="203c5-312">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="203c5-313">El protocolo MessagePack no proporciona una manera `Kind` de `DateTime`codificar el valor de un archivo .</span><span class="sxs-lookup"><span data-stu-id="203c5-313">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="203c5-314">Como resultado, al deserializar una fecha, el protocolo de concentrador de MessagePack supone que la fecha entrante está en formato UTC.</span><span class="sxs-lookup"><span data-stu-id="203c5-314">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="203c5-315">Si está trabajando `DateTime` con valores en la hora local, se recomienda convertir a UTC antes de enviarlos.</span><span class="sxs-lookup"><span data-stu-id="203c5-315">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="203c5-316">Conviértalos de UTC a la hora local cuando los reciba.</span><span class="sxs-lookup"><span data-stu-id="203c5-316">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="203c5-317">Para obtener más información sobre esta limitación, consulte problema de GitHub [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span><span class="sxs-lookup"><span data-stu-id="203c5-317">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="203c5-318">DateTime.MinValue no es compatible con MessagePack en JavaScript</span><span class="sxs-lookup"><span data-stu-id="203c5-318">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="203c5-319">La biblioteca [msgpack5](https://github.com/mcollina/msgpack5) SignalR utilizada por el cliente `timestamp96` JavaScript no admite el tipo en MessagePack.</span><span class="sxs-lookup"><span data-stu-id="203c5-319">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="203c5-320">Este tipo se utiliza para codificar valores de fecha muy grandes (muy temprano en el pasado o muy lejos en el futuro).</span><span class="sxs-lookup"><span data-stu-id="203c5-320">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="203c5-321">El valor `DateTime.MinValue` `January 1, 0001` de es que `timestamp96` se debe codificar en un valor.</span><span class="sxs-lookup"><span data-stu-id="203c5-321">The value of `DateTime.MinValue` is `January 1, 0001` which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="203c5-322">Debido a esto, no se admite el envío `DateTime.MinValue` a un cliente JavaScript.</span><span class="sxs-lookup"><span data-stu-id="203c5-322">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="203c5-323">Cuando `DateTime.MinValue` el cliente JavaScript lo recibe, se produce el siguiente error:</span><span class="sxs-lookup"><span data-stu-id="203c5-323">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="203c5-324">Por `DateTime.MinValue` lo general, se utiliza para `null` codificar un valor o "falta".</span><span class="sxs-lookup"><span data-stu-id="203c5-324">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="203c5-325">Si necesita codificar ese valor en MessagePack, `DateTime` utilice`DateTime?`un valor que `bool` acepta valores NULL ( ) o codifique un valor independiente que indique si la fecha está presente.</span><span class="sxs-lookup"><span data-stu-id="203c5-325">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="203c5-326">Para obtener más información sobre esta limitación, consulte problema de GitHub [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="203c5-326">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="203c5-327">Compatibilidad con MessagePack en un entorno de compilación "de avanzada"</span><span class="sxs-lookup"><span data-stu-id="203c5-327">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="203c5-328">La biblioteca [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) utilizada por el cliente y el servidor de .NET usa la generación de código para optimizar la serialización.</span><span class="sxs-lookup"><span data-stu-id="203c5-328">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="203c5-329">Como resultado, no se admite de forma predeterminada en entornos que usan la compilación "anticipada de tiempo" (como Xamarin iOS o Unity).</span><span class="sxs-lookup"><span data-stu-id="203c5-329">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="203c5-330">Es posible utilizar MessagePack en estos entornos mediante la "pregeneración" del código serializador/deserializador.</span><span class="sxs-lookup"><span data-stu-id="203c5-330">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="203c5-331">Para obtener más información, consulte la documentación de [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span><span class="sxs-lookup"><span data-stu-id="203c5-331">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="203c5-332">Una vez que haya generado previamente los serializadores, puede `AddMessagePackProtocol`registrarlos utilizando el delegado de configuración pasado a:</span><span class="sxs-lookup"><span data-stu-id="203c5-332">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="203c5-333">Las comprobaciones de tipo son más estrictas en MessagePack</span><span class="sxs-lookup"><span data-stu-id="203c5-333">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="203c5-334">El protocolo de concentrador JSON realizará conversiones de tipos durante la deserialización.</span><span class="sxs-lookup"><span data-stu-id="203c5-334">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="203c5-335">Por ejemplo, si el objeto entrante tiene un`{ foo: 42 }`valor de propiedad que es un `string`número ( ) pero la propiedad de la clase .NET es de tipo , el valor se convertirá.</span><span class="sxs-lookup"><span data-stu-id="203c5-335">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="203c5-336">Sin embargo, MessagePack no realiza esta conversión y producirá una excepción que se puede ver en los registros del lado del servidor (y en la consola):</span><span class="sxs-lookup"><span data-stu-id="203c5-336">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="203c5-337">Para obtener más información sobre esta limitación, consulte problema de GitHub [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="203c5-337">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="203c5-338">Recursos relacionados</span><span class="sxs-lookup"><span data-stu-id="203c5-338">Related resources</span></span>

* [<span data-ttu-id="203c5-339">Primeros pasos</span><span class="sxs-lookup"><span data-stu-id="203c5-339">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="203c5-340">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="203c5-340">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="203c5-341">Cliente de JavaScript</span><span class="sxs-lookup"><span data-stu-id="203c5-341">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end
