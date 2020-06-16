---
title: Plataformas admitidas de Blazor de ASP.NET Core
author: guardrex
description: Obtenga información sobre las plataformas admitidas en Blazor de ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/supported-platforms
ms.openlocfilehash: c10132c87c93346af89c548363e786967609f3da
ms.sourcegitcommit: d243fadeda20ad4f142ea60301ae5f5e0d41ed60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/12/2020
ms.locfileid: "83607984"
---
# <a name="aspnet-core-blazor-supported-platforms"></a>Plataformas admitidas de Blazor de ASP.NET Core

Por [Luke Latham](https://github.com/guardrex)

## <a name="browser-requirements"></a>Requisitos del explorador

### <a name="blazor-webassembly"></a>Blazor WebAssembly

| Explorador                          | Versión               |
| -------------------------------- | :-------------------: |
| Microsoft Edge                   | Current               |
| Mozilla Firefox                  | Current               |
| Google Chrome, incluido Android | Current               |
| Safari, incluido iOS            | Current               |
| Microsoft Internet Explorer      | No admitidas&dagger; |

&dagger;Microsoft Internet Explorer no admite [WebAssembly](https://webassembly.org).

### <a name="blazor-server"></a>Servidor de Blazor

| Explorador                          | Versión    |
| -------------------------------- | :--------: |
| Microsoft Edge                   | Current    |
| Mozilla Firefox                  | Current    |
| Google Chrome, incluido Android | Current    |
| Safari, incluido iOS            | Current    |
| Microsoft Internet Explorer      | 11&dagger; |

&dagger;Se requieren más polyfill (por ejemplo, se pueden agregar promesas a través de un paquete [Polyfill.io](https://polyfill.io/v3/)).

## <a name="additional-resources"></a>Recursos adicionales

* <xref:blazor/hosting-models>
