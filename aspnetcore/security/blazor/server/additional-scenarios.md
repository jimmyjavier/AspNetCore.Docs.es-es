---
title: Escenarios Blazor de seguridad adicionales de ASP.net Core Server
author: guardrex
description: Obtenga información acerca de Blazor cómo configurar el servidor para escenarios de seguridad adicionales.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/27/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/server/additional-scenarios
ms.openlocfilehash: 95e9e57889fdbb5270f895874c9b8148ae4ca48d
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2020
ms.locfileid: "82772809"
---
# <a name="aspnet-core-blazor-server-additional-security-scenarios"></a><span data-ttu-id="4cb79-103">Escenarios Blazor de seguridad adicionales de ASP.net Core Server</span><span class="sxs-lookup"><span data-stu-id="4cb79-103">ASP.NET Core Blazor Server additional security scenarios</span></span>

<span data-ttu-id="4cb79-104">Por [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="4cb79-104">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

## <a name="pass-tokens-to-a-blazor-server-app"></a><span data-ttu-id="4cb79-105">Pasar tokens a una Blazor aplicación de servidor</span><span class="sxs-lookup"><span data-stu-id="4cb79-105">Pass tokens to a Blazor Server app</span></span>

<span data-ttu-id="4cb79-106">Los tokens disponibles fuera de Razor los componentes de Blazor una aplicación de servidor se pueden pasar a los componentes con el enfoque descrito en esta sección.</span><span class="sxs-lookup"><span data-stu-id="4cb79-106">Tokens available outside of the Razor components in a Blazor Server app can be passed to components with the approach described in this section.</span></span> <span data-ttu-id="4cb79-107">Para obtener código de ejemplo, incluido `Startup.ConfigureServices` un ejemplo completo, vea [pasar tokens a una aplicación del Blazor lado servidor](https://github.com/javiercn/blazor-server-aad-sample).</span><span class="sxs-lookup"><span data-stu-id="4cb79-107">For sample code, including a complete `Startup.ConfigureServices` example, see the [Passing tokens to a server-side Blazor application](https://github.com/javiercn/blazor-server-aad-sample).</span></span>

<span data-ttu-id="4cb79-108">Autentique la aplicación Blazor de servidor como lo haría con las páginas Razor normales o la aplicación MVC.</span><span class="sxs-lookup"><span data-stu-id="4cb79-108">Authenticate the Blazor Server app as you would with a regular Razor Pages or MVC app.</span></span> <span data-ttu-id="4cb79-109">Aprovisione y guarde los tokens en la cookie de autenticación.</span><span class="sxs-lookup"><span data-stu-id="4cb79-109">Provision and save the tokens to the authentication cookie.</span></span> <span data-ttu-id="4cb79-110">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="4cb79-110">For example:</span></span>

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

<span data-ttu-id="4cb79-111">Defina una clase para pasar el estado de la aplicación inicial con los tokens de acceso y actualización:</span><span class="sxs-lookup"><span data-stu-id="4cb79-111">Define a class to pass in the initial app state with the access and refresh tokens:</span></span>

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="4cb79-112">Defina un servicio de proveedor de tokens de **ámbito** que se pueda Blazor usar dentro de la aplicación para resolver los tokens de la [inserción de dependencias (di)](xref:blazor/dependency-injection):</span><span class="sxs-lookup"><span data-stu-id="4cb79-112">Define a **scoped** token provider service that can be used within the Blazor app to resolve the tokens from [dependency injection (DI)](xref:blazor/dependency-injection):</span></span>

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="4cb79-113">En `Startup.ConfigureServices`, agregue servicios para:</span><span class="sxs-lookup"><span data-stu-id="4cb79-113">In `Startup.ConfigureServices`, add services for:</span></span>

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

<span data-ttu-id="4cb79-114">En el archivo *_Host. cshtml* , cree una instancia de `InitialApplicationState` y pásela como un parámetro a la aplicación:</span><span class="sxs-lookup"><span data-stu-id="4cb79-114">In the *_Host.cshtml* file, create and instance of `InitialApplicationState` and pass it as a parameter to the app:</span></span>

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

<span data-ttu-id="4cb79-115">En el `App` componente (*app. Razor*), resuelva el servicio e Inicialícelo con los datos del parámetro:</span><span class="sxs-lookup"><span data-stu-id="4cb79-115">In the `App` component (*App.razor*), resolve the service and initialize it with the data from the parameter:</span></span>

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

<span data-ttu-id="4cb79-116">En el servicio que realiza una solicitud de API segura, inserte el proveedor de tokens y recupere el token para llamar a la API:</span><span class="sxs-lookup"><span data-stu-id="4cb79-116">In the service that makes a secure API request, inject the token provider and retrieve the token to call the API:</span></span>

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
