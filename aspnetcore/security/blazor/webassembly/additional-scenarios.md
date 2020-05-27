---
<span data-ttu-id="ebe18-101">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="ebe18-101">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="ebe18-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebe18-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ebe18-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebe18-103">'Blazor'</span></span>
- <span data-ttu-id="ebe18-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebe18-104">'Identity'</span></span>
- <span data-ttu-id="ebe18-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebe18-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="ebe18-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebe18-106">'Razor'</span></span>
- <span data-ttu-id="ebe18-107">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebe18-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="ebe18-108">ASP.NET Core Blazor escenarios de seguridad adicionales de Webassembly</span><span class="sxs-lookup"><span data-stu-id="ebe18-108">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="ebe18-109">Por [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="ebe18-109">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

## <a name="attach-tokens-to-outgoing-requests"></a><span data-ttu-id="ebe18-110">Adjuntar tokens a solicitudes salientes</span><span class="sxs-lookup"><span data-stu-id="ebe18-110">Attach tokens to outgoing requests</span></span>

<span data-ttu-id="ebe18-111">El <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> servicio se puede usar con <xref:System.Net.Http.HttpClient> para adjuntar tokens de acceso a las solicitudes salientes.</span><span class="sxs-lookup"><span data-stu-id="ebe18-111">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> service can be used with <xref:System.Net.Http.HttpClient> to attach access tokens to outgoing requests.</span></span> <span data-ttu-id="ebe18-112">Los tokens se adquieren con el <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> servicio existente.</span><span class="sxs-lookup"><span data-stu-id="ebe18-112">Tokens are acquired using the existing <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> service.</span></span> <span data-ttu-id="ebe18-113">Si no se puede adquirir un token, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> se produce una excepción.</span><span class="sxs-lookup"><span data-stu-id="ebe18-113">If a token can't be acquired, an <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> is thrown.</span></span> <span data-ttu-id="ebe18-114"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException>tiene un <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> método que se puede utilizar para navegar por el usuario al proveedor de identidades para adquirir un nuevo token.</span><span class="sxs-lookup"><span data-stu-id="ebe18-114"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> has a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> method that can be used to navigate the user to the identity provider to acquire a new token.</span></span> <span data-ttu-id="ebe18-115"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler>Se puede configurar con las direcciones URL autorizadas, los ámbitos y la dirección URL de retorno mediante el <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> método.</span><span class="sxs-lookup"><span data-stu-id="ebe18-115">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> can be configured with the authorized URLs, scopes, and return URL using the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> method.</span></span>

<span data-ttu-id="ebe18-116">En el ejemplo siguiente, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> configura <xref:System.Net.Http.HttpClient> en `Program.Main` (*Program.CS*):</span><span class="sxs-lookup"><span data-stu-id="ebe18-116">In the following example, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> configures an <xref:System.Net.Http.HttpClient> in `Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="ebe18-117">Para mayor comodidad, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> se incluye una que está preconfigurada con la dirección base de la aplicación como una dirección URL autorizada.</span><span class="sxs-lookup"><span data-stu-id="ebe18-117">For convenience, a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> is included that's preconfigured with the app base address as an authorized URL.</span></span> <span data-ttu-id="ebe18-118">Las Blazor plantillas de Webassembly habilitadas para la autenticación ahora <xref:System.Net.Http.IHttpClientFactory> se usan en el proyecto de API de servidor para configurar una <xref:System.Net.Http.HttpClient> con <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> :</span><span class="sxs-lookup"><span data-stu-id="ebe18-118">The authentication-enabled Blazor WebAssembly templates now use <xref:System.Net.Http.IHttpClientFactory> in the Server API project to set up an <xref:System.Net.Http.HttpClient> with the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler>:</span></span>

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

<span data-ttu-id="ebe18-119">Cuando el cliente se crea con <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> en el ejemplo anterior, <xref:System.Net.Http.HttpClient> se proporcionan instancias de que incluyen tokens de acceso al hacer solicitudes al proyecto de servidor.</span><span class="sxs-lookup"><span data-stu-id="ebe18-119">Where the client is created with <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> in the preceding example, the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="ebe18-120"><xref:System.Net.Http.HttpClient>A continuación, se usa el configurado para realizar solicitudes autorizadas mediante un patrón [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) simple.</span><span class="sxs-lookup"><span data-stu-id="ebe18-120">The configured <xref:System.Net.Http.HttpClient> is then used to make authorized requests using a simple [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) pattern.</span></span>

<span data-ttu-id="ebe18-121">Componente `FetchData`(*Pages/FetchData.razor*):</span><span class="sxs-lookup"><span data-stu-id="ebe18-121">`FetchData` component (*Pages/FetchData.razor*):</span></span>

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

## <a name="typed-httpclient"></a><span data-ttu-id="ebe18-122">HttpClient con tipo</span><span class="sxs-lookup"><span data-stu-id="ebe18-122">Typed HttpClient</span></span>

<span data-ttu-id="ebe18-123">Se puede definir un cliente con tipo que controle todos los problemas de adquisición de tokens y HTTP en una sola clase.</span><span class="sxs-lookup"><span data-stu-id="ebe18-123">A typed client can be defined that handles all of the HTTP and token acquisition concerns within a single class.</span></span>

<span data-ttu-id="ebe18-124">*WeatherForecastClient.cs*:</span><span class="sxs-lookup"><span data-stu-id="ebe18-124">*WeatherForecastClient.cs*:</span></span>

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

<span data-ttu-id="ebe18-125">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="ebe18-125">`Program.Main` (*Program.cs*):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherForecastClient>(
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

<span data-ttu-id="ebe18-126">Componente `FetchData`(*Pages/FetchData.razor*):</span><span class="sxs-lookup"><span data-stu-id="ebe18-126">`FetchData` component (*Pages/FetchData.razor*):</span></span>

```razor
@inject WeatherForecastClient Client

...

protected override async Task OnInitializedAsync()
{
    forecasts = await Client.GetForecastAsync();
}
```

## <a name="configure-the-httpclient-handler"></a><span data-ttu-id="ebe18-127">Configurar el controlador HttpClient</span><span class="sxs-lookup"><span data-stu-id="ebe18-127">Configure the HttpClient handler</span></span>

<span data-ttu-id="ebe18-128">El controlador se puede configurar aún más con <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> para las solicitudes HTTP salientes.</span><span class="sxs-lookup"><span data-stu-id="ebe18-128">The handler can be further configured with <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> for outbound HTTP requests.</span></span>

<span data-ttu-id="ebe18-129">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="ebe18-129">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler(sp => sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(new [] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }));
```

## <a name="unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client"></a><span data-ttu-id="ebe18-130">Solicitudes de API Web no autenticadas o no autorizadas en una aplicación con un cliente predeterminado seguro</span><span class="sxs-lookup"><span data-stu-id="ebe18-130">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>

<span data-ttu-id="ebe18-131">Si la Blazor aplicación Webassembly usa normalmente un valor predeterminado seguro <xref:System.Net.Http.HttpClient> , la aplicación también puede realizar solicitudes de API Web no autenticadas o no autorizadas mediante la configuración de una llamada <xref:System.Net.Http.HttpClient> :</span><span class="sxs-lookup"><span data-stu-id="ebe18-131">If the Blazor WebAssembly app ordinarily uses a secure default <xref:System.Net.Http.HttpClient>, the app can also make unauthenticated or unauthorized web API requests by configuring a named <xref:System.Net.Http.HttpClient>:</span></span>

<span data-ttu-id="ebe18-132">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="ebe18-132">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI.NoAuthenticationClient", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="ebe18-133">El registro anterior se suma al registro predeterminado seguro existente <xref:System.Net.Http.HttpClient> .</span><span class="sxs-lookup"><span data-stu-id="ebe18-133">The preceding registration is in addition to the existing secure default <xref:System.Net.Http.HttpClient> registration.</span></span>

<span data-ttu-id="ebe18-134">Un componente crea <xref:System.Net.Http.HttpClient> desde <xref:System.Net.Http.IHttpClientFactory> para realizar solicitudes no autenticadas o no autorizadas:</span><span class="sxs-lookup"><span data-stu-id="ebe18-134">A component creates the <xref:System.Net.Http.HttpClient> from the <xref:System.Net.Http.IHttpClientFactory> to make unauthenticated or unauthorized requests:</span></span>

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
> <span data-ttu-id="ebe18-135">El controlador de la API de servidor, `WeatherForecastNoAuthenticationController` para el ejemplo anterior, no está marcado con el [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atributo.</span><span class="sxs-lookup"><span data-stu-id="ebe18-135">The controller in the server API, `WeatherForecastNoAuthenticationController` for the preceding example, isn't marked with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute.</span></span>

## <a name="request-additional-access-tokens"></a><span data-ttu-id="ebe18-136">Solicitar tokens de acceso adicionales</span><span class="sxs-lookup"><span data-stu-id="ebe18-136">Request additional access tokens</span></span>

<span data-ttu-id="ebe18-137">Los tokens de acceso se pueden obtener manualmente mediante una llamada a `IAccessTokenProvider.RequestAccessToken` .</span><span class="sxs-lookup"><span data-stu-id="ebe18-137">Access tokens can be manually obtained by calling `IAccessTokenProvider.RequestAccessToken`.</span></span>

<span data-ttu-id="ebe18-138">En el siguiente ejemplo, una aplicación necesita los ámbitos de API de Azure Active Directory adicionales (AAD) Microsoft Graph para leer los datos de usuario y enviar correo.</span><span class="sxs-lookup"><span data-stu-id="ebe18-138">In the following example, additional Azure Active Directory (AAD) Microsoft Graph API scopes are required by an app to read user data and send mail.</span></span> <span data-ttu-id="ebe18-139">Después de agregar los permisos de Microsoft Graph API en el portal de Azure AAD, los ámbitos adicionales se configuran en la aplicación cliente.</span><span class="sxs-lookup"><span data-stu-id="ebe18-139">After adding the Microsoft Graph API permissions in the Azure AAD portal, the additional scopes are configured in the Client app.</span></span>

<span data-ttu-id="ebe18-140">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="ebe18-140">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="ebe18-141">El `IAccessTokenProvider.RequestToken` método proporciona una sobrecarga que permite a una aplicación aprovisionar un token de acceso con un conjunto determinado de ámbitos.</span><span class="sxs-lookup"><span data-stu-id="ebe18-141">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision an access token with a given set of scopes.</span></span>

<span data-ttu-id="ebe18-142">En un Razor componente:</span><span class="sxs-lookup"><span data-stu-id="ebe18-142">In a Razor component:</span></span>

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

<span data-ttu-id="ebe18-143"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType>Devuelve</span><span class="sxs-lookup"><span data-stu-id="ebe18-143"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType> returns:</span></span>

* <span data-ttu-id="ebe18-144">`true`con `token` para su uso.</span><span class="sxs-lookup"><span data-stu-id="ebe18-144">`true` with the `token` for use.</span></span>
* <span data-ttu-id="ebe18-145">`false`Si el token no se recupera.</span><span class="sxs-lookup"><span data-stu-id="ebe18-145">`false` if the token isn't retrieved.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="ebe18-146">HttpClient y HttpRequestMessage con las opciones de solicitud de Fetch API</span><span class="sxs-lookup"><span data-stu-id="ebe18-146">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="ebe18-147">Al ejecutar webassembly en una Blazor aplicación Webassembly, [HttpClient](xref:fundamentals/http-requests) y <xref:System.Net.Http.HttpRequestMessage> se puede usar para personalizar las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="ebe18-147">When running on WebAssembly in a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) and <xref:System.Net.Http.HttpRequestMessage> can be used to customize requests.</span></span> <span data-ttu-id="ebe18-148">Por ejemplo, puede especificar el método HTTP y los encabezados de solicitud.</span><span class="sxs-lookup"><span data-stu-id="ebe18-148">For example, you can specify the HTTP method and request headers.</span></span> <span data-ttu-id="ebe18-149">El componente siguiente realiza una `POST` solicitud a un punto de conexión de API de lista de tareas pendientes en el servidor y muestra el cuerpo de la respuesta:</span><span class="sxs-lookup"><span data-stu-id="ebe18-149">The following component makes a `POST` request to a To Do List API endpoint on the server and shows the response body:</span></span>

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

<span data-ttu-id="ebe18-150">En la implementación de <xref:System.Net.Http.HttpClient> de WebAssembly de .NET se usa [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span><span class="sxs-lookup"><span data-stu-id="ebe18-150">.NET WebAssembly's implementation of <xref:System.Net.Http.HttpClient> uses [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span></span> <span data-ttu-id="ebe18-151">Fetch permite configurar varias [opciones específicas de la solicitud](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="ebe18-151">Fetch allows configuring several [request-specific options](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span> 

<span data-ttu-id="ebe18-152">Se pueden configurar opciones de solicitud de captura HTTP con los métodos de extensión <xref:System.Net.Http.HttpRequestMessage> que se muestran en la siguiente tabla.</span><span class="sxs-lookup"><span data-stu-id="ebe18-152">HTTP fetch request options can be configured with <xref:System.Net.Http.HttpRequestMessage> extension methods shown in the following table.</span></span>

| <span data-ttu-id="ebe18-153">Método de extensión</span><span class="sxs-lookup"><span data-stu-id="ebe18-153">Extension method</span></span> | <span data-ttu-id="ebe18-154">Propiedad de solicitud de captura</span><span class="sxs-lookup"><span data-stu-id="ebe18-154">Fetch request property</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> | [<span data-ttu-id="ebe18-155">credentials</span><span class="sxs-lookup"><span data-stu-id="ebe18-155">credentials</span></span>](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCache%2A> | [<span data-ttu-id="ebe18-156">cache</span><span class="sxs-lookup"><span data-stu-id="ebe18-156">cache</span></span>](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestMode%2A> | [<span data-ttu-id="ebe18-157">mode</span><span class="sxs-lookup"><span data-stu-id="ebe18-157">mode</span></span>](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestIntegrity%2A> | [<span data-ttu-id="ebe18-158">integrity</span><span class="sxs-lookup"><span data-stu-id="ebe18-158">integrity</span></span>](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

<span data-ttu-id="ebe18-159">Se pueden establecer más opciones usando el método de extensión <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A>, que es más genérico.</span><span class="sxs-lookup"><span data-stu-id="ebe18-159">You can set additional options using the more generic <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> extension method.</span></span>
 
<span data-ttu-id="ebe18-160">Normalmente, la respuesta HTTP se almacena en búfer en una aplicación WebAssembly de Blazor para habilitar la compatibilidad con las lecturas de sincronización en el contenido de la respuesta.</span><span class="sxs-lookup"><span data-stu-id="ebe18-160">The HTTP response is typically buffered in a Blazor WebAssembly app to enable support for sync reads on the response content.</span></span> <span data-ttu-id="ebe18-161">Para habilitar la compatibilidad con la transmisión de respuestas, use el método de extensión <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> en la solicitud.</span><span class="sxs-lookup"><span data-stu-id="ebe18-161">To enable support for response streaming, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> extension method on the request.</span></span>

<span data-ttu-id="ebe18-162">Para incluir credenciales en una solicitud entre orígenes, use el método de extensión <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A>:</span><span class="sxs-lookup"><span data-stu-id="ebe18-162">To include credentials in a cross-origin request, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> extension method:</span></span>

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

<span data-ttu-id="ebe18-163">Para obtener más información sobre las opciones de Fetch API, vea [Documentación web de MDN: WindowOrWorkerGlobalScope.fetch(): parámetros](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="ebe18-163">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

<span data-ttu-id="ebe18-164">Al enviar credenciales (cookies de autorización o encabezados) en solicitudes de CORS, la directiva de CORS debe permitir el encabezado `Authorization`.</span><span class="sxs-lookup"><span data-stu-id="ebe18-164">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="ebe18-165">En la directiva siguiente se incluye la configuración de:</span><span class="sxs-lookup"><span data-stu-id="ebe18-165">The following policy includes configuration for:</span></span>

* <span data-ttu-id="ebe18-166">Orígenes de la solicitud (`http://localhost:5000`, `https://localhost:5001`).</span><span class="sxs-lookup"><span data-stu-id="ebe18-166">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="ebe18-167">Cualquier método (verbo).</span><span class="sxs-lookup"><span data-stu-id="ebe18-167">Any method (verb).</span></span>
* <span data-ttu-id="ebe18-168">Los encabezados `Content-Type` y `Authorization`.</span><span class="sxs-lookup"><span data-stu-id="ebe18-168">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="ebe18-169">Para permitir un encabezado personalizado (por ejemplo, `x-custom-header`), enumere el encabezado al llamar a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span><span class="sxs-lookup"><span data-stu-id="ebe18-169">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="ebe18-170">Las credenciales establecidas por el código JavaScript del lado cliente (la propiedad `credentials` establecida en `include`).</span><span class="sxs-lookup"><span data-stu-id="ebe18-170">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="ebe18-171">Para obtener más información, vea <xref:security/cors> y el componente del evaluador de solicitudes HTTP de la aplicación de ejemplo (*Components/HTTPRequestTester.razor*).</span><span class="sxs-lookup"><span data-stu-id="ebe18-171">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (*Components/HTTPRequestTester.razor*).</span></span>

## <a name="handle-token-request-errors"></a><span data-ttu-id="ebe18-172">Controlar errores de solicitudes de token</span><span class="sxs-lookup"><span data-stu-id="ebe18-172">Handle token request errors</span></span>

<span data-ttu-id="ebe18-173">Cuando una aplicación de una sola página (SPA) autentica a un usuario mediante Open ID Connect (OIDC), el estado de autenticación se mantiene localmente en el SPA y en el Identity proveedor (IP) en forma de cookie de sesión que se establece como resultado del usuario que proporciona sus credenciales.</span><span class="sxs-lookup"><span data-stu-id="ebe18-173">When a Single Page Application (SPA) authenticates a user using Open ID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="ebe18-174">Normalmente, los tokens que emite la IP para el usuario son válidos durante breves períodos de tiempo, aproximadamente una hora, por lo que la aplicación cliente debe capturar nuevos tokens con regularidad.</span><span class="sxs-lookup"><span data-stu-id="ebe18-174">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="ebe18-175">De lo contrario, se cerrará la sesión del usuario después de que expiren los tokens concedidos.</span><span class="sxs-lookup"><span data-stu-id="ebe18-175">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="ebe18-176">En la mayoría de los casos, los clientes de OIDC pueden aprovisionar nuevos tokens sin necesidad de que el usuario se autentique de nuevo gracias al estado de autenticación o a la "sesión" que se mantiene dentro de la dirección IP.</span><span class="sxs-lookup"><span data-stu-id="ebe18-176">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="ebe18-177">Hay algunos casos en los que el cliente no puede obtener un token sin la interacción del usuario, por ejemplo, cuando por alguna razón el usuario cierra la sesión explícitamente desde la dirección IP.</span><span class="sxs-lookup"><span data-stu-id="ebe18-177">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="ebe18-178">Este escenario se produce si un usuario visita `https://login.microsoftonline.com` y cierra la sesión. En estos casos, la aplicación no sabe inmediatamente que el usuario ha cerrado la sesión. Es posible que el token que contiene el cliente ya no sea válido.</span><span class="sxs-lookup"><span data-stu-id="ebe18-178">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="ebe18-179">Además, el cliente no puede aprovisionar un token nuevo sin la interacción del usuario después de que expire el token actual.</span><span class="sxs-lookup"><span data-stu-id="ebe18-179">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="ebe18-180">Estos escenarios no son específicos de la autenticación basada en tokens.</span><span class="sxs-lookup"><span data-stu-id="ebe18-180">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="ebe18-181">Forman parte de la naturaleza de spa.</span><span class="sxs-lookup"><span data-stu-id="ebe18-181">They are part of the nature of SPAs.</span></span> <span data-ttu-id="ebe18-182">Un SPA que use cookies tampoco podrá llamar a una API de servidor si se quita la cookie de autenticación.</span><span class="sxs-lookup"><span data-stu-id="ebe18-182">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="ebe18-183">Cuando una aplicación realiza llamadas API a recursos protegidos, debe tener en cuenta lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="ebe18-183">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="ebe18-184">Para aprovisionar un nuevo token de acceso para llamar a la API, es posible que sea necesario volver a autenticar al usuario.</span><span class="sxs-lookup"><span data-stu-id="ebe18-184">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="ebe18-185">Incluso si el cliente tiene un token que parece ser válido, es posible que se produzca un error en la llamada al servidor porque el usuario revocó el token.</span><span class="sxs-lookup"><span data-stu-id="ebe18-185">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="ebe18-186">Cuando la aplicación solicita un token, hay dos resultados posibles:</span><span class="sxs-lookup"><span data-stu-id="ebe18-186">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="ebe18-187">La solicitud se realiza correctamente y la aplicación tiene un token válido.</span><span class="sxs-lookup"><span data-stu-id="ebe18-187">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="ebe18-188">Se produce un error en la solicitud y la aplicación debe autenticar al usuario de nuevo para obtener un nuevo token.</span><span class="sxs-lookup"><span data-stu-id="ebe18-188">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="ebe18-189">Cuando se produce un error en una solicitud de token, debe decidir si desea guardar el estado actual antes de realizar una redirección.</span><span class="sxs-lookup"><span data-stu-id="ebe18-189">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="ebe18-190">Existen varios enfoques con niveles crecientes de complejidad:</span><span class="sxs-lookup"><span data-stu-id="ebe18-190">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="ebe18-191">Almacenar el estado de la página actual en el almacenamiento de sesión.</span><span class="sxs-lookup"><span data-stu-id="ebe18-191">Store the current page state in session storage.</span></span> <span data-ttu-id="ebe18-192">Durante el [evento del ciclo de vida de OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods) ( <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> ), compruebe si el estado se puede restaurar antes de continuar.</span><span class="sxs-lookup"><span data-stu-id="ebe18-192">During the [OnInitializedAsync lifecycle event](xref:blazor/lifecycle#component-initialization-methods) (<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>), check if state can be restored before continuing.</span></span>
* <span data-ttu-id="ebe18-193">Agregue un parámetro de cadena de consulta y úselo como una manera de señalar la aplicación que necesita para volver a hidratar el estado guardado anteriormente.</span><span class="sxs-lookup"><span data-stu-id="ebe18-193">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="ebe18-194">Agregue un parámetro de cadena de consulta con un identificador único para almacenar los datos en el almacenamiento de sesión sin poner en peligro las colisiones con otros elementos.</span><span class="sxs-lookup"><span data-stu-id="ebe18-194">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="ebe18-195">El ejemplo siguiente muestra cómo:</span><span class="sxs-lookup"><span data-stu-id="ebe18-195">The following example shows how to:</span></span>

* <span data-ttu-id="ebe18-196">Conservar el estado antes de redirigir a la página de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="ebe18-196">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="ebe18-197">Recupere el estado anterior después de la autenticación mediante el parámetro de cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="ebe18-197">Recover the previous state afterward authentication using the query string parameter.</span></span>

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

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="ebe18-198">Guardar el estado de la aplicación antes de una operación de autenticación</span><span class="sxs-lookup"><span data-stu-id="ebe18-198">Save app state before an authentication operation</span></span>

<span data-ttu-id="ebe18-199">Durante una operación de autenticación, hay casos en los que desea guardar el estado de la aplicación antes de que el explorador se redirija a la dirección IP.</span><span class="sxs-lookup"><span data-stu-id="ebe18-199">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="ebe18-200">Esto puede ser así cuando se usa algo como un contenedor de estado y se desea restaurar el estado después de que la autenticación se realice correctamente.</span><span class="sxs-lookup"><span data-stu-id="ebe18-200">This can be the case when you are using something like a state container and you want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="ebe18-201">Puede usar un objeto de estado de autenticación personalizado para conservar el estado específico de la aplicación o una referencia a él y restaurar ese estado una vez que la operación de autenticación se complete correctamente.</span><span class="sxs-lookup"><span data-stu-id="ebe18-201">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state once the authentication operation successfully completes.</span></span>

<span data-ttu-id="ebe18-202">`Authentication`componente (*páginas/autenticación. Razor*):</span><span class="sxs-lookup"><span data-stu-id="ebe18-202">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

## <a name="customize-app-routes"></a><span data-ttu-id="ebe18-203">Personalización de rutas de aplicación</span><span class="sxs-lookup"><span data-stu-id="ebe18-203">Customize app routes</span></span>

<span data-ttu-id="ebe18-204">De forma predeterminada, la biblioteca [Microsoft. AspNetCore. Components. Webassembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) usa las rutas que se muestran en la tabla siguiente para representar distintos Estados de autenticación.</span><span class="sxs-lookup"><span data-stu-id="ebe18-204">By default, the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="ebe18-205">Ruta</span><span class="sxs-lookup"><span data-stu-id="ebe18-205">Route</span></span>                            | <span data-ttu-id="ebe18-206">Propósito</span><span class="sxs-lookup"><span data-stu-id="ebe18-206">Purpose</span></span> |
| ---
<span data-ttu-id="ebe18-207">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="ebe18-207">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="ebe18-208">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebe18-208">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ebe18-209">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebe18-209">'Blazor'</span></span>
- <span data-ttu-id="ebe18-210">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebe18-210">'Identity'</span></span>
- <span data-ttu-id="ebe18-211">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebe18-211">'Let's Encrypt'</span></span>
- <span data-ttu-id="ebe18-212">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebe18-212">'Razor'</span></span>
- <span data-ttu-id="ebe18-213">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebe18-213">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ebe18-214">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="ebe18-214">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="ebe18-215">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebe18-215">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ebe18-216">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebe18-216">'Blazor'</span></span>
- <span data-ttu-id="ebe18-217">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebe18-217">'Identity'</span></span>
- <span data-ttu-id="ebe18-218">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebe18-218">'Let's Encrypt'</span></span>
- <span data-ttu-id="ebe18-219">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebe18-219">'Razor'</span></span>
- <span data-ttu-id="ebe18-220">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebe18-220">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ebe18-221">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="ebe18-221">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="ebe18-222">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebe18-222">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ebe18-223">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebe18-223">'Blazor'</span></span>
- <span data-ttu-id="ebe18-224">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebe18-224">'Identity'</span></span>
- <span data-ttu-id="ebe18-225">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebe18-225">'Let's Encrypt'</span></span>
- <span data-ttu-id="ebe18-226">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebe18-226">'Razor'</span></span>
- <span data-ttu-id="ebe18-227">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebe18-227">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ebe18-228">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="ebe18-228">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="ebe18-229">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebe18-229">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ebe18-230">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebe18-230">'Blazor'</span></span>
- <span data-ttu-id="ebe18-231">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebe18-231">'Identity'</span></span>
- <span data-ttu-id="ebe18-232">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebe18-232">'Let's Encrypt'</span></span>
- <span data-ttu-id="ebe18-233">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebe18-233">'Razor'</span></span>
- <span data-ttu-id="ebe18-234">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebe18-234">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ebe18-235">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="ebe18-235">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="ebe18-236">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebe18-236">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ebe18-237">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebe18-237">'Blazor'</span></span>
- <span data-ttu-id="ebe18-238">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebe18-238">'Identity'</span></span>
- <span data-ttu-id="ebe18-239">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebe18-239">'Let's Encrypt'</span></span>
- <span data-ttu-id="ebe18-240">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebe18-240">'Razor'</span></span>
- <span data-ttu-id="ebe18-241">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebe18-241">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ebe18-242">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="ebe18-242">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="ebe18-243">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebe18-243">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ebe18-244">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebe18-244">'Blazor'</span></span>
- <span data-ttu-id="ebe18-245">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebe18-245">'Identity'</span></span>
- <span data-ttu-id="ebe18-246">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebe18-246">'Let's Encrypt'</span></span>
- <span data-ttu-id="ebe18-247">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebe18-247">'Razor'</span></span>
- <span data-ttu-id="ebe18-248">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebe18-248">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ebe18-249">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="ebe18-249">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="ebe18-250">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebe18-250">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ebe18-251">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebe18-251">'Blazor'</span></span>
- <span data-ttu-id="ebe18-252">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebe18-252">'Identity'</span></span>
- <span data-ttu-id="ebe18-253">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebe18-253">'Let's Encrypt'</span></span>
- <span data-ttu-id="ebe18-254">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebe18-254">'Razor'</span></span>
- <span data-ttu-id="ebe18-255">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebe18-255">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ebe18-256">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="ebe18-256">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="ebe18-257">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebe18-257">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ebe18-258">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebe18-258">'Blazor'</span></span>
- <span data-ttu-id="ebe18-259">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebe18-259">'Identity'</span></span>
- <span data-ttu-id="ebe18-260">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebe18-260">'Let's Encrypt'</span></span>
- <span data-ttu-id="ebe18-261">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebe18-261">'Razor'</span></span>
- <span data-ttu-id="ebe18-262">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebe18-262">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ebe18-263">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="ebe18-263">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="ebe18-264">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebe18-264">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ebe18-265">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebe18-265">'Blazor'</span></span>
- <span data-ttu-id="ebe18-266">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebe18-266">'Identity'</span></span>
- <span data-ttu-id="ebe18-267">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebe18-267">'Let's Encrypt'</span></span>
- <span data-ttu-id="ebe18-268">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebe18-268">'Razor'</span></span>
- <span data-ttu-id="ebe18-269">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebe18-269">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ebe18-270">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="ebe18-270">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="ebe18-271">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebe18-271">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ebe18-272">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebe18-272">'Blazor'</span></span>
- <span data-ttu-id="ebe18-273">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebe18-273">'Identity'</span></span>
- <span data-ttu-id="ebe18-274">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebe18-274">'Let's Encrypt'</span></span>
- <span data-ttu-id="ebe18-275">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebe18-275">'Razor'</span></span>
- <span data-ttu-id="ebe18-276">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebe18-276">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ebe18-277">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="ebe18-277">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="ebe18-278">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebe18-278">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ebe18-279">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebe18-279">'Blazor'</span></span>
- <span data-ttu-id="ebe18-280">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebe18-280">'Identity'</span></span>
- <span data-ttu-id="ebe18-281">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebe18-281">'Let's Encrypt'</span></span>
- <span data-ttu-id="ebe18-282">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebe18-282">'Razor'</span></span>
- <span data-ttu-id="ebe18-283">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebe18-283">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ebe18-284">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="ebe18-284">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="ebe18-285">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebe18-285">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ebe18-286">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebe18-286">'Blazor'</span></span>
- <span data-ttu-id="ebe18-287">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebe18-287">'Identity'</span></span>
- <span data-ttu-id="ebe18-288">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebe18-288">'Let's Encrypt'</span></span>
- <span data-ttu-id="ebe18-289">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebe18-289">'Razor'</span></span>
- <span data-ttu-id="ebe18-290">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebe18-290">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ebe18-291">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="ebe18-291">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="ebe18-292">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebe18-292">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ebe18-293">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebe18-293">'Blazor'</span></span>
- <span data-ttu-id="ebe18-294">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebe18-294">'Identity'</span></span>
- <span data-ttu-id="ebe18-295">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebe18-295">'Let's Encrypt'</span></span>
- <span data-ttu-id="ebe18-296">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebe18-296">'Razor'</span></span>
- <span data-ttu-id="ebe18-297">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebe18-297">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ebe18-298">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="ebe18-298">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="ebe18-299">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebe18-299">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ebe18-300">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebe18-300">'Blazor'</span></span>
- <span data-ttu-id="ebe18-301">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebe18-301">'Identity'</span></span>
- <span data-ttu-id="ebe18-302">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebe18-302">'Let's Encrypt'</span></span>
- <span data-ttu-id="ebe18-303">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebe18-303">'Razor'</span></span>
- <span data-ttu-id="ebe18-304">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebe18-304">'SignalR' uid:</span></span> 

<span data-ttu-id="ebe18-305">---------------- | ---title: "ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales" Author: Description: "Obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales".</span><span class="sxs-lookup"><span data-stu-id="ebe18-305">---------------- | --- title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="ebe18-306">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebe18-306">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ebe18-307">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebe18-307">'Blazor'</span></span>
- <span data-ttu-id="ebe18-308">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebe18-308">'Identity'</span></span>
- <span data-ttu-id="ebe18-309">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebe18-309">'Let's Encrypt'</span></span>
- <span data-ttu-id="ebe18-310">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebe18-310">'Razor'</span></span>
- <span data-ttu-id="ebe18-311">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebe18-311">'SignalR' uid:</span></span> 

<span data-ttu-id="ebe18-312">---- | | `authentication/login`           | Desencadena una operación de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="ebe18-312">---- | | `authentication/login`           | Triggers a sign-in operation.</span></span> <span data-ttu-id="ebe18-313">| | `authentication/login-callback`  | Controla el resultado de cualquier operación de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="ebe18-313">| | `authentication/login-callback`  | Handles the result of any sign-in operation.</span></span> <span data-ttu-id="ebe18-314">| | `authentication/login-failed`    | Muestra mensajes de error cuando se produce un error en la operación de inicio de sesión por algún motivo.</span><span class="sxs-lookup"><span data-stu-id="ebe18-314">| | `authentication/login-failed`    | Displays error messages when the sign-in operation fails for some reason.</span></span> <span data-ttu-id="ebe18-315">| | `authentication/logout`          | Desencadena una operación de cierre de sesión.</span><span class="sxs-lookup"><span data-stu-id="ebe18-315">| | `authentication/logout`          | Triggers a sign-out operation.</span></span> <span data-ttu-id="ebe18-316">| | `authentication/logout-callback` | Controla el resultado de una operación de cierre de sesión.</span><span class="sxs-lookup"><span data-stu-id="ebe18-316">| | `authentication/logout-callback` | Handles the result of a sign-out operation.</span></span> <span data-ttu-id="ebe18-317">| | `authentication/logout-failed`   | Muestra mensajes de error cuando se produce un error en la operación de cierre de sesión por algún motivo.</span><span class="sxs-lookup"><span data-stu-id="ebe18-317">| | `authentication/logout-failed`   | Displays error messages when the sign-out operation fails for some reason.</span></span> <span data-ttu-id="ebe18-318">| | `authentication/logged-out`      | Indica que el usuario ha cerrado la sesión correctamente.</span><span class="sxs-lookup"><span data-stu-id="ebe18-318">| | `authentication/logged-out`      | Indicates that the user has successfully logout.</span></span> <span data-ttu-id="ebe18-319">| | `authentication/profile`         | Desencadena una operación para editar el perfil de usuario.</span><span class="sxs-lookup"><span data-stu-id="ebe18-319">| | `authentication/profile`         | Triggers an operation to edit the user profile.</span></span> <span data-ttu-id="ebe18-320">| | `authentication/register`        | Desencadena una operación para registrar un nuevo usuario.</span><span class="sxs-lookup"><span data-stu-id="ebe18-320">| | `authentication/register`        | Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="ebe18-321">Las rutas que se muestran en la tabla anterior se pueden configurar mediante <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="ebe18-321">The routes shown in the preceding table are configurable via <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="ebe18-322">Al establecer las opciones para proporcionar rutas personalizadas, confirme que la aplicación tiene una ruta que controla cada ruta de acceso.</span><span class="sxs-lookup"><span data-stu-id="ebe18-322">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="ebe18-323">En el ejemplo siguiente, todas las rutas de acceso tienen el prefijo `/security` .</span><span class="sxs-lookup"><span data-stu-id="ebe18-323">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="ebe18-324">`Authentication`componente (*páginas/autenticación. Razor*):</span><span class="sxs-lookup"><span data-stu-id="ebe18-324">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="ebe18-325">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="ebe18-325">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="ebe18-326">Si el requisito requiere rutas de acceso completamente diferentes, establezca las rutas como se describió anteriormente y represente el <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> con un parámetro de acción explícito:</span><span class="sxs-lookup"><span data-stu-id="ebe18-326">If the requirement calls for completely different paths, set the routes as described previously and render the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="ebe18-327">Puede descomponer la interfaz de usuario en diferentes páginas si decide hacerlo.</span><span class="sxs-lookup"><span data-stu-id="ebe18-327">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="ebe18-328">Personalización de la interfaz de usuario de autenticación</span><span class="sxs-lookup"><span data-stu-id="ebe18-328">Customize the authentication user interface</span></span>

<span data-ttu-id="ebe18-329"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView>incluye un conjunto predeterminado de partes de la interfaz de usuario para cada estado de autenticación.</span><span class="sxs-lookup"><span data-stu-id="ebe18-329"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="ebe18-330">Cada Estado se puede personalizar pasando un personalizado <xref:Microsoft.AspNetCore.Components.RenderFragment> .</span><span class="sxs-lookup"><span data-stu-id="ebe18-330">Each state can be customized by passing in a custom <xref:Microsoft.AspNetCore.Components.RenderFragment>.</span></span> <span data-ttu-id="ebe18-331">Para personalizar el texto mostrado durante el proceso de inicio de sesión inicial, puede cambiar el de la <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> manera siguiente.</span><span class="sxs-lookup"><span data-stu-id="ebe18-331">To customize the displayed text during the initial login process, can change the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> as follows.</span></span>

<span data-ttu-id="ebe18-332">`Authentication`componente (*páginas/autenticación. Razor*):</span><span class="sxs-lookup"><span data-stu-id="ebe18-332">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

<span data-ttu-id="ebe18-333"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView>Tiene un fragmento que se puede utilizar por cada ruta de autenticación que se muestra en la tabla siguiente.</span><span class="sxs-lookup"><span data-stu-id="ebe18-333">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="ebe18-334">Ruta</span><span class="sxs-lookup"><span data-stu-id="ebe18-334">Route</span></span>                            | <span data-ttu-id="ebe18-335">Fragmento</span><span class="sxs-lookup"><span data-stu-id="ebe18-335">Fragment</span></span>                |
| ---
<span data-ttu-id="ebe18-336">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="ebe18-336">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="ebe18-337">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebe18-337">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ebe18-338">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebe18-338">'Blazor'</span></span>
- <span data-ttu-id="ebe18-339">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebe18-339">'Identity'</span></span>
- <span data-ttu-id="ebe18-340">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebe18-340">'Let's Encrypt'</span></span>
- <span data-ttu-id="ebe18-341">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebe18-341">'Razor'</span></span>
- <span data-ttu-id="ebe18-342">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebe18-342">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ebe18-343">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="ebe18-343">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="ebe18-344">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebe18-344">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ebe18-345">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebe18-345">'Blazor'</span></span>
- <span data-ttu-id="ebe18-346">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebe18-346">'Identity'</span></span>
- <span data-ttu-id="ebe18-347">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebe18-347">'Let's Encrypt'</span></span>
- <span data-ttu-id="ebe18-348">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebe18-348">'Razor'</span></span>
- <span data-ttu-id="ebe18-349">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebe18-349">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ebe18-350">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="ebe18-350">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="ebe18-351">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebe18-351">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ebe18-352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebe18-352">'Blazor'</span></span>
- <span data-ttu-id="ebe18-353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebe18-353">'Identity'</span></span>
- <span data-ttu-id="ebe18-354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebe18-354">'Let's Encrypt'</span></span>
- <span data-ttu-id="ebe18-355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebe18-355">'Razor'</span></span>
- <span data-ttu-id="ebe18-356">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebe18-356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ebe18-357">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="ebe18-357">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="ebe18-358">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebe18-358">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ebe18-359">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebe18-359">'Blazor'</span></span>
- <span data-ttu-id="ebe18-360">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebe18-360">'Identity'</span></span>
- <span data-ttu-id="ebe18-361">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebe18-361">'Let's Encrypt'</span></span>
- <span data-ttu-id="ebe18-362">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebe18-362">'Razor'</span></span>
- <span data-ttu-id="ebe18-363">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebe18-363">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ebe18-364">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="ebe18-364">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="ebe18-365">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebe18-365">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ebe18-366">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebe18-366">'Blazor'</span></span>
- <span data-ttu-id="ebe18-367">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebe18-367">'Identity'</span></span>
- <span data-ttu-id="ebe18-368">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebe18-368">'Let's Encrypt'</span></span>
- <span data-ttu-id="ebe18-369">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebe18-369">'Razor'</span></span>
- <span data-ttu-id="ebe18-370">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebe18-370">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ebe18-371">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="ebe18-371">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="ebe18-372">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebe18-372">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ebe18-373">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebe18-373">'Blazor'</span></span>
- <span data-ttu-id="ebe18-374">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebe18-374">'Identity'</span></span>
- <span data-ttu-id="ebe18-375">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebe18-375">'Let's Encrypt'</span></span>
- <span data-ttu-id="ebe18-376">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebe18-376">'Razor'</span></span>
- <span data-ttu-id="ebe18-377">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebe18-377">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ebe18-378">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="ebe18-378">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="ebe18-379">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebe18-379">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ebe18-380">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebe18-380">'Blazor'</span></span>
- <span data-ttu-id="ebe18-381">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebe18-381">'Identity'</span></span>
- <span data-ttu-id="ebe18-382">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebe18-382">'Let's Encrypt'</span></span>
- <span data-ttu-id="ebe18-383">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebe18-383">'Razor'</span></span>
- <span data-ttu-id="ebe18-384">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebe18-384">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ebe18-385">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="ebe18-385">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="ebe18-386">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebe18-386">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ebe18-387">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebe18-387">'Blazor'</span></span>
- <span data-ttu-id="ebe18-388">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebe18-388">'Identity'</span></span>
- <span data-ttu-id="ebe18-389">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebe18-389">'Let's Encrypt'</span></span>
- <span data-ttu-id="ebe18-390">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebe18-390">'Razor'</span></span>
- <span data-ttu-id="ebe18-391">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebe18-391">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ebe18-392">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="ebe18-392">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="ebe18-393">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebe18-393">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ebe18-394">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebe18-394">'Blazor'</span></span>
- <span data-ttu-id="ebe18-395">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebe18-395">'Identity'</span></span>
- <span data-ttu-id="ebe18-396">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebe18-396">'Let's Encrypt'</span></span>
- <span data-ttu-id="ebe18-397">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebe18-397">'Razor'</span></span>
- <span data-ttu-id="ebe18-398">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebe18-398">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ebe18-399">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="ebe18-399">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="ebe18-400">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebe18-400">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ebe18-401">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebe18-401">'Blazor'</span></span>
- <span data-ttu-id="ebe18-402">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebe18-402">'Identity'</span></span>
- <span data-ttu-id="ebe18-403">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebe18-403">'Let's Encrypt'</span></span>
- <span data-ttu-id="ebe18-404">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebe18-404">'Razor'</span></span>
- <span data-ttu-id="ebe18-405">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebe18-405">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ebe18-406">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="ebe18-406">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="ebe18-407">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebe18-407">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ebe18-408">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebe18-408">'Blazor'</span></span>
- <span data-ttu-id="ebe18-409">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebe18-409">'Identity'</span></span>
- <span data-ttu-id="ebe18-410">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebe18-410">'Let's Encrypt'</span></span>
- <span data-ttu-id="ebe18-411">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebe18-411">'Razor'</span></span>
- <span data-ttu-id="ebe18-412">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebe18-412">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ebe18-413">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="ebe18-413">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="ebe18-414">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebe18-414">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ebe18-415">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebe18-415">'Blazor'</span></span>
- <span data-ttu-id="ebe18-416">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebe18-416">'Identity'</span></span>
- <span data-ttu-id="ebe18-417">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebe18-417">'Let's Encrypt'</span></span>
- <span data-ttu-id="ebe18-418">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebe18-418">'Razor'</span></span>
- <span data-ttu-id="ebe18-419">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebe18-419">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ebe18-420">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="ebe18-420">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="ebe18-421">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebe18-421">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ebe18-422">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebe18-422">'Blazor'</span></span>
- <span data-ttu-id="ebe18-423">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebe18-423">'Identity'</span></span>
- <span data-ttu-id="ebe18-424">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebe18-424">'Let's Encrypt'</span></span>
- <span data-ttu-id="ebe18-425">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebe18-425">'Razor'</span></span>
- <span data-ttu-id="ebe18-426">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebe18-426">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ebe18-427">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="ebe18-427">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="ebe18-428">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebe18-428">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ebe18-429">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebe18-429">'Blazor'</span></span>
- <span data-ttu-id="ebe18-430">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebe18-430">'Identity'</span></span>
- <span data-ttu-id="ebe18-431">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebe18-431">'Let's Encrypt'</span></span>
- <span data-ttu-id="ebe18-432">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebe18-432">'Razor'</span></span>
- <span data-ttu-id="ebe18-433">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebe18-433">'SignalR' uid:</span></span> 

<span data-ttu-id="ebe18-434">---------------- | ---title: "ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales" Author: Description: "Obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales".</span><span class="sxs-lookup"><span data-stu-id="ebe18-434">---------------- | --- title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="ebe18-435">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebe18-435">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ebe18-436">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebe18-436">'Blazor'</span></span>
- <span data-ttu-id="ebe18-437">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebe18-437">'Identity'</span></span>
- <span data-ttu-id="ebe18-438">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebe18-438">'Let's Encrypt'</span></span>
- <span data-ttu-id="ebe18-439">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebe18-439">'Razor'</span></span>
- <span data-ttu-id="ebe18-440">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebe18-440">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ebe18-441">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="ebe18-441">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="ebe18-442">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebe18-442">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ebe18-443">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebe18-443">'Blazor'</span></span>
- <span data-ttu-id="ebe18-444">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebe18-444">'Identity'</span></span>
- <span data-ttu-id="ebe18-445">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebe18-445">'Let's Encrypt'</span></span>
- <span data-ttu-id="ebe18-446">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebe18-446">'Razor'</span></span>
- <span data-ttu-id="ebe18-447">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebe18-447">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ebe18-448">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="ebe18-448">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="ebe18-449">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebe18-449">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ebe18-450">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebe18-450">'Blazor'</span></span>
- <span data-ttu-id="ebe18-451">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebe18-451">'Identity'</span></span>
- <span data-ttu-id="ebe18-452">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebe18-452">'Let's Encrypt'</span></span>
- <span data-ttu-id="ebe18-453">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebe18-453">'Razor'</span></span>
- <span data-ttu-id="ebe18-454">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebe18-454">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ebe18-455">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="ebe18-455">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="ebe18-456">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebe18-456">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ebe18-457">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebe18-457">'Blazor'</span></span>
- <span data-ttu-id="ebe18-458">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebe18-458">'Identity'</span></span>
- <span data-ttu-id="ebe18-459">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebe18-459">'Let's Encrypt'</span></span>
- <span data-ttu-id="ebe18-460">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebe18-460">'Razor'</span></span>
- <span data-ttu-id="ebe18-461">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebe18-461">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ebe18-462">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="ebe18-462">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="ebe18-463">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebe18-463">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ebe18-464">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebe18-464">'Blazor'</span></span>
- <span data-ttu-id="ebe18-465">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebe18-465">'Identity'</span></span>
- <span data-ttu-id="ebe18-466">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebe18-466">'Let's Encrypt'</span></span>
- <span data-ttu-id="ebe18-467">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebe18-467">'Razor'</span></span>
- <span data-ttu-id="ebe18-468">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebe18-468">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ebe18-469">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="ebe18-469">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="ebe18-470">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebe18-470">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ebe18-471">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebe18-471">'Blazor'</span></span>
- <span data-ttu-id="ebe18-472">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebe18-472">'Identity'</span></span>
- <span data-ttu-id="ebe18-473">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebe18-473">'Let's Encrypt'</span></span>
- <span data-ttu-id="ebe18-474">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebe18-474">'Razor'</span></span>
- <span data-ttu-id="ebe18-475">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebe18-475">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ebe18-476">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="ebe18-476">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="ebe18-477">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebe18-477">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ebe18-478">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebe18-478">'Blazor'</span></span>
- <span data-ttu-id="ebe18-479">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebe18-479">'Identity'</span></span>
- <span data-ttu-id="ebe18-480">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebe18-480">'Let's Encrypt'</span></span>
- <span data-ttu-id="ebe18-481">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebe18-481">'Razor'</span></span>
- <span data-ttu-id="ebe18-482">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebe18-482">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ebe18-483">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="ebe18-483">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="ebe18-484">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebe18-484">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ebe18-485">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebe18-485">'Blazor'</span></span>
- <span data-ttu-id="ebe18-486">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebe18-486">'Identity'</span></span>
- <span data-ttu-id="ebe18-487">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebe18-487">'Let's Encrypt'</span></span>
- <span data-ttu-id="ebe18-488">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebe18-488">'Razor'</span></span>
- <span data-ttu-id="ebe18-489">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebe18-489">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ebe18-490">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: Description: ' obtenga información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="ebe18-490">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="ebe18-491">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebe18-491">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ebe18-492">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebe18-492">'Blazor'</span></span>
- <span data-ttu-id="ebe18-493">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebe18-493">'Identity'</span></span>
- <span data-ttu-id="ebe18-494">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebe18-494">'Let's Encrypt'</span></span>
- <span data-ttu-id="ebe18-495">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebe18-495">'Razor'</span></span>
- <span data-ttu-id="ebe18-496">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebe18-496">'SignalR' uid:</span></span> 

<span data-ttu-id="ebe18-497">------------ | | `authentication/login`           | `<LoggingIn>`           | | `authentication/login-callback`  | `<CompletingLoggingIn>` | | `authentication/login-failed`    | `<LogInFailed>`         | | `authentication/logout`          | `<LogOut>`              | | `authentication/logout-callback` | `<CompletingLogOut>`    | | `authentication/logout-failed`   | `<LogOutFailed>`        | | `authentication/logged-out`      | `<LogOutSucceeded>`     | | `authentication/profile`         | `<UserProfile>`         | | `authentication/register`        | `<Registering>`         |</span><span class="sxs-lookup"><span data-stu-id="ebe18-497">------------ | | `authentication/login`           | `<LoggingIn>`           | | `authentication/login-callback`  | `<CompletingLoggingIn>` | | `authentication/login-failed`    | `<LogInFailed>`         | | `authentication/logout`          | `<LogOut>`              | | `authentication/logout-callback` | `<CompletingLogOut>`    | | `authentication/logout-failed`   | `<LogOutFailed>`        | | `authentication/logged-out`      | `<LogOutSucceeded>`     | | `authentication/profile`         | `<UserProfile>`         | | `authentication/register`        | `<Registering>`         |</span></span>

## <a name="customize-the-user"></a><span data-ttu-id="ebe18-498">Personalización del usuario</span><span class="sxs-lookup"><span data-stu-id="ebe18-498">Customize the user</span></span>

<span data-ttu-id="ebe18-499">Los usuarios enlazados a la aplicación se pueden personalizar.</span><span class="sxs-lookup"><span data-stu-id="ebe18-499">Users bound to the app can be customized.</span></span> <span data-ttu-id="ebe18-500">En el ejemplo siguiente, todos los usuarios autenticados reciben una `amr` demanda por cada uno de los métodos de autenticación del usuario.</span><span class="sxs-lookup"><span data-stu-id="ebe18-500">In the following example, all authenticated users receive an `amr` claim for each of the user's authentication methods.</span></span>

<span data-ttu-id="ebe18-501">Cree una clase que extienda la <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> clase:</span><span class="sxs-lookup"><span data-stu-id="ebe18-501">Create a class that extends the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> class:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

<span data-ttu-id="ebe18-502">Cree una fábrica que extienda <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601> :</span><span class="sxs-lookup"><span data-stu-id="ebe18-502">Create a factory that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601>:</span></span>

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

<span data-ttu-id="ebe18-503">Registre `CustomAccountFactory` para el proveedor de autenticación en uso.</span><span class="sxs-lookup"><span data-stu-id="ebe18-503">Register the `CustomAccountFactory` for the authentication provider in use.</span></span> <span data-ttu-id="ebe18-504">Cualquiera de los siguientes registros son válidos:</span><span class="sxs-lookup"><span data-stu-id="ebe18-504">Any of the following registrations are valid:</span></span> 

* <span data-ttu-id="ebe18-505"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="ebe18-505"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span></span>

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

* <span data-ttu-id="ebe18-506"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="ebe18-506"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span></span>

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
  
* <span data-ttu-id="ebe18-507"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span><span class="sxs-lookup"><span data-stu-id="ebe18-507"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span></span>

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

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="ebe18-508">Compatibilidad de la representación previa con la autenticación</span><span class="sxs-lookup"><span data-stu-id="ebe18-508">Support prerendering with authentication</span></span>

<span data-ttu-id="ebe18-509">Después de seguir las instrucciones que aparecen en uno de los temas de la aplicación WebAssembly de Blazor hospedada, use estas instrucciones para crear una aplicación que:</span><span class="sxs-lookup"><span data-stu-id="ebe18-509">After following the guidance in one of the hosted Blazor WebAssembly app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="ebe18-510">Representa previamente las rutas de acceso para las que no se requiere autorización.</span><span class="sxs-lookup"><span data-stu-id="ebe18-510">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="ebe18-511">No representa previamente las rutas de acceso para las que se requiere autorización.</span><span class="sxs-lookup"><span data-stu-id="ebe18-511">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="ebe18-512">En la clase `Program` de la aplicación cliente (*Program.cs*), factorice los registros de servicio comunes en un método independiente (por ejemplo, `ConfigureCommonServices`):</span><span class="sxs-lookup"><span data-stu-id="ebe18-512">In the Client app's `Program` class (*Program.cs*), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

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

<span data-ttu-id="ebe18-513">En `Startup.ConfigureServices` de la aplicación de servidor, registre estos servicios adicionales:</span><span class="sxs-lookup"><span data-stu-id="ebe18-513">In the Server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

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

<span data-ttu-id="ebe18-514">En el método de la aplicación de servidor `Startup.Configure` , reemplace los [extremos. MapFallbackToFile ("index. html")](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) con [extremos. MapFallbackToPage ("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A):</span><span class="sxs-lookup"><span data-stu-id="ebe18-514">In the Server app's `Startup.Configure` method, replace [endpoints.MapFallbackToFile("index.html")](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) with [endpoints.MapFallbackToPage("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A):</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="ebe18-515">En la aplicación de servidor, cree una carpeta *Pages* si todavía no existe.</span><span class="sxs-lookup"><span data-stu-id="ebe18-515">In the Server app, create a *Pages* folder if it doesn't exist.</span></span> <span data-ttu-id="ebe18-516">Cree una página *_Host.cshtml* dentro de la carpeta *Pages* de la aplicación de servidor.</span><span class="sxs-lookup"><span data-stu-id="ebe18-516">Create a *_Host.cshtml* page inside the Server app's *Pages* folder.</span></span> <span data-ttu-id="ebe18-517">Pegue el contenido del archivo *wwwroot/index.html* de la aplicación cliente en el archivo *Pages/_Host.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="ebe18-517">Paste the contents from the Client app's *wwwroot/index.html* file into the *Pages/_Host.cshtml* file.</span></span> <span data-ttu-id="ebe18-518">Actualice el contenido del archivo:</span><span class="sxs-lookup"><span data-stu-id="ebe18-518">Update the file's contents:</span></span>

* <span data-ttu-id="ebe18-519">Agregue `@page "_Host"` a la parte superior del archivo.</span><span class="sxs-lookup"><span data-stu-id="ebe18-519">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="ebe18-520">Reemplace la etiqueta `<app>Loading...</app>` por la siguiente:</span><span class="sxs-lookup"><span data-stu-id="ebe18-520">Replace the `<app>Loading...</app>` tag with the following:</span></span>

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
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="ebe18-521">Opciones para aplicaciones hospedadas y proveedores de inicio de sesión de terceros</span><span class="sxs-lookup"><span data-stu-id="ebe18-521">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="ebe18-522">Al autenticar y autorizar una aplicación WebAssembly de Blazor con un proveedor de terceros, hay varias opciones disponibles para autenticar al usuario.</span><span class="sxs-lookup"><span data-stu-id="ebe18-522">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="ebe18-523">Su elección depende del escenario.</span><span class="sxs-lookup"><span data-stu-id="ebe18-523">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="ebe18-524">Para obtener más información, vea <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="ebe18-524">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="ebe18-525">Autenticación de usuarios solo para llamadas a las API de terceros protegidas</span><span class="sxs-lookup"><span data-stu-id="ebe18-525">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="ebe18-526">Autentique al usuario con un flujo de OAuth del lado cliente en el proveedor de la API de terceros:</span><span class="sxs-lookup"><span data-stu-id="ebe18-526">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="ebe18-527">En este escenario:</span><span class="sxs-lookup"><span data-stu-id="ebe18-527">In this scenario:</span></span>

* <span data-ttu-id="ebe18-528">El servidor que hospeda la aplicación no desempeña ningún rol.</span><span class="sxs-lookup"><span data-stu-id="ebe18-528">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="ebe18-529">No se pueden proteger las API en el servidor.</span><span class="sxs-lookup"><span data-stu-id="ebe18-529">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="ebe18-530">La aplicación solo puede llamar a las API de terceros protegidas.</span><span class="sxs-lookup"><span data-stu-id="ebe18-530">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="ebe18-531">Autenticación de usuarios con un proveedor de terceros y llamada a las API protegidas en el servidor host y en el tercero</span><span class="sxs-lookup"><span data-stu-id="ebe18-531">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="ebe18-532">Configure Identity con un proveedor de inicio de sesión de terceros.</span><span class="sxs-lookup"><span data-stu-id="ebe18-532">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="ebe18-533">Obtenga los tokens necesarios para el acceso de API de terceros y almacénelos.</span><span class="sxs-lookup"><span data-stu-id="ebe18-533">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="ebe18-534">Cuando un usuario inicia sesión, Identity recopila tokens de acceso y actualización como parte del proceso de autenticación.</span><span class="sxs-lookup"><span data-stu-id="ebe18-534">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="ebe18-535">En ese momento, hay un par de enfoques disponibles para realizar llamadas API a las API de terceros.</span><span class="sxs-lookup"><span data-stu-id="ebe18-535">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="ebe18-536">Uso de un token de acceso de servidor para recuperar el token de acceso de terceros</span><span class="sxs-lookup"><span data-stu-id="ebe18-536">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="ebe18-537">Use el token de acceso generado en el servidor para recuperar el token de acceso de terceros de un punto de conexión de la API de servidor.</span><span class="sxs-lookup"><span data-stu-id="ebe18-537">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="ebe18-538">Desde allí, use el token de acceso de terceros para llamar a recursos de API de terceros directamente desde Identity en el cliente.</span><span class="sxs-lookup"><span data-stu-id="ebe18-538">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="ebe18-539">Este enfoque no se recomienda.</span><span class="sxs-lookup"><span data-stu-id="ebe18-539">We don't recommend this approach.</span></span> <span data-ttu-id="ebe18-540">Este enfoque requiere tratar el token de acceso de terceros como si se hubiera generado para un cliente público.</span><span class="sxs-lookup"><span data-stu-id="ebe18-540">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="ebe18-541">En términos de OAuth, la aplicación pública no tiene un secreto de cliente porque no es de confianza para almacenar secretos de forma segura y el token de acceso se genera para un cliente confidencial.</span><span class="sxs-lookup"><span data-stu-id="ebe18-541">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="ebe18-542">Un cliente confidencial es un cliente que tiene un secreto de cliente y se supone que puede almacenar secretos de forma segura.</span><span class="sxs-lookup"><span data-stu-id="ebe18-542">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="ebe18-543">El token de acceso de terceros podría recibir ámbitos adicionales para realizar operaciones confidenciales en función del hecho de que la tercera parte emitió el token para un cliente de más confianza.</span><span class="sxs-lookup"><span data-stu-id="ebe18-543">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="ebe18-544">Del mismo modo, los tokens de actualización no deben emitirse para un cliente que no sea de confianza, ya que esto proporciona acceso ilimitado al cliente a menos que se apliquen otras restricciones.</span><span class="sxs-lookup"><span data-stu-id="ebe18-544">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="ebe18-545">Realización de llamadas API desde el cliente a la API de servidor para llamar a las API de terceros</span><span class="sxs-lookup"><span data-stu-id="ebe18-545">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="ebe18-546">Realice una llamada API desde el cliente a la API del servidor.</span><span class="sxs-lookup"><span data-stu-id="ebe18-546">Make an API call from the client to the server API.</span></span> <span data-ttu-id="ebe18-547">En el servidor, recupere el token de acceso para el recurso de la API de terceros y emita cualquier llamada que sea necesaria.</span><span class="sxs-lookup"><span data-stu-id="ebe18-547">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="ebe18-548">Aunque este enfoque requiere un salto de red adicional a través del servidor para llamar a una API de terceros, en última instancia resulta una experiencia más segura:</span><span class="sxs-lookup"><span data-stu-id="ebe18-548">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="ebe18-549">El servidor puede almacenar tokens de actualización y asegurarse de que la aplicación no pierde el acceso a recursos de terceros.</span><span class="sxs-lookup"><span data-stu-id="ebe18-549">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="ebe18-550">La aplicación no puede filtrar los tokens de acceso del servidor que puedan contener permisos más confidenciales.</span><span class="sxs-lookup"><span data-stu-id="ebe18-550">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>

## <a name="use-open-id-connect-oidc-v20-endpoints"></a><span data-ttu-id="ebe18-551">Usar puntos de conexión v 2.0 de Open ID Connect (OIDC)</span><span class="sxs-lookup"><span data-stu-id="ebe18-551">Use Open ID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="ebe18-552">La biblioteca de autenticación y Blazor las plantillas usan puntos de conexión de Open ID Connect (OIDC) v 1.0.</span><span class="sxs-lookup"><span data-stu-id="ebe18-552">The authentication library and Blazor templates use Open ID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="ebe18-553">Para usar un punto de conexión v 2.0, configure la <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> opción portador JWT.</span><span class="sxs-lookup"><span data-stu-id="ebe18-553">To use a v2.0 endpoint, configure the JWT Bearer <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> option.</span></span> <span data-ttu-id="ebe18-554">En el ejemplo siguiente, AAD se configura para v 2.0 anexando un `v2.0` segmento a la <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> propiedad:</span><span class="sxs-lookup"><span data-stu-id="ebe18-554">In the following example, AAD is configured for v2.0 by appending a `v2.0` segment to the <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> property:</span></span>

```csharp
builder.Services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    });
```

<span data-ttu-id="ebe18-555">Como alternativa, la configuración se puede realizar en el archivo de configuración de la aplicación (*appSettings. JSON*):</span><span class="sxs-lookup"><span data-stu-id="ebe18-555">Alternatively, the setting can be made in the app settings (*appsettings.json*) file:</span></span>

```json
{
  "Local": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="ebe18-556">Si el seguimiento de un segmento a la autoridad no es apropiado para el proveedor de OIDC de la aplicación, como con proveedores que no son de AAD, establezca la <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> propiedad directamente.</span><span class="sxs-lookup"><span data-stu-id="ebe18-556">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> property directly.</span></span> <span data-ttu-id="ebe18-557">Establezca la propiedad en <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> o en el archivo de configuración de la aplicación (*appSettings. JSON*) con la `Authority` clave.</span><span class="sxs-lookup"><span data-stu-id="ebe18-557">Either set the property in <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> or in the app settings file (*appsettings.json*) with the `Authority` key.</span></span>

<span data-ttu-id="ebe18-558">La lista de notificaciones en el token de identificador cambia para los puntos de conexión v 2.0.</span><span class="sxs-lookup"><span data-stu-id="ebe18-558">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="ebe18-559">Para obtener más información, consulte [¿por qué actualizar a Microsoft Identity Platform (v 2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span><span class="sxs-lookup"><span data-stu-id="ebe18-559">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span></span>
