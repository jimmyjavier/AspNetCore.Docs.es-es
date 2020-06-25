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
ms.openlocfilehash: 2811e08fd2f6c66112ffa0bb40f474158f4c7a59
ms.sourcegitcommit: 5e462c3328c70f95969d02adce9c71592049f54c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/24/2020
ms.locfileid: "85292690"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a><span data-ttu-id="dbc77-103">Protección de aplicaciones de ASP.NET Core Blazor Server</span><span class="sxs-lookup"><span data-stu-id="dbc77-103">Secure ASP.NET Core Blazor Server apps</span></span>

<span data-ttu-id="dbc77-104">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="dbc77-104">By [Luke Latham](https://github.com/guardrex)</span></span>

Blazor<span data-ttu-id="dbc77-105"> Las aplicaciones de servidor se configuran para que sean seguras de la misma forma que las aplicaciones de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="dbc77-105"> Server apps are configured for security in the same manner as ASP.NET Core apps.</span></span> <span data-ttu-id="dbc77-106">Para obtener más información, vea los artículos en <xref:security/index>.</span><span class="sxs-lookup"><span data-stu-id="dbc77-106">For more information, see the articles under <xref:security/index>.</span></span> <span data-ttu-id="dbc77-107">Los temas de esta información general se aplican específicamente a Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="dbc77-107">Topics under this overview apply specifically to Blazor Server.</span></span> 

## <a name="blazor-server-project-template"></a><span data-ttu-id="dbc77-108">Plantilla de proyecto de Blazor Server</span><span class="sxs-lookup"><span data-stu-id="dbc77-108">Blazor Server project template</span></span>

<span data-ttu-id="dbc77-109">La plantilla de proyecto de Blazor Server se puede configurar para la autenticación cuando se crea el proyecto.</span><span class="sxs-lookup"><span data-stu-id="dbc77-109">The Blazor Server project template can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dbc77-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dbc77-110">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="dbc77-111">Siga las instrucciones de Visual Studio que se indican en el artículo <xref:blazor/get-started> para crear un proyecto del servidor de Blazor con un mecanismo de autenticación.</span><span class="sxs-lookup"><span data-stu-id="dbc77-111">Follow the Visual Studio guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="dbc77-112">Después de elegir la plantilla **Aplicación del servidor de Blazor** en el cuadro de diálogo **Crear una aplicación web ASP.NET Core**, seleccione **Cambiar** en **Autenticación**.</span><span class="sxs-lookup"><span data-stu-id="dbc77-112">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="dbc77-113">Se abre un cuadro de diálogo para ofrecer el mismo conjunto de mecanismos de autenticación disponibles para otros proyectos ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="dbc77-113">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="dbc77-114">**Sin autenticación**</span><span class="sxs-lookup"><span data-stu-id="dbc77-114">**No Authentication**</span></span>
* <span data-ttu-id="dbc77-115">**Cuentas de usuario individuales**: las cuentas de usuario se pueden almacenar:</span><span class="sxs-lookup"><span data-stu-id="dbc77-115">**Individual User Accounts**: User accounts can be stored:</span></span>
  * <span data-ttu-id="dbc77-116">Dentro de la aplicación mediante el sistema de [Identity](xref:security/authentication/identity) de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="dbc77-116">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="dbc77-117">Con [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="dbc77-117">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="dbc77-118">**Cuentas profesionales o educativas**</span><span class="sxs-lookup"><span data-stu-id="dbc77-118">**Work or School Accounts**</span></span>
* <span data-ttu-id="dbc77-119">**Autenticación de Windows**</span><span class="sxs-lookup"><span data-stu-id="dbc77-119">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="dbc77-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dbc77-120">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="dbc77-121">Siga las instrucciones de Visual Studio Code que se indican en el artículo <xref:blazor/get-started> para crear un proyecto del servidor de Blazor con un mecanismo de autenticación:</span><span class="sxs-lookup"><span data-stu-id="dbc77-121">Follow the Visual Studio Code guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="dbc77-122">En la tabla siguiente se muestran los valores de autenticación permitidos (`{AUTHENTICATION}`).</span><span class="sxs-lookup"><span data-stu-id="dbc77-122">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="dbc77-123">Mecanismo de autenticación</span><span class="sxs-lookup"><span data-stu-id="dbc77-123">Authentication mechanism</span></span> | <span data-ttu-id="dbc77-124">Descripción</span><span class="sxs-lookup"><span data-stu-id="dbc77-124">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="dbc77-125">`None` (valor predeterminado)</span><span class="sxs-lookup"><span data-stu-id="dbc77-125">`None` (default)</span></span>         | <span data-ttu-id="dbc77-126">Sin autenticación</span><span class="sxs-lookup"><span data-stu-id="dbc77-126">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="dbc77-127">Usuarios almacenados en la aplicación con ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="dbc77-127">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="dbc77-128">Usuarios almacenados en [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="dbc77-128">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="dbc77-129">Autenticación organizativa para un solo inquilino</span><span class="sxs-lookup"><span data-stu-id="dbc77-129">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="dbc77-130">Autenticación organizativa para varios inquilinos</span><span class="sxs-lookup"><span data-stu-id="dbc77-130">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="dbc77-131">Autenticación de Windows</span><span class="sxs-lookup"><span data-stu-id="dbc77-131">Windows Authentication</span></span> |

<span data-ttu-id="dbc77-132">Con la opción `-o|--output`, el comando usa el valor proporcionado para el marcador de posición `{APP NAME}` para:</span><span class="sxs-lookup"><span data-stu-id="dbc77-132">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="dbc77-133">Cree una carpeta para el proyecto.</span><span class="sxs-lookup"><span data-stu-id="dbc77-133">Create a folder for the project.</span></span>
* <span data-ttu-id="dbc77-134">Asigne el nombre al proyecto.</span><span class="sxs-lookup"><span data-stu-id="dbc77-134">Name the project.</span></span>

<span data-ttu-id="dbc77-135">Para más información, consulte el comando [`dotnet new`](/dotnet/core/tools/dotnet-new) de la guía de .NET Core.</span><span class="sxs-lookup"><span data-stu-id="dbc77-135">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="dbc77-136">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="dbc77-136">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="dbc77-137">Siga las instrucciones de Visual Studio para Mac del artículo <xref:blazor/get-started>.</span><span class="sxs-lookup"><span data-stu-id="dbc77-137">Follow the Visual Studio for Mac guidance in the <xref:blazor/get-started> article.</span></span>

1. <span data-ttu-id="dbc77-138">En el paso **Configure your new Blazor Server App** (Configurar la nueva aplicación de Blazor Server), seleccione **Individual Authentication (in-app)** (Autenticación individual [en aplicación]) en la lista desplegable **Authentication** (Autenticación).</span><span class="sxs-lookup"><span data-stu-id="dbc77-138">On the **Configure your new Blazor Server App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="dbc77-139">La aplicación se crea para usuarios individuales almacenados en la aplicación con ASP.NET Core Identity.</span><span class="sxs-lookup"><span data-stu-id="dbc77-139">The app is created for individual users stored in the app with ASP.NET Core Identity.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="dbc77-140">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="dbc77-140">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="dbc77-141">Siga las instrucciones de la CLI de .NET Core del artículo <xref:blazor/get-started> para crear un proyecto de Blazor Server con un mecanismo de autenticación:</span><span class="sxs-lookup"><span data-stu-id="dbc77-141">Follow the .NET Core CLI guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="dbc77-142">En la tabla siguiente se muestran los valores de autenticación permitidos (`{AUTHENTICATION}`).</span><span class="sxs-lookup"><span data-stu-id="dbc77-142">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="dbc77-143">Mecanismo de autenticación</span><span class="sxs-lookup"><span data-stu-id="dbc77-143">Authentication mechanism</span></span> | <span data-ttu-id="dbc77-144">Descripción</span><span class="sxs-lookup"><span data-stu-id="dbc77-144">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="dbc77-145">`None` (valor predeterminado)</span><span class="sxs-lookup"><span data-stu-id="dbc77-145">`None` (default)</span></span>         | <span data-ttu-id="dbc77-146">Sin autenticación</span><span class="sxs-lookup"><span data-stu-id="dbc77-146">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="dbc77-147">Usuarios almacenados en la aplicación con ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="dbc77-147">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="dbc77-148">Usuarios almacenados en [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="dbc77-148">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="dbc77-149">Autenticación organizativa para un solo inquilino</span><span class="sxs-lookup"><span data-stu-id="dbc77-149">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="dbc77-150">Autenticación organizativa para varios inquilinos</span><span class="sxs-lookup"><span data-stu-id="dbc77-150">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="dbc77-151">Autenticación de Windows</span><span class="sxs-lookup"><span data-stu-id="dbc77-151">Windows Authentication</span></span> |

<span data-ttu-id="dbc77-152">Con la opción `-o|--output`, el comando usa el valor proporcionado para el marcador de posición `{APP NAME}` para:</span><span class="sxs-lookup"><span data-stu-id="dbc77-152">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="dbc77-153">Cree una carpeta para el proyecto.</span><span class="sxs-lookup"><span data-stu-id="dbc77-153">Create a folder for the project.</span></span>
* <span data-ttu-id="dbc77-154">Asigne el nombre al proyecto.</span><span class="sxs-lookup"><span data-stu-id="dbc77-154">Name the project.</span></span>

<span data-ttu-id="dbc77-155">Para más información, consulte el comando [`dotnet new`](/dotnet/core/tools/dotnet-new) de la guía de .NET Core.</span><span class="sxs-lookup"><span data-stu-id="dbc77-155">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

---

## <a name="scaffold-identity"></a><span data-ttu-id="dbc77-156">Scaffolding para Identity</span><span class="sxs-lookup"><span data-stu-id="dbc77-156">Scaffold Identity</span></span>

<span data-ttu-id="dbc77-157">Scaffolding para Identity en un proyecto de Blazor Server:</span><span class="sxs-lookup"><span data-stu-id="dbc77-157">Scaffold Identity into a Blazor Server project:</span></span>

* <span data-ttu-id="dbc77-158">[Sin autorización existente](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization)</span><span class="sxs-lookup"><span data-stu-id="dbc77-158">[Without existing authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span></span>
* <span data-ttu-id="dbc77-159">[Con autorización](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization)</span><span class="sxs-lookup"><span data-stu-id="dbc77-159">[With authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span></span>
