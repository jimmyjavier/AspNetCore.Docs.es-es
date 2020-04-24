---
title: Protección de una Blazor aplicación hospedada en webassembly ASP.NET Core con Azure Active Directory B2C
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: 45ef1e6599777a38da7db753a8868028f3134f4b
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/24/2020
ms.locfileid: "82110985"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a><span data-ttu-id="5f9a6-102">Protección de una Blazor aplicación hospedada en webassembly ASP.NET Core con Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="5f9a6-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C</span></span>

<span data-ttu-id="5f9a6-103">Por [Javier Calvarro Nelson](https://github.com/javiercn) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="5f9a6-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

> [!NOTE]
> <span data-ttu-id="5f9a6-104">Las instrucciones de este artículo se aplican a ASP.NET Core 3,2 Preview 4.</span><span class="sxs-lookup"><span data-stu-id="5f9a6-104">The guidance in this article applies to ASP.NET Core 3.2 Preview 4.</span></span> <span data-ttu-id="5f9a6-105">Este tema se actualizará a la versión preliminar 5 del viernes, 24 de abril.</span><span class="sxs-lookup"><span data-stu-id="5f9a6-105">This topic will be updated to cover Preview 5 on Friday, April 24.</span></span>

<span data-ttu-id="5f9a6-106">En este artículo se describe cómo crear Blazor una aplicación independiente webassembly que usa [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) para la autenticación.</span><span class="sxs-lookup"><span data-stu-id="5f9a6-106">This article describes how to create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="5f9a6-107">Registrar aplicaciones en AAD B2C y crear una solución</span><span class="sxs-lookup"><span data-stu-id="5f9a6-107">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="5f9a6-108">Creación de un inquilino</span><span class="sxs-lookup"><span data-stu-id="5f9a6-108">Create a tenant</span></span>

<span data-ttu-id="5f9a6-109">Siga las instrucciones de [Tutorial: creación de un inquilino de Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant) para crear un inquilino de AAD B2C y registre la siguiente información:</span><span class="sxs-lookup"><span data-stu-id="5f9a6-109">Follow the guidance in [Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) to create an AAD B2C tenant and record the following information:</span></span>

* <span data-ttu-id="5f9a6-110">AAD B2C instancia (por ejemplo, `https://contoso.b2clogin.com/`, que incluye la barra diagonal final)</span><span class="sxs-lookup"><span data-stu-id="5f9a6-110">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span>
* <span data-ttu-id="5f9a6-111">AAD B2C dominio del inquilino (por ejemplo `contoso.onmicrosoft.com`,)</span><span class="sxs-lookup"><span data-stu-id="5f9a6-111">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="5f9a6-112">Registrar una aplicación de API de servidor</span><span class="sxs-lookup"><span data-stu-id="5f9a6-112">Register a server API app</span></span>

<span data-ttu-id="5f9a6-113">Siga las instrucciones de [Tutorial: registro de una aplicación en Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) para registrar una aplicación de AAD para la *aplicación de API de servidor* en el área de **Azure Active Directory** > **registros de aplicaciones** de la Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="5f9a6-113">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) to register an AAD app for the *Server API app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="5f9a6-114">Seleccione **Nuevo registro**.</span><span class="sxs-lookup"><span data-stu-id="5f9a6-114">Select **New registration**.</span></span>
1. <span data-ttu-id="5f9a6-115">Proporcione un **nombre** para la aplicación (por ejemplo, \*\* Blazor servidor AAD B2C\*\*).</span><span class="sxs-lookup"><span data-stu-id="5f9a6-115">Provide a **Name** for the app (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="5f9a6-116">En **tipos de cuenta compatibles**, seleccione **cuentas en cualquier directorio de la organización o cualquier proveedor de identidades. Para autenticar a los usuarios con Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="5f9a6-116">For **Supported account types**, select **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span> <span data-ttu-id="5f9a6-117">(multiinquilino) para esta experiencia.</span><span class="sxs-lookup"><span data-stu-id="5f9a6-117">(multi-tenant) for this experience.</span></span>
1. <span data-ttu-id="5f9a6-118">La *aplicación de API de servidor* no requiere un **URI de redirección** en este escenario, por lo que deje la lista desplegable establecida en **Web** y no escriba un URI de redirección.</span><span class="sxs-lookup"><span data-stu-id="5f9a6-118">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="5f9a6-119">Confirme que **permisos** > **conceder permisos de administrador a OpenID y offline_access** está habilitado.</span><span class="sxs-lookup"><span data-stu-id="5f9a6-119">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="5f9a6-120">Seleccione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="5f9a6-120">Select **Register**.</span></span>

<span data-ttu-id="5f9a6-121">En **exponer una API**:</span><span class="sxs-lookup"><span data-stu-id="5f9a6-121">In **Expose an API**:</span></span>

1. <span data-ttu-id="5f9a6-122">Seleccione **Agregar un ámbito**.</span><span class="sxs-lookup"><span data-stu-id="5f9a6-122">Select **Add a scope**.</span></span>
1. <span data-ttu-id="5f9a6-123">Selecciona **Guardar y continuar**.</span><span class="sxs-lookup"><span data-stu-id="5f9a6-123">Select **Save and continue**.</span></span>
1. <span data-ttu-id="5f9a6-124">Proporcione un **nombre de ámbito** (por ejemplo `API.Access`,).</span><span class="sxs-lookup"><span data-stu-id="5f9a6-124">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="5f9a6-125">Proporcione un **nombre para mostrar del consentimiento del administrador** ( `Access API`por ejemplo,).</span><span class="sxs-lookup"><span data-stu-id="5f9a6-125">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="5f9a6-126">Proporcione una **Descripción del consentimiento del administrador** (por `Allows the app to access server app API endpoints.`ejemplo,).</span><span class="sxs-lookup"><span data-stu-id="5f9a6-126">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="5f9a6-127">Confirme que el **Estado** está establecido en **habilitado**.</span><span class="sxs-lookup"><span data-stu-id="5f9a6-127">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="5f9a6-128">Seleccione la opción **Agregar un ámbito**.</span><span class="sxs-lookup"><span data-stu-id="5f9a6-128">Select **Add scope**.</span></span>

<span data-ttu-id="5f9a6-129">Registre la siguiente información:</span><span class="sxs-lookup"><span data-stu-id="5f9a6-129">Record the following information:</span></span>

* <span data-ttu-id="5f9a6-130">*Aplicación de API de servidor* IDENTIFICADOR de aplicación (ID. de cliente) ( `11111111-1111-1111-1111-111111111111`por ejemplo,)</span><span class="sxs-lookup"><span data-stu-id="5f9a6-130">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="5f9a6-131">URI de ID. de aplicación ( `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`por `api://11111111-1111-1111-1111-111111111111`ejemplo,, o el valor personalizado que proporcionó)</span><span class="sxs-lookup"><span data-stu-id="5f9a6-131">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="5f9a6-132">IDENTIFICADOR de directorio (identificador de inquilino) (por `222222222-2222-2222-2222-222222222222`ejemplo,)</span><span class="sxs-lookup"><span data-stu-id="5f9a6-132">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="5f9a6-133">*Aplicación de API de servidor* URI de ID. de aplicación ( `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`por ejemplo,, el Azure Portal puede tener como valor predeterminado el identificador de cliente)</span><span class="sxs-lookup"><span data-stu-id="5f9a6-133">*Server API app* App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, the Azure portal might default the value to the Client ID)</span></span>
* <span data-ttu-id="5f9a6-134">Ámbito predeterminado (por ejemplo, `API.Access`)</span><span class="sxs-lookup"><span data-stu-id="5f9a6-134">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="5f9a6-135">Registrar una aplicación de cliente</span><span class="sxs-lookup"><span data-stu-id="5f9a6-135">Register a client app</span></span>

