---
title: BlazorEscenarios de seguridad adicionales de ASP.net Core Server
author: guardrex
description: Obtenga información acerca de cómo configurar el Blazor servidor para escenarios de seguridad adicionales.
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
uid: security/blazor/server/additional-scenarios
ms.openlocfilehash: 9d26cde4d8964a8285241bb0158d8e6f8d5f8dbc
ms.sourcegitcommit: 16b3abec1ed70f9a206f0cfa7cf6404eebaf693d
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/17/2020
ms.locfileid: "83444079"
---
# <a name="aspnet-core-blazor-server-additional-security-scenarios"></a><span data-ttu-id="cb0b3-103">BlazorEscenarios de seguridad adicionales de ASP.net Core Server</span><span class="sxs-lookup"><span data-stu-id="cb0b3-103">ASP.NET Core Blazor Server additional security scenarios</span></span>

<span data-ttu-id="cb0b3-104">Por [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="cb0b3-104">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

## <a name="pass-tokens-to-a-blazor-server-app"></a><span data-ttu-id="cb0b3-105">Pasar tokens a una Blazor aplicación de servidor</span><span class="sxs-lookup"><span data-stu-id="cb0b3-105">Pass tokens to a Blazor Server app</span></span>

<span data-ttu-id="cb0b3-106">Los tokens disponibles fuera de los Razor componentes de una Blazor aplicación de servidor se pueden pasar a los componentes con el enfoque descrito en esta sección.</span><span class="sxs-lookup"><span data-stu-id="cb0b3-106">Tokens available outside of the Razor components in a Blazor Server app can be passed to components with the approach described in this section.</span></span> <span data-ttu-id="cb0b3-107">Para obtener código de ejemplo, incluido un `Startup.ConfigureServices` ejemplo completo, vea [pasar tokens a una Blazor aplicación del lado servidor](https://github.com/javiercn/blazor-server-aad-sample).</span><span class="sxs-lookup"><span data-stu-id="cb0b3-107">For sample code, including a complete `Startup.ConfigureServices` example, see the [Passing tokens to a server-side Blazor application](https://github.com/javiercn/blazor-server-aad-sample).</span></span>

<span data-ttu-id="cb0b3-108">Autentique la Blazor aplicación de servidor como lo haría con Razor las páginas normales o la aplicación MVC.</span><span class="sxs-lookup"><span data-stu-id="cb0b3-108">Authenticate the Blazor Server app as you would with a regular Razor Pages or MVC app.</span></span> <span data-ttu-id="cb0b3-109">Aprovisione y guarde los tokens en la cookie de autenticación.</span><span class="sxs-lookup"><span data-stu-id="cb0b3-109">Provision and save the tokens to the authentication cookie.</span></span> <span data-ttu-id="cb0b3-110">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="cb0b3-110">For example:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.OpenIdConnect;

...

services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
{
    options.ResponseType = "code";
    options.SaveTokens = true;

    options.Scope.Add("offline_access");
    options.Scope.Add("{SCOPE}");
    options.Resource = "{RESOURCE}";
});
```

<span data-ttu-id="cb0b3-111">Defina una clase para pasar el estado de la aplicación inicial con los tokens de acceso y actualización:</span><span class="sxs-lookup"><span data-stu-id="cb0b3-111">Define a class to pass in the initial app state with the access and refresh tokens:</span></span>

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="cb0b3-112">Defina un servicio de proveedor de tokens de **ámbito** que se pueda usar dentro de la Blazor aplicación para resolver los tokens de la [inserción de dependencias (di)](xref:blazor/dependency-injection):</span><span class="sxs-lookup"><span data-stu-id="cb0b3-112">Define a **scoped** token provider service that can be used within the Blazor app to resolve the tokens from [dependency injection (DI)](xref:blazor/dependency-injection):</span></span>

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="cb0b3-113">En `Startup.ConfigureServices` , agregue servicios para:</span><span class="sxs-lookup"><span data-stu-id="cb0b3-113">In `Startup.ConfigureServices`, add services for:</span></span>

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

<span data-ttu-id="cb0b3-114">En el archivo *_Host. cshtml* , cree una instancia de y `InitialApplicationState` pásela como un parámetro a la aplicación:</span><span class="sxs-lookup"><span data-stu-id="cb0b3-114">In the *_Host.cshtml* file, create and instance of `InitialApplicationState` and pass it as a parameter to the app:</span></span>

```cshtml
@using Microsoft.AspNetCore.Authentication

...

@{
    var tokens = new InitialApplicationState
    {
        AccessToken = await HttpContext.GetTokenAsync("access_token"),
        RefreshToken = await HttpContext.GetTokenAsync("refresh_token")
    };
}

<app>
    <component type="typeof(App)" param-InitialState="tokens" 
        render-mode="ServerPrerendered" />
</app>
```

<span data-ttu-id="cb0b3-115">En el `App` componente (*app. Razor*), resuelva el servicio e Inicialícelo con los datos del parámetro:</span><span class="sxs-lookup"><span data-stu-id="cb0b3-115">In the `App` component (*App.razor*), resolve the service and initialize it with the data from the parameter:</span></span>

```razor
@inject TokenProvider TokenProvider

...

@code {
    [Parameter]
    public InitialApplicationState InitialState { get; set; }

    protected override Task OnInitializedAsync()
    {
        TokenProvider.AccessToken = InitialState.AccessToken;
        TokenProvider.RefreshToken = InitialState.RefreshToken;

        return base.OnInitializedAsync();
    }
}
```

<span data-ttu-id="cb0b3-116">En el servicio que realiza una solicitud de API segura, inserte el proveedor de tokens y recupere el token para llamar a la API:</span><span class="sxs-lookup"><span data-stu-id="cb0b3-116">In the service that makes a secure API request, inject the token provider and retrieve the token to call the API:</span></span>

```csharp
public class WeatherForecastService
{
    private readonly TokenProvider _store;

    public WeatherForecastService(IHttpClientFactory clientFactory, 
        TokenProvider tokenProvider)
    {
        Client = clientFactory.CreateClient();
        _store = tokenProvider;
    }

    public HttpClient Client { get; }

    public async Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        var token = _store.AccessToken;
        var request = new HttpRequestMessage(HttpMethod.Get, 
            "https://localhost:5003/WeatherForecast");
        request.Headers.Add("Authorization", $"Bearer {token}");
        var response = await Client.SendAsync(request);
        response.EnsureSuccessStatusCode();

        return await response.Content.ReadAsAsync<WeatherForecast[]>();
    }
}
```

## <a name="use-open-id-connect-oidc-v20-endpoints"></a><span data-ttu-id="cb0b3-117">Usar puntos de conexión v 2.0 de Open ID Connect (OIDC)</span><span class="sxs-lookup"><span data-stu-id="cb0b3-117">Use Open ID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="cb0b3-118">La biblioteca de autenticación y Blazor las plantillas usan puntos de conexión de Open ID Connect (OIDC) v 1.0.</span><span class="sxs-lookup"><span data-stu-id="cb0b3-118">The authentication library and Blazor templates use Open ID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="cb0b3-119">Para usar un punto de conexión v 2.0, configure la <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> opción en la <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> :</span><span class="sxs-lookup"><span data-stu-id="cb0b3-119">To use a v2.0 endpoint, configure the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> option in the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>:</span></span>

```csharp
services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    }
```

<span data-ttu-id="cb0b3-120">Como alternativa, la configuración se puede realizar en el archivo de configuración de la aplicación (*appSettings. JSON*):</span><span class="sxs-lookup"><span data-stu-id="cb0b3-120">Alternatively, the setting can be made in the app settings (*appsettings.json*) file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="cb0b3-121">Si el seguimiento de un segmento a la autoridad no es apropiado para el proveedor de OIDC de la aplicación, como con proveedores que no son de AAD, establezca la `Authority` propiedad directamente.</span><span class="sxs-lookup"><span data-stu-id="cb0b3-121">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the `Authority` property directly.</span></span> <span data-ttu-id="cb0b3-122">Establezca la propiedad en <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> o en el archivo de configuración de la aplicación con la `Authority` clave.</span><span class="sxs-lookup"><span data-stu-id="cb0b3-122">Either set the property in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> or in the app settings file with the `Authority` key.</span></span>

### <a name="code-changes"></a><span data-ttu-id="cb0b3-123">Cambios de código</span><span class="sxs-lookup"><span data-stu-id="cb0b3-123">Code changes</span></span>

* <span data-ttu-id="cb0b3-124">La lista de notificaciones en el token de identificador cambia para los puntos de conexión v 2.0.</span><span class="sxs-lookup"><span data-stu-id="cb0b3-124">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="cb0b3-125">Para obtener más información, consulte [¿por qué actualizar a Microsoft Identity Platform (v 2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)</span><span class="sxs-lookup"><span data-stu-id="cb0b3-125">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)</span></span> <span data-ttu-id="cb0b3-126">en la documentación de Azure.</span><span class="sxs-lookup"><span data-stu-id="cb0b3-126">in the Azure documentation.</span></span>
* <span data-ttu-id="cb0b3-127">Dado que los recursos se especifican en los URI de ámbito de los puntos de conexión v 2.0, quite el <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Resource?displayProperty=nameWithType> valor de la propiedad en <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> :</span><span class="sxs-lookup"><span data-stu-id="cb0b3-127">Since resources are specified in scope URIs for v2.0 endpoints, remove the the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Resource?displayProperty=nameWithType> property setting in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>:</span></span>

  ```csharp
  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options => 
      {
          ...
          options.Resource = "...";    // REMOVE THIS LINE
          ...
      }
      ```

  For more information, see [Scopes, not resources](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison#scopes-not-resources) in the Azure documentation.

### App ID URI

* When using v2.0 endpoints, APIs define an *App ID URI*, which is meant to represent a unique identifier for the API.
* All scopes include the App ID URI as a prefix, and v2.0 endpoints emit access tokens with the App ID URI as the audience.
* When using V2.0 endpoints, the client ID configured in the Server API changes from the API Application ID (Client ID) to the App ID URI.

*appsettings.json*:

```json
{
  "AzureAd": {
    ...
    "ClientId": "https://{TENANT}.onmicrosoft.com/{APP NAME}"
    ...
  }
}
```

<span data-ttu-id="cb0b3-128">Puede encontrar el URI del ID. de aplicación que se usará en la descripción del registro de la aplicación del proveedor de OIDC.</span><span class="sxs-lookup"><span data-stu-id="cb0b3-128">You can find the App ID URI to use in the OIDC provider app registration description.</span></span>
