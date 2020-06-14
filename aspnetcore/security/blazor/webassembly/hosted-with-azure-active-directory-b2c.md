---
title: Protección de una Blazor aplicación hospedada en Webassembly ASP.net Core con Azure Active Directory B2C
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: 9a63d6ca0ab6b71875212d54035dfb5cf94a8cad
ms.sourcegitcommit: d243fadeda20ad4f142ea60301ae5f5e0d41ed60
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/12/2020
ms.locfileid: "84724307"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a><span data-ttu-id="9c395-102">Protección de una Blazor aplicación hospedada en Webassembly ASP.net Core con Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="9c395-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C</span></span>

<span data-ttu-id="9c395-103">Por [Javier Calvarro Nelson](https://github.com/javiercn) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="9c395-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="9c395-104">En este artículo se describe cómo crear una Blazor aplicación independiente Webassembly que usa [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) para la autenticación.</span><span class="sxs-lookup"><span data-stu-id="9c395-104">This article describes how to create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="9c395-105">Registrar aplicaciones en AAD B2C y crear una solución</span><span class="sxs-lookup"><span data-stu-id="9c395-105">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="9c395-106">Creación de un inquilino</span><span class="sxs-lookup"><span data-stu-id="9c395-106">Create a tenant</span></span>

<span data-ttu-id="9c395-107">Siga las instrucciones del [Tutorial: creación de un inquilino de Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant) para crear un inquilino de AAD B2C.</span><span class="sxs-lookup"><span data-stu-id="9c395-107">Follow the guidance in [Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) to create an AAD B2C tenant.</span></span>

<span data-ttu-id="9c395-108">Registre la siguiente información:</span><span class="sxs-lookup"><span data-stu-id="9c395-108">Record the following information:</span></span>

* <span data-ttu-id="9c395-109">AAD B2C instancia (por ejemplo, `https://contoso.b2clogin.com/` , que incluye la barra diagonal final)</span><span class="sxs-lookup"><span data-stu-id="9c395-109">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span>
* <span data-ttu-id="9c395-110">AAD B2C dominio del inquilino (por ejemplo, `contoso.onmicrosoft.com` )</span><span class="sxs-lookup"><span data-stu-id="9c395-110">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="9c395-111">Registrar una aplicación de API de servidor</span><span class="sxs-lookup"><span data-stu-id="9c395-111">Register a server API app</span></span>

<span data-ttu-id="9c395-112">Siga las instrucciones de [Tutorial: registro de una aplicación en Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) para registrar una aplicación de AAD para la *aplicación de API de servidor* y, a continuación, haga lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="9c395-112">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) to register an AAD app for the *Server API app* and then do the following:</span></span>

