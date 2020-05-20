---
<span data-ttu-id="43d54-101">Título: ' proteger ASP.NET Core una Blazor aplicación independiente de Webassembly con la biblioteca de autenticación ' Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="43d54-101">title: 'Secure an ASP.NET Core Blazor WebAssembly standalone app with the Authentication library' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="43d54-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="43d54-102">'Blazor'</span></span>
- <span data-ttu-id="43d54-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="43d54-103">'Identity'</span></span>
- <span data-ttu-id="43d54-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="43d54-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="43d54-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="43d54-105">'Razor'</span></span>
- <span data-ttu-id="43d54-106">SignalRUID ' ':</span><span class="sxs-lookup"><span data-stu-id="43d54-106">'SignalR' uid:</span></span> 

---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-the-authentication-library"></a><span data-ttu-id="43d54-107">Protección de una Blazor aplicación independiente ASP.net Core Webassembly con la biblioteca de autenticación</span><span class="sxs-lookup"><span data-stu-id="43d54-107">Secure an ASP.NET Core Blazor WebAssembly standalone app with the Authentication library</span></span>

<span data-ttu-id="43d54-108">Por [Javier Calvarro Nelson](https://github.com/javiercn) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="43d54-108">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="43d54-109">*Por Azure Active Directory (AAD) y Azure Active Directory B2C (AAD B2C), no siga las instrucciones de este tema. Vea los temas de AAD y AAD B2C en este nodo de tabla de contenido.*</span><span class="sxs-lookup"><span data-stu-id="43d54-109">*For Azure Active Directory (AAD) and Azure Active Directory B2C (AAD B2C), don't follow the guidance in this topic. See the AAD and AAD B2C topics in this table of contents node.*</span></span>

<span data-ttu-id="43d54-110">Para crear una Blazor aplicación independiente Webassembly que use `Microsoft.AspNetCore.Components.WebAssembly.Authentication` la biblioteca, ejecute el siguiente comando en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="43d54-110">To create a Blazor WebAssembly standalone app that uses `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library, execute the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual
```

<span data-ttu-id="43d54-111">Para especificar la ubicación de salida, que crea una carpeta de proyecto si no existe, incluya la opción output en el comando con una ruta de acceso (por ejemplo, `-o BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="43d54-111">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="43d54-112">El nombre de la carpeta también se convierte en parte del nombre del proyecto.</span><span class="sxs-lookup"><span data-stu-id="43d54-112">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="43d54-113">En Visual Studio, [cree una Blazor aplicación webassembly](xref:blazor/get-started).</span><span class="sxs-lookup"><span data-stu-id="43d54-113">In Visual Studio, [create a Blazor WebAssembly app](xref:blazor/get-started).</span></span> <span data-ttu-id="43d54-114">Establezca la **autenticación** en **cuentas de usuario individuales** con la opción **almacenar cuentas de usuario en la aplicación** .</span><span class="sxs-lookup"><span data-stu-id="43d54-114">Set **Authentication** to **Individual User Accounts** with the **Store user accounts in-app** option.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="43d54-115">Paquete de autenticación</span><span class="sxs-lookup"><span data-stu-id="43d54-115">Authentication package</span></span>

<span data-ttu-id="43d54-116">Cuando se crea una aplicación para usar cuentas de usuario individuales, la aplicación recibe automáticamente una referencia de paquete para el `Microsoft.AspNetCore.Components.WebAssembly.Authentication` paquete en el archivo de proyecto de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="43d54-116">When an app is created to use Individual User Accounts, the app automatically receives a package reference for the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package in the app's project file.</span></span> <span data-ttu-id="43d54-117">El paquete proporciona un conjunto de primitivas que ayudan a la aplicación a autenticar a los usuarios y obtener tokens para llamar a las API protegidas.</span><span class="sxs-lookup"><span data-stu-id="43d54-117">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="43d54-118">Si agrega la autenticación a una aplicación, agregue manualmente el paquete al archivo de proyecto de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="43d54-118">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="3.2.0" />
```

## <a name="authentication-service-support"></a><span data-ttu-id="43d54-119">Compatibilidad con el servicio de autenticación</span><span class="sxs-lookup"><span data-stu-id="43d54-119">Authentication service support</span></span>

<span data-ttu-id="43d54-120">La compatibilidad con la autenticación de usuarios se registra en el contenedor de servicios con el `AddOidcAuthentication` método de extensión proporcionado por el `Microsoft.AspNetCore.Components.WebAssembly.Authentication` paquete.</span><span class="sxs-lookup"><span data-stu-id="43d54-120">Support for authenticating users is registered in the service container with the `AddOidcAuthentication` extension method provided by the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package.</span></span> <span data-ttu-id="43d54-121">Este método configura los servicios necesarios para que la aplicación interactúe con el Identity proveedor (IP).</span><span class="sxs-lookup"><span data-stu-id="43d54-121">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="43d54-122">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="43d54-122">*Program.cs*:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    builder.Configuration.Bind("Local", options.ProviderOptions);
});
```

<span data-ttu-id="43d54-123">La configuración se proporciona mediante el archivo *wwwroot/appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="43d54-123">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
    "Local": {
        "Authority": "{AUTHORITY}",
        "ClientId": "{CLIENT ID}"
    }
}
```

