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
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a>ASP.NET Core Blazor WebAssembly escenarios de seguridad adicionales

Por [Javier Calvarro Nelson](https://github.com/javiercn)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

## <a name="request-additional-access-tokens"></a>Solicitar tokens de acceso adicionales

La mayoría de las aplicaciones solo requieren un token de acceso para interactuar con los recursos protegidos que usan. En algunos escenarios, una aplicación puede requerir más de un token para interactuar con dos o más recursos.

En el ejemplo siguiente, una aplicación requiere ámbitos adicionales de Microsoft Graph API de Azure Active Directory (AAD) para leer los datos de usuario y enviar correo. Después de agregar los permisos de Microsoft Graph API en el portal`Program.Main`de Azure AAD, los ámbitos adicionales se configuran en la aplicación cliente ( , *Program.cs*):

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

El `IAccessTokenProvider.RequestToken` método proporciona una sobrecarga que permite a una aplicación aprovisionar un token con un conjunto determinado de ámbitos, como se muestra en el ejemplo siguiente:

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

`TryGetToken`Devuelve:

* `true`con `token` el uso.
* `false`si no se recupera el token.

## <a name="handle-token-request-errors"></a>Controlar errores de solicitud de token

Cuando una aplicación de una sola página (SPA) autentica a un usuario usando Open ID Connect (OIDC), el estado de autenticación se mantiene localmente dentro del SPA y en el proveedor de identidad (IP) en forma de una cookie de sesión que se fija como resultado del usuario que proporciona sus credenciales.

Los tokens que la ip emite para el usuario suelen ser válidos durante períodos cortos de tiempo, aproximadamente una hora normalmente, por lo que la aplicación cliente debe capturar regularmente nuevos tokens. De lo contrario, el usuario cerraría la sesión después de que expiren los tokens concedidos. En la mayoría de los casos, los clientes OIDC pueden aprovisionar nuevos tokens sin necesidad de que el usuario se autentique de nuevo gracias al estado de autenticación o "sesión" que se mantiene dentro de la IP.

Hay algunos casos en los que el cliente no puede obtener un token sin la interacción del usuario, por ejemplo, cuando por alguna razón el usuario cierra la sesión explícitamente de la ip. Este escenario se produce `https://login.microsoftonline.com` si un usuario visita y cierra sesión. En estos escenarios, la aplicación no sabe inmediatamente que el usuario ha cerrado la sesión. Es posible que cualquier token que el cliente tenga ya no sea válido. Además, el cliente no puede aprovisionar un nuevo token sin la interacción del usuario después de que expire el token actual.

Estos escenarios no son específicos de la autenticación basada en token. Son parte de la naturaleza de las ZAP. Un SPA que utiliza cookies tampoco puede llamar a una API de servidor si se elimina la cookie de autenticación.

Cuando una aplicación realiza llamadas a la API a recursos protegidos, debe tener en cuenta lo siguiente:

* Para aprovisionar un nuevo token de acceso para llamar a la API, es posible que el usuario tenga que autenticarse de nuevo.
* Incluso si el cliente tiene un token que parece ser válido, la llamada al servidor puede producir un error porque el usuario revocó el token.

Cuando la aplicación solicita un token, hay dos resultados posibles:

* La solicitud se realiza correctamente y la aplicación tiene un token válido.
* Se produce un error en la solicitud y la aplicación debe autenticar al usuario de nuevo para obtener un nuevo token.

Cuando se produce un error en una solicitud de token, debe decidir si desea guardar cualquier estado actual antes de realizar una redirección. Existen varios enfoques con niveles crecientes de complejidad:

* Almacene el estado actual de la página en el almacenamiento de sesión. Durante `OnInitializeAsync`, compruebe si el estado se puede restaurar antes de continuar.
* Agregue un parámetro de cadena de consulta y úselo como una forma de indicar a la aplicación que necesita volver a hidratar el estado guardado anteriormente.
* Agregue un parámetro de cadena de consulta con un identificador único para almacenar datos en el almacenamiento de sesión sin correr el riesgo de colisiones con otros elementos.

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

Durante una operación de autenticación, hay casos en los que desea guardar el estado de la aplicación antes de redirigir el explorador a la dirección IP. Este puede ser el caso cuando se usa algo como un contenedor de estado y desea restaurar el estado después de que la autenticación se realice correctamente. Puede usar un objeto de estado de autenticación personalizado para conservar el estado específico de la aplicación o una referencia a él y restaurar ese estado una vez que la operación de autenticación se complete correctamente.

`Authentication`componente (*Pages/Authentication.razor*):

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

## <a name="customize-app-routes"></a>Personalizar rutas de aplicaciones

De forma `Microsoft.AspNetCore.Components.WebAssembly.Authentication` predeterminada, la biblioteca utiliza las rutas que se muestran en la tabla siguiente para representar diferentes estados de autenticación.

| Enrutar                            | Propósito |
| -------------------------------- | ------- |
| `authentication/login`           | Desencadena una operación de inicio de sesión. |
| `authentication/login-callback`  | Controla el resultado de cualquier operación de inicio de sesión. |
| `authentication/login-failed`    | Muestra mensajes de error cuando se produce un error en la operación de inicio de sesión por algún motivo. |
| `authentication/logout`          | Activa una operación de cierre de sesión. |
| `authentication/logout-callback` | Controla el resultado de una operación de cierre de sesión. |
| `authentication/logout-failed`   | Muestra mensajes de error cuando se produce un error en la operación de cierre de sesión por algún motivo. |
| `authentication/logged-out`      | Indica que el usuario ha cierre la sesión correctamente. |
| `authentication/profile`         | Activa una operación para editar el perfil de usuario. |
| `authentication/register`        | Desencadena una operación para registrar un nuevo usuario. |

Las rutas mostradas en la tabla `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`anterior son configurables a través de . Al establecer opciones para proporcionar rutas personalizadas, confirme que la aplicación tiene una ruta que controla cada ruta de acceso.

En el ejemplo siguiente, todas las `/security`rutas de acceso tienen el prefijo .

`Authentication`componente (*Pages/Authentication.razor*):

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

`Program.Main`(*Program.cs*):

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

Si el requisito requiere rutas completamente diferentes, establezca las `RemoteAuthenticatorView` rutas como se describió anteriormente y represente el parámetro de acción explícita:

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

Puede dividir la interfaz de usuario en páginas diferentes si decide hacerlo.

## <a name="customize-the-authentication-user-interface"></a>Personalizar la interfaz de usuario de autenticación

`RemoteAuthenticatorView`incluye un conjunto predeterminado de piezas de interfaz de usuario para cada estado de autenticación. Cada estado se puede personalizar `RenderFragment`pasando un archivo . Para personalizar el texto mostrado durante el proceso `RemoteAuthenticatorView` de inicio de sesión inicial, puede cambiar lo siguiente.

`Authentication`componente (*Pages/Authentication.razor*):

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

Tiene `RemoteAuthenticatorView` un fragmento que se puede utilizar por ruta de autenticación que se muestra en la tabla siguiente.

| Enrutar                            | Fragmento                |
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
