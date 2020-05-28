---
<span data-ttu-id="c2081-101">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c2081-101">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2081-102">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="c2081-102">'Blazor'</span></span>
- <span data-ttu-id="c2081-103">"Identity"</span><span class="sxs-lookup"><span data-stu-id="c2081-103">'Identity'</span></span>
- <span data-ttu-id="c2081-104">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="c2081-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2081-105">"Razor"</span><span class="sxs-lookup"><span data-stu-id="c2081-105">'Razor'</span></span>
- <span data-ttu-id="c2081-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c2081-106">'SignalR' uid:</span></span> 

---

# <a name="authentication-and-authorization-in-grpc-for-aspnet-core"></a><span data-ttu-id="c2081-107">Autenticación y autorización en gRPC para ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c2081-107">Authentication and authorization in gRPC for ASP.NET Core</span></span>

<span data-ttu-id="c2081-108">Por [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="c2081-108">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="c2081-109">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/grpc/authn-and-authz/sample/) [(cómo descargarlo)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="c2081-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/grpc/authn-and-authz/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="authenticate-users-calling-a-grpc-service"></a><span data-ttu-id="c2081-110">Autenticación de los usuarios que llaman a un servicio gRPC</span><span class="sxs-lookup"><span data-stu-id="c2081-110">Authenticate users calling a gRPC service</span></span>

<span data-ttu-id="c2081-111">gRPC se puede usar con la [autenticación de ASP.NET Core](xref:security/authentication/identity) para asociar un usuario a cada llamada.</span><span class="sxs-lookup"><span data-stu-id="c2081-111">gRPC can be used with [ASP.NET Core authentication](xref:security/authentication/identity) to associate a user with each call.</span></span>

<span data-ttu-id="c2081-112">El siguiente es un ejemplo de `Startup.Configure` en el que se usa gRPC y la autenticación de ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="c2081-112">The following is an example of `Startup.Configure` which uses gRPC and ASP.NET Core authentication:</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseRouting();
    
    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapGrpcService<GreeterService>();
    });
}
```

> [!NOTE]
> <span data-ttu-id="c2081-113">El orden en el que se registra el middleware de autenticación de ASP.NET Core es importante.</span><span class="sxs-lookup"><span data-stu-id="c2081-113">The order in which you register the ASP.NET Core authentication middleware matters.</span></span> <span data-ttu-id="c2081-114">Llame siempre a `UseAuthentication` y `UseAuthorization` después de `UseRouting` y antes de `UseEndpoints`.</span><span class="sxs-lookup"><span data-stu-id="c2081-114">Always call `UseAuthentication` and `UseAuthorization` after `UseRouting` and before `UseEndpoints`.</span></span>

<span data-ttu-id="c2081-115">Es necesario configurar el mecanismo de autenticación que usa la aplicación durante una llamada.</span><span class="sxs-lookup"><span data-stu-id="c2081-115">The authentication mechanism your app uses during a call needs to be configured.</span></span> <span data-ttu-id="c2081-116">La configuración de autenticación se agrega en `Startup.ConfigureServices` y será diferente en función del mecanismo de autenticación que use la aplicación.</span><span class="sxs-lookup"><span data-stu-id="c2081-116">Authentication configuration is added in `Startup.ConfigureServices` and will be different depending upon the authentication mechanism your app uses.</span></span> <span data-ttu-id="c2081-117">Para obtener ejemplos de cómo proteger aplicaciones ASP.NET Core, vea [Ejemplos de autenticación](xref:security/authentication/samples).</span><span class="sxs-lookup"><span data-stu-id="c2081-117">For examples of how to secure ASP.NET Core apps, see [Authentication samples](xref:security/authentication/samples).</span></span>

<span data-ttu-id="c2081-118">Una vez que se ha configurado la autenticación, se puede acceder al usuario en los métodos de un servicio gRPC mediante `ServerCallContext`.</span><span class="sxs-lookup"><span data-stu-id="c2081-118">Once authentication has been setup, the user can be accessed in a gRPC service methods via the `ServerCallContext`.</span></span>

```csharp
public override Task<BuyTicketsResponse> BuyTickets(
    BuyTicketsRequest request, ServerCallContext context)
{
    var user = context.GetHttpContext().User;

    // ... access data from ClaimsPrincipal ...
}

