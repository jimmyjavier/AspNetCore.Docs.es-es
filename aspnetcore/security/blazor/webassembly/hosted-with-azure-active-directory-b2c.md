---
title: Protección de una Blazor aplicación hospedada en webassembly ASP.NET Core con Azure Active Directory B2C
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/24/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: 05068853615a63611188175d95c27f1442973a86
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2020
ms.locfileid: "82768213"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a><span data-ttu-id="1a015-102">Protección de una Blazor aplicación hospedada en webassembly ASP.NET Core con Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="1a015-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C</span></span>

<span data-ttu-id="1a015-103">Por [Javier Calvarro Nelson](https://github.com/javiercn) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="1a015-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="1a015-104">En este artículo se describe cómo crear Blazor una aplicación independiente webassembly que usa [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) para la autenticación.</span><span class="sxs-lookup"><span data-stu-id="1a015-104">This article describes how to create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="1a015-105">Registrar aplicaciones en AAD B2C y crear una solución</span><span class="sxs-lookup"><span data-stu-id="1a015-105">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="1a015-106">Creación de un inquilino</span><span class="sxs-lookup"><span data-stu-id="1a015-106">Create a tenant</span></span>

<span data-ttu-id="1a015-107">Siga las instrucciones de [Tutorial: creación de un inquilino de Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant) para crear un inquilino de AAD B2C y registre la siguiente información:</span><span class="sxs-lookup"><span data-stu-id="1a015-107">Follow the guidance in [Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) to create an AAD B2C tenant and record the following information:</span></span>

* <span data-ttu-id="1a015-108">AAD B2C instancia (por ejemplo, `https://contoso.b2clogin.com/`, que incluye la barra diagonal final)</span><span class="sxs-lookup"><span data-stu-id="1a015-108">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span>
* <span data-ttu-id="1a015-109">AAD B2C dominio del inquilino (por ejemplo `contoso.onmicrosoft.com`,)</span><span class="sxs-lookup"><span data-stu-id="1a015-109">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="1a015-110">Registrar una aplicación de API de servidor</span><span class="sxs-lookup"><span data-stu-id="1a015-110">Register a server API app</span></span>

<span data-ttu-id="1a015-111">Siga las instrucciones de [Tutorial: registro de una aplicación en Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) para registrar una aplicación de AAD para la *aplicación de API de servidor* en el área de **Azure Active Directory** > **registros de aplicaciones** de la Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="1a015-111">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) to register an AAD app for the *Server API app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="1a015-112">Seleccione **Nuevo registro**.</span><span class="sxs-lookup"><span data-stu-id="1a015-112">Select **New registration**.</span></span>
1. <span data-ttu-id="1a015-113">Proporcione un **nombre** para la aplicación (por ejemplo, \*\* Blazor servidor AAD B2C\*\*).</span><span class="sxs-lookup"><span data-stu-id="1a015-113">Provide a **Name** for the app (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="1a015-114">En **tipos de cuenta compatibles**, seleccione **cuentas en cualquier directorio de la organización o cualquier proveedor de identidades. Para autenticar a los usuarios con Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="1a015-114">For **Supported account types**, select **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span> <span data-ttu-id="1a015-115">(multiinquilino) para esta experiencia.</span><span class="sxs-lookup"><span data-stu-id="1a015-115">(multi-tenant) for this experience.</span></span>
1. <span data-ttu-id="1a015-116">La *aplicación de API de servidor* no requiere un **URI de redirección** en este escenario, por lo que deje la lista desplegable establecida en **Web** y no escriba un URI de redirección.</span><span class="sxs-lookup"><span data-stu-id="1a015-116">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="1a015-117">Confirme que **permisos** > **conceder permisos de administrador a OpenID y offline_access** está habilitado.</span><span class="sxs-lookup"><span data-stu-id="1a015-117">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="1a015-118">Seleccione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="1a015-118">Select **Register**.</span></span>

<span data-ttu-id="1a015-119">En **exponer una API**:</span><span class="sxs-lookup"><span data-stu-id="1a015-119">In **Expose an API**:</span></span>

1. <span data-ttu-id="1a015-120">Seleccione **Agregar un ámbito**.</span><span class="sxs-lookup"><span data-stu-id="1a015-120">Select **Add a scope**.</span></span>
1. <span data-ttu-id="1a015-121">Selecciona **Guardar y continuar**.</span><span class="sxs-lookup"><span data-stu-id="1a015-121">Select **Save and continue**.</span></span>
1. <span data-ttu-id="1a015-122">Proporcione un **nombre de ámbito** (por ejemplo `API.Access`,).</span><span class="sxs-lookup"><span data-stu-id="1a015-122">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="1a015-123">Proporcione un **nombre para mostrar del consentimiento del administrador** ( `Access API`por ejemplo,).</span><span class="sxs-lookup"><span data-stu-id="1a015-123">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="1a015-124">Proporcione una **Descripción del consentimiento del administrador** (por `Allows the app to access server app API endpoints.`ejemplo,).</span><span class="sxs-lookup"><span data-stu-id="1a015-124">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="1a015-125">Confirme que el **Estado** está establecido en **habilitado**.</span><span class="sxs-lookup"><span data-stu-id="1a015-125">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="1a015-126">Seleccione la opción **Agregar un ámbito**.</span><span class="sxs-lookup"><span data-stu-id="1a015-126">Select **Add scope**.</span></span>

<span data-ttu-id="1a015-127">Registre la siguiente información:</span><span class="sxs-lookup"><span data-stu-id="1a015-127">Record the following information:</span></span>

* <span data-ttu-id="1a015-128">*Aplicación de API de servidor* IDENTIFICADOR de aplicación (ID. de cliente) ( `11111111-1111-1111-1111-111111111111`por ejemplo,)</span><span class="sxs-lookup"><span data-stu-id="1a015-128">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="1a015-129">URI de ID. de aplicación ( `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`por `api://11111111-1111-1111-1111-111111111111`ejemplo,, o el valor personalizado que proporcionó)</span><span class="sxs-lookup"><span data-stu-id="1a015-129">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="1a015-130">IDENTIFICADOR de directorio (identificador de inquilino) (por `222222222-2222-2222-2222-222222222222`ejemplo,)</span><span class="sxs-lookup"><span data-stu-id="1a015-130">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="1a015-131">*Aplicación de API de servidor* URI de ID. de aplicación ( `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`por ejemplo,, el Azure Portal puede tener como valor predeterminado el identificador de cliente)</span><span class="sxs-lookup"><span data-stu-id="1a015-131">*Server API app* App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, the Azure portal might default the value to the Client ID)</span></span>
* <span data-ttu-id="1a015-132">Ámbito predeterminado (por ejemplo, `API.Access`)</span><span class="sxs-lookup"><span data-stu-id="1a015-132">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="1a015-133">Registrar una aplicación de cliente</span><span class="sxs-lookup"><span data-stu-id="1a015-133">Register a client app</span></span>

