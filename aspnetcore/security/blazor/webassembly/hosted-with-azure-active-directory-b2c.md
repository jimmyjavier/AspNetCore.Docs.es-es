---
title: Proteja una Blazor aplicación hospedada de ASP.NET Core WebAssembly con Azure Active Directory B2C
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/09/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: e2bb0c1bd807d590331b714e3b80d8c4ab434e2f
ms.sourcegitcommit: e8dc30453af8bbefcb61857987090d79230a461d
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/11/2020
ms.locfileid: "81123466"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a><span data-ttu-id="e0ae2-102">Proteja una Blazor aplicación hospedada de ASP.NET Core WebAssembly con Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="e0ae2-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C</span></span>

<span data-ttu-id="e0ae2-103">Por [Javier Calvarro Nelson](https://github.com/javiercn) y Luke [Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="e0ae2-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="e0ae2-104">En este artículo se Blazor describe cómo crear una aplicación independiente de WebAssembly que usa [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) para la autenticación.</span><span class="sxs-lookup"><span data-stu-id="e0ae2-104">This article describes how to create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="e0ae2-105">Registre aplicaciones en AAD B2C y cree una solución</span><span class="sxs-lookup"><span data-stu-id="e0ae2-105">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="e0ae2-106">Creación de un inquilino</span><span class="sxs-lookup"><span data-stu-id="e0ae2-106">Create a tenant</span></span>

<span data-ttu-id="e0ae2-107">Siga las instrucciones de Tutorial: Crear un inquilino de [Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant) para crear un inquilino de AAD B2C y registrar la siguiente información:</span><span class="sxs-lookup"><span data-stu-id="e0ae2-107">Follow the guidance in [Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) to create an AAD B2C tenant and record the following information:</span></span>

* <span data-ttu-id="e0ae2-108">Instancia de AAD B2C `https://contoso.b2clogin.com/`(por ejemplo, , que incluye la barra diagonal final)</span><span class="sxs-lookup"><span data-stu-id="e0ae2-108">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span>
* <span data-ttu-id="e0ae2-109">Dominio de inquilino de AAD `contoso.onmicrosoft.com`B2C (por ejemplo, )</span><span class="sxs-lookup"><span data-stu-id="e0ae2-109">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="e0ae2-110">Registrar una aplicación de API de servidor</span><span class="sxs-lookup"><span data-stu-id="e0ae2-110">Register a server API app</span></span>

<span data-ttu-id="e0ae2-111">Siga las instrucciones de [Tutorial: Registrar una aplicación en Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) para registrar una aplicación de AAD para la *aplicación API* de servidor en el área**Registros** de aplicaciones de **Azure Active Directory** > de Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="e0ae2-111">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) to register an AAD app for the *Server API app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="e0ae2-112">Seleccione **Nuevo registro**.</span><span class="sxs-lookup"><span data-stu-id="e0ae2-112">Select **New registration**.</span></span>
1. <span data-ttu-id="e0ae2-113">Proporcione un **nombre** para la aplicación (por ejemplo, \*\* Blazor AAD B2C\*\*de servidor).</span><span class="sxs-lookup"><span data-stu-id="e0ae2-113">Provide a **Name** for the app (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="e0ae2-114">En **Tipos de cuenta admitidos**, seleccione **Cuentas en cualquier directorio de organización o en cualquier proveedor de identidades. Para autenticar usuarios con Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="e0ae2-114">For **Supported account types**, select **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span> <span data-ttu-id="e0ae2-115">(multiinquilino) para esta experiencia.</span><span class="sxs-lookup"><span data-stu-id="e0ae2-115">(multi-tenant) for this experience.</span></span>
1. <span data-ttu-id="e0ae2-116">La *aplicación API* de servidor no requiere un URI de **redirección** en este escenario, así que deje el menú desplegable establecido en **Web** y no escriba un URI de redirección.</span><span class="sxs-lookup"><span data-stu-id="e0ae2-116">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="e0ae2-117">Confirme que **permisos** > **Conceder permisos admin a openid y offline_access permisos** está habilitado.</span><span class="sxs-lookup"><span data-stu-id="e0ae2-117">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="e0ae2-118">Seleccione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="e0ae2-118">Select **Register**.</span></span>

<span data-ttu-id="e0ae2-119">En **Exponer una API:**</span><span class="sxs-lookup"><span data-stu-id="e0ae2-119">In **Expose an API**:</span></span>

1. <span data-ttu-id="e0ae2-120">Seleccione **Agregar un ámbito**.</span><span class="sxs-lookup"><span data-stu-id="e0ae2-120">Select **Add a scope**.</span></span>
1. <span data-ttu-id="e0ae2-121">Seleccione **Guardar y continuar**.</span><span class="sxs-lookup"><span data-stu-id="e0ae2-121">Select **Save and continue**.</span></span>
1. <span data-ttu-id="e0ae2-122">Proporcione un **nombre de** `API.Access`ámbito (por ejemplo, ).</span><span class="sxs-lookup"><span data-stu-id="e0ae2-122">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="e0ae2-123">Proporcione un **nombre para mostrar** `Access API`del consentimiento de administrador (por ejemplo, ).</span><span class="sxs-lookup"><span data-stu-id="e0ae2-123">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="e0ae2-124">Proporcione una **descripción** del `Allows the app to access server app API endpoints.`consentimiento del administrador (por ejemplo, ).</span><span class="sxs-lookup"><span data-stu-id="e0ae2-124">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="e0ae2-125">Confirme que el **estado** está establecido en **Habilitado**.</span><span class="sxs-lookup"><span data-stu-id="e0ae2-125">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="e0ae2-126">Seleccione la opción **Agregar un ámbito**.</span><span class="sxs-lookup"><span data-stu-id="e0ae2-126">Select **Add scope**.</span></span>

<span data-ttu-id="e0ae2-127">Registre la siguiente información:</span><span class="sxs-lookup"><span data-stu-id="e0ae2-127">Record the following information:</span></span>

* <span data-ttu-id="e0ae2-128">*Aplicación API de servidor* ID de aplicación (ID de `11111111-1111-1111-1111-111111111111`cliente) (por ejemplo, )</span><span class="sxs-lookup"><span data-stu-id="e0ae2-128">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="e0ae2-129">URI de identificador de `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` `api://11111111-1111-1111-1111-111111111111`aplicación (por ejemplo, , , o el valor personalizado que proporcionó)</span><span class="sxs-lookup"><span data-stu-id="e0ae2-129">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="e0ae2-130">ID de directorio (ID de `222222222-2222-2222-2222-222222222222`inquilino) (por ejemplo, )</span><span class="sxs-lookup"><span data-stu-id="e0ae2-130">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="e0ae2-131">*Aplicación API de servidor* URI de identificador de `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`aplicación (por ejemplo, Azure Portal podría establecer el valor predeterminado en el identificador de cliente)</span><span class="sxs-lookup"><span data-stu-id="e0ae2-131">*Server API app* App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, the Azure portal might default the value to the Client ID)</span></span>
* <span data-ttu-id="e0ae2-132">Alcance predeterminado (por `API.Access`ejemplo, )</span><span class="sxs-lookup"><span data-stu-id="e0ae2-132">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="e0ae2-133">Registrar una aplicación de cliente</span><span class="sxs-lookup"><span data-stu-id="e0ae2-133">Register a client app</span></span>

