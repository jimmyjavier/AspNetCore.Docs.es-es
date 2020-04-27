---
title: Configuración gRPC para .NET
author: jamesnk
description: Obtenga información sobre cómo configurar gRPC para aplicaciones .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 02/26/2020
uid: grpc/configuration
ms.openlocfilehash: 4c13c45ce745643c3cb089a1c984d2ef599db48b
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440836"
---
# <a name="grpc-for-net-configuration"></a>Configuración gRPC para .NET

## <a name="configure-services-options"></a>Configuración de opciones de servicios

Los servicios gRPC se configuran con `AddGrpc` en *Startup.cs*. La tabla siguiente describe las opciones disponibles para configurar los servicios gRPC:

| Opción | Valor predeterminado | Descripción |
| ------ | ------------- | ----------- |
| MaxSendMessageSize | `null` | Tamaño máximo de mensaje en bytes que se puede enviar desde el servidor. Al intentar enviar un mensaje que supere el tamaño máximo configurado del mensaje, se produce una excepción. Cuando se establece en `null`, el tamaño del mensaje es ilimitado. |
| MaxReceiveMessageSize | 4 MB | Tamaño máximo de mensaje en bytes que puede recibir el servidor. Si el servidor recibe un mensaje que supere este límite, se produce una excepción. Aumentar este valor permite que el servidor reciba mensajes de mayor tamaño, pero puede afectar negativamente al consumo de memoria. Cuando se establece en `null`, el tamaño del mensaje es ilimitado. |
| EnableDetailedErrors | `false` | Si es `true`, los mensajes de excepción detallados se devuelven a los clientes cuando se produzca una excepción en un método de servicio. De manera predeterminada, es `false`. Si se establece `EnableDetailedErrors` en `true`, se puede perder información confidencial. |
| CompressionProviders | gzip | Colección de proveedores de compresión usados para comprimir y descomprimir mensajes. Los proveedores personalizados de compresión se pueden crear y agregar a la colección. Los proveedores configurados de forma predeterminada admiten la compresión **gzip**. |
| <span style="word-break:normal;word-wrap:normal">ResponseCompressionAlgorithm</span> | `null` | Algoritmo de compresión que se usa para comprimir los mensajes enviados desde el servidor. El algoritmo debe coincidir con un proveedor de compresión en `CompressionProviders`. Para que el algoritmo pueda comprimir una respuesta, el cliente debe indicar que es compatible con el algoritmo enviándola en el encabezado **grpc-accept-encoding**. |
| ResponseCompressionLevel | `null` | Nivel de compresión utilizado para comprimir los mensajes enviados desde el servidor. |
| Interceptores | None | Colección de interceptores que se ejecutan con cada llamada a gRPC. Los interceptores se ejecutan en el orden en que se registran. Los interceptores configurados globalmente se ejecutan antes que los interceptores configurados para un servicio único. Para obtener más información sobre los interceptores de gRPC, vea [Interceptores de gRPC frente a middleware](xref:grpc/migration#grpc-interceptors-vs-middleware). |

Las opciones se pueden configurar para todos los servicios proporcionando un delegado de opciones a la llamada `AddGrpc` en `Startup.ConfigureServices`:

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup.cs?name=snippet)]

Las opciones de un servicio único invalidan las opciones globales proporcionadas en `AddGrpc` y se pueden configurar mediante `AddServiceOptions<TService>`:

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup2.cs?name=snippet)]

## <a name="configure-client-options"></a>Configuración de opciones de cliente

La configuración de cliente gRPC se establece en `GrpcChannelOptions`. En la tabla siguiente se describen las opciones disponibles para configurar los canales gRPC:

| Opción | Valor predeterminado | Descripción |
| ------ | ------------- | ----------- |
| HttpClient | Nueva instancia | El elemento `HttpClient` que se usa para realizar llamadas gRPC. Se puede establecer un cliente para configurar un elemento `HttpClientHandler` o agregar controladores adicionales a la canalización HTTP para llamadas gRPC. Si no se especifica ningún elemento `HttpClient`, se creará una nueva instancia de `HttpClient` para el canal. Esta se eliminará automáticamente. |
| DisposeHttpClient | `false` | Si es `true` y se especifica un elemento `HttpClient`, se eliminará la instancia de `HttpClient` cuando se elimine `GrpcChannel`. |
| LoggerFactory | `null` | El elemento `LoggerFactory` que usa el cliente para registrar información acerca de las llamadas gRPC. Una instancia de `LoggerFactory` se puede resolver a partir de la inserción de dependencia o crearse mediante `LoggerFactory.Create`. Para obtener ejemplos de cómo configurar el registro, vea <xref:grpc/diagnostics#grpc-client-logging>. |
| MaxSendMessageSize | `null` | Tamaño máximo de mensaje en bytes que se puede enviar desde el cliente. Al intentar enviar un mensaje que supere el tamaño máximo configurado del mensaje, se produce una excepción. Cuando se establece en `null`, el tamaño del mensaje es ilimitado. |
| <span style="word-break:normal;word-wrap:normal">MaxReceiveMessageSize</span> | 4 MB | Tamaño máximo de mensaje en bytes que puede recibir el cliente. Si el cliente recibe un mensaje que supere este límite, se produce una excepción. Aumentar este valor permite que el cliente reciba mensajes de mayor tamaño, pero puede afectar negativamente al consumo de memoria. Cuando se establece en `null`, el tamaño del mensaje es ilimitado. |
| Credenciales | `null` | Instancia de `ChannelCredentials`. Las credenciales se usan para agregar metadatos de autenticación a llamadas gRPC. |
| CompressionProviders | gzip | Colección de proveedores de compresión usados para comprimir y descomprimir mensajes. Los proveedores personalizados de compresión se pueden crear y agregar a la colección. Los proveedores configurados de forma predeterminada admiten la compresión **gzip**. |

El código siguiente:

* Establece el tamaño máximo de mensaje de envío y recepción en el canal.
* Crea un cliente.

[!code-csharp[](~/grpc/configuration/sample/Program.cs?name=snippet&highlight=3-8)]

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a>Recursos adicionales

* <xref:grpc/aspnetcore>
* <xref:grpc/client>
* <xref:grpc/diagnostics>
* <xref:tutorials/grpc/grpc-start>
