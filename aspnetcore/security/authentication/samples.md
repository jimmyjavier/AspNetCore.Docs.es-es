---
title: Ejemplos de autenticación para ASP.NET Core
author: rick-anderson
description: Proporciona vínculos a los ejemplos de autenticación en el repositorio de ASP.NET Core.
ms.author: riande
ms.date: 01/31/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/samples
ms.openlocfilehash: 7cd0fe60d7917abda7d8ac0e071deca13a4136ce
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776557"
---
# <a name="authentication-samples-for-aspnet-core"></a>Ejemplos de autenticación para ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

El [repositorio ASP.net Core](https://github.com/dotnet/AspNetCore) contiene los siguientes ejemplos de autenticación en la carpeta *AspNetCore/src/Security/samples* :

* [Transformación de notificaciones](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/ClaimsTransformation)
* [Autenticación de cookies](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Cookies)
* [Proveedor de directivas personalizadas: IAuthorizationPolicyProvider](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/CustomPolicyProvider)
* [Opciones y esquemas de autenticación dinámica](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/DynamicSchemes)
* [Notificaciones externas](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Identity.ExternalClaims)
* [Selección entre cookies y otro esquema de autenticación en función de la solicitud](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/PathSchemeSelection)
* [Restringe el acceso a los archivos estáticos](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a>Ejecución de las muestras

* Seleccione una [rama](https://github.com/dotnet/AspNetCore). Por ejemplo: `release/3.1`
* Clone o descargue el [repositorio de ASP.net Core](https://github.com/dotnet/AspNetCore).
* Compruebe que ha instalado la versión [SDK de .net Core](https://dotnet.microsoft.com/download/dotnet-core) que coincide con el clon del repositorio de ASP.net Core.
* Navegue a un ejemplo en *AspNetCore/src/Security/samples* y ejecute el ejemplo `dotnet run`con.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

El [repositorio ASP.net Core](https://github.com/dotnet/AspNetCore) contiene los siguientes ejemplos de autenticación en la carpeta *AspNetCore/src/Security/samples* :

* [Transformación de notificaciones](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/ClaimsTransformation)
* [Autenticación de cookies](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Cookies)
* [Proveedor de directivas personalizadas: IAuthorizationPolicyProvider](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider)
* [Opciones y esquemas de autenticación dinámica](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/DynamicSchemes)
* [Notificaciones externas](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Identity.ExternalClaims)
* [Selección entre cookies y otro esquema de autenticación en función de la solicitud](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/PathSchemeSelection)
* [Restringe el acceso a los archivos estáticos](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a>Ejecución de las muestras

* Seleccione una [rama](https://github.com/dotnet/AspNetCore). Por ejemplo: `release/2.2`
* Clone o descargue el [repositorio de ASP.net Core](https://github.com/dotnet/AspNetCore).
* Compruebe que ha instalado la versión [SDK de .net Core](https://dotnet.microsoft.com/download/dotnet-core) que coincide con el clon del repositorio de ASP.net Core.
* Navegue a un ejemplo en *AspNetCore/src/Security/samples* y ejecute el ejemplo `dotnet run`con.

::: moniker-end