<span data-ttu-id="1a015-134">Siga las instrucciones de [Tutorial: registro de una aplicación en Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) de nuevo para registrar una aplicación de AAD para la *aplicación cliente* en el área de **Azure Active Directory** > **registros de aplicaciones** de la Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="1a015-134">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *Client app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="1a015-135">Seleccione **Nuevo registro**.</span><span class="sxs-lookup"><span data-stu-id="1a015-135">Select **New registration**.</span></span>
1. <span data-ttu-id="1a015-136">Proporcione un **nombre** para la aplicación (por ejemplo, \*\* Blazor cliente AAD B2C\*\*).</span><span class="sxs-lookup"><span data-stu-id="1a015-136">Provide a **Name** for the app (for example, **Blazor Client AAD B2C**).</span></span>
1. <span data-ttu-id="1a015-137">En **tipos de cuenta compatibles**, seleccione **cuentas en cualquier directorio de la organización o cualquier proveedor de identidades. Para autenticar a los usuarios con Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="1a015-137">For **Supported account types**, select **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span> <span data-ttu-id="1a015-138">(multiinquilino) para esta experiencia.</span><span class="sxs-lookup"><span data-stu-id="1a015-138">(multi-tenant) for this experience.</span></span>
1. <span data-ttu-id="1a015-139">Deje la lista desplegable **URI de redirección** establecida en **Web**y proporcione un URI de redireccionamiento de `https://localhost:5001/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="1a015-139">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="1a015-140">Confirme que **permisos** > **conceder permisos de administrador a OpenID y offline_access** está habilitado.</span><span class="sxs-lookup"><span data-stu-id="1a015-140">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="1a015-141">Seleccione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="1a015-141">Select **Register**.</span></span>

<span data-ttu-id="1a015-142">En **Authentication** > **Platform configurations**configuración > de la plataforma de autenticación**Web**:</span><span class="sxs-lookup"><span data-stu-id="1a015-142">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="1a015-143">Confirme que el **URI de redirección** de `https://localhost:5001/authentication/login-callback` está presente.</span><span class="sxs-lookup"><span data-stu-id="1a015-143">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="1a015-144">En **concesión implícita**, active las casillas de verificación de **tokens de acceso** y **tokens de identificador**.</span><span class="sxs-lookup"><span data-stu-id="1a015-144">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="1a015-145">Los valores predeterminados restantes de la aplicación son aceptables para esta experiencia.</span><span class="sxs-lookup"><span data-stu-id="1a015-145">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="1a015-146">Seleccione el botón **Guardar**.</span><span class="sxs-lookup"><span data-stu-id="1a015-146">Select the **Save** button.</span></span>

