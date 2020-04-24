---
title: Protección de una Blazor aplicación hospedada en webassembly ASP.NET Core con Azure Active Directory
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: 8c24546da50607d692a9cdc9f9c007d6ac8645ad
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/24/2020
ms.locfileid: "82110933"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-azure-active-directory"></a><span data-ttu-id="3a72c-102">Protección de una Blazor aplicación hospedada en webassembly ASP.NET Core con Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="3a72c-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory</span></span>

<span data-ttu-id="3a72c-103">Por [Javier Calvarro Nelson](https://github.com/javiercn) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="3a72c-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

> [!NOTE]
> <span data-ttu-id="3a72c-104">Las instrucciones de este artículo se aplican a ASP.NET Core 3,2 Preview 4.</span><span class="sxs-lookup"><span data-stu-id="3a72c-104">The guidance in this article applies to ASP.NET Core 3.2 Preview 4.</span></span> <span data-ttu-id="3a72c-105">Este tema se actualizará a la versión preliminar 5 del viernes, 24 de abril.</span><span class="sxs-lookup"><span data-stu-id="3a72c-105">This topic will be updated to cover Preview 5 on Friday, April 24.</span></span>

<span data-ttu-id="3a72c-106">En este artículo se describe cómo crear una [ Blazor aplicación hospedada en webassembly](xref:blazor/hosting-models#blazor-webassembly) que usa [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) para la autenticación.</span><span class="sxs-lookup"><span data-stu-id="3a72c-106">This article describes how to create a [Blazor WebAssembly hosted app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="3a72c-107">Registrar aplicaciones en AAD B2C y crear una solución</span><span class="sxs-lookup"><span data-stu-id="3a72c-107">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="3a72c-108">Creación de un inquilino</span><span class="sxs-lookup"><span data-stu-id="3a72c-108">Create a tenant</span></span>

<span data-ttu-id="3a72c-109">Siga las instrucciones de [Inicio rápido: configuración de un inquilino](/azure/active-directory/develop/quickstart-create-new-tenant) para crear un inquilino en AAD.</span><span class="sxs-lookup"><span data-stu-id="3a72c-109">Follow the guidance in [Quickstart: Set up a tenant](/azure/active-directory/develop/quickstart-create-new-tenant) to create a tenant in AAD.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="3a72c-110">Registrar una aplicación de API de servidor</span><span class="sxs-lookup"><span data-stu-id="3a72c-110">Register a server API app</span></span>

<span data-ttu-id="3a72c-111">Siga las instrucciones de [Inicio rápido: registro de una aplicación con la plataforma de Microsoft Identity y los](/azure/active-directory/develop/quickstart-register-app) temas de Azure AAD subsiguientes para registrar una aplicación de AAD para la *aplicación de API de servidor* en el área de**registros de aplicaciones** de **Azure Active Directory** > del Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="3a72c-111">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register an AAD app for the *Server API app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="3a72c-112">Seleccione **Nuevo registro**.</span><span class="sxs-lookup"><span data-stu-id="3a72c-112">Select **New registration**.</span></span>
1. <span data-ttu-id="3a72c-113">Proporcione un **nombre** para la aplicación (por ejemplo, \*\* Blazor servidor AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="3a72c-113">Provide a **Name** for the app (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="3a72c-114">Elija un **tipo de cuenta compatible**.</span><span class="sxs-lookup"><span data-stu-id="3a72c-114">Choose a **Supported account types**.</span></span> <span data-ttu-id="3a72c-115">Solo puede seleccionar **cuentas en este directorio de la organización** (un solo inquilino) para esta experiencia.</span><span class="sxs-lookup"><span data-stu-id="3a72c-115">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="3a72c-116">La *aplicación de API de servidor* no requiere un **URI de redirección** en este escenario, por lo que deje la lista desplegable establecida en **Web** y no escriba un URI de redirección.</span><span class="sxs-lookup"><span data-stu-id="3a72c-116">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="3a72c-117">Deshabilite **Permissions** > la casilla**conceder permisos Grant admin to OpenID y offline_access permisos** .</span><span class="sxs-lookup"><span data-stu-id="3a72c-117">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="3a72c-118">Seleccione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="3a72c-118">Select **Register**.</span></span>

<span data-ttu-id="3a72c-119">En **permisos**de la API, quite el **Microsoft Graph** > **usuario.** permiso de lectura, ya que la aplicación no requiere el inicio de sesión o el acceso de Perfil de UER.</span><span class="sxs-lookup"><span data-stu-id="3a72c-119">In **API permissions**, remove the **Microsoft Graph** > **User.Read** permission, as the app doesn't require sign in or uer profile access.</span></span>

<span data-ttu-id="3a72c-120">En **exponer una API**:</span><span class="sxs-lookup"><span data-stu-id="3a72c-120">In **Expose an API**:</span></span>

1. <span data-ttu-id="3a72c-121">Seleccione **Agregar un ámbito**.</span><span class="sxs-lookup"><span data-stu-id="3a72c-121">Select **Add a scope**.</span></span>
1. <span data-ttu-id="3a72c-122">Selecciona **Guardar y continuar**.</span><span class="sxs-lookup"><span data-stu-id="3a72c-122">Select **Save and continue**.</span></span>
1. <span data-ttu-id="3a72c-123">Proporcione un **nombre de ámbito** (por ejemplo `API.Access`,).</span><span class="sxs-lookup"><span data-stu-id="3a72c-123">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="3a72c-124">Proporcione un **nombre para mostrar del consentimiento del administrador** ( `Access API`por ejemplo,).</span><span class="sxs-lookup"><span data-stu-id="3a72c-124">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="3a72c-125">Proporcione una **Descripción del consentimiento del administrador** (por `Allows the app to access server app API endpoints.`ejemplo,).</span><span class="sxs-lookup"><span data-stu-id="3a72c-125">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="3a72c-126">Confirme que el **Estado** está establecido en **habilitado**.</span><span class="sxs-lookup"><span data-stu-id="3a72c-126">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="3a72c-127">Seleccione la opción **Agregar un ámbito**.</span><span class="sxs-lookup"><span data-stu-id="3a72c-127">Select **Add scope**.</span></span>

<span data-ttu-id="3a72c-128">Registre la siguiente información:</span><span class="sxs-lookup"><span data-stu-id="3a72c-128">Record the following information:</span></span>

* <span data-ttu-id="3a72c-129">*Aplicación de API de servidor* IDENTIFICADOR de aplicación (ID. de cliente) ( `11111111-1111-1111-1111-111111111111`por ejemplo,)</span><span class="sxs-lookup"><span data-stu-id="3a72c-129">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="3a72c-130">URI de ID. de aplicación ( `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`por `api://11111111-1111-1111-1111-111111111111`ejemplo,, o el valor personalizado que proporcionó)</span><span class="sxs-lookup"><span data-stu-id="3a72c-130">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="3a72c-131">IDENTIFICADOR de directorio (identificador de inquilino) (por `222222222-2222-2222-2222-222222222222`ejemplo,)</span><span class="sxs-lookup"><span data-stu-id="3a72c-131">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="3a72c-132">Dominio del inquilino de AAD (por `contoso.onmicrosoft.com`ejemplo,)</span><span class="sxs-lookup"><span data-stu-id="3a72c-132">AAD Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>
* <span data-ttu-id="3a72c-133">Ámbito predeterminado (por ejemplo, `API.Access`)</span><span class="sxs-lookup"><span data-stu-id="3a72c-133">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="3a72c-134">Registrar una aplicación de cliente</span><span class="sxs-lookup"><span data-stu-id="3a72c-134">Register a client app</span></span>

<span data-ttu-id="3a72c-135">Siga las instrucciones de [Inicio rápido: registro de una aplicación con la plataforma de Microsoft Identity y los](/azure/active-directory/develop/quickstart-register-app) temas de Azure AAD subsiguientes para registrar una aplicación de AAD para la *aplicación cliente* en el área de**registros de aplicaciones** de **Azure Active Directory** > del Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="3a72c-135">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register a AAD app for the *Client app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="3a72c-136">Seleccione **Nuevo registro**.</span><span class="sxs-lookup"><span data-stu-id="3a72c-136">Select **New registration**.</span></span>
1. <span data-ttu-id="3a72c-137">Proporcione un **nombre** para la aplicación (por ejemplo, \*\* Blazor cliente AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="3a72c-137">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="3a72c-138">Elija un **tipo de cuenta compatible**.</span><span class="sxs-lookup"><span data-stu-id="3a72c-138">Choose a **Supported account types**.</span></span> <span data-ttu-id="3a72c-139">Solo puede seleccionar **cuentas en este directorio de la organización** (un solo inquilino) para esta experiencia.</span><span class="sxs-lookup"><span data-stu-id="3a72c-139">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="3a72c-140">Deje la lista desplegable **URI de redirección** establecida en **Web**y proporcione un URI de redireccionamiento de `https://localhost:5001/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="3a72c-140">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="3a72c-141">Deshabilite **Permissions** > la casilla**conceder permisos Grant admin to OpenID y offline_access permisos** .</span><span class="sxs-lookup"><span data-stu-id="3a72c-141">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="3a72c-142">Seleccione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="3a72c-142">Select **Register**.</span></span>

<span data-ttu-id="3a72c-143">En **Authentication** > **Platform configurations**configuración > de la plataforma de autenticación**Web**:</span><span class="sxs-lookup"><span data-stu-id="3a72c-143">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="3a72c-144">Confirme que el **URI de redirección** de `https://localhost:5001/authentication/login-callback` está presente.</span><span class="sxs-lookup"><span data-stu-id="3a72c-144">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="3a72c-145">En **concesión implícita**, active las casillas de verificación de **tokens de acceso** y **tokens de identificador**.</span><span class="sxs-lookup"><span data-stu-id="3a72c-145">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="3a72c-146">Los valores predeterminados restantes de la aplicación son aceptables para esta experiencia.</span><span class="sxs-lookup"><span data-stu-id="3a72c-146">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="3a72c-147">Seleccione el botón **Guardar**.</span><span class="sxs-lookup"><span data-stu-id="3a72c-147">Select the **Save** button.</span></span>

<span data-ttu-id="3a72c-148">En **permisos de API**:</span><span class="sxs-lookup"><span data-stu-id="3a72c-148">In **API permissions**:</span></span>

1. <span data-ttu-id="3a72c-149">Confirme que la aplicación tiene **Microsoft Graph** > permiso**User. Read** .</span><span class="sxs-lookup"><span data-stu-id="3a72c-149">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="3a72c-150">Seleccione **Agregar un permiso** seguido de **mis API**.</span><span class="sxs-lookup"><span data-stu-id="3a72c-150">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="3a72c-151">Seleccione la *aplicación de API de servidor* en la columna **nombre** (por ejemplo, \*\* Blazor servidor AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="3a72c-151">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="3a72c-152">Abra la lista de **API** .</span><span class="sxs-lookup"><span data-stu-id="3a72c-152">Open the **API** list.</span></span>
1. <span data-ttu-id="3a72c-153">Habilite el acceso a la API (por ejemplo `API.Access`,).</span><span class="sxs-lookup"><span data-stu-id="3a72c-153">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="3a72c-154">Seleccione **Agregar permisos**.</span><span class="sxs-lookup"><span data-stu-id="3a72c-154">Select **Add permissions**.</span></span>
1. <span data-ttu-id="3a72c-155">Seleccione el botón **conceder contenido de administración para {nombre de inquilino}** .</span><span class="sxs-lookup"><span data-stu-id="3a72c-155">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="3a72c-156">Seleccione **Sí** para confirmar la acción.</span><span class="sxs-lookup"><span data-stu-id="3a72c-156">Select **Yes** to confirm.</span></span>

<span data-ttu-id="3a72c-157">Registre el identificador de aplicación de la aplicación *cliente* (identificador de cliente) `33333333-3333-3333-3333-333333333333`(por ejemplo,).</span><span class="sxs-lookup"><span data-stu-id="3a72c-157">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="3a72c-158">Creación de la aplicación</span><span class="sxs-lookup"><span data-stu-id="3a72c-158">Create the app</span></span>

<span data-ttu-id="3a72c-159">Reemplace los marcadores de posición en el siguiente comando por la información registrada anteriormente y ejecute el comando en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="3a72c-159">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho --tenant-id "{TENANT ID}"
```

<span data-ttu-id="3a72c-160">Para especificar la ubicación de salida, que crea una carpeta de proyecto si no existe, incluya la opción output en el comando con una ruta de acceso ( `-o BlazorSample`por ejemplo,).</span><span class="sxs-lookup"><span data-stu-id="3a72c-160">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="3a72c-161">El nombre de la carpeta también se convierte en parte del nombre del proyecto.</span><span class="sxs-lookup"><span data-stu-id="3a72c-161">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="3a72c-162">Pase el URI de ID. de `app-id-uri` aplicación a la opción, pero tenga en cuenta que es posible que se requiera un cambio de configuración en la aplicación cliente, que se describe en la sección [ámbitos de token de acceso](#access-token-scopes) .</span><span class="sxs-lookup"><span data-stu-id="3a72c-162">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="3a72c-163">Configuración de la aplicación de servidor</span><span class="sxs-lookup"><span data-stu-id="3a72c-163">Server app configuration</span></span>

<span data-ttu-id="3a72c-164">*Esta sección pertenece a la aplicación de **servidor** de la solución.*</span><span class="sxs-lookup"><span data-stu-id="3a72c-164">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="3a72c-165">Paquete de autenticación</span><span class="sxs-lookup"><span data-stu-id="3a72c-165">Authentication package</span></span>

<span data-ttu-id="3a72c-166">La compatibilidad para autenticar y autorizar llamadas a ASP.NET Core API Web la `Microsoft.AspNetCore.Authentication.AzureAD.UI`proporciona:</span><span class="sxs-lookup"><span data-stu-id="3a72c-166">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the `Microsoft.AspNetCore.Authentication.AzureAD.UI`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
    Version="3.1.0" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="3a72c-167">Compatibilidad con el servicio de autenticación</span><span class="sxs-lookup"><span data-stu-id="3a72c-167">Authentication service support</span></span>

<span data-ttu-id="3a72c-168">El `AddAuthentication` método configura los servicios de autenticación dentro de la aplicación y configura el controlador de portador JWT como el método de autenticación predeterminado.</span><span class="sxs-lookup"><span data-stu-id="3a72c-168">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="3a72c-169">El `AddAzureADBearer` método configura los parámetros específicos en el controlador de portador JWT necesario para validar los tokens emitidos por el Azure Active Directory:</span><span class="sxs-lookup"><span data-stu-id="3a72c-169">The `AddAzureADBearer` method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

<span data-ttu-id="3a72c-170">`UseAuthentication`y `UseAuthorization` Asegúrese de que:</span><span class="sxs-lookup"><span data-stu-id="3a72c-170">`UseAuthentication` and `UseAuthorization` ensure that:</span></span>

* <span data-ttu-id="3a72c-171">La aplicación intenta analizar y validar los tokens en las solicitudes entrantes.</span><span class="sxs-lookup"><span data-stu-id="3a72c-171">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="3a72c-172">Se produce un error en cualquier solicitud que intente obtener acceso a un recurso protegido sin credenciales adecuadas.</span><span class="sxs-lookup"><span data-stu-id="3a72c-172">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="3a72c-173">User.Identity.Name</span><span class="sxs-lookup"><span data-stu-id="3a72c-173">User.Identity.Name</span></span>

<span data-ttu-id="3a72c-174">De forma predeterminada, la API de la aplicación `User.Identity.Name` de servidor se rellena con `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` el valor del tipo de notificaciones (por ejemplo, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span><span class="sxs-lookup"><span data-stu-id="3a72c-174">By default, the Server app API populates `User.Identity.Name` with the value from the `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` claim type (for example, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="3a72c-175">Para configurar la aplicación para recibir el valor del tipo `name` de notificaciones, configure el [TokenValidationParameters. NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> de `Startup.ConfigureServices`en:</span><span class="sxs-lookup"><span data-stu-id="3a72c-175">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="3a72c-176">Configuración de la aplicación</span><span class="sxs-lookup"><span data-stu-id="3a72c-176">App settings</span></span>

<span data-ttu-id="3a72c-177">El archivo *appSettings. JSON* contiene las opciones para configurar el controlador de portador JWT que se usa para validar los tokens de acceso.</span><span class="sxs-lookup"><span data-stu-id="3a72c-177">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{API CLIENT ID}",
  }
}
```

### <a name="weatherforecast-controller"></a><span data-ttu-id="3a72c-178">Controlador de WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="3a72c-178">WeatherForecast controller</span></span>

<span data-ttu-id="3a72c-179">El controlador WeatherForecast (*Controllers/WeatherForecastController. CS*) expone una API protegida con el `[Authorize]` atributo que se aplica al controlador.</span><span class="sxs-lookup"><span data-stu-id="3a72c-179">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the `[Authorize]` attribute applied to the controller.</span></span> <span data-ttu-id="3a72c-180">Es **importante** comprender esto:</span><span class="sxs-lookup"><span data-stu-id="3a72c-180">It's **important** to understand that:</span></span>

* <span data-ttu-id="3a72c-181">El `[Authorize]` atributo de este controlador de API es lo único que protege esta API frente al acceso no autorizado.</span><span class="sxs-lookup"><span data-stu-id="3a72c-181">The `[Authorize]` attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="3a72c-182">El `[Authorize]` atributo que se usa Blazor en la aplicación webassembly solo sirve como una sugerencia a la aplicación a la que el usuario debe estar autorizado para que la aplicación funcione correctamente.</span><span class="sxs-lookup"><span data-stu-id="3a72c-182">The `[Authorize]` attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="3a72c-183">Configuración de la aplicación cliente</span><span class="sxs-lookup"><span data-stu-id="3a72c-183">Client app configuration</span></span>

<span data-ttu-id="3a72c-184">*Esta sección pertenece a la aplicación **cliente** de la solución.*</span><span class="sxs-lookup"><span data-stu-id="3a72c-184">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="3a72c-185">Paquete de autenticación</span><span class="sxs-lookup"><span data-stu-id="3a72c-185">Authentication package</span></span>

<span data-ttu-id="3a72c-186">Cuando se crea una aplicación para usar cuentas profesionales o educativas (`SingleOrg`), la aplicación recibe automáticamente una referencia de paquete para la biblioteca de autenticación`Microsoft.Authentication.WebAssembly.Msal`de [Microsoft](/azure/active-directory/develop/msal-overview) ().</span><span class="sxs-lookup"><span data-stu-id="3a72c-186">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="3a72c-187">El paquete proporciona un conjunto de primitivas que ayudan a la aplicación a autenticar a los usuarios y obtener tokens para llamar a las API protegidas.</span><span class="sxs-lookup"><span data-stu-id="3a72c-187">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="3a72c-188">Si agrega la autenticación a una aplicación, agregue manualmente el paquete al archivo de proyecto de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="3a72c-188">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="3a72c-189">Reemplace `{VERSION}` en la referencia de paquete anterior por la versión del `Microsoft.AspNetCore.Blazor.Templates` paquete que se muestra <xref:blazor/get-started> en el artículo.</span><span class="sxs-lookup"><span data-stu-id="3a72c-189">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="3a72c-190">El `Microsoft.Authentication.WebAssembly.Msal` paquete agrega de manera transitiva el `Microsoft.AspNetCore.Components.WebAssembly.Authentication` paquete a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="3a72c-190">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="3a72c-191">Compatibilidad con el servicio de autenticación</span><span class="sxs-lookup"><span data-stu-id="3a72c-191">Authentication service support</span></span>

<span data-ttu-id="3a72c-192">La compatibilidad con la autenticación de usuarios se registra en el contenedor de `AddMsalAuthentication` servicios con el método de `Microsoft.Authentication.WebAssembly.Msal` extensión proporcionado por el paquete.</span><span class="sxs-lookup"><span data-stu-id="3a72c-192">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="3a72c-193">Este método configura todos los servicios necesarios para que la aplicación interactúe con el proveedor de identidades (IP).</span><span class="sxs-lookup"><span data-stu-id="3a72c-193">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="3a72c-194">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="3a72c-194">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "https://login.microsoftonline.com/{TENANT ID}";
    authentication.ClientId = "{CLIENT ID}";
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="3a72c-195">El `AddMsalAuthentication` método acepta una devolución de llamada para configurar los parámetros necesarios para autenticar una aplicación.</span><span class="sxs-lookup"><span data-stu-id="3a72c-195">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="3a72c-196">Los valores necesarios para configurar la aplicación pueden obtenerse a partir de la configuración de AAD de Azure portal cuando se registra la aplicación.</span><span class="sxs-lookup"><span data-stu-id="3a72c-196">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

### <a name="access-token-scopes"></a><span data-ttu-id="3a72c-197">Ámbitos de token de acceso</span><span class="sxs-lookup"><span data-stu-id="3a72c-197">Access token scopes</span></span>

<span data-ttu-id="3a72c-198">Los ámbitos de token de acceso predeterminados representan la lista de ámbitos de token de acceso que son:</span><span class="sxs-lookup"><span data-stu-id="3a72c-198">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="3a72c-199">Se incluye de forma predeterminada en la solicitud de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="3a72c-199">Included by default in the sign in request.</span></span>
* <span data-ttu-id="3a72c-200">Se usa para aprovisionar un token de acceso inmediatamente después de la autenticación.</span><span class="sxs-lookup"><span data-stu-id="3a72c-200">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="3a72c-201">Todos los ámbitos deben pertenecer a la misma aplicación por reglas de Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="3a72c-201">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="3a72c-202">Se pueden agregar ámbitos adicionales para las aplicaciones de API adicionales según sea necesario:</span><span class="sxs-lookup"><span data-stu-id="3a72c-202">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="3a72c-203">Si el Azure Portal proporciona un URI de ámbito y **la aplicación produce una excepción no controlada** cuando recibe una respuesta *401 no autorizada* de la API, intente usar un URI de ámbito que no incluya el esquema y el host.</span><span class="sxs-lookup"><span data-stu-id="3a72c-203">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="3a72c-204">Por ejemplo, el Azure Portal puede proporcionar uno de los siguientes formatos de URI de ámbito:</span><span class="sxs-lookup"><span data-stu-id="3a72c-204">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="3a72c-205">Proporcione el URI del ámbito sin el esquema y el host:</span><span class="sxs-lookup"><span data-stu-id="3a72c-205">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="3a72c-206">Para obtener más información, vea <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span><span class="sxs-lookup"><span data-stu-id="3a72c-206">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

<!--
    For more information, see <xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests>.
-->

### <a name="imports-file"></a><span data-ttu-id="3a72c-207">Importar archivo</span><span class="sxs-lookup"><span data-stu-id="3a72c-207">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="3a72c-208">Página de índice</span><span class="sxs-lookup"><span data-stu-id="3a72c-208">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="3a72c-209">Componente de aplicación</span><span class="sxs-lookup"><span data-stu-id="3a72c-209">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="3a72c-210">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="3a72c-210">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="3a72c-211">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="3a72c-211">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="3a72c-212">Componente de autenticación</span><span class="sxs-lookup"><span data-stu-id="3a72c-212">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="3a72c-213">Componente FetchData</span><span class="sxs-lookup"><span data-stu-id="3a72c-213">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="3a72c-214">Ejecución la aplicación</span><span class="sxs-lookup"><span data-stu-id="3a72c-214">Run the app</span></span>

<span data-ttu-id="3a72c-215">Ejecute la aplicación desde el proyecto de servidor.</span><span class="sxs-lookup"><span data-stu-id="3a72c-215">Run the app from the Server project.</span></span> <span data-ttu-id="3a72c-216">Al usar Visual Studio, seleccione el proyecto de servidor en **Explorador de soluciones** y seleccione el botón **Ejecutar** en la barra de herramientas o inicie la aplicación en el menú **depurar** .</span><span class="sxs-lookup"><span data-stu-id="3a72c-216">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="3a72c-217">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="3a72c-217">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="3a72c-218">Documentación de la plataforma de identidad de Microsoft</span><span class="sxs-lookup"><span data-stu-id="3a72c-218">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
