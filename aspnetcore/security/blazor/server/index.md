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
uid: security/blazor/server/index
ms.openlocfilehash: 5ba7bbde49bfc232795d375a1ec644825a0dee1e
ms.sourcegitcommit: 67eadd7bf28eae0b8786d85e90a7df811ffe5904
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2020
ms.locfileid: "84454641"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a><span data-ttu-id="c1d89-103">Protección de aplicaciones de ASP.NET Core Blazor Server</span><span class="sxs-lookup"><span data-stu-id="c1d89-103">Secure ASP.NET Core Blazor Server apps</span></span>

<span data-ttu-id="c1d89-104">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="c1d89-104">By [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="blazor-server-project-template"></a><span data-ttu-id="c1d89-105">Plantilla de proyecto de Blazor Server</span><span class="sxs-lookup"><span data-stu-id="c1d89-105">Blazor Server project template</span></span>

<span data-ttu-id="c1d89-106">La plantilla de proyecto de Blazor Server se puede configurar para la autenticación cuando se crea el proyecto.</span><span class="sxs-lookup"><span data-stu-id="c1d89-106">The Blazor Server project template can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c1d89-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c1d89-107">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c1d89-108">Siga las instrucciones de Visual Studio que se indican en el artículo <xref:blazor/get-started> para crear un proyecto del servidor de Blazor con un mecanismo de autenticación.</span><span class="sxs-lookup"><span data-stu-id="c1d89-108">Follow the Visual Studio guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="c1d89-109">Después de elegir la plantilla **Aplicación del servidor de Blazor** en el cuadro de diálogo **Crear una aplicación web ASP.NET Core**, seleccione **Cambiar** en **Autenticación**.</span><span class="sxs-lookup"><span data-stu-id="c1d89-109">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="c1d89-110">Se abre un cuadro de diálogo para ofrecer el mismo conjunto de mecanismos de autenticación disponibles para otros proyectos ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="c1d89-110">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="c1d89-111">**Sin autenticación**</span><span class="sxs-lookup"><span data-stu-id="c1d89-111">**No Authentication**</span></span>
* <span data-ttu-id="c1d89-112">**Cuentas de usuario individuales**: las cuentas de usuario se pueden almacenar:</span><span class="sxs-lookup"><span data-stu-id="c1d89-112">**Individual User Accounts**: User accounts can be stored:</span></span>
  * <span data-ttu-id="c1d89-113">Dentro de la aplicación mediante el sistema de [Identity](xref:security/authentication/identity) de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c1d89-113">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="c1d89-114">Con [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="c1d89-114">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="c1d89-115">**Cuentas profesionales o educativas**</span><span class="sxs-lookup"><span data-stu-id="c1d89-115">**Work or School Accounts**</span></span>
* <span data-ttu-id="c1d89-116">**Autenticación de Windows**</span><span class="sxs-lookup"><span data-stu-id="c1d89-116">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c1d89-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c1d89-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="c1d89-118">Siga las instrucciones de Visual Studio Code que se indican en el artículo <xref:blazor/get-started> para crear un proyecto del servidor de Blazor con un mecanismo de autenticación:</span><span class="sxs-lookup"><span data-stu-id="c1d89-118">Follow the Visual Studio Code guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="c1d89-119">En la tabla siguiente se muestran los valores de autenticación permitidos (`{AUTHENTICATION}`).</span><span class="sxs-lookup"><span data-stu-id="c1d89-119">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="c1d89-120">Mecanismo de autenticación</span><span class="sxs-lookup"><span data-stu-id="c1d89-120">Authentication mechanism</span></span> | <span data-ttu-id="c1d89-121">Descripción</span><span class="sxs-lookup"><span data-stu-id="c1d89-121">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="c1d89-122">`None` (valor predeterminado)</span><span class="sxs-lookup"><span data-stu-id="c1d89-122">`None` (default)</span></span>         | <span data-ttu-id="c1d89-123">Sin autenticación</span><span class="sxs-lookup"><span data-stu-id="c1d89-123">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="c1d89-124">Usuarios almacenados en la aplicación con ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="c1d89-124">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="c1d89-125">Usuarios almacenados en [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="c1d89-125">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="c1d89-126">Autenticación organizativa para un solo inquilino</span><span class="sxs-lookup"><span data-stu-id="c1d89-126">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="c1d89-127">Autenticación organizativa para varios inquilinos</span><span class="sxs-lookup"><span data-stu-id="c1d89-127">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="c1d89-128">Autenticación de Windows</span><span class="sxs-lookup"><span data-stu-id="c1d89-128">Windows Authentication</span></span> |

<span data-ttu-id="c1d89-129">Con la opción `-o|--output`, el comando usa el valor proporcionado para el marcador de posición `{APP NAME}` para:</span><span class="sxs-lookup"><span data-stu-id="c1d89-129">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="c1d89-130">Cree una carpeta para el proyecto.</span><span class="sxs-lookup"><span data-stu-id="c1d89-130">Create a folder for the project.</span></span>
* <span data-ttu-id="c1d89-131">Asigne el nombre al proyecto.</span><span class="sxs-lookup"><span data-stu-id="c1d89-131">Name the project.</span></span>

<span data-ttu-id="c1d89-132">Para más información, consulte el comando [dotnet new](/dotnet/core/tools/dotnet-new) de la guía de .NET Core.</span><span class="sxs-lookup"><span data-stu-id="c1d89-132">For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c1d89-133">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="c1d89-133">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="c1d89-134">Siga las instrucciones de Visual Studio para Mac del artículo <xref:blazor/get-started>.</span><span class="sxs-lookup"><span data-stu-id="c1d89-134">Follow the Visual Studio for Mac guidance in the <xref:blazor/get-started> article.</span></span>

1. <span data-ttu-id="c1d89-135">En el paso **Configure your new Blazor Server App** (Configurar la nueva aplicación de Blazor Server), seleccione **Individual Authentication (in-app)** (Autenticación individual [en aplicación]) en la lista desplegable **Authentication** (Autenticación).</span><span class="sxs-lookup"><span data-stu-id="c1d89-135">On the **Configure your new Blazor Server App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="c1d89-136">La aplicación se crea para usuarios individuales almacenados en la aplicación con ASP.NET Core Identity.</span><span class="sxs-lookup"><span data-stu-id="c1d89-136">The app is created for individual users stored in the app with ASP.NET Core Identity.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="c1d89-137">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="c1d89-137">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="c1d89-138">Siga las instrucciones de la CLI de .NET Core del artículo <xref:blazor/get-started> para crear un proyecto de Blazor Server con un mecanismo de autenticación:</span><span class="sxs-lookup"><span data-stu-id="c1d89-138">Follow the .NET Core CLI guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="c1d89-139">En la tabla siguiente se muestran los valores de autenticación permitidos (`{AUTHENTICATION}`).</span><span class="sxs-lookup"><span data-stu-id="c1d89-139">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="c1d89-140">Mecanismo de autenticación</span><span class="sxs-lookup"><span data-stu-id="c1d89-140">Authentication mechanism</span></span> | <span data-ttu-id="c1d89-141">Descripción</span><span class="sxs-lookup"><span data-stu-id="c1d89-141">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="c1d89-142">`None` (valor predeterminado)</span><span class="sxs-lookup"><span data-stu-id="c1d89-142">`None` (default)</span></span>         | <span data-ttu-id="c1d89-143">Sin autenticación</span><span class="sxs-lookup"><span data-stu-id="c1d89-143">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="c1d89-144">Usuarios almacenados en la aplicación con ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="c1d89-144">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="c1d89-145">Usuarios almacenados en [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="c1d89-145">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="c1d89-146">Autenticación organizativa para un solo inquilino</span><span class="sxs-lookup"><span data-stu-id="c1d89-146">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="c1d89-147">Autenticación organizativa para varios inquilinos</span><span class="sxs-lookup"><span data-stu-id="c1d89-147">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="c1d89-148">Autenticación de Windows</span><span class="sxs-lookup"><span data-stu-id="c1d89-148">Windows Authentication</span></span> |

<span data-ttu-id="c1d89-149">Con la opción `-o|--output`, el comando usa el valor proporcionado para el marcador de posición `{APP NAME}` para:</span><span class="sxs-lookup"><span data-stu-id="c1d89-149">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="c1d89-150">Cree una carpeta para el proyecto.</span><span class="sxs-lookup"><span data-stu-id="c1d89-150">Create a folder for the project.</span></span>
* <span data-ttu-id="c1d89-151">Asigne el nombre al proyecto.</span><span class="sxs-lookup"><span data-stu-id="c1d89-151">Name the project.</span></span>

<span data-ttu-id="c1d89-152">Para más información, consulte el comando [dotnet new](/dotnet/core/tools/dotnet-new) de la guía de .NET Core.</span><span class="sxs-lookup"><span data-stu-id="c1d89-152">For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

---

## <a name="secure-an-existing-app"></a><span data-ttu-id="c1d89-153">Protección de una aplicación existente</span><span class="sxs-lookup"><span data-stu-id="c1d89-153">Secure an existing app</span></span>

Blazor<span data-ttu-id="c1d89-154"> Las aplicaciones de servidor se configuran para que sean seguras de la misma forma que las aplicaciones de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c1d89-154"> Server apps are configured for security in the same manner as ASP.NET Core apps.</span></span> <span data-ttu-id="c1d89-155">Para obtener más información, vea los artículos en <xref:security/index>.</span><span class="sxs-lookup"><span data-stu-id="c1d89-155">For more information, see the articles under <xref:security/index>.</span></span>

## <a name="scaffold-identity"></a><span data-ttu-id="c1d89-156">Scaffolding para Identity</span><span class="sxs-lookup"><span data-stu-id="c1d89-156">Scaffold Identity</span></span>

<span data-ttu-id="c1d89-157">Scaffolding para Identity en un proyecto de Blazor Server:</span><span class="sxs-lookup"><span data-stu-id="c1d89-157">Scaffold Identity into a Blazor Server project:</span></span>

* <span data-ttu-id="c1d89-158">[Sin autorización existente](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization)</span><span class="sxs-lookup"><span data-stu-id="c1d89-158">[Without existing authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span></span>
* <span data-ttu-id="c1d89-159">[Con autorización](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization)</span><span class="sxs-lookup"><span data-stu-id="c1d89-159">[With authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span></span>
