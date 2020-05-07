---
title: Plataformas admitidas de Blazor de ASP.NET Core
author: guardrex
description: Obtenga información sobre las plataformas admitidas en Blazor de ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/supported-platforms
ms.openlocfilehash: 1c78803e6468f924bf8c8e9403a34565b114006f
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2020
ms.locfileid: "82771121"
---
# <a name="aspnet-core-blazor-supported-platforms"></a><span data-ttu-id="90237-103">Plataformas admitidas de Blazor de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="90237-103">ASP.NET Core Blazor supported platforms</span></span>

<span data-ttu-id="90237-104">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="90237-104">By [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

## <a name="browser-requirements"></a><span data-ttu-id="90237-105">Requisitos del explorador</span><span class="sxs-lookup"><span data-stu-id="90237-105">Browser requirements</span></span>

### <a name="blazor-webassembly"></a><span data-ttu-id="90237-106">WebAssembly de Blazor</span><span class="sxs-lookup"><span data-stu-id="90237-106">Blazor WebAssembly</span></span>

| <span data-ttu-id="90237-107">Explorador</span><span class="sxs-lookup"><span data-stu-id="90237-107">Browser</span></span>                          | <span data-ttu-id="90237-108">Versión</span><span class="sxs-lookup"><span data-stu-id="90237-108">Version</span></span>               |
| -------------------------------- | :-------------------: |
| <span data-ttu-id="90237-109">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="90237-109">Microsoft Edge</span></span>                   | <span data-ttu-id="90237-110">Current</span><span class="sxs-lookup"><span data-stu-id="90237-110">Current</span></span>               |
| <span data-ttu-id="90237-111">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="90237-111">Mozilla Firefox</span></span>                  | <span data-ttu-id="90237-112">Current</span><span class="sxs-lookup"><span data-stu-id="90237-112">Current</span></span>               |
| <span data-ttu-id="90237-113">Google Chrome, incluido Android</span><span class="sxs-lookup"><span data-stu-id="90237-113">Google Chrome, including Android</span></span> | <span data-ttu-id="90237-114">Current</span><span class="sxs-lookup"><span data-stu-id="90237-114">Current</span></span>               |
| <span data-ttu-id="90237-115">Safari, incluido iOS</span><span class="sxs-lookup"><span data-stu-id="90237-115">Safari, including iOS</span></span>            | <span data-ttu-id="90237-116">Current</span><span class="sxs-lookup"><span data-stu-id="90237-116">Current</span></span>               |
| <span data-ttu-id="90237-117">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="90237-117">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="90237-118">No admitidas&dagger;</span><span class="sxs-lookup"><span data-stu-id="90237-118">Not Supported&dagger;</span></span> |

<span data-ttu-id="90237-119">&dagger;Microsoft Internet Explorer no admite [WebAssembly](https://webassembly.org).</span><span class="sxs-lookup"><span data-stu-id="90237-119">&dagger;Microsoft Internet Explorer doesn't support [WebAssembly](https://webassembly.org).</span></span>

### <a name="blazor-server"></a><span data-ttu-id="90237-120">Servidor Blazor</span><span class="sxs-lookup"><span data-stu-id="90237-120">Blazor Server</span></span>

| <span data-ttu-id="90237-121">Explorador</span><span class="sxs-lookup"><span data-stu-id="90237-121">Browser</span></span>                          | <span data-ttu-id="90237-122">Versión</span><span class="sxs-lookup"><span data-stu-id="90237-122">Version</span></span>    |
| -------------------------------- | :--------: |
| <span data-ttu-id="90237-123">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="90237-123">Microsoft Edge</span></span>                   | <span data-ttu-id="90237-124">Current</span><span class="sxs-lookup"><span data-stu-id="90237-124">Current</span></span>    |
| <span data-ttu-id="90237-125">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="90237-125">Mozilla Firefox</span></span>                  | <span data-ttu-id="90237-126">Current</span><span class="sxs-lookup"><span data-stu-id="90237-126">Current</span></span>    |
| <span data-ttu-id="90237-127">Google Chrome, incluido Android</span><span class="sxs-lookup"><span data-stu-id="90237-127">Google Chrome, including Android</span></span> | <span data-ttu-id="90237-128">Current</span><span class="sxs-lookup"><span data-stu-id="90237-128">Current</span></span>    |
| <span data-ttu-id="90237-129">Safari, incluido iOS</span><span class="sxs-lookup"><span data-stu-id="90237-129">Safari, including iOS</span></span>            | <span data-ttu-id="90237-130">Current</span><span class="sxs-lookup"><span data-stu-id="90237-130">Current</span></span>    |
| <span data-ttu-id="90237-131">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="90237-131">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="90237-132">11&dagger;</span><span class="sxs-lookup"><span data-stu-id="90237-132">11&dagger;</span></span> |

<span data-ttu-id="90237-133">&dagger;Se requieren más polyfill (por ejemplo, se pueden agregar promesas a través de un paquete [Polyfill.io](https://polyfill.io/v3/)).</span><span class="sxs-lookup"><span data-stu-id="90237-133">&dagger;Additional polyfills are required (for example, promises can be added via a [Polyfill.io](https://polyfill.io/v3/) bundle).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="90237-134">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="90237-134">Additional resources</span></span>

* <xref:blazor/hosting-models>
