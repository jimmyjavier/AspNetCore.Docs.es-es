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
# <a name="authentication-samples-for-aspnet-core"></a>Ejemplos de autenticación para ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

El [repositorio ASP.NET Core](https://github.com/dotnet/AspNetCore) contiene los siguientes ejemplos de autenticación en la carpeta *AspNetCore/src/Security/samples:*

* [Transformación de notificaciones](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/ClaimsTransformation)
* [Autenticación de cookies](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Cookies)
* [Proveedor de directivas personalizado: IAuthorizationPolicyProvider](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/CustomPolicyProvider)
* [Esquemas y opciones de autenticación dinámica](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/DynamicSchemes)
* [Reclamaciones externas](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Identity.ExternalClaims)
* [Selección entre cookie y otro esquema de autenticación basado en la solicitud](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/PathSchemeSelection)
* [Restringe el acceso a archivos estáticos](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a>Ejecución de las muestras

* Seleccione una [rama](https://github.com/dotnet/AspNetCore). Por ejemplo: `release/3.1`
* Clonar o descargar el [repositorio ASP.NET Core](https://github.com/dotnet/AspNetCore).
* Compruebe que ha instalado la versión del SDK de [.NET Core](https://dotnet.microsoft.com/download/dotnet-core) que coincida con el clon del repositorio ASP.NET Core.
* Vaya a un ejemplo en *AspNetCore/src/Security/samples* y ejecute el ejemplo con `dotnet run`.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

El [repositorio ASP.NET Core](https://github.com/dotnet/AspNetCore) contiene los siguientes ejemplos de autenticación en la carpeta *AspNetCore/src/Security/samples:*

* [Transformación de notificaciones](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/ClaimsTransformation)
* [Autenticación de cookies](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Cookies)
* [Proveedor de directivas personalizado: IAuthorizationPolicyProvider](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider)
* [Esquemas y opciones de autenticación dinámica](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/DynamicSchemes)
* [Reclamaciones externas](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Identity.ExternalClaims)
* [Selección entre cookie y otro esquema de autenticación basado en la solicitud](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/PathSchemeSelection)
* [Restringe el acceso a archivos estáticos](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a>Ejecución de las muestras

* Seleccione una [rama](https://github.com/dotnet/AspNetCore). Por ejemplo: `release/2.2`
* Clonar o descargar el [repositorio ASP.NET Core](https://github.com/dotnet/AspNetCore).
* Compruebe que ha instalado la versión del SDK de [.NET Core](https://dotnet.microsoft.com/download/dotnet-core) que coincida con el clon del repositorio ASP.NET Core.
* Vaya a un ejemplo en *AspNetCore/src/Security/samples* y ejecute el ejemplo con `dotnet run`.

::: moniker-end
