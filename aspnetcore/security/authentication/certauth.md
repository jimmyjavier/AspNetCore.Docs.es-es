---
<span data-ttu-id="58f2c-101">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="58f2c-101">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="58f2c-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="58f2c-102">'Blazor'</span></span>
- <span data-ttu-id="58f2c-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="58f2c-103">'Identity'</span></span>
- <span data-ttu-id="58f2c-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="58f2c-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="58f2c-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="58f2c-105">'Razor'</span></span>
- <span data-ttu-id="58f2c-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="58f2c-106">'SignalR' uid:</span></span> 

---
# <a name="configure-certificate-authentication-in-aspnet-core"></a><span data-ttu-id="58f2c-107">Configurar la autenticación de certificados en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="58f2c-107">Configure certificate authentication in ASP.NET Core</span></span>

<span data-ttu-id="58f2c-108">`Microsoft.AspNetCore.Authentication.Certificate`contiene una implementación similar a la [autenticación de certificado](https://tools.ietf.org/html/rfc5246#section-7.4.4) para ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="58f2c-108">`Microsoft.AspNetCore.Authentication.Certificate` contains an implementation similar to [Certificate Authentication](https://tools.ietf.org/html/rfc5246#section-7.4.4) for ASP.NET Core.</span></span> <span data-ttu-id="58f2c-109">La autenticación de certificados se produce en el nivel de TLS, mucho antes de que llegue a ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="58f2c-109">Certificate authentication happens at the TLS level, long before it ever gets to ASP.NET Core.</span></span> <span data-ttu-id="58f2c-110">Más concretamente, se trata de un controlador de autenticación que valida el certificado y, a continuación, le proporciona un evento en el que puede resolver ese certificado en un `ClaimsPrincipal` .</span><span class="sxs-lookup"><span data-stu-id="58f2c-110">More accurately, this is an authentication handler that validates the certificate and then gives you an event where you can resolve that certificate to a `ClaimsPrincipal`.</span></span> 

<span data-ttu-id="58f2c-111">[Configure el host para la](#configure-your-host-to-require-certificates) autenticación de certificados, ya sea IIS, Kestrel, Azure Web Apps o cualquier otra cosa que esté usando.</span><span class="sxs-lookup"><span data-stu-id="58f2c-111">[Configure your host](#configure-your-host-to-require-certificates) for certificate authentication, be it IIS, Kestrel, Azure Web Apps, or whatever else you're using.</span></span>

## <a name="proxy-and-load-balancer-scenarios"></a><span data-ttu-id="58f2c-112">Escenarios de proxy y equilibrador de carga</span><span class="sxs-lookup"><span data-stu-id="58f2c-112">Proxy and load balancer scenarios</span></span>

<span data-ttu-id="58f2c-113">La autenticación de certificados es un escenario con estado que se usa principalmente en el que un proxy o un equilibrador de carga no controla el tráfico entre clientes y servidores.</span><span class="sxs-lookup"><span data-stu-id="58f2c-113">Certificate authentication is a stateful scenario primarily used where a proxy or load balancer doesn't handle traffic between clients and servers.</span></span> <span data-ttu-id="58f2c-114">Si se usa un proxy o un equilibrador de carga, la autenticación de certificado solo funciona si el proxy o el equilibrador de carga:</span><span class="sxs-lookup"><span data-stu-id="58f2c-114">If a proxy or load balancer is used, certificate authentication only works if the proxy or load balancer:</span></span>

* <span data-ttu-id="58f2c-115">Controla la autenticación.</span><span class="sxs-lookup"><span data-stu-id="58f2c-115">Handles the authentication.</span></span>
* <span data-ttu-id="58f2c-116">Pasa la información de autenticación del usuario a la aplicación (por ejemplo, en un encabezado de solicitud), que actúa en la información de autenticación.</span><span class="sxs-lookup"><span data-stu-id="58f2c-116">Passes the user authentication information to the app (for example, in a request header), which acts on the authentication information.</span></span>

<span data-ttu-id="58f2c-117">Una alternativa a la autenticación de certificados en entornos en los que se usan servidores proxy y equilibradores de carga es Active Directory Federated Services (ADFS) con OpenID Connect (OIDC).</span><span class="sxs-lookup"><span data-stu-id="58f2c-117">An alternative to certificate authentication in environments where proxies and load balancers are used is Active Directory Federated Services (ADFS) with OpenID Connect (OIDC).</span></span>

## <a name="get-started"></a><span data-ttu-id="58f2c-118">Introducción</span><span class="sxs-lookup"><span data-stu-id="58f2c-118">Get started</span></span>

<span data-ttu-id="58f2c-119">Adquiera un certificado HTTPS, aplíquelo y [Configure el host](#configure-your-host-to-require-certificates) para que requiera certificados.</span><span class="sxs-lookup"><span data-stu-id="58f2c-119">Acquire an HTTPS certificate, apply it, and [configure your host](#configure-your-host-to-require-certificates) to require certificates.</span></span>

<span data-ttu-id="58f2c-120">En la aplicación Web, agregue una referencia al `Microsoft.AspNetCore.Authentication.Certificate` paquete.</span><span class="sxs-lookup"><span data-stu-id="58f2c-120">In your web app, add a reference to the `Microsoft.AspNetCore.Authentication.Certificate` package.</span></span> <span data-ttu-id="58f2c-121">Después, en el `Startup.ConfigureServices` método, llame a `services.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).AddCertificate(...);` con sus opciones, proporcionando un delegado para `OnCertificateValidated` que realice cualquier validación complementaria en el certificado de cliente enviado con las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="58f2c-121">Then in the `Startup.ConfigureServices` method, call `services.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).AddCertificate(...);` with your options, providing a delegate for `OnCertificateValidated` to do any supplementary validation on the client certificate sent with requests.</span></span> <span data-ttu-id="58f2c-122">Convierta esa información en un `ClaimsPrincipal` y establézcala en la `context.Principal` propiedad.</span><span class="sxs-lookup"><span data-stu-id="58f2c-122">Turn that information into a `ClaimsPrincipal` and set it on the `context.Principal` property.</span></span>

<span data-ttu-id="58f2c-123">Si se produce un error en la autenticación, este controlador devuelve una `403 (Forbidden)` respuesta en lugar de `401 (Unauthorized)` , como cabría esperar.</span><span class="sxs-lookup"><span data-stu-id="58f2c-123">If authentication fails, this handler returns a `403 (Forbidden)` response rather a `401 (Unauthorized)`, as you might expect.</span></span> <span data-ttu-id="58f2c-124">La razón es que la autenticación debe realizarse durante la conexión TLS inicial.</span><span class="sxs-lookup"><span data-stu-id="58f2c-124">The reasoning is that the authentication should happen during the initial TLS connection.</span></span> <span data-ttu-id="58f2c-125">En el momento en que llega al controlador, es demasiado tarde.</span><span class="sxs-lookup"><span data-stu-id="58f2c-125">By the time it reaches the handler, it's too late.</span></span> <span data-ttu-id="58f2c-126">No hay ninguna manera de actualizar la conexión de una conexión anónima a una con un certificado.</span><span class="sxs-lookup"><span data-stu-id="58f2c-126">There's no way to upgrade the connection from an anonymous connection to one with a certificate.</span></span>

<span data-ttu-id="58f2c-127">Agregue también `app.UseAuthentication();` en el `Startup.Configure` método.</span><span class="sxs-lookup"><span data-stu-id="58f2c-127">Also add `app.UseAuthentication();` in the `Startup.Configure` method.</span></span> <span data-ttu-id="58f2c-128">De lo contrario, `HttpContext.User` no se establecerá en `ClaimsPrincipal` creado a partir del certificado.</span><span class="sxs-lookup"><span data-stu-id="58f2c-128">Otherwise, the `HttpContext.User` will not be set to `ClaimsPrincipal` created from the certificate.</span></span> <span data-ttu-id="58f2c-129">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="58f2c-129">For example:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
        .AddCertificate();
    // All the other service configuration.
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();

    // All the other app configuration.
}
```

<span data-ttu-id="58f2c-130">En el ejemplo anterior se muestra la manera predeterminada de agregar la autenticación de certificado.</span><span class="sxs-lookup"><span data-stu-id="58f2c-130">The preceding example demonstrates the default way to add certificate authentication.</span></span> <span data-ttu-id="58f2c-131">El controlador crea una entidad de seguridad de usuario mediante las propiedades comunes del certificado.</span><span class="sxs-lookup"><span data-stu-id="58f2c-131">The handler constructs a user principal using the common certificate properties.</span></span>

## <a name="configure-certificate-validation"></a><span data-ttu-id="58f2c-132">Configurar la validación de certificados</span><span class="sxs-lookup"><span data-stu-id="58f2c-132">Configure certificate validation</span></span>

<span data-ttu-id="58f2c-133">El `CertificateAuthenticationOptions` controlador tiene algunas validaciones integradas que son las validaciones mínimas que se deben realizar en un certificado.</span><span class="sxs-lookup"><span data-stu-id="58f2c-133">The `CertificateAuthenticationOptions` handler has some built-in validations that are the minimum validations you should perform on a certificate.</span></span> <span data-ttu-id="58f2c-134">Cada una de estas opciones está habilitada de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="58f2c-134">Each of these settings is enabled by default.</span></span>

### <a name="allowedcertificatetypes--chained-selfsigned-or-all-chained--selfsigned"></a><span data-ttu-id="58f2c-135">AllowedCertificateTypes = encadenado, SelfSigned o todos (encadenado | SelfSigned</span><span class="sxs-lookup"><span data-stu-id="58f2c-135">AllowedCertificateTypes = Chained, SelfSigned, or All (Chained | SelfSigned)</span></span>

<span data-ttu-id="58f2c-136">Valor predeterminado: `CertificateTypes.Chained`</span><span class="sxs-lookup"><span data-stu-id="58f2c-136">Default value: `CertificateTypes.Chained`</span></span>

<span data-ttu-id="58f2c-137">Esta comprobación valida que solo se permite el tipo de certificado adecuado.</span><span class="sxs-lookup"><span data-stu-id="58f2c-137">This check validates that only the appropriate certificate type is allowed.</span></span> <span data-ttu-id="58f2c-138">Si la aplicación usa certificados autofirmados, esta opción debe estar establecida en `CertificateTypes.All` o `CertificateTypes.SelfSigned` .</span><span class="sxs-lookup"><span data-stu-id="58f2c-138">If the app is using self-signed certificates, this option needs to be set to `CertificateTypes.All` or `CertificateTypes.SelfSigned`.</span></span>

### <a name="validatecertificateuse"></a><span data-ttu-id="58f2c-139">ValidateCertificateUse</span><span class="sxs-lookup"><span data-stu-id="58f2c-139">ValidateCertificateUse</span></span>

<span data-ttu-id="58f2c-140">Valor predeterminado: `true`</span><span class="sxs-lookup"><span data-stu-id="58f2c-140">Default value: `true`</span></span>

<span data-ttu-id="58f2c-141">Esta comprobación valida que el certificado presentado por el cliente tiene el uso mejorado de clave (EKU) de autenticación del cliente o no tiene ningún EKU.</span><span class="sxs-lookup"><span data-stu-id="58f2c-141">This check validates that the certificate presented by the client has the Client Authentication extended key use (EKU), or no EKUs at all.</span></span> <span data-ttu-id="58f2c-142">Como indican las especificaciones, si no se especifica ningún EKU, se considera que todos los EKU son válidos.</span><span class="sxs-lookup"><span data-stu-id="58f2c-142">As the specifications say, if no EKU is specified, then all EKUs are deemed valid.</span></span>

### <a name="validatevalidityperiod"></a><span data-ttu-id="58f2c-143">ValidateValidityPeriod</span><span class="sxs-lookup"><span data-stu-id="58f2c-143">ValidateValidityPeriod</span></span>

<span data-ttu-id="58f2c-144">Valor predeterminado: `true`</span><span class="sxs-lookup"><span data-stu-id="58f2c-144">Default value: `true`</span></span>

<span data-ttu-id="58f2c-145">Esta comprobación valida que el certificado está dentro de su período de validez.</span><span class="sxs-lookup"><span data-stu-id="58f2c-145">This check validates that the certificate is within its validity period.</span></span> <span data-ttu-id="58f2c-146">En cada solicitud, el controlador garantiza que un certificado que era válido cuando se presentó no ha expirado durante su sesión actual.</span><span class="sxs-lookup"><span data-stu-id="58f2c-146">On each request, the handler ensures that a certificate that was valid when it was presented hasn't expired during its current session.</span></span>

### <a name="revocationflag"></a><span data-ttu-id="58f2c-147">RevocationFlag</span><span class="sxs-lookup"><span data-stu-id="58f2c-147">RevocationFlag</span></span>

<span data-ttu-id="58f2c-148">Valor predeterminado: `X509RevocationFlag.ExcludeRoot`</span><span class="sxs-lookup"><span data-stu-id="58f2c-148">Default value: `X509RevocationFlag.ExcludeRoot`</span></span>

<span data-ttu-id="58f2c-149">Marca que especifica en qué certificados de la cadena se comprueba la revocación.</span><span class="sxs-lookup"><span data-stu-id="58f2c-149">A flag that specifies which certificates in the chain are checked for revocation.</span></span>

<span data-ttu-id="58f2c-150">Las comprobaciones de revocación solo se realizan cuando el certificado está encadenado a un certificado raíz.</span><span class="sxs-lookup"><span data-stu-id="58f2c-150">Revocation checks are only performed when the certificate is chained to a root certificate.</span></span>

### <a name="revocationmode"></a><span data-ttu-id="58f2c-151">RevocationMode</span><span class="sxs-lookup"><span data-stu-id="58f2c-151">RevocationMode</span></span>

<span data-ttu-id="58f2c-152">Valor predeterminado: `X509RevocationMode.Online`</span><span class="sxs-lookup"><span data-stu-id="58f2c-152">Default value: `X509RevocationMode.Online`</span></span>

<span data-ttu-id="58f2c-153">Marca que especifica cómo se realizan las comprobaciones de revocación.</span><span class="sxs-lookup"><span data-stu-id="58f2c-153">A flag that specifies how revocation checks are performed.</span></span>

<span data-ttu-id="58f2c-154">Si se especifica una comprobación en línea, se puede producir un retraso largo mientras se Contacta con la entidad de certificación.</span><span class="sxs-lookup"><span data-stu-id="58f2c-154">Specifying an online check can result in a long delay while the certificate authority is contacted.</span></span>

<span data-ttu-id="58f2c-155">Las comprobaciones de revocación solo se realizan cuando el certificado está encadenado a un certificado raíz.</span><span class="sxs-lookup"><span data-stu-id="58f2c-155">Revocation checks are only performed when the certificate is chained to a root certificate.</span></span>

### <a name="can-i-configure-my-app-to-require-a-certificate-only-on-certain-paths"></a><span data-ttu-id="58f2c-156">¿Puedo configurar mi aplicación para requerir un certificado solo en determinadas rutas?</span><span class="sxs-lookup"><span data-stu-id="58f2c-156">Can I configure my app to require a certificate only on certain paths?</span></span>

<span data-ttu-id="58f2c-157">Esto no es posible.</span><span class="sxs-lookup"><span data-stu-id="58f2c-157">This isn't possible.</span></span> <span data-ttu-id="58f2c-158">Recuerde que el intercambio de certificados se realiza al inicio de la conversación HTTPS, se realiza en el servidor antes de que se reciba la primera solicitud en esa conexión, por lo que no es posible el ámbito en función de los campos de solicitud.</span><span class="sxs-lookup"><span data-stu-id="58f2c-158">Remember the certificate exchange is done that the start of the HTTPS conversation, it's done by the server before the first request is received on that connection so it's not possible to scope based on any request fields.</span></span>

## <a name="handler-events"></a><span data-ttu-id="58f2c-159">Eventos de controlador</span><span class="sxs-lookup"><span data-stu-id="58f2c-159">Handler events</span></span>

<span data-ttu-id="58f2c-160">El controlador tiene dos eventos:</span><span class="sxs-lookup"><span data-stu-id="58f2c-160">The handler has two events:</span></span>

* <span data-ttu-id="58f2c-161">`OnAuthenticationFailed`: Se le llama si se produce una excepción durante la autenticación y le permite reaccionar.</span><span class="sxs-lookup"><span data-stu-id="58f2c-161">`OnAuthenticationFailed`: Called if an exception happens during authentication and allows you to react.</span></span>
* <span data-ttu-id="58f2c-162">`OnCertificateValidated`: Se llama después de validar el certificado, se ha aprobado la validación y se ha creado una entidad de seguridad predeterminada.</span><span class="sxs-lookup"><span data-stu-id="58f2c-162">`OnCertificateValidated`: Called after the certificate has been validated, passed validation and a default principal has been created.</span></span> <span data-ttu-id="58f2c-163">Este evento le permite realizar su propia validación y aumentar o reemplazar la entidad de seguridad.</span><span class="sxs-lookup"><span data-stu-id="58f2c-163">This event allows you to perform your own validation and augment or replace the principal.</span></span> <span data-ttu-id="58f2c-164">Por ejemplo, se incluyen:</span><span class="sxs-lookup"><span data-stu-id="58f2c-164">For examples include:</span></span>
  * <span data-ttu-id="58f2c-165">Determinar si los servicios conocen el certificado.</span><span class="sxs-lookup"><span data-stu-id="58f2c-165">Determining if the certificate is known to your services.</span></span>
  * <span data-ttu-id="58f2c-166">Construir su propia entidad de seguridad.</span><span class="sxs-lookup"><span data-stu-id="58f2c-166">Constructing your own principal.</span></span> <span data-ttu-id="58f2c-167">Considere el ejemplo siguiente de `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="58f2c-167">Consider the following example in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddAuthentication(
    CertificateAuthenticationDefaults.AuthenticationScheme)
    .AddCertificate(options =>
    {
        options.Events = new CertificateAuthenticationEvents
        {
            OnCertificateValidated = context =>
            {
                var claims = new[]
                {
                    new Claim(
                        ClaimTypes.NameIdentifier, 
                        context.ClientCertificate.Subject,
                        ClaimValueTypes.String, 
                        context.Options.ClaimsIssuer),
                    new Claim(ClaimTypes.Name,
                        context.ClientCertificate.Subject,
                        ClaimValueTypes.String, 
                        context.Options.ClaimsIssuer)
                };

                context.Principal = new ClaimsPrincipal(
                    new ClaimsIdentity(claims, context.Scheme.Name));
                context.Success();

                return Task.CompletedTask;
            }
        };
    });
```

<span data-ttu-id="58f2c-168">Si encuentra que el certificado de entrada no cumple su validación adicional, llame a `context.Fail("failure reason")` con un motivo del error.</span><span class="sxs-lookup"><span data-stu-id="58f2c-168">If you find the inbound certificate doesn't meet your extra validation, call `context.Fail("failure reason")` with a failure reason.</span></span>

<span data-ttu-id="58f2c-169">En el caso de una funcionalidad real, es probable que quiera llamar a un servicio registrado en la inserción de dependencias que se conecta a una base de datos u otro tipo de almacén de usuario.</span><span class="sxs-lookup"><span data-stu-id="58f2c-169">For real functionality, you'll probably want to call a service registered in dependency injection that connects to a database or other type of user store.</span></span> <span data-ttu-id="58f2c-170">Obtenga acceso a su servicio mediante el contexto que se pasa al delegado.</span><span class="sxs-lookup"><span data-stu-id="58f2c-170">Access your service by using the context passed into your delegate.</span></span> <span data-ttu-id="58f2c-171">Considere el ejemplo siguiente de `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="58f2c-171">Consider the following example in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddAuthentication(
    CertificateAuthenticationDefaults.AuthenticationScheme)
    .AddCertificate(options =>
    {
        options.Events = new CertificateAuthenticationEvents
        {
            OnCertificateValidated = context =>
            {
                var validationService =
                    context.HttpContext.RequestServices
                        .GetService<ICertificateValidationService>();
                
                if (validationService.ValidateCertificate(
                    context.ClientCertificate))
                {
                    var claims = new[]
                    {
                        new Claim(
                            ClaimTypes.NameIdentifier, 
                            context.ClientCertificate.Subject, 
                            ClaimValueTypes.String, 
                            context.Options.ClaimsIssuer),
                        new Claim(
                            ClaimTypes.Name, 
                            context.ClientCertificate.Subject, 
                            ClaimValueTypes.String, 
                            context.Options.ClaimsIssuer)
                    };

                    context.Principal = new ClaimsPrincipal(
                        new ClaimsIdentity(claims, context.Scheme.Name));
                    context.Success();
                }                     

                return Task.CompletedTask;
            }
        };
    });
```

<span data-ttu-id="58f2c-172">Conceptualmente, la validación del certificado es un problema de autorización.</span><span class="sxs-lookup"><span data-stu-id="58f2c-172">Conceptually, the validation of the certificate is an authorization concern.</span></span> <span data-ttu-id="58f2c-173">La adición de una comprobación, por ejemplo, a un emisor o huella digital en una directiva de autorización, en lugar de dentro de `OnCertificateValidated` , es absolutamente aceptable.</span><span class="sxs-lookup"><span data-stu-id="58f2c-173">Adding a check on, for example, an issuer or thumbprint in an authorization policy, rather than inside `OnCertificateValidated`, is perfectly acceptable.</span></span>

## <a name="configure-your-host-to-require-certificates"></a><span data-ttu-id="58f2c-174">Configuración del host para requerir certificados</span><span class="sxs-lookup"><span data-stu-id="58f2c-174">Configure your host to require certificates</span></span>

### <a name="kestrel"></a><span data-ttu-id="58f2c-175">Kestrel</span><span class="sxs-lookup"><span data-stu-id="58f2c-175">Kestrel</span></span>

<span data-ttu-id="58f2c-176">En *Program.CS*, configure Kestrel como se indica a continuación:</span><span class="sxs-lookup"><span data-stu-id="58f2c-176">In *Program.cs*, configure Kestrel as follows:</span></span>

```csharp
public static void Main(string[] args)
{
    CreateHostBuilder(args).Build().Run();
}

public static IHostBuilder CreateHostBuilder(string[] args)
{
    return Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
            webBuilder.ConfigureKestrel(o =>
            {
                o.ConfigureHttpsDefaults(o => 
            o.ClientCertificateMode = 
                ClientCertificateMode.RequireCertificate);
            });
        });
}
```

> [!NOTE]
> <span data-ttu-id="58f2c-177">Los puntos de conexión que se crean mediante una llamada a <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **antes** de llamar a <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> no tendrán aplicados los valores predeterminados.</span><span class="sxs-lookup"><span data-stu-id="58f2c-177">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="iis"></a><span data-ttu-id="58f2c-178">IIS</span><span class="sxs-lookup"><span data-stu-id="58f2c-178">IIS</span></span>

<span data-ttu-id="58f2c-179">Complete los pasos siguientes en el administrador de IIS:</span><span class="sxs-lookup"><span data-stu-id="58f2c-179">Complete the following steps in IIS Manager:</span></span>

1. <span data-ttu-id="58f2c-180">Seleccione su sitio en la pestaña **conexiones** .</span><span class="sxs-lookup"><span data-stu-id="58f2c-180">Select your site from the **Connections** tab.</span></span>
1. <span data-ttu-id="58f2c-181">Haga doble clic en la opción **configuración de SSL** en la ventana **ver características** .</span><span class="sxs-lookup"><span data-stu-id="58f2c-181">Double-click the **SSL Settings** option in the **Features View** window.</span></span>
1. <span data-ttu-id="58f2c-182">Active la casilla **requerir SSL** y seleccione el botón de radio **requerir** en la sección **certificados de cliente** .</span><span class="sxs-lookup"><span data-stu-id="58f2c-182">Check the **Require SSL** checkbox, and select the **Require** radio button in the **Client certificates** section.</span></span>

![Configuración de certificados de cliente en IIS](README-IISConfig.png)

### <a name="azure-and-custom-web-proxies"></a><span data-ttu-id="58f2c-184">Azure y proxies web personalizados</span><span class="sxs-lookup"><span data-stu-id="58f2c-184">Azure and custom web proxies</span></span>

<span data-ttu-id="58f2c-185">Consulte la [documentación de host e implementación](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding) para obtener información sobre cómo configurar el middleware de reenvío de certificados.</span><span class="sxs-lookup"><span data-stu-id="58f2c-185">See the [host and deploy documentation](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding) for how to configure the certificate forwarding middleware.</span></span>

### <a name="use-certificate-authentication-in-azure-web-apps"></a><span data-ttu-id="58f2c-186">Uso de la autenticación de certificados en Azure Web Apps</span><span class="sxs-lookup"><span data-stu-id="58f2c-186">Use certificate authentication in Azure Web Apps</span></span>

<span data-ttu-id="58f2c-187">No se requiere ninguna configuración de reenvío para Azure.</span><span class="sxs-lookup"><span data-stu-id="58f2c-187">No forwarding configuration is required for Azure.</span></span> <span data-ttu-id="58f2c-188">Esto ya está configurado en el middleware de reenvío de certificados.</span><span class="sxs-lookup"><span data-stu-id="58f2c-188">This is already setup in the certificate forwarding middleware.</span></span>

> [!NOTE]
> <span data-ttu-id="58f2c-189">Esto requiere que el CertificateForwardingMiddleware esté presente.</span><span class="sxs-lookup"><span data-stu-id="58f2c-189">This requires that the CertificateForwardingMiddleware is present.</span></span>

### <a name="use-certificate-authentication-in-custom-web-proxies"></a><span data-ttu-id="58f2c-190">Usar la autenticación de certificados en proxies web personalizados</span><span class="sxs-lookup"><span data-stu-id="58f2c-190">Use certificate authentication in custom web proxies</span></span>

<span data-ttu-id="58f2c-191">El `AddCertificateForwarding` método se usa para especificar:</span><span class="sxs-lookup"><span data-stu-id="58f2c-191">The `AddCertificateForwarding` method is used to specify:</span></span>

* <span data-ttu-id="58f2c-192">Nombre del encabezado de cliente.</span><span class="sxs-lookup"><span data-stu-id="58f2c-192">The client header name.</span></span>
* <span data-ttu-id="58f2c-193">Cómo se va a cargar el certificado (mediante la `HeaderConverter` propiedad).</span><span class="sxs-lookup"><span data-stu-id="58f2c-193">How the certificate is to be loaded (using the `HeaderConverter` property).</span></span>

<span data-ttu-id="58f2c-194">En los proxies web personalizados, el certificado se pasa como un encabezado de solicitud personalizado, por ejemplo `X-SSL-CERT` .</span><span class="sxs-lookup"><span data-stu-id="58f2c-194">In custom web proxies, the certificate is passed as a custom request header, for example `X-SSL-CERT`.</span></span> <span data-ttu-id="58f2c-195">Para usarlo, configure el reenvío de certificados en `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="58f2c-195">To use it, configure certificate forwarding in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddCertificateForwarding(options =>
    {
        options.CertificateHeader = "X-SSL-CERT";
        options.HeaderConverter = (headerValue) =>
        {
            X509Certificate2 clientCertificate = null;
        
            if(!string.IsNullOrWhiteSpace(headerValue))
            {
                byte[] bytes = StringToByteArray(headerValue);
                clientCertificate = new X509Certificate2(bytes);
            }

            return clientCertificate;
        };
    });
}

