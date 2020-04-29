---
title: ASP.NET Core Blazor escenarios de seguridad adicionales de webassembly
author: guardrex
description: Aprenda a configurar Blazor webassembly para escenarios de seguridad adicionales.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/27/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/additional-scenarios
ms.openlocfilehash: 093498c3e0d42430c66c66a0998bcc44f62d1e0d
ms.sourcegitcommit: 56861af66bb364a5d60c3c72d133d854b4cf292d
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2020
ms.locfileid: "82206156"
---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="ef15e-103">ASP.NET Core más escenarios de seguridad adicionales de webassembly</span><span class="sxs-lookup"><span data-stu-id="ef15e-103">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="ef15e-104">Por [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="ef15e-104">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

## <a name="request-additional-access-tokens"></a><span data-ttu-id="ef15e-105">Solicitar tokens de acceso adicionales</span><span class="sxs-lookup"><span data-stu-id="ef15e-105">Request additional access tokens</span></span>

<span data-ttu-id="ef15e-106">La mayoría de las aplicaciones solo requieren un token de acceso para interactuar con los recursos protegidos que usan.</span><span class="sxs-lookup"><span data-stu-id="ef15e-106">Most apps only require an access token to interact with the protected resources that they use.</span></span> <span data-ttu-id="ef15e-107">En algunos escenarios, una aplicación podría requerir más de un token para interactuar con dos o más recursos.</span><span class="sxs-lookup"><span data-stu-id="ef15e-107">In some scenarios, an app might require more than one token in order to interact with two or more resources.</span></span>

<span data-ttu-id="ef15e-108">En el siguiente ejemplo, una aplicación necesita los ámbitos de API de Azure Active Directory adicionales (AAD) Microsoft Graph para leer los datos de usuario y enviar correo.</span><span class="sxs-lookup"><span data-stu-id="ef15e-108">In the following example, additional Azure Active Directory (AAD) Microsoft Graph API scopes are required by an app to read user data and send mail.</span></span> <span data-ttu-id="ef15e-109">Después de agregar los permisos de Microsoft Graph API en el portal de Azure AAD, los ámbitos adicionales se configuran en`Program.Main`la aplicación cliente (, *Program.CS*):</span><span class="sxs-lookup"><span data-stu-id="ef15e-109">After adding the Microsoft Graph API permissions in the Azure AAD portal, the additional scopes are configured in the Client app (`Program.Main`, *Program.cs*):</span></span>

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

<span data-ttu-id="ef15e-110">El `IAccessTokenProvider.RequestToken` método proporciona una sobrecarga que permite a una aplicación aprovisionar un token de acceso con un conjunto determinado de ámbitos, tal como se muestra en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="ef15e-110">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision an access token with a given set of scopes, as seen in the following example:</span></span>

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

<span data-ttu-id="ef15e-111">`TryGetToken`Devuelve</span><span class="sxs-lookup"><span data-stu-id="ef15e-111">`TryGetToken` returns:</span></span>

* <span data-ttu-id="ef15e-112">`true`con para `token` su uso.</span><span class="sxs-lookup"><span data-stu-id="ef15e-112">`true` with the `token` for use.</span></span>
* <span data-ttu-id="ef15e-113">`false`Si el token no se recupera.</span><span class="sxs-lookup"><span data-stu-id="ef15e-113">`false` if the token isn't retrieved.</span></span>

## <a name="attach-tokens-to-outgoing-requests"></a><span data-ttu-id="ef15e-114">Adjuntar tokens a solicitudes salientes</span><span class="sxs-lookup"><span data-stu-id="ef15e-114">Attach tokens to outgoing requests</span></span>

<span data-ttu-id="ef15e-115">El `AuthorizationMessageHandler` servicio se puede usar con `HttpClient` para adjuntar tokens de acceso a las solicitudes salientes.</span><span class="sxs-lookup"><span data-stu-id="ef15e-115">The `AuthorizationMessageHandler` service can be used with `HttpClient` to attach access tokens to outgoing requests.</span></span> <span data-ttu-id="ef15e-116">Los tokens se adquieren `IAccessTokenProvider` con el servicio existente.</span><span class="sxs-lookup"><span data-stu-id="ef15e-116">Tokens are acquired using the existing `IAccessTokenProvider` service.</span></span> <span data-ttu-id="ef15e-117">Si no se puede adquirir un token, `AccessTokenNotAvailableException` se produce una excepción.</span><span class="sxs-lookup"><span data-stu-id="ef15e-117">If a token can't be acquired, an `AccessTokenNotAvailableException` is thrown.</span></span> <span data-ttu-id="ef15e-118">`AccessTokenNotAvailableException`tiene un `Redirect` método que se puede utilizar para navegar por el usuario al proveedor de identidades para adquirir un nuevo token.</span><span class="sxs-lookup"><span data-stu-id="ef15e-118">`AccessTokenNotAvailableException` has a `Redirect` method that can be used to navigate the user to the identity provider to acquire a new token.</span></span> <span data-ttu-id="ef15e-119">Se `AuthorizationMessageHandler` puede configurar con las direcciones URL autorizadas, los ámbitos y la dirección URL de `ConfigureHandler` retorno mediante el método.</span><span class="sxs-lookup"><span data-stu-id="ef15e-119">The `AuthorizationMessageHandler` can be configured with the authorized URLs, scopes, and return URL using the `ConfigureHandler` method.</span></span>

<span data-ttu-id="ef15e-120">En el ejemplo siguiente, `AuthorizationMessageHandler` configura `HttpClient` en `Program.Main` (*Program.CS*):</span><span class="sxs-lookup"><span data-stu-id="ef15e-120">In the following example, `AuthorizationMessageHandler` configures an `HttpClient` in `Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddSingleton(sp =>
{
    return new HttpClient(sp.GetRequiredService<AuthorizationMessageHandler>()
        .ConfigureHandler(
            new [] { "https://www.example.com/base" },
            scopes: new[] { "example.read", "example.write" }))
        {
            BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
        };
});
```

<span data-ttu-id="ef15e-121">Para mayor comodidad, `BaseAddressAuthorizationMessageHandler` se incluye una que está preconfigurada con la dirección base de la aplicación como una dirección URL autorizada.</span><span class="sxs-lookup"><span data-stu-id="ef15e-121">For convenience, a `BaseAddressAuthorizationMessageHandler` is included that's preconfigured with the app base address as an authorized URL.</span></span> <span data-ttu-id="ef15e-122">Las plantillas de webassembly increíblemente habilitadas para la autenticación ahora usan [IHttpClientFactory](https://docs.microsoft.com/aspnet/core/fundamentals/http-requests) para `HttpClient` configurar un `BaseAddressAuthorizationMessageHandler`con:</span><span class="sxs-lookup"><span data-stu-id="ef15e-122">The authentication-enabled Blazor WebAssembly templates now use [IHttpClientFactory](https://docs.microsoft.com/aspnet/core/fundamentals/http-requests) to set up an `HttpClient` with the `BaseAddressAuthorizationMessageHandler`:</span></span>

```csharp
builder.Services.AddHttpClient("BlazorWithIdentityApp1.ServerAPI", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
        .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("BlazorWithIdentityApp1.ServerAPI"));
```

<span data-ttu-id="ef15e-123">Cuando el cliente se crea con `CreateClient` en el ejemplo anterior, `HttpClient` se proporcionan instancias de que incluyen tokens de acceso al hacer solicitudes al proyecto de servidor.</span><span class="sxs-lookup"><span data-stu-id="ef15e-123">Where the client is created with `CreateClient` in the preceding example, the `HttpClient` is supplied instances that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="ef15e-124">A continuación `HttpClient` , se usa el configurado para hacer solicitudes autorizadas `try-catch` mediante un patrón simple.</span><span class="sxs-lookup"><span data-stu-id="ef15e-124">The configured `HttpClient` is then used to make authorized requests using a simple `try-catch` pattern.</span></span> <span data-ttu-id="ef15e-125">El siguiente `FetchData` componente solicita datos de previsión meteorológica:</span><span class="sxs-lookup"><span data-stu-id="ef15e-125">The following `FetchData` component requests weather forecast data:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    try
    {
        forecasts = 
            await Http.GetFromJsonAsync<WeatherForecast[]>("WeatherForecast");
    }
    catch (AccessTokenNotAvailableException exception)
    {
        exception.Redirect();
    }
}
```

<span data-ttu-id="ef15e-126">Como alternativa, puede definir un cliente con tipo que controle todos los problemas de adquisición de tokens y HTTP en una sola clase:</span><span class="sxs-lookup"><span data-stu-id="ef15e-126">Alternatively, you can define a typed client that handles all of the HTTP and token acquisition concerns within a single class:</span></span>

<span data-ttu-id="ef15e-127">*WeatherClient.CS*:</span><span class="sxs-lookup"><span data-stu-id="ef15e-127">*WeatherClient.cs*:</span></span>

```csharp
public class WeatherClient
{
    private readonly HttpClient httpClient;
 
