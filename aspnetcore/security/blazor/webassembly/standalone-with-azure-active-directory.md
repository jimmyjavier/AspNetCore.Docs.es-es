---
<span data-ttu-id="48e14-101">Título: "proteger ASP.NET Core una Blazor aplicación independiente Webassembly con Azure Active Directory ' Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="48e14-101">title: 'Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="48e14-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="48e14-102">'Blazor'</span></span>
- <span data-ttu-id="48e14-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="48e14-103">'Identity'</span></span>
- <span data-ttu-id="48e14-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="48e14-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="48e14-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="48e14-105">'Razor'</span></span>
- <span data-ttu-id="48e14-106">SignalRUID ' ':</span><span class="sxs-lookup"><span data-stu-id="48e14-106">'SignalR' uid:</span></span> 

---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-azure-active-directory"></a><span data-ttu-id="48e14-107">Protección de una Blazor aplicación independiente ASP.net Core Webassembly con Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="48e14-107">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory</span></span>

<span data-ttu-id="48e14-108">Por [Javier Calvarro Nelson](https://github.com/javiercn) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="48e14-108">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="48e14-109">Para crear una Blazor aplicación independiente Webassembly que use [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) para la autenticación:</span><span class="sxs-lookup"><span data-stu-id="48e14-109">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication:</span></span>

<span data-ttu-id="48e14-110">[Cree un inquilino de AAD y una aplicación web](/azure/active-directory/develop/v2-overview):</span><span class="sxs-lookup"><span data-stu-id="48e14-110">[Create an AAD tenant and web application](/azure/active-directory/develop/v2-overview):</span></span>

<span data-ttu-id="48e14-111">Registre una aplicación de AAD en **Azure Active Directory**el  >  área de**registros de aplicaciones** de Azure Active Directory del Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="48e14-111">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="48e14-112">Proporcione un **nombre** para la aplicación (por ejemplo, \*\* Blazor AAD independiente\*\*).</span><span class="sxs-lookup"><span data-stu-id="48e14-112">Provide a **Name** for the app (for example, **Blazor Standalone AAD**).</span></span>
1. <span data-ttu-id="48e14-113">Elija un **tipo de cuenta compatible**.</span><span class="sxs-lookup"><span data-stu-id="48e14-113">Choose a **Supported account types**.</span></span> <span data-ttu-id="48e14-114">Puede seleccionar **cuentas en este directorio de la organización solo** para esta experiencia.</span><span class="sxs-lookup"><span data-stu-id="48e14-114">You may select **Accounts in this organizational directory only** for this experience.</span></span>
1. <span data-ttu-id="48e14-115">Deje la lista desplegable **URI de redirección** establecida en **Web**y proporcione el siguiente URI de redirección: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="48e14-115">Leave the **Redirect URI** drop down set to **Web**, and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="48e14-116">El puerto predeterminado para una aplicación que se ejecuta en Kestrel es 5001.</span><span class="sxs-lookup"><span data-stu-id="48e14-116">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="48e14-117">Por IIS Express, el puerto generado aleatoriamente se puede encontrar en las propiedades de la aplicación en el panel **depurar** .</span><span class="sxs-lookup"><span data-stu-id="48e14-117">For IIS Express, the randomly generated port can be found in the app's properties in the **Debug** panel.</span></span>
1. <span data-ttu-id="48e14-118">Deshabilite **Permissions**la  >  casilla**conceder permisos Grant admin to OpenID y offline_access permisos** .</span><span class="sxs-lookup"><span data-stu-id="48e14-118">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="48e14-119">Seleccione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="48e14-119">Select **Register**.</span></span>

<span data-ttu-id="48e14-120">Registre la siguiente información:</span><span class="sxs-lookup"><span data-stu-id="48e14-120">Record the following information:</span></span>

* <span data-ttu-id="48e14-121">IDENTIFICADOR de aplicación (ID. de cliente) (por ejemplo, `11111111-1111-1111-1111-111111111111` )</span><span class="sxs-lookup"><span data-stu-id="48e14-121">Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="48e14-122">IDENTIFICADOR de directorio (identificador de inquilino) (por ejemplo, `22222222-2222-2222-2222-222222222222` )</span><span class="sxs-lookup"><span data-stu-id="48e14-122">Directory ID (Tenant ID) (for example, `22222222-2222-2222-2222-222222222222`)</span></span>

<span data-ttu-id="48e14-123">En configuración de la plataforma de **autenticación**  >  **Platform configurations**  >  **Web**:</span><span class="sxs-lookup"><span data-stu-id="48e14-123">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="48e14-124">Confirme que el **URI de redirección** de `https://localhost:{PORT}/authentication/login-callback` está presente.</span><span class="sxs-lookup"><span data-stu-id="48e14-124">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="48e14-125">En **concesión implícita**, active las casillas de verificación de **tokens de acceso** y **tokens de identificador**.</span><span class="sxs-lookup"><span data-stu-id="48e14-125">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="48e14-126">Los valores predeterminados restantes de la aplicación son aceptables para esta experiencia.</span><span class="sxs-lookup"><span data-stu-id="48e14-126">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="48e14-127">Seleccione el botón **Guardar**.</span><span class="sxs-lookup"><span data-stu-id="48e14-127">Select the **Save** button.</span></span>

<span data-ttu-id="48e14-128">Crear la aplicación.</span><span class="sxs-lookup"><span data-stu-id="48e14-128">Create the app.</span></span> <span data-ttu-id="48e14-129">Reemplace los marcadores de posición en el siguiente comando por la información registrada anteriormente y ejecute el comando en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="48e14-129">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="48e14-130">Para especificar la ubicación de salida, que crea una carpeta de proyecto si no existe, incluya la opción output en el comando con una ruta de acceso (por ejemplo, `-o BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="48e14-130">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="48e14-131">El nombre de la carpeta también se convierte en parte del nombre del proyecto.</span><span class="sxs-lookup"><span data-stu-id="48e14-131">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="48e14-132">Después de crear la aplicación, debe poder:</span><span class="sxs-lookup"><span data-stu-id="48e14-132">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="48e14-133">Inicie sesión en la aplicación con una cuenta de usuario de AAD.</span><span class="sxs-lookup"><span data-stu-id="48e14-133">Log into the app using an AAD user account.</span></span>
* <span data-ttu-id="48e14-134">Solicitar tokens de acceso para las API de Microsoft.</span><span class="sxs-lookup"><span data-stu-id="48e14-134">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="48e14-135">Para más información, consulte:</span><span class="sxs-lookup"><span data-stu-id="48e14-135">For more information, see:</span></span>
  * [<span data-ttu-id="48e14-136">Ámbitos de token de acceso</span><span class="sxs-lookup"><span data-stu-id="48e14-136">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="48e14-137">[Inicio rápido: configurar una aplicación para exponer las API Web](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span><span class="sxs-lookup"><span data-stu-id="48e14-137">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="48e14-138">Paquete de autenticación</span><span class="sxs-lookup"><span data-stu-id="48e14-138">Authentication package</span></span>

<span data-ttu-id="48e14-139">Cuando se crea una aplicación para usar cuentas profesionales o educativas ( `SingleOrg` ), la aplicación recibe automáticamente una referencia de paquete para la [biblioteca de autenticación de Microsoft](/azure/active-directory/develop/msal-overview) ( `Microsoft.Authentication.WebAssembly.Msal` ).</span><span class="sxs-lookup"><span data-stu-id="48e14-139">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="48e14-140">El paquete proporciona un conjunto de primitivas que ayudan a la aplicación a autenticar a los usuarios y obtener tokens para llamar a las API protegidas.</span><span class="sxs-lookup"><span data-stu-id="48e14-140">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="48e14-141">Si agrega la autenticación a una aplicación, agregue manualmente el paquete al archivo de proyecto de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="48e14-141">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="48e14-142">El `Microsoft.Authentication.WebAssembly.Msal` paquete agrega de manera transitiva el `Microsoft.AspNetCore.Components.WebAssembly.Authentication` paquete a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="48e14-142">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="48e14-143">Compatibilidad con el servicio de autenticación</span><span class="sxs-lookup"><span data-stu-id="48e14-143">Authentication service support</span></span>

<span data-ttu-id="48e14-144">La compatibilidad con la autenticación de usuarios se registra en el contenedor de servicios con el `AddMsalAuthentication` método de extensión proporcionado por el `Microsoft.Authentication.WebAssembly.Msal` paquete.</span><span class="sxs-lookup"><span data-stu-id="48e14-144">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="48e14-145">Este método configura los servicios necesarios para que la aplicación interactúe con el Identity proveedor (IP).</span><span class="sxs-lookup"><span data-stu-id="48e14-145">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="48e14-146">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="48e14-146">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="48e14-147">El `AddMsalAuthentication` método acepta una devolución de llamada para configurar los parámetros necesarios para autenticar una aplicación.</span><span class="sxs-lookup"><span data-stu-id="48e14-147">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="48e14-148">Los valores necesarios para configurar la aplicación se pueden obtener a partir de la configuración de AAD al registrar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="48e14-148">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="48e14-149">La configuración se proporciona mediante el archivo *wwwroot/appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="48e14-149">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="48e14-150">Ejemplo:</span><span class="sxs-lookup"><span data-stu-id="48e14-150">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="48e14-151">Ámbitos de token de acceso</span><span class="sxs-lookup"><span data-stu-id="48e14-151">Access token scopes</span></span>

<span data-ttu-id="48e14-152">La Blazor plantilla Webassembly no configura automáticamente la aplicación para solicitar un token de acceso para una API segura.</span><span class="sxs-lookup"><span data-stu-id="48e14-152">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="48e14-153">Para aprovisionar un token de acceso como parte del flujo de inicio de sesión, agregue el ámbito a los ámbitos de token de acceso predeterminados de `MsalProviderOptions` :</span><span class="sxs-lookup"><span data-stu-id="48e14-153">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="48e14-154">Para obtener más información, consulte las siguientes secciones del artículo *escenarios adicionales* :</span><span class="sxs-lookup"><span data-stu-id="48e14-154">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="48e14-155">Solicitar tokens de acceso adicionales</span><span class="sxs-lookup"><span data-stu-id="48e14-155">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="48e14-156">Adjuntar tokens a solicitudes salientes</span><span class="sxs-lookup"><span data-stu-id="48e14-156">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="48e14-157">Importar archivo</span><span class="sxs-lookup"><span data-stu-id="48e14-157">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="48e14-158">Página de índice</span><span class="sxs-lookup"><span data-stu-id="48e14-158">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="48e14-159">Componente de aplicación</span><span class="sxs-lookup"><span data-stu-id="48e14-159">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="48e14-160">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="48e14-160">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="48e14-161">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="48e14-161">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="48e14-162">Componente de autenticación</span><span class="sxs-lookup"><span data-stu-id="48e14-162">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="48e14-163">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="48e14-163">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="48e14-164">Solicitudes de API Web no autenticadas o no autorizadas en una aplicación con un cliente predeterminado seguro</span><span class="sxs-lookup"><span data-stu-id="48e14-164">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/blazor/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="48e14-165">Documentación de la plataforma de identidad de Microsoft</span><span class="sxs-lookup"><span data-stu-id="48e14-165">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
