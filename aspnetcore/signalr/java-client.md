---
title: Cliente de ASP.NET Core SignalR Java
author: mikaelm12
description: Aprenda a usar el cliente de SignalR Java de ASP.net Core.
monikerRange: '>= aspnetcore-2.2'
ms.author: mimengis
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/java-client
ms.openlocfilehash: 27ab8cc1b6e419b59aadb97a8a1fbdddc3579276
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408804"
---
# <a name="aspnet-core-signalr-java-client"></a>Cliente de ASP.NET Core SignalR Java

Por [Mikael Mengistu](https://twitter.com/MikaelM_12)

El cliente de Java permite la conexión a un SignalR servidor de ASP.net Core desde código Java, incluidas las aplicaciones Android. Al igual que el [cliente JavaScript](xref:signalr/javascript-client) y el [cliente .net](xref:signalr/dotnet-client), el cliente Java permite recibir y enviar mensajes a un centro en tiempo real. El cliente de Java está disponible en ASP.NET Core 2,2 y versiones posteriores.

La aplicación de consola de Java de ejemplo a la que se hace referencia en este artículo usa el SignalR cliente de Java.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/java-client/sample) ([cómo descargarlo](xref:index#how-to-download-a-sample))

## <a name="install-the-signalr-java-client-package"></a>Instalar el SignalR paquete de cliente de Java

El archivo jar *signalr-1.0.0* permite que los clientes se conecten a los SignalR concentradores. Para buscar el número de versión del archivo JAR más reciente, consulte los resultados de la [búsqueda de Maven](https://search.maven.org/search?q=g:com.microsoft.signalr%20AND%20a:signalr).

Si usa Gradle, agregue la siguiente línea a la `dependencies` sección del archivo *Build. Gradle* :

```gradle
implementation 'com.microsoft.signalr:signalr:1.0.0'
```

Si usa Maven, agregue las líneas siguientes dentro del `<dependencies>` elemento de su *pom.xml* archivo:

[!code-xml[pom.xml dependency element](java-client/sample/pom.xml?name=snippet_dependencyElement)]

## <a name="connect-to-a-hub"></a>Conexión a un concentrador

Para establecer un `HubConnection` , `HubConnectionBuilder` se debe usar. La dirección URL del concentrador y el nivel de registro se pueden configurar al compilar una conexión. Configure las opciones necesarias llamando a cualquiera de los `HubConnectionBuilder` métodos antes de `build` . Inicie la conexión con `start` .

[!code-java[Build hub connection](java-client/sample/src/main/java/Chat.java?range=16-17)]

## <a name="call-hub-methods-from-client"></a>Llamar a métodos de Hub desde el cliente

Una llamada a `send` invoca un método de concentrador. Pase el nombre del método de concentrador y los argumentos definidos en el método de concentrador a `send` .

[!code-java[send method](java-client/sample/src/main/java/Chat.java?range=28)]

> [!NOTE]
> Si utiliza SignalR el servicio de Azure en *modo sin servidor*, no puede llamar a métodos de concentrador desde un cliente. Para obtener más información, consulte la [ SignalR documentación del servicio](/azure/azure-signalr/signalr-concept-serverless-development-config).

## <a name="call-client-methods-from-hub"></a>Llamar a métodos de cliente desde el concentrador

`hubConnection.on`Se usa para definir métodos en el cliente a los que puede llamar el concentrador. Defina los métodos después de la compilación, pero antes de iniciar la conexión.

[!code-java[Define client methods](java-client/sample/src/main/java/Chat.java?range=19-21)]

## <a name="add-logging"></a>Adición de registro

El SignalR cliente de Java utiliza la biblioteca [SLF4J](https://www.slf4j.org/) para el registro. Se trata de una API de registro de alto nivel que permite a los usuarios de la biblioteca elegir su propia implementación de registro específica mediante la incorporación de una dependencia de registro específica. En el fragmento de código siguiente se muestra cómo utilizar `java.util.logging` con el SignalR cliente de Java.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Si no configura el registro en las dependencias, SLF4J carga un registrador de no operaciones predeterminado con el siguiente mensaje de ADVERTENCIA:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Esto se puede omitir sin ningún riesgo.

## <a name="android-development-notes"></a>Notas de desarrollo de Android

En lo que respecta a Android SDK la compatibilidad de las SignalR características de cliente, tenga en cuenta los siguientes elementos al especificar la versión de Android SDK de destino:

* El SignalR cliente de Java se ejecutará en el nivel de API de Android 16 y versiones posteriores.
* La conexión a través del servicio de Azure SignalR requerirá el nivel de API de Android 20 y versiones posteriores, ya que el [ SignalR servicio de azure](/azure/azure-signalr/signalr-overview) requiere TLS 1,2 y no es compatible con conjuntos de cifrado basados en SHA-1. Android [ha agregado compatibilidad con los conjuntos de cifrado SHA-256 (y versiones posteriores)](https://developer.android.com/reference/javax/net/ssl/SSLSocket) en el nivel de API 20.

## <a name="configure-bearer-token-authentication"></a>Configurar la autenticación de token de portador

En el SignalR cliente de Java, puede configurar un token de portador que se usará para la autenticación proporcionando un "generador de tokens de acceso" a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java). Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para proporcionar una [RxJava](https://github.com/ReactiveX/RxJava) [única \<String> ](https://reactivex.io/documentation/single.html)RxJava. Con una llamada a [Single. defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), puede escribir lógica para generar tokens de acceso para el cliente.

```java
HubConnection hubConnection = HubConnectionBuilder.create("YOUR HUB URL HERE")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

## <a name="known-limitations"></a>Restricciones conocidas

::: moniker range=">= aspnetcore-3.0"

* Solo se admite el protocolo JSON.
* No se admiten la reserva de transporte ni el transporte de eventos enviados del servidor.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* Solo se admite el protocolo JSON.
* Solo se admite el transporte de WebSockets.
* Todavía no se admite la transmisión por secuencias.

::: moniker-end

## <a name="additional-resources"></a>Recursos adicionales

* [Referencia de API de Java](/java/api/com.microsoft.signalr?view=aspnet-signalr-java)
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/publish-to-azure-web-app>
* [Documentación sin servidor del servicio de Azure SignalR](/azure/azure-signalr/signalr-concept-serverless-development-config)
