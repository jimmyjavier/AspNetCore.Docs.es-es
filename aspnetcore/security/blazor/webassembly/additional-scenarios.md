---
<span data-ttu-id="61ddc-101">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="61ddc-101">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="61ddc-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="61ddc-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61ddc-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-103">'Blazor'</span></span>
- <span data-ttu-id="61ddc-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61ddc-104">'Identity'</span></span>
- <span data-ttu-id="61ddc-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61ddc-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="61ddc-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-106">'Razor'</span></span>
- <span data-ttu-id="61ddc-107">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="61ddc-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="61ddc-108">ASP.NET Core Blazor escenarios de seguridad adicionales de Webassembly</span><span class="sxs-lookup"><span data-stu-id="61ddc-108">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="61ddc-109">Por [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="61ddc-109">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

## <a name="attach-tokens-to-outgoing-requests"></a><span data-ttu-id="61ddc-110">Adjuntar tokens a solicitudes salientes</span><span class="sxs-lookup"><span data-stu-id="61ddc-110">Attach tokens to outgoing requests</span></span>

<span data-ttu-id="61ddc-111">El `AuthorizationMessageHandler` servicio se puede usar con `HttpClient` para adjuntar tokens de acceso a las solicitudes salientes.</span><span class="sxs-lookup"><span data-stu-id="61ddc-111">The `AuthorizationMessageHandler` service can be used with `HttpClient` to attach access tokens to outgoing requests.</span></span> <span data-ttu-id="61ddc-112">Los tokens se adquieren con el `IAccessTokenProvider` servicio existente.</span><span class="sxs-lookup"><span data-stu-id="61ddc-112">Tokens are acquired using the existing `IAccessTokenProvider` service.</span></span> <span data-ttu-id="61ddc-113">Si no se puede adquirir un token, `AccessTokenNotAvailableException` se produce una excepción.</span><span class="sxs-lookup"><span data-stu-id="61ddc-113">If a token can't be acquired, an `AccessTokenNotAvailableException` is thrown.</span></span> <span data-ttu-id="61ddc-114">`AccessTokenNotAvailableException`tiene un `Redirect` método que se puede utilizar para navegar por el usuario al proveedor de identidades para adquirir un nuevo token.</span><span class="sxs-lookup"><span data-stu-id="61ddc-114">`AccessTokenNotAvailableException` has a `Redirect` method that can be used to navigate the user to the identity provider to acquire a new token.</span></span> <span data-ttu-id="61ddc-115">`AuthorizationMessageHandler`Se puede configurar con las direcciones URL autorizadas, los ámbitos y la dirección URL de retorno mediante el `ConfigureHandler` método.</span><span class="sxs-lookup"><span data-stu-id="61ddc-115">The `AuthorizationMessageHandler` can be configured with the authorized URLs, scopes, and return URL using the `ConfigureHandler` method.</span></span>

<span data-ttu-id="61ddc-116">En el ejemplo siguiente, `AuthorizationMessageHandler` configura `HttpClient` en `Program.Main` (*Program.CS*):</span><span class="sxs-lookup"><span data-stu-id="61ddc-116">In the following example, `AuthorizationMessageHandler` configures an `HttpClient` in `Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="61ddc-117">Para mayor comodidad, `BaseAddressAuthorizationMessageHandler` se incluye una que está preconfigurada con la dirección base de la aplicación como una dirección URL autorizada.</span><span class="sxs-lookup"><span data-stu-id="61ddc-117">For convenience, a `BaseAddressAuthorizationMessageHandler` is included that's preconfigured with the app base address as an authorized URL.</span></span> <span data-ttu-id="61ddc-118">Las Blazor plantillas de Webassembly habilitadas para la autenticación ahora <xref:System.Net.Http.IHttpClientFactory> se usan en el proyecto de API de servidor para configurar una <xref:System.Net.Http.HttpClient> con `BaseAddressAuthorizationMessageHandler` :</span><span class="sxs-lookup"><span data-stu-id="61ddc-118">The authentication-enabled Blazor WebAssembly templates now use <xref:System.Net.Http.IHttpClientFactory> in the Server API project to set up an <xref:System.Net.Http.HttpClient> with the `BaseAddressAuthorizationMessageHandler`:</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient("BlazorWithIdentity.ServerAPI", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
        .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("BlazorWithIdentity.ServerAPI"));
```

<span data-ttu-id="61ddc-119">Cuando el cliente se crea con `CreateClient` en el ejemplo anterior, <xref:System.Net.Http.HttpClient> se proporcionan instancias de que incluyen tokens de acceso al hacer solicitudes al proyecto de servidor.</span><span class="sxs-lookup"><span data-stu-id="61ddc-119">Where the client is created with `CreateClient` in the preceding example, the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="61ddc-120"><xref:System.Net.Http.HttpClient>A continuación, se usa el configurado para hacer solicitudes autorizadas mediante un `try-catch` patrón simple.</span><span class="sxs-lookup"><span data-stu-id="61ddc-120">The configured <xref:System.Net.Http.HttpClient> is then used to make authorized requests using a simple `try-catch` pattern.</span></span>

<span data-ttu-id="61ddc-121">Componente `FetchData`(*Pages/FetchData.razor*):</span><span class="sxs-lookup"><span data-stu-id="61ddc-121">`FetchData` component (*Pages/FetchData.razor*):</span></span>

```csharp
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject HttpClient Client

...

protected override async Task OnInitializedAsync()
{
    try
    {
        forecasts = 
            await Client.GetFromJsonAsync<WeatherForecast[]>("WeatherForecast");
    }
    catch (AccessTokenNotAvailableException exception)
    {
        exception.Redirect();
    }
}
```

## <a name="typed-httpclient"></a><span data-ttu-id="61ddc-122">HttpClient con tipo</span><span class="sxs-lookup"><span data-stu-id="61ddc-122">Typed HttpClient</span></span>

<span data-ttu-id="61ddc-123">Se puede definir un cliente con tipo que controle todos los problemas de adquisición de tokens y HTTP en una sola clase.</span><span class="sxs-lookup"><span data-stu-id="61ddc-123">A typed client can be defined that handles all of the HTTP and token acquisition concerns within a single class.</span></span>

<span data-ttu-id="61ddc-124">*WeatherForecastClient.cs*:</span><span class="sxs-lookup"><span data-stu-id="61ddc-124">*WeatherForecastClient.cs*:</span></span>

```csharp
using System.Net.Http;
using System.Net.Http.Json;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using static {APP ASSEMBLY}.Data;

public class WeatherForecastClient
{
    private readonly HttpClient client;
 
    public WeatherForecastClient(HttpClient client)
    {
        this.client = client;
    }
 
    public async Task<WeatherForecast[]> GetForecastAsync()
    {
        var forecasts = new WeatherForecast[0];

        try
        {
            forecasts = await client.GetFromJsonAsync<WeatherForecast[]>(
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

<span data-ttu-id="61ddc-125">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="61ddc-125">`Program.Main` (*Program.cs*):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherForecastClient>(
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

<span data-ttu-id="61ddc-126">Componente `FetchData`(*Pages/FetchData.razor*):</span><span class="sxs-lookup"><span data-stu-id="61ddc-126">`FetchData` component (*Pages/FetchData.razor*):</span></span>

```razor
@inject WeatherForecastClient Client

...

protected override async Task OnInitializedAsync()
{
    forecasts = await Client.GetForecastAsync();
}
```

## <a name="configure-the-httpclient-handler"></a><span data-ttu-id="61ddc-127">Configurar el controlador HttpClient</span><span class="sxs-lookup"><span data-stu-id="61ddc-127">Configure the HttpClient handler</span></span>

<span data-ttu-id="61ddc-128">El controlador se puede configurar aún más con <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> para las solicitudes HTTP salientes.</span><span class="sxs-lookup"><span data-stu-id="61ddc-128">The handler can be further configured with <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> for outbound HTTP requests.</span></span>

<span data-ttu-id="61ddc-129">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="61ddc-129">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler(sp => sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(new [] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }));
```

## <a name="unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client"></a><span data-ttu-id="61ddc-130">Solicitudes de API Web no autenticadas o no autorizadas en una aplicación con un cliente predeterminado seguro</span><span class="sxs-lookup"><span data-stu-id="61ddc-130">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>

<span data-ttu-id="61ddc-131">Si la Blazor aplicación Webassembly usa normalmente un valor predeterminado seguro <xref:System.Net.Http.HttpClient> , la aplicación también puede realizar solicitudes de API Web no autenticadas o no autorizadas mediante la configuración de una llamada <xref:System.Net.Http.HttpClient> :</span><span class="sxs-lookup"><span data-stu-id="61ddc-131">If the Blazor WebAssembly app ordinarily uses a secure default <xref:System.Net.Http.HttpClient>, the app can also make unauthenticated or unauthorized web API requests by configuring a named <xref:System.Net.Http.HttpClient>:</span></span>

<span data-ttu-id="61ddc-132">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="61ddc-132">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI.NoAuthenticationClient", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="61ddc-133">El registro anterior se suma al registro predeterminado seguro existente <xref:System.Net.Http.HttpClient> .</span><span class="sxs-lookup"><span data-stu-id="61ddc-133">The preceding registration is in addition to the existing secure default <xref:System.Net.Http.HttpClient> registration.</span></span>

<span data-ttu-id="61ddc-134">Un componente crea <xref:System.Net.Http.HttpClient> desde <xref:System.Net.Http.IHttpClientFactory> para realizar solicitudes no autenticadas o no autorizadas:</span><span class="sxs-lookup"><span data-stu-id="61ddc-134">A component creates the <xref:System.Net.Http.HttpClient> from the <xref:System.Net.Http.IHttpClientFactory> to make unauthenticated or unauthorized requests:</span></span>

```razor
@inject IHttpClientFactory ClientFactory

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        var client = ClientFactory.CreateClient("ServerAPI.NoAuthenticationClient");

        forecasts = await client.GetFromJsonAsync<WeatherForecast[]>(
            "WeatherForecastNoAuthentication");
    }
}
```

