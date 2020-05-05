---
title: ASP.NET Core Blazor escenarios de seguridad adicionales de webassembly
author: guardrex
description: Aprenda a configurar Blazor webassembly para escenarios de seguridad adicionales.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/additional-scenarios
ms.openlocfilehash: e69b598431027aa540227b87dedfd091057a1af4
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2020
ms.locfileid: "82768174"
---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="8cd43-103">ASP.NET Core más escenarios de seguridad adicionales de webassembly</span><span class="sxs-lookup"><span data-stu-id="8cd43-103">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="8cd43-104">Por [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="8cd43-104">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

## <a name="attach-tokens-to-outgoing-requests"></a><span data-ttu-id="8cd43-105">Adjuntar tokens a solicitudes salientes</span><span class="sxs-lookup"><span data-stu-id="8cd43-105">Attach tokens to outgoing requests</span></span>

<span data-ttu-id="8cd43-106">El `AuthorizationMessageHandler` servicio se puede usar con `HttpClient` para adjuntar tokens de acceso a las solicitudes salientes.</span><span class="sxs-lookup"><span data-stu-id="8cd43-106">The `AuthorizationMessageHandler` service can be used with `HttpClient` to attach access tokens to outgoing requests.</span></span> <span data-ttu-id="8cd43-107">Los tokens se adquieren `IAccessTokenProvider` con el servicio existente.</span><span class="sxs-lookup"><span data-stu-id="8cd43-107">Tokens are acquired using the existing `IAccessTokenProvider` service.</span></span> <span data-ttu-id="8cd43-108">Si no se puede adquirir un token, `AccessTokenNotAvailableException` se produce una excepción.</span><span class="sxs-lookup"><span data-stu-id="8cd43-108">If a token can't be acquired, an `AccessTokenNotAvailableException` is thrown.</span></span> <span data-ttu-id="8cd43-109">`AccessTokenNotAvailableException`tiene un `Redirect` método que se puede utilizar para navegar por el usuario al proveedor de identidades para adquirir un nuevo token.</span><span class="sxs-lookup"><span data-stu-id="8cd43-109">`AccessTokenNotAvailableException` has a `Redirect` method that can be used to navigate the user to the identity provider to acquire a new token.</span></span> <span data-ttu-id="8cd43-110">Se `AuthorizationMessageHandler` puede configurar con las direcciones URL autorizadas, los ámbitos y la dirección URL de `ConfigureHandler` retorno mediante el método.</span><span class="sxs-lookup"><span data-stu-id="8cd43-110">The `AuthorizationMessageHandler` can be configured with the authorized URLs, scopes, and return URL using the `ConfigureHandler` method.</span></span>

<span data-ttu-id="8cd43-111">En el ejemplo siguiente, `AuthorizationMessageHandler` configura `HttpClient` en `Program.Main` (*Program.CS*):</span><span class="sxs-lookup"><span data-stu-id="8cd43-111">In the following example, `AuthorizationMessageHandler` configures an `HttpClient` in `Program.Main` (*Program.cs*):</span></span>

```csharp
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

<span data-ttu-id="8cd43-112">Para mayor comodidad, `BaseAddressAuthorizationMessageHandler` se incluye una que está preconfigurada con la dirección base de la aplicación como una dirección URL autorizada.</span><span class="sxs-lookup"><span data-stu-id="8cd43-112">For convenience, a `BaseAddressAuthorizationMessageHandler` is included that's preconfigured with the app base address as an authorized URL.</span></span> <span data-ttu-id="8cd43-113">Las plantillas de webassembly increíblemente habilitadas para la autenticación ahora usan [IHttpClientFactory](https://docs.microsoft.com/aspnet/core/fundamentals/http-requests) para `HttpClient` configurar un `BaseAddressAuthorizationMessageHandler`con:</span><span class="sxs-lookup"><span data-stu-id="8cd43-113">The authentication-enabled Blazor WebAssembly templates now use [IHttpClientFactory](https://docs.microsoft.com/aspnet/core/fundamentals/http-requests) to set up an `HttpClient` with the `BaseAddressAuthorizationMessageHandler`:</span></span>

```csharp
builder.Services.AddHttpClient("BlazorWithIdentityApp1.ServerAPI", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
        .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("BlazorWithIdentityApp1.ServerAPI"));
```

<span data-ttu-id="8cd43-114">Cuando el cliente se crea con `CreateClient` en el ejemplo anterior, `HttpClient` se proporcionan instancias de que incluyen tokens de acceso al hacer solicitudes al proyecto de servidor.</span><span class="sxs-lookup"><span data-stu-id="8cd43-114">Where the client is created with `CreateClient` in the preceding example, the `HttpClient` is supplied instances that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="8cd43-115">A continuación `HttpClient` , se usa el configurado para hacer solicitudes autorizadas `try-catch` mediante un patrón simple.</span><span class="sxs-lookup"><span data-stu-id="8cd43-115">The configured `HttpClient` is then used to make authorized requests using a simple `try-catch` pattern.</span></span> <span data-ttu-id="8cd43-116">El siguiente `FetchData` componente solicita datos de previsión meteorológica:</span><span class="sxs-lookup"><span data-stu-id="8cd43-116">The following `FetchData` component requests weather forecast data:</span></span>

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

<span data-ttu-id="8cd43-117">Como alternativa, puede definir un cliente con tipo que controle todos los problemas de adquisición de tokens y HTTP en una sola clase:</span><span class="sxs-lookup"><span data-stu-id="8cd43-117">Alternatively, you can define a typed client that handles all of the HTTP and token acquisition concerns within a single class:</span></span>

<span data-ttu-id="8cd43-118">*WeatherClient.CS*:</span><span class="sxs-lookup"><span data-stu-id="8cd43-118">*WeatherClient.cs*:</span></span>

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

<span data-ttu-id="8cd43-119">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="8cd43-119">*Program.cs*:</span></span>

```csharp
builder.Services.AddHttpClient<WeatherClient>(
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

<span data-ttu-id="8cd43-120">*FetchData. Razor*:</span><span class="sxs-lookup"><span data-stu-id="8cd43-120">*FetchData.razor*:</span></span>

```razor
@inject WeatherClient WeatherClient

...

protected override async Task OnInitializedAsync()
{
    forecasts = await WeatherClient.GetWeatherForeacasts();
}
```

## <a name="request-additional-access-tokens"></a><span data-ttu-id="8cd43-121">Solicitar tokens de acceso adicionales</span><span class="sxs-lookup"><span data-stu-id="8cd43-121">Request additional access tokens</span></span>

<span data-ttu-id="8cd43-122">Los tokens de acceso se pueden obtener manualmente `IAccessTokenProvider.RequestAccessToken`mediante una llamada a.</span><span class="sxs-lookup"><span data-stu-id="8cd43-122">Access tokens can be manually obtained by calling `IAccessTokenProvider.RequestAccessToken`.</span></span>

<span data-ttu-id="8cd43-123">En el siguiente ejemplo, una aplicación necesita los ámbitos de API de Azure Active Directory adicionales (AAD) Microsoft Graph para leer los datos de usuario y enviar correo.</span><span class="sxs-lookup"><span data-stu-id="8cd43-123">In the following example, additional Azure Active Directory (AAD) Microsoft Graph API scopes are required by an app to read user data and send mail.</span></span> <span data-ttu-id="8cd43-124">Después de agregar los permisos de Microsoft Graph API en el portal de Azure AAD, los ámbitos adicionales se configuran en`Program.Main`la aplicación cliente (, *Program.CS*):</span><span class="sxs-lookup"><span data-stu-id="8cd43-124">After adding the Microsoft Graph API permissions in the Azure AAD portal, the additional scopes are configured in the Client app (`Program.Main`, *Program.cs*):</span></span>

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

<span data-ttu-id="8cd43-125">El `IAccessTokenProvider.RequestToken` método proporciona una sobrecarga que permite a una aplicación aprovisionar un token de acceso con un conjunto determinado de ámbitos, tal como se muestra en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="8cd43-125">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision an access token with a given set of scopes, as seen in the following example:</span></span>

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

<span data-ttu-id="8cd43-126">`TryGetToken`Devuelve</span><span class="sxs-lookup"><span data-stu-id="8cd43-126">`TryGetToken` returns:</span></span>

* <span data-ttu-id="8cd43-127">`true`con para `token` su uso.</span><span class="sxs-lookup"><span data-stu-id="8cd43-127">`true` with the `token` for use.</span></span>
* <span data-ttu-id="8cd43-128">`false`Si el token no se recupera.</span><span class="sxs-lookup"><span data-stu-id="8cd43-128">`false` if the token isn't retrieved.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="8cd43-129">HttpClient y HttpRequestMessage con las opciones de solicitud de Fetch API</span><span class="sxs-lookup"><span data-stu-id="8cd43-129">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="8cd43-130">Cuando se ejecuta en webassembly en una aplicación de webassembly increíblemente <xref:System.Net.Http.HttpRequestMessage> brillante, [HttpClient](xref:fundamentals/http-requests) y se puede usar para personalizar las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="8cd43-130">When running on WebAssembly in a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) and <xref:System.Net.Http.HttpRequestMessage> can be used to customize requests.</span></span> <span data-ttu-id="8cd43-131">Por ejemplo, puede especificar el método HTTP y los encabezados de solicitud.</span><span class="sxs-lookup"><span data-stu-id="8cd43-131">For example, you can specify the HTTP method and request headers.</span></span> <span data-ttu-id="8cd43-132">En el ejemplo siguiente se `POST` realiza una solicitud a un punto de conexión de API de lista de tareas pendientes en el servidor y se muestra el cuerpo de la respuesta:</span><span class="sxs-lookup"><span data-stu-id="8cd43-132">The following example makes a `POST` request to a To Do List API endpoint on the server and shows the response body:</span></span>

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

<span data-ttu-id="8cd43-133">En la implementación de `HttpClient` de WebAssembly de .NET se usa [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span><span class="sxs-lookup"><span data-stu-id="8cd43-133">.NET WebAssembly's implementation of `HttpClient` uses [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span></span> <span data-ttu-id="8cd43-134">Fetch permite configurar varias [opciones específicas de la solicitud](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="8cd43-134">Fetch allows configuring several [request-specific options](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span> 

<span data-ttu-id="8cd43-135">Se pueden configurar opciones de solicitud de captura HTTP con los métodos de extensión `HttpRequestMessage` que se muestran en la siguiente tabla.</span><span class="sxs-lookup"><span data-stu-id="8cd43-135">HTTP fetch request options can be configured with `HttpRequestMessage` extension methods shown in the following table.</span></span>

| <span data-ttu-id="8cd43-136">Método de extensión `HttpRequestMessage`</span><span class="sxs-lookup"><span data-stu-id="8cd43-136">`HttpRequestMessage` extension method</span></span> | <span data-ttu-id="8cd43-137">Propiedad de solicitud de captura</span><span class="sxs-lookup"><span data-stu-id="8cd43-137">Fetch request property</span></span> |
| ------------------------------------- | ---------------------- |
| `SetBrowserRequestCredentials`        | [<span data-ttu-id="8cd43-138">credentials</span><span class="sxs-lookup"><span data-stu-id="8cd43-138">credentials</span></span>](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| `SetBrowserRequestCache`              | [<span data-ttu-id="8cd43-139">cache</span><span class="sxs-lookup"><span data-stu-id="8cd43-139">cache</span></span>](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| `SetBrowserRequestMode`               | [<span data-ttu-id="8cd43-140">mode</span><span class="sxs-lookup"><span data-stu-id="8cd43-140">mode</span></span>](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| `SetBrowserRequestIntegrity`          | [<span data-ttu-id="8cd43-141">integrity</span><span class="sxs-lookup"><span data-stu-id="8cd43-141">integrity</span></span>](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

<span data-ttu-id="8cd43-142">Se pueden establecer más opciones usando el método de extensión `SetBrowserRequestOption`, que es más genérico.</span><span class="sxs-lookup"><span data-stu-id="8cd43-142">You can set additional options using the more generic `SetBrowserRequestOption` extension method.</span></span>
 
<span data-ttu-id="8cd43-143">Normalmente, la respuesta HTTP se almacena en búfer en una aplicación webassembly increíblemente ligera para habilitar la compatibilidad con las lecturas de sincronización en el contenido de la respuesta.</span><span class="sxs-lookup"><span data-stu-id="8cd43-143">The HTTP response is typically buffered in a Blazor WebAssembly app to enable support for sync reads on the response content.</span></span> <span data-ttu-id="8cd43-144">Para habilitar la compatibilidad con la transmisión de respuestas, use el método de extensión `SetBrowserResponseStreamingEnabled` en la solicitud.</span><span class="sxs-lookup"><span data-stu-id="8cd43-144">To enable support for response streaming, use the `SetBrowserResponseStreamingEnabled` extension method on the request.</span></span>

<span data-ttu-id="8cd43-145">Para incluir credenciales en una solicitud entre orígenes, use el método de extensión `SetBrowserRequestCredentials`:</span><span class="sxs-lookup"><span data-stu-id="8cd43-145">To include credentials in a cross-origin request, use the `SetBrowserRequestCredentials` extension method:</span></span>

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

<span data-ttu-id="8cd43-146">Para obtener más información sobre las opciones de Fetch API, vea [Documentación web de MDN: WindowOrWorkerGlobalScope.fetch(): parámetros](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="8cd43-146">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

<span data-ttu-id="8cd43-147">Al enviar credenciales (cookies de autorización o encabezados) en solicitudes de CORS, la directiva de CORS debe permitir el encabezado `Authorization`.</span><span class="sxs-lookup"><span data-stu-id="8cd43-147">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="8cd43-148">En la directiva siguiente se incluye la configuración de:</span><span class="sxs-lookup"><span data-stu-id="8cd43-148">The following policy includes configuration for:</span></span>

* <span data-ttu-id="8cd43-149">Orígenes de la solicitud (`http://localhost:5000`, `https://localhost:5001`).</span><span class="sxs-lookup"><span data-stu-id="8cd43-149">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="8cd43-150">Cualquier método (verbo).</span><span class="sxs-lookup"><span data-stu-id="8cd43-150">Any method (verb).</span></span>
* <span data-ttu-id="8cd43-151">Los encabezados `Content-Type` y `Authorization`.</span><span class="sxs-lookup"><span data-stu-id="8cd43-151">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="8cd43-152">Para permitir un encabezado personalizado (por ejemplo, `x-custom-header`), enumere el encabezado al llamar a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span><span class="sxs-lookup"><span data-stu-id="8cd43-152">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="8cd43-153">Las credenciales establecidas por el código JavaScript del lado cliente (la propiedad `credentials` establecida en `include`).</span><span class="sxs-lookup"><span data-stu-id="8cd43-153">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="8cd43-154">Para obtener más información, vea <xref:security/cors> y el componente del evaluador de solicitudes HTTP de la aplicación de ejemplo (*Components/HTTPRequestTester.razor*).</span><span class="sxs-lookup"><span data-stu-id="8cd43-154">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (*Components/HTTPRequestTester.razor*).</span></span>

## <a name="handle-token-request-errors"></a><span data-ttu-id="8cd43-155">Controlar errores de solicitudes de token</span><span class="sxs-lookup"><span data-stu-id="8cd43-155">Handle token request errors</span></span>

<span data-ttu-id="8cd43-156">Cuando una aplicación de una sola página (SPA) autentica a un usuario mediante Open ID Connect (OIDC), el estado de autenticación se mantiene localmente en el SPA y en el proveedor de identidades (IP) en forma de una cookie de sesión que se establece como resultado del usuario que proporciona sus credenciales.</span><span class="sxs-lookup"><span data-stu-id="8cd43-156">When a Single Page Application (SPA) authenticates a user using Open ID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="8cd43-157">Normalmente, los tokens que emite la IP para el usuario son válidos durante breves períodos de tiempo, aproximadamente una hora, por lo que la aplicación cliente debe capturar nuevos tokens con regularidad.</span><span class="sxs-lookup"><span data-stu-id="8cd43-157">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="8cd43-158">De lo contrario, se cerrará la sesión del usuario después de que expiren los tokens concedidos.</span><span class="sxs-lookup"><span data-stu-id="8cd43-158">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="8cd43-159">En la mayoría de los casos, los clientes de OIDC pueden aprovisionar nuevos tokens sin necesidad de que el usuario se autentique de nuevo gracias al estado de autenticación o a la "sesión" que se mantiene dentro de la dirección IP.</span><span class="sxs-lookup"><span data-stu-id="8cd43-159">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="8cd43-160">Hay algunos casos en los que el cliente no puede obtener un token sin la interacción del usuario, por ejemplo, cuando por alguna razón el usuario cierra la sesión explícitamente desde la dirección IP.</span><span class="sxs-lookup"><span data-stu-id="8cd43-160">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="8cd43-161">Este escenario se produce si un usuario `https://login.microsoftonline.com` visita y cierra la sesión. En estos casos, la aplicación no sabe inmediatamente que el usuario ha cerrado la sesión. Es posible que el token que contiene el cliente ya no sea válido.</span><span class="sxs-lookup"><span data-stu-id="8cd43-161">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="8cd43-162">Además, el cliente no puede aprovisionar un token nuevo sin la interacción del usuario después de que expire el token actual.</span><span class="sxs-lookup"><span data-stu-id="8cd43-162">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="8cd43-163">Estos escenarios no son específicos de la autenticación basada en tokens.</span><span class="sxs-lookup"><span data-stu-id="8cd43-163">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="8cd43-164">Forman parte de la naturaleza de spa.</span><span class="sxs-lookup"><span data-stu-id="8cd43-164">They are part of the nature of SPAs.</span></span> <span data-ttu-id="8cd43-165">Un SPA que use cookies tampoco podrá llamar a una API de servidor si se quita la cookie de autenticación.</span><span class="sxs-lookup"><span data-stu-id="8cd43-165">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="8cd43-166">Cuando una aplicación realiza llamadas API a recursos protegidos, debe tener en cuenta lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="8cd43-166">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="8cd43-167">Para aprovisionar un nuevo token de acceso para llamar a la API, es posible que sea necesario volver a autenticar al usuario.</span><span class="sxs-lookup"><span data-stu-id="8cd43-167">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="8cd43-168">Incluso si el cliente tiene un token que parece ser válido, es posible que se produzca un error en la llamada al servidor porque el usuario revocó el token.</span><span class="sxs-lookup"><span data-stu-id="8cd43-168">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="8cd43-169">Cuando la aplicación solicita un token, hay dos resultados posibles:</span><span class="sxs-lookup"><span data-stu-id="8cd43-169">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="8cd43-170">La solicitud se realiza correctamente y la aplicación tiene un token válido.</span><span class="sxs-lookup"><span data-stu-id="8cd43-170">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="8cd43-171">Se produce un error en la solicitud y la aplicación debe autenticar al usuario de nuevo para obtener un nuevo token.</span><span class="sxs-lookup"><span data-stu-id="8cd43-171">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="8cd43-172">Cuando se produce un error en una solicitud de token, debe decidir si desea guardar el estado actual antes de realizar una redirección.</span><span class="sxs-lookup"><span data-stu-id="8cd43-172">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="8cd43-173">Existen varios enfoques con niveles crecientes de complejidad:</span><span class="sxs-lookup"><span data-stu-id="8cd43-173">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="8cd43-174">Almacenar el estado de la página actual en el almacenamiento de sesión.</span><span class="sxs-lookup"><span data-stu-id="8cd43-174">Store the current page state in session storage.</span></span> <span data-ttu-id="8cd43-175">Durante `OnInitializeAsync`, compruebe si el estado se puede restaurar antes de continuar.</span><span class="sxs-lookup"><span data-stu-id="8cd43-175">During `OnInitializeAsync`, check if state can be restored before continuing.</span></span>
* <span data-ttu-id="8cd43-176">Agregue un parámetro de cadena de consulta y úselo como una manera de señalar la aplicación que necesita para volver a hidratar el estado guardado anteriormente.</span><span class="sxs-lookup"><span data-stu-id="8cd43-176">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="8cd43-177">Agregue un parámetro de cadena de consulta con un identificador único para almacenar los datos en el almacenamiento de sesión sin poner en peligro las colisiones con otros elementos.</span><span class="sxs-lookup"><span data-stu-id="8cd43-177">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="8cd43-178">El ejemplo siguiente muestra cómo:</span><span class="sxs-lookup"><span data-stu-id="8cd43-178">The following example shows how to:</span></span>

* <span data-ttu-id="8cd43-179">Conservar el estado antes de redirigir a la página de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="8cd43-179">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="8cd43-180">Recupere el estado anterior después de la autenticación mediante el parámetro de cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="8cd43-180">Recover the previous state afterward authentication using the query string parameter.</span></span>

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

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="8cd43-181">Guardar el estado de la aplicación antes de una operación de autenticación</span><span class="sxs-lookup"><span data-stu-id="8cd43-181">Save app state before an authentication operation</span></span>

<span data-ttu-id="8cd43-182">Durante una operación de autenticación, hay casos en los que desea guardar el estado de la aplicación antes de que el explorador se redirija a la dirección IP.</span><span class="sxs-lookup"><span data-stu-id="8cd43-182">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="8cd43-183">Esto puede ser así cuando se usa algo como un contenedor de estado y se desea restaurar el estado después de que la autenticación se realice correctamente.</span><span class="sxs-lookup"><span data-stu-id="8cd43-183">This can be the case when you are using something like a state container and you want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="8cd43-184">Puede usar un objeto de estado de autenticación personalizado para conservar el estado específico de la aplicación o una referencia a él y restaurar ese estado una vez que la operación de autenticación se complete correctamente.</span><span class="sxs-lookup"><span data-stu-id="8cd43-184">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state once the authentication operation successfully completes.</span></span>

<span data-ttu-id="8cd43-185">`Authentication`componente (*páginas/autenticación. Razor*):</span><span class="sxs-lookup"><span data-stu-id="8cd43-185">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

## <a name="customize-app-routes"></a><span data-ttu-id="8cd43-186">Personalización de rutas de aplicación</span><span class="sxs-lookup"><span data-stu-id="8cd43-186">Customize app routes</span></span>

<span data-ttu-id="8cd43-187">De forma predeterminada, `Microsoft.AspNetCore.Components.WebAssembly.Authentication` la biblioteca utiliza las rutas que se muestran en la tabla siguiente para representar diferentes Estados de autenticación.</span><span class="sxs-lookup"><span data-stu-id="8cd43-187">By default, the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="8cd43-188">Ruta</span><span class="sxs-lookup"><span data-stu-id="8cd43-188">Route</span></span>                            | <span data-ttu-id="8cd43-189">Propósito</span><span class="sxs-lookup"><span data-stu-id="8cd43-189">Purpose</span></span> |
| -------------------------------- | ------- |
| `authentication/login`           | <span data-ttu-id="8cd43-190">Desencadena una operación de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="8cd43-190">Triggers a sign-in operation.</span></span> |
| `authentication/login-callback`  | <span data-ttu-id="8cd43-191">Controla el resultado de cualquier operación de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="8cd43-191">Handles the result of any sign-in operation.</span></span> |
| `authentication/login-failed`    | <span data-ttu-id="8cd43-192">Muestra mensajes de error cuando se produce un error en la operación de inicio de sesión por algún motivo.</span><span class="sxs-lookup"><span data-stu-id="8cd43-192">Displays error messages when the sign-in operation fails for some reason.</span></span> |
| `authentication/logout`          | <span data-ttu-id="8cd43-193">Desencadena una operación de cierre de sesión.</span><span class="sxs-lookup"><span data-stu-id="8cd43-193">Triggers a sign-out operation.</span></span> |
| `authentication/logout-callback` | <span data-ttu-id="8cd43-194">Controla el resultado de una operación de cierre de sesión.</span><span class="sxs-lookup"><span data-stu-id="8cd43-194">Handles the result of a sign-out operation.</span></span> |
| `authentication/logout-failed`   | <span data-ttu-id="8cd43-195">Muestra mensajes de error cuando se produce un error en la operación de cierre de sesión por algún motivo.</span><span class="sxs-lookup"><span data-stu-id="8cd43-195">Displays error messages when the sign-out operation fails for some reason.</span></span> |
| `authentication/logged-out`      | <span data-ttu-id="8cd43-196">Indica que el usuario ha cerrado la sesión correctamente.</span><span class="sxs-lookup"><span data-stu-id="8cd43-196">Indicates that the user has successfully logout.</span></span> |
| `authentication/profile`         | <span data-ttu-id="8cd43-197">Desencadena una operación para editar el perfil de usuario.</span><span class="sxs-lookup"><span data-stu-id="8cd43-197">Triggers an operation to edit the user profile.</span></span> |
| `authentication/register`        | <span data-ttu-id="8cd43-198">Desencadena una operación para registrar un nuevo usuario.</span><span class="sxs-lookup"><span data-stu-id="8cd43-198">Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="8cd43-199">Las rutas que se muestran en la tabla anterior se `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`pueden configurar mediante.</span><span class="sxs-lookup"><span data-stu-id="8cd43-199">The routes shown in the preceding table are configurable via `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`.</span></span> <span data-ttu-id="8cd43-200">Al establecer las opciones para proporcionar rutas personalizadas, confirme que la aplicación tiene una ruta que controla cada ruta de acceso.</span><span class="sxs-lookup"><span data-stu-id="8cd43-200">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="8cd43-201">En el ejemplo siguiente, todas las rutas de acceso tienen el `/security`prefijo.</span><span class="sxs-lookup"><span data-stu-id="8cd43-201">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="8cd43-202">`Authentication`componente (*páginas/autenticación. Razor*):</span><span class="sxs-lookup"><span data-stu-id="8cd43-202">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="8cd43-203">`Program.Main`(*Program.CS*):</span><span class="sxs-lookup"><span data-stu-id="8cd43-203">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="8cd43-204">Si el requisito requiere rutas de acceso completamente diferentes, establezca las rutas como se describió anteriormente `RemoteAuthenticatorView` y represente el con un parámetro de acción explícito:</span><span class="sxs-lookup"><span data-stu-id="8cd43-204">If the requirement calls for completely different paths, set the routes as described previously and render the `RemoteAuthenticatorView` with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="8cd43-205">Puede descomponer la interfaz de usuario en diferentes páginas si decide hacerlo.</span><span class="sxs-lookup"><span data-stu-id="8cd43-205">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="8cd43-206">Personalización de la interfaz de usuario de autenticación</span><span class="sxs-lookup"><span data-stu-id="8cd43-206">Customize the authentication user interface</span></span>

<span data-ttu-id="8cd43-207">`RemoteAuthenticatorView`incluye un conjunto predeterminado de partes de la interfaz de usuario para cada estado de autenticación.</span><span class="sxs-lookup"><span data-stu-id="8cd43-207">`RemoteAuthenticatorView` includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="8cd43-208">Cada Estado se puede personalizar pasando un personalizado `RenderFragment`.</span><span class="sxs-lookup"><span data-stu-id="8cd43-208">Each state can be customized by passing in a custom `RenderFragment`.</span></span> <span data-ttu-id="8cd43-209">Para personalizar el texto mostrado durante el proceso de inicio de sesión inicial, `RemoteAuthenticatorView` puede cambiar el de la manera siguiente.</span><span class="sxs-lookup"><span data-stu-id="8cd43-209">To customize the displayed text during the initial login process, can change the `RemoteAuthenticatorView` as follows.</span></span>

<span data-ttu-id="8cd43-210">`Authentication`componente (*páginas/autenticación. Razor*):</span><span class="sxs-lookup"><span data-stu-id="8cd43-210">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

<span data-ttu-id="8cd43-211">`RemoteAuthenticatorView` Tiene un fragmento que se puede utilizar por cada ruta de autenticación que se muestra en la tabla siguiente.</span><span class="sxs-lookup"><span data-stu-id="8cd43-211">The `RemoteAuthenticatorView` has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="8cd43-212">Ruta</span><span class="sxs-lookup"><span data-stu-id="8cd43-212">Route</span></span>                            | <span data-ttu-id="8cd43-213">Fragmento</span><span class="sxs-lookup"><span data-stu-id="8cd43-213">Fragment</span></span>                |
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

## <a name="customize-the-user"></a><span data-ttu-id="8cd43-214">Personalización del usuario</span><span class="sxs-lookup"><span data-stu-id="8cd43-214">Customize the user</span></span>

<span data-ttu-id="8cd43-215">Los usuarios enlazados a la aplicación se pueden personalizar.</span><span class="sxs-lookup"><span data-stu-id="8cd43-215">Users bound to the app can be customized.</span></span> <span data-ttu-id="8cd43-216">En el ejemplo siguiente, todos los usuarios autenticados reciben `amr` una demanda por cada uno de los métodos de autenticación del usuario.</span><span class="sxs-lookup"><span data-stu-id="8cd43-216">In the following example, all authenticated users receive an `amr` claim for each of the user's authentication methods.</span></span>

<span data-ttu-id="8cd43-217">Cree una clase que extienda la `RemoteUserAccount` clase:</span><span class="sxs-lookup"><span data-stu-id="8cd43-217">Create a class that extends the `RemoteUserAccount` class:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class OidcAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

<span data-ttu-id="8cd43-218">Cree una fábrica que extienda `AccountClaimsPrincipalFactory<TAccount>`:</span><span class="sxs-lookup"><span data-stu-id="8cd43-218">Create a factory that extends `AccountClaimsPrincipalFactory<TAccount>`:</span></span>

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

<span data-ttu-id="8cd43-219">Registrar servicios para usar `CustomAccountFactory`:</span><span class="sxs-lookup"><span data-stu-id="8cd43-219">Register services to use the `CustomAccountFactory`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddApiAuthorization<RemoteAuthenticationState, OidcAccount>()
    .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, OidcAccount, 
        CustomAccountFactory>();
```

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="8cd43-220">Compatibilidad de la representación previa con la autenticación</span><span class="sxs-lookup"><span data-stu-id="8cd43-220">Support prerendering with authentication</span></span>

<span data-ttu-id="8cd43-221">Después de seguir las instrucciones que aparecen en uno de los temas de la aplicación WebAssembly de Blazor hospedada, use estas instrucciones para crear una aplicación que:</span><span class="sxs-lookup"><span data-stu-id="8cd43-221">After following the guidance in one of the hosted Blazor WebAssembly app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="8cd43-222">Representa previamente las rutas de acceso para las que no se requiere autorización.</span><span class="sxs-lookup"><span data-stu-id="8cd43-222">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="8cd43-223">No representa previamente las rutas de acceso para las que se requiere autorización.</span><span class="sxs-lookup"><span data-stu-id="8cd43-223">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="8cd43-224">En la clase `Program` de la aplicación cliente (*Program.cs*), factorice los registros de servicio comunes en un método independiente (por ejemplo, `ConfigureCommonServices`):</span><span class="sxs-lookup"><span data-stu-id="8cd43-224">In the Client app's `Program` class (*Program.cs*), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

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

<span data-ttu-id="8cd43-225">En `Startup.ConfigureServices` de la aplicación de servidor, registre estos servicios adicionales:</span><span class="sxs-lookup"><span data-stu-id="8cd43-225">In the Server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

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

<span data-ttu-id="8cd43-226">En el método `Startup.Configure` de la aplicación de servidor, reemplace `endpoints.MapFallbackToFile("index.html")` por `endpoints.MapFallbackToPage("/_Host")`:</span><span class="sxs-lookup"><span data-stu-id="8cd43-226">In the Server app's `Startup.Configure` method, replace `endpoints.MapFallbackToFile("index.html")` with `endpoints.MapFallbackToPage("/_Host")`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="8cd43-227">En la aplicación de servidor, cree una carpeta *Pages* si todavía no existe.</span><span class="sxs-lookup"><span data-stu-id="8cd43-227">In the Server app, create a *Pages* folder if it doesn't exist.</span></span> <span data-ttu-id="8cd43-228">Cree una página *_Host.cshtml* dentro de la carpeta *Pages* de la aplicación de servidor.</span><span class="sxs-lookup"><span data-stu-id="8cd43-228">Create a *_Host.cshtml* page inside the Server app's *Pages* folder.</span></span> <span data-ttu-id="8cd43-229">Pegue el contenido del archivo *wwwroot/index.html* de la aplicación cliente en el archivo *Pages/_Host.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="8cd43-229">Paste the contents from the Client app's *wwwroot/index.html* file into the *Pages/_Host.cshtml* file.</span></span> <span data-ttu-id="8cd43-230">Actualice el contenido del archivo:</span><span class="sxs-lookup"><span data-stu-id="8cd43-230">Update the file's contents:</span></span>

* <span data-ttu-id="8cd43-231">Agregue `@page "_Host"` a la parte superior del archivo.</span><span class="sxs-lookup"><span data-stu-id="8cd43-231">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="8cd43-232">Reemplace la etiqueta `<app>Loading...</app>` por la siguiente:</span><span class="sxs-lookup"><span data-stu-id="8cd43-232">Replace the `<app>Loading...</app>` tag with the following:</span></span>

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
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="8cd43-233">Opciones para aplicaciones hospedadas y proveedores de inicio de sesión de terceros</span><span class="sxs-lookup"><span data-stu-id="8cd43-233">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="8cd43-234">Al autenticar y autorizar una aplicación WebAssembly de Blazor con un proveedor de terceros, hay varias opciones disponibles para autenticar al usuario.</span><span class="sxs-lookup"><span data-stu-id="8cd43-234">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="8cd43-235">Su elección depende del escenario.</span><span class="sxs-lookup"><span data-stu-id="8cd43-235">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="8cd43-236">Para obtener más información, vea <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="8cd43-236">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="8cd43-237">Autenticación de usuarios solo para llamadas a las API de terceros protegidas</span><span class="sxs-lookup"><span data-stu-id="8cd43-237">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="8cd43-238">Autentique al usuario con un flujo de OAuth del lado cliente en el proveedor de la API de terceros:</span><span class="sxs-lookup"><span data-stu-id="8cd43-238">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="8cd43-239">En este escenario:</span><span class="sxs-lookup"><span data-stu-id="8cd43-239">In this scenario:</span></span>

* <span data-ttu-id="8cd43-240">El servidor que hospeda la aplicación no desempeña ningún rol.</span><span class="sxs-lookup"><span data-stu-id="8cd43-240">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="8cd43-241">No se pueden proteger las API en el servidor.</span><span class="sxs-lookup"><span data-stu-id="8cd43-241">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="8cd43-242">La aplicación solo puede llamar a las API de terceros protegidas.</span><span class="sxs-lookup"><span data-stu-id="8cd43-242">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="8cd43-243">Autenticación de usuarios con un proveedor de terceros y llamada a las API protegidas en el servidor host y en el tercero</span><span class="sxs-lookup"><span data-stu-id="8cd43-243">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="8cd43-244">Configure Identity con un proveedor de inicio de sesión de terceros.</span><span class="sxs-lookup"><span data-stu-id="8cd43-244">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="8cd43-245">Obtenga los tokens necesarios para el acceso de API de terceros y almacénelos.</span><span class="sxs-lookup"><span data-stu-id="8cd43-245">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="8cd43-246">Cuando un usuario inicia sesión, Identity recopila tokens de acceso y actualización como parte del proceso de autenticación.</span><span class="sxs-lookup"><span data-stu-id="8cd43-246">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="8cd43-247">En ese momento, hay un par de enfoques disponibles para realizar llamadas API a las API de terceros.</span><span class="sxs-lookup"><span data-stu-id="8cd43-247">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="8cd43-248">Uso de un token de acceso de servidor para recuperar el token de acceso de terceros</span><span class="sxs-lookup"><span data-stu-id="8cd43-248">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="8cd43-249">Use el token de acceso generado en el servidor para recuperar el token de acceso de terceros de un punto de conexión de la API de servidor.</span><span class="sxs-lookup"><span data-stu-id="8cd43-249">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="8cd43-250">Desde allí, use el token de acceso de terceros para llamar a recursos de API de terceros directamente Identity desde en el cliente.</span><span class="sxs-lookup"><span data-stu-id="8cd43-250">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="8cd43-251">Este enfoque no se recomienda.</span><span class="sxs-lookup"><span data-stu-id="8cd43-251">We don't recommend this approach.</span></span> <span data-ttu-id="8cd43-252">Este enfoque requiere tratar el token de acceso de terceros como si se hubiera generado para un cliente público.</span><span class="sxs-lookup"><span data-stu-id="8cd43-252">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="8cd43-253">En términos de OAuth, la aplicación pública no tiene un secreto de cliente porque no es de confianza para almacenar secretos de forma segura y el token de acceso se genera para un cliente confidencial.</span><span class="sxs-lookup"><span data-stu-id="8cd43-253">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="8cd43-254">Un cliente confidencial es un cliente que tiene un secreto de cliente y se supone que puede almacenar secretos de forma segura.</span><span class="sxs-lookup"><span data-stu-id="8cd43-254">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="8cd43-255">El token de acceso de terceros podría recibir ámbitos adicionales para realizar operaciones confidenciales en función del hecho de que la tercera parte emitió el token para un cliente de más confianza.</span><span class="sxs-lookup"><span data-stu-id="8cd43-255">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="8cd43-256">Del mismo modo, los tokens de actualización no deben emitirse para un cliente que no sea de confianza, ya que esto proporciona acceso ilimitado al cliente a menos que se apliquen otras restricciones.</span><span class="sxs-lookup"><span data-stu-id="8cd43-256">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="8cd43-257">Realización de llamadas API desde el cliente a la API de servidor para llamar a las API de terceros</span><span class="sxs-lookup"><span data-stu-id="8cd43-257">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="8cd43-258">Realice una llamada API desde el cliente a la API del servidor.</span><span class="sxs-lookup"><span data-stu-id="8cd43-258">Make an API call from the client to the server API.</span></span> <span data-ttu-id="8cd43-259">En el servidor, recupere el token de acceso para el recurso de la API de terceros y emita cualquier llamada que sea necesaria.</span><span class="sxs-lookup"><span data-stu-id="8cd43-259">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="8cd43-260">Aunque este enfoque requiere un salto de red adicional a través del servidor para llamar a una API de terceros, en última instancia resulta una experiencia más segura:</span><span class="sxs-lookup"><span data-stu-id="8cd43-260">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="8cd43-261">El servidor puede almacenar tokens de actualización y asegurarse de que la aplicación no pierde el acceso a recursos de terceros.</span><span class="sxs-lookup"><span data-stu-id="8cd43-261">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="8cd43-262">La aplicación no puede filtrar los tokens de acceso del servidor que puedan contener permisos más confidenciales.</span><span class="sxs-lookup"><span data-stu-id="8cd43-262">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>
