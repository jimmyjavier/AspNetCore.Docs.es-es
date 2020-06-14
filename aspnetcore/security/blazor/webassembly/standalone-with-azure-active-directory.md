---
title: Protección de una Blazor aplicación independiente ASP.net Core Webassembly con Azure Active Directory
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
uid: security/blazor/webassembly/standalone-with-azure-active-directory
ms.openlocfilehash: 6d8db8f546e21ccce27cca06b4112ebc52653782
ms.sourcegitcommit: d243fadeda20ad4f142ea60301ae5f5e0d41ed60
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/12/2020
ms.locfileid: "84724385"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-azure-active-directory"></a><span data-ttu-id="ad87f-102">Protección de una Blazor aplicación independiente ASP.net Core Webassembly con Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="ad87f-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory</span></span>

<span data-ttu-id="ad87f-103">Por [Javier Calvarro Nelson](https://github.com/javiercn) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="ad87f-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="ad87f-104">Para crear una Blazor aplicación independiente Webassembly que use [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) para la autenticación:</span><span class="sxs-lookup"><span data-stu-id="ad87f-104">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication:</span></span>

<span data-ttu-id="ad87f-105">[Cree un inquilino de AAD y una aplicación web](/azure/active-directory/develop/v2-overview):</span><span class="sxs-lookup"><span data-stu-id="ad87f-105">[Create an AAD tenant and web application](/azure/active-directory/develop/v2-overview):</span></span>

<span data-ttu-id="ad87f-106">Registre una aplicación de AAD en **Azure Active Directory**el  >  área de**registros de aplicaciones** de Azure Active Directory del Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="ad87f-106">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="ad87f-107">Proporcione un **nombre** para la aplicación (por ejemplo, \*\* Blazor AAD independiente\*\*).</span><span class="sxs-lookup"><span data-stu-id="ad87f-107">Provide a **Name** for the app (for example, **Blazor Standalone AAD**).</span></span>
1. <span data-ttu-id="ad87f-108">Elija un **tipo de cuenta compatible**.</span><span class="sxs-lookup"><span data-stu-id="ad87f-108">Choose a **Supported account types**.</span></span> <span data-ttu-id="ad87f-109">Puede seleccionar **cuentas en este directorio de la organización solo** para esta experiencia.</span><span class="sxs-lookup"><span data-stu-id="ad87f-109">You may select **Accounts in this organizational directory only** for this experience.</span></span>
1. <span data-ttu-id="ad87f-110">Deje la lista desplegable **URI de redirección** establecida en **Web** y proporcione el siguiente URI de redirección: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="ad87f-110">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="ad87f-111">El puerto predeterminado para una aplicación que se ejecuta en Kestrel es 5001.</span><span class="sxs-lookup"><span data-stu-id="ad87f-111">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="ad87f-112">Si la aplicación se ejecuta en otro puerto Kestrel, use el puerto de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ad87f-112">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="ad87f-113">Por IIS Express, el puerto generado de forma aleatoria para la aplicación se puede encontrar en las propiedades de la aplicación en el panel **depurar** .</span><span class="sxs-lookup"><span data-stu-id="ad87f-113">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="ad87f-114">Dado que la aplicación no existe en este momento y no se conoce el puerto IIS Express, vuelva a este paso después de crear la aplicación y actualice el URI de redirección.</span><span class="sxs-lookup"><span data-stu-id="ad87f-114">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="ad87f-115">Más adelante en este tema aparece un comentario para recordar a IIS Express usuarios que actualicen el URI de redirección.</span><span class="sxs-lookup"><span data-stu-id="ad87f-115">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="ad87f-116">Deshabilite **Permissions**la  >  casilla conceder permisos de**consentimiento de administrador a OpenID y offline_access** .</span><span class="sxs-lookup"><span data-stu-id="ad87f-116">Disable the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="ad87f-117">Seleccione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="ad87f-117">Select **Register**.</span></span>

<span data-ttu-id="ad87f-118">Registre la siguiente información:</span><span class="sxs-lookup"><span data-stu-id="ad87f-118">Record the following information:</span></span>

* <span data-ttu-id="ad87f-119">IDENTIFICADOR de aplicación (ID. de cliente) (por ejemplo, `11111111-1111-1111-1111-111111111111` )</span><span class="sxs-lookup"><span data-stu-id="ad87f-119">Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="ad87f-120">IDENTIFICADOR de directorio (identificador de inquilino) (por ejemplo, `22222222-2222-2222-2222-222222222222` )</span><span class="sxs-lookup"><span data-stu-id="ad87f-120">Directory ID (Tenant ID) (for example, `22222222-2222-2222-2222-222222222222`)</span></span>

<span data-ttu-id="ad87f-121">En configuración de la plataforma de **autenticación**  >  **Platform configurations**  >  **Web**:</span><span class="sxs-lookup"><span data-stu-id="ad87f-121">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="ad87f-122">Confirme que el **URI de redirección** de `https://localhost:{PORT}/authentication/login-callback` está presente.</span><span class="sxs-lookup"><span data-stu-id="ad87f-122">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="ad87f-123">En **concesión implícita**, active las casillas de verificación de **tokens de acceso** y **tokens de identificador**.</span><span class="sxs-lookup"><span data-stu-id="ad87f-123">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="ad87f-124">Los valores predeterminados restantes de la aplicación son aceptables para esta experiencia.</span><span class="sxs-lookup"><span data-stu-id="ad87f-124">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="ad87f-125">Seleccione el botón **Guardar**.</span><span class="sxs-lookup"><span data-stu-id="ad87f-125">Select the **Save** button.</span></span>

<span data-ttu-id="ad87f-126">Crear la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ad87f-126">Create the app.</span></span> <span data-ttu-id="ad87f-127">Reemplace los marcadores de posición en el siguiente comando por la información registrada anteriormente y ejecute el comando en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="ad87f-127">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="ad87f-128">Para especificar la ubicación de salida, que crea una carpeta de proyecto si no existe, incluya la opción output en el comando con una ruta de acceso (por ejemplo, `-o BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="ad87f-128">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="ad87f-129">El nombre de la carpeta también se convierte en parte del nombre del proyecto.</span><span class="sxs-lookup"><span data-stu-id="ad87f-129">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="ad87f-130">En el Azure portal, el URI de **Authentication**redireccionamiento web de configuración de la plataforma de autenticación de la aplicación  >  **Platform configurations**  >  **Web**  >  **Redirect URI** se configura para el puerto 5001 para las aplicaciones que se ejecutan en el servidor de Kestrel con la configuración predeterminada.</span><span class="sxs-lookup"><span data-stu-id="ad87f-130">In the Azure portal, the app's **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="ad87f-131">Si la aplicación se ejecuta en un puerto de IIS Express aleatorio, el puerto de la aplicación se puede encontrar en las propiedades de la aplicación en el panel de **depuración** .</span><span class="sxs-lookup"><span data-stu-id="ad87f-131">If the app is run on a random IIS Express port, the port for the app can be found in the app's properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="ad87f-132">Si el puerto no se configuró anteriormente con el puerto conocido de la aplicación, vuelva al registro de la aplicación en el Azure Portal y actualice el URI de redirección con el puerto correcto.</span><span class="sxs-lookup"><span data-stu-id="ad87f-132">If the port wasn't configured earlier with the app's known port, return to the app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

<span data-ttu-id="ad87f-133">Después de crear la aplicación, debe poder:</span><span class="sxs-lookup"><span data-stu-id="ad87f-133">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="ad87f-134">Inicie sesión en la aplicación con una cuenta de usuario de AAD.</span><span class="sxs-lookup"><span data-stu-id="ad87f-134">Log into the app using an AAD user account.</span></span>
* <span data-ttu-id="ad87f-135">Solicitar tokens de acceso para las API de Microsoft.</span><span class="sxs-lookup"><span data-stu-id="ad87f-135">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="ad87f-136">Para más información, consulte:</span><span class="sxs-lookup"><span data-stu-id="ad87f-136">For more information, see:</span></span>
  * [<span data-ttu-id="ad87f-137">Ámbitos de token de acceso</span><span class="sxs-lookup"><span data-stu-id="ad87f-137">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="ad87f-138">[Inicio rápido: configurar una aplicación para exponer las API Web](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span><span class="sxs-lookup"><span data-stu-id="ad87f-138">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="ad87f-139">Paquete de autenticación</span><span class="sxs-lookup"><span data-stu-id="ad87f-139">Authentication package</span></span>

<span data-ttu-id="ad87f-140">Cuando se crea una aplicación para usar cuentas profesionales o educativas ( `SingleOrg` ), la aplicación recibe automáticamente una referencia de paquete para la [biblioteca de autenticación de Microsoft](/azure/active-directory/develop/msal-overview) ([Microsoft. Authentication. webassembly. msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span><span class="sxs-lookup"><span data-stu-id="ad87f-140">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span></span> <span data-ttu-id="ad87f-141">El paquete proporciona un conjunto de primitivas que ayudan a la aplicación a autenticar a los usuarios y obtener tokens para llamar a las API protegidas.</span><span class="sxs-lookup"><span data-stu-id="ad87f-141">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="ad87f-142">Si agrega la autenticación a una aplicación, agregue manualmente el paquete al archivo de proyecto de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="ad87f-142">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="ad87f-143">El paquete [Microsoft. Authentication. Webassembly. msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) agrega de manera transitiva el paquete [Microsoft. AspNetCore. Components. webassembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ad87f-143">The [Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package transitively adds the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="ad87f-144">Compatibilidad con el servicio de autenticación</span><span class="sxs-lookup"><span data-stu-id="ad87f-144">Authentication service support</span></span>

<span data-ttu-id="ad87f-145">La compatibilidad con la autenticación de usuarios se registra en el contenedor de servicios con el <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> método de extensión proporcionado por el paquete [Microsoft. Authentication. webassembly. msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) .</span><span class="sxs-lookup"><span data-stu-id="ad87f-145">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package.</span></span> <span data-ttu-id="ad87f-146">Este método configura los servicios necesarios para que la aplicación interactúe con el Identity proveedor (IP).</span><span class="sxs-lookup"><span data-stu-id="ad87f-146">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="ad87f-147">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="ad87f-147">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="ad87f-148">El <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> método acepta una devolución de llamada para configurar los parámetros necesarios para autenticar una aplicación.</span><span class="sxs-lookup"><span data-stu-id="ad87f-148">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="ad87f-149">Los valores necesarios para configurar la aplicación se pueden obtener a partir de la configuración de AAD al registrar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ad87f-149">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="ad87f-150">La configuración se proporciona mediante *wwwroot/appsettings.jsen* el archivo:</span><span class="sxs-lookup"><span data-stu-id="ad87f-150">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="ad87f-151">Ejemplo:</span><span class="sxs-lookup"><span data-stu-id="ad87f-151">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="ad87f-152">Ámbitos de token de acceso</span><span class="sxs-lookup"><span data-stu-id="ad87f-152">Access token scopes</span></span>

<span data-ttu-id="ad87f-153">La Blazor plantilla Webassembly no configura automáticamente la aplicación para solicitar un token de acceso para una API segura.</span><span class="sxs-lookup"><span data-stu-id="ad87f-153">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="ad87f-154">Para aprovisionar un token de acceso como parte del flujo de inicio de sesión, agregue el ámbito a los ámbitos de token de acceso predeterminados de <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> :</span><span class="sxs-lookup"><span data-stu-id="ad87f-154">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="ad87f-155">Para obtener más información, consulte las siguientes secciones del artículo *escenarios adicionales* :</span><span class="sxs-lookup"><span data-stu-id="ad87f-155">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="ad87f-156">Solicitar tokens de acceso adicionales</span><span class="sxs-lookup"><span data-stu-id="ad87f-156">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="ad87f-157">Adjuntar tokens a solicitudes salientes</span><span class="sxs-lookup"><span data-stu-id="ad87f-157">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="ad87f-158">Importar archivo</span><span class="sxs-lookup"><span data-stu-id="ad87f-158">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="ad87f-159">Página de índice</span><span class="sxs-lookup"><span data-stu-id="ad87f-159">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="ad87f-160">Componente de aplicación</span><span class="sxs-lookup"><span data-stu-id="ad87f-160">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="ad87f-161">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="ad87f-161">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="ad87f-162">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="ad87f-162">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="ad87f-163">Componente de autenticación</span><span class="sxs-lookup"><span data-stu-id="ad87f-163">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="ad87f-164">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="ad87f-164">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="ad87f-165">Solicitudes de API Web no autenticadas o no autorizadas en una aplicación con un cliente predeterminado seguro</span><span class="sxs-lookup"><span data-stu-id="ad87f-165">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/blazor/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="ad87f-166">Documentación de la plataforma de identidad de Microsoft</span><span class="sxs-lookup"><span data-stu-id="ad87f-166">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
