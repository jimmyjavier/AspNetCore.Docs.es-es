---
title: Varias API de protección de datos ASP.NET Core
author: rick-anderson
description: Obtenga información sobre la ASP.NET Core interfaz de ISecret de protección de datos.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/extensibility/misc-apis
ms.openlocfilehash: e9de92233468e9e07791df608b1c37ffb3b29949
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408505"
---
# <a name="miscellaneous-aspnet-core-data-protection-apis"></a>Varias API de protección de datos ASP.NET Core

<a name="data-protection-extensibility-mics-apis"></a>

>[!WARNING]
> Los tipos que implementan cualquiera de las interfaces siguientes deben ser seguros para subprocesos para varios llamadores.

## <a name="isecret"></a>ISecret

La `ISecret` interfaz representa un valor secreto, como material de clave criptográfica. Contiene la siguiente superficie de API:

* `Length`: `int`

* `Dispose()`: `void`

* `WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`

El `WriteSecretIntoBuffer` método rellena el búfer proporcionado con el valor de secreto sin formato. La razón por la que esta API toma el búfer como parámetro en lugar de devolver un elemento `byte[]` directamente es que permite al llamador la oportunidad de anclar el objeto de búfer, lo que limita la exposición secreta al recolector de elementos no utilizados administrado.

El `Secret` tipo es una implementación concreta de `ISecret` donde el valor secreto se almacena en memoria en proceso. En las plataformas de Windows, el valor secreto se cifra mediante [CryptProtectMemory](https://msdn.microsoft.com/library/windows/desktop/aa380262(v=vs.85).aspx).
