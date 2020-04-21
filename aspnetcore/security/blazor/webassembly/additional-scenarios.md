---
title: ASP.NET Blazor escenarios de seguridad adicionales de Core WebAssembly
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/19/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/additional-scenarios
ms.openlocfilehash: 314a7b54ab87295b8ca814f5e369942ae911407e
ms.sourcegitcommit: 5547d920f322e5a823575c031529e4755ab119de
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661592"
---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="9ee19-102">ASP.NET Core Blazor WebAssembly escenarios de seguridad adicionales</span><span class="sxs-lookup"><span data-stu-id="9ee19-102">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="9ee19-103">Por [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="9ee19-103">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

## <a name="request-additional-access-tokens"></a><span data-ttu-id="9ee19-104">Solicitar tokens de acceso adicionales</span><span class="sxs-lookup"><span data-stu-id="9ee19-104">Request additional access tokens</span></span>

<span data-ttu-id="9ee19-105">La mayoría de las aplicaciones solo requieren un token de acceso para interactuar con los recursos protegidos que usan.</span><span class="sxs-lookup"><span data-stu-id="9ee19-105">Most apps only require an access token to interact with the protected resources that they use.</span></span> <span data-ttu-id="9ee19-106">En algunos escenarios, una aplicación puede requerir más de un token para interactuar con dos o más recursos.</span><span class="sxs-lookup"><span data-stu-id="9ee19-106">In some scenarios, an app might require more than one token in order to interact with two or more resources.</span></span>

<span data-ttu-id="9ee19-107">En el ejemplo siguiente, una aplicación requiere ámbitos adicionales de Microsoft Graph API de Azure Active Directory (AAD) para leer los datos de usuario y enviar correo.</span><span class="sxs-lookup"><span data-stu-id="9ee19-107">In the following example, additional Azure Active Directory (AAD) Microsoft Graph API scopes are required by an app to read user data and send mail.</span></span> <span data-ttu-id="9ee19-108">Después de agregar los permisos de Microsoft Graph API en el portal`Program.Main`de Azure AAD, los ámbitos adicionales se configuran en la aplicación cliente ( , *Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="9ee19-108">After adding the Microsoft Graph API permissions in the Azure AAD portal, the additional scopes are configured in the Client app (`Program.Main`, *Program.cs*):</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.ProviderOptions.AdditionalScopesToConsent.Add(
        "https://graph.microsoft.com/Mail.Send");
    options.ProviderOptions.AdditionalScopesToConsent.Add(
        "https://graph.microsoft.com/User.Read");
}
```

<span data-ttu-id="9ee19-109">El `IAccessTokenProvider.RequestToken` método proporciona una sobrecarga que permite a una aplicación aprovisionar un token con un conjunto determinado de ámbitos, como se muestra en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="9ee19-109">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision a token with a given set of scopes, as seen in the following example:</span></span>

```csharp
var tokenResult = await AuthenticationService.RequestAccessToken(
    new AccessTokenRequestOptions
    {
        Scopes = new[] { "https://graph.microsoft.com/Mail.Send", 
            "https://graph.microsoft.com/User.Read" }
    });

if (tokenResult.TryGetToken(out var token))
{
    ...
}
```

<span data-ttu-id="9ee19-110">`TryGetToken`Devuelve:</span><span class="sxs-lookup"><span data-stu-id="9ee19-110">`TryGetToken` returns:</span></span>

* <span data-ttu-id="9ee19-111">`true`con `token` el uso.</span><span class="sxs-lookup"><span data-stu-id="9ee19-111">`true` with the `token` for use.</span></span>
* <span data-ttu-id="9ee19-112">`false`si no se recupera el token.</span><span class="sxs-lookup"><span data-stu-id="9ee19-112">`false` if the token isn't retrieved.</span></span>

## <a name="handle-token-request-errors"></a><span data-ttu-id="9ee19-113">Controlar errores de solicitud de token</span><span class="sxs-lookup"><span data-stu-id="9ee19-113">Handle token request errors</span></span>

<span data-ttu-id="9ee19-114">Cuando una aplicación de una sola página (SPA) autentica a un usuario usando Open ID Connect (OIDC), el estado de autenticación se mantiene localmente dentro del SPA y en el proveedor de identidad (IP) en forma de una cookie de sesión que se fija como resultado del usuario que proporciona sus credenciales.</span><span class="sxs-lookup"><span data-stu-id="9ee19-114">When a Single Page Application (SPA) authenticates a user using Open ID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="9ee19-115">Los tokens que la ip emite para el usuario suelen ser válidos durante períodos cortos de tiempo, aproximadamente una hora normalmente, por lo que la aplicación cliente debe capturar regularmente nuevos tokens.</span><span class="sxs-lookup"><span data-stu-id="9ee19-115">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="9ee19-116">De lo contrario, el usuario cerraría la sesión después de que expiren los tokens concedidos.</span><span class="sxs-lookup"><span data-stu-id="9ee19-116">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="9ee19-117">En la mayoría de los casos, los clientes OIDC pueden aprovisionar nuevos tokens sin necesidad de que el usuario se autentique de nuevo gracias al estado de autenticación o "sesión" que se mantiene dentro de la IP.</span><span class="sxs-lookup"><span data-stu-id="9ee19-117">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="9ee19-118">Hay algunos casos en los que el cliente no puede obtener un token sin la interacción del usuario, por ejemplo, cuando por alguna razón el usuario cierra la sesión explícitamente de la ip.</span><span class="sxs-lookup"><span data-stu-id="9ee19-118">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="9ee19-119">Este escenario se produce `https://login.microsoftonline.com` si un usuario visita y cierra sesión. En estos escenarios, la aplicación no sabe inmediatamente que el usuario ha cerrado la sesión. Es posible que cualquier token que el cliente tenga ya no sea válido.</span><span class="sxs-lookup"><span data-stu-id="9ee19-119">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="9ee19-120">Además, el cliente no puede aprovisionar un nuevo token sin la interacción del usuario después de que expire el token actual.</span><span class="sxs-lookup"><span data-stu-id="9ee19-120">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="9ee19-121">Estos escenarios no son específicos de la autenticación basada en token.</span><span class="sxs-lookup"><span data-stu-id="9ee19-121">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="9ee19-122">Son parte de la naturaleza de las ZAP.</span><span class="sxs-lookup"><span data-stu-id="9ee19-122">They are part of the nature of SPAs.</span></span> <span data-ttu-id="9ee19-123">Un SPA que utiliza cookies tampoco puede llamar a una API de servidor si se elimina la cookie de autenticación.</span><span class="sxs-lookup"><span data-stu-id="9ee19-123">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="9ee19-124">Cuando una aplicación realiza llamadas a la API a recursos protegidos, debe tener en cuenta lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="9ee19-124">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="9ee19-125">Para aprovisionar un nuevo token de acceso para llamar a la API, es posible que el usuario tenga que autenticarse de nuevo.</span><span class="sxs-lookup"><span data-stu-id="9ee19-125">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="9ee19-126">Incluso si el cliente tiene un token que parece ser válido, la llamada al servidor puede producir un error porque el usuario revocó el token.</span><span class="sxs-lookup"><span data-stu-id="9ee19-126">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="9ee19-127">Cuando la aplicación solicita un token, hay dos resultados posibles:</span><span class="sxs-lookup"><span data-stu-id="9ee19-127">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="9ee19-128">La solicitud se realiza correctamente y la aplicación tiene un token válido.</span><span class="sxs-lookup"><span data-stu-id="9ee19-128">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="9ee19-129">Se produce un error en la solicitud y la aplicación debe autenticar al usuario de nuevo para obtener un nuevo token.</span><span class="sxs-lookup"><span data-stu-id="9ee19-129">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="9ee19-130">Cuando se produce un error en una solicitud de token, debe decidir si desea guardar cualquier estado actual antes de realizar una redirección.</span><span class="sxs-lookup"><span data-stu-id="9ee19-130">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="9ee19-131">Existen varios enfoques con niveles crecientes de complejidad:</span><span class="sxs-lookup"><span data-stu-id="9ee19-131">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="9ee19-132">Almacene el estado actual de la página en el almacenamiento de sesión.</span><span class="sxs-lookup"><span data-stu-id="9ee19-132">Store the current page state in session storage.</span></span> <span data-ttu-id="9ee19-133">Durante `OnInitializeAsync`, compruebe si el estado se puede restaurar antes de continuar.</span><span class="sxs-lookup"><span data-stu-id="9ee19-133">During `OnInitializeAsync`, check if state can be restored before continuing.</span></span>
* <span data-ttu-id="9ee19-134">Agregue un parámetro de cadena de consulta y úselo como una forma de indicar a la aplicación que necesita volver a hidratar el estado guardado anteriormente.</span><span class="sxs-lookup"><span data-stu-id="9ee19-134">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="9ee19-135">Agregue un parámetro de cadena de consulta con un identificador único para almacenar datos en el almacenamiento de sesión sin correr el riesgo de colisiones con otros elementos.</span><span class="sxs-lookup"><span data-stu-id="9ee19-135">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="9ee19-136">El ejemplo siguiente muestra cómo:</span><span class="sxs-lookup"><span data-stu-id="9ee19-136">The following example shows how to:</span></span>

* <span data-ttu-id="9ee19-137">Conservar el estado antes de redirigir a la página de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="9ee19-137">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="9ee19-138">Recupere el estado anterior después de la autenticación mediante el parámetro de cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="9ee19-138">Recover the previous state afterward authentication using the query string parameter.</span></span>

```razor
<EditForm Model="User" @onsubmit="OnSaveAsync">
    <label>User
        <InputText @bind-Value="User.Name" />
    </label>
    <label>Last name
        <InputText @bind-Value="User.LastName" />
    </label>
</EditForm>

@code {
    public class Profile
    {
        public string Name { get; set; }
        public string LastName { get; set; }
    }

    public Profile User { get; set; } = new Profile();

    protected async override Task OnInitializedAsync()
    {
        var currentQuery = new Uri(Navigation.Uri).Query;

        if (currentQuery.Contains("state=resumeSavingProfile"))
        {
            User = await JS.InvokeAsync<Profile>("sessionStorage.getState", 
                "resumeSavingProfile");
        }
    }

    public async Task OnSaveAsync()
    {
        var httpClient = new HttpClient();
        httpClient.BaseAddress = new Uri(Navigation.BaseUri);

        var resumeUri = Navigation.Uri + $"?state=resumeSavingProfile";

        var tokenResult = await AuthenticationService.RequestAccessToken(
            new AccessTokenRequestOptions
            {
                ReturnUrl = resumeUri
            });

        if (tokenResult.TryGetToken(out var token))
        {
            httpClient.DefaultRequestHeaders.Add("Authorization", 
                $"Bearer {token.Value}");
            await httpClient.PostAsJsonAsync("Save", User);
        }
        else
        {
            await JS.InvokeVoidAsync("sessionStorage.setState", 
                "resumeSavingProfile", User);
            Navigation.NavigateTo(tokenResult.RedirectUrl);
        }
    }
}
```

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="9ee19-139">Guardar el estado de la aplicación antes de una operación de autenticación</span><span class="sxs-lookup"><span data-stu-id="9ee19-139">Save app state before an authentication operation</span></span>

<span data-ttu-id="9ee19-140">Durante una operación de autenticación, hay casos en los que desea guardar el estado de la aplicación antes de redirigir el explorador a la dirección IP.</span><span class="sxs-lookup"><span data-stu-id="9ee19-140">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="9ee19-141">Este puede ser el caso cuando se usa algo como un contenedor de estado y desea restaurar el estado después de que la autenticación se realice correctamente.</span><span class="sxs-lookup"><span data-stu-id="9ee19-141">This can be the case when you are using something like a state container and you want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="9ee19-142">Puede usar un objeto de estado de autenticación personalizado para conservar el estado específico de la aplicación o una referencia a él y restaurar ese estado una vez que la operación de autenticación se complete correctamente.</span><span class="sxs-lookup"><span data-stu-id="9ee19-142">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state once the authentication operation successfully completes.</span></span>

<span data-ttu-id="9ee19-143">`Authentication`componente (*Pages/Authentication.razor*):</span><span class="sxs-lookup"><span data-stu-id="9ee19-143">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/authentication/{action}"
@inject JSRuntime JS
@inject StateContainer State
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorViewCore Action="@Action" 
    AuthenticationState="AuthenticationState" OnLoginSucceded="RestoreState" 
    OnLogoutSucceded="RestoreState" />

@code {
    [Parameter]
    public string Action { get; set; }

    public class ApplicationAuthenticationState : RemoteAuthenticationState
    {
        public string Id { get; set; }
    }

    protected async override Task OnInitializedAsync()
    {
        if (RemoteAuthenticationActions.IsAction(RemoteAuthenticationActions.LogIn, 
            Action))
        {
            AuthenticationState.Id = Guid.NewGuid().ToString();
            await JS.InvokeVoidAsync("sessionStorage.setKey", 
                AuthenticationState.Id, State.Store());
        }
    }

    public async Task RestoreState(ApplicationAuthenticationState state)
    {
        var stored = await JS.InvokeAsync<string>("sessionStorage.getKey", 
            state.Id);
        State.FromStore(stored);
    }

    public ApplicationAuthenticationState AuthenticationState { get; set; } = 
        new ApplicationAuthenticationState();
}
```

## <a name="customize-app-routes"></a><span data-ttu-id="9ee19-144">Personalizar rutas de aplicaciones</span><span class="sxs-lookup"><span data-stu-id="9ee19-144">Customize app routes</span></span>

<span data-ttu-id="9ee19-145">De forma `Microsoft.AspNetCore.Components.WebAssembly.Authentication` predeterminada, la biblioteca utiliza las rutas que se muestran en la tabla siguiente para representar diferentes estados de autenticación.</span><span class="sxs-lookup"><span data-stu-id="9ee19-145">By default, the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="9ee19-146">Enrutar</span><span class="sxs-lookup"><span data-stu-id="9ee19-146">Route</span></span>                            | <span data-ttu-id="9ee19-147">Propósito</span><span class="sxs-lookup"><span data-stu-id="9ee19-147">Purpose</span></span> |
| -------------------------------- | ------- |
| `authentication/login`           | <span data-ttu-id="9ee19-148">Desencadena una operación de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="9ee19-148">Triggers a sign-in operation.</span></span> |
| `authentication/login-callback`  | <span data-ttu-id="9ee19-149">Controla el resultado de cualquier operación de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="9ee19-149">Handles the result of any sign-in operation.</span></span> |
| `authentication/login-failed`    | <span data-ttu-id="9ee19-150">Muestra mensajes de error cuando se produce un error en la operación de inicio de sesión por algún motivo.</span><span class="sxs-lookup"><span data-stu-id="9ee19-150">Displays error messages when the sign-in operation fails for some reason.</span></span> |
| `authentication/logout`          | <span data-ttu-id="9ee19-151">Activa una operación de cierre de sesión.</span><span class="sxs-lookup"><span data-stu-id="9ee19-151">Triggers a sign-out operation.</span></span> |
| `authentication/logout-callback` | <span data-ttu-id="9ee19-152">Controla el resultado de una operación de cierre de sesión.</span><span class="sxs-lookup"><span data-stu-id="9ee19-152">Handles the result of a sign-out operation.</span></span> |
| `authentication/logout-failed`   | <span data-ttu-id="9ee19-153">Muestra mensajes de error cuando se produce un error en la operación de cierre de sesión por algún motivo.</span><span class="sxs-lookup"><span data-stu-id="9ee19-153">Displays error messages when the sign-out operation fails for some reason.</span></span> |
| `authentication/logged-out`      | <span data-ttu-id="9ee19-154">Indica que el usuario ha cierre la sesión correctamente.</span><span class="sxs-lookup"><span data-stu-id="9ee19-154">Indicates that the user has successfully logout.</span></span> |
| `authentication/profile`         | <span data-ttu-id="9ee19-155">Activa una operación para editar el perfil de usuario.</span><span class="sxs-lookup"><span data-stu-id="9ee19-155">Triggers an operation to edit the user profile.</span></span> |
| `authentication/register`        | <span data-ttu-id="9ee19-156">Desencadena una operación para registrar un nuevo usuario.</span><span class="sxs-lookup"><span data-stu-id="9ee19-156">Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="9ee19-157">Las rutas mostradas en la tabla `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`anterior son configurables a través de .</span><span class="sxs-lookup"><span data-stu-id="9ee19-157">The routes shown in the preceding table are configurable via `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`.</span></span> <span data-ttu-id="9ee19-158">Al establecer opciones para proporcionar rutas personalizadas, confirme que la aplicación tiene una ruta que controla cada ruta de acceso.</span><span class="sxs-lookup"><span data-stu-id="9ee19-158">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="9ee19-159">En el ejemplo siguiente, todas las `/security`rutas de acceso tienen el prefijo .</span><span class="sxs-lookup"><span data-stu-id="9ee19-159">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="9ee19-160">`Authentication`componente (*Pages/Authentication.razor*):</span><span class="sxs-lookup"><span data-stu-id="9ee19-160">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="9ee19-161">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="9ee19-161">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddApiAuthorization(options => { 
    options.AuthenticationPaths.LogInPath = "security/login";
    options.AuthenticationPaths.LogInCallbackPath = "security/login-callback";
    options.AuthenticationPaths.LogInFailedPath = "security/login-failed";
    options.AuthenticationPaths.LogOutPath = "security/logout";
    options.AuthenticationPaths.LogOutCallbackPath = "security/logout-callback";
    options.AuthenticationPaths.LogOutFailedPath = "security/logout-failed";
    options.AuthenticationPaths.LogOutSucceededPath = "security/logged-out";
    options.AuthenticationPaths.ProfilePath = "security/profile";
    options.AuthenticationPaths.RegisterPath = "security/register";
});
```

<span data-ttu-id="9ee19-162">Si el requisito requiere rutas completamente diferentes, establezca las `RemoteAuthenticatorView` rutas como se describió anteriormente y represente el parámetro de acción explícita:</span><span class="sxs-lookup"><span data-stu-id="9ee19-162">If the requirement calls for completely different paths, set the routes as described previously and render the `RemoteAuthenticatorView` with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="9ee19-163">Puede dividir la interfaz de usuario en páginas diferentes si decide hacerlo.</span><span class="sxs-lookup"><span data-stu-id="9ee19-163">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="9ee19-164">Personalizar la interfaz de usuario de autenticación</span><span class="sxs-lookup"><span data-stu-id="9ee19-164">Customize the authentication user interface</span></span>

<span data-ttu-id="9ee19-165">`RemoteAuthenticatorView`incluye un conjunto predeterminado de piezas de interfaz de usuario para cada estado de autenticación.</span><span class="sxs-lookup"><span data-stu-id="9ee19-165">`RemoteAuthenticatorView` includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="9ee19-166">Cada estado se puede personalizar `RenderFragment`pasando un archivo .</span><span class="sxs-lookup"><span data-stu-id="9ee19-166">Each state can be customized by passing in a custom `RenderFragment`.</span></span> <span data-ttu-id="9ee19-167">Para personalizar el texto mostrado durante el proceso `RemoteAuthenticatorView` de inicio de sesión inicial, puede cambiar lo siguiente.</span><span class="sxs-lookup"><span data-stu-id="9ee19-167">To customize the displayed text during the initial login process, can change the `RemoteAuthenticatorView` as follows.</span></span>

<span data-ttu-id="9ee19-168">`Authentication`componente (*Pages/Authentication.razor*):</span><span class="sxs-lookup"><span data-stu-id="9ee19-168">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action">
    <LoggingIn>
        You are about to be redirected to https://login.microsoftonline.com.
    </LoggingIn>
</RemoteAuthenticatorView>

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="9ee19-169">Tiene `RemoteAuthenticatorView` un fragmento que se puede utilizar por ruta de autenticación que se muestra en la tabla siguiente.</span><span class="sxs-lookup"><span data-stu-id="9ee19-169">The `RemoteAuthenticatorView` has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="9ee19-170">Enrutar</span><span class="sxs-lookup"><span data-stu-id="9ee19-170">Route</span></span>                            | <span data-ttu-id="9ee19-171">Fragmento</span><span class="sxs-lookup"><span data-stu-id="9ee19-171">Fragment</span></span>                |
| -------------------------------- | ----------------------- |
| `authentication/login`           | `<LoggingIn>`           |
| `authentication/login-callback`  | `<CompletingLoggingIn>` |
| `authentication/login-failed`    | `<LogInFailed>`         |
| `authentication/logout`          | `<LogOut>`              |
| `authentication/logout-callback` | `<CompletingLogOut>`    |
| `authentication/logout-failed`   | `<LogOutFailed>`        |
| `authentication/logged-out`      | `<LogOutSucceeded>`     |
| `authentication/profile`         | `<UserProfile>`         |
| `authentication/register`        | `<Registering>`         |
