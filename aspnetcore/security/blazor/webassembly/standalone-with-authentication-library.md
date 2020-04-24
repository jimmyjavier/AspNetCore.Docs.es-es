---
title: Protección de una Blazor aplicación independiente ASP.net Core webassembly con la biblioteca de autenticación
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-authentication-library
ms.openlocfilehash: 043e4548ad6f40fdf1e6c27cd51946c7bf59a66e
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/24/2020
ms.locfileid: "82110959"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-the-authentication-library"></a><span data-ttu-id="c9af5-102">Protección de una Blazor aplicación independiente ASP.net Core webassembly con la biblioteca de autenticación</span><span class="sxs-lookup"><span data-stu-id="c9af5-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with the Authentication library</span></span>

<span data-ttu-id="c9af5-103">Por [Javier Calvarro Nelson](https://github.com/javiercn) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="c9af5-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

> [!NOTE]
> <span data-ttu-id="c9af5-104">Las instrucciones de este artículo se aplican a ASP.NET Core 3,2 Preview 4.</span><span class="sxs-lookup"><span data-stu-id="c9af5-104">The guidance in this article applies to ASP.NET Core 3.2 Preview 4.</span></span> <span data-ttu-id="c9af5-105">Este tema se actualizará a la versión preliminar 5 del viernes, 24 de abril.</span><span class="sxs-lookup"><span data-stu-id="c9af5-105">This topic will be updated to cover Preview 5 on Friday, April 24.</span></span>

<span data-ttu-id="c9af5-106">*Por Azure Active Directory (AAD) y Azure Active Directory B2C (AAD B2C), no siga las instrucciones de este tema. Vea los temas de AAD y AAD B2C en este nodo de tabla de contenido.*</span><span class="sxs-lookup"><span data-stu-id="c9af5-106">*For Azure Active Directory (AAD) and Azure Active Directory B2C (AAD B2C), don't follow the guidance in this topic. See the AAD and AAD B2C topics in this table of contents node.*</span></span>

<span data-ttu-id="c9af5-107">Para crear una Blazor aplicación independiente webassembly que `Microsoft.AspNetCore.Components.WebAssembly.Authentication` use la biblioteca, ejecute el siguiente comando en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="c9af5-107">To create a Blazor WebAssembly standalone app that uses `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library, execute the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual
```

<span data-ttu-id="c9af5-108">Para especificar la ubicación de salida, que crea una carpeta de proyecto si no existe, incluya la opción output en el comando con una ruta de acceso ( `-o BlazorSample`por ejemplo,).</span><span class="sxs-lookup"><span data-stu-id="c9af5-108">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="c9af5-109">El nombre de la carpeta también se convierte en parte del nombre del proyecto.</span><span class="sxs-lookup"><span data-stu-id="c9af5-109">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="c9af5-110">En Visual Studio, [cree una Blazor aplicación webassembly](xref:blazor/get-started).</span><span class="sxs-lookup"><span data-stu-id="c9af5-110">In Visual Studio, [create a Blazor WebAssembly app](xref:blazor/get-started).</span></span> <span data-ttu-id="c9af5-111">Establezca la **autenticación** en **cuentas de usuario individuales** con la opción **almacenar cuentas de usuario en la aplicación** .</span><span class="sxs-lookup"><span data-stu-id="c9af5-111">Set **Authentication** to **Individual User Accounts** with the **Store user accounts in-app** option.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="c9af5-112">Paquete de autenticación</span><span class="sxs-lookup"><span data-stu-id="c9af5-112">Authentication package</span></span>

<span data-ttu-id="c9af5-113">Cuando se crea una aplicación para usar cuentas de usuario individuales, la aplicación recibe automáticamente una referencia de paquete `Microsoft.AspNetCore.Components.WebAssembly.Authentication` para el paquete en el archivo de proyecto de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="c9af5-113">When an app is created to use Individual User Accounts, the app automatically receives a package reference for the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package in the app's project file.</span></span> <span data-ttu-id="c9af5-114">El paquete proporciona un conjunto de primitivas que ayudan a la aplicación a autenticar a los usuarios y obtener tokens para llamar a las API protegidas.</span><span class="sxs-lookup"><span data-stu-id="c9af5-114">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="c9af5-115">Si agrega la autenticación a una aplicación, agregue manualmente el paquete al archivo de proyecto de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="c9af5-115">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

<span data-ttu-id="c9af5-116">Reemplace `{VERSION}` en la referencia de paquete anterior por la versión del `Microsoft.AspNetCore.Blazor.Templates` paquete que se muestra <xref:blazor/get-started> en el artículo.</span><span class="sxs-lookup"><span data-stu-id="c9af5-116">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="c9af5-117">Compatibilidad con el servicio de autenticación</span><span class="sxs-lookup"><span data-stu-id="c9af5-117">Authentication service support</span></span>

<span data-ttu-id="c9af5-118">La compatibilidad con la autenticación de usuarios se registra en el contenedor de `AddOidcAuthentication` servicios con el método de `Microsoft.AspNetCore.Components.WebAssembly.Authentication` extensión proporcionado por el paquete.</span><span class="sxs-lookup"><span data-stu-id="c9af5-118">Support for authenticating users is registered in the service container with the `AddOidcAuthentication` extension method provided by the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package.</span></span> <span data-ttu-id="c9af5-119">Este método configura todos los servicios necesarios para que la aplicación interactúe con el proveedor de identidades (IP).</span><span class="sxs-lookup"><span data-stu-id="c9af5-119">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="c9af5-120">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="c9af5-120">*Program.cs*:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    options.ProviderOptions.Authority = "{AUTHORITY}";
    options.ProviderOptions.ClientId = "{CLIENT ID}";
});
```

<span data-ttu-id="c9af5-121">La compatibilidad con la autenticación para aplicaciones independientes se ofrece mediante Open ID Connect (OIDC).</span><span class="sxs-lookup"><span data-stu-id="c9af5-121">Authentication support for standalone apps is offered using Open ID Connect (OIDC).</span></span> <span data-ttu-id="c9af5-122">El `AddOidcAuthentication` método acepta una devolución de llamada para configurar los parámetros necesarios para autenticar una aplicación mediante OIDC.</span><span class="sxs-lookup"><span data-stu-id="c9af5-122">The `AddOidcAuthentication` method accepts a callback to configure the parameters required to authenticate an app using OIDC.</span></span> <span data-ttu-id="c9af5-123">Los valores necesarios para configurar la aplicación pueden obtenerse a partir de la dirección IP compatible con OIDC.</span><span class="sxs-lookup"><span data-stu-id="c9af5-123">The values required for configuring the app can be obtained from the OIDC-compliant IP.</span></span> <span data-ttu-id="c9af5-124">Obtenga los valores al registrar la aplicación, que suele producirse en su portal en línea.</span><span class="sxs-lookup"><span data-stu-id="c9af5-124">Obtain the values when you register the app, which typically occurs in their online portal.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="c9af5-125">Ámbitos de token de acceso</span><span class="sxs-lookup"><span data-stu-id="c9af5-125">Access token scopes</span></span>

<span data-ttu-id="c9af5-126">La Blazor plantilla webassembly no configura automáticamente la aplicación para solicitar un token de acceso para una API segura.</span><span class="sxs-lookup"><span data-stu-id="c9af5-126">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="c9af5-127">Para aprovisionar un token como parte del flujo de inicio de sesión, agregue el ámbito a los ámbitos de token predeterminados de `OidcProviderOptions`:</span><span class="sxs-lookup"><span data-stu-id="c9af5-127">To provision a token as part of the sign-in flow, add the scope to the default token scopes of the `OidcProviderOptions`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="c9af5-128">Si el Azure Portal proporciona un URI de ámbito y **la aplicación produce una excepción no controlada** cuando recibe una respuesta *401 no autorizada* de la API, intente usar un URI de ámbito que no incluya el esquema y el host.</span><span class="sxs-lookup"><span data-stu-id="c9af5-128">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="c9af5-129">Por ejemplo, el Azure Portal puede proporcionar uno de los siguientes formatos de URI de ámbito:</span><span class="sxs-lookup"><span data-stu-id="c9af5-129">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="c9af5-130">Proporcione el URI del ámbito sin el esquema y el host:</span><span class="sxs-lookup"><span data-stu-id="c9af5-130">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="c9af5-131">Para obtener más información, vea <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span><span class="sxs-lookup"><span data-stu-id="c9af5-131">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

<!--
    For more information, see <xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests>.
-->

## <a name="imports-file"></a><span data-ttu-id="c9af5-132">Importar archivo</span><span class="sxs-lookup"><span data-stu-id="c9af5-132">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="c9af5-133">Página de índice</span><span class="sxs-lookup"><span data-stu-id="c9af5-133">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

## <a name="app-component"></a><span data-ttu-id="c9af5-134">Componente de aplicación</span><span class="sxs-lookup"><span data-stu-id="c9af5-134">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="c9af5-135">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="c9af5-135">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="c9af5-136">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="c9af5-136">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="c9af5-137">Componente de autenticación</span><span class="sxs-lookup"><span data-stu-id="c9af5-137">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="c9af5-138">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="c9af5-138">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
 