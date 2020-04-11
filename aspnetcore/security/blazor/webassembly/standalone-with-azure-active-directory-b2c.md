---
title: Proteja una Blazor aplicación independiente de ASP.NET Core WebAssembly con Azure Active Directory B2C
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/09/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory-b2c
ms.openlocfilehash: 96e39a4c975a65fd11776f774fb1799acab525b9
ms.sourcegitcommit: e8dc30453af8bbefcb61857987090d79230a461d
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/11/2020
ms.locfileid: "81123448"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a><span data-ttu-id="200f9-102">Proteja una Blazor aplicación independiente de ASP.NET Core WebAssembly con Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="200f9-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory B2C</span></span>

<span data-ttu-id="200f9-103">Por [Javier Calvarro Nelson](https://github.com/javiercn) y Luke [Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="200f9-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="200f9-104">Para crear Blazor una aplicación independiente de WebAssembly que use [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) para la autenticación:</span><span class="sxs-lookup"><span data-stu-id="200f9-104">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication:</span></span>

1. <span data-ttu-id="200f9-105">Siga las instrucciones de los temas siguientes para crear un inquilino y registrar una aplicación web en Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="200f9-105">Follow the guidance in the following topics to create a tenant and register a web app in the Azure Portal:</span></span>

   * <span data-ttu-id="200f9-106">Crear un inquilino &ndash; de [AAD B2C](/azure/active-directory-b2c/tutorial-create-tenant) Registre la siguiente información:</span><span class="sxs-lookup"><span data-stu-id="200f9-106">[Create an AAD B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) &ndash; Record the following information:</span></span>

     <span data-ttu-id="200f9-107">1\.</span><span class="sxs-lookup"><span data-stu-id="200f9-107">1\.</span></span> <span data-ttu-id="200f9-108">Instancia de AAD B2C `https://contoso.b2clogin.com/`(por ejemplo, , que incluye la barra diagonal final)</span><span class="sxs-lookup"><span data-stu-id="200f9-108">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span><br>
     <span data-ttu-id="200f9-109">2\.</span><span class="sxs-lookup"><span data-stu-id="200f9-109">2\.</span></span> <span data-ttu-id="200f9-110">Dominio de inquilino de AAD `contoso.onmicrosoft.com`B2C (por ejemplo, )</span><span class="sxs-lookup"><span data-stu-id="200f9-110">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

   * <span data-ttu-id="200f9-111">[Registrar una aplicación](/azure/active-directory-b2c/tutorial-register-applications) &ndash; web Realice las siguientes selecciones durante el registro de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="200f9-111">[Register a web application](/azure/active-directory-b2c/tutorial-register-applications) &ndash; Make the following selections during app registration:</span></span>

     <span data-ttu-id="200f9-112">1\.</span><span class="sxs-lookup"><span data-stu-id="200f9-112">1\.</span></span> <span data-ttu-id="200f9-113">Establezca **Web App / Web API** en **Yes**.</span><span class="sxs-lookup"><span data-stu-id="200f9-113">Set **Web App / Web API** to **Yes**.</span></span><br>
     <span data-ttu-id="200f9-114">2\.</span><span class="sxs-lookup"><span data-stu-id="200f9-114">2\.</span></span> <span data-ttu-id="200f9-115">Establezca **Permitir flujo implícito** en **Sí**.</span><span class="sxs-lookup"><span data-stu-id="200f9-115">Set **Allow implicit flow** to **Yes**.</span></span><br>
     <span data-ttu-id="200f9-116">3\.</span><span class="sxs-lookup"><span data-stu-id="200f9-116">3\.</span></span> <span data-ttu-id="200f9-117">Agregue una dirección `https://localhost:5001/authentication/login-callback`URL de **respuesta** de .</span><span class="sxs-lookup"><span data-stu-id="200f9-117">Add a **Reply URL** of `https://localhost:5001/authentication/login-callback`.</span></span>

     <span data-ttu-id="200f9-118">Registre el ID de aplicación (ID `11111111-1111-1111-1111-111111111111`de cliente) (por ejemplo, ).</span><span class="sxs-lookup"><span data-stu-id="200f9-118">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

   * <span data-ttu-id="200f9-119">[Crear flujos](/azure/active-directory-b2c/tutorial-create-user-flows) &ndash; de usuario Crear un flujo de usuarios de registro e inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="200f9-119">[Create user flows](/azure/active-directory-b2c/tutorial-create-user-flows) &ndash; Create a sign-up and sign-in user flow.</span></span>

     <span data-ttu-id="200f9-120">Como mínimo, seleccione el atributo de usuario `context.User.Identity.Name` Nombre `LoginDisplay` para**mostrar** **notificaciones** > de aplicación para rellenar el componente (*Shared/LoginDisplay.razor*).</span><span class="sxs-lookup"><span data-stu-id="200f9-120">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

     <span data-ttu-id="200f9-121">Registre el nombre de flujo de usuario de registro e `B2C_1_signupsignin`inicio de sesión creado para la aplicación (por ejemplo, ).</span><span class="sxs-lookup"><span data-stu-id="200f9-121">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

1. <span data-ttu-id="200f9-122">Reemplace los marcadores de posición del siguiente comando por la información registrada anteriormente y ejecute el comando en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="200f9-122">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{DOMAIN}" -ssp "{SIGN UP OR SIGN IN POLICY}"
   ```

   <span data-ttu-id="200f9-123">Para especificar la ubicación de salida, que crea una carpeta de proyecto si no existe, `-o BlazorSample`incluya la opción de salida en el comando con una ruta de acceso (por ejemplo, ).</span><span class="sxs-lookup"><span data-stu-id="200f9-123">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="200f9-124">El nombre de la carpeta también pasa a formar parte del nombre del proyecto.</span><span class="sxs-lookup"><span data-stu-id="200f9-124">The folder name also becomes part of the project's name.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="200f9-125">Paquete de autenticación</span><span class="sxs-lookup"><span data-stu-id="200f9-125">Authentication package</span></span>

<span data-ttu-id="200f9-126">Cuando se crea una aplicación para usar`IndividualB2C`una cuenta B2C individual ( ), la`Microsoft.Authentication.WebAssembly.Msal`aplicación recibe automáticamente una referencia de paquete para la biblioteca de autenticación de [Microsoft](/azure/active-directory/develop/msal-overview) ( ).</span><span class="sxs-lookup"><span data-stu-id="200f9-126">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="200f9-127">El paquete proporciona un conjunto de primitivas que ayudan a la aplicación a autenticar a los usuarios y obtener tokens para llamar a las API protegidas.</span><span class="sxs-lookup"><span data-stu-id="200f9-127">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="200f9-128">Si agrega autenticación a una aplicación, agregue manualmente el paquete al archivo de proyecto de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="200f9-128">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="200f9-129">Reemplace `{VERSION}` en la referencia del paquete `Microsoft.AspNetCore.Blazor.Templates` anterior por <xref:blazor/get-started> la versión del paquete que se muestra en el artículo.</span><span class="sxs-lookup"><span data-stu-id="200f9-129">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="200f9-130">El `Microsoft.Authentication.WebAssembly.Msal` paquete agrega transitivamente el `Microsoft.AspNetCore.Components.WebAssembly.Authentication` paquete a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="200f9-130">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="200f9-131">Soporte de servicio de autenticación</span><span class="sxs-lookup"><span data-stu-id="200f9-131">Authentication service support</span></span>

<span data-ttu-id="200f9-132">La compatibilidad con la autenticación de usuarios `AddMsalAuthentication` se registra `Microsoft.Authentication.WebAssembly.Msal` en el contenedor de servicios con el método de extensión proporcionado por el paquete.</span><span class="sxs-lookup"><span data-stu-id="200f9-132">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="200f9-133">Este método configura todos los servicios necesarios para que la aplicación interactúe con el proveedor de identidades (IP).</span><span class="sxs-lookup"><span data-stu-id="200f9-133">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="200f9-134">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="200f9-134">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = 
        "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}";
    authentication.ClientId = "{CLIENT ID}";
    authentication.ValidateAuthority = false;
});
```

<span data-ttu-id="200f9-135">El `AddMsalAuthentication` método acepta una devolución de llamada para configurar los parámetros necesarios para autenticar una aplicación.</span><span class="sxs-lookup"><span data-stu-id="200f9-135">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="200f9-136">Los valores necesarios para configurar la aplicación se pueden obtener de la configuración de AAD de Azure Portal al registrar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="200f9-136">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="200f9-137">Acceso a ámbitos de token</span><span class="sxs-lookup"><span data-stu-id="200f9-137">Access token scopes</span></span>

<span data-ttu-id="200f9-138">La Blazor plantilla WebAssembly no configura automáticamente la aplicación para solicitar un token de acceso para una API segura.</span><span class="sxs-lookup"><span data-stu-id="200f9-138">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="200f9-139">Para aprovisionar un token como parte del flujo de inicio de sesión, `MsalProviderOptions`agregue el ámbito a los ámbitos de token de acceso predeterminados de:</span><span class="sxs-lookup"><span data-stu-id="200f9-139">To provision a token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="200f9-140">Si Azure Portal proporciona un URI de ámbito y **la aplicación produce una excepción no controlada** cuando recibe una respuesta *401 Unauthorized* de la API, intente usar un URI de ámbito que no incluya el esquema y el host.</span><span class="sxs-lookup"><span data-stu-id="200f9-140">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="200f9-141">Por ejemplo, Azure Portal puede proporcionar uno de los siguientes formatos de URI de ámbito:</span><span class="sxs-lookup"><span data-stu-id="200f9-141">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="200f9-142">Proporcione el URI de ámbito sin el esquema y el host:</span><span class="sxs-lookup"><span data-stu-id="200f9-142">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="200f9-143">Para obtener más información, vea <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span><span class="sxs-lookup"><span data-stu-id="200f9-143">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

## <a name="imports-file"></a><span data-ttu-id="200f9-144">Archivo de importaciones</span><span class="sxs-lookup"><span data-stu-id="200f9-144">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="200f9-145">Página de índice</span><span class="sxs-lookup"><span data-stu-id="200f9-145">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="200f9-146">Componente de la aplicación</span><span class="sxs-lookup"><span data-stu-id="200f9-146">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="200f9-147">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="200f9-147">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="200f9-148">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="200f9-148">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="200f9-149">Componente de autenticación</span><span class="sxs-lookup"><span data-stu-id="200f9-149">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="200f9-150">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="200f9-150">Additional resources</span></span>

* [<span data-ttu-id="200f9-151">Solicitar tokens de acceso adicionales</span><span class="sxs-lookup"><span data-stu-id="200f9-151">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="200f9-152">Tutorial: Creación de un inquilino de Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="200f9-152">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="200f9-153">Documentación de la plataforma de identidad de Microsoft</span><span class="sxs-lookup"><span data-stu-id="200f9-153">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