<span data-ttu-id="e0ae2-134">Siga las instrucciones de [Tutorial: Registrar una aplicación en Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) de nuevo para registrar una aplicación de AAD para la *aplicación cliente* en el área**de registros** de aplicaciones de **Azure Active Directory** > de Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="e0ae2-134">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *Client app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="e0ae2-135">Seleccione **Nuevo registro**.</span><span class="sxs-lookup"><span data-stu-id="e0ae2-135">Select **New registration**.</span></span>
1. <span data-ttu-id="e0ae2-136">Proporcione un **nombre** para la aplicación (por ejemplo, \*\* Blazor Cliente AAD B2C).\*\*</span><span class="sxs-lookup"><span data-stu-id="e0ae2-136">Provide a **Name** for the app (for example, **Blazor Client AAD B2C**).</span></span>
1. <span data-ttu-id="e0ae2-137">En **Tipos de cuenta admitidos**, seleccione **Cuentas en cualquier directorio de organización o en cualquier proveedor de identidades. Para autenticar usuarios con Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="e0ae2-137">For **Supported account types**, select **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span> <span data-ttu-id="e0ae2-138">(multiinquilino) para esta experiencia.</span><span class="sxs-lookup"><span data-stu-id="e0ae2-138">(multi-tenant) for this experience.</span></span>
1. <span data-ttu-id="e0ae2-139">Deje el descenso desplegable URI de **redirección** establecido `https://localhost:5001/authentication/login-callback`en **Web**y proporcione un URI de redireccionamiento de .</span><span class="sxs-lookup"><span data-stu-id="e0ae2-139">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="e0ae2-140">Confirme que **permisos** > **Conceder permisos admin a openid y offline_access permisos** está habilitado.</span><span class="sxs-lookup"><span data-stu-id="e0ae2-140">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="e0ae2-141">Seleccione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="e0ae2-141">Select **Register**.</span></span>

