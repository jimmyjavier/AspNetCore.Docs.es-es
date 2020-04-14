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
# <a name="use-messagepack-hub-protocol-in-opno-locsignalr-for-aspnet-core"></a>Use el protocolo SignalR de concentrador de MessagePack en ASP.NET Core

::: moniker range=">= aspnetcore-5.0"

En este artículo se supone que el lector está familiarizado con los temas tratados en [Introducción](xref:tutorials/signalr).

## <a name="what-is-messagepack"></a>¿Qué es MessagePack?

[MessagePack](https://msgpack.org/index.html) es un formato de serialización binaria rápido y compacto. Es útil cuando el rendimiento y el ancho de banda son una preocupación porque crea mensajes más pequeños en comparación con [JSON.](https://www.json.org/) Los mensajes binarios son ilegibles cuando se examinan los seguimientos de red y los registros a menos que los bytes se pasen a través de un analizador MessagePack. SignalRtiene compatibilidad integrada con el formato MessagePack y proporciona API para que el cliente y el servidor las usen.

## <a name="configure-messagepack-on-the-server"></a>Configurar MessagePack en el servidor

Para habilitar el protocolo de concentrador de `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` MessagePack en el servidor, instale el paquete en la aplicación. En `Startup.ConfigureServices` el método, `AddMessagePackProtocol` `AddSignalR` agregue a la llamada para habilitar la compatibilidad con MessagePack en el servidor.

> [!NOTE]
> JSON está habilitado de forma predeterminada. Agregar MessagePack permite la compatibilidad con clientes JSON y MessagePack.

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

Para personalizar cómo MessagePack dará `AddMessagePackProtocol` formato a los datos, toma un delegado para configurar opciones. En ese delegado, la `SerializerOptions` propiedad se puede usar para configurar las opciones de serialización de MessagePack. Para obtener más información sobre cómo funcionan los solucionadores, visite la biblioteca MessagePack en [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp). Los atributos se pueden utilizar en los objetos que desea serializar para definir cómo se deben controlar.

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
> Recomendamos encarecidamente revisar [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) y aplicar los parches recomendados. Por ejemplo, `.WithSecurity(MessagePackSecurity.UntrustedData)` al llamar `SerializerOptions`al reemplazar el archivo .

## <a name="configure-messagepack-on-the-client"></a>Configurar MessagePack en el cliente

> [!NOTE]
> JSON está habilitado de forma predeterminada para los clientes admitidos. Los clientes solo pueden admitir un único protocolo. Agregar compatibilidad con MessagePack reemplazará los protocolos configurados anteriormente.

### <a name="net-client"></a>Cliente .NET

Para habilitar MessagePack en el cliente `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` .NET, instale el paquete y llame a `AddMessagePackProtocol` `HubConnectionBuilder`.

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> Esta `AddMessagePackProtocol` llamada toma un delegado para configurar opciones al igual que el servidor.

### <a name="javascript-client"></a>Cliente de JavaScript

El paquete [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm proporciona compatibilidad con MessagePack para el cliente JavaScript. Instale el paquete ejecutando el siguiente comando en un shell de comandos:

```bash
npm install @microsoft/signalr-protocol-msgpack
```

Después de instalar el paquete npm, el módulo se puede utilizar directamente a través de un cargador de módulos JavaScript o importarse en el navegador haciendo referencia al siguiente archivo:

*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js* 

En un explorador, también se debe hacer referencia a la `msgpack5` biblioteca. Utilice `<script>` una etiqueta para crear una referencia. La biblioteca se puede encontrar en *node_modules.msgpack5-dist-msgpack5.js*.

> [!NOTE]
> Cuando se `<script>` utiliza el elemento, el orden es importante. Si se hace referencia a *signalr-protocol-msgpack.js* antes de *msgpack5.js*, se produce un error al intentar conectarse con MessagePack. *signalr.js* también es necesario antes *de signalr-protocol-msgpack.js*.

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

Agregar `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` a `HubConnectionBuilder` la configurará el cliente para usar el protocolo MessagePack al conectarse a un servidor.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> En este momento, no hay opciones de configuración para el protocolo MessagePack en el cliente JavaScript.

## <a name="messagepack-quirks"></a>Peculiaridades MessagePack

Hay algunos problemas que debe tener en cuenta al usar el protocolo de concentrador de MessagePack.

### <a name="messagepack-is-case-sensitive"></a>MessagePack distingue mayúsculas de minúsculas

El protocolo MessagePack distingue mayúsculas de minúsculas. Por ejemplo, considere la siguiente clase de C-:

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

Al enviar desde el cliente de `PascalCased` JavaScript, debe usar nombres de propiedad, ya que las mayúsculas y minúsculas deben coincidir exactamente con la clase de C. Por ejemplo:

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

El `camelCased` uso de nombres no se enlazará correctamente a la clase de C. Puede evitar esto mediante `Key` el uso del atributo para especificar un nombre diferente para el MessagePack propiedad. Para obtener más información, consulte la documentación de [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a>DateTime.Kind no se conserva al serializar/deserializar

El protocolo MessagePack no proporciona una manera `Kind` de `DateTime`codificar el valor de un archivo . Como resultado, al deserializar una fecha, el protocolo de concentrador `DateTime.Kind` de `DateTimeKind.Local` MessagePack se convertirá al formato UTC si lo que es, de lo contrario, no tocará la hora y la pasará tal cual. Si está trabajando `DateTime` con valores, le recomendamos que se convierta a UTC antes de enviarlos. Conviértalos de UTC a la hora local cuando los reciba.

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a>DateTime.MinValue no es compatible con MessagePack en JavaScript

La biblioteca [msgpack5](https://github.com/mcollina/msgpack5) SignalR utilizada por el cliente `timestamp96` JavaScript no admite el tipo en MessagePack. Este tipo se utiliza para codificar valores de fecha muy grandes (muy temprano en el pasado o muy lejos en el futuro). El valor `DateTime.MinValue` `January 1, 0001`de es , que `timestamp96` debe codificarse en un valor. Debido a esto, no se admite el envío `DateTime.MinValue` a un cliente JavaScript. Cuando `DateTime.MinValue` el cliente JavaScript lo recibe, se produce el siguiente error:

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

Por `DateTime.MinValue` lo general, se utiliza para `null` codificar un valor o "falta". Si necesita codificar ese valor en MessagePack, `DateTime` utilice`DateTime?`un valor que `bool` acepta valores NULL ( ) o codifique un valor independiente que indique si la fecha está presente.

Para obtener más información sobre esta limitación, consulte problema de GitHub [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a>Compatibilidad con MessagePack en un entorno de compilación "de avanzada"

La biblioteca [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) utilizada por el cliente y el servidor de .NET usa la generación de código para optimizar la serialización. Como resultado, no se admite de forma predeterminada en entornos que usan la compilación "anticipada de tiempo" (como Xamarin iOS o Unity). Es posible utilizar MessagePack en estos entornos mediante la "pregeneración" del código serializador/deserializador. Para obtener más información, consulte la documentación de [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin). Una vez que haya generado previamente los serializadores, puede `AddMessagePackProtocol`registrarlos utilizando el delegado de configuración pasado a:

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

### <a name="type-checks-are-more-strict-in-messagepack"></a>Las comprobaciones de tipo son más estrictas en MessagePack

El protocolo de concentrador JSON realizará conversiones de tipos durante la deserialización. Por ejemplo, si el objeto entrante tiene un`{ foo: 42 }`valor de propiedad que es un `string`número ( ) pero la propiedad de la clase .NET es de tipo , el valor se convertirá. Sin embargo, MessagePack no realiza esta conversión y producirá una excepción que se puede ver en los registros del lado del servidor (y en la consola):

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

Para obtener más información sobre esta limitación, consulte problema de GitHub [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).

## <a name="related-resources"></a>Recursos relacionados

* [Primeros pasos](xref:tutorials/signalr)
* [Cliente .NET](xref:signalr/dotnet-client)
* [Cliente de JavaScript](xref:signalr/javascript-client)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

En este artículo se supone que el lector está familiarizado con los temas tratados en [Introducción](xref:tutorials/signalr).

## <a name="what-is-messagepack"></a>¿Qué es MessagePack?

[MessagePack](https://msgpack.org/index.html) es un formato de serialización binaria rápido y compacto. Es útil cuando el rendimiento y el ancho de banda son una preocupación porque crea mensajes más pequeños en comparación con [JSON.](https://www.json.org/) Los mensajes binarios son ilegibles cuando se examinan los seguimientos de red y los registros a menos que los bytes se pasen a través de un analizador MessagePack. SignalRtiene compatibilidad integrada con el formato MessagePack y proporciona API para que el cliente y el servidor las usen.

## <a name="configure-messagepack-on-the-server"></a>Configurar MessagePack en el servidor

Para habilitar el protocolo de concentrador de `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` MessagePack en el servidor, instale el paquete en la aplicación. En `Startup.ConfigureServices` el método, `AddMessagePackProtocol` `AddSignalR` agregue a la llamada para habilitar la compatibilidad con MessagePack en el servidor.

> [!NOTE]
> JSON está habilitado de forma predeterminada. Agregar MessagePack permite la compatibilidad con clientes JSON y MessagePack.

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

Para personalizar cómo MessagePack dará `AddMessagePackProtocol` formato a los datos, toma un delegado para configurar opciones. En ese delegado, la `FormatterResolvers` propiedad se puede usar para configurar las opciones de serialización de MessagePack. Para obtener más información sobre cómo funcionan los solucionadores, visite la biblioteca MessagePack en [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp). Los atributos se pueden utilizar en los objetos que desea serializar para definir cómo se deben controlar.

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
> Recomendamos encarecidamente revisar [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) y aplicar los parches recomendados. Por ejemplo, `MessagePackSecurity.Active` establecer la `MessagePackSecurity.UntrustedData`propiedad estática en . Establecer `MessagePackSecurity.Active` la instalación manual de una [versión 1.9.x de MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3). La `MessagePack` instalación de 1.9.x actualiza el uso de la versión. SignalR Cuando `MessagePackSecurity.Active` no se `MessagePackSecurity.UntrustedData`establece en , un cliente malintencionado podría provocar una denegación de servicio. Establecer `MessagePackSecurity.Active` `Program.Main`en , como se muestra en el código siguiente:

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a>Configurar MessagePack en el cliente

> [!NOTE]
> JSON está habilitado de forma predeterminada para los clientes admitidos. Los clientes solo pueden admitir un único protocolo. Agregar compatibilidad con MessagePack reemplazará los protocolos configurados anteriormente.

### <a name="net-client"></a>Cliente .NET

Para habilitar MessagePack en el cliente `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` .NET, instale el paquete y llame a `AddMessagePackProtocol` `HubConnectionBuilder`.

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> Esta `AddMessagePackProtocol` llamada toma un delegado para configurar opciones al igual que el servidor.

### <a name="javascript-client"></a>Cliente de JavaScript

El paquete [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm proporciona compatibilidad con MessagePack para el cliente JavaScript. Instale el paquete ejecutando el siguiente comando en un shell de comandos:

```bash
npm install @microsoft/signalr-protocol-msgpack
```

Después de instalar el paquete npm, el módulo se puede utilizar directamente a través de un cargador de módulos JavaScript o importarse en el navegador haciendo referencia al siguiente archivo:

*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js* 

En un explorador, también se debe hacer referencia a la `msgpack5` biblioteca. Utilice `<script>` una etiqueta para crear una referencia. La biblioteca se puede encontrar en *node_modules.msgpack5-dist-msgpack5.js*.

> [!NOTE]
> Cuando se `<script>` utiliza el elemento, el orden es importante. Si se hace referencia a *signalr-protocol-msgpack.js* antes de *msgpack5.js*, se produce un error al intentar conectarse con MessagePack. *signalr.js* también es necesario antes *de signalr-protocol-msgpack.js*.

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

Agregar `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` a `HubConnectionBuilder` la configurará el cliente para usar el protocolo MessagePack al conectarse a un servidor.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> En este momento, no hay opciones de configuración para el protocolo MessagePack en el cliente JavaScript.

## <a name="messagepack-quirks"></a>Peculiaridades MessagePack

Hay algunos problemas que debe tener en cuenta al usar el protocolo de concentrador de MessagePack.

### <a name="messagepack-is-case-sensitive"></a>MessagePack distingue mayúsculas de minúsculas

El protocolo MessagePack distingue mayúsculas de minúsculas. Por ejemplo, considere la siguiente clase de C-:

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

Al enviar desde el cliente de `PascalCased` JavaScript, debe usar nombres de propiedad, ya que las mayúsculas y minúsculas deben coincidir exactamente con la clase de C. Por ejemplo:

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

El `camelCased` uso de nombres no se enlazará correctamente a la clase de C. Puede evitar esto mediante `Key` el uso del atributo para especificar un nombre diferente para el MessagePack propiedad. Para obtener más información, consulte la documentación de [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a>DateTime.Kind no se conserva al serializar/deserializar

El protocolo MessagePack no proporciona una manera `Kind` de `DateTime`codificar el valor de un archivo . Como resultado, al deserializar una fecha, el protocolo de concentrador de MessagePack supone que la fecha entrante está en formato UTC. Si está trabajando `DateTime` con valores en la hora local, se recomienda convertir a UTC antes de enviarlos. Conviértalos de UTC a la hora local cuando los reciba.

Para obtener más información sobre esta limitación, consulte problema de GitHub [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a>DateTime.MinValue no es compatible con MessagePack en JavaScript

La biblioteca [msgpack5](https://github.com/mcollina/msgpack5) SignalR utilizada por el cliente `timestamp96` JavaScript no admite el tipo en MessagePack. Este tipo se utiliza para codificar valores de fecha muy grandes (muy temprano en el pasado o muy lejos en el futuro). El valor `DateTime.MinValue` `January 1, 0001`de es , que `timestamp96` debe codificarse en un valor. Debido a esto, no se admite el envío `DateTime.MinValue` a un cliente JavaScript. Cuando `DateTime.MinValue` el cliente JavaScript lo recibe, se produce el siguiente error:

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

Por `DateTime.MinValue` lo general, se utiliza para `null` codificar un valor o "falta". Si necesita codificar ese valor en MessagePack, `DateTime` utilice`DateTime?`un valor que `bool` acepta valores NULL ( ) o codifique un valor independiente que indique si la fecha está presente.

Para obtener más información sobre esta limitación, consulte problema de GitHub [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a>Compatibilidad con MessagePack en un entorno de compilación "de avanzada"

La biblioteca [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) utilizada por el cliente y el servidor de .NET usa la generación de código para optimizar la serialización. Como resultado, no se admite de forma predeterminada en entornos que usan la compilación "anticipada de tiempo" (como Xamarin iOS o Unity). Es posible utilizar MessagePack en estos entornos mediante la "pregeneración" del código serializador/deserializador. Para obtener más información, consulte la documentación de [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports). Una vez que haya generado previamente los serializadores, puede `AddMessagePackProtocol`registrarlos utilizando el delegado de configuración pasado a:

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

### <a name="type-checks-are-more-strict-in-messagepack"></a>Las comprobaciones de tipo son más estrictas en MessagePack

El protocolo de concentrador JSON realizará conversiones de tipos durante la deserialización. Por ejemplo, si el objeto entrante tiene un`{ foo: 42 }`valor de propiedad que es un `string`número ( ) pero la propiedad de la clase .NET es de tipo , el valor se convertirá. Sin embargo, MessagePack no realiza esta conversión y producirá una excepción que se puede ver en los registros del lado del servidor (y en la consola):

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

Para obtener más información sobre esta limitación, consulte problema de GitHub [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).

## <a name="related-resources"></a>Recursos relacionados

* [Primeros pasos](xref:tutorials/signalr)
* [Cliente .NET](xref:signalr/dotnet-client)
* [Cliente de JavaScript](xref:signalr/javascript-client)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

En este artículo se supone que el lector está familiarizado con los temas tratados en [Introducción](xref:tutorials/signalr).

## <a name="what-is-messagepack"></a>¿Qué es MessagePack?

[MessagePack](https://msgpack.org/index.html) es un formato de serialización binaria rápido y compacto. Es útil cuando el rendimiento y el ancho de banda son una preocupación porque crea mensajes más pequeños en comparación con [JSON.](https://www.json.org/) Los mensajes binarios son ilegibles cuando se examinan los seguimientos de red y los registros a menos que los bytes se pasen a través de un analizador MessagePack. SignalRtiene compatibilidad integrada con el formato MessagePack y proporciona API para que el cliente y el servidor las usen.

## <a name="configure-messagepack-on-the-server"></a>Configurar MessagePack en el servidor

Para habilitar el protocolo de concentrador de `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` MessagePack en el servidor, instale el paquete en la aplicación. En `Startup.ConfigureServices` el método, `AddMessagePackProtocol` `AddSignalR` agregue a la llamada para habilitar la compatibilidad con MessagePack en el servidor.

> [!NOTE]
> JSON está habilitado de forma predeterminada. Agregar MessagePack permite la compatibilidad con clientes JSON y MessagePack.

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

Para personalizar cómo MessagePack dará `AddMessagePackProtocol` formato a los datos, toma un delegado para configurar opciones. En ese delegado, la `FormatterResolvers` propiedad se puede usar para configurar las opciones de serialización de MessagePack. Para obtener más información sobre cómo funcionan los solucionadores, visite la biblioteca MessagePack en [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp). Los atributos se pueden utilizar en los objetos que desea serializar para definir cómo se deben controlar.

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
> Recomendamos encarecidamente revisar [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) y aplicar los parches recomendados. Por ejemplo, `MessagePackSecurity.Active` establecer la `MessagePackSecurity.UntrustedData`propiedad estática en . Establecer `MessagePackSecurity.Active` la instalación manual de una [versión 1.9.x de MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3). La `MessagePack` instalación de 1.9.x actualiza el uso de la versión. SignalR Cuando `MessagePackSecurity.Active` no se `MessagePackSecurity.UntrustedData`establece en , un cliente malintencionado podría provocar una denegación de servicio. Establecer `MessagePackSecurity.Active` `Program.Main`en , como se muestra en el código siguiente:

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a>Configurar MessagePack en el cliente

> [!NOTE]
> JSON está habilitado de forma predeterminada para los clientes admitidos. Los clientes solo pueden admitir un único protocolo. Agregar compatibilidad con MessagePack reemplazará los protocolos configurados anteriormente.

### <a name="net-client"></a>Cliente .NET

Para habilitar MessagePack en el cliente `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` .NET, instale el paquete y llame a `AddMessagePackProtocol` `HubConnectionBuilder`.

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> Esta `AddMessagePackProtocol` llamada toma un delegado para configurar opciones al igual que el servidor.

### <a name="javascript-client"></a>Cliente de JavaScript

El paquete [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) npm proporciona compatibilidad con MessagePack para el cliente JavaScript. Instale el paquete ejecutando el siguiente comando en un shell de comandos:

```bash
npm install @aspnet/signalr-protocol-msgpack
```

Después de instalar el paquete npm, el módulo se puede utilizar directamente a través de un cargador de módulos JavaScript o importarse en el navegador haciendo referencia al siguiente archivo:

*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*

En un explorador, también se debe hacer referencia a la `msgpack5` biblioteca. Utilice `<script>` una etiqueta para crear una referencia. La biblioteca se puede encontrar en *node_modules.msgpack5-dist-msgpack5.js*.

> [!NOTE]
> Cuando se `<script>` utiliza el elemento, el orden es importante. Si se hace referencia a *signalr-protocol-msgpack.js* antes de *msgpack5.js*, se produce un error al intentar conectarse con MessagePack. *signalr.js* también es necesario antes *de signalr-protocol-msgpack.js*.

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

Agregar `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` a `HubConnectionBuilder` la configurará el cliente para usar el protocolo MessagePack al conectarse a un servidor.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> En este momento, no hay opciones de configuración para el protocolo MessagePack en el cliente JavaScript.

## <a name="messagepack-quirks"></a>Peculiaridades MessagePack

Hay algunos problemas que debe tener en cuenta al usar el protocolo de concentrador de MessagePack.

### <a name="messagepack-is-case-sensitive"></a>MessagePack distingue mayúsculas de minúsculas

El protocolo MessagePack distingue mayúsculas de minúsculas. Por ejemplo, considere la siguiente clase de C-:

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

Al enviar desde el cliente de `PascalCased` JavaScript, debe usar nombres de propiedad, ya que las mayúsculas y minúsculas deben coincidir exactamente con la clase de C. Por ejemplo:

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

El `camelCased` uso de nombres no se enlazará correctamente a la clase de C. Puede evitar esto mediante `Key` el uso del atributo para especificar un nombre diferente para el MessagePack propiedad. Para obtener más información, consulte la documentación de [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a>DateTime.Kind no se conserva al serializar/deserializar

El protocolo MessagePack no proporciona una manera `Kind` de `DateTime`codificar el valor de un archivo . Como resultado, al deserializar una fecha, el protocolo de concentrador de MessagePack supone que la fecha entrante está en formato UTC. Si está trabajando `DateTime` con valores en la hora local, se recomienda convertir a UTC antes de enviarlos. Conviértalos de UTC a la hora local cuando los reciba.

Para obtener más información sobre esta limitación, consulte problema de GitHub [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a>DateTime.MinValue no es compatible con MessagePack en JavaScript

La biblioteca [msgpack5](https://github.com/mcollina/msgpack5) SignalR utilizada por el cliente `timestamp96` JavaScript no admite el tipo en MessagePack. Este tipo se utiliza para codificar valores de fecha muy grandes (muy temprano en el pasado o muy lejos en el futuro). El valor `DateTime.MinValue` `January 1, 0001` de es que `timestamp96` se debe codificar en un valor. Debido a esto, no se admite el envío `DateTime.MinValue` a un cliente JavaScript. Cuando `DateTime.MinValue` el cliente JavaScript lo recibe, se produce el siguiente error:

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

Por `DateTime.MinValue` lo general, se utiliza para `null` codificar un valor o "falta". Si necesita codificar ese valor en MessagePack, `DateTime` utilice`DateTime?`un valor que `bool` acepta valores NULL ( ) o codifique un valor independiente que indique si la fecha está presente.

Para obtener más información sobre esta limitación, consulte problema de GitHub [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a>Compatibilidad con MessagePack en un entorno de compilación "de avanzada"

La biblioteca [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) utilizada por el cliente y el servidor de .NET usa la generación de código para optimizar la serialización. Como resultado, no se admite de forma predeterminada en entornos que usan la compilación "anticipada de tiempo" (como Xamarin iOS o Unity). Es posible utilizar MessagePack en estos entornos mediante la "pregeneración" del código serializador/deserializador. Para obtener más información, consulte la documentación de [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports). Una vez que haya generado previamente los serializadores, puede `AddMessagePackProtocol`registrarlos utilizando el delegado de configuración pasado a:

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

### <a name="type-checks-are-more-strict-in-messagepack"></a>Las comprobaciones de tipo son más estrictas en MessagePack

El protocolo de concentrador JSON realizará conversiones de tipos durante la deserialización. Por ejemplo, si el objeto entrante tiene un`{ foo: 42 }`valor de propiedad que es un `string`número ( ) pero la propiedad de la clase .NET es de tipo , el valor se convertirá. Sin embargo, MessagePack no realiza esta conversión y producirá una excepción que se puede ver en los registros del lado del servidor (y en la consola):

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

Para obtener más información sobre esta limitación, consulte problema de GitHub [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).

## <a name="related-resources"></a>Recursos relacionados

* [Primeros pasos](xref:tutorials/signalr)
* [Cliente .NET](xref:signalr/dotnet-client)
* [Cliente de JavaScript](xref:signalr/javascript-client)

::: moniker-end