private static byte[] StringToByteArray(string hex)
{
    int NumberChars = hex.Length;
    byte[] bytes = new byte[NumberChars / 2];

    for (int i = 0; i < NumberChars; i += 2)
    {
        bytes[i / 2] = Convert.ToByte(hex.Substring(i, 2), 16);
    }

    return bytes;
}
```

<span data-ttu-id="58f2c-196">`Startup.Configure`Después, el método agrega el middleware.</span><span class="sxs-lookup"><span data-stu-id="58f2c-196">The `Startup.Configure` method then adds the middleware.</span></span> <span data-ttu-id="58f2c-197">`UseCertificateForwarding`se llama a antes de llamar a `UseAuthentication` y `UseAuthorization` :</span><span class="sxs-lookup"><span data-stu-id="58f2c-197">`UseCertificateForwarding` is called before the calls to `UseAuthentication` and `UseAuthorization`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseRouting();

    app.UseCertificateForwarding();
    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllers();
    });
}
```

<span data-ttu-id="58f2c-198">Se puede usar una clase independiente para implementar la lógica de validación.</span><span class="sxs-lookup"><span data-stu-id="58f2c-198">A separate class can be used to implement validation logic.</span></span> <span data-ttu-id="58f2c-199">Dado que en este ejemplo se usa el mismo certificado autofirmado, asegúrese de que solo se puede usar el certificado.</span><span class="sxs-lookup"><span data-stu-id="58f2c-199">Because the same self-signed certificate is used in this example, ensure that only your certificate can be used.</span></span> <span data-ttu-id="58f2c-200">Compruebe que las huellas digitales del certificado de cliente y del certificado de servidor coinciden; de lo contrario, se puede usar cualquier certificado y será suficiente para autenticarse.</span><span class="sxs-lookup"><span data-stu-id="58f2c-200">Validate that the thumbprints of both the client certificate and the server certificate match, otherwise any certificate can be used and will be enough to authenticate.</span></span> <span data-ttu-id="58f2c-201">Se utilizará en el `AddCertificate` método.</span><span class="sxs-lookup"><span data-stu-id="58f2c-201">This would be used inside the `AddCertificate` method.</span></span> <span data-ttu-id="58f2c-202">También puede validar el asunto o el emisor aquí si usa certificados intermedios o secundarios.</span><span class="sxs-lookup"><span data-stu-id="58f2c-202">You could also validate the subject or the issuer here if you're using intermediate or child certificates.</span></span>

