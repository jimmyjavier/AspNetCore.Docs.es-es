---
title: Configuración de la protección de datos en ASP.NET Core
author: rick-anderson
description: Explore los temas en los que se explica cómo configurar la protección de datos en ASP.NET Core.
ms.author: riande
ms.date: 10/12/2017
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/configuration/index
ms.openlocfilehash: 9dfe20731fcec14fd359833b9a893f9305fecc10
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/26/2020
ms.locfileid: "85400705"
---
# <a name="data-protection-configuration-in-aspnet-core"></a>Configuración de la protección de datos en ASP.NET Core

Consulte estos temas para obtener información sobre la configuración de la protección de datos en ASP.NET Core:

* [Configurar la protección de datos en ASP.NET Core](xref:security/data-protection/configuration/overview)  
  Una introducción a la configuración de la protección de datos de ASP.NET Core.

* [Administración y duración de las claves de protección de datos](xref:security/data-protection/configuration/default-settings)  
  Información sobre la administración y duración de las claves de protección de datos.

* [Compatibilidad con la directiva a nivel de equipo para la protección de datos](xref:security/data-protection/configuration/machine-wide-policy)  
  Obtenga más información sobre cómo configurar una directiva predeterminada a nivel de equipo para todas las aplicaciones que usan la protección de datos.

* [Escenarios no compatibles con DI para la protección de datos en ASP.NET Core](xref:security/data-protection/configuration/non-di-scenarios)  
  Cómo usar el tipo concreto [DataProtectionProvider](/dotnet/api/Microsoft.AspNetCore.DataProtection.DataProtectionProvider) para usar la protección de datos sin tener que pasar a través de rutas de código específicas de DI.
