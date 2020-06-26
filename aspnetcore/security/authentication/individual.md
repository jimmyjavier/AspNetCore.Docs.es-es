---
title: Artículos basados en proyectos de ASP.NET Core creados con cuentas de usuario individuales
author: rick-anderson
description: Descubra artículos basados en ASP.NET Core proyectos creados con cuentas de usuario individuales.
ms.author: riande
ms.date: 12/11/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/individual
ms.openlocfilehash: 6d3743fc31c29bed5075ac29381aea51a64a908c
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/26/2020
ms.locfileid: "85406256"
---
# <a name="articles-based-on-aspnet-core-projects-created-with-individual-user-accounts"></a><span data-ttu-id="e9dd4-103">Artículos basados en proyectos de ASP.NET Core creados con cuentas de usuario individuales</span><span class="sxs-lookup"><span data-stu-id="e9dd4-103">Articles based on ASP.NET Core projects created with individual user accounts</span></span>

<span data-ttu-id="e9dd4-104">ASP.NET Core Identity se incluye en las plantillas de proyecto de Visual Studio con la opción "cuentas de usuario individuales".</span><span class="sxs-lookup"><span data-stu-id="e9dd4-104">ASP.NET Core Identity is included in project templates in Visual Studio with the "Individual User Accounts" option.</span></span>

<span data-ttu-id="e9dd4-105">Las plantillas de autenticación están disponibles en CLI de .NET Core con `-au Individual` :</span><span class="sxs-lookup"><span data-stu-id="e9dd4-105">The authentication templates are available in .NET Core CLI with `-au Individual`:</span></span>

::: moniker range=">= aspnetcore-2.1"

```dotnetcli
dotnet new mvc -au Individual
dotnet new webapp -au Individual
```

::: moniker-end

::: moniker range="= aspnetcore-2.0"

```dotnetcli
dotnet new mvc -au Individual
dotnet new razor -au Individual
```

::: moniker-end

