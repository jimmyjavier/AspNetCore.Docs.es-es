---
title: Protección de aplicaciones de ASP.NET Core Blazor Server
author: guardrex
description: Aprenda a proteger aplicaciones de Blazor Server como las aplicaciones de ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/02/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/server/index
ms.openlocfilehash: a8604ca6ea60386bb3c54c950205ee695d37c689
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103142"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a>Protección de aplicaciones de ASP.NET Core Blazor Server

Por [Luke Latham](https://github.com/guardrex)

## <a name="blazor-server-project-template"></a>Plantilla de proyecto de Blazor Server

La plantilla de proyecto de Blazor Server se puede configurar para la autenticación cuando se crea el proyecto.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Siga las instrucciones de Visual Studio que se indican en el artículo <xref:blazor/get-started> para crear un proyecto del servidor de Blazor con un mecanismo de autenticación.

Después de elegir la plantilla **Aplicación del servidor de Blazor** en el cuadro de diálogo **Crear una aplicación web ASP.NET Core**, seleccione **Cambiar** en **Autenticación**.

Se abre un cuadro de diálogo para ofrecer el mismo conjunto de mecanismos de autenticación disponibles para otros proyectos ASP.NET Core:

* **Sin autenticación**
* **Cuentas de usuario individuales**: las cuentas de usuario se pueden almacenar:
  * Dentro de la aplicación mediante el sistema de [Identity](xref:security/authentication/identity) de ASP.NET Core.
  * Con [Azure AD B2C](xref:security/authentication/azure-ad-b2c)
* **Cuentas profesionales o educativas**
* **Autenticación de Windows**

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Siga las instrucciones de Visual Studio Code que se indican en el artículo <xref:blazor/get-started> para crear un proyecto del servidor de Blazor con un mecanismo de autenticación:

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

En la tabla siguiente se muestran los valores de autenticación permitidos (`{AUTHENTICATION}`).

| Mecanismo de autenticación | Descripción |
| ------------------------ | ----------- |
| `None` (valor predeterminado)         | Sin autenticación |
| `Individual`             | Usuarios almacenados en la aplicación con ASP.NET Core Identity |
| `IndividualB2C`          | Usuarios almacenados en [Azure AD B2C](xref:security/authentication/azure-ad-b2c) |
| `SingleOrg`              | Autenticación organizativa para un solo inquilino |
| `MultiOrg`               | Autenticación organizativa para varios inquilinos |
| `Windows`                | Autenticación de Windows |

Con la opción `-o|--output`, el comando usa el valor proporcionado para el marcador de posición `{APP NAME}` para:

* Cree una carpeta para el proyecto.
* Asigne el nombre al proyecto.

Para más información, consulte el comando [dotnet new](/dotnet/core/tools/dotnet-new) de la guía de .NET Core.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

1. Siga las instrucciones de Visual Studio para Mac del artículo <xref:blazor/get-started>.

1. En el paso **Configure your new Blazor Server App** (Configurar la nueva aplicación de Blazor Server), seleccione **Individual Authentication (in-app)** (Autenticación individual [en aplicación]) en la lista desplegable **Authentication** (Autenticación).

1. La aplicación se crea para usuarios individuales almacenados en la aplicación con ASP.NET Core Identity.

# <a name="net-core-cli"></a>[CLI de .NET Core](#tab/netcore-cli/)

Siga las instrucciones de la CLI de .NET Core del artículo <xref:blazor/get-started> para crear un proyecto de Blazor Server con un mecanismo de autenticación:

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

En la tabla siguiente se muestran los valores de autenticación permitidos (`{AUTHENTICATION}`).

| Mecanismo de autenticación | Descripción |
| ------------------------ | ----------- |
| `None` (valor predeterminado)         | Sin autenticación |
| `Individual`             | Usuarios almacenados en la aplicación con ASP.NET Core Identity |
| `IndividualB2C`          | Usuarios almacenados en [Azure AD B2C](xref:security/authentication/azure-ad-b2c) |
| `SingleOrg`              | Autenticación organizativa para un solo inquilino |
| `MultiOrg`               | Autenticación organizativa para varios inquilinos |
| `Windows`                | Autenticación de Windows |

Con la opción `-o|--output`, el comando usa el valor proporcionado para el marcador de posición `{APP NAME}` para:

* Cree una carpeta para el proyecto.
* Asigne el nombre al proyecto.

Para más información, consulte el comando [dotnet new](/dotnet/core/tools/dotnet-new) de la guía de .NET Core.

---

## <a name="secure-an-existing-app"></a>Protección de una aplicación existente

Blazor Las aplicaciones de servidor se configuran para que sean seguras de la misma forma que las aplicaciones de ASP.NET Core. Para obtener más información, vea los artículos en <xref:security/index>.

## <a name="scaffold-identity"></a>Scaffolding para Identity

Scaffolding para Identity en un proyecto de Blazor Server:

* [Sin autorización existente](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization)
* [Con autorización](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization)
