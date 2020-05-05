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
# <a name="aspnet-core-blazor-server-additional-security-scenarios"></a>Escenarios Blazor de seguridad adicionales de ASP.net Core Server

Por [Javier Calvarro Nelson](https://github.com/javiercn)

## <a name="pass-tokens-to-a-blazor-server-app"></a>Pasar tokens a una Blazor aplicación de servidor

Los tokens disponibles fuera de Razor los componentes de Blazor una aplicación de servidor se pueden pasar a los componentes con el enfoque descrito en esta sección. Para obtener código de ejemplo, incluido `Startup.ConfigureServices` un ejemplo completo, vea [pasar tokens a una aplicación del Blazor lado servidor](https://github.com/javiercn/blazor-server-aad-sample).

Autentique la aplicación Blazor de servidor como lo haría con las páginas Razor normales o la aplicación MVC. Aprovisione y guarde los tokens en la cookie de autenticación. Por ejemplo:

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

Defina una clase para pasar el estado de la aplicación inicial con los tokens de acceso y actualización:

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

Defina un servicio de proveedor de tokens de **ámbito** que se pueda Blazor usar dentro de la aplicación para resolver los tokens de la [inserción de dependencias (di)](xref:blazor/dependency-injection):

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

En `Startup.ConfigureServices`, agregue servicios para:

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

En el archivo *_Host. cshtml* , cree una instancia de `InitialApplicationState` y pásela como un parámetro a la aplicación:

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

En el `App` componente (*app. Razor*), resuelva el servicio e Inicialícelo con los datos del parámetro:

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

En el servicio que realiza una solicitud de API segura, inserte el proveedor de tokens y recupere el token para llamar a la API:

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