```

### <a name="bearer-token-authentication"></a><span data-ttu-id="c2081-119">Autenticación por token de portador</span><span class="sxs-lookup"><span data-stu-id="c2081-119">Bearer token authentication</span></span>

<span data-ttu-id="c2081-120">El cliente puede proporcionar un token de acceso para la autenticación.</span><span class="sxs-lookup"><span data-stu-id="c2081-120">The client can provide an access token for authentication.</span></span> <span data-ttu-id="c2081-121">El servidor valida el token y lo usa para identificar al usuario.</span><span class="sxs-lookup"><span data-stu-id="c2081-121">The server validates the token and uses it to identify the user.</span></span>

<span data-ttu-id="c2081-122">En el servidor, la autenticación de token de portador se configura mediante el [middleware de portador JWT](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).</span><span class="sxs-lookup"><span data-stu-id="c2081-122">On the server, bearer token authentication is configured using the [JWT Bearer middleware](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).</span></span>

<span data-ttu-id="c2081-123">En el cliente gRPC de .NET, el token se puede enviar con llamadas como un encabezado:</span><span class="sxs-lookup"><span data-stu-id="c2081-123">In the .NET gRPC client, the token can be sent with calls as a header:</span></span>

```csharp
public bool DoAuthenticatedCall(
    Ticketer.TicketerClient client, string token)
{
    var headers = new Metadata();
    headers.Add("Authorization", $"Bearer {token}");

    var request = new BuyTicketsRequest { Count = 1 };
    var response = await client.BuyTicketsAsync(request, headers);

    return response.Success;
}
```

<span data-ttu-id="c2081-124">La configuración de `ChannelCredentials` en un canal es una forma alternativa de enviar el token al servicio con llamadas a gRPC.</span><span class="sxs-lookup"><span data-stu-id="c2081-124">Configuring `ChannelCredentials` on a channel is an alternative way to send the token to the service with gRPC calls.</span></span> <span data-ttu-id="c2081-125">La credencial se ejecuta cada vez que se realiza una llamada a gRPC, lo que evita la necesidad de escribir código en varios lugares para pasar el token personalmente.</span><span class="sxs-lookup"><span data-stu-id="c2081-125">The credential is run each time a gRPC call is made, which avoids the need to write code in multiple places to pass the token yourself.</span></span>

<span data-ttu-id="c2081-126">La credencial del ejemplo siguiente configura el canal para enviar el token con cada llamada a gRPC:</span><span class="sxs-lookup"><span data-stu-id="c2081-126">The credential in the following example configures the channel to send the token with every gRPC call:</span></span>

```csharp
private static GrpcChannel CreateAuthenticatedChannel(string address)
{
    var credentials = CallCredentials.FromInterceptor((context, metadata) =>
    {
        if (!string.IsNullOrEmpty(_token))
        {
            metadata.Add("Authorization", $"Bearer {_token}");
        }
        return Task.CompletedTask;
    });

    // SslCredentials is used here because this channel is using TLS.
    // CallCredentials can't be used with ChannelCredentials.Insecure on non-TLS channels.
    var channel = GrpcChannel.ForAddress(address, new GrpcChannelOptions
    {
        Credentials = ChannelCredentials.Create(new SslCredentials(), credentials)
    });
    return channel;
}
```

### <a name="client-certificate-authentication"></a><span data-ttu-id="c2081-127">Autenticación de certificados de clientes</span><span class="sxs-lookup"><span data-stu-id="c2081-127">Client certificate authentication</span></span>

<span data-ttu-id="c2081-128">Un cliente podría proporcionar alternativamente un certificado de cliente para la autenticación.</span><span class="sxs-lookup"><span data-stu-id="c2081-128">A client could alternatively provide a client certificate for authentication.</span></span> <span data-ttu-id="c2081-129">La [autenticación de certificados](https://tools.ietf.org/html/rfc5246#section-7.4.4) se realiza en el nivel de TLS, mucho antes de que llegue a ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c2081-129">[Certificate authentication](https://tools.ietf.org/html/rfc5246#section-7.4.4) happens at the TLS level, long before it ever gets to ASP.NET Core.</span></span> <span data-ttu-id="c2081-130">Cuando la solicitud entra en ASP.NET Core, el [paquete de autenticación de certificado de cliente](xref:security/authentication/certauth) le permite resolver el certificado en un elemento `ClaimsPrincipal`.</span><span class="sxs-lookup"><span data-stu-id="c2081-130">When the request enters ASP.NET Core, the [client certificate authentication package](xref:security/authentication/certauth) allows you to resolve the certificate to a `ClaimsPrincipal`.</span></span>

> [!NOTE]
> <span data-ttu-id="c2081-131">El host debe estar configurado para aceptar certificados de cliente.</span><span class="sxs-lookup"><span data-stu-id="c2081-131">The host needs to be configured to accept client certificates.</span></span> <span data-ttu-id="c2081-132">Vea [Configuración del host para que requiera certificados](xref:security/authentication/certauth#configure-your-host-to-require-certificates) para obtener información sobre la aceptación de certificados de cliente en Kestrel, IIS y Azure.</span><span class="sxs-lookup"><span data-stu-id="c2081-132">See [configure your host to require certificates](xref:security/authentication/certauth#configure-your-host-to-require-certificates) for information on accepting client certificates in Kestrel, IIS and Azure.</span></span>

<span data-ttu-id="c2081-133">En el cliente gRPC de .NET, el certificado de cliente se agrega a `HttpClientHandler`, que después se usa para crear el cliente gRPC:</span><span class="sxs-lookup"><span data-stu-id="c2081-133">In the .NET gRPC client, the client certificate is added to `HttpClientHandler` that is then used to create the gRPC client:</span></span>

```csharp
public Ticketer.TicketerClient CreateClientWithCert(
    string baseAddress,
    X509Certificate2 certificate)
{
    // Add client cert to the handler
    var handler = new HttpClientHandler();
    handler.ClientCertificates.Add(certificate);

    // Create the gRPC channel
    var channel = GrpcChannel.ForAddress(baseAddress, new GrpcChannelOptions
    {
        HttpHandler = handler
    });

    return new Ticketer.TicketerClient(channel);
}
```

### <a name="other-authentication-mechanisms"></a><span data-ttu-id="c2081-134">Otros mecanismos de autenticación</span><span class="sxs-lookup"><span data-stu-id="c2081-134">Other authentication mechanisms</span></span>

<span data-ttu-id="c2081-135">Muchos mecanismos de autenticación admitidos por ASP.NET Core funcionan con gRPC:</span><span class="sxs-lookup"><span data-stu-id="c2081-135">Many ASP.NET Core supported authentication mechanisms work with gRPC:</span></span>

* <span data-ttu-id="c2081-136">Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="c2081-136">Azure Active Directory</span></span>
* <span data-ttu-id="c2081-137">Certificado de cliente</span><span class="sxs-lookup"><span data-stu-id="c2081-137">Client Certificate</span></span>
* <span data-ttu-id="c2081-138">IdentityServer</span><span class="sxs-lookup"><span data-stu-id="c2081-138">IdentityServer</span></span>
* <span data-ttu-id="c2081-139">Token de JWT</span><span class="sxs-lookup"><span data-stu-id="c2081-139">JWT Token</span></span>
* <span data-ttu-id="c2081-140">OAuth 2.0</span><span class="sxs-lookup"><span data-stu-id="c2081-140">OAuth 2.0</span></span>
* <span data-ttu-id="c2081-141">OpenID Connect</span><span class="sxs-lookup"><span data-stu-id="c2081-141">OpenID Connect</span></span>
* <span data-ttu-id="c2081-142">WS-Federation</span><span class="sxs-lookup"><span data-stu-id="c2081-142">WS-Federation</span></span>

<span data-ttu-id="c2081-143">Para obtener más información sobre la configuración de la autenticación en el servidor, vea [Autenticación de ASP.NET Core](xref:security/authentication/identity).</span><span class="sxs-lookup"><span data-stu-id="c2081-143">For more information on configuring authentication on the server, see [ASP.NET Core authentication](xref:security/authentication/identity).</span></span>

<span data-ttu-id="c2081-144">La configuración del cliente gRPC para usar la autenticación dependerá del mecanismo de autenticación que se use.</span><span class="sxs-lookup"><span data-stu-id="c2081-144">Configuring the gRPC client to use authentication will depend on the authentication mechanism you are using.</span></span> <span data-ttu-id="c2081-145">En los ejemplos anteriores de token de portador y certificado de cliente se muestran un par de formas de configurar el cliente gRPC para enviar metadatos de autenticación con llamadas a gRPC:</span><span class="sxs-lookup"><span data-stu-id="c2081-145">The previous bearer token and client certificate examples show a couple of ways the gRPC client can be configured to send authentication metadata with gRPC calls:</span></span>

* <span data-ttu-id="c2081-146">Los clientes gRPC fuertemente tipados usan `HttpClient` internamente.</span><span class="sxs-lookup"><span data-stu-id="c2081-146">Strongly typed gRPC clients use `HttpClient` internally.</span></span> <span data-ttu-id="c2081-147">La autenticación se puede configurar en [HttpClientHandler](/dotnet/api/system.net.http.httpclienthandler) o mediante la adición de instancias de [HttpMessageHandler](/dotnet/api/system.net.http.httpmessagehandler) personalizadas a `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="c2081-147">Authentication can be configured on [HttpClientHandler](/dotnet/api/system.net.http.httpclienthandler), or by adding custom [HttpMessageHandler](/dotnet/api/system.net.http.httpmessagehandler) instances to the `HttpClient`.</span></span>
* <span data-ttu-id="c2081-148">Cada llamada a gRPC tiene un argumento `CallOptions` opcional.</span><span class="sxs-lookup"><span data-stu-id="c2081-148">Each gRPC call has an optional `CallOptions` argument.</span></span> <span data-ttu-id="c2081-149">Se pueden enviar encabezados personalizados mediante la colección de encabezados de la opción.</span><span class="sxs-lookup"><span data-stu-id="c2081-149">Custom headers can be sent using the option's headers collection.</span></span>