<span data-ttu-id="5f9a6-136">Siga las instrucciones de [Tutorial: registro de una aplicación en Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) de nuevo para registrar una aplicación de AAD para la *aplicación cliente* en el área de **Azure Active Directory** > **registros de aplicaciones** de la Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="5f9a6-136">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *Client app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="5f9a6-137">Seleccione **Nuevo registro**.</span><span class="sxs-lookup"><span data-stu-id="5f9a6-137">Select **New registration**.</span></span>
1. <span data-ttu-id="5f9a6-138">Proporcione un **nombre** para la aplicación (por ejemplo, \*\* Blazor cliente AAD B2C\*\*).</span><span class="sxs-lookup"><span data-stu-id="5f9a6-138">Provide a **Name** for the app (for example, **Blazor Client AAD B2C**).</span></span>
1. <span data-ttu-id="5f9a6-139">En **tipos de cuenta compatibles**, seleccione **cuentas en cualquier directorio de la organización o cualquier proveedor de identidades. Para autenticar a los usuarios con Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="5f9a6-139">For **Supported account types**, select **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span> <span data-ttu-id="5f9a6-140">(multiinquilino) para esta experiencia.</span><span class="sxs-lookup"><span data-stu-id="5f9a6-140">(multi-tenant) for this experience.</span></span>
1. <span data-ttu-id="5f9a6-141">Deje la lista desplegable **URI de redirección** establecida en **Web**y proporcione un URI de redireccionamiento de `https://localhost:5001/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="5f9a6-141">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="5f9a6-142">Confirme que **permisos** > **conceder permisos de administrador a OpenID y offline_access** está habilitado.</span><span class="sxs-lookup"><span data-stu-id="5f9a6-142">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="5f9a6-143">Seleccione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="5f9a6-143">Select **Register**.</span></span>

<span data-ttu-id="5f9a6-144">En **Authentication** > **Platform configurations**configuración > de la plataforma de autenticación**Web**:</span><span class="sxs-lookup"><span data-stu-id="5f9a6-144">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="5f9a6-145">Confirme que el **URI de redirección** de `https://localhost:5001/authentication/login-callback` está presente.</span><span class="sxs-lookup"><span data-stu-id="5f9a6-145">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="5f9a6-146">En **concesión implícita**, active las casillas de verificación de **tokens de acceso** y **tokens de identificador**.</span><span class="sxs-lookup"><span data-stu-id="5f9a6-146">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="5f9a6-147">Los valores predeterminados restantes de la aplicación son aceptables para esta experiencia.</span><span class="sxs-lookup"><span data-stu-id="5f9a6-147">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="5f9a6-148">Seleccione el botón **Guardar**.</span><span class="sxs-lookup"><span data-stu-id="5f9a6-148">Select the **Save** button.</span></span>

