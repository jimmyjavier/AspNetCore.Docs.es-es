---
title: Protección de una Blazor aplicación independiente ASP.net Core webassembly con la biblioteca de autenticación
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/24/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-authentication-library
ms.openlocfilehash: 25aa7761b9c1acc72081653422e80cb004500573
ms.sourcegitcommit: 4f91da9ce4543b39dba5e8920a9500d3ce959746
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/24/2020
ms.locfileid: "82138531"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-the-authentication-library"></a><span data-ttu-id="30c3d-102">Protección de una Blazor aplicación independiente ASP.net Core webassembly con la biblioteca de autenticación</span><span class="sxs-lookup"><span data-stu-id="30c3d-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with the Authentication library</span></span>

<span data-ttu-id="30c3d-103">Por [Javier Calvarro Nelson](https://github.com/javiercn) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="30c3d-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="30c3d-104">*Por Azure Active Directory (AAD) y Azure Active Directory B2C (AAD B2C), no siga las instrucciones de este tema. Vea los temas de AAD y AAD B2C en este nodo de tabla de contenido.*</span><span class="sxs-lookup"><span data-stu-id="30c3d-104">*For Azure Active Directory (AAD) and Azure Active Directory B2C (AAD B2C), don't follow the guidance in this topic. See the AAD and AAD B2C topics in this table of contents node.*</span></span>

<span data-ttu-id="30c3d-105">Para crear una Blazor aplicación independiente webassembly que `Microsoft.AspNetCore.Components.WebAssembly.Authentication` use la biblioteca, ejecute el siguiente comando en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="30c3d-105">To create a Blazor WebAssembly standalone app that uses `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library, execute the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual
```

<span data-ttu-id="30c3d-106">Para especificar la ubicación de salida, que crea una carpeta de proyecto si no existe, incluya la opción output en el comando con una ruta de acceso ( `-o BlazorSample`por ejemplo,).</span><span class="sxs-lookup"><span data-stu-id="30c3d-106">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="30c3d-107">El nombre de la carpeta también se convierte en parte del nombre del proyecto.</span><span class="sxs-lookup"><span data-stu-id="30c3d-107">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="30c3d-108">En Visual Studio, [cree una Blazor aplicación webassembly](xref:blazor/get-started).</span><span class="sxs-lookup"><span data-stu-id="30c3d-108">In Visual Studio, [create a Blazor WebAssembly app](xref:blazor/get-started).</span></span> <span data-ttu-id="30c3d-109">Establezca la **autenticación** en **cuentas de usuario individuales** con la opción **almacenar cuentas de usuario en la aplicación** .</span><span class="sxs-lookup"><span data-stu-id="30c3d-109">Set **Authentication** to **Individual User Accounts** with the **Store user accounts in-app** option.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="30c3d-110">Paquete de autenticación</span><span class="sxs-lookup"><span data-stu-id="30c3d-110">Authentication package</span></span>

<span data-ttu-id="30c3d-111">Cuando se crea una aplicación para usar cuentas de usuario individuales, la aplicación recibe automáticamente una referencia de paquete `Microsoft.AspNetCore.Components.WebAssembly.Authentication` para el paquete en el archivo de proyecto de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="30c3d-111">When an app is created to use Individual User Accounts, the app automatically receives a package reference for the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package in the app's project file.</span></span> <span data-ttu-id="30c3d-112">El paquete proporciona un conjunto de primitivas que ayudan a la aplicación a autenticar a los usuarios y obtener tokens para llamar a las API protegidas.</span><span class="sxs-lookup"><span data-stu-id="30c3d-112">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="30c3d-113">Si agrega la autenticación a una aplicación, agregue manualmente el paquete al archivo de proyecto de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="30c3d-113">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

<span data-ttu-id="30c3d-114">Reemplace `{VERSION}` en la referencia de paquete anterior por la versión del `Microsoft.AspNetCore.Blazor.Templates` paquete que se muestra <xref:blazor/get-started> en el artículo.</span><span class="sxs-lookup"><span data-stu-id="30c3d-114">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="30c3d-115">Compatibilidad con el servicio de autenticación</span><span class="sxs-lookup"><span data-stu-id="30c3d-115">Authentication service support</span></span>

<span data-ttu-id="30c3d-116">La compatibilidad con la autenticación de usuarios se registra en el contenedor de `AddOidcAuthentication` servicios con el método de `Microsoft.AspNetCore.Components.WebAssembly.Authentication` extensión proporcionado por el paquete.</span><span class="sxs-lookup"><span data-stu-id="30c3d-116">Support for authenticating users is registered in the service container with the `AddOidcAuthentication` extension method provided by the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package.</span></span> <span data-ttu-id="30c3d-117">Este método configura todos los servicios necesarios para que la aplicación interactúe con el proveedor de identidades (IP).</span><span class="sxs-lookup"><span data-stu-id="30c3d-117">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="30c3d-118">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="30c3d-118">*Program.cs*:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    builder.Configuration.Bind("Local", options.ProviderOptions);
});
```

<span data-ttu-id="30c3d-119">La configuración se proporciona mediante el archivo *wwwroot/appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="30c3d-119">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
    "Local": {
        "Authority": "{AUTHORITY}",
        "ClientId": "{CLIENT ID}"
    }
}
```