> [!NOTE]
> <span data-ttu-id="61ddc-135">El controlador de la API de servidor, `WeatherForecastNoAuthenticationController` para el ejemplo anterior, no está marcado con el [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atributo.</span><span class="sxs-lookup"><span data-stu-id="61ddc-135">The controller in the server API, `WeatherForecastNoAuthenticationController` for the preceding example, isn't marked with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute.</span></span>

## <a name="request-additional-access-tokens"></a><span data-ttu-id="61ddc-136">Solicitar tokens de acceso adicionales</span><span class="sxs-lookup"><span data-stu-id="61ddc-136">Request additional access tokens</span></span>

<span data-ttu-id="61ddc-137">Los tokens de acceso se pueden obtener manualmente mediante una llamada a `IAccessTokenProvider.RequestAccessToken` .</span><span class="sxs-lookup"><span data-stu-id="61ddc-137">Access tokens can be manually obtained by calling `IAccessTokenProvider.RequestAccessToken`.</span></span>

<span data-ttu-id="61ddc-138">En el siguiente ejemplo, una aplicación necesita los ámbitos de API de Azure Active Directory adicionales (AAD) Microsoft Graph para leer los datos de usuario y enviar correo.</span><span class="sxs-lookup"><span data-stu-id="61ddc-138">In the following example, additional Azure Active Directory (AAD) Microsoft Graph API scopes are required by an app to read user data and send mail.</span></span> <span data-ttu-id="61ddc-139">Después de agregar los permisos de Microsoft Graph API en el portal de Azure AAD, los ámbitos adicionales se configuran en la aplicación cliente.</span><span class="sxs-lookup"><span data-stu-id="61ddc-139">After adding the Microsoft Graph API permissions in the Azure AAD portal, the additional scopes are configured in the Client app.</span></span>

<span data-ttu-id="61ddc-140">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="61ddc-140">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="61ddc-141">El `IAccessTokenProvider.RequestToken` método proporciona una sobrecarga que permite a una aplicación aprovisionar un token de acceso con un conjunto determinado de ámbitos.</span><span class="sxs-lookup"><span data-stu-id="61ddc-141">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision an access token with a given set of scopes.</span></span>

<span data-ttu-id="61ddc-142">En un Razor componente:</span><span class="sxs-lookup"><span data-stu-id="61ddc-142">In a Razor component:</span></span>

```razor
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

<span data-ttu-id="61ddc-143">`TryGetToken`Devuelve</span><span class="sxs-lookup"><span data-stu-id="61ddc-143">`TryGetToken` returns:</span></span>

* <span data-ttu-id="61ddc-144">`true`con `token` para su uso.</span><span class="sxs-lookup"><span data-stu-id="61ddc-144">`true` with the `token` for use.</span></span>
* <span data-ttu-id="61ddc-145">`false`Si el token no se recupera.</span><span class="sxs-lookup"><span data-stu-id="61ddc-145">`false` if the token isn't retrieved.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="61ddc-146">HttpClient y HttpRequestMessage con las opciones de solicitud de Fetch API</span><span class="sxs-lookup"><span data-stu-id="61ddc-146">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="61ddc-147">Al ejecutar webassembly en una Blazor aplicación Webassembly, [HttpClient](xref:fundamentals/http-requests) y <xref:System.Net.Http.HttpRequestMessage> se puede usar para personalizar las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="61ddc-147">When running on WebAssembly in a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) and <xref:System.Net.Http.HttpRequestMessage> can be used to customize requests.</span></span> <span data-ttu-id="61ddc-148">Por ejemplo, puede especificar el método HTTP y los encabezados de solicitud.</span><span class="sxs-lookup"><span data-stu-id="61ddc-148">For example, you can specify the HTTP method and request headers.</span></span> <span data-ttu-id="61ddc-149">El componente siguiente realiza una `POST` solicitud a un punto de conexión de API de lista de tareas pendientes en el servidor y muestra el cuerpo de la respuesta:</span><span class="sxs-lookup"><span data-stu-id="61ddc-149">The following component makes a `POST` request to a To Do List API endpoint on the server and shows the response body:</span></span>

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

<span data-ttu-id="61ddc-150">En la implementación de `HttpClient` de WebAssembly de .NET se usa [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span><span class="sxs-lookup"><span data-stu-id="61ddc-150">.NET WebAssembly's implementation of `HttpClient` uses [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span></span> <span data-ttu-id="61ddc-151">Fetch permite configurar varias [opciones específicas de la solicitud](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="61ddc-151">Fetch allows configuring several [request-specific options](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span> 

<span data-ttu-id="61ddc-152">Se pueden configurar opciones de solicitud de captura HTTP con los métodos de extensión `HttpRequestMessage` que se muestran en la siguiente tabla.</span><span class="sxs-lookup"><span data-stu-id="61ddc-152">HTTP fetch request options can be configured with `HttpRequestMessage` extension methods shown in the following table.</span></span>

| <span data-ttu-id="61ddc-153">Método de extensión `HttpRequestMessage`</span><span class="sxs-lookup"><span data-stu-id="61ddc-153">`HttpRequestMessage` extension method</span></span> | <span data-ttu-id="61ddc-154">Propiedad de solicitud de captura</span><span class="sxs-lookup"><span data-stu-id="61ddc-154">Fetch request property</span></span> |
| ---
<span data-ttu-id="61ddc-155">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="61ddc-155">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="61ddc-156">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="61ddc-156">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61ddc-157">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-157">'Blazor'</span></span>
- <span data-ttu-id="61ddc-158">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61ddc-158">'Identity'</span></span>
- <span data-ttu-id="61ddc-159">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61ddc-159">'Let's Encrypt'</span></span>
- <span data-ttu-id="61ddc-160">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-160">'Razor'</span></span>
- <span data-ttu-id="61ddc-161">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="61ddc-161">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61ddc-162">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="61ddc-162">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="61ddc-163">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="61ddc-163">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61ddc-164">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-164">'Blazor'</span></span>
- <span data-ttu-id="61ddc-165">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61ddc-165">'Identity'</span></span>
- <span data-ttu-id="61ddc-166">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61ddc-166">'Let's Encrypt'</span></span>
- <span data-ttu-id="61ddc-167">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-167">'Razor'</span></span>
- <span data-ttu-id="61ddc-168">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="61ddc-168">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61ddc-169">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="61ddc-169">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="61ddc-170">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="61ddc-170">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61ddc-171">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-171">'Blazor'</span></span>
- <span data-ttu-id="61ddc-172">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61ddc-172">'Identity'</span></span>
- <span data-ttu-id="61ddc-173">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61ddc-173">'Let's Encrypt'</span></span>
- <span data-ttu-id="61ddc-174">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-174">'Razor'</span></span>
- <span data-ttu-id="61ddc-175">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="61ddc-175">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61ddc-176">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="61ddc-176">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="61ddc-177">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="61ddc-177">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61ddc-178">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-178">'Blazor'</span></span>
- <span data-ttu-id="61ddc-179">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61ddc-179">'Identity'</span></span>
- <span data-ttu-id="61ddc-180">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61ddc-180">'Let's Encrypt'</span></span>
- <span data-ttu-id="61ddc-181">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-181">'Razor'</span></span>
- <span data-ttu-id="61ddc-182">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="61ddc-182">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61ddc-183">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="61ddc-183">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="61ddc-184">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="61ddc-184">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61ddc-185">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-185">'Blazor'</span></span>
- <span data-ttu-id="61ddc-186">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61ddc-186">'Identity'</span></span>
- <span data-ttu-id="61ddc-187">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61ddc-187">'Let's Encrypt'</span></span>
- <span data-ttu-id="61ddc-188">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-188">'Razor'</span></span>
- <span data-ttu-id="61ddc-189">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="61ddc-189">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61ddc-190">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="61ddc-190">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="61ddc-191">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="61ddc-191">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61ddc-192">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-192">'Blazor'</span></span>
- <span data-ttu-id="61ddc-193">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61ddc-193">'Identity'</span></span>
- <span data-ttu-id="61ddc-194">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61ddc-194">'Let's Encrypt'</span></span>
- <span data-ttu-id="61ddc-195">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-195">'Razor'</span></span>
- <span data-ttu-id="61ddc-196">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="61ddc-196">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61ddc-197">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="61ddc-197">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="61ddc-198">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="61ddc-198">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61ddc-199">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-199">'Blazor'</span></span>
- <span data-ttu-id="61ddc-200">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61ddc-200">'Identity'</span></span>
- <span data-ttu-id="61ddc-201">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61ddc-201">'Let's Encrypt'</span></span>
- <span data-ttu-id="61ddc-202">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-202">'Razor'</span></span>
- <span data-ttu-id="61ddc-203">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="61ddc-203">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61ddc-204">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="61ddc-204">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="61ddc-205">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="61ddc-205">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61ddc-206">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-206">'Blazor'</span></span>
- <span data-ttu-id="61ddc-207">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61ddc-207">'Identity'</span></span>
- <span data-ttu-id="61ddc-208">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61ddc-208">'Let's Encrypt'</span></span>
- <span data-ttu-id="61ddc-209">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-209">'Razor'</span></span>
- <span data-ttu-id="61ddc-210">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="61ddc-210">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61ddc-211">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="61ddc-211">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="61ddc-212">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="61ddc-212">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61ddc-213">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-213">'Blazor'</span></span>
- <span data-ttu-id="61ddc-214">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61ddc-214">'Identity'</span></span>
- <span data-ttu-id="61ddc-215">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61ddc-215">'Let's Encrypt'</span></span>
- <span data-ttu-id="61ddc-216">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-216">'Razor'</span></span>
- <span data-ttu-id="61ddc-217">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="61ddc-217">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61ddc-218">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="61ddc-218">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="61ddc-219">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="61ddc-219">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61ddc-220">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-220">'Blazor'</span></span>
- <span data-ttu-id="61ddc-221">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61ddc-221">'Identity'</span></span>
- <span data-ttu-id="61ddc-222">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61ddc-222">'Let's Encrypt'</span></span>
- <span data-ttu-id="61ddc-223">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-223">'Razor'</span></span>
- <span data-ttu-id="61ddc-224">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="61ddc-224">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61ddc-225">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="61ddc-225">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="61ddc-226">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="61ddc-226">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61ddc-227">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-227">'Blazor'</span></span>
- <span data-ttu-id="61ddc-228">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61ddc-228">'Identity'</span></span>
- <span data-ttu-id="61ddc-229">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61ddc-229">'Let's Encrypt'</span></span>
- <span data-ttu-id="61ddc-230">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-230">'Razor'</span></span>
- <span data-ttu-id="61ddc-231">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="61ddc-231">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61ddc-232">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="61ddc-232">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="61ddc-233">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="61ddc-233">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61ddc-234">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-234">'Blazor'</span></span>
- <span data-ttu-id="61ddc-235">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61ddc-235">'Identity'</span></span>
- <span data-ttu-id="61ddc-236">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61ddc-236">'Let's Encrypt'</span></span>
- <span data-ttu-id="61ddc-237">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-237">'Razor'</span></span>
- <span data-ttu-id="61ddc-238">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="61ddc-238">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61ddc-239">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="61ddc-239">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="61ddc-240">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="61ddc-240">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61ddc-241">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-241">'Blazor'</span></span>
- <span data-ttu-id="61ddc-242">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61ddc-242">'Identity'</span></span>
- <span data-ttu-id="61ddc-243">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61ddc-243">'Let's Encrypt'</span></span>
- <span data-ttu-id="61ddc-244">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-244">'Razor'</span></span>
- <span data-ttu-id="61ddc-245">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="61ddc-245">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61ddc-246">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="61ddc-246">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="61ddc-247">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="61ddc-247">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61ddc-248">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-248">'Blazor'</span></span>
- <span data-ttu-id="61ddc-249">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61ddc-249">'Identity'</span></span>
- <span data-ttu-id="61ddc-250">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61ddc-250">'Let's Encrypt'</span></span>
- <span data-ttu-id="61ddc-251">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-251">'Razor'</span></span>
- <span data-ttu-id="61ddc-252">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="61ddc-252">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61ddc-253">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="61ddc-253">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="61ddc-254">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="61ddc-254">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61ddc-255">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-255">'Blazor'</span></span>
- <span data-ttu-id="61ddc-256">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61ddc-256">'Identity'</span></span>
- <span data-ttu-id="61ddc-257">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61ddc-257">'Let's Encrypt'</span></span>
- <span data-ttu-id="61ddc-258">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-258">'Razor'</span></span>
- <span data-ttu-id="61ddc-259">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="61ddc-259">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61ddc-260">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="61ddc-260">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="61ddc-261">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="61ddc-261">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61ddc-262">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-262">'Blazor'</span></span>
- <span data-ttu-id="61ddc-263">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61ddc-263">'Identity'</span></span>
- <span data-ttu-id="61ddc-264">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61ddc-264">'Let's Encrypt'</span></span>
- <span data-ttu-id="61ddc-265">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-265">'Razor'</span></span>
- <span data-ttu-id="61ddc-266">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="61ddc-266">'SignalR' uid:</span></span> 

<span data-ttu-id="61ddc-267">------------------- | ---title: "ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales" Author: Description: "Obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales".</span><span class="sxs-lookup"><span data-stu-id="61ddc-267">------------------- | --- title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="61ddc-268">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="61ddc-268">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61ddc-269">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-269">'Blazor'</span></span>
- <span data-ttu-id="61ddc-270">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61ddc-270">'Identity'</span></span>
- <span data-ttu-id="61ddc-271">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61ddc-271">'Let's Encrypt'</span></span>
- <span data-ttu-id="61ddc-272">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-272">'Razor'</span></span>
- <span data-ttu-id="61ddc-273">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="61ddc-273">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61ddc-274">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="61ddc-274">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="61ddc-275">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="61ddc-275">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61ddc-276">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-276">'Blazor'</span></span>
- <span data-ttu-id="61ddc-277">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61ddc-277">'Identity'</span></span>
- <span data-ttu-id="61ddc-278">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61ddc-278">'Let's Encrypt'</span></span>
- <span data-ttu-id="61ddc-279">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-279">'Razor'</span></span>
- <span data-ttu-id="61ddc-280">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="61ddc-280">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61ddc-281">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="61ddc-281">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="61ddc-282">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="61ddc-282">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61ddc-283">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-283">'Blazor'</span></span>
- <span data-ttu-id="61ddc-284">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61ddc-284">'Identity'</span></span>
- <span data-ttu-id="61ddc-285">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61ddc-285">'Let's Encrypt'</span></span>
- <span data-ttu-id="61ddc-286">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-286">'Razor'</span></span>
- <span data-ttu-id="61ddc-287">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="61ddc-287">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61ddc-288">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="61ddc-288">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="61ddc-289">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="61ddc-289">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61ddc-290">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-290">'Blazor'</span></span>
- <span data-ttu-id="61ddc-291">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61ddc-291">'Identity'</span></span>
- <span data-ttu-id="61ddc-292">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61ddc-292">'Let's Encrypt'</span></span>
- <span data-ttu-id="61ddc-293">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-293">'Razor'</span></span>
- <span data-ttu-id="61ddc-294">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="61ddc-294">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61ddc-295">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="61ddc-295">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="61ddc-296">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="61ddc-296">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61ddc-297">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-297">'Blazor'</span></span>
- <span data-ttu-id="61ddc-298">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61ddc-298">'Identity'</span></span>
- <span data-ttu-id="61ddc-299">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61ddc-299">'Let's Encrypt'</span></span>
- <span data-ttu-id="61ddc-300">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-300">'Razor'</span></span>
- <span data-ttu-id="61ddc-301">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="61ddc-301">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61ddc-302">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="61ddc-302">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="61ddc-303">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="61ddc-303">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61ddc-304">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-304">'Blazor'</span></span>
- <span data-ttu-id="61ddc-305">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61ddc-305">'Identity'</span></span>
- <span data-ttu-id="61ddc-306">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61ddc-306">'Let's Encrypt'</span></span>
- <span data-ttu-id="61ddc-307">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-307">'Razor'</span></span>
- <span data-ttu-id="61ddc-308">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="61ddc-308">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61ddc-309">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="61ddc-309">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="61ddc-310">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="61ddc-310">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61ddc-311">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-311">'Blazor'</span></span>
- <span data-ttu-id="61ddc-312">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61ddc-312">'Identity'</span></span>
- <span data-ttu-id="61ddc-313">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61ddc-313">'Let's Encrypt'</span></span>
- <span data-ttu-id="61ddc-314">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-314">'Razor'</span></span>
- <span data-ttu-id="61ddc-315">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="61ddc-315">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61ddc-316">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="61ddc-316">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="61ddc-317">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="61ddc-317">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61ddc-318">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-318">'Blazor'</span></span>
- <span data-ttu-id="61ddc-319">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61ddc-319">'Identity'</span></span>
- <span data-ttu-id="61ddc-320">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61ddc-320">'Let's Encrypt'</span></span>
- <span data-ttu-id="61ddc-321">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-321">'Razor'</span></span>
- <span data-ttu-id="61ddc-322">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="61ddc-322">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61ddc-323">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="61ddc-323">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="61ddc-324">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="61ddc-324">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61ddc-325">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-325">'Blazor'</span></span>
- <span data-ttu-id="61ddc-326">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61ddc-326">'Identity'</span></span>
- <span data-ttu-id="61ddc-327">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61ddc-327">'Let's Encrypt'</span></span>
- <span data-ttu-id="61ddc-328">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-328">'Razor'</span></span>
- <span data-ttu-id="61ddc-329">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="61ddc-329">'SignalR' uid:</span></span> 

<span data-ttu-id="61ddc-330">----------- | | `SetBrowserRequestCredentials`         |  [credenciales](https://developer.mozilla.org/docs/Web/API/Request/credentials) | | `SetBrowserRequestCache`               |  [caché](https://developer.mozilla.org/docs/Web/API/Request/cache) | | `SetBrowserRequestMode`                |  [modo](https://developer.mozilla.org/docs/Web/API/Request/mode) | `SetBrowserRequestIntegrity` |           |  [integridad](https://developer.mozilla.org/docs/Web/API/Request/integrity) de |</span><span class="sxs-lookup"><span data-stu-id="61ddc-330">----------- | | `SetBrowserRequestCredentials`        | [credentials](https://developer.mozilla.org/docs/Web/API/Request/credentials) | | `SetBrowserRequestCache`              | [cache](https://developer.mozilla.org/docs/Web/API/Request/cache) | | `SetBrowserRequestMode`               | [mode](https://developer.mozilla.org/docs/Web/API/Request/mode) | | `SetBrowserRequestIntegrity`          | [integrity](https://developer.mozilla.org/docs/Web/API/Request/integrity) |</span></span>

<span data-ttu-id="61ddc-331">Se pueden establecer más opciones usando el método de extensión `SetBrowserRequestOption`, que es más genérico.</span><span class="sxs-lookup"><span data-stu-id="61ddc-331">You can set additional options using the more generic `SetBrowserRequestOption` extension method.</span></span>
 
<span data-ttu-id="61ddc-332">Normalmente, la respuesta HTTP se almacena en búfer en una aplicación WebAssembly de Blazor para habilitar la compatibilidad con las lecturas de sincronización en el contenido de la respuesta.</span><span class="sxs-lookup"><span data-stu-id="61ddc-332">The HTTP response is typically buffered in a Blazor WebAssembly app to enable support for sync reads on the response content.</span></span> <span data-ttu-id="61ddc-333">Para habilitar la compatibilidad con la transmisión de respuestas, use el método de extensión `SetBrowserResponseStreamingEnabled` en la solicitud.</span><span class="sxs-lookup"><span data-stu-id="61ddc-333">To enable support for response streaming, use the `SetBrowserResponseStreamingEnabled` extension method on the request.</span></span>

<span data-ttu-id="61ddc-334">Para incluir credenciales en una solicitud entre orígenes, use el método de extensión `SetBrowserRequestCredentials`:</span><span class="sxs-lookup"><span data-stu-id="61ddc-334">To include credentials in a cross-origin request, use the `SetBrowserRequestCredentials` extension method:</span></span>

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

<span data-ttu-id="61ddc-335">Para obtener más información sobre las opciones de Fetch API, vea [Documentación web de MDN: WindowOrWorkerGlobalScope.fetch(): parámetros](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="61ddc-335">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

<span data-ttu-id="61ddc-336">Al enviar credenciales (cookies de autorización o encabezados) en solicitudes de CORS, la directiva de CORS debe permitir el encabezado `Authorization`.</span><span class="sxs-lookup"><span data-stu-id="61ddc-336">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="61ddc-337">En la directiva siguiente se incluye la configuración de:</span><span class="sxs-lookup"><span data-stu-id="61ddc-337">The following policy includes configuration for:</span></span>

* <span data-ttu-id="61ddc-338">Orígenes de la solicitud (`http://localhost:5000`, `https://localhost:5001`).</span><span class="sxs-lookup"><span data-stu-id="61ddc-338">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="61ddc-339">Cualquier método (verbo).</span><span class="sxs-lookup"><span data-stu-id="61ddc-339">Any method (verb).</span></span>
* <span data-ttu-id="61ddc-340">Los encabezados `Content-Type` y `Authorization`.</span><span class="sxs-lookup"><span data-stu-id="61ddc-340">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="61ddc-341">Para permitir un encabezado personalizado (por ejemplo, `x-custom-header`), enumere el encabezado al llamar a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span><span class="sxs-lookup"><span data-stu-id="61ddc-341">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="61ddc-342">Las credenciales establecidas por el código JavaScript del lado cliente (la propiedad `credentials` establecida en `include`).</span><span class="sxs-lookup"><span data-stu-id="61ddc-342">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="61ddc-343">Para obtener más información, vea <xref:security/cors> y el componente del evaluador de solicitudes HTTP de la aplicación de ejemplo (*Components/HTTPRequestTester.razor*).</span><span class="sxs-lookup"><span data-stu-id="61ddc-343">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (*Components/HTTPRequestTester.razor*).</span></span>

## <a name="handle-token-request-errors"></a><span data-ttu-id="61ddc-344">Controlar errores de solicitudes de token</span><span class="sxs-lookup"><span data-stu-id="61ddc-344">Handle token request errors</span></span>

<span data-ttu-id="61ddc-345">Cuando una aplicación de una sola página (SPA) autentica a un usuario mediante Open ID Connect (OIDC), el estado de autenticación se mantiene localmente en el SPA y en el Identity proveedor (IP) en forma de cookie de sesión que se establece como resultado del usuario que proporciona sus credenciales.</span><span class="sxs-lookup"><span data-stu-id="61ddc-345">When a Single Page Application (SPA) authenticates a user using Open ID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="61ddc-346">Normalmente, los tokens que emite la IP para el usuario son válidos durante breves períodos de tiempo, aproximadamente una hora, por lo que la aplicación cliente debe capturar nuevos tokens con regularidad.</span><span class="sxs-lookup"><span data-stu-id="61ddc-346">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="61ddc-347">De lo contrario, se cerrará la sesión del usuario después de que expiren los tokens concedidos.</span><span class="sxs-lookup"><span data-stu-id="61ddc-347">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="61ddc-348">En la mayoría de los casos, los clientes de OIDC pueden aprovisionar nuevos tokens sin necesidad de que el usuario se autentique de nuevo gracias al estado de autenticación o a la "sesión" que se mantiene dentro de la dirección IP.</span><span class="sxs-lookup"><span data-stu-id="61ddc-348">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="61ddc-349">Hay algunos casos en los que el cliente no puede obtener un token sin la interacción del usuario, por ejemplo, cuando por alguna razón el usuario cierra la sesión explícitamente desde la dirección IP.</span><span class="sxs-lookup"><span data-stu-id="61ddc-349">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="61ddc-350">Este escenario se produce si un usuario visita `https://login.microsoftonline.com` y cierra la sesión. En estos casos, la aplicación no sabe inmediatamente que el usuario ha cerrado la sesión. Es posible que el token que contiene el cliente ya no sea válido.</span><span class="sxs-lookup"><span data-stu-id="61ddc-350">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="61ddc-351">Además, el cliente no puede aprovisionar un token nuevo sin la interacción del usuario después de que expire el token actual.</span><span class="sxs-lookup"><span data-stu-id="61ddc-351">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="61ddc-352">Estos escenarios no son específicos de la autenticación basada en tokens.</span><span class="sxs-lookup"><span data-stu-id="61ddc-352">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="61ddc-353">Forman parte de la naturaleza de spa.</span><span class="sxs-lookup"><span data-stu-id="61ddc-353">They are part of the nature of SPAs.</span></span> <span data-ttu-id="61ddc-354">Un SPA que use cookies tampoco podrá llamar a una API de servidor si se quita la cookie de autenticación.</span><span class="sxs-lookup"><span data-stu-id="61ddc-354">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="61ddc-355">Cuando una aplicación realiza llamadas API a recursos protegidos, debe tener en cuenta lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="61ddc-355">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="61ddc-356">Para aprovisionar un nuevo token de acceso para llamar a la API, es posible que sea necesario volver a autenticar al usuario.</span><span class="sxs-lookup"><span data-stu-id="61ddc-356">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="61ddc-357">Incluso si el cliente tiene un token que parece ser válido, es posible que se produzca un error en la llamada al servidor porque el usuario revocó el token.</span><span class="sxs-lookup"><span data-stu-id="61ddc-357">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="61ddc-358">Cuando la aplicación solicita un token, hay dos resultados posibles:</span><span class="sxs-lookup"><span data-stu-id="61ddc-358">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="61ddc-359">La solicitud se realiza correctamente y la aplicación tiene un token válido.</span><span class="sxs-lookup"><span data-stu-id="61ddc-359">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="61ddc-360">Se produce un error en la solicitud y la aplicación debe autenticar al usuario de nuevo para obtener un nuevo token.</span><span class="sxs-lookup"><span data-stu-id="61ddc-360">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="61ddc-361">Cuando se produce un error en una solicitud de token, debe decidir si desea guardar el estado actual antes de realizar una redirección.</span><span class="sxs-lookup"><span data-stu-id="61ddc-361">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="61ddc-362">Existen varios enfoques con niveles crecientes de complejidad:</span><span class="sxs-lookup"><span data-stu-id="61ddc-362">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="61ddc-363">Almacenar el estado de la página actual en el almacenamiento de sesión.</span><span class="sxs-lookup"><span data-stu-id="61ddc-363">Store the current page state in session storage.</span></span> <span data-ttu-id="61ddc-364">Durante `OnInitializeAsync` , compruebe si el estado se puede restaurar antes de continuar.</span><span class="sxs-lookup"><span data-stu-id="61ddc-364">During `OnInitializeAsync`, check if state can be restored before continuing.</span></span>
* <span data-ttu-id="61ddc-365">Agregue un parámetro de cadena de consulta y úselo como una manera de señalar la aplicación que necesita para volver a hidratar el estado guardado anteriormente.</span><span class="sxs-lookup"><span data-stu-id="61ddc-365">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="61ddc-366">Agregue un parámetro de cadena de consulta con un identificador único para almacenar los datos en el almacenamiento de sesión sin poner en peligro las colisiones con otros elementos.</span><span class="sxs-lookup"><span data-stu-id="61ddc-366">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="61ddc-367">El ejemplo siguiente muestra cómo:</span><span class="sxs-lookup"><span data-stu-id="61ddc-367">The following example shows how to:</span></span>

* <span data-ttu-id="61ddc-368">Conservar el estado antes de redirigir a la página de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="61ddc-368">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="61ddc-369">Recupere el estado anterior después de la autenticación mediante el parámetro de cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="61ddc-369">Recover the previous state afterward authentication using the query string parameter.</span></span>

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

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="61ddc-370">Guardar el estado de la aplicación antes de una operación de autenticación</span><span class="sxs-lookup"><span data-stu-id="61ddc-370">Save app state before an authentication operation</span></span>

<span data-ttu-id="61ddc-371">Durante una operación de autenticación, hay casos en los que desea guardar el estado de la aplicación antes de que el explorador se redirija a la dirección IP.</span><span class="sxs-lookup"><span data-stu-id="61ddc-371">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="61ddc-372">Esto puede ser así cuando se usa algo como un contenedor de estado y se desea restaurar el estado después de que la autenticación se realice correctamente.</span><span class="sxs-lookup"><span data-stu-id="61ddc-372">This can be the case when you are using something like a state container and you want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="61ddc-373">Puede usar un objeto de estado de autenticación personalizado para conservar el estado específico de la aplicación o una referencia a él y restaurar ese estado una vez que la operación de autenticación se complete correctamente.</span><span class="sxs-lookup"><span data-stu-id="61ddc-373">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state once the authentication operation successfully completes.</span></span>

<span data-ttu-id="61ddc-374">`Authentication`componente (*páginas/autenticación. Razor*):</span><span class="sxs-lookup"><span data-stu-id="61ddc-374">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

## <a name="customize-app-routes"></a><span data-ttu-id="61ddc-375">Personalización de rutas de aplicación</span><span class="sxs-lookup"><span data-stu-id="61ddc-375">Customize app routes</span></span>

<span data-ttu-id="61ddc-376">De forma predeterminada, la `Microsoft.AspNetCore.Components.WebAssembly.Authentication` biblioteca utiliza las rutas que se muestran en la tabla siguiente para representar diferentes Estados de autenticación.</span><span class="sxs-lookup"><span data-stu-id="61ddc-376">By default, the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="61ddc-377">Ruta</span><span class="sxs-lookup"><span data-stu-id="61ddc-377">Route</span></span>                            | <span data-ttu-id="61ddc-378">Propósito</span><span class="sxs-lookup"><span data-stu-id="61ddc-378">Purpose</span></span> |
| ---
<span data-ttu-id="61ddc-379">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="61ddc-379">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="61ddc-380">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="61ddc-380">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61ddc-381">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-381">'Blazor'</span></span>
- <span data-ttu-id="61ddc-382">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61ddc-382">'Identity'</span></span>
- <span data-ttu-id="61ddc-383">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61ddc-383">'Let's Encrypt'</span></span>
- <span data-ttu-id="61ddc-384">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-384">'Razor'</span></span>
- <span data-ttu-id="61ddc-385">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="61ddc-385">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61ddc-386">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="61ddc-386">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="61ddc-387">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="61ddc-387">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61ddc-388">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-388">'Blazor'</span></span>
- <span data-ttu-id="61ddc-389">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61ddc-389">'Identity'</span></span>
- <span data-ttu-id="61ddc-390">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61ddc-390">'Let's Encrypt'</span></span>
- <span data-ttu-id="61ddc-391">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-391">'Razor'</span></span>
- <span data-ttu-id="61ddc-392">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="61ddc-392">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61ddc-393">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="61ddc-393">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="61ddc-394">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="61ddc-394">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61ddc-395">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-395">'Blazor'</span></span>
- <span data-ttu-id="61ddc-396">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61ddc-396">'Identity'</span></span>
- <span data-ttu-id="61ddc-397">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61ddc-397">'Let's Encrypt'</span></span>
- <span data-ttu-id="61ddc-398">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-398">'Razor'</span></span>
- <span data-ttu-id="61ddc-399">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="61ddc-399">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61ddc-400">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="61ddc-400">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="61ddc-401">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="61ddc-401">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61ddc-402">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-402">'Blazor'</span></span>
- <span data-ttu-id="61ddc-403">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61ddc-403">'Identity'</span></span>
- <span data-ttu-id="61ddc-404">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61ddc-404">'Let's Encrypt'</span></span>
- <span data-ttu-id="61ddc-405">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-405">'Razor'</span></span>
- <span data-ttu-id="61ddc-406">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="61ddc-406">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61ddc-407">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="61ddc-407">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="61ddc-408">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="61ddc-408">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61ddc-409">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-409">'Blazor'</span></span>
- <span data-ttu-id="61ddc-410">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61ddc-410">'Identity'</span></span>
- <span data-ttu-id="61ddc-411">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61ddc-411">'Let's Encrypt'</span></span>
- <span data-ttu-id="61ddc-412">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-412">'Razor'</span></span>
- <span data-ttu-id="61ddc-413">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="61ddc-413">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61ddc-414">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="61ddc-414">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="61ddc-415">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="61ddc-415">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61ddc-416">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-416">'Blazor'</span></span>
- <span data-ttu-id="61ddc-417">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61ddc-417">'Identity'</span></span>
- <span data-ttu-id="61ddc-418">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61ddc-418">'Let's Encrypt'</span></span>
- <span data-ttu-id="61ddc-419">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-419">'Razor'</span></span>
- <span data-ttu-id="61ddc-420">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="61ddc-420">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61ddc-421">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="61ddc-421">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="61ddc-422">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="61ddc-422">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61ddc-423">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-423">'Blazor'</span></span>
- <span data-ttu-id="61ddc-424">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61ddc-424">'Identity'</span></span>
- <span data-ttu-id="61ddc-425">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61ddc-425">'Let's Encrypt'</span></span>
- <span data-ttu-id="61ddc-426">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-426">'Razor'</span></span>
- <span data-ttu-id="61ddc-427">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="61ddc-427">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61ddc-428">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="61ddc-428">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="61ddc-429">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="61ddc-429">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61ddc-430">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-430">'Blazor'</span></span>
- <span data-ttu-id="61ddc-431">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61ddc-431">'Identity'</span></span>
- <span data-ttu-id="61ddc-432">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61ddc-432">'Let's Encrypt'</span></span>
- <span data-ttu-id="61ddc-433">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-433">'Razor'</span></span>
- <span data-ttu-id="61ddc-434">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="61ddc-434">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61ddc-435">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="61ddc-435">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="61ddc-436">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="61ddc-436">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61ddc-437">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-437">'Blazor'</span></span>
- <span data-ttu-id="61ddc-438">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61ddc-438">'Identity'</span></span>
- <span data-ttu-id="61ddc-439">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61ddc-439">'Let's Encrypt'</span></span>
- <span data-ttu-id="61ddc-440">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-440">'Razor'</span></span>
- <span data-ttu-id="61ddc-441">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="61ddc-441">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61ddc-442">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="61ddc-442">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="61ddc-443">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="61ddc-443">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61ddc-444">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-444">'Blazor'</span></span>
- <span data-ttu-id="61ddc-445">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61ddc-445">'Identity'</span></span>
- <span data-ttu-id="61ddc-446">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61ddc-446">'Let's Encrypt'</span></span>
- <span data-ttu-id="61ddc-447">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-447">'Razor'</span></span>
- <span data-ttu-id="61ddc-448">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="61ddc-448">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61ddc-449">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="61ddc-449">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="61ddc-450">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="61ddc-450">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61ddc-451">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-451">'Blazor'</span></span>
- <span data-ttu-id="61ddc-452">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61ddc-452">'Identity'</span></span>
- <span data-ttu-id="61ddc-453">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61ddc-453">'Let's Encrypt'</span></span>
- <span data-ttu-id="61ddc-454">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-454">'Razor'</span></span>
- <span data-ttu-id="61ddc-455">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="61ddc-455">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61ddc-456">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="61ddc-456">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="61ddc-457">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="61ddc-457">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61ddc-458">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-458">'Blazor'</span></span>
- <span data-ttu-id="61ddc-459">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61ddc-459">'Identity'</span></span>
- <span data-ttu-id="61ddc-460">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61ddc-460">'Let's Encrypt'</span></span>
- <span data-ttu-id="61ddc-461">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-461">'Razor'</span></span>
- <span data-ttu-id="61ddc-462">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="61ddc-462">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61ddc-463">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="61ddc-463">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="61ddc-464">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="61ddc-464">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61ddc-465">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-465">'Blazor'</span></span>
- <span data-ttu-id="61ddc-466">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61ddc-466">'Identity'</span></span>
- <span data-ttu-id="61ddc-467">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61ddc-467">'Let's Encrypt'</span></span>
- <span data-ttu-id="61ddc-468">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-468">'Razor'</span></span>
- <span data-ttu-id="61ddc-469">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="61ddc-469">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61ddc-470">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="61ddc-470">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="61ddc-471">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="61ddc-471">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61ddc-472">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-472">'Blazor'</span></span>
- <span data-ttu-id="61ddc-473">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61ddc-473">'Identity'</span></span>
- <span data-ttu-id="61ddc-474">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61ddc-474">'Let's Encrypt'</span></span>
- <span data-ttu-id="61ddc-475">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-475">'Razor'</span></span>
- <span data-ttu-id="61ddc-476">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="61ddc-476">'SignalR' uid:</span></span> 

<span data-ttu-id="61ddc-477">---------------- | ---title: "ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales" Author: Description: "Obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales".</span><span class="sxs-lookup"><span data-stu-id="61ddc-477">---------------- | --- title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="61ddc-478">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="61ddc-478">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61ddc-479">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-479">'Blazor'</span></span>
- <span data-ttu-id="61ddc-480">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61ddc-480">'Identity'</span></span>
- <span data-ttu-id="61ddc-481">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61ddc-481">'Let's Encrypt'</span></span>
- <span data-ttu-id="61ddc-482">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-482">'Razor'</span></span>
- <span data-ttu-id="61ddc-483">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="61ddc-483">'SignalR' uid:</span></span> 

<span data-ttu-id="61ddc-484">---- | | `authentication/login`           | Desencadena una operación de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="61ddc-484">---- | | `authentication/login`           | Triggers a sign-in operation.</span></span> <span data-ttu-id="61ddc-485">| | `authentication/login-callback`  | Controla el resultado de cualquier operación de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="61ddc-485">| | `authentication/login-callback`  | Handles the result of any sign-in operation.</span></span> <span data-ttu-id="61ddc-486">| | `authentication/login-failed`    | Muestra mensajes de error cuando se produce un error en la operación de inicio de sesión por algún motivo.</span><span class="sxs-lookup"><span data-stu-id="61ddc-486">| | `authentication/login-failed`    | Displays error messages when the sign-in operation fails for some reason.</span></span> <span data-ttu-id="61ddc-487">| | `authentication/logout`          | Desencadena una operación de cierre de sesión.</span><span class="sxs-lookup"><span data-stu-id="61ddc-487">| | `authentication/logout`          | Triggers a sign-out operation.</span></span> <span data-ttu-id="61ddc-488">| | `authentication/logout-callback` | Controla el resultado de una operación de cierre de sesión.</span><span class="sxs-lookup"><span data-stu-id="61ddc-488">| | `authentication/logout-callback` | Handles the result of a sign-out operation.</span></span> <span data-ttu-id="61ddc-489">| | `authentication/logout-failed`   | Muestra mensajes de error cuando se produce un error en la operación de cierre de sesión por algún motivo.</span><span class="sxs-lookup"><span data-stu-id="61ddc-489">| | `authentication/logout-failed`   | Displays error messages when the sign-out operation fails for some reason.</span></span> <span data-ttu-id="61ddc-490">| | `authentication/logged-out`      | Indica que el usuario ha cerrado la sesión correctamente.</span><span class="sxs-lookup"><span data-stu-id="61ddc-490">| | `authentication/logged-out`      | Indicates that the user has successfully logout.</span></span> <span data-ttu-id="61ddc-491">| | `authentication/profile`         | Desencadena una operación para editar el perfil de usuario.</span><span class="sxs-lookup"><span data-stu-id="61ddc-491">| | `authentication/profile`         | Triggers an operation to edit the user profile.</span></span> <span data-ttu-id="61ddc-492">| | `authentication/register`        | Desencadena una operación para registrar un nuevo usuario.</span><span class="sxs-lookup"><span data-stu-id="61ddc-492">| | `authentication/register`        | Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="61ddc-493">Las rutas que se muestran en la tabla anterior se pueden configurar mediante `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths` .</span><span class="sxs-lookup"><span data-stu-id="61ddc-493">The routes shown in the preceding table are configurable via `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`.</span></span> <span data-ttu-id="61ddc-494">Al establecer las opciones para proporcionar rutas personalizadas, confirme que la aplicación tiene una ruta que controla cada ruta de acceso.</span><span class="sxs-lookup"><span data-stu-id="61ddc-494">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="61ddc-495">En el ejemplo siguiente, todas las rutas de acceso tienen el prefijo `/security` .</span><span class="sxs-lookup"><span data-stu-id="61ddc-495">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="61ddc-496">`Authentication`componente (*páginas/autenticación. Razor*):</span><span class="sxs-lookup"><span data-stu-id="61ddc-496">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="61ddc-497">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="61ddc-497">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="61ddc-498">Si el requisito requiere rutas de acceso completamente diferentes, establezca las rutas como se describió anteriormente y represente el `RemoteAuthenticatorView` con un parámetro de acción explícito:</span><span class="sxs-lookup"><span data-stu-id="61ddc-498">If the requirement calls for completely different paths, set the routes as described previously and render the `RemoteAuthenticatorView` with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="61ddc-499">Puede descomponer la interfaz de usuario en diferentes páginas si decide hacerlo.</span><span class="sxs-lookup"><span data-stu-id="61ddc-499">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="61ddc-500">Personalización de la interfaz de usuario de autenticación</span><span class="sxs-lookup"><span data-stu-id="61ddc-500">Customize the authentication user interface</span></span>

<span data-ttu-id="61ddc-501">`RemoteAuthenticatorView`incluye un conjunto predeterminado de partes de la interfaz de usuario para cada estado de autenticación.</span><span class="sxs-lookup"><span data-stu-id="61ddc-501">`RemoteAuthenticatorView` includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="61ddc-502">Cada Estado se puede personalizar pasando un personalizado `RenderFragment` .</span><span class="sxs-lookup"><span data-stu-id="61ddc-502">Each state can be customized by passing in a custom `RenderFragment`.</span></span> <span data-ttu-id="61ddc-503">Para personalizar el texto mostrado durante el proceso de inicio de sesión inicial, puede cambiar el de la `RemoteAuthenticatorView` manera siguiente.</span><span class="sxs-lookup"><span data-stu-id="61ddc-503">To customize the displayed text during the initial login process, can change the `RemoteAuthenticatorView` as follows.</span></span>

<span data-ttu-id="61ddc-504">`Authentication`componente (*páginas/autenticación. Razor*):</span><span class="sxs-lookup"><span data-stu-id="61ddc-504">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

<span data-ttu-id="61ddc-505">`RemoteAuthenticatorView`Tiene un fragmento que se puede utilizar por cada ruta de autenticación que se muestra en la tabla siguiente.</span><span class="sxs-lookup"><span data-stu-id="61ddc-505">The `RemoteAuthenticatorView` has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="61ddc-506">Ruta</span><span class="sxs-lookup"><span data-stu-id="61ddc-506">Route</span></span>                            | <span data-ttu-id="61ddc-507">Fragmento</span><span class="sxs-lookup"><span data-stu-id="61ddc-507">Fragment</span></span>                |
| ---
<span data-ttu-id="61ddc-508">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="61ddc-508">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="61ddc-509">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="61ddc-509">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61ddc-510">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-510">'Blazor'</span></span>
- <span data-ttu-id="61ddc-511">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61ddc-511">'Identity'</span></span>
- <span data-ttu-id="61ddc-512">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61ddc-512">'Let's Encrypt'</span></span>
- <span data-ttu-id="61ddc-513">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-513">'Razor'</span></span>
- <span data-ttu-id="61ddc-514">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="61ddc-514">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61ddc-515">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="61ddc-515">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="61ddc-516">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="61ddc-516">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61ddc-517">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-517">'Blazor'</span></span>
- <span data-ttu-id="61ddc-518">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61ddc-518">'Identity'</span></span>
- <span data-ttu-id="61ddc-519">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61ddc-519">'Let's Encrypt'</span></span>
- <span data-ttu-id="61ddc-520">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-520">'Razor'</span></span>
- <span data-ttu-id="61ddc-521">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="61ddc-521">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61ddc-522">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="61ddc-522">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="61ddc-523">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="61ddc-523">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61ddc-524">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-524">'Blazor'</span></span>
- <span data-ttu-id="61ddc-525">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61ddc-525">'Identity'</span></span>
- <span data-ttu-id="61ddc-526">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61ddc-526">'Let's Encrypt'</span></span>
- <span data-ttu-id="61ddc-527">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-527">'Razor'</span></span>
- <span data-ttu-id="61ddc-528">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="61ddc-528">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61ddc-529">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="61ddc-529">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="61ddc-530">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="61ddc-530">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61ddc-531">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-531">'Blazor'</span></span>
- <span data-ttu-id="61ddc-532">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61ddc-532">'Identity'</span></span>
- <span data-ttu-id="61ddc-533">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61ddc-533">'Let's Encrypt'</span></span>
- <span data-ttu-id="61ddc-534">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-534">'Razor'</span></span>
- <span data-ttu-id="61ddc-535">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="61ddc-535">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61ddc-536">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="61ddc-536">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="61ddc-537">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="61ddc-537">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61ddc-538">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-538">'Blazor'</span></span>
- <span data-ttu-id="61ddc-539">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61ddc-539">'Identity'</span></span>
- <span data-ttu-id="61ddc-540">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61ddc-540">'Let's Encrypt'</span></span>
- <span data-ttu-id="61ddc-541">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-541">'Razor'</span></span>
- <span data-ttu-id="61ddc-542">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="61ddc-542">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61ddc-543">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="61ddc-543">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="61ddc-544">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="61ddc-544">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61ddc-545">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-545">'Blazor'</span></span>
- <span data-ttu-id="61ddc-546">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61ddc-546">'Identity'</span></span>
- <span data-ttu-id="61ddc-547">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61ddc-547">'Let's Encrypt'</span></span>
- <span data-ttu-id="61ddc-548">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-548">'Razor'</span></span>
- <span data-ttu-id="61ddc-549">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="61ddc-549">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61ddc-550">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="61ddc-550">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="61ddc-551">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="61ddc-551">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61ddc-552">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-552">'Blazor'</span></span>
- <span data-ttu-id="61ddc-553">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61ddc-553">'Identity'</span></span>
- <span data-ttu-id="61ddc-554">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61ddc-554">'Let's Encrypt'</span></span>
- <span data-ttu-id="61ddc-555">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-555">'Razor'</span></span>
- <span data-ttu-id="61ddc-556">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="61ddc-556">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61ddc-557">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="61ddc-557">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="61ddc-558">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="61ddc-558">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61ddc-559">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-559">'Blazor'</span></span>
- <span data-ttu-id="61ddc-560">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61ddc-560">'Identity'</span></span>
- <span data-ttu-id="61ddc-561">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61ddc-561">'Let's Encrypt'</span></span>
- <span data-ttu-id="61ddc-562">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-562">'Razor'</span></span>
- <span data-ttu-id="61ddc-563">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="61ddc-563">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61ddc-564">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="61ddc-564">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="61ddc-565">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="61ddc-565">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61ddc-566">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-566">'Blazor'</span></span>
- <span data-ttu-id="61ddc-567">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61ddc-567">'Identity'</span></span>
- <span data-ttu-id="61ddc-568">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61ddc-568">'Let's Encrypt'</span></span>
- <span data-ttu-id="61ddc-569">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-569">'Razor'</span></span>
- <span data-ttu-id="61ddc-570">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="61ddc-570">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61ddc-571">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="61ddc-571">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="61ddc-572">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="61ddc-572">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61ddc-573">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-573">'Blazor'</span></span>
- <span data-ttu-id="61ddc-574">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61ddc-574">'Identity'</span></span>
- <span data-ttu-id="61ddc-575">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61ddc-575">'Let's Encrypt'</span></span>
- <span data-ttu-id="61ddc-576">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-576">'Razor'</span></span>
- <span data-ttu-id="61ddc-577">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="61ddc-577">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61ddc-578">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="61ddc-578">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="61ddc-579">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="61ddc-579">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61ddc-580">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-580">'Blazor'</span></span>
- <span data-ttu-id="61ddc-581">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61ddc-581">'Identity'</span></span>
- <span data-ttu-id="61ddc-582">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61ddc-582">'Let's Encrypt'</span></span>
- <span data-ttu-id="61ddc-583">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-583">'Razor'</span></span>
- <span data-ttu-id="61ddc-584">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="61ddc-584">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61ddc-585">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="61ddc-585">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="61ddc-586">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="61ddc-586">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61ddc-587">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-587">'Blazor'</span></span>
- <span data-ttu-id="61ddc-588">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61ddc-588">'Identity'</span></span>
- <span data-ttu-id="61ddc-589">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61ddc-589">'Let's Encrypt'</span></span>
- <span data-ttu-id="61ddc-590">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-590">'Razor'</span></span>
- <span data-ttu-id="61ddc-591">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="61ddc-591">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61ddc-592">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="61ddc-592">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="61ddc-593">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="61ddc-593">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61ddc-594">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-594">'Blazor'</span></span>
- <span data-ttu-id="61ddc-595">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61ddc-595">'Identity'</span></span>
- <span data-ttu-id="61ddc-596">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61ddc-596">'Let's Encrypt'</span></span>
- <span data-ttu-id="61ddc-597">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-597">'Razor'</span></span>
- <span data-ttu-id="61ddc-598">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="61ddc-598">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61ddc-599">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="61ddc-599">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="61ddc-600">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="61ddc-600">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61ddc-601">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-601">'Blazor'</span></span>
- <span data-ttu-id="61ddc-602">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61ddc-602">'Identity'</span></span>
- <span data-ttu-id="61ddc-603">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61ddc-603">'Let's Encrypt'</span></span>
- <span data-ttu-id="61ddc-604">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-604">'Razor'</span></span>
- <span data-ttu-id="61ddc-605">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="61ddc-605">'SignalR' uid:</span></span> 

<span data-ttu-id="61ddc-606">---------------- | ---title: "ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales" Author: Description: "Obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales".</span><span class="sxs-lookup"><span data-stu-id="61ddc-606">---------------- | --- title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="61ddc-607">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="61ddc-607">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61ddc-608">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-608">'Blazor'</span></span>
- <span data-ttu-id="61ddc-609">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61ddc-609">'Identity'</span></span>
- <span data-ttu-id="61ddc-610">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61ddc-610">'Let's Encrypt'</span></span>
- <span data-ttu-id="61ddc-611">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-611">'Razor'</span></span>
- <span data-ttu-id="61ddc-612">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="61ddc-612">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61ddc-613">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="61ddc-613">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="61ddc-614">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="61ddc-614">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61ddc-615">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-615">'Blazor'</span></span>
- <span data-ttu-id="61ddc-616">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61ddc-616">'Identity'</span></span>
- <span data-ttu-id="61ddc-617">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61ddc-617">'Let's Encrypt'</span></span>
- <span data-ttu-id="61ddc-618">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-618">'Razor'</span></span>
- <span data-ttu-id="61ddc-619">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="61ddc-619">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61ddc-620">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="61ddc-620">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="61ddc-621">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="61ddc-621">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61ddc-622">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-622">'Blazor'</span></span>
- <span data-ttu-id="61ddc-623">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61ddc-623">'Identity'</span></span>
- <span data-ttu-id="61ddc-624">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61ddc-624">'Let's Encrypt'</span></span>
- <span data-ttu-id="61ddc-625">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-625">'Razor'</span></span>
- <span data-ttu-id="61ddc-626">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="61ddc-626">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61ddc-627">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="61ddc-627">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="61ddc-628">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="61ddc-628">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61ddc-629">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-629">'Blazor'</span></span>
- <span data-ttu-id="61ddc-630">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61ddc-630">'Identity'</span></span>
- <span data-ttu-id="61ddc-631">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61ddc-631">'Let's Encrypt'</span></span>
- <span data-ttu-id="61ddc-632">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-632">'Razor'</span></span>
- <span data-ttu-id="61ddc-633">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="61ddc-633">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61ddc-634">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="61ddc-634">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="61ddc-635">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="61ddc-635">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61ddc-636">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-636">'Blazor'</span></span>
- <span data-ttu-id="61ddc-637">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61ddc-637">'Identity'</span></span>
- <span data-ttu-id="61ddc-638">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61ddc-638">'Let's Encrypt'</span></span>
- <span data-ttu-id="61ddc-639">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-639">'Razor'</span></span>
- <span data-ttu-id="61ddc-640">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="61ddc-640">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61ddc-641">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="61ddc-641">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="61ddc-642">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="61ddc-642">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61ddc-643">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-643">'Blazor'</span></span>
- <span data-ttu-id="61ddc-644">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61ddc-644">'Identity'</span></span>
- <span data-ttu-id="61ddc-645">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61ddc-645">'Let's Encrypt'</span></span>
- <span data-ttu-id="61ddc-646">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-646">'Razor'</span></span>
- <span data-ttu-id="61ddc-647">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="61ddc-647">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61ddc-648">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="61ddc-648">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="61ddc-649">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="61ddc-649">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61ddc-650">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-650">'Blazor'</span></span>
- <span data-ttu-id="61ddc-651">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61ddc-651">'Identity'</span></span>
- <span data-ttu-id="61ddc-652">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61ddc-652">'Let's Encrypt'</span></span>
- <span data-ttu-id="61ddc-653">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-653">'Razor'</span></span>
- <span data-ttu-id="61ddc-654">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="61ddc-654">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61ddc-655">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="61ddc-655">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="61ddc-656">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="61ddc-656">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61ddc-657">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-657">'Blazor'</span></span>
- <span data-ttu-id="61ddc-658">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61ddc-658">'Identity'</span></span>
- <span data-ttu-id="61ddc-659">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61ddc-659">'Let's Encrypt'</span></span>
- <span data-ttu-id="61ddc-660">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-660">'Razor'</span></span>
- <span data-ttu-id="61ddc-661">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="61ddc-661">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61ddc-662">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="61ddc-662">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="61ddc-663">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="61ddc-663">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61ddc-664">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-664">'Blazor'</span></span>
- <span data-ttu-id="61ddc-665">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61ddc-665">'Identity'</span></span>
- <span data-ttu-id="61ddc-666">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61ddc-666">'Let's Encrypt'</span></span>
- <span data-ttu-id="61ddc-667">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61ddc-667">'Razor'</span></span>
- <span data-ttu-id="61ddc-668">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="61ddc-668">'SignalR' uid:</span></span> 

<span data-ttu-id="61ddc-669">------------ | | `authentication/login`           | `<LoggingIn>`           | | `authentication/login-callback`  | `<CompletingLoggingIn>` | | `authentication/login-failed`    | `<LogInFailed>`         | | `authentication/logout`          | `<LogOut>`              | | `authentication/logout-callback` | `<CompletingLogOut>`    | | `authentication/logout-failed`   | `<LogOutFailed>`        | | `authentication/logged-out`      | `<LogOutSucceeded>`     | | `authentication/profile`         | `<UserProfile>`         | | `authentication/register`        | `<Registering>`         |</span><span class="sxs-lookup"><span data-stu-id="61ddc-669">------------ | | `authentication/login`           | `<LoggingIn>`           | | `authentication/login-callback`  | `<CompletingLoggingIn>` | | `authentication/login-failed`    | `<LogInFailed>`         | | `authentication/logout`          | `<LogOut>`              | | `authentication/logout-callback` | `<CompletingLogOut>`    | | `authentication/logout-failed`   | `<LogOutFailed>`        | | `authentication/logged-out`      | `<LogOutSucceeded>`     | | `authentication/profile`         | `<UserProfile>`         | | `authentication/register`        | `<Registering>`         |</span></span>

## <a name="customize-the-user"></a><span data-ttu-id="61ddc-670">Personalización del usuario</span><span class="sxs-lookup"><span data-stu-id="61ddc-670">Customize the user</span></span>

<span data-ttu-id="61ddc-671">Los usuarios enlazados a la aplicación se pueden personalizar.</span><span class="sxs-lookup"><span data-stu-id="61ddc-671">Users bound to the app can be customized.</span></span> <span data-ttu-id="61ddc-672">En el ejemplo siguiente, todos los usuarios autenticados reciben una `amr` demanda por cada uno de los métodos de autenticación del usuario.</span><span class="sxs-lookup"><span data-stu-id="61ddc-672">In the following example, all authenticated users receive an `amr` claim for each of the user's authentication methods.</span></span>

<span data-ttu-id="61ddc-673">Cree una clase que extienda la `RemoteUserAccount` clase:</span><span class="sxs-lookup"><span data-stu-id="61ddc-673">Create a class that extends the `RemoteUserAccount` class:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

<span data-ttu-id="61ddc-674">Cree una fábrica que extienda `AccountClaimsPrincipalFactory<TAccount>` :</span><span class="sxs-lookup"><span data-stu-id="61ddc-674">Create a factory that extends `AccountClaimsPrincipalFactory<TAccount>`:</span></span>

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

<span data-ttu-id="61ddc-675">Registre `CustomAccountFactory` para el proveedor de autenticación en uso.</span><span class="sxs-lookup"><span data-stu-id="61ddc-675">Register the `CustomAccountFactory` for the authentication provider in use.</span></span> <span data-ttu-id="61ddc-676">Cualquiera de los siguientes registros son válidos:</span><span class="sxs-lookup"><span data-stu-id="61ddc-676">Any of the following registrations are valid:</span></span> 

* <span data-ttu-id="61ddc-677">`AddOidcAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="61ddc-677">`AddOidcAuthentication`:</span></span>

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

* <span data-ttu-id="61ddc-678">`AddMsalAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="61ddc-678">`AddMsalAuthentication`:</span></span>

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
  
* <span data-ttu-id="61ddc-679">`AddApiAuthorization`:</span><span class="sxs-lookup"><span data-stu-id="61ddc-679">`AddApiAuthorization`:</span></span>

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

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="61ddc-680">Compatibilidad de la representación previa con la autenticación</span><span class="sxs-lookup"><span data-stu-id="61ddc-680">Support prerendering with authentication</span></span>

<span data-ttu-id="61ddc-681">Después de seguir las instrucciones que aparecen en uno de los temas de la aplicación WebAssembly de Blazor hospedada, use estas instrucciones para crear una aplicación que:</span><span class="sxs-lookup"><span data-stu-id="61ddc-681">After following the guidance in one of the hosted Blazor WebAssembly app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="61ddc-682">Representa previamente las rutas de acceso para las que no se requiere autorización.</span><span class="sxs-lookup"><span data-stu-id="61ddc-682">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="61ddc-683">No representa previamente las rutas de acceso para las que se requiere autorización.</span><span class="sxs-lookup"><span data-stu-id="61ddc-683">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="61ddc-684">En la clase `Program` de la aplicación cliente (*Program.cs*), factorice los registros de servicio comunes en un método independiente (por ejemplo, `ConfigureCommonServices`):</span><span class="sxs-lookup"><span data-stu-id="61ddc-684">In the Client app's `Program` class (*Program.cs*), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

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

<span data-ttu-id="61ddc-685">En `Startup.ConfigureServices` de la aplicación de servidor, registre estos servicios adicionales:</span><span class="sxs-lookup"><span data-stu-id="61ddc-685">In the Server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

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

<span data-ttu-id="61ddc-686">En el método `Startup.Configure` de la aplicación de servidor, reemplace `endpoints.MapFallbackToFile("index.html")` por `endpoints.MapFallbackToPage("/_Host")`:</span><span class="sxs-lookup"><span data-stu-id="61ddc-686">In the Server app's `Startup.Configure` method, replace `endpoints.MapFallbackToFile("index.html")` with `endpoints.MapFallbackToPage("/_Host")`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="61ddc-687">En la aplicación de servidor, cree una carpeta *Pages* si todavía no existe.</span><span class="sxs-lookup"><span data-stu-id="61ddc-687">In the Server app, create a *Pages* folder if it doesn't exist.</span></span> <span data-ttu-id="61ddc-688">Cree una página *_Host.cshtml* dentro de la carpeta *Pages* de la aplicación de servidor.</span><span class="sxs-lookup"><span data-stu-id="61ddc-688">Create a *_Host.cshtml* page inside the Server app's *Pages* folder.</span></span> <span data-ttu-id="61ddc-689">Pegue el contenido del archivo *wwwroot/index.html* de la aplicación cliente en el archivo *Pages/_Host.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="61ddc-689">Paste the contents from the Client app's *wwwroot/index.html* file into the *Pages/_Host.cshtml* file.</span></span> <span data-ttu-id="61ddc-690">Actualice el contenido del archivo:</span><span class="sxs-lookup"><span data-stu-id="61ddc-690">Update the file's contents:</span></span>

* <span data-ttu-id="61ddc-691">Agregue `@page "_Host"` a la parte superior del archivo.</span><span class="sxs-lookup"><span data-stu-id="61ddc-691">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="61ddc-692">Reemplace la etiqueta `<app>Loading...</app>` por la siguiente:</span><span class="sxs-lookup"><span data-stu-id="61ddc-692">Replace the `<app>Loading...</app>` tag with the following:</span></span>

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
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="61ddc-693">Opciones para aplicaciones hospedadas y proveedores de inicio de sesión de terceros</span><span class="sxs-lookup"><span data-stu-id="61ddc-693">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="61ddc-694">Al autenticar y autorizar una aplicación WebAssembly de Blazor con un proveedor de terceros, hay varias opciones disponibles para autenticar al usuario.</span><span class="sxs-lookup"><span data-stu-id="61ddc-694">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="61ddc-695">Su elección depende del escenario.</span><span class="sxs-lookup"><span data-stu-id="61ddc-695">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="61ddc-696">Para obtener más información, vea <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="61ddc-696">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="61ddc-697">Autenticación de usuarios solo para llamadas a las API de terceros protegidas</span><span class="sxs-lookup"><span data-stu-id="61ddc-697">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="61ddc-698">Autentique al usuario con un flujo de OAuth del lado cliente en el proveedor de la API de terceros:</span><span class="sxs-lookup"><span data-stu-id="61ddc-698">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="61ddc-699">En este escenario:</span><span class="sxs-lookup"><span data-stu-id="61ddc-699">In this scenario:</span></span>

* <span data-ttu-id="61ddc-700">El servidor que hospeda la aplicación no desempeña ningún rol.</span><span class="sxs-lookup"><span data-stu-id="61ddc-700">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="61ddc-701">No se pueden proteger las API en el servidor.</span><span class="sxs-lookup"><span data-stu-id="61ddc-701">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="61ddc-702">La aplicación solo puede llamar a las API de terceros protegidas.</span><span class="sxs-lookup"><span data-stu-id="61ddc-702">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="61ddc-703">Autenticación de usuarios con un proveedor de terceros y llamada a las API protegidas en el servidor host y en el tercero</span><span class="sxs-lookup"><span data-stu-id="61ddc-703">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="61ddc-704">Configure Identity con un proveedor de inicio de sesión de terceros.</span><span class="sxs-lookup"><span data-stu-id="61ddc-704">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="61ddc-705">Obtenga los tokens necesarios para el acceso de API de terceros y almacénelos.</span><span class="sxs-lookup"><span data-stu-id="61ddc-705">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="61ddc-706">Cuando un usuario inicia sesión, Identity recopila tokens de acceso y actualización como parte del proceso de autenticación.</span><span class="sxs-lookup"><span data-stu-id="61ddc-706">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="61ddc-707">En ese momento, hay un par de enfoques disponibles para realizar llamadas API a las API de terceros.</span><span class="sxs-lookup"><span data-stu-id="61ddc-707">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="61ddc-708">Uso de un token de acceso de servidor para recuperar el token de acceso de terceros</span><span class="sxs-lookup"><span data-stu-id="61ddc-708">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="61ddc-709">Use el token de acceso generado en el servidor para recuperar el token de acceso de terceros de un punto de conexión de la API de servidor.</span><span class="sxs-lookup"><span data-stu-id="61ddc-709">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="61ddc-710">Desde allí, use el token de acceso de terceros para llamar a recursos de API de terceros directamente desde Identity en el cliente.</span><span class="sxs-lookup"><span data-stu-id="61ddc-710">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="61ddc-711">Este enfoque no se recomienda.</span><span class="sxs-lookup"><span data-stu-id="61ddc-711">We don't recommend this approach.</span></span> <span data-ttu-id="61ddc-712">Este enfoque requiere tratar el token de acceso de terceros como si se hubiera generado para un cliente público.</span><span class="sxs-lookup"><span data-stu-id="61ddc-712">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="61ddc-713">En términos de OAuth, la aplicación pública no tiene un secreto de cliente porque no es de confianza para almacenar secretos de forma segura y el token de acceso se genera para un cliente confidencial.</span><span class="sxs-lookup"><span data-stu-id="61ddc-713">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="61ddc-714">Un cliente confidencial es un cliente que tiene un secreto de cliente y se supone que puede almacenar secretos de forma segura.</span><span class="sxs-lookup"><span data-stu-id="61ddc-714">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="61ddc-715">El token de acceso de terceros podría recibir ámbitos adicionales para realizar operaciones confidenciales en función del hecho de que la tercera parte emitió el token para un cliente de más confianza.</span><span class="sxs-lookup"><span data-stu-id="61ddc-715">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="61ddc-716">Del mismo modo, los tokens de actualización no deben emitirse para un cliente que no sea de confianza, ya que esto proporciona acceso ilimitado al cliente a menos que se apliquen otras restricciones.</span><span class="sxs-lookup"><span data-stu-id="61ddc-716">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="61ddc-717">Realización de llamadas API desde el cliente a la API de servidor para llamar a las API de terceros</span><span class="sxs-lookup"><span data-stu-id="61ddc-717">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="61ddc-718">Realice una llamada API desde el cliente a la API del servidor.</span><span class="sxs-lookup"><span data-stu-id="61ddc-718">Make an API call from the client to the server API.</span></span> <span data-ttu-id="61ddc-719">En el servidor, recupere el token de acceso para el recurso de la API de terceros y emita cualquier llamada que sea necesaria.</span><span class="sxs-lookup"><span data-stu-id="61ddc-719">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="61ddc-720">Aunque este enfoque requiere un salto de red adicional a través del servidor para llamar a una API de terceros, en última instancia resulta una experiencia más segura:</span><span class="sxs-lookup"><span data-stu-id="61ddc-720">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="61ddc-721">El servidor puede almacenar tokens de actualización y asegurarse de que la aplicación no pierde el acceso a recursos de terceros.</span><span class="sxs-lookup"><span data-stu-id="61ddc-721">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="61ddc-722">La aplicación no puede filtrar los tokens de acceso del servidor que puedan contener permisos más confidenciales.</span><span class="sxs-lookup"><span data-stu-id="61ddc-722">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>

## <a name="use-open-id-connect-oidc-v20-endpoints"></a><span data-ttu-id="61ddc-723">Usar puntos de conexión v 2.0 de Open ID Connect (OIDC)</span><span class="sxs-lookup"><span data-stu-id="61ddc-723">Use Open ID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="61ddc-724">La biblioteca de autenticación y Blazor las plantillas usan puntos de conexión de Open ID Connect (OIDC) v 1.0.</span><span class="sxs-lookup"><span data-stu-id="61ddc-724">The authentication library and Blazor templates use Open ID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="61ddc-725">Para usar un punto de conexión v 2.0, configure la <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> opción portador JWT.</span><span class="sxs-lookup"><span data-stu-id="61ddc-725">To use a v2.0 endpoint, configure the JWT Bearer <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> option.</span></span> <span data-ttu-id="61ddc-726">En el ejemplo siguiente, AAD se configura para v 2.0 anexando un `v2.0` segmento a la `Authority` propiedad:</span><span class="sxs-lookup"><span data-stu-id="61ddc-726">In the following example, AAD is configured for v2.0 by appending a `v2.0` segment to the `Authority` property:</span></span>

```csharp
builder.Services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    });
```

<span data-ttu-id="61ddc-727">Como alternativa, la configuración se puede realizar en el archivo de configuración de la aplicación (*appSettings. JSON*):</span><span class="sxs-lookup"><span data-stu-id="61ddc-727">Alternatively, the setting can be made in the app settings (*appsettings.json*) file:</span></span>

```json
{
  "Local": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="61ddc-728">Si el seguimiento de un segmento a la autoridad no es apropiado para el proveedor de OIDC de la aplicación, como con proveedores que no son de AAD, establezca la `Authority` propiedad directamente.</span><span class="sxs-lookup"><span data-stu-id="61ddc-728">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the `Authority` property directly.</span></span> <span data-ttu-id="61ddc-729">Establezca la propiedad en `JwtBearerOptions` o en el archivo de configuración de la aplicación con la `Authority` clave.</span><span class="sxs-lookup"><span data-stu-id="61ddc-729">Either set the property in `JwtBearerOptions` or in the app settings file with the `Authority` key.</span></span>

<span data-ttu-id="61ddc-730">La lista de notificaciones en el token de identificador cambia para los puntos de conexión v 2.0.</span><span class="sxs-lookup"><span data-stu-id="61ddc-730">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="61ddc-731">Para obtener más información, consulte [¿por qué actualizar a Microsoft Identity Platform (v 2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span><span class="sxs-lookup"><span data-stu-id="61ddc-731">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span></span>