```csharp
using System.IO;
using System.Security.Cryptography.X509Certificates;

namespace AspNetCoreCertificateAuthApi
{
    public class MyCertificateValidationService
    {
        public bool ValidateCertificate(X509Certificate2 clientCertificate)
        {
            // Do not hardcode passwords in production code
            // Use thumbprint or key vault
            var cert = new X509Certificate2(
                Path.Combine("sts_dev_cert.pfx"), "1234");

            if (clientCertificate.Thumbprint == cert.Thumbprint)
            {
                return true;
            }

            return false;
        }
    }
}
```

#### <a name="implement-an-httpclient-using-a-certificate-and-the-httpclienthandler"></a><span data-ttu-id="58f2c-203">Implementar un HttpClient mediante un certificado y el HttpClientHandler</span><span class="sxs-lookup"><span data-stu-id="58f2c-203">Implement an HttpClient using a certificate and the HttpClientHandler</span></span>

<span data-ttu-id="58f2c-204">HttpClientHandler se podría agregar directamente en el constructor de la clase HttpClient.</span><span class="sxs-lookup"><span data-stu-id="58f2c-204">The HttpClientHandler could be added directly in the constructor of the HttpClient class.</span></span> <span data-ttu-id="58f2c-205">Se debe tener cuidado al crear instancias del HttpClient.</span><span class="sxs-lookup"><span data-stu-id="58f2c-205">Care should be taken when creating instances of the HttpClient.</span></span> <span data-ttu-id="58f2c-206">A continuación, HttpClient enviará el certificado con cada solicitud.</span><span class="sxs-lookup"><span data-stu-id="58f2c-206">The HttpClient will then send the certificate with each request.</span></span>

