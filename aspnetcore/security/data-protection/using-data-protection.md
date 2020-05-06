---
title: Introducción a las API de protección de datos en ASP.NET Core
author: rick-anderson
description: Aprenda a usar las API de protección de datos de ASP.NET Core para proteger y desproteger los datos de una aplicación.
ms.author: riande
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/using-data-protection
ms.openlocfilehash: d5e9e61db39a67e8ccb7b345dfa4c97353312857
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774267"
---
# <a name="get-started-with-the-data-protection-apis-in-aspnet-core"></a>Introducción a las API de protección de datos en ASP.NET Core

<a name="security-data-protection-getting-started"></a>

En su forma más sencilla, la protección de datos consta de los siguientes pasos:

1. Cree un protector de datos a partir de un proveedor de protección de datos.

2. Llame al `Protect` método con los datos que desea proteger.

3. Llame al `Unprotect` método con los datos que desea volver a convertir en texto sin formato.

La mayoría de los marcos y modelos de aplicación, como SignalRASP.net Core o, ya configuran el sistema de protección de datos y lo agregan a un contenedor de servicio al que se accede a través de la inserción de dependencias. En el ejemplo siguiente se muestra la configuración de un contenedor de servicios para la inserción de dependencias y el registro de la pila de protección de datos, la recepción del proveedor de protección de datos a través de DI, la creación de un protector y la protección de los datos.

[!code-csharp[](../../security/data-protection/using-data-protection/samples/protectunprotect.cs?highlight=26,34,35,36,37,38,39,40)]

Al crear un protector, debe proporcionar una o más [cadenas de propósito](xref:security/data-protection/consumer-apis/purpose-strings). Una cadena de propósito proporciona aislamiento entre los consumidores. Por ejemplo, un protector creado con una cadena de propósito de "verde" no podría desproteger los datos proporcionados por un protector con un propósito de "púrpura".

>[!TIP]
> Las instancias `IDataProtectionProvider` de `IDataProtector` y son seguras para subprocesos para varios llamadores. Está previsto que, una vez que un componente obtiene una referencia `IDataProtector` a a través de `CreateProtector`una llamada a, usará esa referencia para varias llamadas `Protect` a `Unprotect`y.
>
>Una llamada a `Unprotect` producirá CryptographicException si la carga protegida no se puede comprobar o descifrar. Algunos componentes pueden querer omitir los errores durante las operaciones de desprotección; un componente que lee las cookies de autenticación podría controlar este error y tratar la solicitud como si no tuviera cookies en absoluto, en lugar de que se produzca un error en la solicitud. Los componentes que quieren este comportamiento deben detectar específicamente CryptographicException en lugar de admitir todas las excepciones.