<span data-ttu-id="1a015-147">En **permisos de API**:</span><span class="sxs-lookup"><span data-stu-id="1a015-147">In **API permissions**:</span></span>

1. <span data-ttu-id="1a015-148">Confirme que la aplicación tiene **Microsoft Graph** > permiso**User. Read** .</span><span class="sxs-lookup"><span data-stu-id="1a015-148">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="1a015-149">Seleccione **Agregar un permiso** seguido de **mis API**.</span><span class="sxs-lookup"><span data-stu-id="1a015-149">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="1a015-150">Seleccione la *aplicación de API de servidor* en la columna **nombre** (por ejemplo, \*\* Blazor servidor AAD B2C\*\*).</span><span class="sxs-lookup"><span data-stu-id="1a015-150">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="1a015-151">Abra la lista de **API** .</span><span class="sxs-lookup"><span data-stu-id="1a015-151">Open the **API** list.</span></span>
1. <span data-ttu-id="1a015-152">Habilite el acceso a la API (por ejemplo `API.Access`,).</span><span class="sxs-lookup"><span data-stu-id="1a015-152">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="1a015-153">Seleccione **Agregar permisos**.</span><span class="sxs-lookup"><span data-stu-id="1a015-153">Select **Add permissions**.</span></span>
1. <span data-ttu-id="1a015-154">Seleccione el botón **conceder contenido de administración para {nombre de inquilino}** .</span><span class="sxs-lookup"><span data-stu-id="1a015-154">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="1a015-155">Seleccione **Sí** para confirmar la acción.</span><span class="sxs-lookup"><span data-stu-id="1a015-155">Select **Yes** to confirm.</span></span>

<span data-ttu-id="1a015-156">En **Home** > **Azure ad B2C** > **flujos de usuario**:</span><span class="sxs-lookup"><span data-stu-id="1a015-156">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="1a015-157">Creación de un flujo de usuario de registro e inicio de sesión</span><span class="sxs-lookup"><span data-stu-id="1a015-157">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="1a015-158">Como mínimo, seleccione el atributo de usuario**nombre para mostrar** de **notificaciones** > de `context.User.Identity.Name` aplicación para rellenar el en el `LoginDisplay` componente (*Shared/LoginDisplay. Razor*).</span><span class="sxs-lookup"><span data-stu-id="1a015-158">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

<span data-ttu-id="1a015-159">Registre la siguiente información:</span><span class="sxs-lookup"><span data-stu-id="1a015-159">Record the following information:</span></span>

* <span data-ttu-id="1a015-160">Registre el identificador de aplicación de la aplicación *cliente* (identificador de cliente) `33333333-3333-3333-3333-333333333333`(por ejemplo,).</span><span class="sxs-lookup"><span data-stu-id="1a015-160">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>
* <span data-ttu-id="1a015-161">Registre el nombre del flujo de usuario de inicio de sesión y de registro creado para la aplicación (por `B2C_1_signupsignin`ejemplo,).</span><span class="sxs-lookup"><span data-stu-id="1a015-161">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="1a015-162">Creación de la aplicación</span><span class="sxs-lookup"><span data-stu-id="1a015-162">Create the app</span></span>