> [!NOTE]
> <span data-ttu-id="c2081-150">La autenticación de Windows (NTLM/Kerberos/Negotiate) no se puede usar con gRPC.</span><span class="sxs-lookup"><span data-stu-id="c2081-150">Windows Authentication (NTLM/Kerberos/Negotiate) can't be used with gRPC.</span></span> <span data-ttu-id="c2081-151">gRPC requiere HTTP/2 y HTTP/2 no admite la autenticación de Windows.</span><span class="sxs-lookup"><span data-stu-id="c2081-151">gRPC requires HTTP/2, and HTTP/2 doesn't support Windows Authentication.</span></span>

## <a name="authorize-users-to-access-services-and-service-methods"></a><span data-ttu-id="c2081-152">Autorización a los usuarios para acceder a servicios y métodos de servicio</span><span class="sxs-lookup"><span data-stu-id="c2081-152">Authorize users to access services and service methods</span></span>

<span data-ttu-id="c2081-153">De forma predeterminada, los usuarios no autenticados pueden llamar a todos los métodos de un servicio.</span><span class="sxs-lookup"><span data-stu-id="c2081-153">By default, all methods in a service can be called by unauthenticated users.</span></span> <span data-ttu-id="c2081-154">Para requerir la autenticación, aplique el atributo [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) al servicio:</span><span class="sxs-lookup"><span data-stu-id="c2081-154">To require authentication, apply the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute to the service:</span></span>

