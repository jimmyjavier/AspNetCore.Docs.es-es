---
title: Configurar la autenticación de certificados en ASP.NET Core
author: blowdart
description: Obtenga información sobre cómo configurar la autenticación de certificados en ASP.NET Core para IIS y HTTP.sys.
monikerRange: '>= aspnetcore-3.0'
ms.author: bdorrans
ms.date: 01/02/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/certauth
ms.openlocfilehash: 493046e288c6b1ccd8e41f15a8e6e532a10a4adc
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/26/2020
ms.locfileid: "85403201"
---
# <a name="configure-certificate-authentication-in-aspnet-core"></a><span data-ttu-id="a1435-103">Configurar la autenticación de certificados en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a1435-103">Configure certificate authentication in ASP.NET Core</span></span>

<span data-ttu-id="a1435-104">`Microsoft.AspNetCore.Authentication.Certificate`contiene una implementación similar a la [autenticación de certificado](https://tools.ietf.org/html/rfc5246#section-7.4.4) para ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="a1435-104">`Microsoft.AspNetCore.Authentication.Certificate` contains an implementation similar to [Certificate Authentication](https://tools.ietf.org/html/rfc5246#section-7.4.4) for ASP.NET Core.</span></span> <span data-ttu-id="a1435-105">La autenticación de certificados se realiza en el nivel de TLS, mucho antes de que llegue a ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a1435-105">Certificate authentication happens at the TLS level, long before it ever gets to ASP.NET Core.</span></span> <span data-ttu-id="a1435-106">Más concretamente, se trata de un controlador de autenticación que valida el certificado y, a continuación, le proporciona un evento en el que puede resolver ese certificado en un `ClaimsPrincipal` .</span><span class="sxs-lookup"><span data-stu-id="a1435-106">More accurately, this is an authentication handler that validates the certificate and then gives you an event where you can resolve that certificate to a `ClaimsPrincipal`.</span></span> 

<span data-ttu-id="a1435-107">[Configure el servidor para la](#configure-your-server-to-require-certificates) autenticación de certificados, ya sea IIS, Kestrel, Azure Web Apps o cualquier otra cosa que esté usando.</span><span class="sxs-lookup"><span data-stu-id="a1435-107">[Configure your server](#configure-your-server-to-require-certificates) for certificate authentication, be it IIS, Kestrel, Azure Web Apps, or whatever else you're using.</span></span>

## <a name="proxy-and-load-balancer-scenarios"></a><span data-ttu-id="a1435-108">Escenarios de proxy y equilibrador de carga</span><span class="sxs-lookup"><span data-stu-id="a1435-108">Proxy and load balancer scenarios</span></span>

<span data-ttu-id="a1435-109">La autenticación de certificados es un escenario con estado que se usa principalmente en el que un proxy o un equilibrador de carga no controla el tráfico entre clientes y servidores.</span><span class="sxs-lookup"><span data-stu-id="a1435-109">Certificate authentication is a stateful scenario primarily used where a proxy or load balancer doesn't handle traffic between clients and servers.</span></span> <span data-ttu-id="a1435-110">Si se usa un proxy o un equilibrador de carga, la autenticación de certificado solo funciona si el proxy o el equilibrador de carga:</span><span class="sxs-lookup"><span data-stu-id="a1435-110">If a proxy or load balancer is used, certificate authentication only works if the proxy or load balancer:</span></span>

* <span data-ttu-id="a1435-111">Controla la autenticación.</span><span class="sxs-lookup"><span data-stu-id="a1435-111">Handles the authentication.</span></span>
* <span data-ttu-id="a1435-112">Pasa la información de autenticación del usuario a la aplicación (por ejemplo, en un encabezado de solicitud), que actúa en la información de autenticación.</span><span class="sxs-lookup"><span data-stu-id="a1435-112">Passes the user authentication information to the app (for example, in a request header), which acts on the authentication information.</span></span>

<span data-ttu-id="a1435-113">Una alternativa a la autenticación de certificados en entornos en los que se usan servidores proxy y equilibradores de carga es Active Directory Federated Services (ADFS) con OpenID Connect (OIDC).</span><span class="sxs-lookup"><span data-stu-id="a1435-113">An alternative to certificate authentication in environments where proxies and load balancers are used is Active Directory Federated Services (ADFS) with OpenID Connect (OIDC).</span></span>

## <a name="get-started"></a><span data-ttu-id="a1435-114">Introducción</span><span class="sxs-lookup"><span data-stu-id="a1435-114">Get started</span></span>

<span data-ttu-id="a1435-115">Adquiera un certificado HTTPS, aplíquelo y [Configure el servidor](#configure-your-server-to-require-certificates) para que requiera certificados.</span><span class="sxs-lookup"><span data-stu-id="a1435-115">Acquire an HTTPS certificate, apply it, and [configure your server](#configure-your-server-to-require-certificates) to require certificates.</span></span>

<span data-ttu-id="a1435-116">En la aplicación Web, agregue una referencia al `Microsoft.AspNetCore.Authentication.Certificate` paquete.</span><span class="sxs-lookup"><span data-stu-id="a1435-116">In your web app, add a reference to the `Microsoft.AspNetCore.Authentication.Certificate` package.</span></span> <span data-ttu-id="a1435-117">Después, en el `Startup.ConfigureServices` método, llame a `services.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).AddCertificate(...);` con sus opciones, proporcionando un delegado para `OnCertificateValidated` que realice cualquier validación complementaria en el certificado de cliente enviado con las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="a1435-117">Then in the `Startup.ConfigureServices` method, call `services.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).AddCertificate(...);` with your options, providing a delegate for `OnCertificateValidated` to do any supplementary validation on the client certificate sent with requests.</span></span> <span data-ttu-id="a1435-118">Convierta esa información en un `ClaimsPrincipal` y establézcala en la `context.Principal` propiedad.</span><span class="sxs-lookup"><span data-stu-id="a1435-118">Turn that information into a `ClaimsPrincipal` and set it on the `context.Principal` property.</span></span>

<span data-ttu-id="a1435-119">Si se produce un error en la autenticación, este controlador devuelve una `403 (Forbidden)` respuesta en lugar de `401 (Unauthorized)` , como cabría esperar.</span><span class="sxs-lookup"><span data-stu-id="a1435-119">If authentication fails, this handler returns a `403 (Forbidden)` response rather a `401 (Unauthorized)`, as you might expect.</span></span> <span data-ttu-id="a1435-120">La razón es que la autenticación debe realizarse durante la conexión TLS inicial.</span><span class="sxs-lookup"><span data-stu-id="a1435-120">The reasoning is that the authentication should happen during the initial TLS connection.</span></span> <span data-ttu-id="a1435-121">En el momento en que llega al controlador, es demasiado tarde.</span><span class="sxs-lookup"><span data-stu-id="a1435-121">By the time it reaches the handler, it's too late.</span></span> <span data-ttu-id="a1435-122">No hay ninguna manera de actualizar la conexión de una conexión anónima a una con un certificado.</span><span class="sxs-lookup"><span data-stu-id="a1435-122">There's no way to upgrade the connection from an anonymous connection to one with a certificate.</span></span>

<span data-ttu-id="a1435-123">Agregue también `app.UseAuthentication();` en el `Startup.Configure` método.</span><span class="sxs-lookup"><span data-stu-id="a1435-123">Also add `app.UseAuthentication();` in the `Startup.Configure` method.</span></span> <span data-ttu-id="a1435-124">De lo contrario, `HttpContext.User` no se establecerá en `ClaimsPrincipal` creado a partir del certificado.</span><span class="sxs-lookup"><span data-stu-id="a1435-124">Otherwise, the `HttpContext.User` will not be set to `ClaimsPrincipal` created from the certificate.</span></span> <span data-ttu-id="a1435-125">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="a1435-125">For example:</span></span>

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

<span data-ttu-id="a1435-126">En el ejemplo anterior se muestra la manera predeterminada de agregar la autenticación de certificado.</span><span class="sxs-lookup"><span data-stu-id="a1435-126">The preceding example demonstrates the default way to add certificate authentication.</span></span> <span data-ttu-id="a1435-127">El controlador crea una entidad de seguridad de usuario mediante las propiedades comunes del certificado.</span><span class="sxs-lookup"><span data-stu-id="a1435-127">The handler constructs a user principal using the common certificate properties.</span></span>

## <a name="configure-certificate-validation"></a><span data-ttu-id="a1435-128">Configurar la validación de certificados</span><span class="sxs-lookup"><span data-stu-id="a1435-128">Configure certificate validation</span></span>

<span data-ttu-id="a1435-129">El `CertificateAuthenticationOptions` controlador tiene algunas validaciones integradas que son las validaciones mínimas que se deben realizar en un certificado.</span><span class="sxs-lookup"><span data-stu-id="a1435-129">The `CertificateAuthenticationOptions` handler has some built-in validations that are the minimum validations you should perform on a certificate.</span></span> <span data-ttu-id="a1435-130">Cada una de estas opciones está habilitada de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="a1435-130">Each of these settings is enabled by default.</span></span>

### <a name="allowedcertificatetypes--chained-selfsigned-or-all-chained--selfsigned"></a><span data-ttu-id="a1435-131">AllowedCertificateTypes = encadenado, SelfSigned o todos (encadenado | SelfSigned</span><span class="sxs-lookup"><span data-stu-id="a1435-131">AllowedCertificateTypes = Chained, SelfSigned, or All (Chained | SelfSigned)</span></span>

<span data-ttu-id="a1435-132">Valor predeterminado: `CertificateTypes.Chained`</span><span class="sxs-lookup"><span data-stu-id="a1435-132">Default value: `CertificateTypes.Chained`</span></span>

<span data-ttu-id="a1435-133">Esta comprobación valida que solo se permite el tipo de certificado adecuado.</span><span class="sxs-lookup"><span data-stu-id="a1435-133">This check validates that only the appropriate certificate type is allowed.</span></span> <span data-ttu-id="a1435-134">Si la aplicación usa certificados autofirmados, esta opción debe estar establecida en `CertificateTypes.All` o `CertificateTypes.SelfSigned` .</span><span class="sxs-lookup"><span data-stu-id="a1435-134">If the app is using self-signed certificates, this option needs to be set to `CertificateTypes.All` or `CertificateTypes.SelfSigned`.</span></span>

### <a name="validatecertificateuse"></a><span data-ttu-id="a1435-135">ValidateCertificateUse</span><span class="sxs-lookup"><span data-stu-id="a1435-135">ValidateCertificateUse</span></span>

<span data-ttu-id="a1435-136">Valor predeterminado: `true`</span><span class="sxs-lookup"><span data-stu-id="a1435-136">Default value: `true`</span></span>

<span data-ttu-id="a1435-137">Esta comprobación valida que el certificado presentado por el cliente tiene el uso mejorado de clave (EKU) de autenticación del cliente o no tiene ningún EKU.</span><span class="sxs-lookup"><span data-stu-id="a1435-137">This check validates that the certificate presented by the client has the Client Authentication extended key use (EKU), or no EKUs at all.</span></span> <span data-ttu-id="a1435-138">Como indican las especificaciones, si no se especifica ningún EKU, se considera que todos los EKU son válidos.</span><span class="sxs-lookup"><span data-stu-id="a1435-138">As the specifications say, if no EKU is specified, then all EKUs are deemed valid.</span></span>

### <a name="validatevalidityperiod"></a><span data-ttu-id="a1435-139">ValidateValidityPeriod</span><span class="sxs-lookup"><span data-stu-id="a1435-139">ValidateValidityPeriod</span></span>

<span data-ttu-id="a1435-140">Valor predeterminado: `true`</span><span class="sxs-lookup"><span data-stu-id="a1435-140">Default value: `true`</span></span>

<span data-ttu-id="a1435-141">Esta comprobación valida que el certificado está dentro de su período de validez.</span><span class="sxs-lookup"><span data-stu-id="a1435-141">This check validates that the certificate is within its validity period.</span></span> <span data-ttu-id="a1435-142">En cada solicitud, el controlador garantiza que un certificado que era válido cuando se presentó no ha expirado durante su sesión actual.</span><span class="sxs-lookup"><span data-stu-id="a1435-142">On each request, the handler ensures that a certificate that was valid when it was presented hasn't expired during its current session.</span></span>

### <a name="revocationflag"></a><span data-ttu-id="a1435-143">RevocationFlag</span><span class="sxs-lookup"><span data-stu-id="a1435-143">RevocationFlag</span></span>

<span data-ttu-id="a1435-144">Valor predeterminado: `X509RevocationFlag.ExcludeRoot`</span><span class="sxs-lookup"><span data-stu-id="a1435-144">Default value: `X509RevocationFlag.ExcludeRoot`</span></span>

<span data-ttu-id="a1435-145">Marca que especifica en qué certificados de la cadena se comprueba la revocación.</span><span class="sxs-lookup"><span data-stu-id="a1435-145">A flag that specifies which certificates in the chain are checked for revocation.</span></span>

<span data-ttu-id="a1435-146">Las comprobaciones de revocación solo se realizan cuando el certificado está encadenado a un certificado raíz.</span><span class="sxs-lookup"><span data-stu-id="a1435-146">Revocation checks are only performed when the certificate is chained to a root certificate.</span></span>

### <a name="revocationmode"></a><span data-ttu-id="a1435-147">RevocationMode</span><span class="sxs-lookup"><span data-stu-id="a1435-147">RevocationMode</span></span>

<span data-ttu-id="a1435-148">Valor predeterminado: `X509RevocationMode.Online`</span><span class="sxs-lookup"><span data-stu-id="a1435-148">Default value: `X509RevocationMode.Online`</span></span>

<span data-ttu-id="a1435-149">Marca que especifica cómo se realizan las comprobaciones de revocación.</span><span class="sxs-lookup"><span data-stu-id="a1435-149">A flag that specifies how revocation checks are performed.</span></span>

<span data-ttu-id="a1435-150">Si se especifica una comprobación en línea, se puede producir un retraso largo mientras se Contacta con la entidad de certificación.</span><span class="sxs-lookup"><span data-stu-id="a1435-150">Specifying an online check can result in a long delay while the certificate authority is contacted.</span></span>

<span data-ttu-id="a1435-151">Las comprobaciones de revocación solo se realizan cuando el certificado está encadenado a un certificado raíz.</span><span class="sxs-lookup"><span data-stu-id="a1435-151">Revocation checks are only performed when the certificate is chained to a root certificate.</span></span>

### <a name="can-i-configure-my-app-to-require-a-certificate-only-on-certain-paths"></a><span data-ttu-id="a1435-152">¿Puedo configurar mi aplicación para requerir un certificado solo en determinadas rutas?</span><span class="sxs-lookup"><span data-stu-id="a1435-152">Can I configure my app to require a certificate only on certain paths?</span></span>

<span data-ttu-id="a1435-153">Esto no es posible.</span><span class="sxs-lookup"><span data-stu-id="a1435-153">This isn't possible.</span></span> <span data-ttu-id="a1435-154">Recuerde que el intercambio de certificados se realiza al inicio de la conversación HTTPS, se realiza en el servidor antes de que se reciba la primera solicitud en esa conexión, por lo que no es posible el ámbito en función de los campos de solicitud.</span><span class="sxs-lookup"><span data-stu-id="a1435-154">Remember the certificate exchange is done that the start of the HTTPS conversation, it's done by the server before the first request is received on that connection so it's not possible to scope based on any request fields.</span></span>

## <a name="handler-events"></a><span data-ttu-id="a1435-155">Eventos de controlador</span><span class="sxs-lookup"><span data-stu-id="a1435-155">Handler events</span></span>

<span data-ttu-id="a1435-156">El controlador tiene dos eventos:</span><span class="sxs-lookup"><span data-stu-id="a1435-156">The handler has two events:</span></span>

* <span data-ttu-id="a1435-157">`OnAuthenticationFailed`: Se le llama si se produce una excepción durante la autenticación y le permite reaccionar.</span><span class="sxs-lookup"><span data-stu-id="a1435-157">`OnAuthenticationFailed`: Called if an exception happens during authentication and allows you to react.</span></span>
* <span data-ttu-id="a1435-158">`OnCertificateValidated`: Se llama después de validar el certificado, se ha aprobado la validación y se ha creado una entidad de seguridad predeterminada.</span><span class="sxs-lookup"><span data-stu-id="a1435-158">`OnCertificateValidated`: Called after the certificate has been validated, passed validation and a default principal has been created.</span></span> <span data-ttu-id="a1435-159">Este evento le permite realizar su propia validación y aumentar o reemplazar la entidad de seguridad.</span><span class="sxs-lookup"><span data-stu-id="a1435-159">This event allows you to perform your own validation and augment or replace the principal.</span></span> <span data-ttu-id="a1435-160">Por ejemplo, se incluyen:</span><span class="sxs-lookup"><span data-stu-id="a1435-160">For examples include:</span></span>
  * <span data-ttu-id="a1435-161">Determinar si los servicios conocen el certificado.</span><span class="sxs-lookup"><span data-stu-id="a1435-161">Determining if the certificate is known to your services.</span></span>
  * <span data-ttu-id="a1435-162">Construir su propia entidad de seguridad.</span><span class="sxs-lookup"><span data-stu-id="a1435-162">Constructing your own principal.</span></span> <span data-ttu-id="a1435-163">Considere el ejemplo siguiente de `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="a1435-163">Consider the following example in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="a1435-164">Si encuentra que el certificado de entrada no cumple su validación adicional, llame a `context.Fail("failure reason")` con un motivo del error.</span><span class="sxs-lookup"><span data-stu-id="a1435-164">If you find the inbound certificate doesn't meet your extra validation, call `context.Fail("failure reason")` with a failure reason.</span></span>

<span data-ttu-id="a1435-165">En el caso de una funcionalidad real, es probable que quiera llamar a un servicio registrado en la inserción de dependencias que se conecta a una base de datos u otro tipo de almacén de usuario.</span><span class="sxs-lookup"><span data-stu-id="a1435-165">For real functionality, you'll probably want to call a service registered in dependency injection that connects to a database or other type of user store.</span></span> <span data-ttu-id="a1435-166">Obtenga acceso a su servicio mediante el contexto que se pasa al delegado.</span><span class="sxs-lookup"><span data-stu-id="a1435-166">Access your service by using the context passed into your delegate.</span></span> <span data-ttu-id="a1435-167">Considere el ejemplo siguiente de `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="a1435-167">Consider the following example in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="a1435-168">Conceptualmente, la validación del certificado es un problema de autorización.</span><span class="sxs-lookup"><span data-stu-id="a1435-168">Conceptually, the validation of the certificate is an authorization concern.</span></span> <span data-ttu-id="a1435-169">La adición de una comprobación, por ejemplo, a un emisor o huella digital en una directiva de autorización, en lugar de dentro de `OnCertificateValidated` , es absolutamente aceptable.</span><span class="sxs-lookup"><span data-stu-id="a1435-169">Adding a check on, for example, an issuer or thumbprint in an authorization policy, rather than inside `OnCertificateValidated`, is perfectly acceptable.</span></span>

## <a name="configure-your-server-to-require-certificates"></a><span data-ttu-id="a1435-170">Configurar el servidor para que requiera certificados</span><span class="sxs-lookup"><span data-stu-id="a1435-170">Configure your server to require certificates</span></span>

### <a name="kestrel"></a><span data-ttu-id="a1435-171">Kestrel</span><span class="sxs-lookup"><span data-stu-id="a1435-171">Kestrel</span></span>

<span data-ttu-id="a1435-172">En *Program.CS*, configure Kestrel como se indica a continuación:</span><span class="sxs-lookup"><span data-stu-id="a1435-172">In *Program.cs*, configure Kestrel as follows:</span></span>

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
> <span data-ttu-id="a1435-173">Los puntos de conexión que se crean mediante una llamada a <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **antes** de llamar a <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> no tendrán aplicados los valores predeterminados.</span><span class="sxs-lookup"><span data-stu-id="a1435-173">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="iis"></a><span data-ttu-id="a1435-174">IIS</span><span class="sxs-lookup"><span data-stu-id="a1435-174">IIS</span></span>

<span data-ttu-id="a1435-175">Complete los pasos siguientes en el administrador de IIS:</span><span class="sxs-lookup"><span data-stu-id="a1435-175">Complete the following steps in IIS Manager:</span></span>

1. <span data-ttu-id="a1435-176">Seleccione su sitio en la pestaña **conexiones** .</span><span class="sxs-lookup"><span data-stu-id="a1435-176">Select your site from the **Connections** tab.</span></span>
1. <span data-ttu-id="a1435-177">Haga doble clic en la opción **configuración de SSL** en la ventana **ver características** .</span><span class="sxs-lookup"><span data-stu-id="a1435-177">Double-click the **SSL Settings** option in the **Features View** window.</span></span>
1. <span data-ttu-id="a1435-178">Active la casilla **requerir SSL** y seleccione el botón de radio **requerir** en la sección **certificados de cliente** .</span><span class="sxs-lookup"><span data-stu-id="a1435-178">Check the **Require SSL** checkbox, and select the **Require** radio button in the **Client certificates** section.</span></span>

![Configuración de certificados de cliente en IIS](README-IISConfig.png)

### <a name="azure-and-custom-web-proxies"></a><span data-ttu-id="a1435-180">Azure y proxies web personalizados</span><span class="sxs-lookup"><span data-stu-id="a1435-180">Azure and custom web proxies</span></span>

<span data-ttu-id="a1435-181">Consulte la [documentación de host e implementación](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding) para obtener información sobre cómo configurar el middleware de reenvío de certificados.</span><span class="sxs-lookup"><span data-stu-id="a1435-181">See the [host and deploy documentation](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding) for how to configure the certificate forwarding middleware.</span></span>

### <a name="use-certificate-authentication-in-azure-web-apps"></a><span data-ttu-id="a1435-182">Uso de la autenticación de certificados en Azure Web Apps</span><span class="sxs-lookup"><span data-stu-id="a1435-182">Use certificate authentication in Azure Web Apps</span></span>

<span data-ttu-id="a1435-183">No se requiere ninguna configuración de reenvío para Azure.</span><span class="sxs-lookup"><span data-stu-id="a1435-183">No forwarding configuration is required for Azure.</span></span> <span data-ttu-id="a1435-184">Esto ya está configurado en el middleware de reenvío de certificados.</span><span class="sxs-lookup"><span data-stu-id="a1435-184">This is already setup in the certificate forwarding middleware.</span></span>

> [!NOTE]
> <span data-ttu-id="a1435-185">Esto requiere que el CertificateForwardingMiddleware esté presente.</span><span class="sxs-lookup"><span data-stu-id="a1435-185">This requires that the CertificateForwardingMiddleware is present.</span></span>

### <a name="use-certificate-authentication-in-custom-web-proxies"></a><span data-ttu-id="a1435-186">Usar la autenticación de certificados en proxies web personalizados</span><span class="sxs-lookup"><span data-stu-id="a1435-186">Use certificate authentication in custom web proxies</span></span>

<span data-ttu-id="a1435-187">El `AddCertificateForwarding` método se usa para especificar:</span><span class="sxs-lookup"><span data-stu-id="a1435-187">The `AddCertificateForwarding` method is used to specify:</span></span>

* <span data-ttu-id="a1435-188">Nombre del encabezado de cliente.</span><span class="sxs-lookup"><span data-stu-id="a1435-188">The client header name.</span></span>
* <span data-ttu-id="a1435-189">Cómo se va a cargar el certificado (mediante la `HeaderConverter` propiedad).</span><span class="sxs-lookup"><span data-stu-id="a1435-189">How the certificate is to be loaded (using the `HeaderConverter` property).</span></span>

<span data-ttu-id="a1435-190">En los proxies web personalizados, el certificado se pasa como un encabezado de solicitud personalizado, por ejemplo `X-SSL-CERT` .</span><span class="sxs-lookup"><span data-stu-id="a1435-190">In custom web proxies, the certificate is passed as a custom request header, for example `X-SSL-CERT`.</span></span> <span data-ttu-id="a1435-191">Para usarlo, configure el reenvío de certificados en `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="a1435-191">To use it, configure certificate forwarding in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="a1435-192">`Startup.Configure`Después, el método agrega el middleware.</span><span class="sxs-lookup"><span data-stu-id="a1435-192">The `Startup.Configure` method then adds the middleware.</span></span> <span data-ttu-id="a1435-193">`UseCertificateForwarding`se llama a antes de llamar a `UseAuthentication` y `UseAuthorization` :</span><span class="sxs-lookup"><span data-stu-id="a1435-193">`UseCertificateForwarding` is called before the calls to `UseAuthentication` and `UseAuthorization`:</span></span>

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

<span data-ttu-id="a1435-194">Se puede usar una clase independiente para implementar la lógica de validación.</span><span class="sxs-lookup"><span data-stu-id="a1435-194">A separate class can be used to implement validation logic.</span></span> <span data-ttu-id="a1435-195">Dado que en este ejemplo se usa el mismo certificado autofirmado, asegúrese de que solo se puede usar el certificado.</span><span class="sxs-lookup"><span data-stu-id="a1435-195">Because the same self-signed certificate is used in this example, ensure that only your certificate can be used.</span></span> <span data-ttu-id="a1435-196">Compruebe que las huellas digitales del certificado de cliente y del certificado de servidor coinciden; de lo contrario, se puede usar cualquier certificado y será suficiente para autenticarse.</span><span class="sxs-lookup"><span data-stu-id="a1435-196">Validate that the thumbprints of both the client certificate and the server certificate match, otherwise any certificate can be used and will be enough to authenticate.</span></span> <span data-ttu-id="a1435-197">Se utilizará en el `AddCertificate` método.</span><span class="sxs-lookup"><span data-stu-id="a1435-197">This would be used inside the `AddCertificate` method.</span></span> <span data-ttu-id="a1435-198">También puede validar el asunto o el emisor aquí si usa certificados intermedios o secundarios.</span><span class="sxs-lookup"><span data-stu-id="a1435-198">You could also validate the subject or the issuer here if you're using intermediate or child certificates.</span></span>

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

#### <a name="implement-an-httpclient-using-a-certificate-and-the-httpclienthandler"></a><span data-ttu-id="a1435-199">Implementar un HttpClient mediante un certificado y el HttpClientHandler</span><span class="sxs-lookup"><span data-stu-id="a1435-199">Implement an HttpClient using a certificate and the HttpClientHandler</span></span>

<span data-ttu-id="a1435-200">HttpClientHandler se podría agregar directamente en el constructor de la clase HttpClient.</span><span class="sxs-lookup"><span data-stu-id="a1435-200">The HttpClientHandler could be added directly in the constructor of the HttpClient class.</span></span> <span data-ttu-id="a1435-201">Se debe tener cuidado al crear instancias del HttpClient.</span><span class="sxs-lookup"><span data-stu-id="a1435-201">Care should be taken when creating instances of the HttpClient.</span></span> <span data-ttu-id="a1435-202">A continuación, HttpClient enviará el certificado con cada solicitud.</span><span class="sxs-lookup"><span data-stu-id="a1435-202">The HttpClient will then send the certificate with each request.</span></span>

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

#### <a name="implement-an-httpclient-using-a-certificate-and-a-named-httpclient-from-ihttpclientfactory"></a><span data-ttu-id="a1435-203">Implementar un HttpClient mediante un certificado y un HttpClient con nombre de IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="a1435-203">Implement an HttpClient using a certificate and a named HttpClient from IHttpClientFactory</span></span> 

<span data-ttu-id="a1435-204">En el ejemplo siguiente, se agrega un certificado de cliente a un HttpClientHandler mediante la propiedad ClientCertificates desde el controlador.</span><span class="sxs-lookup"><span data-stu-id="a1435-204">In the following example, a client certificate is added to a HttpClientHandler using the ClientCertificates property from the handler.</span></span> <span data-ttu-id="a1435-205">Este controlador se puede usar en una instancia con nombre de HttpClient mediante el método ConfigurePrimaryHttpMessageHandler.</span><span class="sxs-lookup"><span data-stu-id="a1435-205">This handler can then be used in a named instance of a HttpClient using the ConfigurePrimaryHttpMessageHandler method.</span></span> <span data-ttu-id="a1435-206">Se configura en la clase startup en el método ConfigureServices.</span><span class="sxs-lookup"><span data-stu-id="a1435-206">This is setup in the Startup class in the ConfigureServices method.</span></span>

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

<span data-ttu-id="a1435-207">A continuación, se puede usar IHttpClientFactory para obtener la instancia con nombre con el controlador y el certificado.</span><span class="sxs-lookup"><span data-stu-id="a1435-207">The IHttpClientFactory can then be used to get the named instance with the handler and the certificate.</span></span> <span data-ttu-id="a1435-208">El método CreateClient con el nombre del cliente definido en la clase startup se usa para obtener la instancia.</span><span class="sxs-lookup"><span data-stu-id="a1435-208">The CreateClient method with the name of the client defined in the Startup class is used to get the instance.</span></span> <span data-ttu-id="a1435-209">La solicitud HTTP se puede enviar mediante el cliente, según sea necesario.</span><span class="sxs-lookup"><span data-stu-id="a1435-209">The HTTP request can be sent using the client as required.</span></span>

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

<span data-ttu-id="a1435-210">Si el certificado correcto se envía al servidor, se devuelven los datos.</span><span class="sxs-lookup"><span data-stu-id="a1435-210">If the correct certificate is sent to the server, the data is returned.</span></span> <span data-ttu-id="a1435-211">Si no se envía ningún certificado o el certificado equivocado, se devuelve un código de Estado HTTP 403.</span><span class="sxs-lookup"><span data-stu-id="a1435-211">If no certificate or the wrong certificate is sent, an HTTP 403 status code is returned.</span></span>

### <a name="create-certificates-in-powershell"></a><span data-ttu-id="a1435-212">Crear certificados en PowerShell</span><span class="sxs-lookup"><span data-stu-id="a1435-212">Create certificates in PowerShell</span></span>

<span data-ttu-id="a1435-213">La creación de los certificados es la parte más difícil de configurar este flujo.</span><span class="sxs-lookup"><span data-stu-id="a1435-213">Creating the certificates is the hardest part in setting up this flow.</span></span> <span data-ttu-id="a1435-214">Se puede crear un certificado raíz mediante el `New-SelfSignedCertificate` cmdlet de PowerShell.</span><span class="sxs-lookup"><span data-stu-id="a1435-214">A root certificate can be created using the `New-SelfSignedCertificate` PowerShell cmdlet.</span></span> <span data-ttu-id="a1435-215">Al crear el certificado, use una contraseña segura.</span><span class="sxs-lookup"><span data-stu-id="a1435-215">When creating the certificate, use a strong password.</span></span> <span data-ttu-id="a1435-216">Es importante agregar el `KeyUsageProperty` parámetro y el `KeyUsage` parámetro como se muestra.</span><span class="sxs-lookup"><span data-stu-id="a1435-216">It's important to add the `KeyUsageProperty` parameter and the `KeyUsage` parameter as shown.</span></span>

#### <a name="create-root-ca"></a><span data-ttu-id="a1435-217">Crear CA raíz</span><span class="sxs-lookup"><span data-stu-id="a1435-217">Create root CA</span></span>

```powershell
New-SelfSignedCertificate -DnsName "root_ca_dev_damienbod.com", "root_ca_dev_damienbod.com" -CertStoreLocation "cert:\LocalMachine\My" -NotAfter (Get-Date).AddYears(20) -FriendlyName "root_ca_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\root_ca_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath root_ca_dev_damienbod.crt
```

> [!NOTE]
> <span data-ttu-id="a1435-218">El `-DnsName` valor del parámetro debe coincidir con el destino de implementación de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a1435-218">The `-DnsName` parameter value must match the deployment target of the app.</span></span> <span data-ttu-id="a1435-219">Por ejemplo, "localhost" para el desarrollo.</span><span class="sxs-lookup"><span data-stu-id="a1435-219">For example, "localhost" for development.</span></span>

#### <a name="install-in-the-trusted-root"></a><span data-ttu-id="a1435-220">Instalación en la raíz de confianza</span><span class="sxs-lookup"><span data-stu-id="a1435-220">Install in the trusted root</span></span>

<span data-ttu-id="a1435-221">El certificado raíz debe ser de confianza en el sistema host.</span><span class="sxs-lookup"><span data-stu-id="a1435-221">The root certificate needs to be trusted on your host system.</span></span> <span data-ttu-id="a1435-222">De forma predeterminada, no se confiará en un certificado raíz que no haya creado ninguna entidad de certificación.</span><span class="sxs-lookup"><span data-stu-id="a1435-222">A root certificate which was not created by a certificate authority won't be trusted by default.</span></span> <span data-ttu-id="a1435-223">En el vínculo siguiente se explica cómo se puede lograr esto en Windows:</span><span class="sxs-lookup"><span data-stu-id="a1435-223">The following link explains how this can be accomplished on Windows:</span></span>

https://social.msdn.microsoft.com/Forums/SqlServer/5ed119ef-1704-4be4-8a4f-ef11de7c8f34/a-certificate-chain-processed-but-terminated-in-a-root-certificate-which-is-not-trusted-by-the

#### <a name="intermediate-certificate"></a><span data-ttu-id="a1435-224">Certificado intermedio</span><span class="sxs-lookup"><span data-stu-id="a1435-224">Intermediate certificate</span></span>

<span data-ttu-id="a1435-225">Ahora se puede crear un certificado intermedio a partir del certificado raíz.</span><span class="sxs-lookup"><span data-stu-id="a1435-225">An intermediate certificate can now be created from the root certificate.</span></span> <span data-ttu-id="a1435-226">Esto no es necesario para todos los casos de uso, pero es posible que tenga que crear muchos certificados o tener que activar o desactivar grupos de certificados.</span><span class="sxs-lookup"><span data-stu-id="a1435-226">This isn't required for all use cases, but you might need to create many certificates or need to activate or disable groups of certificates.</span></span> <span data-ttu-id="a1435-227">El `TextExtension` parámetro es necesario para establecer la longitud de la ruta de acceso en las restricciones básicas del certificado.</span><span class="sxs-lookup"><span data-stu-id="a1435-227">The `TextExtension` parameter is required to set the path length in the basic constraints of the certificate.</span></span>

<span data-ttu-id="a1435-228">Después, el certificado intermedio se puede Agregar al certificado intermedio de confianza en el sistema host de Windows.</span><span class="sxs-lookup"><span data-stu-id="a1435-228">The intermediate certificate can then be added to the trusted intermediate certificate in the Windows host system.</span></span>

```powershell
$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint of the root..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "intermediate_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "intermediate_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature -TextExtension @("2.5.29.19={text}CA=1&pathlength=1")

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\intermediate_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath intermediate_dev_damienbod.crt
```

#### <a name="create-child-certificate-from-intermediate-certificate"></a><span data-ttu-id="a1435-229">Crear certificado secundario a partir de un certificado intermedio</span><span class="sxs-lookup"><span data-stu-id="a1435-229">Create child certificate from intermediate certificate</span></span>

<span data-ttu-id="a1435-230">Se puede crear un certificado secundario a partir del certificado intermedio.</span><span class="sxs-lookup"><span data-stu-id="a1435-230">A child certificate can be created from the intermediate certificate.</span></span> <span data-ttu-id="a1435-231">Se trata de la entidad final y no es necesario crear más certificados secundarios.</span><span class="sxs-lookup"><span data-stu-id="a1435-231">This is the end entity and doesn't need to create more child certificates.</span></span>

```powershell
$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint from the Intermediate certificate..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com"

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath child_a_dev_damienbod.crt
```

#### <a name="create-child-certificate-from-root-certificate"></a><span data-ttu-id="a1435-232">Crear certificado secundario a partir del certificado raíz</span><span class="sxs-lookup"><span data-stu-id="a1435-232">Create child certificate from root certificate</span></span>

<span data-ttu-id="a1435-233">También se puede crear un certificado secundario a partir del certificado raíz directamente.</span><span class="sxs-lookup"><span data-stu-id="a1435-233">A child certificate can also be created from the root certificate directly.</span></span> 

```powershell
$rootcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint from the root cert..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $rootcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com"

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath child_a_dev_damienbod.crt
```

#### <a name="example-root---intermediate-certificate---certificate"></a><span data-ttu-id="a1435-234">Ejemplo de certificado intermedio de raíz: certificado</span><span class="sxs-lookup"><span data-stu-id="a1435-234">Example root - intermediate certificate - certificate</span></span>

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

<span data-ttu-id="a1435-235">Al usar los certificados raíz, intermedios o secundarios, los certificados se pueden validar con la huella digital o la PublicKey según sea necesario.</span><span class="sxs-lookup"><span data-stu-id="a1435-235">When using the root, intermediate, or child certificates, the certificates can be validated using the Thumbprint or PublicKey as required.</span></span>

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

<a name="occ"></a>

## <a name="optional-client-certificates"></a><span data-ttu-id="a1435-236">Certificados de cliente opcionales</span><span class="sxs-lookup"><span data-stu-id="a1435-236">Optional client certificates</span></span>

<span data-ttu-id="a1435-237">En esta sección se proporciona información para las aplicaciones que deben proteger un subconjunto de la aplicación con un certificado.</span><span class="sxs-lookup"><span data-stu-id="a1435-237">This section provides information for apps that must protect a subset of the app with a certificate.</span></span> <span data-ttu-id="a1435-238">Por ejemplo, una Razor página o un controlador de la aplicación podrían requerir certificados de cliente.</span><span class="sxs-lookup"><span data-stu-id="a1435-238">For example, a Razor Page or controller in the app might require client certificates.</span></span> <span data-ttu-id="a1435-239">Esto presenta desafíos como certificados de cliente:</span><span class="sxs-lookup"><span data-stu-id="a1435-239">This presents challenges as client certificates:</span></span>
  
* <span data-ttu-id="a1435-240">Son una característica de TLS, no una característica de HTTP.</span><span class="sxs-lookup"><span data-stu-id="a1435-240">Are a TLS feature, not an HTTP feature.</span></span>
* <span data-ttu-id="a1435-241">Se negocian por conexión y se deben negociar al inicio de la conexión antes de que estén disponibles los datos HTTP.</span><span class="sxs-lookup"><span data-stu-id="a1435-241">Are negotiated per-connection and must be be negotiated at the start of the connection before any HTTP data is available.</span></span> <span data-ttu-id="a1435-242">En el inicio de la conexión, solo se conoce el Indicación de nombre de servidor (SNI) &dagger; .</span><span class="sxs-lookup"><span data-stu-id="a1435-242">At the start of the connection, only the Server Name Indication (SNI)&dagger; is known.</span></span> <span data-ttu-id="a1435-243">Los certificados de cliente y servidor se negocian antes de la primera solicitud en una conexión y las solicitudes por lo general no podrán volver a negociar.</span><span class="sxs-lookup"><span data-stu-id="a1435-243">The client and server certificates are negotiated prior to the first request on a connection and requests generally won't be able to renegotiate.</span></span> <span data-ttu-id="a1435-244">La renegociación está prohibida en HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="a1435-244">Renegotiation is prohibited in HTTP/2.</span></span>

<span data-ttu-id="a1435-245">ASP.NET Core 5 Preview 4 y versiones posteriores agregan una compatibilidad más cómoda para los certificados de cliente opcionales.</span><span class="sxs-lookup"><span data-stu-id="a1435-245">ASP.NET Core 5 preview 4 and later adds more convenient support for optional client certificates.</span></span> <span data-ttu-id="a1435-246">Para obtener más información, vea el [ejemplo de certificados opcionales](https://github.com/dotnet/aspnetcore/tree/9ce4a970a21bace3fb262da9591ed52359309592/src/Security/Authentication/Certificate/samples/Certificate.Optional.Sample).</span><span class="sxs-lookup"><span data-stu-id="a1435-246">For more information, see the [Optional certificates sample](https://github.com/dotnet/aspnetcore/tree/9ce4a970a21bace3fb262da9591ed52359309592/src/Security/Authentication/Certificate/samples/Certificate.Optional.Sample).</span></span>

<span data-ttu-id="a1435-247">El siguiente enfoque admite certificados de cliente opcionales:</span><span class="sxs-lookup"><span data-stu-id="a1435-247">The following approach supports optional client certificates:</span></span>

* <span data-ttu-id="a1435-248">Configure el enlace para el dominio y el subdominio:</span><span class="sxs-lookup"><span data-stu-id="a1435-248">Set up binding for the domain and subdomain:</span></span>
  * <span data-ttu-id="a1435-249">Por ejemplo, configure los enlaces en `contoso.com` y `myClient.contoso.com` .</span><span class="sxs-lookup"><span data-stu-id="a1435-249">For example, set up bindings on `contoso.com` and `myClient.contoso.com`.</span></span> <span data-ttu-id="a1435-250">El `contoso.com` host no requiere un certificado de cliente, pero sí lo `myClient.contoso.com` hace.</span><span class="sxs-lookup"><span data-stu-id="a1435-250">The `contoso.com` host doesn't require a client certificate but `myClient.contoso.com` does.</span></span>
  * <span data-ttu-id="a1435-251">Para obtener más información, consulte:</span><span class="sxs-lookup"><span data-stu-id="a1435-251">For more information, see:</span></span>
    * <span data-ttu-id="a1435-252">[Kestrel](/fundamentals/servers/kestrel):</span><span class="sxs-lookup"><span data-stu-id="a1435-252">[Kestrel](/fundamentals/servers/kestrel):</span></span>
      * [<span data-ttu-id="a1435-253">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="a1435-253">ListenOptions.UseHttps</span></span>](xref:fundamentals/servers/kestrel#listenoptionsusehttps)
      * <xref:Microsoft.AspNetCore.Server.Kestrel.Https.HttpsConnectionAdapterOptions.ClientCertificateMode>
      * <span data-ttu-id="a1435-254">Nota Kestrel no admite actualmente varias configuraciones de TLS en un enlace, necesitará dos enlaces con direcciones IP o puertos únicos.</span><span class="sxs-lookup"><span data-stu-id="a1435-254">Note Kestrel does not currently support multiple TLS configurations on one binding, you'll need two bindings with unique IPs or ports.</span></span> <span data-ttu-id="a1435-255">Vea https://github.com/dotnet/runtime/issues/31097.</span><span class="sxs-lookup"><span data-stu-id="a1435-255">See https://github.com/dotnet/runtime/issues/31097</span></span>
    * <span data-ttu-id="a1435-256">IIS</span><span class="sxs-lookup"><span data-stu-id="a1435-256">IIS</span></span>
      * [<span data-ttu-id="a1435-257">Hospedaje de IIS</span><span class="sxs-lookup"><span data-stu-id="a1435-257">Hosting IIS</span></span>](xref:host-and-deploy/iis/index#create-the-iis-site)
      * [<span data-ttu-id="a1435-258">Configurar la seguridad en IIS</span><span class="sxs-lookup"><span data-stu-id="a1435-258">Configure security on IIS</span></span>](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis#configure-ssl-settings-2)
    * <span data-ttu-id="a1435-259">Http.Sys: [configurar Windows Server](xref:fundamentals/servers/httpsys#configure-windows-server)</span><span class="sxs-lookup"><span data-stu-id="a1435-259">Http.Sys: [Configure Windows Server](xref:fundamentals/servers/httpsys#configure-windows-server)</span></span>
* <span data-ttu-id="a1435-260">Para las solicitudes a la aplicación web que requieren un certificado de cliente y no tienen una:</span><span class="sxs-lookup"><span data-stu-id="a1435-260">For requests to the web app that require a client certificate and don't have one:</span></span>
  * <span data-ttu-id="a1435-261">Redirigir a la misma página mediante el subdominio protegido por el certificado de cliente.</span><span class="sxs-lookup"><span data-stu-id="a1435-261">Redirect to the same page using the client certificate protected subdomain.</span></span>
  * <span data-ttu-id="a1435-262">Por ejemplo, redirigir a `myClient.contoso.com/requestedPage` .</span><span class="sxs-lookup"><span data-stu-id="a1435-262">For example, redirect to `myClient.contoso.com/requestedPage`.</span></span> <span data-ttu-id="a1435-263">Dado que la solicitud a `myClient.contoso.com/requestedPage` es un nombre de host diferente que `contoso.com/requestedPage` , el cliente establece una conexión diferente y se proporciona el certificado de cliente.</span><span class="sxs-lookup"><span data-stu-id="a1435-263">Because the request to `myClient.contoso.com/requestedPage` is a different hostname than `contoso.com/requestedPage`, the client establishes a different connection and the client certificate is provided.</span></span>
  * <span data-ttu-id="a1435-264">Para obtener más información, vea <xref:security/authorization/introduction>.</span><span class="sxs-lookup"><span data-stu-id="a1435-264">For more information, see <xref:security/authorization/introduction>.</span></span>

<span data-ttu-id="a1435-265">Deje preguntas, comentarios y otros comentarios sobre los certificados de cliente opcionales en este problema de [discusión de github](https://github.com/dotnet/AspNetCore.Docs/issues/18720) .</span><span class="sxs-lookup"><span data-stu-id="a1435-265">Leave questions, comments, and other feedback on optional client certificates in [this GitHub discussion](https://github.com/dotnet/AspNetCore.Docs/issues/18720) issue.</span></span>

<span data-ttu-id="a1435-266">&dagger;Indicación de nombre de servidor (SNI) es una extensión de TLS para incluir un dominio virtual como parte de la negociación SSL.</span><span class="sxs-lookup"><span data-stu-id="a1435-266">&dagger; Server Name Indication (SNI) is a TLS extension to include a virtual domain as a part of SSL negotiation.</span></span> <span data-ttu-id="a1435-267">Esto significa que el nombre de dominio virtual, o un nombre de host, se puede usar para identificar el extremo de la red.</span><span class="sxs-lookup"><span data-stu-id="a1435-267">This effectively means the virtual domain name, or a hostname, can be used to identify the network end point.</span></span>
