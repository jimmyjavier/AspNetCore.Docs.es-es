---
title: Protección de una Blazor aplicación independiente ASP.net Core webassembly con cuentas Microsoft
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-microsoft-accounts
ms.openlocfilehash: a12cc8f94a97882e4a0ac3a6553628df4da2e82c
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/24/2020
ms.locfileid: "82111193"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-microsoft-accounts"></a><span data-ttu-id="59e54-102">Protección de una Blazor aplicación independiente ASP.net Core webassembly con cuentas Microsoft</span><span class="sxs-lookup"><span data-stu-id="59e54-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Microsoft Accounts</span></span>

<span data-ttu-id="59e54-103">Por [Javier Calvarro Nelson](https://github.com/javiercn) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="59e54-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

> [!NOTE]
> <span data-ttu-id="59e54-104">Las instrucciones de este artículo se aplican a ASP.NET Core 3,2 Preview 4.</span><span class="sxs-lookup"><span data-stu-id="59e54-104">The guidance in this article applies to ASP.NET Core 3.2 Preview 4.</span></span> <span data-ttu-id="59e54-105">Este tema se actualizará a la versión preliminar 5 del viernes, 24 de abril.</span><span class="sxs-lookup"><span data-stu-id="59e54-105">This topic will be updated to cover Preview 5 on Friday, April 24.</span></span>

<span data-ttu-id="59e54-106">Para crear una Blazor aplicación independiente webassembly que use [cuentas de Microsoft con Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) para la autenticación:</span><span class="sxs-lookup"><span data-stu-id="59e54-106">To create a Blazor WebAssembly standalone app that uses [Microsoft Accounts with Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) for authentication:</span></span>

1. [<span data-ttu-id="59e54-107">Creación de un inquilino de AAD y una aplicación Web</span><span class="sxs-lookup"><span data-stu-id="59e54-107">Create an AAD tenant and web application</span></span>](/azure/active-directory/develop/v2-overview)

   <span data-ttu-id="59e54-108">Registre una aplicación de AAD en el área de**registros de aplicaciones** de **Azure Active Directory** > del Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="59e54-108">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

   <span data-ttu-id="59e54-109">1 \.</span><span class="sxs-lookup"><span data-stu-id="59e54-109">1\.</span></span> <span data-ttu-id="59e54-110">Proporcione un **nombre** para la aplicación (por ejemplo, \*\* Blazor cliente AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="59e54-110">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span><br>
   <span data-ttu-id="59e54-111">2 \.</span><span class="sxs-lookup"><span data-stu-id="59e54-111">2\.</span></span> <span data-ttu-id="59e54-112">En **tipos de cuenta compatibles**, seleccione **cuentas en cualquier directorio de la organización**.</span><span class="sxs-lookup"><span data-stu-id="59e54-112">In **Supported account types**, select **Accounts in any organizational directory**.</span></span><br>
   <span data-ttu-id="59e54-113">3 \.</span><span class="sxs-lookup"><span data-stu-id="59e54-113">3\.</span></span> <span data-ttu-id="59e54-114">Deje la lista desplegable **URI de redirección** establecida en **Web**y proporcione un URI de redireccionamiento de `https://localhost:5001/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="59e54-114">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span><br>
   <span data-ttu-id="59e54-115">4 \.</span><span class="sxs-lookup"><span data-stu-id="59e54-115">4\.</span></span> <span data-ttu-id="59e54-116">Deshabilite **Permissions** > la casilla**conceder permisos Grant admin to OpenID y offline_access permisos** .</span><span class="sxs-lookup"><span data-stu-id="59e54-116">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span><br>
   <span data-ttu-id="59e54-117">5 \.</span><span class="sxs-lookup"><span data-stu-id="59e54-117">5\.</span></span> <span data-ttu-id="59e54-118">Seleccione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="59e54-118">Select **Register**.</span></span>

   <span data-ttu-id="59e54-119">En **Authentication** > **Platform configurations**configuración > de la plataforma de autenticación**Web**:</span><span class="sxs-lookup"><span data-stu-id="59e54-119">In **Authentication** > **Platform configurations** > **Web**:</span></span>

   <span data-ttu-id="59e54-120">1 \.</span><span class="sxs-lookup"><span data-stu-id="59e54-120">1\.</span></span> <span data-ttu-id="59e54-121">Confirme que el **URI de redirección** de `https://localhost:5001/authentication/login-callback` está presente.</span><span class="sxs-lookup"><span data-stu-id="59e54-121">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span><br>
   <span data-ttu-id="59e54-122">2 \.</span><span class="sxs-lookup"><span data-stu-id="59e54-122">2\.</span></span> <span data-ttu-id="59e54-123">En **concesión implícita**, active las casillas de verificación de **tokens de acceso** y **tokens de identificador**.</span><span class="sxs-lookup"><span data-stu-id="59e54-123">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span><br>
   <span data-ttu-id="59e54-124">3 \.</span><span class="sxs-lookup"><span data-stu-id="59e54-124">3\.</span></span> <span data-ttu-id="59e54-125">Los valores predeterminados restantes de la aplicación son aceptables para esta experiencia.</span><span class="sxs-lookup"><span data-stu-id="59e54-125">The remaining defaults for the app are acceptable for this experience.</span></span><br>
   <span data-ttu-id="59e54-126">4 \.</span><span class="sxs-lookup"><span data-stu-id="59e54-126">4\.</span></span> <span data-ttu-id="59e54-127">Seleccione el botón **Guardar**.</span><span class="sxs-lookup"><span data-stu-id="59e54-127">Select the **Save** button.</span></span>

   <span data-ttu-id="59e54-128">Registre el identificador de aplicación (ID. de cliente) ( `11111111-1111-1111-1111-111111111111`por ejemplo,).</span><span class="sxs-lookup"><span data-stu-id="59e54-128">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

1. <span data-ttu-id="59e54-129">Reemplace los marcadores de posición en el siguiente comando por la información registrada anteriormente y ejecute el comando en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="59e54-129">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common"
   ```

   <span data-ttu-id="59e54-130">Para especificar la ubicación de salida, que crea una carpeta de proyecto si no existe, incluya la opción output en el comando con una ruta de acceso ( `-o BlazorSample`por ejemplo,).</span><span class="sxs-lookup"><span data-stu-id="59e54-130">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="59e54-131">El nombre de la carpeta también se convierte en parte del nombre del proyecto.</span><span class="sxs-lookup"><span data-stu-id="59e54-131">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="59e54-132">Después de crear la aplicación, debe poder:</span><span class="sxs-lookup"><span data-stu-id="59e54-132">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="59e54-133">Inicie sesión en la aplicación con una cuenta de Microsoft.</span><span class="sxs-lookup"><span data-stu-id="59e54-133">Log into the app using a Microsoft Account.</span></span>
* <span data-ttu-id="59e54-134">Solicite tokens de acceso para las API de Microsoft con el mismo enfoque Blazor que para las aplicaciones independientes, siempre y cuando haya configurado la aplicación correctamente.</span><span class="sxs-lookup"><span data-stu-id="59e54-134">Request access tokens for Microsoft APIs using the same approach as for standalone Blazor apps provided that you have configured the app correctly.</span></span> <span data-ttu-id="59e54-135">Para obtener más información, consulte [Inicio rápido: configurar una aplicación para exponer API Web](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span><span class="sxs-lookup"><span data-stu-id="59e54-135">For more information, see [Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="59e54-136">Paquete de autenticación</span><span class="sxs-lookup"><span data-stu-id="59e54-136">Authentication package</span></span>

<span data-ttu-id="59e54-137">Cuando se crea una aplicación para usar cuentas profesionales o educativas (`SingleOrg`), la aplicación recibe automáticamente una referencia de paquete para la biblioteca de autenticación`Microsoft.Authentication.WebAssembly.Msal`de [Microsoft](/azure/active-directory/develop/msal-overview) ().</span><span class="sxs-lookup"><span data-stu-id="59e54-137">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="59e54-138">El paquete proporciona un conjunto de primitivas que ayudan a la aplicación a autenticar a los usuarios y obtener tokens para llamar a las API protegidas.</span><span class="sxs-lookup"><span data-stu-id="59e54-138">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="59e54-139">Si agrega la autenticación a una aplicación, agregue manualmente el paquete al archivo de proyecto de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="59e54-139">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="59e54-140">Reemplace `{VERSION}` en la referencia de paquete anterior por la versión del `Microsoft.AspNetCore.Blazor.Templates` paquete que se muestra <xref:blazor/get-started> en el artículo.</span><span class="sxs-lookup"><span data-stu-id="59e54-140">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="59e54-141">El `Microsoft.Authentication.WebAssembly.Msal` paquete agrega de manera transitiva el `Microsoft.AspNetCore.Components.WebAssembly.Authentication` paquete a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="59e54-141">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="59e54-142">Compatibilidad con el servicio de autenticación</span><span class="sxs-lookup"><span data-stu-id="59e54-142">Authentication service support</span></span>

<span data-ttu-id="59e54-143">La compatibilidad con la autenticación de usuarios se registra en el contenedor de `AddMsalAuthentication` servicios con el método de `Microsoft.Authentication.WebAssembly.Msal` extensión proporcionado por el paquete.</span><span class="sxs-lookup"><span data-stu-id="59e54-143">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="59e54-144">Este método configura todos los servicios necesarios para que la aplicación interactúe con el proveedor de identidades (IP).</span><span class="sxs-lookup"><span data-stu-id="59e54-144">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="59e54-145">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="59e54-145">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "{AUTHORITY}";
    authentication.ClientId = "{CLIENT ID}";
});
```

<span data-ttu-id="59e54-146">El `AddMsalAuthentication` método acepta una devolución de llamada para configurar los parámetros necesarios para autenticar una aplicación.</span><span class="sxs-lookup"><span data-stu-id="59e54-146">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="59e54-147">Los valores necesarios para configurar la aplicación se pueden obtener a partir de la configuración de las cuentas de Microsoft cuando se registra la aplicación.</span><span class="sxs-lookup"><span data-stu-id="59e54-147">The values required for configuring the app can be obtained from the Microsoft Accounts configuration when you register the app.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="59e54-148">Ámbitos de token de acceso</span><span class="sxs-lookup"><span data-stu-id="59e54-148">Access token scopes</span></span>

<span data-ttu-id="59e54-149">La Blazor plantilla webassembly no configura automáticamente la aplicación para solicitar un token de acceso para una API segura.</span><span class="sxs-lookup"><span data-stu-id="59e54-149">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="59e54-150">Para aprovisionar un token como parte del flujo de inicio de sesión, agregue el ámbito a los ámbitos de token de acceso predeterminados de `MsalProviderOptions`:</span><span class="sxs-lookup"><span data-stu-id="59e54-150">To provision a token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="59e54-151">Si el Azure Portal proporciona un URI de ámbito y **la aplicación produce una excepción no controlada** cuando recibe una respuesta *401 no autorizada* de la API, intente usar un URI de ámbito que no incluya el esquema y el host.</span><span class="sxs-lookup"><span data-stu-id="59e54-151">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="59e54-152">Por ejemplo, el Azure Portal puede proporcionar uno de los siguientes formatos de URI de ámbito:</span><span class="sxs-lookup"><span data-stu-id="59e54-152">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="59e54-153">Proporcione el URI del ámbito sin el esquema y el host:</span><span class="sxs-lookup"><span data-stu-id="59e54-153">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="59e54-154">Para obtener más información, vea <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span><span class="sxs-lookup"><span data-stu-id="59e54-154">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

<!--
    For more information, see <xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests>.
-->

## <a name="imports-file"></a><span data-ttu-id="59e54-155">Importar archivo</span><span class="sxs-lookup"><span data-stu-id="59e54-155">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="59e54-156">Página de índice</span><span class="sxs-lookup"><span data-stu-id="59e54-156">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="59e54-157">Componente de aplicación</span><span class="sxs-lookup"><span data-stu-id="59e54-157">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="59e54-158">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="59e54-158">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="59e54-159">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="59e54-159">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="59e54-160">Componente de autenticación</span><span class="sxs-lookup"><span data-stu-id="59e54-160">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="59e54-161">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="59e54-161">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="59e54-162">Inicio rápido: Registro de una aplicación en la plataforma de identidad de Microsoft</span><span class="sxs-lookup"><span data-stu-id="59e54-162">Quickstart: Register an application with the Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [<span data-ttu-id="59e54-163">Inicio rápido: configurar una aplicación para exponer las API Web</span><span class="sxs-lookup"><span data-stu-id="59e54-163">Quickstart: Configure an application to expose web APIs</span></span>](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)