    public WeatherClient(HttpClient httpClient)
    {
        this.httpClient = httpClient;
    }
 
    public async Task<IEnumerable<WeatherForecast>> GetWeatherForeacasts()
    {
        IEnumerable<WeatherForecast> forecasts = new WeatherForecast[0];

        try
        {
            forecasts = await httpClient.GetFromJsonAsync<WeatherForecast[]>(
                "WeatherForecast");
        }
        catch (AccessTokenNotAvailableException exception)
        {
            exception.Redirect();
        }

        return forecasts;
    }
}
```

<span data-ttu-id="ef15e-128">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="ef15e-128">*Program.cs*:</span></span>

```csharp
builder.Services.AddHttpClient<WeatherClient>(
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

<span data-ttu-id="ef15e-129">*FetchData. Razor*:</span><span class="sxs-lookup"><span data-stu-id="ef15e-129">*FetchData.razor*:</span></span>

```razor
@inject WeatherClient WeatherClient

...

protected override async Task OnInitializedAsync()
{
    forecasts = await WeatherClient.GetWeatherForeacasts();
}
```

## <a name="handle-token-request-errors"></a><span data-ttu-id="ef15e-130">Controlar errores de solicitudes de token</span><span class="sxs-lookup"><span data-stu-id="ef15e-130">Handle token request errors</span></span>

<span data-ttu-id="ef15e-131">Cuando una aplicación de una sola página (SPA) autentica a un usuario mediante Open ID Connect (OIDC), el estado de autenticación se mantiene localmente en el SPA y en el proveedor de identidades (IP) en forma de una cookie de sesión que se establece como resultado del usuario que proporciona sus credenciales.</span><span class="sxs-lookup"><span data-stu-id="ef15e-131">When a Single Page Application (SPA) authenticates a user using Open ID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="ef15e-132">Normalmente, los tokens que emite la IP para el usuario son válidos durante breves períodos de tiempo, aproximadamente una hora, por lo que la aplicación cliente debe capturar nuevos tokens con regularidad.</span><span class="sxs-lookup"><span data-stu-id="ef15e-132">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="ef15e-133">De lo contrario, se cerrará la sesión del usuario después de que expiren los tokens concedidos.</span><span class="sxs-lookup"><span data-stu-id="ef15e-133">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="ef15e-134">En la mayoría de los casos, los clientes de OIDC pueden aprovisionar nuevos tokens sin necesidad de que el usuario se autentique de nuevo gracias al estado de autenticación o a la "sesión" que se mantiene dentro de la dirección IP.</span><span class="sxs-lookup"><span data-stu-id="ef15e-134">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="ef15e-135">Hay algunos casos en los que el cliente no puede obtener un token sin la interacción del usuario, por ejemplo, cuando por alguna razón el usuario cierra la sesión explícitamente desde la dirección IP.</span><span class="sxs-lookup"><span data-stu-id="ef15e-135">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="ef15e-136">Este escenario se produce si un usuario `https://login.microsoftonline.com` visita y cierra la sesión. En estos casos, la aplicación no sabe inmediatamente que el usuario ha cerrado la sesión. Es posible que el token que contiene el cliente ya no sea válido.</span><span class="sxs-lookup"><span data-stu-id="ef15e-136">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="ef15e-137">Además, el cliente no puede aprovisionar un token nuevo sin la interacción del usuario después de que expire el token actual.</span><span class="sxs-lookup"><span data-stu-id="ef15e-137">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="ef15e-138">Estos escenarios no son específicos de la autenticación basada en tokens.</span><span class="sxs-lookup"><span data-stu-id="ef15e-138">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="ef15e-139">Forman parte de la naturaleza de spa.</span><span class="sxs-lookup"><span data-stu-id="ef15e-139">They are part of the nature of SPAs.</span></span> <span data-ttu-id="ef15e-140">Un SPA que use cookies tampoco podrá llamar a una API de servidor si se quita la cookie de autenticación.</span><span class="sxs-lookup"><span data-stu-id="ef15e-140">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="ef15e-141">Cuando una aplicación realiza llamadas API a recursos protegidos, debe tener en cuenta lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="ef15e-141">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="ef15e-142">Para aprovisionar un nuevo token de acceso para llamar a la API, es posible que sea necesario volver a autenticar al usuario.</span><span class="sxs-lookup"><span data-stu-id="ef15e-142">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="ef15e-143">Incluso si el cliente tiene un token que parece ser válido, es posible que se produzca un error en la llamada al servidor porque el usuario revocó el token.</span><span class="sxs-lookup"><span data-stu-id="ef15e-143">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="ef15e-144">Cuando la aplicación solicita un token, hay dos resultados posibles:</span><span class="sxs-lookup"><span data-stu-id="ef15e-144">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="ef15e-145">La solicitud se realiza correctamente y la aplicación tiene un token válido.</span><span class="sxs-lookup"><span data-stu-id="ef15e-145">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="ef15e-146">Se produce un error en la solicitud y la aplicación debe autenticar al usuario de nuevo para obtener un nuevo token.</span><span class="sxs-lookup"><span data-stu-id="ef15e-146">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="ef15e-147">Cuando se produce un error en una solicitud de token, debe decidir si desea guardar el estado actual antes de realizar una redirección.</span><span class="sxs-lookup"><span data-stu-id="ef15e-147">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="ef15e-148">Existen varios enfoques con niveles crecientes de complejidad:</span><span class="sxs-lookup"><span data-stu-id="ef15e-148">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="ef15e-149">Almacenar el estado de la página actual en el almacenamiento de sesión.</span><span class="sxs-lookup"><span data-stu-id="ef15e-149">Store the current page state in session storage.</span></span> <span data-ttu-id="ef15e-150">Durante `OnInitializeAsync`, compruebe si el estado se puede restaurar antes de continuar.</span><span class="sxs-lookup"><span data-stu-id="ef15e-150">During `OnInitializeAsync`, check if state can be restored before continuing.</span></span>
* <span data-ttu-id="ef15e-151">Agregue un parámetro de cadena de consulta y úselo como una manera de señalar la aplicación que necesita para volver a hidratar el estado guardado anteriormente.</span><span class="sxs-lookup"><span data-stu-id="ef15e-151">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="ef15e-152">Agregue un parámetro de cadena de consulta con un identificador único para almacenar los datos en el almacenamiento de sesión sin poner en peligro las colisiones con otros elementos.</span><span class="sxs-lookup"><span data-stu-id="ef15e-152">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="ef15e-153">El ejemplo siguiente muestra cómo:</span><span class="sxs-lookup"><span data-stu-id="ef15e-153">The following example shows how to:</span></span>

* <span data-ttu-id="ef15e-154">Conservar el estado antes de redirigir a la página de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="ef15e-154">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="ef15e-155">Recupere el estado anterior después de la autenticación mediante el parámetro de cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="ef15e-155">Recover the previous state afterward authentication using the query string parameter.</span></span>

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

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="ef15e-156">Guardar el estado de la aplicación antes de una operación de autenticación</span><span class="sxs-lookup"><span data-stu-id="ef15e-156">Save app state before an authentication operation</span></span>

<span data-ttu-id="ef15e-157">Durante una operación de autenticación, hay casos en los que desea guardar el estado de la aplicación antes de que el explorador se redirija a la dirección IP.</span><span class="sxs-lookup"><span data-stu-id="ef15e-157">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="ef15e-158">Esto puede ser así cuando se usa algo como un contenedor de estado y se desea restaurar el estado después de que la autenticación se realice correctamente.</span><span class="sxs-lookup"><span data-stu-id="ef15e-158">This can be the case when you are using something like a state container and you want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="ef15e-159">Puede usar un objeto de estado de autenticación personalizado para conservar el estado específico de la aplicación o una referencia a él y restaurar ese estado una vez que la operación de autenticación se complete correctamente.</span><span class="sxs-lookup"><span data-stu-id="ef15e-159">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state once the authentication operation successfully completes.</span></span>

<span data-ttu-id="ef15e-160">`Authentication`componente (*páginas/autenticación. Razor*):</span><span class="sxs-lookup"><span data-stu-id="ef15e-160">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

## <a name="customize-app-routes"></a><span data-ttu-id="ef15e-161">Personalización de rutas de aplicación</span><span class="sxs-lookup"><span data-stu-id="ef15e-161">Customize app routes</span></span>

<span data-ttu-id="ef15e-162">De forma predeterminada, `Microsoft.AspNetCore.Components.WebAssembly.Authentication` la biblioteca utiliza las rutas que se muestran en la tabla siguiente para representar diferentes Estados de autenticación.</span><span class="sxs-lookup"><span data-stu-id="ef15e-162">By default, the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="ef15e-163">Enrutar</span><span class="sxs-lookup"><span data-stu-id="ef15e-163">Route</span></span>                            | <span data-ttu-id="ef15e-164">Propósito</span><span class="sxs-lookup"><span data-stu-id="ef15e-164">Purpose</span></span> |
| -------------------------------- | ------- |
| `authentication/login`           | <span data-ttu-id="ef15e-165">Desencadena una operación de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="ef15e-165">Triggers a sign-in operation.</span></span> |
| `authentication/login-callback`  | <span data-ttu-id="ef15e-166">Controla el resultado de cualquier operación de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="ef15e-166">Handles the result of any sign-in operation.</span></span> |
| `authentication/login-failed`    | <span data-ttu-id="ef15e-167">Muestra mensajes de error cuando se produce un error en la operación de inicio de sesión por algún motivo.</span><span class="sxs-lookup"><span data-stu-id="ef15e-167">Displays error messages when the sign-in operation fails for some reason.</span></span> |
| `authentication/logout`          | <span data-ttu-id="ef15e-168">Desencadena una operación de cierre de sesión.</span><span class="sxs-lookup"><span data-stu-id="ef15e-168">Triggers a sign-out operation.</span></span> |
| `authentication/logout-callback` | <span data-ttu-id="ef15e-169">Controla el resultado de una operación de cierre de sesión.</span><span class="sxs-lookup"><span data-stu-id="ef15e-169">Handles the result of a sign-out operation.</span></span> |
| `authentication/logout-failed`   | <span data-ttu-id="ef15e-170">Muestra mensajes de error cuando se produce un error en la operación de cierre de sesión por algún motivo.</span><span class="sxs-lookup"><span data-stu-id="ef15e-170">Displays error messages when the sign-out operation fails for some reason.</span></span> |
| `authentication/logged-out`      | <span data-ttu-id="ef15e-171">Indica que el usuario ha cerrado la sesión correctamente.</span><span class="sxs-lookup"><span data-stu-id="ef15e-171">Indicates that the user has successfully logout.</span></span> |
| `authentication/profile`         | <span data-ttu-id="ef15e-172">Desencadena una operación para editar el perfil de usuario.</span><span class="sxs-lookup"><span data-stu-id="ef15e-172">Triggers an operation to edit the user profile.</span></span> |
| `authentication/register`        | <span data-ttu-id="ef15e-173">Desencadena una operación para registrar un nuevo usuario.</span><span class="sxs-lookup"><span data-stu-id="ef15e-173">Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="ef15e-174">Las rutas que se muestran en la tabla anterior se `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`pueden configurar mediante.</span><span class="sxs-lookup"><span data-stu-id="ef15e-174">The routes shown in the preceding table are configurable via `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`.</span></span> <span data-ttu-id="ef15e-175">Al establecer las opciones para proporcionar rutas personalizadas, confirme que la aplicación tiene una ruta que controla cada ruta de acceso.</span><span class="sxs-lookup"><span data-stu-id="ef15e-175">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="ef15e-176">En el ejemplo siguiente, todas las rutas de acceso tienen el `/security`prefijo.</span><span class="sxs-lookup"><span data-stu-id="ef15e-176">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="ef15e-177">`Authentication`componente (*páginas/autenticación. Razor*):</span><span class="sxs-lookup"><span data-stu-id="ef15e-177">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="ef15e-178">`Program.Main`(*Program.CS*):</span><span class="sxs-lookup"><span data-stu-id="ef15e-178">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="ef15e-179">Si el requisito requiere rutas de acceso completamente diferentes, establezca las rutas como se describió anteriormente `RemoteAuthenticatorView` y represente el con un parámetro de acción explícito:</span><span class="sxs-lookup"><span data-stu-id="ef15e-179">If the requirement calls for completely different paths, set the routes as described previously and render the `RemoteAuthenticatorView` with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="ef15e-180">Puede descomponer la interfaz de usuario en diferentes páginas si decide hacerlo.</span><span class="sxs-lookup"><span data-stu-id="ef15e-180">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="ef15e-181">Personalización de la interfaz de usuario de autenticación</span><span class="sxs-lookup"><span data-stu-id="ef15e-181">Customize the authentication user interface</span></span>

<span data-ttu-id="ef15e-182">`RemoteAuthenticatorView`incluye un conjunto predeterminado de partes de la interfaz de usuario para cada estado de autenticación.</span><span class="sxs-lookup"><span data-stu-id="ef15e-182">`RemoteAuthenticatorView` includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="ef15e-183">Cada Estado se puede personalizar pasando un personalizado `RenderFragment`.</span><span class="sxs-lookup"><span data-stu-id="ef15e-183">Each state can be customized by passing in a custom `RenderFragment`.</span></span> <span data-ttu-id="ef15e-184">Para personalizar el texto mostrado durante el proceso de inicio de sesión inicial, `RemoteAuthenticatorView` puede cambiar el de la manera siguiente.</span><span class="sxs-lookup"><span data-stu-id="ef15e-184">To customize the displayed text during the initial login process, can change the `RemoteAuthenticatorView` as follows.</span></span>

<span data-ttu-id="ef15e-185">`Authentication`componente (*páginas/autenticación. Razor*):</span><span class="sxs-lookup"><span data-stu-id="ef15e-185">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

<span data-ttu-id="ef15e-186">`RemoteAuthenticatorView` Tiene un fragmento que se puede utilizar por cada ruta de autenticación que se muestra en la tabla siguiente.</span><span class="sxs-lookup"><span data-stu-id="ef15e-186">The `RemoteAuthenticatorView` has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="ef15e-187">Enrutar</span><span class="sxs-lookup"><span data-stu-id="ef15e-187">Route</span></span>                            | <span data-ttu-id="ef15e-188">Fragmento</span><span class="sxs-lookup"><span data-stu-id="ef15e-188">Fragment</span></span>                |
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

## <a name="customize-the-user"></a><span data-ttu-id="ef15e-189">Personalización del usuario</span><span class="sxs-lookup"><span data-stu-id="ef15e-189">Customize the user</span></span>

<span data-ttu-id="ef15e-190">Los usuarios enlazados a la aplicación se pueden personalizar.</span><span class="sxs-lookup"><span data-stu-id="ef15e-190">Users bound to the app can be customized.</span></span> <span data-ttu-id="ef15e-191">En el ejemplo siguiente, todos los usuarios autenticados reciben `amr` una demanda por cada uno de los métodos de autenticación del usuario.</span><span class="sxs-lookup"><span data-stu-id="ef15e-191">In the following example, all authenticated users receive an `amr` claim for each of the user's authentication methods.</span></span>

<span data-ttu-id="ef15e-192">Cree una clase que extienda la `RemoteUserAccount` clase:</span><span class="sxs-lookup"><span data-stu-id="ef15e-192">Create a class that extends the `RemoteUserAccount` class:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class OidcAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

<span data-ttu-id="ef15e-193">Cree una fábrica que extienda `AccountClaimsPrincipalFactory<TAccount>`:</span><span class="sxs-lookup"><span data-stu-id="ef15e-193">Create a factory that extends `AccountClaimsPrincipalFactory<TAccount>`:</span></span>

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;

public class CustomAccountFactory 
    : AccountClaimsPrincipalFactory<OidcAccount>
{
    public CustomAccountFactory(NavigationManager navigationManager, 
        IAccessTokenProviderAccessor accessor) : base(accessor)
    {
    }
  
    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        OidcAccount account, RemoteAuthenticationUserOptions options)
    {
        var initialUser = await base.CreateUserAsync(account, options);
        
        if (initialUser.Identity.IsAuthenticated)
        {
            foreach (var value in account.AuthenticationMethod)
            {
                ((ClaimsIdentity)initialUser.Identity)
                    .AddClaim(new Claim("amr", value));
            }
        }
           
        return initialUser;
    }
}
```

<span data-ttu-id="ef15e-194">Registrar servicios para usar `CustomAccountFactory`:</span><span class="sxs-lookup"><span data-stu-id="ef15e-194">Register services to use the `CustomAccountFactory`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddApiAuthorization<RemoteAuthenticationState, OidcAccount>()
    .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, OidcAccount, 
        CustomAccountFactory>();
```

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="ef15e-195">Compatibilidad de la representación previa con la autenticación</span><span class="sxs-lookup"><span data-stu-id="ef15e-195">Support prerendering with authentication</span></span>

