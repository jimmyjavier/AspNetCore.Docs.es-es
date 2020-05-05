---
title: Protección de una Blazor aplicación independiente ASP.net Core webassembly con Azure Active Directory B2C
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
uid: security/blazor/webassembly/standalone-with-azure-active-directory-b2c
ms.openlocfilehash: 0fb4f4176f214d6bf0c005838a0ccbe4487243f2
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767979"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a><span data-ttu-id="fb987-102">Protección de una Blazor aplicación independiente ASP.net Core webassembly con Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="fb987-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory B2C</span></span>

<span data-ttu-id="fb987-103">Por [Javier Calvarro Nelson](https://github.com/javiercn) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="fb987-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="fb987-104">Para crear una Blazor aplicación independiente webassembly que use [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) para la autenticación:</span><span class="sxs-lookup"><span data-stu-id="fb987-104">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication:</span></span>

1. <span data-ttu-id="fb987-105">Siga las instrucciones de los temas siguientes para crear un inquilino y registrar una aplicación web en Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="fb987-105">Follow the guidance in the following topics to create a tenant and register a web app in the Azure Portal:</span></span>

   * <span data-ttu-id="fb987-106">[Cree un inquilino](/azure/active-directory-b2c/tutorial-create-tenant) &ndash; de AAD B2C registre la siguiente información:</span><span class="sxs-lookup"><span data-stu-id="fb987-106">[Create an AAD B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) &ndash; Record the following information:</span></span>

     <span data-ttu-id="fb987-107">1\.</span><span class="sxs-lookup"><span data-stu-id="fb987-107">1\.</span></span> <span data-ttu-id="fb987-108">AAD B2C instancia (por ejemplo, `https://contoso.b2clogin.com/`, que incluye la barra diagonal final)</span><span class="sxs-lookup"><span data-stu-id="fb987-108">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span><br>
     <span data-ttu-id="fb987-109">2\.</span><span class="sxs-lookup"><span data-stu-id="fb987-109">2\.</span></span> <span data-ttu-id="fb987-110">AAD B2C dominio del inquilino (por ejemplo `contoso.onmicrosoft.com`,)</span><span class="sxs-lookup"><span data-stu-id="fb987-110">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

   * <span data-ttu-id="fb987-111">[Registrar una aplicación](/azure/active-directory-b2c/tutorial-register-applications) &ndash; web realice las siguientes selecciones durante el registro de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="fb987-111">[Register a web application](/azure/active-directory-b2c/tutorial-register-applications) &ndash; Make the following selections during app registration:</span></span>

     <span data-ttu-id="fb987-112">1\.</span><span class="sxs-lookup"><span data-stu-id="fb987-112">1\.</span></span> <span data-ttu-id="fb987-113">Establezca **aplicación web/API Web** en **sí**.</span><span class="sxs-lookup"><span data-stu-id="fb987-113">Set **Web App / Web API** to **Yes**.</span></span><br>
     <span data-ttu-id="fb987-114">2\.</span><span class="sxs-lookup"><span data-stu-id="fb987-114">2\.</span></span> <span data-ttu-id="fb987-115">Establezca **permitir flujo implícito** en **sí**.</span><span class="sxs-lookup"><span data-stu-id="fb987-115">Set **Allow implicit flow** to **Yes**.</span></span><br>
     <span data-ttu-id="fb987-116">3 \.</span><span class="sxs-lookup"><span data-stu-id="fb987-116">3\.</span></span> <span data-ttu-id="fb987-117">Agregue una **dirección URL** de `https://localhost:5001/authentication/login-callback`respuesta de.</span><span class="sxs-lookup"><span data-stu-id="fb987-117">Add a **Reply URL** of `https://localhost:5001/authentication/login-callback`.</span></span>

     <span data-ttu-id="fb987-118">Registre el identificador de aplicación (ID. de cliente) ( `11111111-1111-1111-1111-111111111111`por ejemplo,).</span><span class="sxs-lookup"><span data-stu-id="fb987-118">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

   * <span data-ttu-id="fb987-119">[Crear flujos](/azure/active-directory-b2c/tutorial-create-user-flows) &ndash; de usuario crea un flujo de usuario de inicio de sesión e inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="fb987-119">[Create user flows](/azure/active-directory-b2c/tutorial-create-user-flows) &ndash; Create a sign-up and sign-in user flow.</span></span>

     <span data-ttu-id="fb987-120">Como mínimo, seleccione el atributo de usuario**nombre para mostrar** de **notificaciones** > de `context.User.Identity.Name` aplicación para rellenar el en el `LoginDisplay` componente (*Shared/LoginDisplay. Razor*).</span><span class="sxs-lookup"><span data-stu-id="fb987-120">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

     <span data-ttu-id="fb987-121">Registre el nombre del flujo de usuario de inicio de sesión y de registro creado para la aplicación (por `B2C_1_signupsignin`ejemplo,).</span><span class="sxs-lookup"><span data-stu-id="fb987-121">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

1. <span data-ttu-id="fb987-122">Reemplace los marcadores de posición en el siguiente comando por la información registrada anteriormente y ejecute el comando en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="fb987-122">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{DOMAIN}" -ssp "{SIGN UP OR SIGN IN POLICY}"
   ```

   <span data-ttu-id="fb987-123">Para especificar la ubicación de salida, que crea una carpeta de proyecto si no existe, incluya la opción output en el comando con una ruta de acceso ( `-o BlazorSample`por ejemplo,).</span><span class="sxs-lookup"><span data-stu-id="fb987-123">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="fb987-124">El nombre de la carpeta también se convierte en parte del nombre del proyecto.</span><span class="sxs-lookup"><span data-stu-id="fb987-124">The folder name also becomes part of the project's name.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="fb987-125">Paquete de autenticación</span><span class="sxs-lookup"><span data-stu-id="fb987-125">Authentication package</span></span>

<span data-ttu-id="fb987-126">Cuando se crea una aplicación para usar una cuenta de B2C individual`IndividualB2C`(), la aplicación recibe automáticamente una referencia de paquete para la biblioteca de`Microsoft.Authentication.WebAssembly.Msal`autenticación de [Microsoft](/azure/active-directory/develop/msal-overview) ().</span><span class="sxs-lookup"><span data-stu-id="fb987-126">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="fb987-127">El paquete proporciona un conjunto de primitivas que ayudan a la aplicación a autenticar a los usuarios y obtener tokens para llamar a las API protegidas.</span><span class="sxs-lookup"><span data-stu-id="fb987-127">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="fb987-128">Si agrega la autenticación a una aplicación, agregue manualmente el paquete al archivo de proyecto de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="fb987-128">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="fb987-129">Reemplace `{VERSION}` en la referencia de paquete anterior por la versión del `Microsoft.AspNetCore.Blazor.Templates` paquete que se muestra <xref:blazor/get-started> en el artículo.</span><span class="sxs-lookup"><span data-stu-id="fb987-129">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="fb987-130">El `Microsoft.Authentication.WebAssembly.Msal` paquete agrega de manera transitiva el `Microsoft.AspNetCore.Components.WebAssembly.Authentication` paquete a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="fb987-130">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="fb987-131">Compatibilidad con el servicio de autenticación</span><span class="sxs-lookup"><span data-stu-id="fb987-131">Authentication service support</span></span>

<span data-ttu-id="fb987-132">La compatibilidad con la autenticación de usuarios se registra en el contenedor de `AddMsalAuthentication` servicios con el método de `Microsoft.Authentication.WebAssembly.Msal` extensión proporcionado por el paquete.</span><span class="sxs-lookup"><span data-stu-id="fb987-132">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="fb987-133">Este método configura todos los servicios necesarios para que la aplicación interactúe con el Identity proveedor (IP).</span><span class="sxs-lookup"><span data-stu-id="fb987-133">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="fb987-134">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="fb987-134">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="fb987-135">El `AddMsalAuthentication` método acepta una devolución de llamada para configurar los parámetros necesarios para autenticar una aplicación.</span><span class="sxs-lookup"><span data-stu-id="fb987-135">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="fb987-136">Los valores necesarios para configurar la aplicación se pueden obtener a partir de la configuración de las cuentas de Microsoft cuando se registra la aplicación.</span><span class="sxs-lookup"><span data-stu-id="fb987-136">The values required for configuring the app can be obtained from the Microsoft Accounts configuration when you register the app.</span></span>

<span data-ttu-id="fb987-137">La configuración se proporciona mediante el archivo *wwwroot/appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="fb987-137">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="fb987-138">Ejemplo:</span><span class="sxs-lookup"><span data-stu-id="fb987-138">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd"
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="fb987-139">Ámbitos de token de acceso</span><span class="sxs-lookup"><span data-stu-id="fb987-139">Access token scopes</span></span>

<span data-ttu-id="fb987-140">La Blazor plantilla webassembly no configura automáticamente la aplicación para solicitar un token de acceso para una API segura.</span><span class="sxs-lookup"><span data-stu-id="fb987-140">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="fb987-141">Para aprovisionar un token de acceso como parte del flujo de inicio de sesión, agregue el ámbito a los ámbitos de token de acceso `MsalProviderOptions`predeterminados de:</span><span class="sxs-lookup"><span data-stu-id="fb987-141">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="fb987-142">Si el Azure Portal proporciona un URI de ámbito y **la aplicación produce una excepción no controlada** cuando recibe una respuesta *401 no autorizada* de la API, intente usar un URI de ámbito que no incluya el esquema y el host.</span><span class="sxs-lookup"><span data-stu-id="fb987-142">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="fb987-143">Por ejemplo, el Azure Portal puede proporcionar uno de los siguientes formatos de URI de ámbito:</span><span class="sxs-lookup"><span data-stu-id="fb987-143">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="fb987-144">Proporcione el URI del ámbito sin el esquema y el host:</span><span class="sxs-lookup"><span data-stu-id="fb987-144">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="fb987-145">Para obtener más información, consulte las siguientes secciones del artículo *escenarios adicionales* :</span><span class="sxs-lookup"><span data-stu-id="fb987-145">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="fb987-146">Solicitar tokens de acceso adicionales</span><span class="sxs-lookup"><span data-stu-id="fb987-146">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="fb987-147">Adjuntar tokens a solicitudes salientes</span><span class="sxs-lookup"><span data-stu-id="fb987-147">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="fb987-148">Importar archivo</span><span class="sxs-lookup"><span data-stu-id="fb987-148">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="fb987-149">Página de índice</span><span class="sxs-lookup"><span data-stu-id="fb987-149">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="fb987-150">Componente de aplicación</span><span class="sxs-lookup"><span data-stu-id="fb987-150">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="fb987-151">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="fb987-151">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="fb987-152">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="fb987-152">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="fb987-153">Componente de autenticación</span><span class="sxs-lookup"><span data-stu-id="fb987-153">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="fb987-154">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="fb987-154">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="fb987-155">Tutorial: Creación de un inquilino de Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="fb987-155">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="fb987-156">Documentación de la plataforma de identidad de Microsoft</span><span class="sxs-lookup"><span data-stu-id="fb987-156">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