<span data-ttu-id="1a015-163">Reemplace los marcadores de posición en el siguiente comando por la información registrada anteriormente y ejecute el comando en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="1a015-163">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho -ssp "{SIGN UP OR SIGN IN POLICY}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="1a015-164">Para especificar la ubicación de salida, que crea una carpeta de proyecto si no existe, incluya la opción output en el comando con una ruta de acceso ( `-o BlazorSample`por ejemplo,).</span><span class="sxs-lookup"><span data-stu-id="1a015-164">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="1a015-165">El nombre de la carpeta también se convierte en parte del nombre del proyecto.</span><span class="sxs-lookup"><span data-stu-id="1a015-165">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="1a015-166">Pase el URI de ID. de `app-id-uri` aplicación a la opción, pero tenga en cuenta que es posible que se requiera un cambio de configuración en la aplicación cliente, que se describe en la sección [ámbitos de token de acceso](#access-token-scopes) .</span><span class="sxs-lookup"><span data-stu-id="1a015-166">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="1a015-167">Configuración de la aplicación de servidor</span><span class="sxs-lookup"><span data-stu-id="1a015-167">Server app configuration</span></span>

<span data-ttu-id="1a015-168">*Esta sección pertenece a la aplicación de **servidor** de la solución.*</span><span class="sxs-lookup"><span data-stu-id="1a015-168">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="1a015-169">Paquete de autenticación</span><span class="sxs-lookup"><span data-stu-id="1a015-169">Authentication package</span></span>

<span data-ttu-id="1a015-170">La compatibilidad para autenticar y autorizar llamadas a ASP.NET Core API Web la `Microsoft.AspNetCore.Authentication.AzureADB2C.UI`proporciona:</span><span class="sxs-lookup"><span data-stu-id="1a015-170">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the `Microsoft.AspNetCore.Authentication.AzureADB2C.UI`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
    Version="{VERSION}" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="1a015-171">Compatibilidad con el servicio de autenticación</span><span class="sxs-lookup"><span data-stu-id="1a015-171">Authentication service support</span></span>

<span data-ttu-id="1a015-172">El `AddAuthentication` método configura los servicios de autenticación dentro de la aplicación y configura el controlador de portador JWT como el método de autenticación predeterminado.</span><span class="sxs-lookup"><span data-stu-id="1a015-172">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="1a015-173">El `AddAzureADB2CBearer` método configura los parámetros específicos en el controlador de portador JWT necesario para validar los tokens emitidos por el Azure Active Directory B2C:</span><span class="sxs-lookup"><span data-stu-id="1a015-173">The `AddAzureADB2CBearer` method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory B2C:</span></span>

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

<span data-ttu-id="1a015-174">`UseAuthentication`y `UseAuthorization` Asegúrese de que:</span><span class="sxs-lookup"><span data-stu-id="1a015-174">`UseAuthentication` and `UseAuthorization` ensure that:</span></span>

* <span data-ttu-id="1a015-175">La aplicación intenta analizar y validar los tokens en las solicitudes entrantes.</span><span class="sxs-lookup"><span data-stu-id="1a015-175">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="1a015-176">Se produce un error en cualquier solicitud que intente obtener acceso a un recurso protegido sin credenciales adecuadas.</span><span class="sxs-lookup"><span data-stu-id="1a015-176">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="1a015-177">Usuario. Identity. Name</span><span class="sxs-lookup"><span data-stu-id="1a015-177">User.Identity.Name</span></span>

<span data-ttu-id="1a015-178">De forma predeterminada, `User.Identity.Name` no se rellena.</span><span class="sxs-lookup"><span data-stu-id="1a015-178">By default, the `User.Identity.Name` isn't populated.</span></span>

<span data-ttu-id="1a015-179">Para configurar la aplicación para recibir el valor del tipo `name` de notificaciones, configure el [TokenValidationParameters. NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> de `Startup.ConfigureServices`en:</span><span class="sxs-lookup"><span data-stu-id="1a015-179">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<JwtBearerOptions>(
    AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="1a015-180">Configuración de la aplicación</span><span class="sxs-lookup"><span data-stu-id="1a015-180">App settings</span></span>

<span data-ttu-id="1a015-181">El archivo *appSettings. JSON* contiene las opciones para configurar el controlador de portador JWT que se usa para validar los tokens de acceso.</span><span class="sxs-lookup"><span data-stu-id="1a015-181">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://{ORGANIZATION}.b2clogin.com/",
    "ClientId": "{SERVER API APP CLIENT ID}",
    "Domain": "{DOMAIN}",
    "SignUpSignInPolicyId": "{SIGN UP OR SIGN IN POLICY}"
  }
}
```

<span data-ttu-id="1a015-182">Ejemplo:</span><span class="sxs-lookup"><span data-stu-id="1a015-182">Example:</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://contoso.b2clogin.com/",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "Domain": "contoso.onmicrosoft.com",
    "SignUpSignInPolicyId": "B2C_1_signupsignin1",
  }
}
```

