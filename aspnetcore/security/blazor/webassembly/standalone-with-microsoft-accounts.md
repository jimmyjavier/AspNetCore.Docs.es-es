---
<span data-ttu-id="cc5d5-101">title: "proteger ASP.NET Core una Blazor aplicación independiente de Webassembly con cuentas de Microsoft" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="cc5d5-101">title: 'Secure an ASP.NET Core Blazor WebAssembly standalone app with Microsoft Accounts' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cc5d5-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cc5d5-102">'Blazor'</span></span>
- <span data-ttu-id="cc5d5-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cc5d5-103">'Identity'</span></span>
- <span data-ttu-id="cc5d5-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cc5d5-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="cc5d5-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cc5d5-105">'Razor'</span></span>
- <span data-ttu-id="cc5d5-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cc5d5-106">'SignalR' uid:</span></span> 

---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-microsoft-accounts"></a><span data-ttu-id="cc5d5-107">Protección de una Blazor aplicación independiente ASP.net Core Webassembly con cuentas Microsoft</span><span class="sxs-lookup"><span data-stu-id="cc5d5-107">Secure an ASP.NET Core Blazor WebAssembly standalone app with Microsoft Accounts</span></span>

<span data-ttu-id="cc5d5-108">Por [Javier Calvarro Nelson](https://github.com/javiercn) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="cc5d5-108">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="cc5d5-109">Para crear una Blazor aplicación independiente Webassembly que use [cuentas de Microsoft con Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) para la autenticación:</span><span class="sxs-lookup"><span data-stu-id="cc5d5-109">To create a Blazor WebAssembly standalone app that uses [Microsoft Accounts with Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) for authentication:</span></span>

[<span data-ttu-id="cc5d5-110">Creación de un inquilino de AAD y una aplicación Web</span><span class="sxs-lookup"><span data-stu-id="cc5d5-110">Create an AAD tenant and web application</span></span>](/azure/active-directory/develop/v2-overview)

<span data-ttu-id="cc5d5-111">Registre una aplicación de AAD en **Azure Active Directory**el  >  área de**registros de aplicaciones** de Azure Active Directory del Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="cc5d5-111">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="cc5d5-112">Proporcione un **nombre** para la aplicación (por ejemplo, \*\* Blazor cuentas de Microsoft de AAD independientes\*\*).</span><span class="sxs-lookup"><span data-stu-id="cc5d5-112">Provide a **Name** for the app (for example, **Blazor Standalone AAD Microsoft Accounts**).</span></span>
1. <span data-ttu-id="cc5d5-113">En **tipos de cuenta compatibles**, seleccione **cuentas en cualquier directorio de la organización**.</span><span class="sxs-lookup"><span data-stu-id="cc5d5-113">In **Supported account types**, select **Accounts in any organizational directory**.</span></span>
1. <span data-ttu-id="cc5d5-114">Deje la lista desplegable **URI de redirección** establecida en **Web** y proporcione el siguiente URI de redirección: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="cc5d5-114">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="cc5d5-115">El puerto predeterminado para una aplicación que se ejecuta en Kestrel es 5001.</span><span class="sxs-lookup"><span data-stu-id="cc5d5-115">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="cc5d5-116">Si la aplicación se ejecuta en otro puerto Kestrel, use el puerto de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="cc5d5-116">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="cc5d5-117">Por IIS Express, el puerto generado de forma aleatoria para la aplicación se puede encontrar en las propiedades de la aplicación en el panel **depurar** .</span><span class="sxs-lookup"><span data-stu-id="cc5d5-117">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="cc5d5-118">Dado que la aplicación no existe en este momento y no se conoce el puerto IIS Express, vuelva a este paso después de crear la aplicación y actualice el URI de redirección.</span><span class="sxs-lookup"><span data-stu-id="cc5d5-118">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="cc5d5-119">Más adelante en este tema aparece un comentario para recordar a IIS Express usuarios que actualicen el URI de redirección.</span><span class="sxs-lookup"><span data-stu-id="cc5d5-119">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="cc5d5-120">Deshabilite **Permissions**la  >  casilla**conceder permisos Grant admin to OpenID y offline_access permisos** .</span><span class="sxs-lookup"><span data-stu-id="cc5d5-120">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="cc5d5-121">Seleccione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="cc5d5-121">Select **Register**.</span></span>

<span data-ttu-id="cc5d5-122">Registre el identificador de aplicación (ID. de cliente) (por ejemplo, `11111111-1111-1111-1111-111111111111` ).</span><span class="sxs-lookup"><span data-stu-id="cc5d5-122">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

<span data-ttu-id="cc5d5-123">En configuración de la plataforma de **autenticación**  >  **Platform configurations**  >  **Web**:</span><span class="sxs-lookup"><span data-stu-id="cc5d5-123">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="cc5d5-124">Confirme que el **URI de redirección** de `https://localhost:{PORT}/authentication/login-callback` está presente.</span><span class="sxs-lookup"><span data-stu-id="cc5d5-124">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="cc5d5-125">En **concesión implícita**, active las casillas de verificación de **tokens de acceso** y **tokens de identificador**.</span><span class="sxs-lookup"><span data-stu-id="cc5d5-125">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="cc5d5-126">Los valores predeterminados restantes de la aplicación son aceptables para esta experiencia.</span><span class="sxs-lookup"><span data-stu-id="cc5d5-126">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="cc5d5-127">Seleccione el botón **Guardar**.</span><span class="sxs-lookup"><span data-stu-id="cc5d5-127">Select the **Save** button.</span></span>

<span data-ttu-id="cc5d5-128">Crear la aplicación.</span><span class="sxs-lookup"><span data-stu-id="cc5d5-128">Create the app.</span></span> <span data-ttu-id="cc5d5-129">Reemplace los marcadores de posición en el siguiente comando por la información registrada anteriormente y ejecute el siguiente comando en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="cc5d5-129">Replace the placeholders in the following command with the information recorded earlier and execute the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common"
```

<span data-ttu-id="cc5d5-130">Para especificar la ubicación de salida, que crea una carpeta de proyecto si no existe, incluya la opción output en el comando con una ruta de acceso (por ejemplo, `-o BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="cc5d5-130">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="cc5d5-131">El nombre de la carpeta también se convierte en parte del nombre del proyecto.</span><span class="sxs-lookup"><span data-stu-id="cc5d5-131">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="cc5d5-132">En el Azure portal, el URI de **Authentication**redireccionamiento web de configuración de la plataforma de autenticación de la aplicación  >  **Platform configurations**  >  **Web**  >  **Redirect URI** se configura para el puerto 5001 para las aplicaciones que se ejecutan en el servidor de Kestrel con la configuración predeterminada.</span><span class="sxs-lookup"><span data-stu-id="cc5d5-132">In the Azure portal, the app's **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="cc5d5-133">Si la aplicación se ejecuta en un puerto de IIS Express aleatorio, el puerto de la aplicación se puede encontrar en las propiedades de la aplicación en el panel de **depuración** .</span><span class="sxs-lookup"><span data-stu-id="cc5d5-133">If the app is run on a random IIS Express port, the port for the app can be found in the app's properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="cc5d5-134">Si el puerto no se configuró anteriormente con el puerto conocido de la aplicación, vuelva al registro de la aplicación en el Azure Portal y actualice el URI de redirección con el puerto correcto.</span><span class="sxs-lookup"><span data-stu-id="cc5d5-134">If the port wasn't configured earlier with the app's known port, return to the app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

<span data-ttu-id="cc5d5-135">Después de crear la aplicación, debe poder:</span><span class="sxs-lookup"><span data-stu-id="cc5d5-135">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="cc5d5-136">Inicie sesión en la aplicación con un cuenta de Microsoft.</span><span class="sxs-lookup"><span data-stu-id="cc5d5-136">Log into the app using a Microsoft account.</span></span>
* <span data-ttu-id="cc5d5-137">Solicitar tokens de acceso para las API de Microsoft.</span><span class="sxs-lookup"><span data-stu-id="cc5d5-137">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="cc5d5-138">Para más información, consulte:</span><span class="sxs-lookup"><span data-stu-id="cc5d5-138">For more information, see:</span></span>
  * [<span data-ttu-id="cc5d5-139">Ámbitos de token de acceso</span><span class="sxs-lookup"><span data-stu-id="cc5d5-139">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="cc5d5-140">[Inicio rápido: configurar una aplicación para exponer las API Web](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span><span class="sxs-lookup"><span data-stu-id="cc5d5-140">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="cc5d5-141">Paquete de autenticación</span><span class="sxs-lookup"><span data-stu-id="cc5d5-141">Authentication package</span></span>

<span data-ttu-id="cc5d5-142">Cuando se crea una aplicación para usar cuentas profesionales o educativas ( `SingleOrg` ), la aplicación recibe automáticamente una referencia de paquete para la [biblioteca de autenticación de Microsoft](/azure/active-directory/develop/msal-overview) ([Microsoft. Authentication. webassembly. msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span><span class="sxs-lookup"><span data-stu-id="cc5d5-142">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span></span> <span data-ttu-id="cc5d5-143">El paquete proporciona un conjunto de primitivas que ayudan a la aplicación a autenticar a los usuarios y obtener tokens para llamar a las API protegidas.</span><span class="sxs-lookup"><span data-stu-id="cc5d5-143">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="cc5d5-144">Si agrega la autenticación a una aplicación, agregue manualmente el paquete al archivo de proyecto de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="cc5d5-144">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="cc5d5-145">El paquete [Microsoft. Authentication. Webassembly. msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) agrega de manera transitiva el paquete [Microsoft. AspNetCore. Components. webassembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="cc5d5-145">The [Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package transitively adds the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="cc5d5-146">Compatibilidad con el servicio de autenticación</span><span class="sxs-lookup"><span data-stu-id="cc5d5-146">Authentication service support</span></span>

<span data-ttu-id="cc5d5-147">La compatibilidad con la autenticación de usuarios se registra en el contenedor de servicios con el <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> método de extensión proporcionado por el paquete [Microsoft. Authentication. webassembly. msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) .</span><span class="sxs-lookup"><span data-stu-id="cc5d5-147">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package.</span></span> <span data-ttu-id="cc5d5-148">Este método configura todos los servicios necesarios para que la aplicación interactúe con el Identity proveedor (IP).</span><span class="sxs-lookup"><span data-stu-id="cc5d5-148">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="cc5d5-149">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="cc5d5-149">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="cc5d5-150">El <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> método acepta una devolución de llamada para configurar los parámetros necesarios para autenticar una aplicación.</span><span class="sxs-lookup"><span data-stu-id="cc5d5-150">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="cc5d5-151">Los valores necesarios para configurar la aplicación se pueden obtener a partir de la configuración de AAD al registrar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="cc5d5-151">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="cc5d5-152">La configuración se proporciona mediante el archivo *wwwroot/appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="cc5d5-152">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="cc5d5-153">Ejemplo:</span><span class="sxs-lookup"><span data-stu-id="cc5d5-153">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="cc5d5-154">Ámbitos de token de acceso</span><span class="sxs-lookup"><span data-stu-id="cc5d5-154">Access token scopes</span></span>

<span data-ttu-id="cc5d5-155">La Blazor plantilla Webassembly no configura automáticamente la aplicación para solicitar un token de acceso para una API segura.</span><span class="sxs-lookup"><span data-stu-id="cc5d5-155">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="cc5d5-156">Para aprovisionar un token de acceso como parte del flujo de inicio de sesión, agregue el ámbito a los ámbitos de token de acceso predeterminados de <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> :</span><span class="sxs-lookup"><span data-stu-id="cc5d5-156">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="cc5d5-157">Para obtener más información, consulte las siguientes secciones del artículo *escenarios adicionales* :</span><span class="sxs-lookup"><span data-stu-id="cc5d5-157">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="cc5d5-158">Solicitar tokens de acceso adicionales</span><span class="sxs-lookup"><span data-stu-id="cc5d5-158">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="cc5d5-159">Adjuntar tokens a solicitudes salientes</span><span class="sxs-lookup"><span data-stu-id="cc5d5-159">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="cc5d5-160">Importar archivo</span><span class="sxs-lookup"><span data-stu-id="cc5d5-160">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="cc5d5-161">Página de índice</span><span class="sxs-lookup"><span data-stu-id="cc5d5-161">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="cc5d5-162">Componente de aplicación</span><span class="sxs-lookup"><span data-stu-id="cc5d5-162">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="cc5d5-163">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="cc5d5-163">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="cc5d5-164">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="cc5d5-164">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="cc5d5-165">Componente de autenticación</span><span class="sxs-lookup"><span data-stu-id="cc5d5-165">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="cc5d5-166">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="cc5d5-166">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="cc5d5-167">Solicitudes de API Web no autenticadas o no autorizadas en una aplicación con un cliente predeterminado seguro</span><span class="sxs-lookup"><span data-stu-id="cc5d5-167">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/blazor/webassembly/aad-groups-roles>
* [<span data-ttu-id="cc5d5-168">Inicio rápido: Registro de una aplicación en la plataforma de identidad de Microsoft</span><span class="sxs-lookup"><span data-stu-id="cc5d5-168">Quickstart: Register an application with the Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [<span data-ttu-id="cc5d5-169">Inicio rápido: Configuración de una aplicación para exponer las API web</span><span class="sxs-lookup"><span data-stu-id="cc5d5-169">Quickstart: Configure an application to expose web APIs</span></span>](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)
