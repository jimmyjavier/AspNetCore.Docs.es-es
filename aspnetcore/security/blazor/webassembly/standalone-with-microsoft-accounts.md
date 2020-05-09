---
title: Protección de una Blazor aplicación independiente ASP.net Core webassembly con cuentas Microsoft
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/06/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/standalone-with-microsoft-accounts
ms.openlocfilehash: 6f884cbfc9ac50f38c415af522d3d09a3ef38712
ms.sourcegitcommit: 363e3a2a035f4082cb92e7b75ed150ba304258b3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/08/2020
ms.locfileid: "82976849"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-microsoft-accounts"></a><span data-ttu-id="4c670-102">Protección de una Blazor aplicación independiente ASP.net Core webassembly con cuentas Microsoft</span><span class="sxs-lookup"><span data-stu-id="4c670-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Microsoft Accounts</span></span>

<span data-ttu-id="4c670-103">Por [Javier Calvarro Nelson](https://github.com/javiercn) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="4c670-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="4c670-104">Para crear una Blazor aplicación independiente webassembly que use [cuentas de Microsoft con Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) para la autenticación:</span><span class="sxs-lookup"><span data-stu-id="4c670-104">To create a Blazor WebAssembly standalone app that uses [Microsoft Accounts with Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) for authentication:</span></span>

1. [<span data-ttu-id="4c670-105">Creación de un inquilino de AAD y una aplicación Web</span><span class="sxs-lookup"><span data-stu-id="4c670-105">Create an AAD tenant and web application</span></span>](/azure/active-directory/develop/v2-overview)

   <span data-ttu-id="4c670-106">Registre una aplicación de AAD en el área de**registros de aplicaciones** de **Azure Active Directory** > del Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="4c670-106">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

   <span data-ttu-id="4c670-107">1\.</span><span class="sxs-lookup"><span data-stu-id="4c670-107">1\.</span></span> <span data-ttu-id="4c670-108">Proporcione un **nombre** para la aplicación (por ejemplo, \*\* Blazor cliente AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="4c670-108">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span><br>
   <span data-ttu-id="4c670-109">2\.</span><span class="sxs-lookup"><span data-stu-id="4c670-109">2\.</span></span> <span data-ttu-id="4c670-110">En **tipos de cuenta compatibles**, seleccione **cuentas en cualquier directorio de la organización**.</span><span class="sxs-lookup"><span data-stu-id="4c670-110">In **Supported account types**, select **Accounts in any organizational directory**.</span></span><br>
   <span data-ttu-id="4c670-111">3 \.</span><span class="sxs-lookup"><span data-stu-id="4c670-111">3\.</span></span> <span data-ttu-id="4c670-112">Deje la lista desplegable **URI de redirección** establecida en **Web**y proporcione un URI de redireccionamiento de `https://localhost:5001/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="4c670-112">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span><br>
   <span data-ttu-id="4c670-113">4 \.</span><span class="sxs-lookup"><span data-stu-id="4c670-113">4\.</span></span> <span data-ttu-id="4c670-114">Deshabilite **Permissions** > la casilla**conceder permisos Grant admin to OpenID y offline_access permisos** .</span><span class="sxs-lookup"><span data-stu-id="4c670-114">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span><br>
   <span data-ttu-id="4c670-115">5 \.</span><span class="sxs-lookup"><span data-stu-id="4c670-115">5\.</span></span> <span data-ttu-id="4c670-116">Seleccione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="4c670-116">Select **Register**.</span></span>

   <span data-ttu-id="4c670-117">En **Authentication** > **Platform configurations**configuración > de la plataforma de autenticación**Web**:</span><span class="sxs-lookup"><span data-stu-id="4c670-117">In **Authentication** > **Platform configurations** > **Web**:</span></span>

   <span data-ttu-id="4c670-118">1\.</span><span class="sxs-lookup"><span data-stu-id="4c670-118">1\.</span></span> <span data-ttu-id="4c670-119">Confirme que el **URI de redirección** de `https://localhost:5001/authentication/login-callback` está presente.</span><span class="sxs-lookup"><span data-stu-id="4c670-119">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span><br>
   <span data-ttu-id="4c670-120">2\.</span><span class="sxs-lookup"><span data-stu-id="4c670-120">2\.</span></span> <span data-ttu-id="4c670-121">En **concesión implícita**, active las casillas de verificación de **tokens de acceso** y **tokens de identificador**.</span><span class="sxs-lookup"><span data-stu-id="4c670-121">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span><br>
   <span data-ttu-id="4c670-122">3 \.</span><span class="sxs-lookup"><span data-stu-id="4c670-122">3\.</span></span> <span data-ttu-id="4c670-123">Los valores predeterminados restantes de la aplicación son aceptables para esta experiencia.</span><span class="sxs-lookup"><span data-stu-id="4c670-123">The remaining defaults for the app are acceptable for this experience.</span></span><br>
   <span data-ttu-id="4c670-124">4 \.</span><span class="sxs-lookup"><span data-stu-id="4c670-124">4\.</span></span> <span data-ttu-id="4c670-125">Seleccione el botón **Guardar**.</span><span class="sxs-lookup"><span data-stu-id="4c670-125">Select the **Save** button.</span></span>

   <span data-ttu-id="4c670-126">Registre el identificador de aplicación (ID. de cliente) ( `11111111-1111-1111-1111-111111111111`por ejemplo,).</span><span class="sxs-lookup"><span data-stu-id="4c670-126">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

1. <span data-ttu-id="4c670-127">Reemplace los marcadores de posición en el siguiente comando por la información registrada anteriormente y ejecute el comando en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="4c670-127">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common"
   ```

   <span data-ttu-id="4c670-128">Para especificar la ubicación de salida, que crea una carpeta de proyecto si no existe, incluya la opción output en el comando con una ruta de acceso ( `-o BlazorSample`por ejemplo,).</span><span class="sxs-lookup"><span data-stu-id="4c670-128">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="4c670-129">El nombre de la carpeta también se convierte en parte del nombre del proyecto.</span><span class="sxs-lookup"><span data-stu-id="4c670-129">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="4c670-130">Después de crear la aplicación, debe poder:</span><span class="sxs-lookup"><span data-stu-id="4c670-130">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="4c670-131">Inicie sesión en la aplicación con una cuenta de Microsoft.</span><span class="sxs-lookup"><span data-stu-id="4c670-131">Log into the app using a Microsoft Account.</span></span>
* <span data-ttu-id="4c670-132">Solicite tokens de acceso para las API de Microsoft con el mismo enfoque Blazor que para las aplicaciones independientes, siempre y cuando haya configurado la aplicación correctamente.</span><span class="sxs-lookup"><span data-stu-id="4c670-132">Request access tokens for Microsoft APIs using the same approach as for standalone Blazor apps provided that you have configured the app correctly.</span></span> <span data-ttu-id="4c670-133">Para obtener más información, consulte [Inicio rápido: configurar una aplicación para exponer API Web](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span><span class="sxs-lookup"><span data-stu-id="4c670-133">For more information, see [Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="4c670-134">Paquete de autenticación</span><span class="sxs-lookup"><span data-stu-id="4c670-134">Authentication package</span></span>

<span data-ttu-id="4c670-135">Cuando se crea una aplicación para usar cuentas profesionales o educativas (`SingleOrg`), la aplicación recibe automáticamente una referencia de paquete para la biblioteca de autenticación`Microsoft.Authentication.WebAssembly.Msal`de [Microsoft](/azure/active-directory/develop/msal-overview) ().</span><span class="sxs-lookup"><span data-stu-id="4c670-135">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="4c670-136">El paquete proporciona un conjunto de primitivas que ayudan a la aplicación a autenticar a los usuarios y obtener tokens para llamar a las API protegidas.</span><span class="sxs-lookup"><span data-stu-id="4c670-136">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="4c670-137">Si agrega la autenticación a una aplicación, agregue manualmente el paquete al archivo de proyecto de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="4c670-137">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="4c670-138">Reemplace `{VERSION}` en la referencia de paquete anterior por la versión del `Microsoft.AspNetCore.Blazor.Templates` paquete que se muestra <xref:blazor/get-started> en el artículo.</span><span class="sxs-lookup"><span data-stu-id="4c670-138">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="4c670-139">El `Microsoft.Authentication.WebAssembly.Msal` paquete agrega de manera transitiva el `Microsoft.AspNetCore.Components.WebAssembly.Authentication` paquete a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4c670-139">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="4c670-140">Compatibilidad con el servicio de autenticación</span><span class="sxs-lookup"><span data-stu-id="4c670-140">Authentication service support</span></span>

<span data-ttu-id="4c670-141">La compatibilidad con la autenticación de usuarios se registra en el contenedor de `AddMsalAuthentication` servicios con el método de `Microsoft.Authentication.WebAssembly.Msal` extensión proporcionado por el paquete.</span><span class="sxs-lookup"><span data-stu-id="4c670-141">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="4c670-142">Este método configura todos los servicios necesarios para que la aplicación interactúe con el Identity proveedor (IP).</span><span class="sxs-lookup"><span data-stu-id="4c670-142">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="4c670-143">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="4c670-143">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="4c670-144">El `AddMsalAuthentication` método acepta una devolución de llamada para configurar los parámetros necesarios para autenticar una aplicación.</span><span class="sxs-lookup"><span data-stu-id="4c670-144">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="4c670-145">Los valores necesarios para configurar la aplicación se pueden obtener a partir de la configuración de las cuentas de Microsoft cuando se registra la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4c670-145">The values required for configuring the app can be obtained from the Microsoft Accounts configuration when you register the app.</span></span>

<span data-ttu-id="4c670-146">La configuración se proporciona mediante el archivo *wwwroot/appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="4c670-146">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="4c670-147">Ejemplo:</span><span class="sxs-lookup"><span data-stu-id="4c670-147">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd"
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="4c670-148">Ámbitos de token de acceso</span><span class="sxs-lookup"><span data-stu-id="4c670-148">Access token scopes</span></span>

<span data-ttu-id="4c670-149">La Blazor plantilla webassembly no configura automáticamente la aplicación para solicitar un token de acceso para una API segura.</span><span class="sxs-lookup"><span data-stu-id="4c670-149">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="4c670-150">Para aprovisionar un token de acceso como parte del flujo de inicio de sesión, agregue el ámbito a los ámbitos de token de acceso `MsalProviderOptions`predeterminados de:</span><span class="sxs-lookup"><span data-stu-id="4c670-150">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="4c670-151">Si el Azure Portal proporciona un URI de ámbito y **la aplicación produce una excepción no controlada** cuando recibe una respuesta *401 no autorizada* de la API, intente usar un URI de ámbito que no incluya el esquema y el host.</span><span class="sxs-lookup"><span data-stu-id="4c670-151">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="4c670-152">Por ejemplo, el Azure Portal puede proporcionar uno de los siguientes formatos de URI de ámbito:</span><span class="sxs-lookup"><span data-stu-id="4c670-152">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="4c670-153">Proporcione el URI del ámbito sin el esquema y el host:</span><span class="sxs-lookup"><span data-stu-id="4c670-153">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="4c670-154">Para obtener más información, consulte las siguientes secciones del artículo *escenarios adicionales* :</span><span class="sxs-lookup"><span data-stu-id="4c670-154">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="4c670-155">Solicitar tokens de acceso adicionales</span><span class="sxs-lookup"><span data-stu-id="4c670-155">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="4c670-156">Adjuntar tokens a solicitudes salientes</span><span class="sxs-lookup"><span data-stu-id="4c670-156">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="4c670-157">Importar archivo</span><span class="sxs-lookup"><span data-stu-id="4c670-157">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="4c670-158">Página de índice</span><span class="sxs-lookup"><span data-stu-id="4c670-158">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="4c670-159">Componente de aplicación</span><span class="sxs-lookup"><span data-stu-id="4c670-159">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="4c670-160">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="4c670-160">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="4c670-161">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="4c670-161">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="4c670-162">Componente de autenticación</span><span class="sxs-lookup"><span data-stu-id="4c670-162">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="4c670-163">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="4c670-163">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:security/blazor/webassembly/aad-groups-roles>
* [<span data-ttu-id="4c670-164">Inicio rápido: Registro de una aplicación en la plataforma de identidad de Microsoft</span><span class="sxs-lookup"><span data-stu-id="4c670-164">Quickstart: Register an application with the Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [<span data-ttu-id="4c670-165">Inicio rápido: Configuración de una aplicación para exponer las API web</span><span class="sxs-lookup"><span data-stu-id="4c670-165">Quickstart: Configure an application to expose web APIs</span></span>](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)