<span data-ttu-id="30c3d-120">La compatibilidad con la autenticación para aplicaciones independientes se ofrece mediante Open ID Connect (OIDC).</span><span class="sxs-lookup"><span data-stu-id="30c3d-120">Authentication support for standalone apps is offered using Open ID Connect (OIDC).</span></span> <span data-ttu-id="30c3d-121">El `AddOidcAuthentication` método acepta una devolución de llamada para configurar los parámetros necesarios para autenticar una aplicación mediante OIDC.</span><span class="sxs-lookup"><span data-stu-id="30c3d-121">The `AddOidcAuthentication` method accepts a callback to configure the parameters required to authenticate an app using OIDC.</span></span> <span data-ttu-id="30c3d-122">Los valores necesarios para configurar la aplicación pueden obtenerse a partir de la dirección IP compatible con OIDC.</span><span class="sxs-lookup"><span data-stu-id="30c3d-122">The values required for configuring the app can be obtained from the OIDC-compliant IP.</span></span> <span data-ttu-id="30c3d-123">Obtenga los valores al registrar la aplicación, que suele producirse en su portal en línea.</span><span class="sxs-lookup"><span data-stu-id="30c3d-123">Obtain the values when you register the app, which typically occurs in their online portal.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="30c3d-124">Ámbitos de token de acceso</span><span class="sxs-lookup"><span data-stu-id="30c3d-124">Access token scopes</span></span>

<span data-ttu-id="30c3d-125">La Blazor plantilla webassembly no configura automáticamente la aplicación para solicitar un token de acceso para una API segura.</span><span class="sxs-lookup"><span data-stu-id="30c3d-125">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="30c3d-126">Para aprovisionar un token de acceso como parte del flujo de inicio de sesión, agregue el ámbito a los ámbitos de token predeterminados de `OidcProviderOptions`:</span><span class="sxs-lookup"><span data-stu-id="30c3d-126">To provision an access token as part of the sign-in flow, add the scope to the default token scopes of the `OidcProviderOptions`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="30c3d-127">Si el Azure Portal proporciona un URI de ámbito y **la aplicación produce una excepción no controlada** cuando recibe una respuesta *401 no autorizada* de la API, intente usar un URI de ámbito que no incluya el esquema y el host.</span><span class="sxs-lookup"><span data-stu-id="30c3d-127">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="30c3d-128">Por ejemplo, el Azure Portal puede proporcionar uno de los siguientes formatos de URI de ámbito:</span><span class="sxs-lookup"><span data-stu-id="30c3d-128">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="30c3d-129">Proporcione el URI del ámbito sin el esquema y el host:</span><span class="sxs-lookup"><span data-stu-id="30c3d-129">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="30c3d-130">Para obtener más información, consulte las siguientes secciones del artículo *escenarios adicionales* :</span><span class="sxs-lookup"><span data-stu-id="30c3d-130">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="30c3d-131">Solicitar tokens de acceso adicionales</span><span class="sxs-lookup"><span data-stu-id="30c3d-131">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="30c3d-132">Adjuntar tokens a solicitudes salientes</span><span class="sxs-lookup"><span data-stu-id="30c3d-132">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="30c3d-133">Importar archivo</span><span class="sxs-lookup"><span data-stu-id="30c3d-133">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="30c3d-134">Página de índice</span><span class="sxs-lookup"><span data-stu-id="30c3d-134">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

## <a name="app-component"></a><span data-ttu-id="30c3d-135">Componente de aplicación</span><span class="sxs-lookup"><span data-stu-id="30c3d-135">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="30c3d-136">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="30c3d-136">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="30c3d-137">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="30c3d-137">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="30c3d-138">Componente de autenticación</span><span class="sxs-lookup"><span data-stu-id="30c3d-138">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="30c3d-139">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="30c3d-139">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