```csharp
private async Task<JsonDocument> GetApiDataUsingHttpClientHandler()
{
    var cert = new X509Certificate2(Path.Combine(_environment.ContentRootPath, "sts_dev_cert.pfx"), "1234");
    var handler = new HttpClientHandler();
    handler.ClientCertificates.Add(cert);
    var client = new HttpClient(handler);
     
    var request = new HttpRequestMessage()
    {
        RequestUri = new Uri("https://localhost:44379/api/values"),
        Method = HttpMethod.Get,
    };
    var response = await client.SendAsync(request);
    if (response.IsSuccessStatusCode)
    {
        var responseContent = await response.Content.ReadAsStringAsync();
        var data = JsonDocument.Parse(responseContent);
        return data;
    }
 
    throw new ApplicationException($"Status code: {response.StatusCode}, Error: {response.ReasonPhrase}");
}
```

#### <a name="implement-an-httpclient-using-a-certificate-and-a-named-httpclient-from-ihttpclientfactory"></a><span data-ttu-id="58f2c-207">Implementar un HttpClient mediante un certificado y un HttpClient con nombre de IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="58f2c-207">Implement an HttpClient using a certificate and a named HttpClient from IHttpClientFactory</span></span> 

<span data-ttu-id="58f2c-208">En el ejemplo siguiente, se agrega un certificado de cliente a un HttpClientHandler mediante la propiedad ClientCertificates desde el controlador.</span><span class="sxs-lookup"><span data-stu-id="58f2c-208">In the following example, a client certificate is added to a HttpClientHandler using the ClientCertificates property from the handler.</span></span> <span data-ttu-id="58f2c-209">Este controlador se puede usar en una instancia con nombre de HttpClient mediante el método ConfigurePrimaryHttpMessageHandler.</span><span class="sxs-lookup"><span data-stu-id="58f2c-209">This handler can then be used in a named instance of a HttpClient using the ConfigurePrimaryHttpMessageHandler method.</span></span> <span data-ttu-id="58f2c-210">Se configura en la clase startup en el método ConfigureServices.</span><span class="sxs-lookup"><span data-stu-id="58f2c-210">This is setup in the Startup class in the ConfigureServices method.</span></span>