<span data-ttu-id="5f9a6-149">En **permisos de API**:</span><span class="sxs-lookup"><span data-stu-id="5f9a6-149">In **API permissions**:</span></span>

1. <span data-ttu-id="5f9a6-150">Confirme que la aplicación tiene **Microsoft Graph** > permiso**User. Read** .</span><span class="sxs-lookup"><span data-stu-id="5f9a6-150">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="5f9a6-151">Seleccione **Agregar un permiso** seguido de **mis API**.</span><span class="sxs-lookup"><span data-stu-id="5f9a6-151">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="5f9a6-152">Seleccione la *aplicación de API de servidor* en la columna **nombre** (por ejemplo, \*\* Blazor servidor AAD B2C\*\*).</span><span class="sxs-lookup"><span data-stu-id="5f9a6-152">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="5f9a6-153">Abra la lista de **API** .</span><span class="sxs-lookup"><span data-stu-id="5f9a6-153">Open the **API** list.</span></span>
1. <span data-ttu-id="5f9a6-154">Habilite el acceso a la API (por ejemplo `API.Access`,).</span><span class="sxs-lookup"><span data-stu-id="5f9a6-154">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="5f9a6-155">Seleccione **Agregar permisos**.</span><span class="sxs-lookup"><span data-stu-id="5f9a6-155">Select **Add permissions**.</span></span>
1. <span data-ttu-id="5f9a6-156">Seleccione el botón **conceder contenido de administración para {nombre de inquilino}** .</span><span class="sxs-lookup"><span data-stu-id="5f9a6-156">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="5f9a6-157">Seleccione **Sí** para confirmar la acción.</span><span class="sxs-lookup"><span data-stu-id="5f9a6-157">Select **Yes** to confirm.</span></span>