<span data-ttu-id="e9dd4-106">Consulte [este problema de github para la](https://github.com/dotnet/AspNetCore/issues/5833) autenticación de API Web.</span><span class="sxs-lookup"><span data-stu-id="e9dd4-106">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5833) for web API authentication.</span></span>

<a name="no"></a>

## <a name="no-authentication"></a><span data-ttu-id="e9dd4-107">Sin autenticación</span><span class="sxs-lookup"><span data-stu-id="e9dd4-107">No Authentication</span></span>

<span data-ttu-id="e9dd4-108">La autenticación se especifica en el CLI de .NET Core con la `-au` opción.</span><span class="sxs-lookup"><span data-stu-id="e9dd4-108">Authentication is specified in the .NET Core CLI with the `-au` option.</span></span> <span data-ttu-id="e9dd4-109">En Visual Studio, el cuadro de diálogo **cambiar autenticación** está disponible para las nuevas aplicaciones Web.</span><span class="sxs-lookup"><span data-stu-id="e9dd4-109">In Visual Studio, the **Change Authentication** dialog is available for new web applications.</span></span> <span data-ttu-id="e9dd4-110">El valor predeterminado para las nuevas aplicaciones web en Visual Studio **no es autenticación**.</span><span class="sxs-lookup"><span data-stu-id="e9dd4-110">The default for new web apps in Visual Studio is **No Authentication**.</span></span>

<span data-ttu-id="e9dd4-111">Proyectos creados sin autenticación:</span><span class="sxs-lookup"><span data-stu-id="e9dd4-111">Projects created with no authentication:</span></span>

* <span data-ttu-id="e9dd4-112">No contenga páginas web y la interfaz de usuario para iniciar sesión y cerrar sesión.</span><span class="sxs-lookup"><span data-stu-id="e9dd4-112">Don't contain web pages and UI to sign in and sign out.</span></span>
* <span data-ttu-id="e9dd4-113">No contenga código de autenticación.</span><span class="sxs-lookup"><span data-stu-id="e9dd4-113">Don't contain authentication code.</span></span>

<a name="win"></a>

## <a name="windows-authentication"></a><span data-ttu-id="e9dd4-114">Autenticación de Windows</span><span class="sxs-lookup"><span data-stu-id="e9dd4-114">Windows Authentication</span></span>

<span data-ttu-id="e9dd4-115">La autenticación de Windows se especifica para las nuevas aplicaciones web en el CLI de .NET Core con la `-au Windows` opción.</span><span class="sxs-lookup"><span data-stu-id="e9dd4-115">Windows Authentication is specified for new web apps in the .NET Core CLI with the `-au Windows` option.</span></span> <span data-ttu-id="e9dd4-116">En Visual Studio, el cuadro de diálogo **cambiar autenticación** proporciona las opciones de **autenticación de Windows** .</span><span class="sxs-lookup"><span data-stu-id="e9dd4-116">In Visual Studio, the **Change Authentication** dialog provides the **Windows Authentication** options.</span></span>

<span data-ttu-id="e9dd4-117">Si se selecciona autenticación de Windows, la aplicación se configura para usar el [módulo IIS de autenticación de Windows](xref:host-and-deploy/iis/modules).</span><span class="sxs-lookup"><span data-stu-id="e9dd4-117">If Windows Authentication is selected, the app is configured to use the [Windows Authentication IIS module](xref:host-and-deploy/iis/modules).</span></span> <span data-ttu-id="e9dd4-118">La autenticación de Windows está pensada para sitios web de la intranet.</span><span class="sxs-lookup"><span data-stu-id="e9dd4-118">Windows Authentication is intended for Intranet web sites.</span></span>

## <a name="dotnet-new-webapp-authentication-options"></a><span data-ttu-id="e9dd4-119">Opciones de autenticación de dotnet New webapp</span><span class="sxs-lookup"><span data-stu-id="e9dd4-119">dotnet new webapp authentication options</span></span>

<span data-ttu-id="e9dd4-120">En la tabla siguiente se muestran las opciones de autenticación disponibles para las nuevas aplicaciones web:</span><span class="sxs-lookup"><span data-stu-id="e9dd4-120">The following table shows the authentication options available for new web apps:</span></span>

| <span data-ttu-id="e9dd4-121">Opción</span><span class="sxs-lookup"><span data-stu-id="e9dd4-121">Option</span></span> | <span data-ttu-id="e9dd4-122">Tipo de autenticación</span><span class="sxs-lookup"><span data-stu-id="e9dd4-122">Type of authentication</span></span> | <span data-ttu-id="e9dd4-123">Vínculo para más información</span><span class="sxs-lookup"><span data-stu-id="e9dd4-123">Link for more information</span></span> |
 | ----------------- | ------------ | ---------- |
| <span data-ttu-id="e9dd4-124">None</span><span class="sxs-lookup"><span data-stu-id="e9dd4-124">None</span></span>            |  <span data-ttu-id="e9dd4-125">Sin autenticación</span><span class="sxs-lookup"><span data-stu-id="e9dd4-125">No authentication</span></span> | | 
| <span data-ttu-id="e9dd4-126">Individual</span><span class="sxs-lookup"><span data-stu-id="e9dd4-126">Individual</span></span>      |  <span data-ttu-id="e9dd4-127">Autenticación individual</span><span class="sxs-lookup"><span data-stu-id="e9dd4-127">Individual authentication</span></span> | <xref:security/authentication/identity>
| <span data-ttu-id="e9dd4-128">IndividualB2C</span><span class="sxs-lookup"><span data-stu-id="e9dd4-128">IndividualB2C</span></span>   |  <span data-ttu-id="e9dd4-129">Autenticación individual hospedada en la nube con Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="e9dd4-129">Cloud-hosted individual authentication with Azure AD B2C</span></span> | [<span data-ttu-id="e9dd4-130">Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="e9dd4-130">Azure AD B2C</span></span>](/azure/active-directory-b2c/) |
| <span data-ttu-id="e9dd4-131">SingleOrg</span><span class="sxs-lookup"><span data-stu-id="e9dd4-131">SingleOrg</span></span>       |  <span data-ttu-id="e9dd4-132">Autenticación organizativa para un solo inquilino</span><span class="sxs-lookup"><span data-stu-id="e9dd4-132">Organizational authentication for a single tenant</span></span> | [<span data-ttu-id="e9dd4-133">Azure AD</span><span class="sxs-lookup"><span data-stu-id="e9dd4-133">Azure AD</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| <span data-ttu-id="e9dd4-134">MultiOrg</span><span class="sxs-lookup"><span data-stu-id="e9dd4-134">MultiOrg</span></span>        |  <span data-ttu-id="e9dd4-135">Autenticación organizativa para varios inquilinos</span><span class="sxs-lookup"><span data-stu-id="e9dd4-135">Organizational authentication for multiple tenants</span></span> | [<span data-ttu-id="e9dd4-136">Azure AD</span><span class="sxs-lookup"><span data-stu-id="e9dd4-136">Azure AD</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| <span data-ttu-id="e9dd4-137">Windows</span><span class="sxs-lookup"><span data-stu-id="e9dd4-137">Windows</span></span>         |  <span data-ttu-id="e9dd4-138">Autenticación de Windows</span><span class="sxs-lookup"><span data-stu-id="e9dd4-138">Windows authentication</span></span> | [<span data-ttu-id="e9dd4-139">Autenticación de Windows</span><span class="sxs-lookup"><span data-stu-id="e9dd4-139">Windows Authentication</span></span>](xref:security/authentication/windowsauth)

## <a name="visual-studio-new-webapp-authentication-options"></a><span data-ttu-id="e9dd4-140">Opciones de autenticación nuevas de Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e9dd4-140">Visual Studio new webapp authentication options</span></span>

<span data-ttu-id="e9dd4-141">En la tabla siguiente se muestran las opciones de autenticación disponibles al crear una nueva aplicación web con Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="e9dd4-141">The following table shows the authentication options available when creating a new web app with Visual Studio:</span></span>

| <span data-ttu-id="e9dd4-142">Opción</span><span class="sxs-lookup"><span data-stu-id="e9dd4-142">Option</span></span> | <span data-ttu-id="e9dd4-143">Tipo de autenticación</span><span class="sxs-lookup"><span data-stu-id="e9dd4-143">Type of authentication</span></span> | <span data-ttu-id="e9dd4-144">Vínculo para más información</span><span class="sxs-lookup"><span data-stu-id="e9dd4-144">Link for more information</span></span> |
 | ----------------- | ------------ | ---------- |
| <span data-ttu-id="e9dd4-145">None</span><span class="sxs-lookup"><span data-stu-id="e9dd4-145">None</span></span>            |  <span data-ttu-id="e9dd4-146">Sin autenticación</span><span class="sxs-lookup"><span data-stu-id="e9dd4-146">No authentication</span></span> | | 
| <span data-ttu-id="e9dd4-147">Cuentas de usuario individuales/almacenar cuentas de usuario en la aplicación</span><span class="sxs-lookup"><span data-stu-id="e9dd4-147">Individual User Accounts / Store user accounts in-app</span></span> |  <span data-ttu-id="e9dd4-148">Autenticación individual</span><span class="sxs-lookup"><span data-stu-id="e9dd4-148">Individual authentication</span></span> | <xref:security/authentication/identity> |
| <span data-ttu-id="e9dd4-149">Cuentas de usuario individuales/conectarse a un almacén de usuario existente en la nube</span><span class="sxs-lookup"><span data-stu-id="e9dd4-149">Individual User Accounts / Connect to an existing user store in the cloud</span></span> |  <span data-ttu-id="e9dd4-150">Autenticación individual hospedada en la nube con Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="e9dd4-150">Cloud-hosted individual authentication with Azure AD B2C</span></span> | [<span data-ttu-id="e9dd4-151">Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="e9dd4-151">Azure AD B2C</span></span>](/azure/active-directory-b2c/) |
| <span data-ttu-id="e9dd4-152">Nube profesional o educativa/organización única</span><span class="sxs-lookup"><span data-stu-id="e9dd4-152">Work or School Cloud / Single Org</span></span>  |  <span data-ttu-id="e9dd4-153">Autenticación organizativa para un solo inquilino</span><span class="sxs-lookup"><span data-stu-id="e9dd4-153">Organizational authentication for a single tenant</span></span> | [<span data-ttu-id="e9dd4-154">Azure AD</span><span class="sxs-lookup"><span data-stu-id="e9dd4-154">Azure AD</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| <span data-ttu-id="e9dd4-155">Nube profesional o educativa/varias organizaciones</span><span class="sxs-lookup"><span data-stu-id="e9dd4-155">Work or School Cloud / Multiple Org</span></span> |  <span data-ttu-id="e9dd4-156">Autenticación organizativa para varios inquilinos</span><span class="sxs-lookup"><span data-stu-id="e9dd4-156">Organizational authentication for multiple tenants</span></span> | [<span data-ttu-id="e9dd4-157">Azure AD</span><span class="sxs-lookup"><span data-stu-id="e9dd4-157">Azure AD</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| <span data-ttu-id="e9dd4-158">Windows</span><span class="sxs-lookup"><span data-stu-id="e9dd4-158">Windows</span></span>         |  <span data-ttu-id="e9dd4-159">Autenticación de Windows</span><span class="sxs-lookup"><span data-stu-id="e9dd4-159">Windows authentication</span></span> | [<span data-ttu-id="e9dd4-160">Autenticación de Windows</span><span class="sxs-lookup"><span data-stu-id="e9dd4-160">Windows Authentication</span></span>](xref:security/authentication/windowsauth)

## <a name="additional-resources"></a><span data-ttu-id="e9dd4-161">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="e9dd4-161">Additional resources</span></span>

<span data-ttu-id="e9dd4-162">En los artículos siguientes se muestra cómo usar el código generado en ASP.NET Core plantillas que utilizan cuentas de usuario individuales:</span><span class="sxs-lookup"><span data-stu-id="e9dd4-162">The following articles show how to use the code generated in ASP.NET Core templates that use individual user accounts:</span></span>

* [<span data-ttu-id="e9dd4-163">Confirmación de las cuentas y recuperación de contraseñas en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e9dd4-163">Account confirmation and password recovery in ASP.NET Core</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="e9dd4-164">Creación de una aplicación ASP.NET Core con datos de usuario protegidos por autorización</span><span class="sxs-lookup"><span data-stu-id="e9dd4-164">Create an ASP.NET Core app with user data protected by authorization</span></span>](xref:security/authorization/secure-data)