```csharp
var clientCertificate = 
    new X509Certificate2(
      Path.Combine(_environment.ContentRootPath, "sts_dev_cert.pfx"), "1234");
 
var handler = new HttpClientHandler();
handler.ClientCertificates.Add(clientCertificate);
 
services.AddHttpClient("namedClient", c =>
{
}).ConfigurePrimaryHttpMessageHandler(() => handler);
```

<span data-ttu-id="58f2c-211">A continuación, se puede usar IHttpClientFactory para obtener la instancia con nombre con el controlador y el certificado.</span><span class="sxs-lookup"><span data-stu-id="58f2c-211">The IHttpClientFactory can then be used to get the named instance with the handler and the certificate.</span></span> <span data-ttu-id="58f2c-212">El método CreateClient con el nombre del cliente definido en la clase startup se usa para obtener la instancia.</span><span class="sxs-lookup"><span data-stu-id="58f2c-212">The CreateClient method with the name of the client defined in the Startup class is used to get the instance.</span></span> <span data-ttu-id="58f2c-213">La solicitud HTTP se puede enviar mediante el cliente, según sea necesario.</span><span class="sxs-lookup"><span data-stu-id="58f2c-213">The HTTP request can be sent using the client as required.</span></span>

```csharp
private readonly IHttpClientFactory _clientFactory;
 
public ApiService(IHttpClientFactory clientFactory)
{
    _clientFactory = clientFactory;
}
 
private async Task<JsonDocument> GetApiDataWithNamedClient()
{
    var client = _clientFactory.CreateClient("namedClient");
 
    var request = new HttpRequestMessage()
    {
        RequestUri = new Uri("https://localhost:44379/api/values"),
        Method = HttpMethod.Get,
    };
    var response = await client.SendAsync(request);
    if (response.IsSuccessStatusCode)
    {
        var responseContent = await response.Content.ReadAsStringAsync();
        var data = JsonDocument.Parse(responseContent);
        return data;
    }
 
    throw new ApplicationException($"Status code: {response.StatusCode}, Error: {response.ReasonPhrase}");
}
```