<span data-ttu-id="e0ae2-142">En la**Web\*\*\*\*de configuraciones** > de la plataforma de **autenticación:** > </span><span class="sxs-lookup"><span data-stu-id="e0ae2-142">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="e0ae2-143">Confirme que el `https://localhost:5001/authentication/login-callback` URI de **redireccionamiento** está presente.</span><span class="sxs-lookup"><span data-stu-id="e0ae2-143">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="e0ae2-144">En **Implicit grant**, active las casillas de los tokens de **acceso** y los tokens **de identificador.**</span><span class="sxs-lookup"><span data-stu-id="e0ae2-144">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="e0ae2-145">Los valores predeterminados restantes de la aplicación son aceptables para esta experiencia.</span><span class="sxs-lookup"><span data-stu-id="e0ae2-145">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="e0ae2-146">Seleccione el botón **Guardar**.</span><span class="sxs-lookup"><span data-stu-id="e0ae2-146">Select the **Save** button.</span></span>

<span data-ttu-id="e0ae2-147">En **Permisos de API:**</span><span class="sxs-lookup"><span data-stu-id="e0ae2-147">In **API permissions**:</span></span>

1. <span data-ttu-id="e0ae2-148">Confirme que la aplicación tiene el permiso **Microsoft Graph** > **User.Read.**</span><span class="sxs-lookup"><span data-stu-id="e0ae2-148">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="e0ae2-149">Seleccione **Agregar un permiso** seguido de Mis **API**.</span><span class="sxs-lookup"><span data-stu-id="e0ae2-149">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="e0ae2-150">Seleccione la *aplicación API* de servidor en la columna **Nombre** (por ejemplo, \*\* Blazor AAD B2C\*\*de servidor).</span><span class="sxs-lookup"><span data-stu-id="e0ae2-150">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="e0ae2-151">Abra la lista **de API.**</span><span class="sxs-lookup"><span data-stu-id="e0ae2-151">Open the **API** list.</span></span>
1. <span data-ttu-id="e0ae2-152">Habilite el acceso a la `API.Access`API (por ejemplo, ).</span><span class="sxs-lookup"><span data-stu-id="e0ae2-152">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="e0ae2-153">Seleccione **Agregar permisos**.</span><span class="sxs-lookup"><span data-stu-id="e0ae2-153">Select **Add permissions**.</span></span>
1. <span data-ttu-id="e0ae2-154">Seleccione el botón Conceder contenido de administrador para el botón **"NOMBRE DEL INQUILINO".**</span><span class="sxs-lookup"><span data-stu-id="e0ae2-154">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="e0ae2-155">Seleccione **Sí** para confirmar la acción.</span><span class="sxs-lookup"><span data-stu-id="e0ae2-155">Select **Yes** to confirm.</span></span>

<span data-ttu-id="e0ae2-156">En Flujos de**usuario\*\*\*\*de Azure AD B2C** >  **en casa:** > </span><span class="sxs-lookup"><span data-stu-id="e0ae2-156">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="e0ae2-157">Creación de un flujo de usuario de registro e inicio de sesión</span><span class="sxs-lookup"><span data-stu-id="e0ae2-157">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="e0ae2-158">Como mínimo, seleccione el atributo de usuario `context.User.Identity.Name` Nombre `LoginDisplay` para**mostrar** **notificaciones** > de aplicación para rellenar el componente (*Shared/LoginDisplay.razor*).</span><span class="sxs-lookup"><span data-stu-id="e0ae2-158">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

<span data-ttu-id="e0ae2-159">Registre la siguiente información:</span><span class="sxs-lookup"><span data-stu-id="e0ae2-159">Record the following information:</span></span>

