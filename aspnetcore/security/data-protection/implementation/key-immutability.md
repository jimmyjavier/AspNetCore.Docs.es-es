---
title: Inmutabilidad de claves y configuración de claves en ASP.NET Core
author: rick-anderson
description: Conozca los detalles de implementación de las API de inmutabilidad de la clave de protección de datos de ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/implementation/key-immutability
ms.openlocfilehash: 5bbd1fb28fc0330ccfe4e829ab0b2f86226c7166
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776921"
---
# <a name="key-immutability-and-key-settings-in-aspnet-core"></a>Inmutabilidad de claves y configuración de claves en ASP.NET Core

Una vez que un objeto se conserva en la memoria auxiliar, su representación es fija para siempre. Se pueden agregar nuevos datos a la memoria auxiliar, pero nunca se pueden mutar los datos existentes. El propósito principal de este comportamiento es evitar daños en los datos.

Una consecuencia de este comportamiento es que una vez que una clave se escribe en la memoria auxiliar, es inmutable. Sus fechas de creación, activación y expiración no se pueden cambiar nunca, aunque se pueden revocar `IKeyManager`mediante. Además, la información algorítmica subyacente, el material de creación de claves maestra y el cifrado en las propiedades de REST también son inmutables.

Si el desarrollador cambia cualquier valor de configuración que afecte a la persistencia de claves, esos cambios no entrarán en vigor hasta la próxima vez que se genere una `IKeyManager.CreateNewKey` clave, ya sea a través de una llamada explícita a o a través del comportamiento de la [generación automática de claves](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) del sistema de protección de datos. Los valores que afectan a la persistencia de claves son los siguientes:

* [La duración de la clave predeterminada](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management)

* [El mecanismo de cifrado de claves en reposo](xref:security/data-protection/implementation/key-encryption-at-rest)

* [La información algorítmica incluida en la clave](xref:security/data-protection/configuration/overview#changing-algorithms-with-usecryptographicalgorithms)

Si necesita que esta configuración se inicie antes que el siguiente tiempo de lanzamiento de claves automática, considere la posibilidad de realizar `IKeyManager.CreateNewKey` una llamada explícita a para forzar la creación de una nueva clave. No olvide proporcionar una fecha de activación explícita ({Now + 2 días} es una buena regla general para dejar tiempo para que se propague el cambio) y la fecha de expiración en la llamada.

>[!TIP]
> Todas las aplicaciones que tocan el repositorio deben especificar la misma `IDataProtectionBuilder` configuración con los métodos de extensión. De lo contrario, las propiedades de la clave conservada dependerán de la aplicación concreta que invocó las rutinas de generación de claves.