<span data-ttu-id="58f2c-214">Si el certificado correcto se envía al servidor, se devuelven los datos.</span><span class="sxs-lookup"><span data-stu-id="58f2c-214">If the correct certificate is sent to the server, the data is returned.</span></span> <span data-ttu-id="58f2c-215">Si no se envía ningún certificado o el certificado equivocado, se devuelve un código de Estado HTTP 403.</span><span class="sxs-lookup"><span data-stu-id="58f2c-215">If no certificate or the wrong certificate is sent, an HTTP 403 status code is returned.</span></span>

### <a name="create-certificates-in-powershell"></a><span data-ttu-id="58f2c-216">Crear certificados en PowerShell</span><span class="sxs-lookup"><span data-stu-id="58f2c-216">Create certificates in PowerShell</span></span>

<span data-ttu-id="58f2c-217">La creación de los certificados es la parte más difícil de configurar este flujo.</span><span class="sxs-lookup"><span data-stu-id="58f2c-217">Creating the certificates is the hardest part in setting up this flow.</span></span> <span data-ttu-id="58f2c-218">Se puede crear un certificado raíz mediante el `New-SelfSignedCertificate` cmdlet de PowerShell.</span><span class="sxs-lookup"><span data-stu-id="58f2c-218">A root certificate can be created using the `New-SelfSignedCertificate` PowerShell cmdlet.</span></span> <span data-ttu-id="58f2c-219">Al crear el certificado, use una contraseña segura.</span><span class="sxs-lookup"><span data-stu-id="58f2c-219">When creating the certificate, use a strong password.</span></span> <span data-ttu-id="58f2c-220">Es importante agregar el `KeyUsageProperty` parámetro y el `KeyUsage` parámetro como se muestra.</span><span class="sxs-lookup"><span data-stu-id="58f2c-220">It's important to add the `KeyUsageProperty` parameter and the `KeyUsage` parameter as shown.</span></span>