<span data-ttu-id="5f9a6-158">En **Home** > **Azure ad B2C** > **flujos de usuario**:</span><span class="sxs-lookup"><span data-stu-id="5f9a6-158">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="5f9a6-159">Creación de un flujo de usuario de registro e inicio de sesión</span><span class="sxs-lookup"><span data-stu-id="5f9a6-159">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="5f9a6-160">Como mínimo, seleccione el atributo de usuario**nombre para mostrar** de **notificaciones** > de `context.User.Identity.Name` aplicación para rellenar el en el `LoginDisplay` componente (*Shared/LoginDisplay. Razor*).</span><span class="sxs-lookup"><span data-stu-id="5f9a6-160">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

<span data-ttu-id="5f9a6-161">Registre la siguiente información:</span><span class="sxs-lookup"><span data-stu-id="5f9a6-161">Record the following information:</span></span>

* <span data-ttu-id="5f9a6-162">Registre el identificador de aplicación de la aplicación *cliente* (identificador de cliente) `33333333-3333-3333-3333-333333333333`(por ejemplo,).</span><span class="sxs-lookup"><span data-stu-id="5f9a6-162">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>
* <span data-ttu-id="5f9a6-163">Registre el nombre del flujo de usuario de inicio de sesión y de registro creado para la aplicación (por `B2C_1_signupsignin`ejemplo,).</span><span class="sxs-lookup"><span data-stu-id="5f9a6-163">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="5f9a6-164">Creación de la aplicación</span><span class="sxs-lookup"><span data-stu-id="5f9a6-164">Create the app</span></span>

<span data-ttu-id="5f9a6-165">Reemplace los marcadores de posición en el siguiente comando por la información registrada anteriormente y ejecute el comando en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="5f9a6-165">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho -ssp "{SIGN UP OR SIGN IN POLICY}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="5f9a6-166">Para especificar la ubicación de salida, que crea una carpeta de proyecto si no existe, incluya la opción output en el comando con una ruta de acceso ( `-o BlazorSample`por ejemplo,).</span><span class="sxs-lookup"><span data-stu-id="5f9a6-166">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="5f9a6-167">El nombre de la carpeta también se convierte en parte del nombre del proyecto.</span><span class="sxs-lookup"><span data-stu-id="5f9a6-167">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="5f9a6-168">Pase el URI de ID. de `app-id-uri` aplicación a la opción, pero tenga en cuenta que es posible que se requiera un cambio de configuración en la aplicación cliente, que se describe en la sección [ámbitos de token de acceso](#access-token-scopes) .</span><span class="sxs-lookup"><span data-stu-id="5f9a6-168">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="5f9a6-169">Configuración de la aplicación de servidor</span><span class="sxs-lookup"><span data-stu-id="5f9a6-169">Server app configuration</span></span>

<span data-ttu-id="5f9a6-170">*Esta sección pertenece a la aplicación de **servidor** de la solución.*</span><span class="sxs-lookup"><span data-stu-id="5f9a6-170">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="5f9a6-171">Paquete de autenticación</span><span class="sxs-lookup"><span data-stu-id="5f9a6-171">Authentication package</span></span>

