---
<span data-ttu-id="ab43d-101">Título: ' ASP.NET Core Blazor Webassembly escenarios de seguridad adicionales ' Author: guardrex Description: ' obtener información sobre cómo configurar Blazor webassembly para escenarios de seguridad adicionales '.</span><span class="sxs-lookup"><span data-stu-id="ab43d-101">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: guardrex description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="ab43d-102">monikerRange: ' >= aspnetcore-3,1 ' ms. Author: Riande ms. Custom: MVC ms. Date: 06/01/2020 no-LOC:</span><span class="sxs-lookup"><span data-stu-id="ab43d-102">monikerRange: '>= aspnetcore-3.1' ms.author: riande ms.custom: mvc ms.date: 06/01/2020 no-loc:</span></span>
- <span data-ttu-id="ab43d-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ab43d-103">'Blazor'</span></span>
- <span data-ttu-id="ab43d-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ab43d-104">'Identity'</span></span>
- <span data-ttu-id="ab43d-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ab43d-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="ab43d-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ab43d-106">'Razor'</span></span>
- <span data-ttu-id="ab43d-107">' SignalR ' UID: Security/increíbles/webassembly/escenarios adicionales</span><span class="sxs-lookup"><span data-stu-id="ab43d-107">'SignalR' uid: security/blazor/webassembly/additional-scenarios</span></span>