#### <a name="create-root-ca"></a><span data-ttu-id="58f2c-221">Crear CA raíz</span><span class="sxs-lookup"><span data-stu-id="58f2c-221">Create root CA</span></span>

```powershell
New-SelfSignedCertificate -DnsName "root_ca_dev_damienbod.com", "root_ca_dev_damienbod.com" -CertStoreLocation "cert:\LocalMachine\My" -NotAfter (Get-Date).AddYears(20) -FriendlyName "root_ca_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\root_ca_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath root_ca_dev_damienbod.crt
```

> [!NOTE]
> <span data-ttu-id="58f2c-222">El `-DnsName` valor del parámetro debe coincidir con el destino de implementación de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="58f2c-222">The `-DnsName` parameter value must match the deployment target of the app.</span></span> <span data-ttu-id="58f2c-223">Por ejemplo, "localhost" para el desarrollo.</span><span class="sxs-lookup"><span data-stu-id="58f2c-223">For example, "localhost" for development.</span></span>

#### <a name="install-in-the-trusted-root"></a><span data-ttu-id="58f2c-224">Instalación en la raíz de confianza</span><span class="sxs-lookup"><span data-stu-id="58f2c-224">Install in the trusted root</span></span>

<span data-ttu-id="58f2c-225">El certificado raíz debe ser de confianza en el sistema host.</span><span class="sxs-lookup"><span data-stu-id="58f2c-225">The root certificate needs to be trusted on your host system.</span></span> <span data-ttu-id="58f2c-226">De forma predeterminada, no se confiará en un certificado raíz que no haya creado ninguna entidad de certificación.</span><span class="sxs-lookup"><span data-stu-id="58f2c-226">A root certificate which was not created by a certificate authority won't be trusted by default.</span></span> <span data-ttu-id="58f2c-227">En el vínculo siguiente se explica cómo se puede lograr esto en Windows:</span><span class="sxs-lookup"><span data-stu-id="58f2c-227">The following link explains how this can be accomplished on Windows:</span></span>

https://social.msdn.microsoft.com/Forums/SqlServer/5ed119ef-1704-4be4-8a4f-ef11de7c8f34/a-certificate-chain-processed-but-terminated-in-a-root-certificate-which-is-not-trusted-by-the

#### <a name="intermediate-certificate"></a><span data-ttu-id="58f2c-228">Certificado intermedio</span><span class="sxs-lookup"><span data-stu-id="58f2c-228">Intermediate certificate</span></span>

