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
# <a name="key-immutability-and-key-settings-in-aspnet-core"></a><span data-ttu-id="f810f-103">Inmutabilidad de claves y configuración de claves en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f810f-103">Key immutability and key settings in ASP.NET Core</span></span>

<span data-ttu-id="f810f-104">Una vez que un objeto se conserva en la memoria auxiliar, su representación es fija para siempre.</span><span class="sxs-lookup"><span data-stu-id="f810f-104">Once an object is persisted to the backing store, its representation is forever fixed.</span></span> <span data-ttu-id="f810f-105">Se pueden agregar nuevos datos a la memoria auxiliar, pero nunca se pueden mutar los datos existentes.</span><span class="sxs-lookup"><span data-stu-id="f810f-105">New data can be added to the backing store, but existing data can never be mutated.</span></span> <span data-ttu-id="f810f-106">El propósito principal de este comportamiento es evitar daños en los datos.</span><span class="sxs-lookup"><span data-stu-id="f810f-106">The primary purpose of this behavior is to prevent data corruption.</span></span>

<span data-ttu-id="f810f-107">Una consecuencia de este comportamiento es que una vez que una clave se escribe en la memoria auxiliar, es inmutable.</span><span class="sxs-lookup"><span data-stu-id="f810f-107">One consequence of this behavior is that once a key is written to the backing store, it's immutable.</span></span> <span data-ttu-id="f810f-108">Sus fechas de creación, activación y expiración no se pueden cambiar nunca, aunque se pueden revocar `IKeyManager`mediante.</span><span class="sxs-lookup"><span data-stu-id="f810f-108">Its creation, activation, and expiration dates can never be changed, though it can revoked by using `IKeyManager`.</span></span> <span data-ttu-id="f810f-109">Además, la información algorítmica subyacente, el material de creación de claves maestra y el cifrado en las propiedades de REST también son inmutables.</span><span class="sxs-lookup"><span data-stu-id="f810f-109">Additionally, its underlying algorithmic information, master keying material, and encryption at rest properties are also immutable.</span></span>

<span data-ttu-id="f810f-110">Si el desarrollador cambia cualquier valor de configuración que afecte a la persistencia de claves, esos cambios no entrarán en vigor hasta la próxima vez que se genere una `IKeyManager.CreateNewKey` clave, ya sea a través de una llamada explícita a o a través del comportamiento de la [generación automática de claves](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) del sistema de protección de datos.</span><span class="sxs-lookup"><span data-stu-id="f810f-110">If the developer changes any setting that affects key persistence, those changes won't go into effect until the next time a key is generated, either via an explicit call to `IKeyManager.CreateNewKey` or via the data protection system's own [automatic key generation](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) behavior.</span></span> <span data-ttu-id="f810f-111">Los valores que afectan a la persistencia de claves son los siguientes:</span><span class="sxs-lookup"><span data-stu-id="f810f-111">The settings that affect key persistence are as follows:</span></span>

* [<span data-ttu-id="f810f-112">La duración de la clave predeterminada</span><span class="sxs-lookup"><span data-stu-id="f810f-112">The default key lifetime</span></span>](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management)

* [<span data-ttu-id="f810f-113">El mecanismo de cifrado de claves en reposo</span><span class="sxs-lookup"><span data-stu-id="f810f-113">The key encryption at rest mechanism</span></span>](xref:security/data-protection/implementation/key-encryption-at-rest)

* [<span data-ttu-id="f810f-114">La información algorítmica incluida en la clave</span><span class="sxs-lookup"><span data-stu-id="f810f-114">The algorithmic information contained within the key</span></span>](xref:security/data-protection/configuration/overview#changing-algorithms-with-usecryptographicalgorithms)

<span data-ttu-id="f810f-115">Si necesita que esta configuración se inicie antes que el siguiente tiempo de lanzamiento de claves automática, considere la posibilidad de realizar `IKeyManager.CreateNewKey` una llamada explícita a para forzar la creación de una nueva clave.</span><span class="sxs-lookup"><span data-stu-id="f810f-115">If you need these settings to kick in earlier than the next automatic key rolling time, consider making an explicit call to `IKeyManager.CreateNewKey` to force the creation of a new key.</span></span> <span data-ttu-id="f810f-116">No olvide proporcionar una fecha de activación explícita ({Now + 2 días} es una buena regla general para dejar tiempo para que se propague el cambio) y la fecha de expiración en la llamada.</span><span class="sxs-lookup"><span data-stu-id="f810f-116">Remember to provide an explicit activation date ({ now + 2 days } is a good rule of thumb to allow time for the change to propagate) and expiration date in the call.</span></span>

>[!TIP]
> <span data-ttu-id="f810f-117">Todas las aplicaciones que tocan el repositorio deben especificar la misma `IDataProtectionBuilder` configuración con los métodos de extensión.</span><span class="sxs-lookup"><span data-stu-id="f810f-117">All applications touching the repository should specify the same settings with the `IDataProtectionBuilder` extension methods.</span></span> <span data-ttu-id="f810f-118">De lo contrario, las propiedades de la clave conservada dependerán de la aplicación concreta que invocó las rutinas de generación de claves.</span><span class="sxs-lookup"><span data-stu-id="f810f-118">Otherwise, the properties of the persisted key will be dependent on the particular application that invoked the key generation routines.</span></span>