<span data-ttu-id="5f9a6-172">La compatibilidad para autenticar y autorizar llamadas a ASP.NET Core API Web la `Microsoft.AspNetCore.Authentication.AzureADB2C.UI`proporciona:</span><span class="sxs-lookup"><span data-stu-id="5f9a6-172">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the `Microsoft.AspNetCore.Authentication.AzureADB2C.UI`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
    Version="3.1.0" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="5f9a6-173">Compatibilidad con el servicio de autenticación</span><span class="sxs-lookup"><span data-stu-id="5f9a6-173">Authentication service support</span></span>

<span data-ttu-id="5f9a6-174">El `AddAuthentication` método configura los servicios de autenticación dentro de la aplicación y configura el controlador de portador JWT como el método de autenticación predeterminado.</span><span class="sxs-lookup"><span data-stu-id="5f9a6-174">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="5f9a6-175">El `AddAzureADB2CBearer` método configura los parámetros específicos en el controlador de portador JWT necesario para validar los tokens emitidos por el Azure Active Directory B2C:</span><span class="sxs-lookup"><span data-stu-id="5f9a6-175">The `AddAzureADB2CBearer` method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory B2C:</span></span>

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

<span data-ttu-id="5f9a6-176">`UseAuthentication`y `UseAuthorization` Asegúrese de que:</span><span class="sxs-lookup"><span data-stu-id="5f9a6-176">`UseAuthentication` and `UseAuthorization` ensure that:</span></span>

* <span data-ttu-id="5f9a6-177">La aplicación intenta analizar y validar los tokens en las solicitudes entrantes.</span><span class="sxs-lookup"><span data-stu-id="5f9a6-177">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="5f9a6-178">Se produce un error en cualquier solicitud que intente obtener acceso a un recurso protegido sin credenciales adecuadas.</span><span class="sxs-lookup"><span data-stu-id="5f9a6-178">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="5f9a6-179">User.Identity.Name</span><span class="sxs-lookup"><span data-stu-id="5f9a6-179">User.Identity.Name</span></span>

<span data-ttu-id="5f9a6-180">De forma predeterminada, `User.Identity.Name` no se rellena.</span><span class="sxs-lookup"><span data-stu-id="5f9a6-180">By default, the `User.Identity.Name` isn't populated.</span></span>