```csharp
[Authorize]
public class TicketerService : Ticketer.TicketerBase
{
}
```

<span data-ttu-id="c2081-155">Puede usar los argumentos de constructor y las propiedades del atributo `[Authorize]` para restringir el acceso solo a los usuarios que coincidan con [directivas de autorización](xref:security/authorization/policies) específicas.</span><span class="sxs-lookup"><span data-stu-id="c2081-155">You can use the constructor arguments and properties of the `[Authorize]` attribute to restrict access to only users matching specific [authorization policies](xref:security/authorization/policies).</span></span> <span data-ttu-id="c2081-156">Por ejemplo, si tiene una directiva de autorización personalizada llamada `MyAuthorizationPolicy`, asegúrese de que solo los usuarios que coincidan con esa directiva puedan acceder al servicio mediante el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="c2081-156">For example, if you have a custom authorization policy called `MyAuthorizationPolicy`, ensure that only users matching that policy can access the service using the following code:</span></span>

```csharp
[Authorize("MyAuthorizationPolicy")]
public class TicketerService : Ticketer.TicketerBase
{
}
```

<span data-ttu-id="c2081-157">A los métodos de servicio individuales también se les puede aplicar el atributo `[Authorize]`.</span><span class="sxs-lookup"><span data-stu-id="c2081-157">Individual service methods can have the `[Authorize]` attribute applied as well.</span></span> <span data-ttu-id="c2081-158">Si el usuario actual no coincide con las directivas aplicadas **tanto** al método como a la clase, se devuelve un error al autor de la llamada:</span><span class="sxs-lookup"><span data-stu-id="c2081-158">If the current user doesn't match the policies applied to **both** the method and the class, an error is returned to the caller:</span></span>

```csharp
[Authorize]
public class TicketerService : Ticketer.TicketerBase
{
    public override Task<AvailableTicketsResponse> GetAvailableTickets(
        Empty request, ServerCallContext context)
    {
        // ... buy tickets for the current user ...
    }

    [Authorize("Administrators")]
    public override Task<BuyTicketsResponse> RefundTickets(
        BuyTicketsRequest request, ServerCallContext context)
    {
        // ... refund tickets (something only Administrators can do) ..
    }
}
```

## <a name="additional-resources"></a><span data-ttu-id="c2081-159">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="c2081-159">Additional resources</span></span>

* [<span data-ttu-id="c2081-160">Autenticación de token de portador en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c2081-160">Bearer Token authentication in ASP.NET Core</span></span>](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [<span data-ttu-id="c2081-161">Configuración de la autenticación del certificado de cliente en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c2081-161">Configure Client Certificate authentication in ASP.NET Core</span></span>](xref:security/authentication/certauth)
