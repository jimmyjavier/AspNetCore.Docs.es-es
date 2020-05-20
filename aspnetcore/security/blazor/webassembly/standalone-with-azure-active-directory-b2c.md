---
<span data-ttu-id="747e7-101">Título: "proteger ASP.NET Core una Blazor aplicación independiente Webassembly con Azure Active Directory B2C ' Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="747e7-101">title: 'Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory B2C' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="747e7-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="747e7-102">'Blazor'</span></span>
- <span data-ttu-id="747e7-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="747e7-103">'Identity'</span></span>
- <span data-ttu-id="747e7-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="747e7-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="747e7-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="747e7-105">'Razor'</span></span>
- <span data-ttu-id="747e7-106">SignalRUID ' ':</span><span class="sxs-lookup"><span data-stu-id="747e7-106">'SignalR' uid:</span></span> 

---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a><span data-ttu-id="747e7-107">Protección de una Blazor aplicación independiente ASP.net Core Webassembly con Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="747e7-107">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory B2C</span></span>

<span data-ttu-id="747e7-108">Por [Javier Calvarro Nelson](https://github.com/javiercn) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="747e7-108">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="747e7-109">Para crear una Blazor aplicación independiente Webassembly que use [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) para la autenticación:</span><span class="sxs-lookup"><span data-stu-id="747e7-109">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication:</span></span>

<span data-ttu-id="747e7-110">Siga las instrucciones de los temas siguientes para crear un inquilino y registrar una aplicación web en Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="747e7-110">Follow the guidance in the following topics to create a tenant and register a web app in the Azure Portal:</span></span>

[<span data-ttu-id="747e7-111">Creación de un inquilino de AAD B2C</span><span class="sxs-lookup"><span data-stu-id="747e7-111">Create an AAD B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)

<span data-ttu-id="747e7-112">Registre la siguiente información:</span><span class="sxs-lookup"><span data-stu-id="747e7-112">Record the following information:</span></span>

* <span data-ttu-id="747e7-113">AAD B2C instancia (por ejemplo, `https://contoso.b2clogin.com/` , que incluye la barra diagonal final).</span><span class="sxs-lookup"><span data-stu-id="747e7-113">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash).</span></span>
* <span data-ttu-id="747e7-114">AAD B2C dominio del inquilino (por ejemplo, `contoso.onmicrosoft.com` ).</span><span class="sxs-lookup"><span data-stu-id="747e7-114">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="747e7-115">Siga las instrucciones de [Tutorial: registro de una aplicación en Azure Active Directory B2C de](/azure/active-directory-b2c/tutorial-register-applications) nuevo para registrar una aplicación de AAD para la *aplicación cliente*:</span><span class="sxs-lookup"><span data-stu-id="747e7-115">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *Client app*:</span></span>

1. <span data-ttu-id="747e7-116">En **Azure Active Directory**  >  **registros de aplicaciones**, seleccione **nuevo registro**.</span><span class="sxs-lookup"><span data-stu-id="747e7-116">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="747e7-117">Proporcione un **nombre** para la aplicación (por ejemplo, \*\* Blazor AAD B2C independiente\*\*).</span><span class="sxs-lookup"><span data-stu-id="747e7-117">Provide a **Name** for the app (for example, **Blazor Standalone AAD B2C**).</span></span>
1. <span data-ttu-id="747e7-118">En **tipos de cuenta compatibles**, seleccione la opción de varios inquilinos: **cuentas en cualquier directorio de la organización o cualquier proveedor de identidades. Para autenticar a los usuarios con Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="747e7-118">For **Supported account types**, select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="747e7-119">Deje la lista desplegable **URI de redirección** establecida en **Web**y proporcione el siguiente URI de redirección: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="747e7-119">Leave the **Redirect URI** drop down set to **Web**, and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="747e7-120">El puerto predeterminado para una aplicación que se ejecuta en Kestrel es 5001.</span><span class="sxs-lookup"><span data-stu-id="747e7-120">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="747e7-121">Por IIS Express, el puerto generado aleatoriamente se puede encontrar en las propiedades de la aplicación en el panel **depurar** .</span><span class="sxs-lookup"><span data-stu-id="747e7-121">For IIS Express, the randomly generated port can be found in the app's properties in the **Debug** panel.</span></span>
1. <span data-ttu-id="747e7-122">Confirme que **permisos**  >  **conceder permisos de administrador a OpenID y offline_access** está habilitado.</span><span class="sxs-lookup"><span data-stu-id="747e7-122">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="747e7-123">Seleccione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="747e7-123">Select **Register**.</span></span>