### <a name="weatherforecast-controller"></a><span data-ttu-id="1a015-183">Controlador de WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="1a015-183">WeatherForecast controller</span></span>

<span data-ttu-id="1a015-184">El controlador WeatherForecast (*Controllers/WeatherForecastController. CS*) expone una API protegida con el `[Authorize]` atributo que se aplica al controlador.</span><span class="sxs-lookup"><span data-stu-id="1a015-184">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the `[Authorize]` attribute applied to the controller.</span></span> <span data-ttu-id="1a015-185">Es **importante** comprender esto:</span><span class="sxs-lookup"><span data-stu-id="1a015-185">It's **important** to understand that:</span></span>

* <span data-ttu-id="1a015-186">El `[Authorize]` atributo de este controlador de API es lo único que protege esta API frente al acceso no autorizado.</span><span class="sxs-lookup"><span data-stu-id="1a015-186">The `[Authorize]` attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="1a015-187">El `[Authorize]` atributo que se usa Blazor en la aplicación webassembly solo sirve como una sugerencia a la aplicación a la que el usuario debe estar autorizado para que la aplicación funcione correctamente.</span><span class="sxs-lookup"><span data-stu-id="1a015-187">The `[Authorize]` attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="1a015-188">Configuración de la aplicación cliente</span><span class="sxs-lookup"><span data-stu-id="1a015-188">Client app configuration</span></span>

<span data-ttu-id="1a015-189">*Esta sección pertenece a la aplicación **cliente** de la solución.*</span><span class="sxs-lookup"><span data-stu-id="1a015-189">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="1a015-190">Paquete de autenticación</span><span class="sxs-lookup"><span data-stu-id="1a015-190">Authentication package</span></span>

<span data-ttu-id="1a015-191">Cuando se crea una aplicación para usar una cuenta de B2C individual`IndividualB2C`(), la aplicación recibe automáticamente una referencia de paquete para la biblioteca de`Microsoft.Authentication.WebAssembly.Msal`autenticación de [Microsoft](/azure/active-directory/develop/msal-overview) ().</span><span class="sxs-lookup"><span data-stu-id="1a015-191">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="1a015-192">El paquete proporciona un conjunto de primitivas que ayudan a la aplicación a autenticar a los usuarios y obtener tokens para llamar a las API protegidas.</span><span class="sxs-lookup"><span data-stu-id="1a015-192">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="1a015-193">Si agrega la autenticación a una aplicación, agregue manualmente el paquete al archivo de proyecto de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="1a015-193">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="1a015-194">Reemplace `{VERSION}` en la referencia de paquete anterior por la versión del `Microsoft.AspNetCore.Blazor.Templates` paquete que se muestra <xref:blazor/get-started> en el artículo.</span><span class="sxs-lookup"><span data-stu-id="1a015-194">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="1a015-195">El `Microsoft.Authentication.WebAssembly.Msal` paquete agrega de manera transitiva el `Microsoft.AspNetCore.Components.WebAssembly.Authentication` paquete a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1a015-195">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="1a015-196">Compatibilidad con el servicio de autenticación</span><span class="sxs-lookup"><span data-stu-id="1a015-196">Authentication service support</span></span>

<span data-ttu-id="1a015-197">Se ha `HttpClient` agregado compatibilidad con las instancias de que incluyen tokens de acceso al hacer solicitudes al proyecto de servidor.</span><span class="sxs-lookup"><span data-stu-id="1a015-197">Support for `HttpClient` instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="1a015-198">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="1a015-198">*Program.cs*:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="1a015-199">La compatibilidad con la autenticación de usuarios se registra en el contenedor de `AddMsalAuthentication` servicios con el método de `Microsoft.Authentication.WebAssembly.Msal` extensión proporcionado por el paquete.</span><span class="sxs-lookup"><span data-stu-id="1a015-199">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="1a015-200">Este método configura todos los servicios necesarios para que la aplicación interactúe con el Identity proveedor (IP).</span><span class="sxs-lookup"><span data-stu-id="1a015-200">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="1a015-201">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="1a015-201">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="1a015-202">El `AddMsalAuthentication` método acepta una devolución de llamada para configurar los parámetros necesarios para autenticar una aplicación.</span><span class="sxs-lookup"><span data-stu-id="1a015-202">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="1a015-203">Los valores necesarios para configurar la aplicación pueden obtenerse a partir de la configuración de AAD de Azure portal cuando se registra la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1a015-203">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="1a015-204">La configuración se proporciona mediante el archivo *wwwroot/appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="1a015-204">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