* <span data-ttu-id="e0ae2-160">Registre el identificador de aplicación de *aplicación* `33333333-3333-3333-3333-333333333333`cliente (ID de cliente) (por ejemplo, ).</span><span class="sxs-lookup"><span data-stu-id="e0ae2-160">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>
* <span data-ttu-id="e0ae2-161">Registre el nombre de flujo de usuario de registro e `B2C_1_signupsignin`inicio de sesión creado para la aplicación (por ejemplo, ).</span><span class="sxs-lookup"><span data-stu-id="e0ae2-161">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="e0ae2-162">Creación de la aplicación</span><span class="sxs-lookup"><span data-stu-id="e0ae2-162">Create the app</span></span>

<span data-ttu-id="e0ae2-163">Reemplace los marcadores de posición del siguiente comando por la información registrada anteriormente y ejecute el comando en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="e0ae2-163">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho -ssp "{SIGN UP OR SIGN IN POLICY}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="e0ae2-164">Para especificar la ubicación de salida, que crea una carpeta de proyecto si no existe, `-o BlazorSample`incluya la opción de salida en el comando con una ruta de acceso (por ejemplo, ).</span><span class="sxs-lookup"><span data-stu-id="e0ae2-164">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="e0ae2-165">El nombre de la carpeta también pasa a formar parte del nombre del proyecto.</span><span class="sxs-lookup"><span data-stu-id="e0ae2-165">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="e0ae2-166">Pase el URI de `app-id-uri` identificador de aplicación a la opción, pero tenga en cuenta que puede ser necesario un cambio de configuración en la aplicación cliente, que se describe en la sección [Ámbitos](#access-token-scopes) de token de acceso.</span><span class="sxs-lookup"><span data-stu-id="e0ae2-166">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="e0ae2-167">Configuración de la aplicación de servidor</span><span class="sxs-lookup"><span data-stu-id="e0ae2-167">Server app configuration</span></span>

<span data-ttu-id="e0ae2-168">*Esta sección pertenece a la aplicación **de servidor** de la solución.*</span><span class="sxs-lookup"><span data-stu-id="e0ae2-168">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="e0ae2-169">Paquete de autenticación</span><span class="sxs-lookup"><span data-stu-id="e0ae2-169">Authentication package</span></span>

<span data-ttu-id="e0ae2-170">El soporte para autenticar y autorizar llamadas a ASP.NET API `Microsoft.AspNetCore.Authentication.AzureADB2C.UI`web principales es proporcionado por:</span><span class="sxs-lookup"><span data-stu-id="e0ae2-170">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the `Microsoft.AspNetCore.Authentication.AzureADB2C.UI`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
    Version="3.1.0" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="e0ae2-171">Soporte de servicio de autenticación</span><span class="sxs-lookup"><span data-stu-id="e0ae2-171">Authentication service support</span></span>

<span data-ttu-id="e0ae2-172">El `AddAuthentication` método configura los servicios de autenticación dentro de la aplicación y configura el controlador JWT Bearer como el método de autenticación predeterminado.</span><span class="sxs-lookup"><span data-stu-id="e0ae2-172">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="e0ae2-173">El `AddAzureADB2CBearer` método configura los parámetros específicos en el controlador de JWT Bearer necesarios para validar los tokens emitidos por Azure Active Directory B2C:</span><span class="sxs-lookup"><span data-stu-id="e0ae2-173">The `AddAzureADB2CBearer` method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory B2C:</span></span>

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

<span data-ttu-id="e0ae2-174">`UseAuthentication`y `UseAuthorization` asegurar se que:</span><span class="sxs-lookup"><span data-stu-id="e0ae2-174">`UseAuthentication` and `UseAuthorization` ensure that:</span></span>

* <span data-ttu-id="e0ae2-175">La aplicación intenta analizar y validar tokens en las solicitudes entrantes.</span><span class="sxs-lookup"><span data-stu-id="e0ae2-175">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="e0ae2-176">Se produce un error en cualquier solicitud que intente tener acceso a un recurso protegido sin las credenciales adecuadas.</span><span class="sxs-lookup"><span data-stu-id="e0ae2-176">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="e0ae2-177">User.Identity.Name</span><span class="sxs-lookup"><span data-stu-id="e0ae2-177">User.Identity.Name</span></span>

<span data-ttu-id="e0ae2-178">De forma `User.Identity.Name` predeterminada, no se rellena.</span><span class="sxs-lookup"><span data-stu-id="e0ae2-178">By default, the `User.Identity.Name` isn't populated.</span></span>