<span data-ttu-id="ef15e-196">Después de seguir las instrucciones que aparecen en uno de los temas de la aplicación WebAssembly de Blazor hospedada, use estas instrucciones para crear una aplicación que:</span><span class="sxs-lookup"><span data-stu-id="ef15e-196">After following the guidance in one of the hosted Blazor WebAssembly app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="ef15e-197">Representa previamente las rutas de acceso para las que no se requiere autorización.</span><span class="sxs-lookup"><span data-stu-id="ef15e-197">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="ef15e-198">No representa previamente las rutas de acceso para las que se requiere autorización.</span><span class="sxs-lookup"><span data-stu-id="ef15e-198">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="ef15e-199">En la clase `Program` de la aplicación cliente (*Program.cs*), factorice los registros de servicio comunes en un método independiente (por ejemplo, `ConfigureCommonServices`):</span><span class="sxs-lookup"><span data-stu-id="ef15e-199">In the Client app's `Program` class (*Program.cs*), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.RootComponents.Add<App>("app");

        builder.Services.AddSingleton(new HttpClient 
        {
            BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
        });

        services.Add...;

        ConfigureCommonServices(builder.Services);

        await builder.Build().RunAsync();
    }

    public static void ConfigureCommonServices(IServiceCollection services)
    {
        // Common service registrations
    }
}
```

<span data-ttu-id="ef15e-200">En `Startup.ConfigureServices` de la aplicación de servidor, registre estos servicios adicionales:</span><span class="sxs-lookup"><span data-stu-id="ef15e-200">In the Server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Server;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddRazorPages();
    services.AddScoped<AuthenticationStateProvider, 
        ServerAuthenticationStateProvider>();
    services.AddScoped<SignOutSessionStateManager>();

    Client.Program.ConfigureCommonServices(services);
}
```