<span data-ttu-id="58f2c-229">Ahora se puede crear un certificado intermedio a partir del certificado raíz.</span><span class="sxs-lookup"><span data-stu-id="58f2c-229">An intermediate certificate can now be created from the root certificate.</span></span> <span data-ttu-id="58f2c-230">Esto no es necesario para todos los casos de uso, pero es posible que tenga que crear muchos certificados o tener que activar o desactivar grupos de certificados.</span><span class="sxs-lookup"><span data-stu-id="58f2c-230">This isn't required for all use cases, but you might need to create many certificates or need to activate or disable groups of certificates.</span></span> <span data-ttu-id="58f2c-231">El `TextExtension` parámetro es necesario para establecer la longitud de la ruta de acceso en las restricciones básicas del certificado.</span><span class="sxs-lookup"><span data-stu-id="58f2c-231">The `TextExtension` parameter is required to set the path length in the basic constraints of the certificate.</span></span>

<span data-ttu-id="58f2c-232">Después, el certificado intermedio se puede Agregar al certificado intermedio de confianza en el sistema host de Windows.</span><span class="sxs-lookup"><span data-stu-id="58f2c-232">The intermediate certificate can then be added to the trusted intermediate certificate in the Windows host system.</span></span>

```powershell
$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint of the root..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "intermediate_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "intermediate_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature -TextExtension @("2.5.29.19={text}CA=1&pathlength=1")

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\intermediate_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath intermediate_dev_damienbod.crt
```

#### <a name="create-child-certificate-from-intermediate-certificate"></a><span data-ttu-id="58f2c-233">Crear certificado secundario a partir de un certificado intermedio</span><span class="sxs-lookup"><span data-stu-id="58f2c-233">Create child certificate from intermediate certificate</span></span>

<span data-ttu-id="58f2c-234">Se puede crear un certificado secundario a partir del certificado intermedio.</span><span class="sxs-lookup"><span data-stu-id="58f2c-234">A child certificate can be created from the intermediate certificate.</span></span> <span data-ttu-id="58f2c-235">Se trata de la entidad final y no es necesario crear más certificados secundarios.</span><span class="sxs-lookup"><span data-stu-id="58f2c-235">This is the end entity and doesn't need to create more child certificates.</span></span>

```powershell
$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint from the Intermediate certificate..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com"

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath child_a_dev_damienbod.crt
```

#### <a name="create-child-certificate-from-root-certificate"></a><span data-ttu-id="58f2c-236">Crear certificado secundario a partir del certificado raíz</span><span class="sxs-lookup"><span data-stu-id="58f2c-236">Create child certificate from root certificate</span></span>

<span data-ttu-id="58f2c-237">También se puede crear un certificado secundario a partir del certificado raíz directamente.</span><span class="sxs-lookup"><span data-stu-id="58f2c-237">A child certificate can also be created from the root certificate directly.</span></span> 

```powershell
$rootcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint from the root cert..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $rootcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com"

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath child_a_dev_damienbod.crt
```

#### <a name="example-root---intermediate-certificate---certificate"></a><span data-ttu-id="58f2c-238">Ejemplo de certificado intermedio de raíz: certificado</span><span class="sxs-lookup"><span data-stu-id="58f2c-238">Example root - intermediate certificate - certificate</span></span>

```powershell
$mypwdroot = ConvertTo-SecureString -String "1234" -Force -AsPlainText
$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

New-SelfSignedCertificate -DnsName "root_ca_dev_damienbod.com", "root_ca_dev_damienbod.com" -CertStoreLocation "cert:\LocalMachine\My" -NotAfter (Get-Date).AddYears(20) -FriendlyName "root_ca_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature

Get-ChildItem -Path cert:\localMachine\my\0C89639E4E2998A93E423F919B36D4009A0F9991 | Export-PfxCertificate -FilePath C:\git\root_ca_dev_damienbod.pfx -Password $mypwdroot

Export-Certificate -Cert cert:\localMachine\my\0C89639E4E2998A93E423F919B36D4009A0F9991 -FilePath root_ca_dev_damienbod.crt

$rootcert = ( Get-ChildItem -Path cert:\LocalMachine\My\0C89639E4E2998A93E423F919B36D4009A0F9991 )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $rootcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature -TextExtension @("2.5.29.19={text}CA=1&pathlength=1")

Get-ChildItem -Path cert:\localMachine\my\BA9BF91ED35538A01375EFC212A2F46104B33A44 | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\BA9BF91ED35538A01375EFC212A2F46104B33A44 -FilePath child_a_dev_damienbod.crt

$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\BA9BF91ED35538A01375EFC212A2F46104B33A44 )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_b_from_a_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_b_from_a_dev_damienbod.com" 

Get-ChildItem -Path cert:\localMachine\my\141594A0AE38CBBECED7AF680F7945CD51D8F28A | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_b_from_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\141594A0AE38CBBECED7AF680F7945CD51D8F28A -FilePath child_b_from_a_dev_damienbod.crt
```

<span data-ttu-id="58f2c-239">Al usar los certificados raíz, intermedios o secundarios, los certificados se pueden validar con la huella digital o la PublicKey según sea necesario.</span><span class="sxs-lookup"><span data-stu-id="58f2c-239">When using the root, intermediate, or child certificates, the certificates can be validated using the Thumbprint or PublicKey as required.</span></span>

```csharp
using System.Collections.Generic;
using System.IO;
using System.Security.Cryptography.X509Certificates;

namespace AspNetCoreCertificateAuthApi
{
    public class MyCertificateValidationService 
    {
        public bool ValidateCertificate(X509Certificate2 clientCertificate)
        {
            return CheckIfThumbprintIsValid(clientCertificate);
        }

        private bool CheckIfThumbprintIsValid(X509Certificate2 clientCertificate)
        {
            var listOfValidThumbprints = new List<string>
            {
                "141594A0AE38CBBECED7AF680F7945CD51D8F28A",
                "0C89639E4E2998A93E423F919B36D4009A0F9991",
                "BA9BF91ED35538A01375EFC212A2F46104B33A44"
            };

            if (listOfValidThumbprints.Contains(clientCertificate.Thumbprint))
            {
                return true;
            }

            return false;
        }
    }
}
```
