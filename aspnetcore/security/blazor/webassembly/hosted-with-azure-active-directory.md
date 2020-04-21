---
title: Proteja una Blazor aplicación hospedada de ASP.NET Core WebAssembly con Azure Active Directory
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: a80be8d145b7c58be35e2c353a448db7e234e20b
ms.sourcegitcommit: 5547d920f322e5a823575c031529e4755ab119de
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661825"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-azure-active-directory"></a><span data-ttu-id="3f8ba-102">Proteja una Blazor aplicación hospedada de ASP.NET Core WebAssembly con Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="3f8ba-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory</span></span>

<span data-ttu-id="3f8ba-103">Por [Javier Calvarro Nelson](https://github.com/javiercn) y Luke [Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="3f8ba-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="3f8ba-104">En este artículo se [ Blazor ](xref:blazor/hosting-models#blazor-webassembly) describe cómo crear una aplicación hospedada de WebAssembly que usa [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) para la autenticación.</span><span class="sxs-lookup"><span data-stu-id="3f8ba-104">This article describes how to create a [Blazor WebAssembly hosted app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="3f8ba-105">Registre aplicaciones en AAD B2C y cree una solución</span><span class="sxs-lookup"><span data-stu-id="3f8ba-105">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="3f8ba-106">Creación de un inquilino</span><span class="sxs-lookup"><span data-stu-id="3f8ba-106">Create a tenant</span></span>

<span data-ttu-id="3f8ba-107">Siga las instrucciones de [Inicio rápido: configure un inquilino](/azure/active-directory/develop/quickstart-create-new-tenant) para crear un inquilino en AAD.</span><span class="sxs-lookup"><span data-stu-id="3f8ba-107">Follow the guidance in [Quickstart: Set up a tenant](/azure/active-directory/develop/quickstart-create-new-tenant) to create a tenant in AAD.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="3f8ba-108">Registrar una aplicación de API de servidor</span><span class="sxs-lookup"><span data-stu-id="3f8ba-108">Register a server API app</span></span>

<span data-ttu-id="3f8ba-109">Siga las instrucciones de [Inicio rápido: registre una aplicación con la plataforma](/azure/active-directory/develop/quickstart-register-app) de identidad de Microsoft y los temas de Azure AAD posteriores para registrar una aplicación de AAD para la aplicación *API* de servidor en el área**Registros** de aplicaciones de **Azure Active Directory** > de Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="3f8ba-109">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register an AAD app for the *Server API app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="3f8ba-110">Seleccione **Nuevo registro**.</span><span class="sxs-lookup"><span data-stu-id="3f8ba-110">Select **New registration**.</span></span>
1. <span data-ttu-id="3f8ba-111">Proporcione un **nombre** para la aplicación (por ejemplo, \*\* Blazor AAD\*\*de servidor).</span><span class="sxs-lookup"><span data-stu-id="3f8ba-111">Provide a **Name** for the app (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="3f8ba-112">Elija un **tipo de cuenta admitida.**</span><span class="sxs-lookup"><span data-stu-id="3f8ba-112">Choose a **Supported account types**.</span></span> <span data-ttu-id="3f8ba-113">Puede seleccionar **Cuentas solo en este directorio de organización** (inquilino único) para esta experiencia.</span><span class="sxs-lookup"><span data-stu-id="3f8ba-113">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="3f8ba-114">La *aplicación API* de servidor no requiere un URI de **redirección** en este escenario, así que deje el menú desplegable establecido en **Web** y no escriba un URI de redirección.</span><span class="sxs-lookup"><span data-stu-id="3f8ba-114">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="3f8ba-115">Deshabilite la casilla de verificación **Permisos** > **de concesión de permisos para abrir y offline_access permisos.**</span><span class="sxs-lookup"><span data-stu-id="3f8ba-115">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="3f8ba-116">Seleccione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="3f8ba-116">Select **Register**.</span></span>

<span data-ttu-id="3f8ba-117">En **Permisos**de API , quite el permiso**User.Read** de **Microsoft Graph,** > ya que la aplicación no requiere acceso al inicio de sesión ni al perfil de uer.</span><span class="sxs-lookup"><span data-stu-id="3f8ba-117">In **API permissions**, remove the **Microsoft Graph** > **User.Read** permission, as the app doesn't require sign in or uer profile access.</span></span>

<span data-ttu-id="3f8ba-118">En **Exponer una API:**</span><span class="sxs-lookup"><span data-stu-id="3f8ba-118">In **Expose an API**:</span></span>

1. <span data-ttu-id="3f8ba-119">Seleccione **Agregar un ámbito**.</span><span class="sxs-lookup"><span data-stu-id="3f8ba-119">Select **Add a scope**.</span></span>
1. <span data-ttu-id="3f8ba-120">Seleccione **Guardar y continuar**.</span><span class="sxs-lookup"><span data-stu-id="3f8ba-120">Select **Save and continue**.</span></span>
1. <span data-ttu-id="3f8ba-121">Proporcione un **nombre de** `API.Access`ámbito (por ejemplo, ).</span><span class="sxs-lookup"><span data-stu-id="3f8ba-121">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="3f8ba-122">Proporcione un **nombre para mostrar** `Access API`del consentimiento de administrador (por ejemplo, ).</span><span class="sxs-lookup"><span data-stu-id="3f8ba-122">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="3f8ba-123">Proporcione una **descripción** del `Allows the app to access server app API endpoints.`consentimiento del administrador (por ejemplo, ).</span><span class="sxs-lookup"><span data-stu-id="3f8ba-123">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="3f8ba-124">Confirme que el **estado** está establecido en **Habilitado**.</span><span class="sxs-lookup"><span data-stu-id="3f8ba-124">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="3f8ba-125">Seleccione la opción **Agregar un ámbito**.</span><span class="sxs-lookup"><span data-stu-id="3f8ba-125">Select **Add scope**.</span></span>

<span data-ttu-id="3f8ba-126">Registre la siguiente información:</span><span class="sxs-lookup"><span data-stu-id="3f8ba-126">Record the following information:</span></span>

* <span data-ttu-id="3f8ba-127">*Aplicación API de servidor* ID de aplicación (ID de `11111111-1111-1111-1111-111111111111`cliente) (por ejemplo, )</span><span class="sxs-lookup"><span data-stu-id="3f8ba-127">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="3f8ba-128">URI de identificador de `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` `api://11111111-1111-1111-1111-111111111111`aplicación (por ejemplo, , , o el valor personalizado que proporcionó)</span><span class="sxs-lookup"><span data-stu-id="3f8ba-128">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="3f8ba-129">ID de directorio (ID de `222222222-2222-2222-2222-222222222222`inquilino) (por ejemplo, )</span><span class="sxs-lookup"><span data-stu-id="3f8ba-129">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="3f8ba-130">Dominio de inquilino de `contoso.onmicrosoft.com`AAD (por ejemplo, )</span><span class="sxs-lookup"><span data-stu-id="3f8ba-130">AAD Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>
* <span data-ttu-id="3f8ba-131">Alcance predeterminado (por `API.Access`ejemplo, )</span><span class="sxs-lookup"><span data-stu-id="3f8ba-131">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="3f8ba-132">Registrar una aplicación de cliente</span><span class="sxs-lookup"><span data-stu-id="3f8ba-132">Register a client app</span></span>

<span data-ttu-id="3f8ba-133">Siga las instrucciones de [Inicio rápido: registre una aplicación con la plataforma](/azure/active-directory/develop/quickstart-register-app) de identidad de Microsoft y los temas de Azure AAD posteriores para registrar una aplicación de AAD para la aplicación *cliente* en el área**Registros** de aplicaciones de **Azure Active Directory** > de Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="3f8ba-133">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register a AAD app for the *Client app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="3f8ba-134">Seleccione **Nuevo registro**.</span><span class="sxs-lookup"><span data-stu-id="3f8ba-134">Select **New registration**.</span></span>
1. <span data-ttu-id="3f8ba-135">Proporcione un **nombre** para la aplicación (por ejemplo, \*\* Blazor Client AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="3f8ba-135">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="3f8ba-136">Elija un **tipo de cuenta admitida.**</span><span class="sxs-lookup"><span data-stu-id="3f8ba-136">Choose a **Supported account types**.</span></span> <span data-ttu-id="3f8ba-137">Puede seleccionar **Cuentas solo en este directorio de organización** (inquilino único) para esta experiencia.</span><span class="sxs-lookup"><span data-stu-id="3f8ba-137">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="3f8ba-138">Deje el descenso desplegable URI de **redirección** establecido `https://localhost:5001/authentication/login-callback`en **Web**y proporcione un URI de redireccionamiento de .</span><span class="sxs-lookup"><span data-stu-id="3f8ba-138">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="3f8ba-139">Deshabilite la casilla de verificación **Permisos** > **de concesión de permisos para abrir y offline_access permisos.**</span><span class="sxs-lookup"><span data-stu-id="3f8ba-139">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="3f8ba-140">Seleccione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="3f8ba-140">Select **Register**.</span></span>

<span data-ttu-id="3f8ba-141">En la**Web\*\*\*\*de configuraciones** > de la plataforma de **autenticación:** > </span><span class="sxs-lookup"><span data-stu-id="3f8ba-141">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="3f8ba-142">Confirme que el `https://localhost:5001/authentication/login-callback` URI de **redireccionamiento** está presente.</span><span class="sxs-lookup"><span data-stu-id="3f8ba-142">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="3f8ba-143">En **Implicit grant**, active las casillas de los tokens de **acceso** y los tokens **de identificador.**</span><span class="sxs-lookup"><span data-stu-id="3f8ba-143">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="3f8ba-144">Los valores predeterminados restantes de la aplicación son aceptables para esta experiencia.</span><span class="sxs-lookup"><span data-stu-id="3f8ba-144">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="3f8ba-145">Seleccione el botón **Guardar**.</span><span class="sxs-lookup"><span data-stu-id="3f8ba-145">Select the **Save** button.</span></span>

<span data-ttu-id="3f8ba-146">En **Permisos de API:**</span><span class="sxs-lookup"><span data-stu-id="3f8ba-146">In **API permissions**:</span></span>

1. <span data-ttu-id="3f8ba-147">Confirme que la aplicación tiene el permiso **Microsoft Graph** > **User.Read.**</span><span class="sxs-lookup"><span data-stu-id="3f8ba-147">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="3f8ba-148">Seleccione **Agregar un permiso** seguido de Mis **API**.</span><span class="sxs-lookup"><span data-stu-id="3f8ba-148">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="3f8ba-149">Seleccione la *aplicación API* de servidor en la columna **Nombre** (por ejemplo, \*\* Blazor AAD de servidor).\*\*</span><span class="sxs-lookup"><span data-stu-id="3f8ba-149">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="3f8ba-150">Abra la lista **de API.**</span><span class="sxs-lookup"><span data-stu-id="3f8ba-150">Open the **API** list.</span></span>
1. <span data-ttu-id="3f8ba-151">Habilite el acceso a la `API.Access`API (por ejemplo, ).</span><span class="sxs-lookup"><span data-stu-id="3f8ba-151">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="3f8ba-152">Seleccione **Agregar permisos**.</span><span class="sxs-lookup"><span data-stu-id="3f8ba-152">Select **Add permissions**.</span></span>
1. <span data-ttu-id="3f8ba-153">Seleccione el botón Conceder contenido de administrador para el botón **"NOMBRE DEL INQUILINO".**</span><span class="sxs-lookup"><span data-stu-id="3f8ba-153">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="3f8ba-154">Seleccione **Sí** para confirmar la acción.</span><span class="sxs-lookup"><span data-stu-id="3f8ba-154">Select **Yes** to confirm.</span></span>

<span data-ttu-id="3f8ba-155">Registre el identificador de aplicación de *aplicación* `33333333-3333-3333-3333-333333333333`cliente (ID de cliente) (por ejemplo, ).</span><span class="sxs-lookup"><span data-stu-id="3f8ba-155">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="3f8ba-156">Creación de la aplicación</span><span class="sxs-lookup"><span data-stu-id="3f8ba-156">Create the app</span></span>

<span data-ttu-id="3f8ba-157">Reemplace los marcadores de posición del siguiente comando por la información registrada anteriormente y ejecute el comando en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="3f8ba-157">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho --tenant-id "{TENANT ID}"
```

<span data-ttu-id="3f8ba-158">Para especificar la ubicación de salida, que crea una carpeta de proyecto si no existe, `-o BlazorSample`incluya la opción de salida en el comando con una ruta de acceso (por ejemplo, ).</span><span class="sxs-lookup"><span data-stu-id="3f8ba-158">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="3f8ba-159">El nombre de la carpeta también pasa a formar parte del nombre del proyecto.</span><span class="sxs-lookup"><span data-stu-id="3f8ba-159">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="3f8ba-160">Pase el URI de `app-id-uri` identificador de aplicación a la opción, pero tenga en cuenta que puede ser necesario un cambio de configuración en la aplicación cliente, que se describe en la sección [Ámbitos](#access-token-scopes) de token de acceso.</span><span class="sxs-lookup"><span data-stu-id="3f8ba-160">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="3f8ba-161">Configuración de la aplicación de servidor</span><span class="sxs-lookup"><span data-stu-id="3f8ba-161">Server app configuration</span></span>

<span data-ttu-id="3f8ba-162">*Esta sección pertenece a la aplicación **de servidor** de la solución.*</span><span class="sxs-lookup"><span data-stu-id="3f8ba-162">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="3f8ba-163">Paquete de autenticación</span><span class="sxs-lookup"><span data-stu-id="3f8ba-163">Authentication package</span></span>

<span data-ttu-id="3f8ba-164">El soporte para autenticar y autorizar llamadas a ASP.NET API `Microsoft.AspNetCore.Authentication.AzureAD.UI`web principales es proporcionado por:</span><span class="sxs-lookup"><span data-stu-id="3f8ba-164">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the `Microsoft.AspNetCore.Authentication.AzureAD.UI`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
    Version="3.1.0" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="3f8ba-165">Soporte de servicio de autenticación</span><span class="sxs-lookup"><span data-stu-id="3f8ba-165">Authentication service support</span></span>

<span data-ttu-id="3f8ba-166">El `AddAuthentication` método configura los servicios de autenticación dentro de la aplicación y configura el controlador JWT Bearer como el método de autenticación predeterminado.</span><span class="sxs-lookup"><span data-stu-id="3f8ba-166">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="3f8ba-167">El `AddAzureADBearer` método configura los parámetros específicos en el controlador de JWT Bearer necesarios para validar los tokens emitidos por Azure Active Directory:</span><span class="sxs-lookup"><span data-stu-id="3f8ba-167">The `AddAzureADBearer` method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

<span data-ttu-id="3f8ba-168">`UseAuthentication`y `UseAuthorization` asegurar se que:</span><span class="sxs-lookup"><span data-stu-id="3f8ba-168">`UseAuthentication` and `UseAuthorization` ensure that:</span></span>

* <span data-ttu-id="3f8ba-169">La aplicación intenta analizar y validar tokens en las solicitudes entrantes.</span><span class="sxs-lookup"><span data-stu-id="3f8ba-169">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="3f8ba-170">Se produce un error en cualquier solicitud que intente tener acceso a un recurso protegido sin las credenciales adecuadas.</span><span class="sxs-lookup"><span data-stu-id="3f8ba-170">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="3f8ba-171">User.Identity.Name</span><span class="sxs-lookup"><span data-stu-id="3f8ba-171">User.Identity.Name</span></span>

<span data-ttu-id="3f8ba-172">De forma predeterminada, la `User.Identity.Name` API de la `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` aplicación de servidor `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`se rellena con el valor del tipo de notificación (por ejemplo, ).</span><span class="sxs-lookup"><span data-stu-id="3f8ba-172">By default, the Server app API populates `User.Identity.Name` with the value from the `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` claim type (for example, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="3f8ba-173">Para configurar la aplicación para `name` recibir el valor del tipo de notificación, `Startup.ConfigureServices`configure [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) de <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in :</span><span class="sxs-lookup"><span data-stu-id="3f8ba-173">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="3f8ba-174">Configuración de la aplicación</span><span class="sxs-lookup"><span data-stu-id="3f8ba-174">App settings</span></span>

<span data-ttu-id="3f8ba-175">El archivo *appsettings.json* contiene las opciones para configurar el controlador portador de JWT utilizado para validar tokens de acceso.</span><span class="sxs-lookup"><span data-stu-id="3f8ba-175">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

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

### <a name="weatherforecast-controller"></a><span data-ttu-id="3f8ba-176">Controlador WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="3f8ba-176">WeatherForecast controller</span></span>

<span data-ttu-id="3f8ba-177">El controlador WeatherForecast (*Controllers/WeatherForecastController.cs*) expone `[Authorize]` una API protegida con el atributo aplicado al controlador.</span><span class="sxs-lookup"><span data-stu-id="3f8ba-177">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the `[Authorize]` attribute applied to the controller.</span></span> <span data-ttu-id="3f8ba-178">Es **importante** entender que:</span><span class="sxs-lookup"><span data-stu-id="3f8ba-178">It's **important** to understand that:</span></span>

* <span data-ttu-id="3f8ba-179">El `[Authorize]` atributo de este controlador de API es lo único que protege esta API del acceso no autorizado.</span><span class="sxs-lookup"><span data-stu-id="3f8ba-179">The `[Authorize]` attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="3f8ba-180">El `[Authorize]` atributo utilizado Blazor en la aplicación WebAssembly solo sirve como sugerencia a la aplicación de que el usuario debe estar autorizado para que la aplicación funcione correctamente.</span><span class="sxs-lookup"><span data-stu-id="3f8ba-180">The `[Authorize]` attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="3f8ba-181">Configuración de la aplicación cliente</span><span class="sxs-lookup"><span data-stu-id="3f8ba-181">Client app configuration</span></span>

<span data-ttu-id="3f8ba-182">*Esta sección pertenece a la aplicación **cliente** de la solución.*</span><span class="sxs-lookup"><span data-stu-id="3f8ba-182">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="3f8ba-183">Paquete de autenticación</span><span class="sxs-lookup"><span data-stu-id="3f8ba-183">Authentication package</span></span>

<span data-ttu-id="3f8ba-184">Cuando se crea una aplicación para`SingleOrg`usar cuentas de trabajo o educativas (`Microsoft.Authentication.WebAssembly.Msal`), la aplicación recibe automáticamente una referencia de paquete para la biblioteca de autenticación de [Microsoft](/azure/active-directory/develop/msal-overview) ( ).</span><span class="sxs-lookup"><span data-stu-id="3f8ba-184">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="3f8ba-185">El paquete proporciona un conjunto de primitivas que ayudan a la aplicación a autenticar a los usuarios y obtener tokens para llamar a las API protegidas.</span><span class="sxs-lookup"><span data-stu-id="3f8ba-185">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="3f8ba-186">Si agrega autenticación a una aplicación, agregue manualmente el paquete al archivo de proyecto de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="3f8ba-186">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="3f8ba-187">Reemplace `{VERSION}` en la referencia del paquete `Microsoft.AspNetCore.Blazor.Templates` anterior por <xref:blazor/get-started> la versión del paquete que se muestra en el artículo.</span><span class="sxs-lookup"><span data-stu-id="3f8ba-187">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="3f8ba-188">El `Microsoft.Authentication.WebAssembly.Msal` paquete agrega transitivamente el `Microsoft.AspNetCore.Components.WebAssembly.Authentication` paquete a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="3f8ba-188">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="3f8ba-189">Soporte de servicio de autenticación</span><span class="sxs-lookup"><span data-stu-id="3f8ba-189">Authentication service support</span></span>

<span data-ttu-id="3f8ba-190">La compatibilidad con la autenticación de usuarios `AddMsalAuthentication` se registra `Microsoft.Authentication.WebAssembly.Msal` en el contenedor de servicios con el método de extensión proporcionado por el paquete.</span><span class="sxs-lookup"><span data-stu-id="3f8ba-190">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="3f8ba-191">Este método configura todos los servicios necesarios para que la aplicación interactúe con el proveedor de identidades (IP).</span><span class="sxs-lookup"><span data-stu-id="3f8ba-191">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="3f8ba-192">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="3f8ba-192">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "https://login.microsoftonline.com/{TENANT ID}";
    authentication.ClientId = "{CLIENT ID}";
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="3f8ba-193">El `AddMsalAuthentication` método acepta una devolución de llamada para configurar los parámetros necesarios para autenticar una aplicación.</span><span class="sxs-lookup"><span data-stu-id="3f8ba-193">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="3f8ba-194">Los valores necesarios para configurar la aplicación se pueden obtener de la configuración de AAD de Azure Portal al registrar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="3f8ba-194">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

### <a name="access-token-scopes"></a><span data-ttu-id="3f8ba-195">Acceso a ámbitos de token</span><span class="sxs-lookup"><span data-stu-id="3f8ba-195">Access token scopes</span></span>

<span data-ttu-id="3f8ba-196">Los ámbitos de token de acceso predeterminados representan la lista de ámbitos de token de acceso que son:</span><span class="sxs-lookup"><span data-stu-id="3f8ba-196">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="3f8ba-197">Incluido de forma predeterminada en la solicitud de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="3f8ba-197">Included by default in the sign in request.</span></span>
* <span data-ttu-id="3f8ba-198">Se utiliza para aprovisionar un token de acceso inmediatamente después de la autenticación.</span><span class="sxs-lookup"><span data-stu-id="3f8ba-198">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="3f8ba-199">Todos los ámbitos deben pertenecer a la misma aplicación según las reglas de Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="3f8ba-199">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="3f8ba-200">Se pueden agregar ámbitos adicionales para aplicaciones de API adicionales según sea necesario:</span><span class="sxs-lookup"><span data-stu-id="3f8ba-200">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="3f8ba-201">Si Azure Portal proporciona un URI de ámbito y **la aplicación produce una excepción no controlada** cuando recibe una respuesta *401 Unauthorized* de la API, intente usar un URI de ámbito que no incluya el esquema y el host.</span><span class="sxs-lookup"><span data-stu-id="3f8ba-201">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="3f8ba-202">Por ejemplo, Azure Portal puede proporcionar uno de los siguientes formatos de URI de ámbito:</span><span class="sxs-lookup"><span data-stu-id="3f8ba-202">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="3f8ba-203">Proporcione el URI de ámbito sin el esquema y el host:</span><span class="sxs-lookup"><span data-stu-id="3f8ba-203">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="3f8ba-204">Para obtener más información, vea <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span><span class="sxs-lookup"><span data-stu-id="3f8ba-204">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

### <a name="imports-file"></a><span data-ttu-id="3f8ba-205">Archivo de importaciones</span><span class="sxs-lookup"><span data-stu-id="3f8ba-205">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="3f8ba-206">Página de índice</span><span class="sxs-lookup"><span data-stu-id="3f8ba-206">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="3f8ba-207">Componente de la aplicación</span><span class="sxs-lookup"><span data-stu-id="3f8ba-207">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="3f8ba-208">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="3f8ba-208">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="3f8ba-209">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="3f8ba-209">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="3f8ba-210">Componente de autenticación</span><span class="sxs-lookup"><span data-stu-id="3f8ba-210">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="3f8ba-211">Componente FetchData</span><span class="sxs-lookup"><span data-stu-id="3f8ba-211">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="3f8ba-212">Ejecutar la aplicación</span><span class="sxs-lookup"><span data-stu-id="3f8ba-212">Run the app</span></span>

<span data-ttu-id="3f8ba-213">Ejecute la aplicación desde el proyecto Servidor.</span><span class="sxs-lookup"><span data-stu-id="3f8ba-213">Run the app from the Server project.</span></span> <span data-ttu-id="3f8ba-214">Al usar Visual Studio, seleccione el proyecto de servidor en el **Explorador** de soluciones y seleccione el botón **Ejecutar** en la barra de herramientas o inicie la aplicación desde el menú **Depurar.**</span><span class="sxs-lookup"><span data-stu-id="3f8ba-214">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="3f8ba-215">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="3f8ba-215">Additional resources</span></span>

* [<span data-ttu-id="3f8ba-216">Solicitar tokens de acceso adicionales</span><span class="sxs-lookup"><span data-stu-id="3f8ba-216">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="3f8ba-217">Documentación de la plataforma de identidad de Microsoft</span><span class="sxs-lookup"><span data-stu-id="3f8ba-217">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
