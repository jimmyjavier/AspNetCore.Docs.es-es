---
title: Ejemplos de autenticación para ASP.NET Core
author: rick-anderson
description: Proporciona vínculos a los ejemplos de autenticación en el repositorio ASP.NET Core.
ms.author: riande
ms.date: 01/31/2019
uid: security/authentication/samples
ms.openlocfilehash: b33eaa5c1bda9e23b2815cd1663e02ae06fec856
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/14/2020
ms.locfileid: "81308220"
---
# <a name="authentication-samples-for-aspnet-core"></a><span data-ttu-id="f66b6-103">Ejemplos de autenticación para ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f66b6-103">Authentication samples for ASP.NET Core</span></span>

<span data-ttu-id="f66b6-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f66b6-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f66b6-105">El [repositorio ASP.NET Core](https://github.com/dotnet/AspNetCore) contiene los siguientes ejemplos de autenticación en la carpeta *AspNetCore/src/Security/samples:*</span><span class="sxs-lookup"><span data-stu-id="f66b6-105">The [ASP.NET Core repository](https://github.com/dotnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="f66b6-106">Transformación de notificaciones</span><span class="sxs-lookup"><span data-stu-id="f66b6-106">Claims transformation</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/ClaimsTransformation)
* [<span data-ttu-id="f66b6-107">Autenticación de cookies</span><span class="sxs-lookup"><span data-stu-id="f66b6-107">Cookie authentication</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Cookies)
* [<span data-ttu-id="f66b6-108">Proveedor de directivas personalizado: IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="f66b6-108">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="f66b6-109">Esquemas y opciones de autenticación dinámica</span><span class="sxs-lookup"><span data-stu-id="f66b6-109">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/DynamicSchemes)
* [<span data-ttu-id="f66b6-110">Reclamaciones externas</span><span class="sxs-lookup"><span data-stu-id="f66b6-110">External claims</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Identity.ExternalClaims)
* [<span data-ttu-id="f66b6-111">Selección entre cookie y otro esquema de autenticación basado en la solicitud</span><span class="sxs-lookup"><span data-stu-id="f66b6-111">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="f66b6-112">Restringe el acceso a archivos estáticos</span><span class="sxs-lookup"><span data-stu-id="f66b6-112">Restricts access to static files</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="f66b6-113">Ejecución de las muestras</span><span class="sxs-lookup"><span data-stu-id="f66b6-113">Run the samples</span></span>

* <span data-ttu-id="f66b6-114">Seleccione una [rama](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="f66b6-114">Select a [branch](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="f66b6-115">Por ejemplo: `release/3.1`</span><span class="sxs-lookup"><span data-stu-id="f66b6-115">For example, `release/3.1`</span></span>
* <span data-ttu-id="f66b6-116">Clonar o descargar el [repositorio ASP.NET Core](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="f66b6-116">Clone or download the [ASP.NET Core repository](https://github.com/dotnet/AspNetCore).</span></span>
* <span data-ttu-id="f66b6-117">Compruebe que ha instalado la versión del SDK de [.NET Core](https://dotnet.microsoft.com/download/dotnet-core) que coincida con el clon del repositorio ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f66b6-117">Verify you have installed the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="f66b6-118">Vaya a un ejemplo en *AspNetCore/src/Security/samples* y ejecute el ejemplo con `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="f66b6-118">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f66b6-119">El [repositorio ASP.NET Core](https://github.com/dotnet/AspNetCore) contiene los siguientes ejemplos de autenticación en la carpeta *AspNetCore/src/Security/samples:*</span><span class="sxs-lookup"><span data-stu-id="f66b6-119">The [ASP.NET Core repository](https://github.com/dotnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="f66b6-120">Transformación de notificaciones</span><span class="sxs-lookup"><span data-stu-id="f66b6-120">Claims transformation</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/ClaimsTransformation)
* [<span data-ttu-id="f66b6-121">Autenticación de cookies</span><span class="sxs-lookup"><span data-stu-id="f66b6-121">Cookie authentication</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Cookies)
* [<span data-ttu-id="f66b6-122">Proveedor de directivas personalizado: IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="f66b6-122">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="f66b6-123">Esquemas y opciones de autenticación dinámica</span><span class="sxs-lookup"><span data-stu-id="f66b6-123">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/DynamicSchemes)
* [<span data-ttu-id="f66b6-124">Reclamaciones externas</span><span class="sxs-lookup"><span data-stu-id="f66b6-124">External claims</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Identity.ExternalClaims)
* [<span data-ttu-id="f66b6-125">Selección entre cookie y otro esquema de autenticación basado en la solicitud</span><span class="sxs-lookup"><span data-stu-id="f66b6-125">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="f66b6-126">Restringe el acceso a archivos estáticos</span><span class="sxs-lookup"><span data-stu-id="f66b6-126">Restricts access to static files</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="f66b6-127">Ejecución de las muestras</span><span class="sxs-lookup"><span data-stu-id="f66b6-127">Run the samples</span></span>

* <span data-ttu-id="f66b6-128">Seleccione una [rama](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="f66b6-128">Select a [branch](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="f66b6-129">Por ejemplo: `release/2.2`</span><span class="sxs-lookup"><span data-stu-id="f66b6-129">For example, `release/2.2`</span></span>
* <span data-ttu-id="f66b6-130">Clonar o descargar el [repositorio ASP.NET Core](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="f66b6-130">Clone or download the [ASP.NET Core repository](https://github.com/dotnet/AspNetCore).</span></span>
* <span data-ttu-id="f66b6-131">Compruebe que ha instalado la versión del SDK de [.NET Core](https://dotnet.microsoft.com/download/dotnet-core) que coincida con el clon del repositorio ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f66b6-131">Verify you have installed the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="f66b6-132">Vaya a un ejemplo en *AspNetCore/src/Security/samples* y ejecute el ejemplo con `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="f66b6-132">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end