<span data-ttu-id="747e7-124">Registre el identificador de aplicación (ID. de cliente) (por ejemplo, `11111111-1111-1111-1111-111111111111` ).</span><span class="sxs-lookup"><span data-stu-id="747e7-124">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

<span data-ttu-id="747e7-125">En configuración de la plataforma de **autenticación**  >  **Platform configurations**  >  **Web**:</span><span class="sxs-lookup"><span data-stu-id="747e7-125">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="747e7-126">Confirme que el **URI de redirección** de `https://localhost:{PORT}/authentication/login-callback` está presente.</span><span class="sxs-lookup"><span data-stu-id="747e7-126">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="747e7-127">En **concesión implícita**, active las casillas de verificación de **tokens de acceso** y **tokens de identificador**.</span><span class="sxs-lookup"><span data-stu-id="747e7-127">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="747e7-128">Los valores predeterminados restantes de la aplicación son aceptables para esta experiencia.</span><span class="sxs-lookup"><span data-stu-id="747e7-128">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="747e7-129">Seleccione el botón **Guardar**.</span><span class="sxs-lookup"><span data-stu-id="747e7-129">Select the **Save** button.</span></span>

<span data-ttu-id="747e7-130">En **Home**  >  **Azure ad B2C**  >  **flujos de usuario**:</span><span class="sxs-lookup"><span data-stu-id="747e7-130">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="747e7-131">Creación de un flujo de usuario de registro e inicio de sesión</span><span class="sxs-lookup"><span data-stu-id="747e7-131">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="747e7-132">Como mínimo, seleccione el **Application claims**  >  atributo de usuario**nombre para mostrar** de notificaciones de aplicación para rellenar el `context.User.Identity.Name` en el `LoginDisplay` componente (*Shared/LoginDisplay. Razor*).</span><span class="sxs-lookup"><span data-stu-id="747e7-132">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

<span data-ttu-id="747e7-133">Registre el nombre del flujo de usuario de inicio de sesión y de registro creado para la aplicación (por ejemplo, `B2C_1_signupsignin` ).</span><span class="sxs-lookup"><span data-stu-id="747e7-133">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