<span data-ttu-id="e0ae2-179">Para configurar la aplicación para `name` recibir el valor del tipo de notificación, `Startup.ConfigureServices`configure [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) de <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in :</span><span class="sxs-lookup"><span data-stu-id="e0ae2-179">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<JwtBearerOptions>(
    AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="e0ae2-180">Configuración de la aplicación</span><span class="sxs-lookup"><span data-stu-id="e0ae2-180">App settings</span></span>

<span data-ttu-id="e0ae2-181">El archivo *appsettings.json* contiene las opciones para configurar el controlador portador de JWT utilizado para validar tokens de acceso.</span><span class="sxs-lookup"><span data-stu-id="e0ae2-181">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

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

### <a name="weatherforecast-controller"></a><span data-ttu-id="e0ae2-182">Controlador WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="e0ae2-182">WeatherForecast controller</span></span>

<span data-ttu-id="e0ae2-183">El controlador WeatherForecast (*Controllers/WeatherForecastController.cs*) expone `[Authorize]` una API protegida con el atributo aplicado al controlador.</span><span class="sxs-lookup"><span data-stu-id="e0ae2-183">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the `[Authorize]` attribute applied to the controller.</span></span> <span data-ttu-id="e0ae2-184">Es **importante** entender que:</span><span class="sxs-lookup"><span data-stu-id="e0ae2-184">It's **important** to understand that:</span></span>

* <span data-ttu-id="e0ae2-185">El `[Authorize]` atributo de este controlador de API es lo único que protege esta API del acceso no autorizado.</span><span class="sxs-lookup"><span data-stu-id="e0ae2-185">The `[Authorize]` attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="e0ae2-186">El `[Authorize]` atributo utilizado Blazor en la aplicación WebAssembly solo sirve como sugerencia a la aplicación de que el usuario debe estar autorizado para que la aplicación funcione correctamente.</span><span class="sxs-lookup"><span data-stu-id="e0ae2-186">The `[Authorize]` attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="e0ae2-187">Configuración de la aplicación cliente</span><span class="sxs-lookup"><span data-stu-id="e0ae2-187">Client app configuration</span></span>

<span data-ttu-id="e0ae2-188">*Esta sección pertenece a la aplicación **cliente** de la solución.*</span><span class="sxs-lookup"><span data-stu-id="e0ae2-188">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="e0ae2-189">Paquete de autenticación</span><span class="sxs-lookup"><span data-stu-id="e0ae2-189">Authentication package</span></span>

<span data-ttu-id="e0ae2-190">Cuando se crea una aplicación para usar`IndividualB2C`una cuenta B2C individual ( ), la`Microsoft.Authentication.WebAssembly.Msal`aplicación recibe automáticamente una referencia de paquete para la biblioteca de autenticación de [Microsoft](/azure/active-directory/develop/msal-overview) ( ).</span><span class="sxs-lookup"><span data-stu-id="e0ae2-190">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="e0ae2-191">El paquete proporciona un conjunto de primitivas que ayudan a la aplicación a autenticar a los usuarios y obtener tokens para llamar a las API protegidas.</span><span class="sxs-lookup"><span data-stu-id="e0ae2-191">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="e0ae2-192">Si agrega autenticación a una aplicación, agregue manualmente el paquete al archivo de proyecto de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="e0ae2-192">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="e0ae2-193">Reemplace `{VERSION}` en la referencia del paquete `Microsoft.AspNetCore.Blazor.Templates` anterior por <xref:blazor/get-started> la versión del paquete que se muestra en el artículo.</span><span class="sxs-lookup"><span data-stu-id="e0ae2-193">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="e0ae2-194">El `Microsoft.Authentication.WebAssembly.Msal` paquete agrega transitivamente el `Microsoft.AspNetCore.Components.WebAssembly.Authentication` paquete a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="e0ae2-194">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="e0ae2-195">Soporte de servicio de autenticación</span><span class="sxs-lookup"><span data-stu-id="e0ae2-195">Authentication service support</span></span>

<span data-ttu-id="e0ae2-196">La compatibilidad con la autenticación de usuarios `AddMsalAuthentication` se registra `Microsoft.Authentication.WebAssembly.Msal` en el contenedor de servicios con el método de extensión proporcionado por el paquete.</span><span class="sxs-lookup"><span data-stu-id="e0ae2-196">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="e0ae2-197">Este método configura todos los servicios necesarios para que la aplicación interactúe con el proveedor de identidades (IP).</span><span class="sxs-lookup"><span data-stu-id="e0ae2-197">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="e0ae2-198">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="e0ae2-198">*Program.cs*:</span></span>

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

<span data-ttu-id="e0ae2-199">El `AddMsalAuthentication` método acepta una devolución de llamada para configurar los parámetros necesarios para autenticar una aplicación.</span><span class="sxs-lookup"><span data-stu-id="e0ae2-199">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="e0ae2-200">Los valores necesarios para configurar la aplicación se pueden obtener de la configuración de AAD de Azure Portal al registrar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="e0ae2-200">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

### <a name="access-token-scopes"></a><span data-ttu-id="e0ae2-201">Acceso a ámbitos de token</span><span class="sxs-lookup"><span data-stu-id="e0ae2-201">Access token scopes</span></span>

<span data-ttu-id="e0ae2-202">Los ámbitos de token de acceso predeterminados representan la lista de ámbitos de token de acceso que son:</span><span class="sxs-lookup"><span data-stu-id="e0ae2-202">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="e0ae2-203">Incluido de forma predeterminada en la solicitud de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="e0ae2-203">Included by default in the sign in request.</span></span>
* <span data-ttu-id="e0ae2-204">Se utiliza para aprovisionar un token de acceso inmediatamente después de la autenticación.</span><span class="sxs-lookup"><span data-stu-id="e0ae2-204">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="e0ae2-205">Todos los ámbitos deben pertenecer a la misma aplicación según las reglas de Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="e0ae2-205">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="e0ae2-206">Se pueden agregar ámbitos adicionales para aplicaciones de API adicionales según sea necesario:</span><span class="sxs-lookup"><span data-stu-id="e0ae2-206">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="e0ae2-207">Si Azure Portal proporciona un URI de ámbito y **la aplicación produce una excepción no controlada** cuando recibe una respuesta *401 Unauthorized* de la API, intente usar un URI de ámbito que no incluya el esquema y el host.</span><span class="sxs-lookup"><span data-stu-id="e0ae2-207">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="e0ae2-208">Por ejemplo, Azure Portal puede proporcionar uno de los siguientes formatos de URI de ámbito:</span><span class="sxs-lookup"><span data-stu-id="e0ae2-208">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="e0ae2-209">Proporcione el URI de ámbito sin el esquema y el host:</span><span class="sxs-lookup"><span data-stu-id="e0ae2-209">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="e0ae2-210">Para obtener más información, vea <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span><span class="sxs-lookup"><span data-stu-id="e0ae2-210">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

### <a name="imports-file"></a><span data-ttu-id="e0ae2-211">Archivo de importaciones</span><span class="sxs-lookup"><span data-stu-id="e0ae2-211">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="e0ae2-212">Página de índice</span><span class="sxs-lookup"><span data-stu-id="e0ae2-212">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="e0ae2-213">Componente de la aplicación</span><span class="sxs-lookup"><span data-stu-id="e0ae2-213">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="e0ae2-214">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="e0ae2-214">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="e0ae2-215">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="e0ae2-215">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="e0ae2-216">Componente de autenticación</span><span class="sxs-lookup"><span data-stu-id="e0ae2-216">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="e0ae2-217">Componente FetchData</span><span class="sxs-lookup"><span data-stu-id="e0ae2-217">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="e0ae2-218">Ejecución la aplicación</span><span class="sxs-lookup"><span data-stu-id="e0ae2-218">Run the app</span></span>

<span data-ttu-id="e0ae2-219">Ejecute la aplicación desde el proyecto Servidor.</span><span class="sxs-lookup"><span data-stu-id="e0ae2-219">Run the app from the Server project.</span></span> <span data-ttu-id="e0ae2-220">Al usar Visual Studio, seleccione el proyecto de servidor en el **Explorador** de soluciones y seleccione el botón **Ejecutar** en la barra de herramientas o inicie la aplicación desde el menú **Depurar.**</span><span class="sxs-lookup"><span data-stu-id="e0ae2-220">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="e0ae2-221">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="e0ae2-221">Additional resources</span></span>

* [<span data-ttu-id="e0ae2-222">Solicitar tokens de acceso adicionales</span><span class="sxs-lookup"><span data-stu-id="e0ae2-222">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="e0ae2-223">Tutorial: Creación de un inquilino de Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="e0ae2-223">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="e0ae2-224">Documentación de la plataforma de identidad de Microsoft</span><span class="sxs-lookup"><span data-stu-id="e0ae2-224">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