<span data-ttu-id="43d54-124">La compatibilidad con la autenticación para aplicaciones independientes se ofrece mediante Open ID Connect (OIDC).</span><span class="sxs-lookup"><span data-stu-id="43d54-124">Authentication support for standalone apps is offered using Open ID Connect (OIDC).</span></span> <span data-ttu-id="43d54-125">El `AddOidcAuthentication` método acepta una devolución de llamada para configurar los parámetros necesarios para autenticar una aplicación mediante OIDC.</span><span class="sxs-lookup"><span data-stu-id="43d54-125">The `AddOidcAuthentication` method accepts a callback to configure the parameters required to authenticate an app using OIDC.</span></span> <span data-ttu-id="43d54-126">Los valores necesarios para configurar la aplicación pueden obtenerse a partir de la dirección IP compatible con OIDC.</span><span class="sxs-lookup"><span data-stu-id="43d54-126">The values required for configuring the app can be obtained from the OIDC-compliant IP.</span></span> <span data-ttu-id="43d54-127">Obtenga los valores al registrar la aplicación, que suele producirse en su portal en línea.</span><span class="sxs-lookup"><span data-stu-id="43d54-127">Obtain the values when you register the app, which typically occurs in their online portal.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="43d54-128">Ámbitos de token de acceso</span><span class="sxs-lookup"><span data-stu-id="43d54-128">Access token scopes</span></span>

<span data-ttu-id="43d54-129">La Blazor plantilla Webassembly no configura automáticamente la aplicación para solicitar un token de acceso para una API segura.</span><span class="sxs-lookup"><span data-stu-id="43d54-129">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="43d54-130">Para aprovisionar un token de acceso como parte del flujo de inicio de sesión, agregue el ámbito a los ámbitos de token predeterminados de `OidcProviderOptions` :</span><span class="sxs-lookup"><span data-stu-id="43d54-130">To provision an access token as part of the sign-in flow, add the scope to the default token scopes of the `OidcProviderOptions`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="43d54-131">Para obtener más información, consulte las siguientes secciones del artículo *escenarios adicionales* :</span><span class="sxs-lookup"><span data-stu-id="43d54-131">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="43d54-132">Solicitar tokens de acceso adicionales</span><span class="sxs-lookup"><span data-stu-id="43d54-132">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="43d54-133">Adjuntar tokens a solicitudes salientes</span><span class="sxs-lookup"><span data-stu-id="43d54-133">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="43d54-134">Importar archivo</span><span class="sxs-lookup"><span data-stu-id="43d54-134">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="43d54-135">Página de índice</span><span class="sxs-lookup"><span data-stu-id="43d54-135">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

## <a name="app-component"></a><span data-ttu-id="43d54-136">Componente de aplicación</span><span class="sxs-lookup"><span data-stu-id="43d54-136">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="43d54-137">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="43d54-137">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="43d54-138">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="43d54-138">LoginDisplay component</span></span>

<span data-ttu-id="43d54-139">El `LoginDisplay` componente (*Shared/LoginDisplay. Razor*) se representa en el `MainLayout` componente (*Shared/MainLayout. Razor*) y administra los comportamientos siguientes:</span><span class="sxs-lookup"><span data-stu-id="43d54-139">The `LoginDisplay` component (*Shared/LoginDisplay.razor*) is rendered in the `MainLayout` component (*Shared/MainLayout.razor*) and manages the following behaviors:</span></span>

* <span data-ttu-id="43d54-140">Para usuarios autenticados:</span><span class="sxs-lookup"><span data-stu-id="43d54-140">For authenticated users:</span></span>
  * <span data-ttu-id="43d54-141">Muestra el nombre de usuario actual.</span><span class="sxs-lookup"><span data-stu-id="43d54-141">Displays the current username.</span></span>
  * <span data-ttu-id="43d54-142">Ofrece un botón para cerrar la sesión de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="43d54-142">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="43d54-143">En el caso de los usuarios anónimos, ofrece la opción de iniciar sesión.</span><span class="sxs-lookup"><span data-stu-id="43d54-143">For anonymous users, offers the option to log in.</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        Hello, @context.User.Identity.Name!
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code {
    private async Task BeginSignOut(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```

## <a name="authentication-component"></a><span data-ttu-id="43d54-144">Componente de autenticación</span><span class="sxs-lookup"><span data-stu-id="43d54-144">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="43d54-145">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="43d54-145">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="43d54-146">Solicitudes de API Web no autenticadas o no autorizadas en una aplicación con un cliente predeterminado seguro</span><span class="sxs-lookup"><span data-stu-id="43d54-146">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