<span data-ttu-id="ef15e-201">En el método `Startup.Configure` de la aplicación de servidor, reemplace `endpoints.MapFallbackToFile("index.html")` por `endpoints.MapFallbackToPage("/_Host")`:</span><span class="sxs-lookup"><span data-stu-id="ef15e-201">In the Server app's `Startup.Configure` method, replace `endpoints.MapFallbackToFile("index.html")` with `endpoints.MapFallbackToPage("/_Host")`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="ef15e-202">En la aplicación de servidor, cree una carpeta *Pages* si todavía no existe.</span><span class="sxs-lookup"><span data-stu-id="ef15e-202">In the Server app, create a *Pages* folder if it doesn't exist.</span></span> <span data-ttu-id="ef15e-203">Cree una página *_Host.cshtml* dentro de la carpeta *Pages* de la aplicación de servidor.</span><span class="sxs-lookup"><span data-stu-id="ef15e-203">Create a *_Host.cshtml* page inside the Server app's *Pages* folder.</span></span> <span data-ttu-id="ef15e-204">Pegue el contenido del archivo *wwwroot/index.html* de la aplicación cliente en el archivo *Pages/_Host.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="ef15e-204">Paste the contents from the Client app's *wwwroot/index.html* file into the *Pages/_Host.cshtml* file.</span></span> <span data-ttu-id="ef15e-205">Actualice el contenido del archivo:</span><span class="sxs-lookup"><span data-stu-id="ef15e-205">Update the file's contents:</span></span>