<span data-ttu-id="1a015-205">Ejemplo:</span><span class="sxs-lookup"><span data-stu-id="1a015-205">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": false
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="1a015-206">Ámbitos de token de acceso</span><span class="sxs-lookup"><span data-stu-id="1a015-206">Access token scopes</span></span>

<span data-ttu-id="1a015-207">Los ámbitos de token de acceso predeterminados representan la lista de ámbitos de token de acceso que son:</span><span class="sxs-lookup"><span data-stu-id="1a015-207">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="1a015-208">Se incluye de forma predeterminada en la solicitud de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="1a015-208">Included by default in the sign in request.</span></span>
* <span data-ttu-id="1a015-209">Se usa para aprovisionar un token de acceso inmediatamente después de la autenticación.</span><span class="sxs-lookup"><span data-stu-id="1a015-209">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="1a015-210">Todos los ámbitos deben pertenecer a la misma aplicación por reglas de Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="1a015-210">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="1a015-211">Se pueden agregar ámbitos adicionales para las aplicaciones de API adicionales según sea necesario:</span><span class="sxs-lookup"><span data-stu-id="1a015-211">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="1a015-212">Si el Azure Portal proporciona un URI de ámbito y **la aplicación produce una excepción no controlada** cuando recibe una respuesta *401 no autorizada* de la API, intente usar un URI de ámbito que no incluya el esquema y el host.</span><span class="sxs-lookup"><span data-stu-id="1a015-212">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="1a015-213">Por ejemplo, el Azure Portal puede proporcionar uno de los siguientes formatos de URI de ámbito:</span><span class="sxs-lookup"><span data-stu-id="1a015-213">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="1a015-214">Proporcione el URI del ámbito sin el esquema y el host:</span><span class="sxs-lookup"><span data-stu-id="1a015-214">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="1a015-215">Para obtener más información, consulte las siguientes secciones del artículo *escenarios adicionales* :</span><span class="sxs-lookup"><span data-stu-id="1a015-215">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="1a015-216">Solicitar tokens de acceso adicionales</span><span class="sxs-lookup"><span data-stu-id="1a015-216">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="1a015-217">Adjuntar tokens a solicitudes salientes</span><span class="sxs-lookup"><span data-stu-id="1a015-217">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)


### <a name="imports-file"></a><span data-ttu-id="1a015-218">Importar archivo</span><span class="sxs-lookup"><span data-stu-id="1a015-218">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="1a015-219">Página de índice</span><span class="sxs-lookup"><span data-stu-id="1a015-219">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="1a015-220">Componente de aplicación</span><span class="sxs-lookup"><span data-stu-id="1a015-220">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="1a015-221">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="1a015-221">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="1a015-222">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="1a015-222">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="1a015-223">Componente de autenticación</span><span class="sxs-lookup"><span data-stu-id="1a015-223">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="1a015-224">Componente FetchData</span><span class="sxs-lookup"><span data-stu-id="1a015-224">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="1a015-225">Ejecutar la aplicación</span><span class="sxs-lookup"><span data-stu-id="1a015-225">Run the app</span></span>

<span data-ttu-id="1a015-226">Ejecute la aplicación desde el proyecto de servidor.</span><span class="sxs-lookup"><span data-stu-id="1a015-226">Run the app from the Server project.</span></span> <span data-ttu-id="1a015-227">Al usar Visual Studio, seleccione el proyecto de servidor en **Explorador de soluciones** y seleccione el botón **Ejecutar** en la barra de herramientas o inicie la aplicación en el menú **depurar** .</span><span class="sxs-lookup"><span data-stu-id="1a015-227">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="1a015-228">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="1a015-228">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="1a015-229">Tutorial: Creación de un inquilino de Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="1a015-229">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="1a015-230">Documentación de la plataforma de identidad de Microsoft</span><span class="sxs-lookup"><span data-stu-id="1a015-230">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
