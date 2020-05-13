---
title: Protección de una Blazor aplicación hospedada en Webassembly ASP.net Core con Azure Active Directory
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/11/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: 6ff95f0c5c925cbafef2b997a6cb23aeb15ff1aa
ms.sourcegitcommit: 1250c90c8d87c2513532be5683640b65bfdf9ddb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/12/2020
ms.locfileid: "83153962"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-azure-active-directory"></a><span data-ttu-id="c7232-102">Protección de una Blazor aplicación hospedada en Webassembly ASP.net Core con Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="c7232-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory</span></span>

<span data-ttu-id="c7232-103">Por [Javier Calvarro Nelson](https://github.com/javiercn) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="c7232-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="c7232-104">En este artículo se describe cómo crear una [ Blazor aplicación hospedada en webassembly](xref:blazor/hosting-models#blazor-webassembly) que usa [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) para la autenticación.</span><span class="sxs-lookup"><span data-stu-id="c7232-104">This article describes how to create a [Blazor WebAssembly hosted app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

## <a name="register-apps-in-aad-and-create-solution"></a><span data-ttu-id="c7232-105">Registro de aplicaciones en AAD y creación de una solución</span><span class="sxs-lookup"><span data-stu-id="c7232-105">Register apps in AAD and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="c7232-106">Creación de un inquilino</span><span class="sxs-lookup"><span data-stu-id="c7232-106">Create a tenant</span></span>

<span data-ttu-id="c7232-107">Siga las instrucciones de [Inicio rápido: configuración de un inquilino](/azure/active-directory/develop/quickstart-create-new-tenant) para crear un inquilino en AAD.</span><span class="sxs-lookup"><span data-stu-id="c7232-107">Follow the guidance in [Quickstart: Set up a tenant](/azure/active-directory/develop/quickstart-create-new-tenant) to create a tenant in AAD.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="c7232-108">Registrar una aplicación de API de servidor</span><span class="sxs-lookup"><span data-stu-id="c7232-108">Register a server API app</span></span>

<span data-ttu-id="c7232-109">Siga las instrucciones de [Inicio rápido: registro de una aplicación con la plataforma de Microsoft Identity y los](/azure/active-directory/develop/quickstart-register-app) temas de Azure AAD subsiguientes para registrar una aplicación de AAD para la *aplicación de API de servidor* en el área de **Azure Active Directory**  >  **registros de aplicaciones** de Azure Active Directory del Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="c7232-109">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register an AAD app for the *Server API app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="c7232-110">Seleccione **Nuevo registro**.</span><span class="sxs-lookup"><span data-stu-id="c7232-110">Select **New registration**.</span></span>
1. <span data-ttu-id="c7232-111">Proporcione un **nombre** para la aplicación (por ejemplo, \*\* Blazor servidor AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="c7232-111">Provide a **Name** for the app (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="c7232-112">Elija un **tipo de cuenta compatible**.</span><span class="sxs-lookup"><span data-stu-id="c7232-112">Choose a **Supported account types**.</span></span> <span data-ttu-id="c7232-113">Solo puede seleccionar **cuentas en este directorio de la organización** (un solo inquilino) para esta experiencia.</span><span class="sxs-lookup"><span data-stu-id="c7232-113">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="c7232-114">La *aplicación de API de servidor* no requiere un **URI de redirección** en este escenario, por lo que deje la lista desplegable establecida en **Web** y no escriba un URI de redirección.</span><span class="sxs-lookup"><span data-stu-id="c7232-114">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="c7232-115">Deshabilite **Permissions**la  >  casilla**conceder permisos Grant admin to OpenID y offline_access permisos** .</span><span class="sxs-lookup"><span data-stu-id="c7232-115">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="c7232-116">Seleccione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="c7232-116">Select **Register**.</span></span>

<span data-ttu-id="c7232-117">En **permisos**de la API, quite el **Microsoft Graph**  >  **usuario.** permiso de lectura, ya que la aplicación no requiere el inicio de sesión o el acceso de Perfil de UER.</span><span class="sxs-lookup"><span data-stu-id="c7232-117">In **API permissions**, remove the **Microsoft Graph** > **User.Read** permission, as the app doesn't require sign in or uer profile access.</span></span>

<span data-ttu-id="c7232-118">En **exponer una API**:</span><span class="sxs-lookup"><span data-stu-id="c7232-118">In **Expose an API**:</span></span>

1. <span data-ttu-id="c7232-119">Seleccione **Agregar un ámbito**.</span><span class="sxs-lookup"><span data-stu-id="c7232-119">Select **Add a scope**.</span></span>
1. <span data-ttu-id="c7232-120">Selecciona **Guardar y continuar**.</span><span class="sxs-lookup"><span data-stu-id="c7232-120">Select **Save and continue**.</span></span>
1. <span data-ttu-id="c7232-121">Proporcione un **nombre de ámbito** (por ejemplo, `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="c7232-121">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="c7232-122">Proporcione un **nombre para mostrar del consentimiento del administrador** (por ejemplo, `Access API` ).</span><span class="sxs-lookup"><span data-stu-id="c7232-122">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="c7232-123">Proporcione una **Descripción del consentimiento del administrador** (por ejemplo, `Allows the app to access server app API endpoints.` ).</span><span class="sxs-lookup"><span data-stu-id="c7232-123">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="c7232-124">Confirme que el **Estado** está establecido en **habilitado**.</span><span class="sxs-lookup"><span data-stu-id="c7232-124">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="c7232-125">Seleccione la opción **Agregar un ámbito**.</span><span class="sxs-lookup"><span data-stu-id="c7232-125">Select **Add scope**.</span></span>

<span data-ttu-id="c7232-126">Registre la siguiente información:</span><span class="sxs-lookup"><span data-stu-id="c7232-126">Record the following information:</span></span>

* <span data-ttu-id="c7232-127">*Aplicación de API de servidor* IDENTIFICADOR de aplicación (ID. de cliente) (por ejemplo, `11111111-1111-1111-1111-111111111111` )</span><span class="sxs-lookup"><span data-stu-id="c7232-127">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="c7232-128">URI de ID. de aplicación (por ejemplo, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` , `api://11111111-1111-1111-1111-111111111111` o el valor personalizado que proporcionó)</span><span class="sxs-lookup"><span data-stu-id="c7232-128">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="c7232-129">IDENTIFICADOR de directorio (identificador de inquilino) (por ejemplo, `222222222-2222-2222-2222-222222222222` )</span><span class="sxs-lookup"><span data-stu-id="c7232-129">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="c7232-130">Dominio del inquilino de AAD (por ejemplo, `contoso.onmicrosoft.com` )</span><span class="sxs-lookup"><span data-stu-id="c7232-130">AAD Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>
* <span data-ttu-id="c7232-131">Ámbito predeterminado (por ejemplo, `API.Access` )</span><span class="sxs-lookup"><span data-stu-id="c7232-131">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="c7232-132">Registrar una aplicación de cliente</span><span class="sxs-lookup"><span data-stu-id="c7232-132">Register a client app</span></span>

<span data-ttu-id="c7232-133">Siga las instrucciones de [Inicio rápido: registro de una aplicación con la plataforma de Microsoft Identity y los](/azure/active-directory/develop/quickstart-register-app) temas de Azure AAD subsiguientes para registrar una aplicación de AAD para la *aplicación cliente* en el **Azure Active Directory**  >  área de**registros de aplicaciones** de Azure Active Directory del Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="c7232-133">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register a AAD app for the *Client app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="c7232-134">Seleccione **Nuevo registro**.</span><span class="sxs-lookup"><span data-stu-id="c7232-134">Select **New registration**.</span></span>
1. <span data-ttu-id="c7232-135">Proporcione un **nombre** para la aplicación (por ejemplo, \*\* Blazor cliente AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="c7232-135">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="c7232-136">Elija un **tipo de cuenta compatible**.</span><span class="sxs-lookup"><span data-stu-id="c7232-136">Choose a **Supported account types**.</span></span> <span data-ttu-id="c7232-137">Solo puede seleccionar **cuentas en este directorio de la organización** (un solo inquilino) para esta experiencia.</span><span class="sxs-lookup"><span data-stu-id="c7232-137">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="c7232-138">Deje la lista desplegable **URI de redirección** establecida en **Web**y proporcione un URI de redireccionamiento de `https://localhost:5001/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="c7232-138">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="c7232-139">Deshabilite **Permissions**la  >  casilla**conceder permisos Grant admin to OpenID y offline_access permisos** .</span><span class="sxs-lookup"><span data-stu-id="c7232-139">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="c7232-140">Seleccione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="c7232-140">Select **Register**.</span></span>

<span data-ttu-id="c7232-141">En configuración de la plataforma de **autenticación**  >  **Platform configurations**  >  **Web**:</span><span class="sxs-lookup"><span data-stu-id="c7232-141">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="c7232-142">Confirme que el **URI de redirección** de `https://localhost:5001/authentication/login-callback` está presente.</span><span class="sxs-lookup"><span data-stu-id="c7232-142">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="c7232-143">En **concesión implícita**, active las casillas de verificación de **tokens de acceso** y **tokens de identificador**.</span><span class="sxs-lookup"><span data-stu-id="c7232-143">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="c7232-144">Los valores predeterminados restantes de la aplicación son aceptables para esta experiencia.</span><span class="sxs-lookup"><span data-stu-id="c7232-144">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="c7232-145">Seleccione el botón **Guardar**.</span><span class="sxs-lookup"><span data-stu-id="c7232-145">Select the **Save** button.</span></span>

<span data-ttu-id="c7232-146">En **permisos de API**:</span><span class="sxs-lookup"><span data-stu-id="c7232-146">In **API permissions**:</span></span>

1. <span data-ttu-id="c7232-147">Confirme que la aplicación tiene **Microsoft Graph**  >  permiso**User. Read** .</span><span class="sxs-lookup"><span data-stu-id="c7232-147">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="c7232-148">Seleccione **Agregar un permiso** seguido de **mis API**.</span><span class="sxs-lookup"><span data-stu-id="c7232-148">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="c7232-149">Seleccione la *aplicación de API de servidor* en la columna **nombre** (por ejemplo, \*\* Blazor servidor AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="c7232-149">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="c7232-150">Abra la lista de **API** .</span><span class="sxs-lookup"><span data-stu-id="c7232-150">Open the **API** list.</span></span>
1. <span data-ttu-id="c7232-151">Habilite el acceso a la API (por ejemplo, `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="c7232-151">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="c7232-152">Seleccione **Agregar permisos**.</span><span class="sxs-lookup"><span data-stu-id="c7232-152">Select **Add permissions**.</span></span>
1. <span data-ttu-id="c7232-153">Seleccione el botón **conceder contenido de administración para {nombre de inquilino}** .</span><span class="sxs-lookup"><span data-stu-id="c7232-153">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="c7232-154">Seleccione **Sí** para confirmar.</span><span class="sxs-lookup"><span data-stu-id="c7232-154">Select **Yes** to confirm.</span></span>

<span data-ttu-id="c7232-155">Registre el identificador de aplicación de la aplicación *cliente* (identificador de cliente) (por ejemplo, `33333333-3333-3333-3333-333333333333` ).</span><span class="sxs-lookup"><span data-stu-id="c7232-155">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="c7232-156">Creación de la aplicación</span><span class="sxs-lookup"><span data-stu-id="c7232-156">Create the app</span></span>

<span data-ttu-id="c7232-157">Reemplace los marcadores de posición en el siguiente comando por la información registrada anteriormente y ejecute el comando en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="c7232-157">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho --tenant-id "{TENANT ID}"
```

<span data-ttu-id="c7232-158">Para especificar la ubicación de salida, que crea una carpeta de proyecto si no existe, incluya la opción output en el comando con una ruta de acceso (por ejemplo, `-o BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="c7232-158">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="c7232-159">El nombre de la carpeta también se convierte en parte del nombre del proyecto.</span><span class="sxs-lookup"><span data-stu-id="c7232-159">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="c7232-160">Pase el URI de ID. de aplicación a la `app-id-uri` opción, pero tenga en cuenta que es posible que se requiera un cambio de configuración en la aplicación cliente, que se describe en la sección [ámbitos de token de acceso](#access-token-scopes) .</span><span class="sxs-lookup"><span data-stu-id="c7232-160">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="c7232-161">Configuración de la aplicación de servidor</span><span class="sxs-lookup"><span data-stu-id="c7232-161">Server app configuration</span></span>

<span data-ttu-id="c7232-162">*Esta sección pertenece a la aplicación de **servidor** de la solución.*</span><span class="sxs-lookup"><span data-stu-id="c7232-162">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="c7232-163">Paquete de autenticación</span><span class="sxs-lookup"><span data-stu-id="c7232-163">Authentication package</span></span>

<span data-ttu-id="c7232-164">La compatibilidad para autenticar y autorizar llamadas a ASP.NET Core API Web la proporciona `Microsoft.AspNetCore.Authentication.AzureAD.UI` :</span><span class="sxs-lookup"><span data-stu-id="c7232-164">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the `Microsoft.AspNetCore.Authentication.AzureAD.UI`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
    Version="{VERSION}" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="c7232-165">Compatibilidad con el servicio de autenticación</span><span class="sxs-lookup"><span data-stu-id="c7232-165">Authentication service support</span></span>

<span data-ttu-id="c7232-166">El `AddAuthentication` método configura los servicios de autenticación dentro de la aplicación y configura el controlador de portador JWT como el método de autenticación predeterminado.</span><span class="sxs-lookup"><span data-stu-id="c7232-166">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="c7232-167">El `AddAzureADBearer` método configura los parámetros específicos en el controlador de portador JWT necesario para validar los tokens emitidos por el Azure Active Directory:</span><span class="sxs-lookup"><span data-stu-id="c7232-167">The `AddAzureADBearer` method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

<span data-ttu-id="c7232-168">`UseAuthentication`y `UseAuthorization` Asegúrese de que:</span><span class="sxs-lookup"><span data-stu-id="c7232-168">`UseAuthentication` and `UseAuthorization` ensure that:</span></span>

* <span data-ttu-id="c7232-169">La aplicación intenta analizar y validar los tokens en las solicitudes entrantes.</span><span class="sxs-lookup"><span data-stu-id="c7232-169">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="c7232-170">Se produce un error en cualquier solicitud que intente obtener acceso a un recurso protegido sin credenciales adecuadas.</span><span class="sxs-lookup"><span data-stu-id="c7232-170">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="c7232-171">Usuario. Identity . Name</span><span class="sxs-lookup"><span data-stu-id="c7232-171">User.Identity.Name</span></span>

<span data-ttu-id="c7232-172">De forma predeterminada, la API de la aplicación de servidor se rellena `User.Identity.Name` con el valor del `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` tipo de notificaciones (por ejemplo, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com` ).</span><span class="sxs-lookup"><span data-stu-id="c7232-172">By default, the Server app API populates `User.Identity.Name` with the value from the `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` claim type (for example, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="c7232-173">Para configurar la aplicación para recibir el valor del `name` tipo de notificaciones, configure el [TokenValidationParameters. NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) de <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> en `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="c7232-173">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="c7232-174">Configuración de aplicaciones</span><span class="sxs-lookup"><span data-stu-id="c7232-174">App settings</span></span>

<span data-ttu-id="c7232-175">El archivo *appSettings. JSON* contiene las opciones para configurar el controlador de portador JWT que se usa para validar los tokens de acceso.</span><span class="sxs-lookup"><span data-stu-id="c7232-175">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{SERVER API APP CLIENT ID}",
  }
}
```

<span data-ttu-id="c7232-176">Ejemplo:</span><span class="sxs-lookup"><span data-stu-id="c7232-176">Example:</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "contoso.onmicrosoft.com",
    "TenantId": "e86c78e2-8bb4-4c41-aefd-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
  }
}
```

### <a name="weatherforecast-controller"></a><span data-ttu-id="c7232-177">Controlador de WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="c7232-177">WeatherForecast controller</span></span>

<span data-ttu-id="c7232-178">El controlador WeatherForecast (*Controllers/WeatherForecastController. CS*) expone una API protegida con el `[Authorize]` atributo que se aplica al controlador.</span><span class="sxs-lookup"><span data-stu-id="c7232-178">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the `[Authorize]` attribute applied to the controller.</span></span> <span data-ttu-id="c7232-179">Es **importante** comprender esto:</span><span class="sxs-lookup"><span data-stu-id="c7232-179">It's **important** to understand that:</span></span>

* <span data-ttu-id="c7232-180">El `[Authorize]` atributo de este controlador de API es lo único que protege esta API frente al acceso no autorizado.</span><span class="sxs-lookup"><span data-stu-id="c7232-180">The `[Authorize]` attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="c7232-181">El `[Authorize]` atributo que se usa en la Blazor aplicación webassembly solo sirve como una sugerencia a la aplicación a la que el usuario debe estar autorizado para que la aplicación funcione correctamente.</span><span class="sxs-lookup"><span data-stu-id="c7232-181">The `[Authorize]` attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="c7232-182">Configuración de la aplicación cliente</span><span class="sxs-lookup"><span data-stu-id="c7232-182">Client app configuration</span></span>

<span data-ttu-id="c7232-183">*Esta sección pertenece a la aplicación **cliente** de la solución.*</span><span class="sxs-lookup"><span data-stu-id="c7232-183">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="c7232-184">Paquete de autenticación</span><span class="sxs-lookup"><span data-stu-id="c7232-184">Authentication package</span></span>

<span data-ttu-id="c7232-185">Cuando se crea una aplicación para usar cuentas profesionales o educativas ( `SingleOrg` ), la aplicación recibe automáticamente una referencia de paquete para la [biblioteca de autenticación de Microsoft](/azure/active-directory/develop/msal-overview) ( `Microsoft.Authentication.WebAssembly.Msal` ).</span><span class="sxs-lookup"><span data-stu-id="c7232-185">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="c7232-186">El paquete proporciona un conjunto de primitivas que ayudan a la aplicación a autenticar a los usuarios y obtener tokens para llamar a las API protegidas.</span><span class="sxs-lookup"><span data-stu-id="c7232-186">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="c7232-187">Si agrega la autenticación a una aplicación, agregue manualmente el paquete al archivo de proyecto de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="c7232-187">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="c7232-188">Reemplace `{VERSION}` en la referencia de paquete anterior por la versión del `Microsoft.AspNetCore.Blazor.Templates` paquete que se muestra en el <xref:blazor/get-started> artículo.</span><span class="sxs-lookup"><span data-stu-id="c7232-188">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="c7232-189">El `Microsoft.Authentication.WebAssembly.Msal` paquete agrega de manera transitiva el `Microsoft.AspNetCore.Components.WebAssembly.Authentication` paquete a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="c7232-189">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="c7232-190">Compatibilidad con el servicio de autenticación</span><span class="sxs-lookup"><span data-stu-id="c7232-190">Authentication service support</span></span>

<span data-ttu-id="c7232-191">`HttpClient`Se ha agregado compatibilidad con las instancias de que incluyen tokens de acceso al hacer solicitudes al proyecto de servidor.</span><span class="sxs-lookup"><span data-stu-id="c7232-191">Support for `HttpClient` instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="c7232-192">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="c7232-192">*Program.cs*:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="c7232-193">La compatibilidad con la autenticación de usuarios se registra en el contenedor de servicios con el `AddMsalAuthentication` método de extensión proporcionado por el `Microsoft.Authentication.WebAssembly.Msal` paquete.</span><span class="sxs-lookup"><span data-stu-id="c7232-193">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="c7232-194">Este método configura todos los servicios necesarios para que la aplicación interactúe con el Identity proveedor (IP).</span><span class="sxs-lookup"><span data-stu-id="c7232-194">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="c7232-195">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="c7232-195">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="c7232-196">El `AddMsalAuthentication` método acepta una devolución de llamada para configurar los parámetros necesarios para autenticar una aplicación.</span><span class="sxs-lookup"><span data-stu-id="c7232-196">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="c7232-197">Los valores necesarios para configurar la aplicación pueden obtenerse a partir de la configuración de AAD de Azure portal cuando se registra la aplicación.</span><span class="sxs-lookup"><span data-stu-id="c7232-197">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="c7232-198">La configuración se proporciona mediante el archivo *wwwroot/appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="c7232-198">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="c7232-199">Ejemplo:</span><span class="sxs-lookup"><span data-stu-id="c7232-199">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": true
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="c7232-200">Ámbitos de token de acceso</span><span class="sxs-lookup"><span data-stu-id="c7232-200">Access token scopes</span></span>

<span data-ttu-id="c7232-201">Los ámbitos de token de acceso predeterminados representan la lista de ámbitos de token de acceso que son:</span><span class="sxs-lookup"><span data-stu-id="c7232-201">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="c7232-202">Se incluye de forma predeterminada en la solicitud de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="c7232-202">Included by default in the sign in request.</span></span>
* <span data-ttu-id="c7232-203">Se usa para aprovisionar un token de acceso inmediatamente después de la autenticación.</span><span class="sxs-lookup"><span data-stu-id="c7232-203">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="c7232-204">Todos los ámbitos deben pertenecer a la misma aplicación por reglas de Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="c7232-204">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="c7232-205">Se pueden agregar ámbitos adicionales para las aplicaciones de API adicionales según sea necesario:</span><span class="sxs-lookup"><span data-stu-id="c7232-205">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="c7232-206">Si el Azure Portal proporciona un URI de ámbito y **la aplicación produce una excepción no controlada** cuando recibe una respuesta *401 no autorizada* de la API, intente usar un URI de ámbito que no incluya el esquema y el host.</span><span class="sxs-lookup"><span data-stu-id="c7232-206">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="c7232-207">Por ejemplo, el Azure Portal puede proporcionar uno de los siguientes formatos de URI de ámbito:</span><span class="sxs-lookup"><span data-stu-id="c7232-207">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="c7232-208">Proporcione el URI del ámbito sin el esquema y el host:</span><span class="sxs-lookup"><span data-stu-id="c7232-208">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="c7232-209">Para obtener más información, consulte las siguientes secciones del artículo *escenarios adicionales* :</span><span class="sxs-lookup"><span data-stu-id="c7232-209">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="c7232-210">Solicitar tokens de acceso adicionales</span><span class="sxs-lookup"><span data-stu-id="c7232-210">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="c7232-211">Adjuntar tokens a solicitudes salientes</span><span class="sxs-lookup"><span data-stu-id="c7232-211">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)


### <a name="imports-file"></a><span data-ttu-id="c7232-212">Importar archivo</span><span class="sxs-lookup"><span data-stu-id="c7232-212">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="c7232-213">Página de índice</span><span class="sxs-lookup"><span data-stu-id="c7232-213">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="c7232-214">Componente de aplicación</span><span class="sxs-lookup"><span data-stu-id="c7232-214">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="c7232-215">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="c7232-215">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="c7232-216">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="c7232-216">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="c7232-217">Componente de autenticación</span><span class="sxs-lookup"><span data-stu-id="c7232-217">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="c7232-218">Componente FetchData</span><span class="sxs-lookup"><span data-stu-id="c7232-218">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="c7232-219">Ejecución la aplicación</span><span class="sxs-lookup"><span data-stu-id="c7232-219">Run the app</span></span>

<span data-ttu-id="c7232-220">Ejecute la aplicación desde el proyecto de servidor.</span><span class="sxs-lookup"><span data-stu-id="c7232-220">Run the app from the Server project.</span></span> <span data-ttu-id="c7232-221">Al usar Visual Studio, seleccione el proyecto de servidor en **Explorador de soluciones** y seleccione el botón **Ejecutar** en la barra de herramientas o inicie la aplicación en el menú **depurar** .</span><span class="sxs-lookup"><span data-stu-id="c7232-221">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="c7232-222">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="c7232-222">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="c7232-223">Solicitudes de API Web no autenticadas o no autorizadas en una aplicación con un cliente predeterminado seguro</span><span class="sxs-lookup"><span data-stu-id="c7232-223">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/blazor/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="c7232-224">Documentación de la plataforma de identidad de Microsoft</span><span class="sxs-lookup"><span data-stu-id="c7232-224">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