* <span data-ttu-id="ef15e-206">Agregue `@page "_Host"` a la parte superior del archivo.</span><span class="sxs-lookup"><span data-stu-id="ef15e-206">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="ef15e-207">Reemplace la etiqueta `<app>Loading...</app>` por la siguiente:</span><span class="sxs-lookup"><span data-stu-id="ef15e-207">Replace the `<app>Loading...</app>` tag with the following:</span></span>

  ```cshtml
  <app>
      @if (!HttpContext.Request.Path.StartsWithSegments("/authentication"))
      {
          <component type="typeof(Wasm.Authentication.Client.App)" 
              render-mode="Static" />
      }
      else
      {
          <text>Loading...</text>
      }
  </app>
  ```
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="ef15e-208">Opciones para aplicaciones hospedadas y proveedores de inicio de sesión de terceros</span><span class="sxs-lookup"><span data-stu-id="ef15e-208">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="ef15e-209">Al autenticar y autorizar una aplicación WebAssembly de Blazor con un proveedor de terceros, hay varias opciones disponibles para autenticar al usuario.</span><span class="sxs-lookup"><span data-stu-id="ef15e-209">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="ef15e-210">Su elección depende del escenario.</span><span class="sxs-lookup"><span data-stu-id="ef15e-210">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="ef15e-211">Para obtener más información, vea <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="ef15e-211">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="ef15e-212">Autenticación de usuarios solo para llamadas a las API de terceros protegidas</span><span class="sxs-lookup"><span data-stu-id="ef15e-212">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="ef15e-213">Autentique al usuario con un flujo de OAuth del lado cliente en el proveedor de la API de terceros:</span><span class="sxs-lookup"><span data-stu-id="ef15e-213">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="ef15e-214">En este escenario:</span><span class="sxs-lookup"><span data-stu-id="ef15e-214">In this scenario:</span></span>