---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="ab43d-108">ASP.NET Core Blazor escenarios de seguridad adicionales de Webassembly</span><span class="sxs-lookup"><span data-stu-id="ab43d-108">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="ab43d-109">Por [Javier Calvarro Nelson](https://github.com/javiercn) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="ab43d-109">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="attach-tokens-to-outgoing-requests"></a><span data-ttu-id="ab43d-110">Adjuntar tokens a solicitudes salientes</span><span class="sxs-lookup"><span data-stu-id="ab43d-110">Attach tokens to outgoing requests</span></span>

<span data-ttu-id="ab43d-111">El <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> servicio se puede usar con <xref:System.Net.Http.HttpClient> para adjuntar tokens de acceso a las solicitudes salientes.</span><span class="sxs-lookup"><span data-stu-id="ab43d-111">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> service can be used with <xref:System.Net.Http.HttpClient> to attach access tokens to outgoing requests.</span></span> <span data-ttu-id="ab43d-112">Los tokens se adquieren con el <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> servicio existente.</span><span class="sxs-lookup"><span data-stu-id="ab43d-112">Tokens are acquired using the existing <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> service.</span></span> <span data-ttu-id="ab43d-113">Si no se puede adquirir un token, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> se produce una excepción.</span><span class="sxs-lookup"><span data-stu-id="ab43d-113">If a token can't be acquired, an <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> is thrown.</span></span> <span data-ttu-id="ab43d-114"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException>tiene un <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> método que se puede utilizar para navegar por el usuario al proveedor de identidades para adquirir un nuevo token.</span><span class="sxs-lookup"><span data-stu-id="ab43d-114"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> has a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> method that can be used to navigate the user to the identity provider to acquire a new token.</span></span> <span data-ttu-id="ab43d-115"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler>Se puede configurar con las direcciones URL autorizadas, los ámbitos y la dirección URL de retorno mediante el <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> método.</span><span class="sxs-lookup"><span data-stu-id="ab43d-115">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> can be configured with the authorized URLs, scopes, and return URL using the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> method.</span></span>

<span data-ttu-id="ab43d-116">Use cualquiera de los métodos siguientes para configurar un controlador de mensajes para las solicitudes salientes:</span><span class="sxs-lookup"><span data-stu-id="ab43d-116">Use either of the following approaches to configure a message handler for outgoing requests:</span></span>

* <span data-ttu-id="ab43d-117">[Clase AuthorizationMessageHandler personalizada](#custom-authorizationmessagehandler-class) (*recomendado*)</span><span class="sxs-lookup"><span data-stu-id="ab43d-117">[Custom AuthorizationMessageHandler class](#custom-authorizationmessagehandler-class) (*Recommended*)</span></span>
* [<span data-ttu-id="ab43d-118">Configuración de AuthorizationMessageHandler</span><span class="sxs-lookup"><span data-stu-id="ab43d-118">Configure AuthorizationMessageHandler</span></span>](#configure-authorizationmessagehandler)

### <a name="custom-authorizationmessagehandler-class"></a><span data-ttu-id="ab43d-119">Clase AuthorizationMessageHandler personalizada</span><span class="sxs-lookup"><span data-stu-id="ab43d-119">Custom AuthorizationMessageHandler class</span></span>

<span data-ttu-id="ab43d-120">En el ejemplo siguiente, una clase personalizada extiende <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> que se puede usar para configurar un <xref:System.Net.Http.HttpClient> :</span><span class="sxs-lookup"><span data-stu-id="ab43d-120">In the following example, a custom class extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> that can be used to configure an <xref:System.Net.Http.HttpClient>:</span></span>

```csharp
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomAuthorizationMessageHandler : AuthorizationMessageHandler
{
    public CustomAuthorizationMessageHandler(IAccessTokenProvider provider, 
        NavigationManager navigationManager)
        : base(provider, navigationManager)
    {
        ConfigureHandler(
            authorizedUrls: new[] { "https://www.example.com/base" },
            scopes: new[] { "example.read", "example.write" });
    }
}
```

<span data-ttu-id="ab43d-121">En `Program.Main` (*Program.CS*), <xref:System.Net.Http.HttpClient> se configura con el controlador de mensajes de autorización personalizado:</span><span class="sxs-lookup"><span data-stu-id="ab43d-121">In `Program.Main` (*Program.cs*), an <xref:System.Net.Http.HttpClient> is configured with the custom authorization message handler:</span></span>

```csharp
builder.Services.AddTransient<CustomAuthorizationMessageHandler>();

builder.Services.AddHttpClient("ServerAPI",
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
        .AddHttpMessageHandler<CustomAuthorizationMessageHandler>();
```

<span data-ttu-id="ab43d-122">El configurado <xref:System.Net.Http.HttpClient> se usa para realizar solicitudes autorizadas mediante el patrón [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) .</span><span class="sxs-lookup"><span data-stu-id="ab43d-122">The configured <xref:System.Net.Http.HttpClient> is used to make authorized requests using the [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) pattern.</span></span> <span data-ttu-id="ab43d-123">Donde se crea el cliente con <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> (paquete[Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) ), <xref:System.Net.Http.HttpClient> se proporcionan instancias de que incluyen tokens de acceso al hacer solicitudes a la API del servidor:</span><span class="sxs-lookup"><span data-stu-id="ab43d-123">Where the client is created with <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> ([Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) package), the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server API:</span></span>

```razor
@inject IHttpClientFactory ClientFactory

...

@code {
    private ExampleType[] examples;

    protected override async Task OnInitializedAsync()
    {
        try
        {
            var client = ClientFactory.CreateClient("ServerAPI");

            examples = 
                await client.GetFromJsonAsync<ExampleType[]>("{API METHOD}");

            ...
        }
        catch (AccessTokenNotAvailableException exception)
        {
            exception.Redirect();
        }
        
    }
}
```

### <a name="configure-authorizationmessagehandler"></a><span data-ttu-id="ab43d-124">Configuración de AuthorizationMessageHandler</span><span class="sxs-lookup"><span data-stu-id="ab43d-124">Configure AuthorizationMessageHandler</span></span>

<span data-ttu-id="ab43d-125">En el ejemplo siguiente, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> configura <xref:System.Net.Http.HttpClient> en `Program.Main` (*Program.CS*):</span><span class="sxs-lookup"><span data-stu-id="ab43d-125">In the following example, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> configures an <xref:System.Net.Http.HttpClient> in `Program.Main` (*Program.cs*):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddTransient(sp =>
{
    return new HttpClient(sp.GetRequiredService<AuthorizationMessageHandler>()
        .ConfigureHandler(
            authorizedUrls: new [] { "https://www.example.com/base" },
            scopes: new[] { "example.read", "example.write" }))
        {
            BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
        };
});
```

<span data-ttu-id="ab43d-126">Para mayor comodidad, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> se incluye una que está preconfigurada con la dirección base de la aplicación como una dirección URL autorizada.</span><span class="sxs-lookup"><span data-stu-id="ab43d-126">For convenience, a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> is included that's preconfigured with the app base address as an authorized URL.</span></span> <span data-ttu-id="ab43d-127">Las plantillas de webassembly habilitadas para la autenticación Blazor ahora usan <xref:System.Net.Http.IHttpClientFactory> (paquete[Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) ) en el proyecto de API de servidor para configurar una <xref:System.Net.Http.HttpClient> con <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> :</span><span class="sxs-lookup"><span data-stu-id="ab43d-127">The authentication-enabled Blazor WebAssembly templates now use <xref:System.Net.Http.IHttpClientFactory> ([Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) package) in the Server API project to set up an <xref:System.Net.Http.HttpClient> with the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler>:</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient("ServerAPI", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
        .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("ServerAPI"));
```

<span data-ttu-id="ab43d-128">Cuando el cliente se crea con <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> en el ejemplo anterior, <xref:System.Net.Http.HttpClient> se proporcionan instancias de que incluyen tokens de acceso al hacer solicitudes al proyecto de servidor.</span><span class="sxs-lookup"><span data-stu-id="ab43d-128">Where the client is created with <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> in the preceding example, the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="ab43d-129">El configurado <xref:System.Net.Http.HttpClient> se usa para realizar solicitudes autorizadas mediante el patrón [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) :</span><span class="sxs-lookup"><span data-stu-id="ab43d-129">The configured <xref:System.Net.Http.HttpClient> is used to make authorized requests using the [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) pattern:</span></span>

```razor
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject HttpClient Client

...

protected override async Task OnInitializedAsync()
{
    private ExampleType[] examples;

    try
    {
        examples = 
            await Client.GetFromJsonAsync<ExampleType[]>("{API METHOD}");

        ...
    }
    catch (AccessTokenNotAvailableException exception)
    {
        exception.Redirect();
    }
}
```

## <a name="typed-httpclient"></a><span data-ttu-id="ab43d-130">HttpClient con tipo</span><span class="sxs-lookup"><span data-stu-id="ab43d-130">Typed HttpClient</span></span>

<span data-ttu-id="ab43d-131">Se puede definir un cliente con tipo que controle todos los problemas de adquisición de tokens y HTTP en una sola clase.</span><span class="sxs-lookup"><span data-stu-id="ab43d-131">A typed client can be defined that handles all of the HTTP and token acquisition concerns within a single class.</span></span>

<span data-ttu-id="ab43d-132">*WeatherForecastClient.cs*:</span><span class="sxs-lookup"><span data-stu-id="ab43d-132">*WeatherForecastClient.cs*:</span></span>

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

<span data-ttu-id="ab43d-133">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="ab43d-133">`Program.Main` (*Program.cs*):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherForecastClient>(
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

<span data-ttu-id="ab43d-134">Componente `FetchData`(*Pages/FetchData.razor*):</span><span class="sxs-lookup"><span data-stu-id="ab43d-134">`FetchData` component (*Pages/FetchData.razor*):</span></span>

```razor
@inject WeatherForecastClient Client

...

protected override async Task OnInitializedAsync()
{
    forecasts = await Client.GetForecastAsync();
}
```

## <a name="configure-the-httpclient-handler"></a><span data-ttu-id="ab43d-135">Configurar el controlador HttpClient</span><span class="sxs-lookup"><span data-stu-id="ab43d-135">Configure the HttpClient handler</span></span>

<span data-ttu-id="ab43d-136">El controlador se puede configurar aún más con <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> para las solicitudes HTTP salientes.</span><span class="sxs-lookup"><span data-stu-id="ab43d-136">The handler can be further configured with <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> for outbound HTTP requests.</span></span>

<span data-ttu-id="ab43d-137">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="ab43d-137">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler(sp => sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(new [] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }));
```

## <a name="unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client"></a><span data-ttu-id="ab43d-138">Solicitudes de API Web no autenticadas o no autorizadas en una aplicación con un cliente predeterminado seguro</span><span class="sxs-lookup"><span data-stu-id="ab43d-138">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>

<span data-ttu-id="ab43d-139">Si la Blazor aplicación Webassembly usa normalmente un valor predeterminado seguro <xref:System.Net.Http.HttpClient> , la aplicación también puede realizar solicitudes de API Web no autenticadas o no autorizadas mediante la configuración de una llamada <xref:System.Net.Http.HttpClient> :</span><span class="sxs-lookup"><span data-stu-id="ab43d-139">If the Blazor WebAssembly app ordinarily uses a secure default <xref:System.Net.Http.HttpClient>, the app can also make unauthenticated or unauthorized web API requests by configuring a named <xref:System.Net.Http.HttpClient>:</span></span>

<span data-ttu-id="ab43d-140">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="ab43d-140">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI.NoAuthenticationClient", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="ab43d-141">El registro anterior se suma al registro predeterminado seguro existente <xref:System.Net.Http.HttpClient> .</span><span class="sxs-lookup"><span data-stu-id="ab43d-141">The preceding registration is in addition to the existing secure default <xref:System.Net.Http.HttpClient> registration.</span></span>

<span data-ttu-id="ab43d-142">Un componente crea el a <xref:System.Net.Http.HttpClient> partir del <xref:System.Net.Http.IHttpClientFactory> paquete ([Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) ) para realizar solicitudes no autenticadas o no autorizadas:</span><span class="sxs-lookup"><span data-stu-id="ab43d-142">A component creates the <xref:System.Net.Http.HttpClient> from the <xref:System.Net.Http.IHttpClientFactory> ([Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) package) to make unauthenticated or unauthorized requests:</span></span>

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
> <span data-ttu-id="ab43d-143">El controlador de la API de servidor, `WeatherForecastNoAuthenticationController` para el ejemplo anterior, no está marcado con el [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atributo.</span><span class="sxs-lookup"><span data-stu-id="ab43d-143">The controller in the server API, `WeatherForecastNoAuthenticationController` for the preceding example, isn't marked with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute.</span></span>

## <a name="request-additional-access-tokens"></a><span data-ttu-id="ab43d-144">Solicitar tokens de acceso adicionales</span><span class="sxs-lookup"><span data-stu-id="ab43d-144">Request additional access tokens</span></span>

<span data-ttu-id="ab43d-145">Los tokens de acceso se pueden obtener manualmente mediante una llamada a `IAccessTokenProvider.RequestAccessToken` .</span><span class="sxs-lookup"><span data-stu-id="ab43d-145">Access tokens can be manually obtained by calling `IAccessTokenProvider.RequestAccessToken`.</span></span>

<span data-ttu-id="ab43d-146">En el siguiente ejemplo, una aplicación necesita los ámbitos de API de Azure Active Directory adicionales (AAD) Microsoft Graph para leer los datos de usuario y enviar correo.</span><span class="sxs-lookup"><span data-stu-id="ab43d-146">In the following example, additional Azure Active Directory (AAD) Microsoft Graph API scopes are required by an app to read user data and send mail.</span></span> <span data-ttu-id="ab43d-147">Después de agregar los permisos de Microsoft Graph API en el portal de Azure AAD, los ámbitos adicionales se configuran en la aplicación cliente.</span><span class="sxs-lookup"><span data-stu-id="ab43d-147">After adding the Microsoft Graph API permissions in the Azure AAD portal, the additional scopes are configured in the Client app.</span></span>

<span data-ttu-id="ab43d-148">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="ab43d-148">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="ab43d-149">El `IAccessTokenProvider.RequestToken` método proporciona una sobrecarga que permite a una aplicación aprovisionar un token de acceso con un conjunto determinado de ámbitos.</span><span class="sxs-lookup"><span data-stu-id="ab43d-149">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision an access token with a given set of scopes.</span></span>

<span data-ttu-id="ab43d-150">En un Razor componente:</span><span class="sxs-lookup"><span data-stu-id="ab43d-150">In a Razor component:</span></span>

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

<span data-ttu-id="ab43d-151"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType>Devuelve</span><span class="sxs-lookup"><span data-stu-id="ab43d-151"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType> returns:</span></span>

* <span data-ttu-id="ab43d-152">`true`con `token` para su uso.</span><span class="sxs-lookup"><span data-stu-id="ab43d-152">`true` with the `token` for use.</span></span>
* <span data-ttu-id="ab43d-153">`false`Si el token no se recupera.</span><span class="sxs-lookup"><span data-stu-id="ab43d-153">`false` if the token isn't retrieved.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="ab43d-154">HttpClient y HttpRequestMessage con las opciones de solicitud de Fetch API</span><span class="sxs-lookup"><span data-stu-id="ab43d-154">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="ab43d-155">Al ejecutar webassembly en una Blazor aplicación Webassembly, [HttpClient](xref:fundamentals/http-requests) y <xref:System.Net.Http.HttpRequestMessage> se puede usar para personalizar las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="ab43d-155">When running on WebAssembly in a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) and <xref:System.Net.Http.HttpRequestMessage> can be used to customize requests.</span></span> <span data-ttu-id="ab43d-156">Por ejemplo, puede especificar el método HTTP y los encabezados de solicitud.</span><span class="sxs-lookup"><span data-stu-id="ab43d-156">For example, you can specify the HTTP method and request headers.</span></span> <span data-ttu-id="ab43d-157">El componente siguiente realiza una `POST` solicitud a un punto de conexión de API de lista de tareas pendientes en el servidor y muestra el cuerpo de la respuesta:</span><span class="sxs-lookup"><span data-stu-id="ab43d-157">The following component makes a `POST` request to a To Do List API endpoint on the server and shows the response body:</span></span>

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

<span data-ttu-id="ab43d-158">En la implementación de <xref:System.Net.Http.HttpClient> de WebAssembly de .NET se usa [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span><span class="sxs-lookup"><span data-stu-id="ab43d-158">.NET WebAssembly's implementation of <xref:System.Net.Http.HttpClient> uses [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span></span> <span data-ttu-id="ab43d-159">Fetch permite configurar varias [opciones específicas de la solicitud](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="ab43d-159">Fetch allows configuring several [request-specific options](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span> 

<span data-ttu-id="ab43d-160">Se pueden configurar opciones de solicitud de captura HTTP con los métodos de extensión <xref:System.Net.Http.HttpRequestMessage> que se muestran en la siguiente tabla.</span><span class="sxs-lookup"><span data-stu-id="ab43d-160">HTTP fetch request options can be configured with <xref:System.Net.Http.HttpRequestMessage> extension methods shown in the following table.</span></span>

| <span data-ttu-id="ab43d-161">Método de extensión</span><span class="sxs-lookup"><span data-stu-id="ab43d-161">Extension method</span></span> | <span data-ttu-id="ab43d-162">Propiedad de solicitud de captura</span><span class="sxs-lookup"><span data-stu-id="ab43d-162">Fetch request property</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> | [<span data-ttu-id="ab43d-163">credentials</span><span class="sxs-lookup"><span data-stu-id="ab43d-163">credentials</span></span>](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCache%2A> | [<span data-ttu-id="ab43d-164">cache</span><span class="sxs-lookup"><span data-stu-id="ab43d-164">cache</span></span>](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestMode%2A> | [<span data-ttu-id="ab43d-165">mode</span><span class="sxs-lookup"><span data-stu-id="ab43d-165">mode</span></span>](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestIntegrity%2A> | [<span data-ttu-id="ab43d-166">integrity</span><span class="sxs-lookup"><span data-stu-id="ab43d-166">integrity</span></span>](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

<span data-ttu-id="ab43d-167">Se pueden establecer más opciones usando el método de extensión <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A>, que es más genérico.</span><span class="sxs-lookup"><span data-stu-id="ab43d-167">You can set additional options using the more generic <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> extension method.</span></span>
 
<span data-ttu-id="ab43d-168">Normalmente, la respuesta HTTP se almacena en búfer en una aplicación WebAssembly de Blazor para habilitar la compatibilidad con las lecturas de sincronización en el contenido de la respuesta.</span><span class="sxs-lookup"><span data-stu-id="ab43d-168">The HTTP response is typically buffered in a Blazor WebAssembly app to enable support for sync reads on the response content.</span></span> <span data-ttu-id="ab43d-169">Para habilitar la compatibilidad con la transmisión de respuestas, use el método de extensión <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> en la solicitud.</span><span class="sxs-lookup"><span data-stu-id="ab43d-169">To enable support for response streaming, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> extension method on the request.</span></span>

<span data-ttu-id="ab43d-170">Para incluir credenciales en una solicitud entre orígenes, use el método de extensión <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A>:</span><span class="sxs-lookup"><span data-stu-id="ab43d-170">To include credentials in a cross-origin request, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> extension method:</span></span>

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

<span data-ttu-id="ab43d-171">Para obtener más información sobre las opciones de Fetch API, vea [Documentación web de MDN: WindowOrWorkerGlobalScope.fetch(): parámetros](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="ab43d-171">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

<span data-ttu-id="ab43d-172">Al enviar credenciales (cookies de autorización o encabezados) en solicitudes de CORS, la directiva de CORS debe permitir el encabezado `Authorization`.</span><span class="sxs-lookup"><span data-stu-id="ab43d-172">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="ab43d-173">En la directiva siguiente se incluye la configuración de:</span><span class="sxs-lookup"><span data-stu-id="ab43d-173">The following policy includes configuration for:</span></span>

* <span data-ttu-id="ab43d-174">Orígenes de la solicitud (`http://localhost:5000`, `https://localhost:5001`).</span><span class="sxs-lookup"><span data-stu-id="ab43d-174">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="ab43d-175">Cualquier método (verbo).</span><span class="sxs-lookup"><span data-stu-id="ab43d-175">Any method (verb).</span></span>
* <span data-ttu-id="ab43d-176">Los encabezados `Content-Type` y `Authorization`.</span><span class="sxs-lookup"><span data-stu-id="ab43d-176">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="ab43d-177">Para permitir un encabezado personalizado (por ejemplo, `x-custom-header`), enumere el encabezado al llamar a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span><span class="sxs-lookup"><span data-stu-id="ab43d-177">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="ab43d-178">Las credenciales establecidas por el código JavaScript del lado cliente (la propiedad `credentials` establecida en `include`).</span><span class="sxs-lookup"><span data-stu-id="ab43d-178">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="ab43d-179">Para obtener más información, vea <xref:security/cors> y el componente del evaluador de solicitudes HTTP de la aplicación de ejemplo (*Components/HTTPRequestTester.razor*).</span><span class="sxs-lookup"><span data-stu-id="ab43d-179">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (*Components/HTTPRequestTester.razor*).</span></span>

## <a name="handle-token-request-errors"></a><span data-ttu-id="ab43d-180">Controlar errores de solicitudes de token</span><span class="sxs-lookup"><span data-stu-id="ab43d-180">Handle token request errors</span></span>

<span data-ttu-id="ab43d-181">Cuando una aplicación de una sola página (SPA) autentica a un usuario mediante Open ID Connect (OIDC), el estado de autenticación se mantiene localmente en el SPA y en el Identity proveedor (IP) en forma de cookie de sesión que se establece como resultado del usuario que proporciona sus credenciales.</span><span class="sxs-lookup"><span data-stu-id="ab43d-181">When a Single Page Application (SPA) authenticates a user using Open ID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="ab43d-182">Normalmente, los tokens que emite la IP para el usuario son válidos durante breves períodos de tiempo, aproximadamente una hora, por lo que la aplicación cliente debe capturar nuevos tokens con regularidad.</span><span class="sxs-lookup"><span data-stu-id="ab43d-182">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="ab43d-183">De lo contrario, se cerrará la sesión del usuario después de que expiren los tokens concedidos.</span><span class="sxs-lookup"><span data-stu-id="ab43d-183">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="ab43d-184">En la mayoría de los casos, los clientes de OIDC pueden aprovisionar nuevos tokens sin necesidad de que el usuario se autentique de nuevo gracias al estado de autenticación o a la "sesión" que se mantiene dentro de la dirección IP.</span><span class="sxs-lookup"><span data-stu-id="ab43d-184">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="ab43d-185">Hay algunos casos en los que el cliente no puede obtener un token sin la interacción del usuario, por ejemplo, cuando por alguna razón el usuario cierra la sesión explícitamente desde la dirección IP.</span><span class="sxs-lookup"><span data-stu-id="ab43d-185">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="ab43d-186">Este escenario se produce si un usuario visita `https://login.microsoftonline.com` y cierra la sesión. En estos casos, la aplicación no sabe inmediatamente que el usuario ha cerrado la sesión. Es posible que el token que contiene el cliente ya no sea válido.</span><span class="sxs-lookup"><span data-stu-id="ab43d-186">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="ab43d-187">Además, el cliente no puede aprovisionar un token nuevo sin la interacción del usuario después de que expire el token actual.</span><span class="sxs-lookup"><span data-stu-id="ab43d-187">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="ab43d-188">Estos escenarios no son específicos de la autenticación basada en tokens.</span><span class="sxs-lookup"><span data-stu-id="ab43d-188">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="ab43d-189">Forman parte de la naturaleza de spa.</span><span class="sxs-lookup"><span data-stu-id="ab43d-189">They are part of the nature of SPAs.</span></span> <span data-ttu-id="ab43d-190">Un SPA que use cookies tampoco podrá llamar a una API de servidor si se quita la cookie de autenticación.</span><span class="sxs-lookup"><span data-stu-id="ab43d-190">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="ab43d-191">Cuando una aplicación realiza llamadas API a recursos protegidos, debe tener en cuenta lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="ab43d-191">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="ab43d-192">Para aprovisionar un nuevo token de acceso para llamar a la API, es posible que sea necesario volver a autenticar al usuario.</span><span class="sxs-lookup"><span data-stu-id="ab43d-192">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="ab43d-193">Incluso si el cliente tiene un token que parece ser válido, es posible que se produzca un error en la llamada al servidor porque el usuario revocó el token.</span><span class="sxs-lookup"><span data-stu-id="ab43d-193">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="ab43d-194">Cuando la aplicación solicita un token, hay dos resultados posibles:</span><span class="sxs-lookup"><span data-stu-id="ab43d-194">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="ab43d-195">La solicitud se realiza correctamente y la aplicación tiene un token válido.</span><span class="sxs-lookup"><span data-stu-id="ab43d-195">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="ab43d-196">Se produce un error en la solicitud y la aplicación debe autenticar al usuario de nuevo para obtener un nuevo token.</span><span class="sxs-lookup"><span data-stu-id="ab43d-196">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="ab43d-197">Cuando se produce un error en una solicitud de token, debe decidir si desea guardar el estado actual antes de realizar una redirección.</span><span class="sxs-lookup"><span data-stu-id="ab43d-197">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="ab43d-198">Existen varios enfoques con niveles crecientes de complejidad:</span><span class="sxs-lookup"><span data-stu-id="ab43d-198">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="ab43d-199">Almacenar el estado de la página actual en el almacenamiento de sesión.</span><span class="sxs-lookup"><span data-stu-id="ab43d-199">Store the current page state in session storage.</span></span> <span data-ttu-id="ab43d-200">Durante el [evento del ciclo de vida de OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods) ( <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> ), compruebe si el estado se puede restaurar antes de continuar.</span><span class="sxs-lookup"><span data-stu-id="ab43d-200">During the [OnInitializedAsync lifecycle event](xref:blazor/lifecycle#component-initialization-methods) (<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>), check if state can be restored before continuing.</span></span>
* <span data-ttu-id="ab43d-201">Agregue un parámetro de cadena de consulta y úselo como una manera de señalar la aplicación que necesita para volver a hidratar el estado guardado anteriormente.</span><span class="sxs-lookup"><span data-stu-id="ab43d-201">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="ab43d-202">Agregue un parámetro de cadena de consulta con un identificador único para almacenar los datos en el almacenamiento de sesión sin poner en peligro las colisiones con otros elementos.</span><span class="sxs-lookup"><span data-stu-id="ab43d-202">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="ab43d-203">El ejemplo siguiente muestra cómo:</span><span class="sxs-lookup"><span data-stu-id="ab43d-203">The following example shows how to:</span></span>

* <span data-ttu-id="ab43d-204">Conservar el estado antes de redirigir a la página de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="ab43d-204">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="ab43d-205">Recupere el estado anterior después de la autenticación mediante el parámetro de cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="ab43d-205">Recover the previous state afterward authentication using the query string parameter.</span></span>

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

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="ab43d-206">Guardar el estado de la aplicación antes de una operación de autenticación</span><span class="sxs-lookup"><span data-stu-id="ab43d-206">Save app state before an authentication operation</span></span>

<span data-ttu-id="ab43d-207">Durante una operación de autenticación, hay casos en los que desea guardar el estado de la aplicación antes de que el explorador se redirija a la dirección IP.</span><span class="sxs-lookup"><span data-stu-id="ab43d-207">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="ab43d-208">Esto puede ser así cuando se usa algo como un contenedor de estado y se desea restaurar el estado después de que la autenticación se realice correctamente.</span><span class="sxs-lookup"><span data-stu-id="ab43d-208">This can be the case when you are using something like a state container and you want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="ab43d-209">Puede usar un objeto de estado de autenticación personalizado para conservar el estado específico de la aplicación o una referencia a él y restaurar ese estado una vez que la operación de autenticación se complete correctamente.</span><span class="sxs-lookup"><span data-stu-id="ab43d-209">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state once the authentication operation successfully completes.</span></span>

<span data-ttu-id="ab43d-210">`Authentication`componente (*páginas/autenticación. Razor*):</span><span class="sxs-lookup"><span data-stu-id="ab43d-210">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/authentication/{action}"
@inject JSRuntime JS
@inject StateContainer State
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorViewCore Action="@Action" 
    AuthenticationState="AuthenticationState" OnLogInSucceeded="RestoreState" 
    OnLogOutSucceeded="RestoreState" />

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

## <a name="customize-app-routes"></a><span data-ttu-id="ab43d-211">Personalización de rutas de aplicación</span><span class="sxs-lookup"><span data-stu-id="ab43d-211">Customize app routes</span></span>

<span data-ttu-id="ab43d-212">De forma predeterminada, la biblioteca [Microsoft. AspNetCore. Components. Webassembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) usa las rutas que se muestran en la tabla siguiente para representar distintos Estados de autenticación.</span><span class="sxs-lookup"><span data-stu-id="ab43d-212">By default, the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="ab43d-213">Ruta</span><span class="sxs-lookup"><span data-stu-id="ab43d-213">Route</span></span>                            | <span data-ttu-id="ab43d-214">Propósito</span><span class="sxs-lookup"><span data-stu-id="ab43d-214">Purpose</span></span> |
| -------------------------------- | ------- |
| `authentication/login`           | <span data-ttu-id="ab43d-215">Desencadena una operación de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="ab43d-215">Triggers a sign-in operation.</span></span> |
| `authentication/login-callback`  | <span data-ttu-id="ab43d-216">Controla el resultado de cualquier operación de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="ab43d-216">Handles the result of any sign-in operation.</span></span> |
| `authentication/login-failed`    | <span data-ttu-id="ab43d-217">Muestra mensajes de error cuando se produce un error en la operación de inicio de sesión por algún motivo.</span><span class="sxs-lookup"><span data-stu-id="ab43d-217">Displays error messages when the sign-in operation fails for some reason.</span></span> |
| `authentication/logout`          | <span data-ttu-id="ab43d-218">Desencadena una operación de cierre de sesión.</span><span class="sxs-lookup"><span data-stu-id="ab43d-218">Triggers a sign-out operation.</span></span> |
| `authentication/logout-callback` | <span data-ttu-id="ab43d-219">Controla el resultado de una operación de cierre de sesión.</span><span class="sxs-lookup"><span data-stu-id="ab43d-219">Handles the result of a sign-out operation.</span></span> |
| `authentication/logout-failed`   | <span data-ttu-id="ab43d-220">Muestra mensajes de error cuando se produce un error en la operación de cierre de sesión por algún motivo.</span><span class="sxs-lookup"><span data-stu-id="ab43d-220">Displays error messages when the sign-out operation fails for some reason.</span></span> |
| `authentication/logged-out`      | <span data-ttu-id="ab43d-221">Indica que el usuario ha cerrado la sesión correctamente.</span><span class="sxs-lookup"><span data-stu-id="ab43d-221">Indicates that the user has successfully logout.</span></span> |
| `authentication/profile`         | <span data-ttu-id="ab43d-222">Desencadena una operación para editar el perfil de usuario.</span><span class="sxs-lookup"><span data-stu-id="ab43d-222">Triggers an operation to edit the user profile.</span></span> |
| `authentication/register`        | <span data-ttu-id="ab43d-223">Desencadena una operación para registrar un nuevo usuario.</span><span class="sxs-lookup"><span data-stu-id="ab43d-223">Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="ab43d-224">Las rutas que se muestran en la tabla anterior se pueden configurar mediante <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="ab43d-224">The routes shown in the preceding table are configurable via <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="ab43d-225">Al establecer las opciones para proporcionar rutas personalizadas, confirme que la aplicación tiene una ruta que controla cada ruta de acceso.</span><span class="sxs-lookup"><span data-stu-id="ab43d-225">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="ab43d-226">En el ejemplo siguiente, todas las rutas de acceso tienen el prefijo `/security` .</span><span class="sxs-lookup"><span data-stu-id="ab43d-226">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="ab43d-227">`Authentication`componente (*páginas/autenticación. Razor*):</span><span class="sxs-lookup"><span data-stu-id="ab43d-227">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="ab43d-228">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="ab43d-228">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="ab43d-229">Si el requisito requiere rutas de acceso completamente diferentes, establezca las rutas como se describió anteriormente y represente el <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> con un parámetro de acción explícito:</span><span class="sxs-lookup"><span data-stu-id="ab43d-229">If the requirement calls for completely different paths, set the routes as described previously and render the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="ab43d-230">Puede descomponer la interfaz de usuario en diferentes páginas si decide hacerlo.</span><span class="sxs-lookup"><span data-stu-id="ab43d-230">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="ab43d-231">Personalización de la interfaz de usuario de autenticación</span><span class="sxs-lookup"><span data-stu-id="ab43d-231">Customize the authentication user interface</span></span>

<span data-ttu-id="ab43d-232"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView>incluye un conjunto predeterminado de partes de la interfaz de usuario para cada estado de autenticación.</span><span class="sxs-lookup"><span data-stu-id="ab43d-232"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="ab43d-233">Cada Estado se puede personalizar pasando un personalizado <xref:Microsoft.AspNetCore.Components.RenderFragment> .</span><span class="sxs-lookup"><span data-stu-id="ab43d-233">Each state can be customized by passing in a custom <xref:Microsoft.AspNetCore.Components.RenderFragment>.</span></span> <span data-ttu-id="ab43d-234">Para personalizar el texto mostrado durante el proceso de inicio de sesión inicial, puede cambiar el de la <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> manera siguiente.</span><span class="sxs-lookup"><span data-stu-id="ab43d-234">To customize the displayed text during the initial login process, can change the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> as follows.</span></span>

<span data-ttu-id="ab43d-235">`Authentication`componente (*páginas/autenticación. Razor*):</span><span class="sxs-lookup"><span data-stu-id="ab43d-235">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

<span data-ttu-id="ab43d-236"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView>Tiene un fragmento que se puede utilizar por cada ruta de autenticación que se muestra en la tabla siguiente.</span><span class="sxs-lookup"><span data-stu-id="ab43d-236">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="ab43d-237">Ruta</span><span class="sxs-lookup"><span data-stu-id="ab43d-237">Route</span></span>                            | <span data-ttu-id="ab43d-238">Fragmento</span><span class="sxs-lookup"><span data-stu-id="ab43d-238">Fragment</span></span>                |
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

## <a name="customize-the-user"></a><span data-ttu-id="ab43d-239">Personalización del usuario</span><span class="sxs-lookup"><span data-stu-id="ab43d-239">Customize the user</span></span>

<span data-ttu-id="ab43d-240">Los usuarios enlazados a la aplicación se pueden personalizar.</span><span class="sxs-lookup"><span data-stu-id="ab43d-240">Users bound to the app can be customized.</span></span> <span data-ttu-id="ab43d-241">En el ejemplo siguiente, todos los usuarios autenticados reciben una `amr` demanda por cada uno de los métodos de autenticación del usuario.</span><span class="sxs-lookup"><span data-stu-id="ab43d-241">In the following example, all authenticated users receive an `amr` claim for each of the user's authentication methods.</span></span>

<span data-ttu-id="ab43d-242">Cree una clase que extienda la <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> clase:</span><span class="sxs-lookup"><span data-stu-id="ab43d-242">Create a class that extends the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> class:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

<span data-ttu-id="ab43d-243">Cree una fábrica que extienda <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601> :</span><span class="sxs-lookup"><span data-stu-id="ab43d-243">Create a factory that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601>:</span></span>

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

<span data-ttu-id="ab43d-244">Registre `CustomAccountFactory` para el proveedor de autenticación en uso.</span><span class="sxs-lookup"><span data-stu-id="ab43d-244">Register the `CustomAccountFactory` for the authentication provider in use.</span></span> <span data-ttu-id="ab43d-245">Cualquiera de los siguientes registros son válidos:</span><span class="sxs-lookup"><span data-stu-id="ab43d-245">Any of the following registrations are valid:</span></span> 

* <span data-ttu-id="ab43d-246"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="ab43d-246"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span></span>

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

* <span data-ttu-id="ab43d-247"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="ab43d-247"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span></span>

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
  
* <span data-ttu-id="ab43d-248"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span><span class="sxs-lookup"><span data-stu-id="ab43d-248"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span></span>

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

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="ab43d-249">Compatibilidad de la representación previa con la autenticación</span><span class="sxs-lookup"><span data-stu-id="ab43d-249">Support prerendering with authentication</span></span>

<span data-ttu-id="ab43d-250">Después de seguir las instrucciones que aparecen en uno de los temas de la aplicación WebAssembly de Blazor hospedada, use estas instrucciones para crear una aplicación que:</span><span class="sxs-lookup"><span data-stu-id="ab43d-250">After following the guidance in one of the hosted Blazor WebAssembly app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="ab43d-251">Representa previamente las rutas de acceso para las que no se requiere autorización.</span><span class="sxs-lookup"><span data-stu-id="ab43d-251">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="ab43d-252">No representa previamente las rutas de acceso para las que se requiere autorización.</span><span class="sxs-lookup"><span data-stu-id="ab43d-252">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="ab43d-253">En la clase `Program` de la aplicación cliente (*Program.cs*), factorice los registros de servicio comunes en un método independiente (por ejemplo, `ConfigureCommonServices`):</span><span class="sxs-lookup"><span data-stu-id="ab43d-253">In the Client app's `Program` class (*Program.cs*), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

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

<span data-ttu-id="ab43d-254">En `Startup.ConfigureServices` de la aplicación de servidor, registre estos servicios adicionales:</span><span class="sxs-lookup"><span data-stu-id="ab43d-254">In the Server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

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

<span data-ttu-id="ab43d-255">En el método de la aplicación de servidor `Startup.Configure` , reemplace los [extremos. MapFallbackToFile ("index. html")](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) con [extremos. MapFallbackToPage ("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A):</span><span class="sxs-lookup"><span data-stu-id="ab43d-255">In the Server app's `Startup.Configure` method, replace [endpoints.MapFallbackToFile("index.html")](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) with [endpoints.MapFallbackToPage("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A):</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="ab43d-256">En la aplicación de servidor, cree una carpeta *Pages* si todavía no existe.</span><span class="sxs-lookup"><span data-stu-id="ab43d-256">In the Server app, create a *Pages* folder if it doesn't exist.</span></span> <span data-ttu-id="ab43d-257">Cree una página *_Host.cshtml* dentro de la carpeta *Pages* de la aplicación de servidor.</span><span class="sxs-lookup"><span data-stu-id="ab43d-257">Create a *_Host.cshtml* page inside the Server app's *Pages* folder.</span></span> <span data-ttu-id="ab43d-258">Pegue el contenido del archivo *wwwroot/index.html* de la aplicación cliente en el archivo *Pages/_Host.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="ab43d-258">Paste the contents from the Client app's *wwwroot/index.html* file into the *Pages/_Host.cshtml* file.</span></span> <span data-ttu-id="ab43d-259">Actualice el contenido del archivo:</span><span class="sxs-lookup"><span data-stu-id="ab43d-259">Update the file's contents:</span></span>

* <span data-ttu-id="ab43d-260">Agregue `@page "_Host"` a la parte superior del archivo.</span><span class="sxs-lookup"><span data-stu-id="ab43d-260">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="ab43d-261">Reemplace la etiqueta `<app>Loading...</app>` por la siguiente:</span><span class="sxs-lookup"><span data-stu-id="ab43d-261">Replace the `<app>Loading...</app>` tag with the following:</span></span>

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
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="ab43d-262">Opciones para aplicaciones hospedadas y proveedores de inicio de sesión de terceros</span><span class="sxs-lookup"><span data-stu-id="ab43d-262">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="ab43d-263">Al autenticar y autorizar una aplicación WebAssembly de Blazor con un proveedor de terceros, hay varias opciones disponibles para autenticar al usuario.</span><span class="sxs-lookup"><span data-stu-id="ab43d-263">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="ab43d-264">Su elección depende del escenario.</span><span class="sxs-lookup"><span data-stu-id="ab43d-264">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="ab43d-265">Para obtener más información, vea <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="ab43d-265">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="ab43d-266">Autenticación de usuarios solo para llamadas a las API de terceros protegidas</span><span class="sxs-lookup"><span data-stu-id="ab43d-266">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="ab43d-267">Autentique al usuario con un flujo de OAuth del lado cliente en el proveedor de la API de terceros:</span><span class="sxs-lookup"><span data-stu-id="ab43d-267">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="ab43d-268">En este escenario:</span><span class="sxs-lookup"><span data-stu-id="ab43d-268">In this scenario:</span></span>

* <span data-ttu-id="ab43d-269">El servidor que hospeda la aplicación no desempeña ningún rol.</span><span class="sxs-lookup"><span data-stu-id="ab43d-269">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="ab43d-270">No se pueden proteger las API en el servidor.</span><span class="sxs-lookup"><span data-stu-id="ab43d-270">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="ab43d-271">La aplicación solo puede llamar a las API de terceros protegidas.</span><span class="sxs-lookup"><span data-stu-id="ab43d-271">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="ab43d-272">Autenticación de usuarios con un proveedor de terceros y llamada a las API protegidas en el servidor host y en el tercero</span><span class="sxs-lookup"><span data-stu-id="ab43d-272">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="ab43d-273">Configure Identity con un proveedor de inicio de sesión de terceros.</span><span class="sxs-lookup"><span data-stu-id="ab43d-273">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="ab43d-274">Obtenga los tokens necesarios para el acceso de API de terceros y almacénelos.</span><span class="sxs-lookup"><span data-stu-id="ab43d-274">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="ab43d-275">Cuando un usuario inicia sesión, Identity recopila tokens de acceso y actualización como parte del proceso de autenticación.</span><span class="sxs-lookup"><span data-stu-id="ab43d-275">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="ab43d-276">En ese momento, hay un par de enfoques disponibles para realizar llamadas API a las API de terceros.</span><span class="sxs-lookup"><span data-stu-id="ab43d-276">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="ab43d-277">Uso de un token de acceso de servidor para recuperar el token de acceso de terceros</span><span class="sxs-lookup"><span data-stu-id="ab43d-277">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="ab43d-278">Use el token de acceso generado en el servidor para recuperar el token de acceso de terceros de un punto de conexión de la API de servidor.</span><span class="sxs-lookup"><span data-stu-id="ab43d-278">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="ab43d-279">Desde allí, use el token de acceso de terceros para llamar a recursos de API de terceros directamente desde Identity en el cliente.</span><span class="sxs-lookup"><span data-stu-id="ab43d-279">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="ab43d-280">Este enfoque no se recomienda.</span><span class="sxs-lookup"><span data-stu-id="ab43d-280">We don't recommend this approach.</span></span> <span data-ttu-id="ab43d-281">Este enfoque requiere tratar el token de acceso de terceros como si se hubiera generado para un cliente público.</span><span class="sxs-lookup"><span data-stu-id="ab43d-281">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="ab43d-282">En términos de OAuth, la aplicación pública no tiene un secreto de cliente porque no es de confianza para almacenar secretos de forma segura y el token de acceso se genera para un cliente confidencial.</span><span class="sxs-lookup"><span data-stu-id="ab43d-282">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="ab43d-283">Un cliente confidencial es un cliente que tiene un secreto de cliente y se supone que puede almacenar secretos de forma segura.</span><span class="sxs-lookup"><span data-stu-id="ab43d-283">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="ab43d-284">El token de acceso de terceros podría recibir ámbitos adicionales para realizar operaciones confidenciales en función del hecho de que la tercera parte emitió el token para un cliente de más confianza.</span><span class="sxs-lookup"><span data-stu-id="ab43d-284">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="ab43d-285">Del mismo modo, los tokens de actualización no deben emitirse para un cliente que no sea de confianza, ya que esto proporciona acceso ilimitado al cliente a menos que se apliquen otras restricciones.</span><span class="sxs-lookup"><span data-stu-id="ab43d-285">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="ab43d-286">Realización de llamadas API desde el cliente a la API de servidor para llamar a las API de terceros</span><span class="sxs-lookup"><span data-stu-id="ab43d-286">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="ab43d-287">Realice una llamada API desde el cliente a la API del servidor.</span><span class="sxs-lookup"><span data-stu-id="ab43d-287">Make an API call from the client to the server API.</span></span> <span data-ttu-id="ab43d-288">En el servidor, recupere el token de acceso para el recurso de la API de terceros y emita cualquier llamada que sea necesaria.</span><span class="sxs-lookup"><span data-stu-id="ab43d-288">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="ab43d-289">Aunque este enfoque requiere un salto de red adicional a través del servidor para llamar a una API de terceros, en última instancia resulta una experiencia más segura:</span><span class="sxs-lookup"><span data-stu-id="ab43d-289">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="ab43d-290">El servidor puede almacenar tokens de actualización y asegurarse de que la aplicación no pierde el acceso a recursos de terceros.</span><span class="sxs-lookup"><span data-stu-id="ab43d-290">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="ab43d-291">La aplicación no puede filtrar los tokens de acceso del servidor que puedan contener permisos más confidenciales.</span><span class="sxs-lookup"><span data-stu-id="ab43d-291">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>

## <a name="use-open-id-connect-oidc-v20-endpoints"></a><span data-ttu-id="ab43d-292">Usar puntos de conexión v 2.0 de Open ID Connect (OIDC)</span><span class="sxs-lookup"><span data-stu-id="ab43d-292">Use Open ID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="ab43d-293">La biblioteca de autenticación y Blazor las plantillas usan puntos de conexión de Open ID Connect (OIDC) v 1.0.</span><span class="sxs-lookup"><span data-stu-id="ab43d-293">The authentication library and Blazor templates use Open ID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="ab43d-294">Para usar un punto de conexión v 2.0, configure la <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> opción portador JWT.</span><span class="sxs-lookup"><span data-stu-id="ab43d-294">To use a v2.0 endpoint, configure the JWT Bearer <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> option.</span></span> <span data-ttu-id="ab43d-295">En el ejemplo siguiente, AAD se configura para v 2.0 anexando un `v2.0` segmento a la <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> propiedad:</span><span class="sxs-lookup"><span data-stu-id="ab43d-295">In the following example, AAD is configured for v2.0 by appending a `v2.0` segment to the <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> property:</span></span>

```csharp
builder.Services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    });
```

<span data-ttu-id="ab43d-296">Como alternativa, la configuración se puede realizar en el archivo de configuración de la aplicación (*appSettings. JSON*):</span><span class="sxs-lookup"><span data-stu-id="ab43d-296">Alternatively, the setting can be made in the app settings (*appsettings.json*) file:</span></span>

```json
{
  "Local": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="ab43d-297">Si el seguimiento de un segmento a la autoridad no es apropiado para el proveedor de OIDC de la aplicación, como con proveedores que no son de AAD, establezca la <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> propiedad directamente.</span><span class="sxs-lookup"><span data-stu-id="ab43d-297">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> property directly.</span></span> <span data-ttu-id="ab43d-298">Establezca la propiedad en <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> o en el archivo de configuración de la aplicación (*appSettings. JSON*) con la `Authority` clave.</span><span class="sxs-lookup"><span data-stu-id="ab43d-298">Either set the property in <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> or in the app settings file (*appsettings.json*) with the `Authority` key.</span></span>

<span data-ttu-id="ab43d-299">La lista de notificaciones en el token de identificador cambia para los puntos de conexión v 2.0.</span><span class="sxs-lookup"><span data-stu-id="ab43d-299">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="ab43d-300">Para obtener más información, consulte [¿por qué actualizar a Microsoft Identity Platform (v 2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span><span class="sxs-lookup"><span data-stu-id="ab43d-300">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span></span>
