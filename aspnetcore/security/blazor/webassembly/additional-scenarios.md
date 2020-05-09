---
title: ASP.NET Core Blazor escenarios de seguridad adicionales de webassembly
author: guardrex
description: Aprenda a configurar Blazor webassembly para escenarios de seguridad adicionales.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/08/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/additional-scenarios
ms.openlocfilehash: e8a088b3f1a4e0eb7d5d1c5c09ef53c4a2bd3628
ms.sourcegitcommit: 363e3a2a035f4082cb92e7b75ed150ba304258b3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/08/2020
ms.locfileid: "82976797"
---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a>ASP.NET Core más escenarios de seguridad adicionales de webassembly

Por [Javier Calvarro Nelson](https://github.com/javiercn)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

## <a name="attach-tokens-to-outgoing-requests"></a>Adjuntar tokens a solicitudes salientes

El `AuthorizationMessageHandler` servicio se puede usar con `HttpClient` para adjuntar tokens de acceso a las solicitudes salientes. Los tokens se adquieren `IAccessTokenProvider` con el servicio existente. Si no se puede adquirir un token, `AccessTokenNotAvailableException` se produce una excepción. `AccessTokenNotAvailableException`tiene un `Redirect` método que se puede utilizar para navegar por el usuario al proveedor de identidades para adquirir un nuevo token. Se `AuthorizationMessageHandler` puede configurar con las direcciones URL autorizadas, los ámbitos y la dirección URL de `ConfigureHandler` retorno mediante el método.

En el ejemplo siguiente, `AuthorizationMessageHandler` configura `HttpClient` en `Program.Main` (*Program.CS*):

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddTransient(sp =>
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

Para mayor comodidad, `BaseAddressAuthorizationMessageHandler` se incluye una que está preconfigurada con la dirección base de la aplicación como una dirección URL autorizada. Las plantillas de webassembly increíblemente habilitadas para la <xref:System.Net.Http.IHttpClientFactory> autenticación ahora se usan en el proyecto de API <xref:System.Net.Http.HttpClient> de servidor `BaseAddressAuthorizationMessageHandler`para configurar una con:

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient("BlazorWithIdentityApp1.ServerAPI", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
        .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("BlazorWithIdentityApp1.ServerAPI"));
```

Cuando el cliente se crea con `CreateClient` en el ejemplo anterior, <xref:System.Net.Http.HttpClient> se proporcionan instancias de que incluyen tokens de acceso al hacer solicitudes al proyecto de servidor.

A continuación <xref:System.Net.Http.HttpClient> , se usa el configurado para hacer solicitudes autorizadas `try-catch` mediante un patrón simple. El siguiente `FetchData` componente solicita datos de previsión meteorológica:

```csharp
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject HttpClient Http

...

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

Como alternativa, puede definir un cliente con tipo que controle todos los problemas de adquisición de tokens y HTTP en una sola clase:

*WeatherClient.CS*:

```csharp
using System.Collections.Generic;
using System.Net.Http;
using System.Net.Http.Json;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using static {APP ASSEMBLY}.Data;

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

            ...
        }
        catch (AccessTokenNotAvailableException exception)
        {
            exception.Redirect();
        }

        return forecasts;
    }
}
```

*Program.cs*:

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherClient>(
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

*FetchData. Razor*:

```razor
@inject WeatherClient WeatherClient

...

protected override async Task OnInitializedAsync()
{
    forecasts = await WeatherClient.GetWeatherForeacasts();
}
```

## <a name="request-additional-access-tokens"></a>Solicitar tokens de acceso adicionales

Los tokens de acceso se pueden obtener manualmente `IAccessTokenProvider.RequestAccessToken`mediante una llamada a.

En el siguiente ejemplo, una aplicación necesita los ámbitos de API de Azure Active Directory adicionales (AAD) Microsoft Graph para leer los datos de usuario y enviar correo. Después de agregar los permisos de Microsoft Graph API en el portal de Azure AAD, los ámbitos adicionales se configuran en`Program.Main`la aplicación cliente (, *Program.CS*):

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

El `IAccessTokenProvider.RequestToken` método proporciona una sobrecarga que permite a una aplicación aprovisionar un token de acceso con un conjunto determinado de ámbitos, tal como se muestra en el ejemplo siguiente:

```csharp
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject IAccessTokenProvider TokenProvider

...

var tokenResult = await TokenProvider.RequestAccessToken(
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

`TryGetToken`Devuelve

* `true`con para `token` su uso.
* `false`Si el token no se recupera.

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a>HttpClient y HttpRequestMessage con las opciones de solicitud de Fetch API

Cuando se ejecuta en webassembly en una aplicación de webassembly increíblemente <xref:System.Net.Http.HttpRequestMessage> brillante, [HttpClient](xref:fundamentals/http-requests) y se puede usar para personalizar las solicitudes. Por ejemplo, puede especificar el método HTTP y los encabezados de solicitud. En el ejemplo siguiente se `POST` realiza una solicitud a un punto de conexión de API de lista de tareas pendientes en el servidor y se muestra el cuerpo de la respuesta:

```razor
@page "/todorequest"
@using System.Net.Http
@using System.Net.Http.Headers
@using System.Net.Http.Json
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject HttpClient Http
@inject IAccessTokenProvider TokenProvider

<h1>ToDo Request</h1>

<button @onclick="PostRequest">Submit POST request</button>

<p>Response body returned by the server:</p>

<p>@_responseBody</p>

@code {
    private string _responseBody;

    private async Task PostRequest()
    {
        var requestMessage = new HttpRequestMessage()
        {
            Method = new HttpMethod("POST"),
            RequestUri = new Uri("https://localhost:10000/api/TodoItems"),
            Content =
                JsonContent.Create(new TodoItem
                {
                    Name = "My New Todo Item",
                    IsComplete = false
                })
        };

        var tokenResult = await TokenProvider.RequestAccessToken();

        if (tokenResult.TryGetToken(out var token))
        {
            requestMessage.Headers.Authorization =
                new AuthenticationHeaderValue("Bearer", token.Value);

            requestMessage.Content.Headers.TryAddWithoutValidation(
                "x-custom-header", "value");

            var response = await Http.SendAsync(requestMessage);
            var responseStatusCode = response.StatusCode;

            _responseBody = await response.Content.ReadAsStringAsync();
        }
    }

    public class TodoItem
    {
        public long Id { get; set; }
        public string Name { get; set; }
        public bool IsComplete { get; set; }
    }
}
```

En la implementación de `HttpClient` de WebAssembly de .NET se usa [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch). Fetch permite configurar varias [opciones específicas de la solicitud](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters). 

Se pueden configurar opciones de solicitud de captura HTTP con los métodos de extensión `HttpRequestMessage` que se muestran en la siguiente tabla.

| Método de extensión `HttpRequestMessage` | Propiedad de solicitud de captura |
| ------------------------------------- | ---------------------- |
| `SetBrowserRequestCredentials`        | [credentials](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| `SetBrowserRequestCache`              | [cache](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| `SetBrowserRequestMode`               | [mode](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| `SetBrowserRequestIntegrity`          | [integrity](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

Se pueden establecer más opciones usando el método de extensión `SetBrowserRequestOption`, que es más genérico.
 
Normalmente, la respuesta HTTP se almacena en búfer en una aplicación webassembly increíblemente ligera para habilitar la compatibilidad con las lecturas de sincronización en el contenido de la respuesta. Para habilitar la compatibilidad con la transmisión de respuestas, use el método de extensión `SetBrowserResponseStreamingEnabled` en la solicitud.

Para incluir credenciales en una solicitud entre orígenes, use el método de extensión `SetBrowserRequestCredentials`:

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

Para obtener más información sobre las opciones de Fetch API, vea [Documentación web de MDN: WindowOrWorkerGlobalScope.fetch(): parámetros](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).

Al enviar credenciales (cookies de autorización o encabezados) en solicitudes de CORS, la directiva de CORS debe permitir el encabezado `Authorization`.

En la directiva siguiente se incluye la configuración de:

* Orígenes de la solicitud (`http://localhost:5000`, `https://localhost:5001`).
* Cualquier método (verbo).
* Los encabezados `Content-Type` y `Authorization`. Para permitir un encabezado personalizado (por ejemplo, `x-custom-header`), enumere el encabezado al llamar a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.
* Las credenciales establecidas por el código JavaScript del lado cliente (la propiedad `credentials` establecida en `include`).

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

Para obtener más información, vea <xref:security/cors> y el componente del evaluador de solicitudes HTTP de la aplicación de ejemplo (*Components/HTTPRequestTester.razor*).

## <a name="handle-token-request-errors"></a>Controlar errores de solicitudes de token

Cuando una aplicación de una sola página (SPA) autentica a un usuario mediante Open ID Connect (OIDC), el estado de autenticación se mantiene localmente en el SPA y en el proveedor de identidades (IP) en forma de una cookie de sesión que se establece como resultado del usuario que proporciona sus credenciales.

Normalmente, los tokens que emite la IP para el usuario son válidos durante breves períodos de tiempo, aproximadamente una hora, por lo que la aplicación cliente debe capturar nuevos tokens con regularidad. De lo contrario, se cerrará la sesión del usuario después de que expiren los tokens concedidos. En la mayoría de los casos, los clientes de OIDC pueden aprovisionar nuevos tokens sin necesidad de que el usuario se autentique de nuevo gracias al estado de autenticación o a la "sesión" que se mantiene dentro de la dirección IP.

Hay algunos casos en los que el cliente no puede obtener un token sin la interacción del usuario, por ejemplo, cuando por alguna razón el usuario cierra la sesión explícitamente desde la dirección IP. Este escenario se produce si un usuario `https://login.microsoftonline.com` visita y cierra la sesión. En estos casos, la aplicación no sabe inmediatamente que el usuario ha cerrado la sesión. Es posible que el token que contiene el cliente ya no sea válido. Además, el cliente no puede aprovisionar un token nuevo sin la interacción del usuario después de que expire el token actual.

Estos escenarios no son específicos de la autenticación basada en tokens. Forman parte de la naturaleza de spa. Un SPA que use cookies tampoco podrá llamar a una API de servidor si se quita la cookie de autenticación.

Cuando una aplicación realiza llamadas API a recursos protegidos, debe tener en cuenta lo siguiente:

* Para aprovisionar un nuevo token de acceso para llamar a la API, es posible que sea necesario volver a autenticar al usuario.
* Incluso si el cliente tiene un token que parece ser válido, es posible que se produzca un error en la llamada al servidor porque el usuario revocó el token.

Cuando la aplicación solicita un token, hay dos resultados posibles:

* La solicitud se realiza correctamente y la aplicación tiene un token válido.
* Se produce un error en la solicitud y la aplicación debe autenticar al usuario de nuevo para obtener un nuevo token.

Cuando se produce un error en una solicitud de token, debe decidir si desea guardar el estado actual antes de realizar una redirección. Existen varios enfoques con niveles crecientes de complejidad:

* Almacenar el estado de la página actual en el almacenamiento de sesión. Durante `OnInitializeAsync`, compruebe si el estado se puede restaurar antes de continuar.
* Agregue un parámetro de cadena de consulta y úselo como una manera de señalar la aplicación que necesita para volver a hidratar el estado guardado anteriormente.
* Agregue un parámetro de cadena de consulta con un identificador único para almacenar los datos en el almacenamiento de sesión sin poner en peligro las colisiones con otros elementos.

El ejemplo siguiente muestra cómo:

* Conservar el estado antes de redirigir a la página de inicio de sesión.
* Recupere el estado anterior después de la autenticación mediante el parámetro de cadena de consulta.

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

## <a name="save-app-state-before-an-authentication-operation"></a>Guardar el estado de la aplicación antes de una operación de autenticación

Durante una operación de autenticación, hay casos en los que desea guardar el estado de la aplicación antes de que el explorador se redirija a la dirección IP. Esto puede ser así cuando se usa algo como un contenedor de estado y se desea restaurar el estado después de que la autenticación se realice correctamente. Puede usar un objeto de estado de autenticación personalizado para conservar el estado específico de la aplicación o una referencia a él y restaurar ese estado una vez que la operación de autenticación se complete correctamente.

`Authentication`componente (*páginas/autenticación. Razor*):

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

## <a name="customize-app-routes"></a>Personalización de rutas de aplicación

De forma predeterminada, `Microsoft.AspNetCore.Components.WebAssembly.Authentication` la biblioteca utiliza las rutas que se muestran en la tabla siguiente para representar diferentes Estados de autenticación.

| Ruta                            | Propósito |
| -------------------------------- | ------- |
| `authentication/login`           | Desencadena una operación de inicio de sesión. |
| `authentication/login-callback`  | Controla el resultado de cualquier operación de inicio de sesión. |
| `authentication/login-failed`    | Muestra mensajes de error cuando se produce un error en la operación de inicio de sesión por algún motivo. |
| `authentication/logout`          | Desencadena una operación de cierre de sesión. |
| `authentication/logout-callback` | Controla el resultado de una operación de cierre de sesión. |
| `authentication/logout-failed`   | Muestra mensajes de error cuando se produce un error en la operación de cierre de sesión por algún motivo. |
| `authentication/logged-out`      | Indica que el usuario ha cerrado la sesión correctamente. |
| `authentication/profile`         | Desencadena una operación para editar el perfil de usuario. |
| `authentication/register`        | Desencadena una operación para registrar un nuevo usuario. |

Las rutas que se muestran en la tabla anterior se `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`pueden configurar mediante. Al establecer las opciones para proporcionar rutas personalizadas, confirme que la aplicación tiene una ruta que controla cada ruta de acceso.

En el ejemplo siguiente, todas las rutas de acceso tienen el `/security`prefijo.

`Authentication`componente (*páginas/autenticación. Razor*):

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

`Program.Main`(*Program.CS*):

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

Si el requisito requiere rutas de acceso completamente diferentes, establezca las rutas como se describió anteriormente `RemoteAuthenticatorView` y represente el con un parámetro de acción explícito:

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

Puede descomponer la interfaz de usuario en diferentes páginas si decide hacerlo.

## <a name="customize-the-authentication-user-interface"></a>Personalización de la interfaz de usuario de autenticación

`RemoteAuthenticatorView`incluye un conjunto predeterminado de partes de la interfaz de usuario para cada estado de autenticación. Cada Estado se puede personalizar pasando un personalizado `RenderFragment`. Para personalizar el texto mostrado durante el proceso de inicio de sesión inicial, `RemoteAuthenticatorView` puede cambiar el de la manera siguiente.

`Authentication`componente (*páginas/autenticación. Razor*):

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

`RemoteAuthenticatorView` Tiene un fragmento que se puede utilizar por cada ruta de autenticación que se muestra en la tabla siguiente.

| Ruta                            | Fragmento                |
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

## <a name="customize-the-user"></a>Personalización del usuario

Los usuarios enlazados a la aplicación se pueden personalizar. En el ejemplo siguiente, todos los usuarios autenticados reciben `amr` una demanda por cada uno de los métodos de autenticación del usuario.

Cree una clase que extienda la `RemoteUserAccount` clase:

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

Cree una fábrica que extienda `AccountClaimsPrincipalFactory<TAccount>`:

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;

public class CustomAccountFactory 
    : AccountClaimsPrincipalFactory<CustomUserAccount>
{
    public CustomAccountFactory(NavigationManager navigationManager, 
        IAccessTokenProviderAccessor accessor) : base(accessor)
    {
    }
  
    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        CustomUserAccount account, RemoteAuthenticationUserOptions options)
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

Registre `CustomAccountFactory` para el proveedor de autenticación en uso. Cualquiera de los siguientes registros son válidos: 

* `AddOidcAuthentication`:

  ```csharp
  using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

  ...

  builder.Services.AddOidcAuthentication<RemoteAuthenticationState, 
      CustomUserAccount>(options =>
  {
      ...
  })
  .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, 
      CustomUserAccount, CustomAccountFactory>();
  ```

* `AddMsalAuthentication`:

  ```csharp
  using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

  ...

  builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
      CustomUserAccount>(options =>
  {
      ...
  })
  .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, 
      CustomUserAccount, CustomAccountFactory>();
  ```
  
* `AddApiAuthorization`:

  ```csharp
  using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

  ...

  builder.Services.AddApiAuthorization<RemoteAuthenticationState, 
      CustomUserAccount>(options =>
  {
      ...
  })
  .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, 
      CustomUserAccount, CustomAccountFactory>();
  ```

## <a name="support-prerendering-with-authentication"></a>Compatibilidad de la representación previa con la autenticación

Después de seguir las instrucciones que aparecen en uno de los temas de la aplicación WebAssembly de Blazor hospedada, use estas instrucciones para crear una aplicación que:

* Representa previamente las rutas de acceso para las que no se requiere autorización.
* No representa previamente las rutas de acceso para las que se requiere autorización.

En la clase `Program` de la aplicación cliente (*Program.cs*), factorice los registros de servicio comunes en un método independiente (por ejemplo, `ConfigureCommonServices`):

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.RootComponents.Add<App>("app");

        builder.Services.AddTransient(new HttpClient 
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

En `Startup.ConfigureServices` de la aplicación de servidor, registre estos servicios adicionales:

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

En el método `Startup.Configure` de la aplicación de servidor, reemplace `endpoints.MapFallbackToFile("index.html")` por `endpoints.MapFallbackToPage("/_Host")`:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

En la aplicación de servidor, cree una carpeta *Pages* si todavía no existe. Cree una página *_Host.cshtml* dentro de la carpeta *Pages* de la aplicación de servidor. Pegue el contenido del archivo *wwwroot/index.html* de la aplicación cliente en el archivo *Pages/_Host.cshtml*. Actualice el contenido del archivo:

* Agregue `@page "_Host"` a la parte superior del archivo.
* Reemplace la etiqueta `<app>Loading...</app>` por la siguiente:

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
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a>Opciones para aplicaciones hospedadas y proveedores de inicio de sesión de terceros

Al autenticar y autorizar una aplicación WebAssembly de Blazor con un proveedor de terceros, hay varias opciones disponibles para autenticar al usuario. Su elección depende del escenario.

Para obtener más información, vea <xref:security/authentication/social/additional-claims>.

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a>Autenticación de usuarios solo para llamadas a las API de terceros protegidas

Autentique al usuario con un flujo de OAuth del lado cliente en el proveedor de la API de terceros:

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 En este escenario:

* El servidor que hospeda la aplicación no desempeña ningún rol.
* No se pueden proteger las API en el servidor.
* La aplicación solo puede llamar a las API de terceros protegidas.

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a>Autenticación de usuarios con un proveedor de terceros y llamada a las API protegidas en el servidor host y en el tercero

Configure Identity con un proveedor de inicio de sesión de terceros. Obtenga los tokens necesarios para el acceso de API de terceros y almacénelos.

Cuando un usuario inicia sesión, Identity recopila tokens de acceso y actualización como parte del proceso de autenticación. En ese momento, hay un par de enfoques disponibles para realizar llamadas API a las API de terceros.

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a>Uso de un token de acceso de servidor para recuperar el token de acceso de terceros

Use el token de acceso generado en el servidor para recuperar el token de acceso de terceros de un punto de conexión de la API de servidor. Desde allí, use el token de acceso de terceros para llamar a recursos de API de terceros directamente Identity desde en el cliente.

Este enfoque no se recomienda. Este enfoque requiere tratar el token de acceso de terceros como si se hubiera generado para un cliente público. En términos de OAuth, la aplicación pública no tiene un secreto de cliente porque no es de confianza para almacenar secretos de forma segura y el token de acceso se genera para un cliente confidencial. Un cliente confidencial es un cliente que tiene un secreto de cliente y se supone que puede almacenar secretos de forma segura.

* El token de acceso de terceros podría recibir ámbitos adicionales para realizar operaciones confidenciales en función del hecho de que la tercera parte emitió el token para un cliente de más confianza.
* Del mismo modo, los tokens de actualización no deben emitirse para un cliente que no sea de confianza, ya que esto proporciona acceso ilimitado al cliente a menos que se apliquen otras restricciones.

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a>Realización de llamadas API desde el cliente a la API de servidor para llamar a las API de terceros

Realice una llamada API desde el cliente a la API del servidor. En el servidor, recupere el token de acceso para el recurso de la API de terceros y emita cualquier llamada que sea necesaria.

Aunque este enfoque requiere un salto de red adicional a través del servidor para llamar a una API de terceros, en última instancia resulta una experiencia más segura:

* El servidor puede almacenar tokens de actualización y asegurarse de que la aplicación no pierde el acceso a recursos de terceros.
* La aplicación no puede filtrar los tokens de acceso del servidor que puedan contener permisos más confidenciales.
