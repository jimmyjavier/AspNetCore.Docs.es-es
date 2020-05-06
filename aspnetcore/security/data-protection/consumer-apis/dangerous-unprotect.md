---
title: Desprotección de cargas cuyas claves se han revocado en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo desproteger datos, protegidos con claves que se han revocado, en una aplicación ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/consumer-apis/dangerous-unprotect
ms.openlocfilehash: 062703fc72ab4e515a99558b3316070ce1f83f79
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776804"
---
# <a name="unprotect-payloads-whose-keys-have-been-revoked-in-aspnet-core"></a><span data-ttu-id="69c13-103">Desprotección de cargas cuyas claves se han revocado en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="69c13-103">Unprotect payloads whose keys have been revoked in ASP.NET Core</span></span>

<a name="data-protection-consumer-apis-dangerous-unprotect"></a>

<span data-ttu-id="69c13-104">Las API de protección de datos de ASP.NET Core no están pensadas principalmente para la persistencia indefinida de cargas confidenciales.</span><span class="sxs-lookup"><span data-stu-id="69c13-104">The ASP.NET Core data protection APIs are not primarily intended for indefinite persistence of confidential payloads.</span></span> <span data-ttu-id="69c13-105">Otras tecnologías, como [Windows CNG DPAPI](https://msdn.microsoft.com/library/windows/desktop/hh706794%28v=vs.85%29.aspx) y [Azure Rights Management](/rights-management/) , son más adecuadas para el escenario de almacenamiento indefinido, y tienen una sólida funcionalidad de administración de claves.</span><span class="sxs-lookup"><span data-stu-id="69c13-105">Other technologies like [Windows CNG DPAPI](https://msdn.microsoft.com/library/windows/desktop/hh706794%28v=vs.85%29.aspx) and [Azure Rights Management](/rights-management/) are more suited to the scenario of indefinite storage, and they have correspondingly strong key management capabilities.</span></span> <span data-ttu-id="69c13-106">Dicho esto, no hay nada que prohíba a un desarrollador usar las API de protección de datos ASP.NET Core para la protección a largo plazo de datos confidenciales.</span><span class="sxs-lookup"><span data-stu-id="69c13-106">That said, there's nothing prohibiting a developer from using the ASP.NET Core data protection APIs for long-term protection of confidential data.</span></span> <span data-ttu-id="69c13-107">Las claves nunca se quitan del anillo de claves `IDataProtector.Unprotect` , por lo que siempre puede recuperar las cargas existentes siempre que las claves estén disponibles y sean válidas.</span><span class="sxs-lookup"><span data-stu-id="69c13-107">Keys are never removed from the key ring, so `IDataProtector.Unprotect` can always recover existing payloads as long as the keys are available and valid.</span></span>

<span data-ttu-id="69c13-108">Sin embargo, surge un problema cuando el desarrollador intenta desproteger los datos que se han protegido con una clave revocada, como `IDataProtector.Unprotect` producirá una excepción en este caso.</span><span class="sxs-lookup"><span data-stu-id="69c13-108">However, an issue arises when the developer tries to unprotect data that has been protected with a revoked key, as `IDataProtector.Unprotect` will throw an exception in this case.</span></span> <span data-ttu-id="69c13-109">Esto puede ser adecuado para cargas de corta duración o transitorias (como tokens de autenticación), ya que el sistema puede volver a crear fácilmente estos tipos de cargas y, en el peor de los demás, el visitante del sitio podría requerir que vuelva a iniciar sesión.</span><span class="sxs-lookup"><span data-stu-id="69c13-109">This might be fine for short-lived or transient payloads (like authentication tokens), as these kinds of payloads can easily be recreated by the system, and at worst the site visitor might be required to log in again.</span></span> <span data-ttu-id="69c13-110">Pero en el caso de las cargas persistentes, tener `Unprotect` Throw podría provocar una pérdida de datos inaceptable.</span><span class="sxs-lookup"><span data-stu-id="69c13-110">But for persisted payloads, having `Unprotect` throw could lead to unacceptable data loss.</span></span>

## <a name="ipersisteddataprotector"></a><span data-ttu-id="69c13-111">IPersistedDataProtector</span><span class="sxs-lookup"><span data-stu-id="69c13-111">IPersistedDataProtector</span></span>

<span data-ttu-id="69c13-112">Para admitir el escenario de permitir que las cargas se desprotejan incluso en el caso de las claves revocadas, el sistema de protección de `IPersistedDataProtector` datos contiene un tipo.</span><span class="sxs-lookup"><span data-stu-id="69c13-112">To support the scenario of allowing payloads to be unprotected even in the face of revoked keys, the data protection system contains an `IPersistedDataProtector` type.</span></span> <span data-ttu-id="69c13-113">Para obtener una instancia de `IPersistedDataProtector`, simplemente obtenga una instancia de `IDataProtector` de manera normal e intente convertir el `IDataProtector` en. `IPersistedDataProtector`</span><span class="sxs-lookup"><span data-stu-id="69c13-113">To get an instance of `IPersistedDataProtector`, simply get an instance of `IDataProtector` in the normal fashion and try casting the `IDataProtector` to `IPersistedDataProtector`.</span></span>

> [!NOTE]
> <span data-ttu-id="69c13-114">No todas `IDataProtector` las instancias de se pueden `IPersistedDataProtector`convertir en.</span><span class="sxs-lookup"><span data-stu-id="69c13-114">Not all `IDataProtector` instances can be cast to `IPersistedDataProtector`.</span></span> <span data-ttu-id="69c13-115">Los desarrolladores deben usar el operador de C# como o similar para evitar las excepciones en tiempo de ejecución causadas por conversiones no válidas y deben estar preparadas para controlar el caso de error adecuadamente.</span><span class="sxs-lookup"><span data-stu-id="69c13-115">Developers should use the C# as operator or similar to avoid runtime exceptions caused by invalid casts, and they should be prepared to handle the failure case appropriately.</span></span>

<span data-ttu-id="69c13-116">`IPersistedDataProtector`expone la siguiente superficie de API:</span><span class="sxs-lookup"><span data-stu-id="69c13-116">`IPersistedDataProtector` exposes the following API surface:</span></span>

```csharp
DangerousUnprotect(byte[] protectedData, bool ignoreRevocationErrors,
     out bool requiresMigration, out bool wasRevoked) : byte[]
```

<span data-ttu-id="69c13-117">Esta API toma la carga protegida (como una matriz de bytes) y devuelve la carga desprotegida.</span><span class="sxs-lookup"><span data-stu-id="69c13-117">This API takes the protected payload (as a byte array) and returns the unprotected payload.</span></span> <span data-ttu-id="69c13-118">No hay ninguna sobrecarga basada en cadena.</span><span class="sxs-lookup"><span data-stu-id="69c13-118">There's no string-based overload.</span></span> <span data-ttu-id="69c13-119">Los dos parámetros out son los siguientes.</span><span class="sxs-lookup"><span data-stu-id="69c13-119">The two out parameters are as follows.</span></span>

* <span data-ttu-id="69c13-120">`requiresMigration`: se establecerá en true si la clave que se usa para proteger esta carga ya no es la clave predeterminada activa; por ejemplo, la clave que se usa para proteger esta carga es antigua y se ha realizado una operación de desplazamiento de claves.</span><span class="sxs-lookup"><span data-stu-id="69c13-120">`requiresMigration`: will be set to true if the key used to protect this payload is no longer the active default key, e.g., the key used to protect this payload is old and a key rolling operation has since taken place.</span></span> <span data-ttu-id="69c13-121">Es posible que el autor de la llamada desee considerar la posibilidad de volver a proteger la carga en función de sus necesidades empresariales.</span><span class="sxs-lookup"><span data-stu-id="69c13-121">The caller may wish to consider reprotecting the payload depending on their business needs.</span></span>

* <span data-ttu-id="69c13-122">`wasRevoked`: se establecerá en true si se revocó la clave usada para proteger esta carga.</span><span class="sxs-lookup"><span data-stu-id="69c13-122">`wasRevoked`: will be set to true if the key used to protect this payload was revoked.</span></span>

>[!WARNING]
> <span data-ttu-id="69c13-123">Extreme las `DangerousUnprotect` precauciones al pasar `ignoreRevocationErrors: true` al método.</span><span class="sxs-lookup"><span data-stu-id="69c13-123">Exercise extreme caution when passing `ignoreRevocationErrors: true` to the `DangerousUnprotect` method.</span></span> <span data-ttu-id="69c13-124">Si después de llamar a este `wasRevoked` método el valor es true, la clave usada para proteger esta carga se ha revocado y la autenticidad de la carga debe tratarse como sospechosa.</span><span class="sxs-lookup"><span data-stu-id="69c13-124">If after calling this method the `wasRevoked` value is true, then the key used to protect this payload was revoked, and the payload's authenticity should be treated as suspect.</span></span> <span data-ttu-id="69c13-125">En este caso, siga usando solo la carga desprotegida si tiene una garantía independiente de que es auténtica, por ejemplo, que proviene de una base de datos segura en lugar de ser enviada por un cliente web que no es de confianza.</span><span class="sxs-lookup"><span data-stu-id="69c13-125">In this case, only continue operating on the unprotected payload if you have some separate assurance that it's authentic, e.g. that it's coming from a secure database rather than being sent by an untrusted web client.</span></span>

[!code-csharp[](dangerous-unprotect/samples/dangerous-unprotect.cs)]