1. <span data-ttu-id="9c395-113">En **Azure Active Directory**  >  **registros de aplicaciones**, seleccione **nuevo registro**.</span><span class="sxs-lookup"><span data-stu-id="9c395-113">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="9c395-114">Proporcione un **nombre** para la aplicación (por ejemplo, \*\* Blazor servidor AAD B2C\*\*).</span><span class="sxs-lookup"><span data-stu-id="9c395-114">Provide a **Name** for the app (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="9c395-115">En **tipos de cuenta compatibles**, seleccione la opción de varios inquilinos: **cuentas en cualquier directorio de la organización o cualquier proveedor de identidades. Para autenticar a los usuarios con Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="9c395-115">For **Supported account types**, select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="9c395-116">La *aplicación de API de servidor* no requiere un **URI de redirección** en este escenario, por lo que deje la lista desplegable establecida en **Web** y no escriba un URI de redirección.</span><span class="sxs-lookup"><span data-stu-id="9c395-116">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="9c395-117">Confirme que **permisos**  >  **conceder consentimiento de administrador a OpenID y offline_access permisos** está habilitado.</span><span class="sxs-lookup"><span data-stu-id="9c395-117">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="9c395-118">Seleccione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="9c395-118">Select **Register**.</span></span>

<span data-ttu-id="9c395-119">Registre la siguiente información:</span><span class="sxs-lookup"><span data-stu-id="9c395-119">Record the following information:</span></span>

* <span data-ttu-id="9c395-120">*Aplicación de API de servidor* IDENTIFICADOR de aplicación (ID. de cliente) (por ejemplo, `11111111-1111-1111-1111-111111111111` )</span><span class="sxs-lookup"><span data-stu-id="9c395-120">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="9c395-121">IDENTIFICADOR de directorio (identificador de inquilino) (por ejemplo, `222222222-2222-2222-2222-222222222222` )</span><span class="sxs-lookup"><span data-stu-id="9c395-121">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="9c395-122">Dominio del inquilino de AAD (por ejemplo, `contoso.onmicrosoft.com` ): el dominio está disponible como el **dominio del publicador** en la hoja de **Personalización de marca** del Azure portal de la aplicación registrada.</span><span class="sxs-lookup"><span data-stu-id="9c395-122">AAD Tenant domain (for example, `contoso.onmicrosoft.com`): The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="9c395-123">En **exponer una API**:</span><span class="sxs-lookup"><span data-stu-id="9c395-123">In **Expose an API**:</span></span>

1. <span data-ttu-id="9c395-124">Seleccione **Agregar un ámbito**.</span><span class="sxs-lookup"><span data-stu-id="9c395-124">Select **Add a scope**.</span></span>
1. <span data-ttu-id="9c395-125">Selecciona **Guardar y continuar**.</span><span class="sxs-lookup"><span data-stu-id="9c395-125">Select **Save and continue**.</span></span>
1. <span data-ttu-id="9c395-126">Proporcione un **nombre de ámbito** (por ejemplo, `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="9c395-126">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="9c395-127">Proporcione un **nombre para mostrar del consentimiento del administrador** (por ejemplo, `Access API` ).</span><span class="sxs-lookup"><span data-stu-id="9c395-127">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="9c395-128">Proporcione una **Descripción del consentimiento del administrador** (por ejemplo, `Allows the app to access server app API endpoints.` ).</span><span class="sxs-lookup"><span data-stu-id="9c395-128">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="9c395-129">Confirme que el **Estado** está establecido en **habilitado**.</span><span class="sxs-lookup"><span data-stu-id="9c395-129">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="9c395-130">Seleccione la opción **Agregar un ámbito**.</span><span class="sxs-lookup"><span data-stu-id="9c395-130">Select **Add scope**.</span></span>

<span data-ttu-id="9c395-131">Registre la siguiente información:</span><span class="sxs-lookup"><span data-stu-id="9c395-131">Record the following information:</span></span>

* <span data-ttu-id="9c395-132">URI de ID. de aplicación (por ejemplo, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` , `api://11111111-1111-1111-1111-111111111111` o el valor personalizado que proporcionó)</span><span class="sxs-lookup"><span data-stu-id="9c395-132">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="9c395-133">Ámbito predeterminado (por ejemplo, `API.Access` )</span><span class="sxs-lookup"><span data-stu-id="9c395-133">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="9c395-134">Registrar una aplicación de cliente</span><span class="sxs-lookup"><span data-stu-id="9c395-134">Register a client app</span></span>

<span data-ttu-id="9c395-135">Siga las instrucciones de [Tutorial: registro de una aplicación en Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) de nuevo para registrar una aplicación de AAD para la *aplicación cliente* y, a continuación, haga lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="9c395-135">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *Client app* and then do the following:</span></span>

1. <span data-ttu-id="9c395-136">En **Azure Active Directory**  >  **registros de aplicaciones**, seleccione **nuevo registro**.</span><span class="sxs-lookup"><span data-stu-id="9c395-136">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="9c395-137">Proporcione un **nombre** para la aplicación (por ejemplo, \*\* Blazor cliente AAD B2C\*\*).</span><span class="sxs-lookup"><span data-stu-id="9c395-137">Provide a **Name** for the app (for example, **Blazor Client AAD B2C**).</span></span>
1. <span data-ttu-id="9c395-138">En **tipos de cuenta compatibles**, seleccione la opción de varios inquilinos: **cuentas en cualquier directorio de la organización o cualquier proveedor de identidades. Para autenticar a los usuarios con Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="9c395-138">For **Supported account types**, select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="9c395-139">Deje la lista desplegable **URI de redirección** establecida en **Web** y proporcione el siguiente URI de redirección: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="9c395-139">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="9c395-140">El puerto predeterminado para una aplicación que se ejecuta en Kestrel es 5001.</span><span class="sxs-lookup"><span data-stu-id="9c395-140">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="9c395-141">Si la aplicación se ejecuta en otro puerto Kestrel, use el puerto de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="9c395-141">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="9c395-142">Por IIS Express, el puerto generado de forma aleatoria para la aplicación se puede encontrar en las propiedades de la aplicación de servidor en el panel de **depuración** .</span><span class="sxs-lookup"><span data-stu-id="9c395-142">For IIS Express, the randomly generated port for the app can be found in the Server app's properties in the **Debug** panel.</span></span> <span data-ttu-id="9c395-143">Dado que la aplicación no existe en este momento y no se conoce el puerto IIS Express, vuelva a este paso después de crear la aplicación y actualice el URI de redirección.</span><span class="sxs-lookup"><span data-stu-id="9c395-143">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="9c395-144">Aparece un Comentario en la sección [creación de la aplicación](#create-the-app) para recordar IIS Express usuarios a actualizar el URI de redirección.</span><span class="sxs-lookup"><span data-stu-id="9c395-144">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="9c395-145">Confirme que **permisos**  >  **conceder consentimiento de administrador a OpenID y offline_access permisos** está habilitado.</span><span class="sxs-lookup"><span data-stu-id="9c395-145">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="9c395-146">Seleccione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="9c395-146">Select **Register**.</span></span>

<span data-ttu-id="9c395-147">Registre el identificador de aplicación (ID. de cliente) (por ejemplo, `11111111-1111-1111-1111-111111111111` ).</span><span class="sxs-lookup"><span data-stu-id="9c395-147">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

<span data-ttu-id="9c395-148">En configuración de la plataforma de **autenticación**  >  **Platform configurations**  >  **Web**:</span><span class="sxs-lookup"><span data-stu-id="9c395-148">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="9c395-149">Confirme que el **URI de redirección** de `https://localhost:{PORT}/authentication/login-callback` está presente.</span><span class="sxs-lookup"><span data-stu-id="9c395-149">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="9c395-150">En **concesión implícita**, active las casillas de verificación de **tokens de acceso** y **tokens de identificador**.</span><span class="sxs-lookup"><span data-stu-id="9c395-150">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="9c395-151">Los valores predeterminados restantes de la aplicación son aceptables para esta experiencia.</span><span class="sxs-lookup"><span data-stu-id="9c395-151">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="9c395-152">Seleccione el botón **Guardar**.</span><span class="sxs-lookup"><span data-stu-id="9c395-152">Select the **Save** button.</span></span>

<span data-ttu-id="9c395-153">En **permisos de API**:</span><span class="sxs-lookup"><span data-stu-id="9c395-153">In **API permissions**:</span></span>

1. <span data-ttu-id="9c395-154">Seleccione **Agregar un permiso** seguido de **mis API**.</span><span class="sxs-lookup"><span data-stu-id="9c395-154">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="9c395-155">Seleccione la *aplicación de API de servidor* en la columna **nombre** (por ejemplo, \*\* Blazor servidor AAD B2C\*\*).</span><span class="sxs-lookup"><span data-stu-id="9c395-155">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="9c395-156">Abra la lista de **API** .</span><span class="sxs-lookup"><span data-stu-id="9c395-156">Open the **API** list.</span></span>
1. <span data-ttu-id="9c395-157">Habilite el acceso a la API (por ejemplo, `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="9c395-157">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="9c395-158">Seleccione **Agregar permisos**.</span><span class="sxs-lookup"><span data-stu-id="9c395-158">Select **Add permissions**.</span></span>
1. <span data-ttu-id="9c395-159">Seleccione el botón **conceder contenido de administración para {nombre de inquilino}** .</span><span class="sxs-lookup"><span data-stu-id="9c395-159">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="9c395-160">Seleccione **Sí** para confirmar la acción.</span><span class="sxs-lookup"><span data-stu-id="9c395-160">Select **Yes** to confirm.</span></span>

<span data-ttu-id="9c395-161">En **Home**  >  **Azure ad B2C**  >  **flujos de usuario**:</span><span class="sxs-lookup"><span data-stu-id="9c395-161">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="9c395-162">Creación de un flujo de usuario de registro e inicio de sesión</span><span class="sxs-lookup"><span data-stu-id="9c395-162">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="9c395-163">Como mínimo, seleccione el **Application claims**  >  atributo de usuario**nombre para mostrar** de notificaciones de aplicación para rellenar el `context.User.Identity.Name` en el `LoginDisplay` componente (*Shared/LoginDisplay. Razor*).</span><span class="sxs-lookup"><span data-stu-id="9c395-163">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

<span data-ttu-id="9c395-164">Registre el nombre del flujo de usuario de inicio de sesión y de registro creado para la aplicación (por ejemplo, `B2C_1_signupsignin` ).</span><span class="sxs-lookup"><span data-stu-id="9c395-164">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="9c395-165">Creación de la aplicación</span><span class="sxs-lookup"><span data-stu-id="9c395-165">Create the app</span></span>

<span data-ttu-id="9c395-166">Reemplace los marcadores de posición en el siguiente comando por la información registrada anteriormente y ejecute el comando en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="9c395-166">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -ssp "{SIGN UP OR SIGN IN POLICY}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="9c395-167">Para especificar la ubicación de salida, que crea una carpeta de proyecto si no existe, incluya la opción output en el comando con una ruta de acceso (por ejemplo, `-o BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="9c395-167">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="9c395-168">El nombre de la carpeta también se convierte en parte del nombre del proyecto.</span><span class="sxs-lookup"><span data-stu-id="9c395-168">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="9c395-169">Pase el URI de ID. de aplicación a la `app-id-uri` opción, pero tenga en cuenta que es posible que se requiera un cambio de configuración en la aplicación cliente, que se describe en la sección [ámbitos de token de acceso](#access-token-scopes) .</span><span class="sxs-lookup"><span data-stu-id="9c395-169">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>
>
> <span data-ttu-id="9c395-170">Además, el ámbito configurado por la plantilla hospedada Blazor podría tener repetido el host del identificador URI de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="9c395-170">Additionally, the scope set up by the Hosted Blazor template might have the App ID URI host repeated.</span></span> <span data-ttu-id="9c395-171">Confirme que el ámbito configurado para la `DefaultAccessTokenScopes` colección es correcto en `Program.Main` (*Program.CS*) de la *aplicación cliente*.</span><span class="sxs-lookup"><span data-stu-id="9c395-171">Confirm that the scope configured for the `DefaultAccessTokenScopes` collection is correct in `Program.Main` (*Program.cs*) of the *Client app*.</span></span>

> [!NOTE]
> <span data-ttu-id="9c395-172">En el Azure portal, el URI de redireccionamiento web de configuración de la plataforma de autenticación de la *aplicación cliente* **Authentication**  >  **Platform configurations**  >  **Web**  >  **Redirect URI** se configura para el puerto 5001 para las aplicaciones que se ejecutan en el servidor Kestrel con la configuración predeterminada.</span><span class="sxs-lookup"><span data-stu-id="9c395-172">In the Azure portal, the *Client app's* **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="9c395-173">Si la *aplicación cliente* se ejecuta en un puerto de IIS Express aleatorio, el puerto de la aplicación se puede encontrar en las propiedades de la *aplicación de servidor* en el panel **depurar** .</span><span class="sxs-lookup"><span data-stu-id="9c395-173">If the *Client app* is run on a random IIS Express port, the port for the app can be found in the *Server app's* properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="9c395-174">Si el puerto no se configuró anteriormente con el puerto conocido *de la aplicación cliente* , vuelva al registro de la *aplicación cliente* en el Azure portal y actualice el URI de redirección con el puerto correcto.</span><span class="sxs-lookup"><span data-stu-id="9c395-174">If the port wasn't configured earlier with the *Client app's* known port, return to the *Client app's* registration in the Azure portal and update the redirect URI with the correct port.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="9c395-175">Configuración de la aplicación de servidor</span><span class="sxs-lookup"><span data-stu-id="9c395-175">Server app configuration</span></span>

<span data-ttu-id="9c395-176">*Esta sección pertenece a la aplicación de **servidor** de la solución.*</span><span class="sxs-lookup"><span data-stu-id="9c395-176">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="9c395-177">Paquete de autenticación</span><span class="sxs-lookup"><span data-stu-id="9c395-177">Authentication package</span></span>

<span data-ttu-id="9c395-178">El soporte para autenticar y autorizar llamadas a ASP.NET Core API Web se proporciona mediante el paquete [Microsoft. AspNetCore. Authentication. AzureADB2C. UI](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureADB2C.UI/) :</span><span class="sxs-lookup"><span data-stu-id="9c395-178">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the [Microsoft.AspNetCore.Authentication.AzureADB2C.UI](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureADB2C.UI/) package:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
  Version="3.1.4" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="9c395-179">Compatibilidad con el servicio de autenticación</span><span class="sxs-lookup"><span data-stu-id="9c395-179">Authentication service support</span></span>

<span data-ttu-id="9c395-180">El `AddAuthentication` método configura los servicios de autenticación dentro de la aplicación y configura el controlador de portador JWT como el método de autenticación predeterminado.</span><span class="sxs-lookup"><span data-stu-id="9c395-180">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="9c395-181">El <xref:Microsoft.AspNetCore.Authentication.AzureADB2CAuthenticationBuilderExtensions.AddAzureADB2CBearer%2A> método configura los parámetros específicos en el controlador de portador JWT necesario para validar los tokens emitidos por el Azure Active Directory B2C:</span><span class="sxs-lookup"><span data-stu-id="9c395-181">The <xref:Microsoft.AspNetCore.Authentication.AzureADB2CAuthenticationBuilderExtensions.AddAzureADB2CBearer%2A> method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory B2C:</span></span>

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

<span data-ttu-id="9c395-182"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>y <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> Asegúrese de que:</span><span class="sxs-lookup"><span data-stu-id="9c395-182"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> and <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> ensure that:</span></span>

* <span data-ttu-id="9c395-183">La aplicación intenta analizar y validar los tokens en las solicitudes entrantes.</span><span class="sxs-lookup"><span data-stu-id="9c395-183">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="9c395-184">Se produce un error en cualquier solicitud que intente obtener acceso a un recurso protegido sin credenciales adecuadas.</span><span class="sxs-lookup"><span data-stu-id="9c395-184">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="9c395-185">Usuario. Identity . Name</span><span class="sxs-lookup"><span data-stu-id="9c395-185">User.Identity.Name</span></span>

<span data-ttu-id="9c395-186">De forma predeterminada, `User.Identity.Name` no se rellena.</span><span class="sxs-lookup"><span data-stu-id="9c395-186">By default, the `User.Identity.Name` isn't populated.</span></span>

<span data-ttu-id="9c395-187">Para configurar la aplicación para recibir el valor del `name` tipo de notificaciones, configure el [TokenValidationParameters. NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) de <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> en `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="9c395-187">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="9c395-188">Configuración de la aplicación</span><span class="sxs-lookup"><span data-stu-id="9c395-188">App settings</span></span>

<span data-ttu-id="9c395-189">El *appsettings.jsen* el archivo contiene las opciones para configurar el controlador de portador JWT que se usa para validar los tokens de acceso.</span><span class="sxs-lookup"><span data-stu-id="9c395-189">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

```json
{
  "AzureAdB2C": {
    "Instance": "https://{TENANT}.b2clogin.com/",
    "ClientId": "{SERVER API APP CLIENT ID}",
    "Domain": "{TENANT DOMAIN}",
    "SignUpSignInPolicyId": "{SIGN UP OR SIGN IN POLICY}"
  }
}
```

<span data-ttu-id="9c395-190">Ejemplo:</span><span class="sxs-lookup"><span data-stu-id="9c395-190">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Instance": "https://contoso.b2clogin.com/",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "Domain": "contoso.onmicrosoft.com",
    "SignUpSignInPolicyId": "B2C_1_signupsignin1",
  }
}
```

### <a name="weatherforecast-controller"></a><span data-ttu-id="9c395-191">Controlador de WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="9c395-191">WeatherForecast controller</span></span>

<span data-ttu-id="9c395-192">El controlador WeatherForecast (*Controllers/WeatherForecastController. CS*) expone una API protegida con el [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atributo que se aplica al controlador.</span><span class="sxs-lookup"><span data-stu-id="9c395-192">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied to the controller.</span></span> <span data-ttu-id="9c395-193">Es **importante** comprender esto:</span><span class="sxs-lookup"><span data-stu-id="9c395-193">It's **important** to understand that:</span></span>

* <span data-ttu-id="9c395-194">El [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atributo de este controlador de API es lo único que protege esta API frente al acceso no autorizado.</span><span class="sxs-lookup"><span data-stu-id="9c395-194">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="9c395-195">El [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atributo que se usa en la Blazor aplicación webassembly solo sirve como una sugerencia a la aplicación a la que el usuario debe estar autorizado para que la aplicación funcione correctamente.</span><span class="sxs-lookup"><span data-stu-id="9c395-195">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="9c395-196">Configuración de la aplicación cliente</span><span class="sxs-lookup"><span data-stu-id="9c395-196">Client app configuration</span></span>

<span data-ttu-id="9c395-197">*Esta sección pertenece a la aplicación **cliente** de la solución.*</span><span class="sxs-lookup"><span data-stu-id="9c395-197">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="9c395-198">Paquete de autenticación</span><span class="sxs-lookup"><span data-stu-id="9c395-198">Authentication package</span></span>

<span data-ttu-id="9c395-199">Cuando se crea una aplicación para usar una cuenta de B2C individual ( `IndividualB2C` ), la aplicación recibe automáticamente una referencia de paquete para la [biblioteca de autenticación de Microsoft](/azure/active-directory/develop/msal-overview) ([Microsoft. Authentication. webassembly. msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span><span class="sxs-lookup"><span data-stu-id="9c395-199">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span></span> <span data-ttu-id="9c395-200">El paquete proporciona un conjunto de primitivas que ayudan a la aplicación a autenticar a los usuarios y obtener tokens para llamar a las API protegidas.</span><span class="sxs-lookup"><span data-stu-id="9c395-200">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="9c395-201">Si agrega la autenticación a una aplicación, agregue manualmente el paquete al archivo de proyecto de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="9c395-201">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="9c395-202">El paquete [Microsoft. Authentication. Webassembly. msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) agrega de manera transitiva el paquete [Microsoft. AspNetCore. Components. webassembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="9c395-202">The [Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package transitively adds the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="9c395-203">Compatibilidad con el servicio de autenticación</span><span class="sxs-lookup"><span data-stu-id="9c395-203">Authentication service support</span></span>

<span data-ttu-id="9c395-204"><xref:System.Net.Http.HttpClient>Se ha agregado compatibilidad con las instancias de que incluyen tokens de acceso al hacer solicitudes al proyecto de servidor.</span><span class="sxs-lookup"><span data-stu-id="9c395-204">Support for <xref:System.Net.Http.HttpClient> instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="9c395-205">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="9c395-205">*Program.cs*:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="9c395-206">La compatibilidad con la autenticación de usuarios se registra en el contenedor de servicios con el <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> método de extensión proporcionado por el paquete [Microsoft. Authentication. webassembly. msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) .</span><span class="sxs-lookup"><span data-stu-id="9c395-206">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package.</span></span> <span data-ttu-id="9c395-207">Este método configura los servicios necesarios para que la aplicación interactúe con el Identity proveedor (IP).</span><span class="sxs-lookup"><span data-stu-id="9c395-207">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="9c395-208">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="9c395-208">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="9c395-209">El <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> método acepta una devolución de llamada para configurar los parámetros necesarios para autenticar una aplicación.</span><span class="sxs-lookup"><span data-stu-id="9c395-209">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="9c395-210">Los valores necesarios para configurar la aplicación pueden obtenerse a partir de la configuración de AAD de Azure portal cuando se registra la aplicación.</span><span class="sxs-lookup"><span data-stu-id="9c395-210">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="9c395-211">La configuración se proporciona mediante *wwwroot/appsettings.jsen* el archivo:</span><span class="sxs-lookup"><span data-stu-id="9c395-211">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{TENANT DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

<span data-ttu-id="9c395-212">Ejemplo:</span><span class="sxs-lookup"><span data-stu-id="9c395-212">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": false
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="9c395-213">Ámbitos de token de acceso</span><span class="sxs-lookup"><span data-stu-id="9c395-213">Access token scopes</span></span>

<span data-ttu-id="9c395-214">Los ámbitos de token de acceso predeterminados representan la lista de ámbitos de token de acceso que son:</span><span class="sxs-lookup"><span data-stu-id="9c395-214">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="9c395-215">Se incluye de forma predeterminada en la solicitud de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="9c395-215">Included by default in the sign in request.</span></span>
* <span data-ttu-id="9c395-216">Se usa para aprovisionar un token de acceso inmediatamente después de la autenticación.</span><span class="sxs-lookup"><span data-stu-id="9c395-216">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="9c395-217">Todos los ámbitos deben pertenecer a la misma aplicación por reglas de Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="9c395-217">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="9c395-218">Se pueden agregar ámbitos adicionales para las aplicaciones de API adicionales según sea necesario:</span><span class="sxs-lookup"><span data-stu-id="9c395-218">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="9c395-219">Para obtener más información, consulte las siguientes secciones del artículo *escenarios adicionales* :</span><span class="sxs-lookup"><span data-stu-id="9c395-219">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="9c395-220">Solicitar tokens de acceso adicionales</span><span class="sxs-lookup"><span data-stu-id="9c395-220">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="9c395-221">Adjuntar tokens a solicitudes salientes</span><span class="sxs-lookup"><span data-stu-id="9c395-221">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)


### <a name="imports-file"></a><span data-ttu-id="9c395-222">Importar archivo</span><span class="sxs-lookup"><span data-stu-id="9c395-222">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="9c395-223">Página de índice</span><span class="sxs-lookup"><span data-stu-id="9c395-223">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="9c395-224">Componente de aplicación</span><span class="sxs-lookup"><span data-stu-id="9c395-224">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="9c395-225">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="9c395-225">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="9c395-226">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="9c395-226">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="9c395-227">Componente de autenticación</span><span class="sxs-lookup"><span data-stu-id="9c395-227">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="9c395-228">Componente FetchData</span><span class="sxs-lookup"><span data-stu-id="9c395-228">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="9c395-229">Ejecutar la aplicación</span><span class="sxs-lookup"><span data-stu-id="9c395-229">Run the app</span></span>

<span data-ttu-id="9c395-230">Ejecute la aplicación desde el proyecto de servidor.</span><span class="sxs-lookup"><span data-stu-id="9c395-230">Run the app from the Server project.</span></span> <span data-ttu-id="9c395-231">Al usar Visual Studio, puede:</span><span class="sxs-lookup"><span data-stu-id="9c395-231">When using Visual Studio, either:</span></span>

* <span data-ttu-id="9c395-232">Establezca la lista desplegable **proyectos de inicio** en la barra de herramientas en la aplicación de *API de servidor* y seleccione el botón **Ejecutar** .</span><span class="sxs-lookup"><span data-stu-id="9c395-232">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="9c395-233">Seleccione el proyecto de servidor en **Explorador de soluciones** y seleccione el botón **Ejecutar** en la barra de herramientas o inicie la aplicación en el menú **depurar** .</span><span class="sxs-lookup"><span data-stu-id="9c395-233">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="9c395-234">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="9c395-234">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="9c395-235">Solicitudes de API Web no autenticadas o no autorizadas en una aplicación con un cliente predeterminado seguro</span><span class="sxs-lookup"><span data-stu-id="9c395-235">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="9c395-236">Tutorial: Creación de un inquilino de Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="9c395-236">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="9c395-237">Documentación de la plataforma de identidad de Microsoft</span><span class="sxs-lookup"><span data-stu-id="9c395-237">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