* <span data-ttu-id="ef15e-215">El servidor que hospeda la aplicación no desempeña ningún rol.</span><span class="sxs-lookup"><span data-stu-id="ef15e-215">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="ef15e-216">No se pueden proteger las API en el servidor.</span><span class="sxs-lookup"><span data-stu-id="ef15e-216">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="ef15e-217">La aplicación solo puede llamar a las API de terceros protegidas.</span><span class="sxs-lookup"><span data-stu-id="ef15e-217">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="ef15e-218">Autenticación de usuarios con un proveedor de terceros y llamada a las API protegidas en el servidor host y en el tercero</span><span class="sxs-lookup"><span data-stu-id="ef15e-218">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="ef15e-219">Configure Identity con un proveedor de inicio de sesión de terceros.</span><span class="sxs-lookup"><span data-stu-id="ef15e-219">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="ef15e-220">Obtenga los tokens necesarios para el acceso de API de terceros y almacénelos.</span><span class="sxs-lookup"><span data-stu-id="ef15e-220">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="ef15e-221">Cuando un usuario inicia sesión, Identity recopila los tokens de acceso y actualización como parte del proceso de autenticación.</span><span class="sxs-lookup"><span data-stu-id="ef15e-221">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="ef15e-222">En ese momento, hay un par de enfoques disponibles para realizar llamadas API a las API de terceros.</span><span class="sxs-lookup"><span data-stu-id="ef15e-222">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="ef15e-223">Uso de un token de acceso de servidor para recuperar el token de acceso de terceros</span><span class="sxs-lookup"><span data-stu-id="ef15e-223">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="ef15e-224">Use el token de acceso generado en el servidor para recuperar el token de acceso de terceros de un punto de conexión de la API de servidor.</span><span class="sxs-lookup"><span data-stu-id="ef15e-224">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="ef15e-225">A partir de ahí, utilice el token de acceso de terceros para llamar directamente a los recursos de la API de terceros desde Identity en el cliente.</span><span class="sxs-lookup"><span data-stu-id="ef15e-225">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="ef15e-226">Este enfoque no se recomienda.</span><span class="sxs-lookup"><span data-stu-id="ef15e-226">We don't recommend this approach.</span></span> <span data-ttu-id="ef15e-227">Este enfoque requiere tratar el token de acceso de terceros como si se hubiera generado para un cliente público.</span><span class="sxs-lookup"><span data-stu-id="ef15e-227">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="ef15e-228">En términos de OAuth, la aplicación pública no tiene un secreto de cliente porque no es de confianza para almacenar secretos de forma segura y el token de acceso se genera para un cliente confidencial.</span><span class="sxs-lookup"><span data-stu-id="ef15e-228">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="ef15e-229">Un cliente confidencial es un cliente que tiene un secreto de cliente y se supone que puede almacenar secretos de forma segura.</span><span class="sxs-lookup"><span data-stu-id="ef15e-229">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="ef15e-230">El token de acceso de terceros podría recibir ámbitos adicionales para realizar operaciones confidenciales en función del hecho de que la tercera parte emitió el token para un cliente de más confianza.</span><span class="sxs-lookup"><span data-stu-id="ef15e-230">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="ef15e-231">Del mismo modo, los tokens de actualización no deben emitirse para un cliente que no sea de confianza, ya que esto proporciona acceso ilimitado al cliente a menos que se apliquen otras restricciones.</span><span class="sxs-lookup"><span data-stu-id="ef15e-231">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="ef15e-232">Realización de llamadas API desde el cliente a la API de servidor para llamar a las API de terceros</span><span class="sxs-lookup"><span data-stu-id="ef15e-232">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="ef15e-233">Realice una llamada API desde el cliente a la API del servidor.</span><span class="sxs-lookup"><span data-stu-id="ef15e-233">Make an API call from the client to the server API.</span></span> <span data-ttu-id="ef15e-234">En el servidor, recupere el token de acceso para el recurso de la API de terceros y emita cualquier llamada que sea necesaria.</span><span class="sxs-lookup"><span data-stu-id="ef15e-234">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="ef15e-235">Aunque este enfoque requiere un salto de red adicional a través del servidor para llamar a una API de terceros, en última instancia resulta una experiencia más segura:</span><span class="sxs-lookup"><span data-stu-id="ef15e-235">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="ef15e-236">El servidor puede almacenar tokens de actualización y asegurarse de que la aplicación no pierde el acceso a recursos de terceros.</span><span class="sxs-lookup"><span data-stu-id="ef15e-236">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="ef15e-237">La aplicación no puede filtrar los tokens de acceso del servidor que puedan contener permisos más confidenciales.</span><span class="sxs-lookup"><span data-stu-id="ef15e-237">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>
