---
title: Información general sobre las API de consumidor para ASP.NET Core
author: rick-anderson
description: Reciba una breve descripción de las distintas API de consumidor disponibles en la ASP.NET Core biblioteca de protección de datos.
ms.author: riande
ms.date: 06/11/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/consumer-apis/overview
ms.openlocfilehash: e840111d702882a45e59cf89d679b87fa537d833
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767862"
---
# <a name="consumer-apis-overview-for-aspnet-core"></a>Información general sobre las API de consumidor para ASP.NET Core

Las `IDataProtectionProvider` interfaces `IDataProtector` y son las interfaces básicas a través de las cuales los consumidores usan el sistema de protección de datos. Se encuentran en el paquete [Microsoft. AspNetCore. Desproteccion. Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Abstractions/) .

## <a name="idataprotectionprovider"></a>IDataProtectionProvider

La interfaz del proveedor representa la raíz del sistema de protección de datos. No se puede usar directamente para proteger o desproteger los datos. En su lugar, el consumidor debe obtener una referencia a `IDataProtector` un llamando `IDataProtectionProvider.CreateProtector(purpose)`a, donde propósito es una cadena que describe el caso de uso previsto del consumidor. Vea [cadenas de propósito](xref:security/data-protection/consumer-apis/purpose-strings) para obtener mucha más información sobre la intención de este parámetro y cómo elegir un valor adecuado.

## <a name="idataprotector"></a>IDataProtector

La interfaz del protector se devuelve mediante una llamada `CreateProtector`a, y es esta interfaz la que los consumidores pueden usar para realizar las operaciones de protección y desprotección.

Para proteger un fragmento de datos, pase los datos al `Protect` método. La interfaz básica define un método que convierte Byte []-> Byte [], pero también hay una sobrecarga (proporcionada como método de extensión) que convierte la cadena > cadena. La seguridad que ofrecen los dos métodos es idéntica; el desarrollador debe elegir cualquier sobrecarga que sea más conveniente para su caso de uso. Con independencia de la sobrecarga elegida, el valor devuelto por el método Protect ahora está protegido (cifrado y con corrección de manipulaciones) y la aplicación puede enviarlo a un cliente que no es de confianza.

Para desproteger un fragmento de datos previamente protegido, pase los datos protegidos al `Unprotect` método. (Hay sobrecargas basadas en bytes [] y basadas en cadenas para facilitar el desarrollo). Si la carga protegida se generó mediante una llamada anterior a `Protect` en este mismo `IDataProtector`, el `Unprotect` método devolverá la carga original no protegida. Si la carga protegida se ha manipulado o se ha producido en otro `IDataProtector`, el `Unprotect` método producirá CryptographicException.

El concepto de la misma comparación con `IDataProtector` el de la finalidad es diferente. Si dos `IDataProtector` instancias se generaron a partir de `IDataProtectionProvider` la misma raíz pero a través de cadenas de `IDataProtectionProvider.CreateProtector`propósito diferentes en la llamada a, se considerarán [protectores diferentes](xref:security/data-protection/consumer-apis/purpose-strings)y una no podrá desproteger las cargas generadas por el otro.

## <a name="consuming-these-interfaces"></a>Utilizar estas interfaces

En el caso de un componente que tiene en cuenta la inserción, el uso previsto `IDataProtectionProvider` es que el componente toma un parámetro en su constructor y que el sistema di proporciona automáticamente este servicio cuando se crea una instancia del componente.

> [!NOTE]
> Algunas aplicaciones (como las aplicaciones de consola o las aplicaciones ASP.NET 4. x) podrían no ser compatibles con DI, por lo que no puede usar el mecanismo que se describe aquí. En estos escenarios, consulte el documento [escenarios no compatibles con di](xref:security/data-protection/configuration/non-di-scenarios) para obtener más información sobre cómo obtener `IDataProtection` una instancia de un proveedor sin pasar por la inserción.

En el ejemplo siguiente se muestran tres conceptos:

1. [Agregue el sistema de protección de datos](xref:security/data-protection/configuration/overview) al contenedor de servicios.

2. Usar DI para recibir una instancia de `IDataProtectionProvider`, y

3. Crear un `IDataProtector` a partir `IDataProtectionProvider` de un y usarlo para proteger y desproteger los datos.

[!code-csharp[](../using-data-protection/samples/protectunprotect.cs?highlight=26,34,35,36,37,38,39,40)]

El paquete Microsoft. AspNetCore. desproteccion. Abstractions contiene un método `IServiceProvider.GetDataProtector` de extensión como comodidad para los desarrolladores. Encapsula como una operación única y recupera un `IDataProtectionProvider` del proveedor de servicios y llama a. `IDataProtectionProvider.CreateProtector` En el ejemplo siguiente se muestra su uso.

[!code-csharp[](./overview/samples/getdataprotector.cs?highlight=15)]

>[!TIP]
> Las instancias `IDataProtectionProvider` de `IDataProtector` y son seguras para subprocesos para varios llamadores. Está previsto que, una vez que un componente obtiene una referencia `IDataProtector` a a través de `CreateProtector`una llamada a, usará esa referencia para varias llamadas `Protect` a `Unprotect`y. Una llamada a `Unprotect` producirá CryptographicException si la carga protegida no se puede comprobar o descifrar. Algunos componentes pueden querer omitir los errores durante las operaciones de desprotección; un componente que lee las cookies de autenticación podría controlar este error y tratar la solicitud como si no tuviera cookies en absoluto, en lugar de que se produzca un error en la solicitud. Los componentes que quieren este comportamiento deben detectar específicamente CryptographicException en lugar de admitir todas las excepciones.