<span data-ttu-id="747e7-134">Reemplace los marcadores de posición en el siguiente comando por la información registrada anteriormente y ejecute el comando en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="747e7-134">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{TENANT DOMAIN}" -ssp "{SIGN UP OR SIGN IN POLICY}"
```

<span data-ttu-id="747e7-135">Para especificar la ubicación de salida, que crea una carpeta de proyecto si no existe, incluya la opción output en el comando con una ruta de acceso (por ejemplo, `-o BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="747e7-135">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="747e7-136">El nombre de la carpeta también se convierte en parte del nombre del proyecto.</span><span class="sxs-lookup"><span data-stu-id="747e7-136">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="747e7-137">Después de crear la aplicación, debe poder:</span><span class="sxs-lookup"><span data-stu-id="747e7-137">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="747e7-138">Inicie sesión en la aplicación con una cuenta de usuario de AAD.</span><span class="sxs-lookup"><span data-stu-id="747e7-138">Log into the app using an AAD user account.</span></span>
* <span data-ttu-id="747e7-139">Solicitar tokens de acceso para las API de Microsoft.</span><span class="sxs-lookup"><span data-stu-id="747e7-139">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="747e7-140">Para más información, consulte:</span><span class="sxs-lookup"><span data-stu-id="747e7-140">For more information, see:</span></span>
  * [<span data-ttu-id="747e7-141">Ámbitos de token de acceso</span><span class="sxs-lookup"><span data-stu-id="747e7-141">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="747e7-142">[Inicio rápido: configurar una aplicación para exponer las API Web](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span><span class="sxs-lookup"><span data-stu-id="747e7-142">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="747e7-143">Paquete de autenticación</span><span class="sxs-lookup"><span data-stu-id="747e7-143">Authentication package</span></span>

<span data-ttu-id="747e7-144">Cuando se crea una aplicación para usar una cuenta de B2C individual ( `IndividualB2C` ), la aplicación recibe automáticamente una referencia de paquete para la [biblioteca de autenticación de Microsoft](/azure/active-directory/develop/msal-overview) ( `Microsoft.Authentication.WebAssembly.Msal` ).</span><span class="sxs-lookup"><span data-stu-id="747e7-144">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="747e7-145">El paquete proporciona un conjunto de primitivas que ayudan a la aplicación a autenticar a los usuarios y obtener tokens para llamar a las API protegidas.</span><span class="sxs-lookup"><span data-stu-id="747e7-145">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="747e7-146">Si agrega la autenticación a una aplicación, agregue manualmente el paquete al archivo de proyecto de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="747e7-146">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="747e7-147">El `Microsoft.Authentication.WebAssembly.Msal` paquete agrega de manera transitiva el `Microsoft.AspNetCore.Components.WebAssembly.Authentication` paquete a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="747e7-147">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="747e7-148">Compatibilidad con el servicio de autenticación</span><span class="sxs-lookup"><span data-stu-id="747e7-148">Authentication service support</span></span>

<span data-ttu-id="747e7-149">La compatibilidad con la autenticación de usuarios se registra en el contenedor de servicios con el `AddMsalAuthentication` método de extensión proporcionado por el `Microsoft.Authentication.WebAssembly.Msal` paquete.</span><span class="sxs-lookup"><span data-stu-id="747e7-149">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="747e7-150">Este método configura todos los servicios necesarios para que la aplicación interactúe con el Identity proveedor (IP).</span><span class="sxs-lookup"><span data-stu-id="747e7-150">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="747e7-151">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="747e7-151">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="747e7-152">El `AddMsalAuthentication` método acepta una devolución de llamada para configurar los parámetros necesarios para autenticar una aplicación.</span><span class="sxs-lookup"><span data-stu-id="747e7-152">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="747e7-153">Los valores necesarios para configurar la aplicación se pueden obtener a partir de la configuración de AAD al registrar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="747e7-153">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="747e7-154">La configuración se proporciona mediante el archivo *wwwroot/appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="747e7-154">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

<span data-ttu-id="747e7-155">Ejemplo:</span><span class="sxs-lookup"><span data-stu-id="747e7-155">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": false
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="747e7-156">Ámbitos de token de acceso</span><span class="sxs-lookup"><span data-stu-id="747e7-156">Access token scopes</span></span>

<span data-ttu-id="747e7-157">La Blazor plantilla Webassembly no configura automáticamente la aplicación para solicitar un token de acceso para una API segura.</span><span class="sxs-lookup"><span data-stu-id="747e7-157">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="747e7-158">Para aprovisionar un token de acceso como parte del flujo de inicio de sesión, agregue el ámbito a los ámbitos de token de acceso predeterminados de `MsalProviderOptions` :</span><span class="sxs-lookup"><span data-stu-id="747e7-158">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="747e7-159">Para obtener más información, consulte las siguientes secciones del artículo *escenarios adicionales* :</span><span class="sxs-lookup"><span data-stu-id="747e7-159">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="747e7-160">Solicitar tokens de acceso adicionales</span><span class="sxs-lookup"><span data-stu-id="747e7-160">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="747e7-161">Adjuntar tokens a solicitudes salientes</span><span class="sxs-lookup"><span data-stu-id="747e7-161">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="747e7-162">Importar archivo</span><span class="sxs-lookup"><span data-stu-id="747e7-162">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="747e7-163">Página de índice</span><span class="sxs-lookup"><span data-stu-id="747e7-163">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="747e7-164">Componente de aplicación</span><span class="sxs-lookup"><span data-stu-id="747e7-164">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="747e7-165">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="747e7-165">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="747e7-166">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="747e7-166">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="747e7-167">Componente de autenticación</span><span class="sxs-lookup"><span data-stu-id="747e7-167">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="747e7-168">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="747e7-168">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="747e7-169">Solicitudes de API Web no autenticadas o no autorizadas en una aplicación con un cliente predeterminado seguro</span><span class="sxs-lookup"><span data-stu-id="747e7-169">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="747e7-170">Tutorial: Creación de un inquilino de Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="747e7-170">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="747e7-171">Documentación de la plataforma de identidad de Microsoft</span><span class="sxs-lookup"><span data-stu-id="747e7-171">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