<span data-ttu-id="5f9a6-181">Para configurar la aplicación para recibir el valor del tipo `name` de notificaciones, configure el [TokenValidationParameters. NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> de `Startup.ConfigureServices`en:</span><span class="sxs-lookup"><span data-stu-id="5f9a6-181">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<JwtBearerOptions>(
    AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="5f9a6-182">Configuración de la aplicación</span><span class="sxs-lookup"><span data-stu-id="5f9a6-182">App settings</span></span>

<span data-ttu-id="5f9a6-183">El archivo *appSettings. JSON* contiene las opciones para configurar el controlador de portador JWT que se usa para validar los tokens de acceso.</span><span class="sxs-lookup"><span data-stu-id="5f9a6-183">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://{ORGANIZATION}.b2clogin.com/",
    "ClientId": "{API CLIENT ID}",
    "Domain": "{DOMAIN}",
    "SignUpSignInPolicyId": "{SIGN UP OR SIGN IN POLICY}"
  }
}
```

### <a name="weatherforecast-controller"></a><span data-ttu-id="5f9a6-184">Controlador de WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="5f9a6-184">WeatherForecast controller</span></span>

<span data-ttu-id="5f9a6-185">El controlador WeatherForecast (*Controllers/WeatherForecastController. CS*) expone una API protegida con el `[Authorize]` atributo que se aplica al controlador.</span><span class="sxs-lookup"><span data-stu-id="5f9a6-185">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the `[Authorize]` attribute applied to the controller.</span></span> <span data-ttu-id="5f9a6-186">Es **importante** comprender esto:</span><span class="sxs-lookup"><span data-stu-id="5f9a6-186">It's **important** to understand that:</span></span>

* <span data-ttu-id="5f9a6-187">El `[Authorize]` atributo de este controlador de API es lo único que protege esta API frente al acceso no autorizado.</span><span class="sxs-lookup"><span data-stu-id="5f9a6-187">The `[Authorize]` attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="5f9a6-188">El `[Authorize]` atributo que se usa Blazor en la aplicación webassembly solo sirve como una sugerencia a la aplicación a la que el usuario debe estar autorizado para que la aplicación funcione correctamente.</span><span class="sxs-lookup"><span data-stu-id="5f9a6-188">The `[Authorize]` attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

```csharp
[Authorize]
[ApiController]
[Route("[controller]")]
public class WeatherForecastController : ControllerBase
{
    [HttpGet]
    public IEnumerable<WeatherForecast> Get()
    {
        ...
    }
}
```

## <a name="client-app-configuration"></a><span data-ttu-id="5f9a6-189">Configuración de la aplicación cliente</span><span class="sxs-lookup"><span data-stu-id="5f9a6-189">Client app configuration</span></span>

<span data-ttu-id="5f9a6-190">*Esta sección pertenece a la aplicación **cliente** de la solución.*</span><span class="sxs-lookup"><span data-stu-id="5f9a6-190">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="5f9a6-191">Paquete de autenticación</span><span class="sxs-lookup"><span data-stu-id="5f9a6-191">Authentication package</span></span>

<span data-ttu-id="5f9a6-192">Cuando se crea una aplicación para usar una cuenta de B2C individual`IndividualB2C`(), la aplicación recibe automáticamente una referencia de paquete para la biblioteca de`Microsoft.Authentication.WebAssembly.Msal`autenticación de [Microsoft](/azure/active-directory/develop/msal-overview) ().</span><span class="sxs-lookup"><span data-stu-id="5f9a6-192">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="5f9a6-193">El paquete proporciona un conjunto de primitivas que ayudan a la aplicación a autenticar a los usuarios y obtener tokens para llamar a las API protegidas.</span><span class="sxs-lookup"><span data-stu-id="5f9a6-193">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="5f9a6-194">Si agrega la autenticación a una aplicación, agregue manualmente el paquete al archivo de proyecto de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="5f9a6-194">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="5f9a6-195">Reemplace `{VERSION}` en la referencia de paquete anterior por la versión del `Microsoft.AspNetCore.Blazor.Templates` paquete que se muestra <xref:blazor/get-started> en el artículo.</span><span class="sxs-lookup"><span data-stu-id="5f9a6-195">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="5f9a6-196">El `Microsoft.Authentication.WebAssembly.Msal` paquete agrega de manera transitiva el `Microsoft.AspNetCore.Components.WebAssembly.Authentication` paquete a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="5f9a6-196">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="5f9a6-197">Compatibilidad con el servicio de autenticación</span><span class="sxs-lookup"><span data-stu-id="5f9a6-197">Authentication service support</span></span>

<span data-ttu-id="5f9a6-198">La compatibilidad con la autenticación de usuarios se registra en el contenedor de `AddMsalAuthentication` servicios con el método de `Microsoft.Authentication.WebAssembly.Msal` extensión proporcionado por el paquete.</span><span class="sxs-lookup"><span data-stu-id="5f9a6-198">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="5f9a6-199">Este método configura todos los servicios necesarios para que la aplicación interactúe con el proveedor de identidades (IP).</span><span class="sxs-lookup"><span data-stu-id="5f9a6-199">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="5f9a6-200">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="5f9a6-200">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = 
        "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}";
    authentication.ClientId = "{CLIENT ID}";
    authentication.ValidateAuthority = false;
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="5f9a6-201">El `AddMsalAuthentication` método acepta una devolución de llamada para configurar los parámetros necesarios para autenticar una aplicación.</span><span class="sxs-lookup"><span data-stu-id="5f9a6-201">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="5f9a6-202">Los valores necesarios para configurar la aplicación pueden obtenerse a partir de la configuración de AAD de Azure portal cuando se registra la aplicación.</span><span class="sxs-lookup"><span data-stu-id="5f9a6-202">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

### <a name="access-token-scopes"></a><span data-ttu-id="5f9a6-203">Ámbitos de token de acceso</span><span class="sxs-lookup"><span data-stu-id="5f9a6-203">Access token scopes</span></span>

<span data-ttu-id="5f9a6-204">Los ámbitos de token de acceso predeterminados representan la lista de ámbitos de token de acceso que son:</span><span class="sxs-lookup"><span data-stu-id="5f9a6-204">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="5f9a6-205">Se incluye de forma predeterminada en la solicitud de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="5f9a6-205">Included by default in the sign in request.</span></span>
* <span data-ttu-id="5f9a6-206">Se usa para aprovisionar un token de acceso inmediatamente después de la autenticación.</span><span class="sxs-lookup"><span data-stu-id="5f9a6-206">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="5f9a6-207">Todos los ámbitos deben pertenecer a la misma aplicación por reglas de Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="5f9a6-207">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="5f9a6-208">Se pueden agregar ámbitos adicionales para las aplicaciones de API adicionales según sea necesario:</span><span class="sxs-lookup"><span data-stu-id="5f9a6-208">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="5f9a6-209">Si el Azure Portal proporciona un URI de ámbito y **la aplicación produce una excepción no controlada** cuando recibe una respuesta *401 no autorizada* de la API, intente usar un URI de ámbito que no incluya el esquema y el host.</span><span class="sxs-lookup"><span data-stu-id="5f9a6-209">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="5f9a6-210">Por ejemplo, el Azure Portal puede proporcionar uno de los siguientes formatos de URI de ámbito:</span><span class="sxs-lookup"><span data-stu-id="5f9a6-210">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="5f9a6-211">Proporcione el URI del ámbito sin el esquema y el host:</span><span class="sxs-lookup"><span data-stu-id="5f9a6-211">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="5f9a6-212">Para obtener más información, vea <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span><span class="sxs-lookup"><span data-stu-id="5f9a6-212">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

<!--
    For more information, see <xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests>.
-->

### <a name="imports-file"></a><span data-ttu-id="5f9a6-213">Importar archivo</span><span class="sxs-lookup"><span data-stu-id="5f9a6-213">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="5f9a6-214">Página de índice</span><span class="sxs-lookup"><span data-stu-id="5f9a6-214">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="5f9a6-215">Componente de aplicación</span><span class="sxs-lookup"><span data-stu-id="5f9a6-215">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="5f9a6-216">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="5f9a6-216">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="5f9a6-217">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="5f9a6-217">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="5f9a6-218">Componente de autenticación</span><span class="sxs-lookup"><span data-stu-id="5f9a6-218">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="5f9a6-219">Componente FetchData</span><span class="sxs-lookup"><span data-stu-id="5f9a6-219">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="5f9a6-220">Ejecución la aplicación</span><span class="sxs-lookup"><span data-stu-id="5f9a6-220">Run the app</span></span>

<span data-ttu-id="5f9a6-221">Ejecute la aplicación desde el proyecto de servidor.</span><span class="sxs-lookup"><span data-stu-id="5f9a6-221">Run the app from the Server project.</span></span> <span data-ttu-id="5f9a6-222">Al usar Visual Studio, seleccione el proyecto de servidor en **Explorador de soluciones** y seleccione el botón **Ejecutar** en la barra de herramientas o inicie la aplicación en el menú **depurar** .</span><span class="sxs-lookup"><span data-stu-id="5f9a6-222">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="5f9a6-223">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="5f9a6-223">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="5f9a6-224">Tutorial: Creación de un inquilino de Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="5f9a6-224">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="5f9a6-225">Documentación de la plataforma de identidad de Microsoft</span><span class="sxs-lookup"><span data-stu-id="5f9a6-225">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
