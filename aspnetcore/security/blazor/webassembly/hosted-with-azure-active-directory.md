---
<span data-ttu-id="195e3-101">Título: "proteger una Blazor aplicación hospedada de Webassemble de ASP.net Core con Azure Active Directory ' Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="195e3-101">title: 'Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="195e3-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="195e3-102">'Blazor'</span></span>
- <span data-ttu-id="195e3-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="195e3-103">'Identity'</span></span>
- <span data-ttu-id="195e3-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="195e3-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="195e3-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="195e3-105">'Razor'</span></span>
- <span data-ttu-id="195e3-106">SignalRUID ' ':</span><span class="sxs-lookup"><span data-stu-id="195e3-106">'SignalR' uid:</span></span> 

---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-azure-active-directory"></a><span data-ttu-id="195e3-107">Protección de una Blazor aplicación hospedada en Webassembly ASP.net Core con Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="195e3-107">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory</span></span>

<span data-ttu-id="195e3-108">Por [Javier Calvarro Nelson](https://github.com/javiercn) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="195e3-108">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="195e3-109">En este artículo se describe cómo crear una [ Blazor aplicación hospedada en webassembly](xref:blazor/hosting-models#blazor-webassembly) que usa [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) para la autenticación.</span><span class="sxs-lookup"><span data-stu-id="195e3-109">This article describes how to create a [Blazor WebAssembly hosted app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

## <a name="register-apps-in-aad-and-create-solution"></a><span data-ttu-id="195e3-110">Registro de aplicaciones en AAD y creación de una solución</span><span class="sxs-lookup"><span data-stu-id="195e3-110">Register apps in AAD and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="195e3-111">Creación de un inquilino</span><span class="sxs-lookup"><span data-stu-id="195e3-111">Create a tenant</span></span>

<span data-ttu-id="195e3-112">Siga las instrucciones de [Inicio rápido: configuración de un inquilino](/azure/active-directory/develop/quickstart-create-new-tenant) para crear un inquilino en AAD.</span><span class="sxs-lookup"><span data-stu-id="195e3-112">Follow the guidance in [Quickstart: Set up a tenant](/azure/active-directory/develop/quickstart-create-new-tenant) to create a tenant in AAD.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="195e3-113">Registrar una aplicación de API de servidor</span><span class="sxs-lookup"><span data-stu-id="195e3-113">Register a server API app</span></span>

<span data-ttu-id="195e3-114">Siga las instrucciones de [Inicio rápido: registro de una aplicación con la plataforma de Microsoft Identity y los](/azure/active-directory/develop/quickstart-register-app) temas de Azure AAD subsiguientes para registrar una aplicación de AAD para la *aplicación de API de servidor*:</span><span class="sxs-lookup"><span data-stu-id="195e3-114">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register an AAD app for the *Server API app*:</span></span>

1. <span data-ttu-id="195e3-115">En **Azure Active Directory**  >  **registros de aplicaciones**, seleccione **nuevo registro**.</span><span class="sxs-lookup"><span data-stu-id="195e3-115">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="195e3-116">Proporcione un **nombre** para la aplicación (por ejemplo, \*\* Blazor servidor AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="195e3-116">Provide a **Name** for the app (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="195e3-117">Elija un **tipo de cuenta compatible**.</span><span class="sxs-lookup"><span data-stu-id="195e3-117">Choose a **Supported account types**.</span></span> <span data-ttu-id="195e3-118">Solo puede seleccionar **cuentas en este directorio de la organización** (un solo inquilino) para esta experiencia.</span><span class="sxs-lookup"><span data-stu-id="195e3-118">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="195e3-119">La *aplicación de API de servidor* no requiere un **URI de redirección** en este escenario, por lo que deje la lista desplegable establecida en **Web** y no escriba un URI de redirección.</span><span class="sxs-lookup"><span data-stu-id="195e3-119">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="195e3-120">Deshabilite **Permissions**la  >  casilla**conceder permisos Grant admin to OpenID y offline_access permisos** .</span><span class="sxs-lookup"><span data-stu-id="195e3-120">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="195e3-121">Seleccione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="195e3-121">Select **Register**.</span></span>

<span data-ttu-id="195e3-122">Registre la siguiente información:</span><span class="sxs-lookup"><span data-stu-id="195e3-122">Record the following information:</span></span>

* <span data-ttu-id="195e3-123">*Aplicación de API de servidor* IDENTIFICADOR de aplicación (ID. de cliente) (por ejemplo, `11111111-1111-1111-1111-111111111111` )</span><span class="sxs-lookup"><span data-stu-id="195e3-123">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="195e3-124">IDENTIFICADOR de directorio (identificador de inquilino) (por ejemplo, `222222222-2222-2222-2222-222222222222` )</span><span class="sxs-lookup"><span data-stu-id="195e3-124">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="195e3-125">Dominio del inquilino de AAD (por ejemplo, `contoso.onmicrosoft.com` ) &ndash; el dominio está disponible como el **dominio del publicador** en la hoja de **personalización de marca** del Azure portal de la aplicación registrada.</span><span class="sxs-lookup"><span data-stu-id="195e3-125">AAD Tenant domain (for example, `contoso.onmicrosoft.com`) &ndash; The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="195e3-126">En **permisos**de la API, quite el **Microsoft Graph**  >  **usuario.** permiso de lectura, ya que la aplicación no requiere el inicio de sesión o el acceso de Perfil de usuario.</span><span class="sxs-lookup"><span data-stu-id="195e3-126">In **API permissions**, remove the **Microsoft Graph** > **User.Read** permission, as the app doesn't require sign in or user profile access.</span></span>

<span data-ttu-id="195e3-127">En **exponer una API**:</span><span class="sxs-lookup"><span data-stu-id="195e3-127">In **Expose an API**:</span></span>

1. <span data-ttu-id="195e3-128">Seleccione **Agregar un ámbito**.</span><span class="sxs-lookup"><span data-stu-id="195e3-128">Select **Add a scope**.</span></span>
1. <span data-ttu-id="195e3-129">Selecciona **Guardar y continuar**.</span><span class="sxs-lookup"><span data-stu-id="195e3-129">Select **Save and continue**.</span></span>
1. <span data-ttu-id="195e3-130">Proporcione un **nombre de ámbito** (por ejemplo, `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="195e3-130">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="195e3-131">Proporcione un **nombre para mostrar del consentimiento del administrador** (por ejemplo, `Access API` ).</span><span class="sxs-lookup"><span data-stu-id="195e3-131">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="195e3-132">Proporcione una **Descripción del consentimiento del administrador** (por ejemplo, `Allows the app to access server app API endpoints.` ).</span><span class="sxs-lookup"><span data-stu-id="195e3-132">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="195e3-133">Confirme que el **Estado** está establecido en **habilitado**.</span><span class="sxs-lookup"><span data-stu-id="195e3-133">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="195e3-134">Seleccione la opción **Agregar un ámbito**.</span><span class="sxs-lookup"><span data-stu-id="195e3-134">Select **Add scope**.</span></span>

<span data-ttu-id="195e3-135">Registre la siguiente información:</span><span class="sxs-lookup"><span data-stu-id="195e3-135">Record the following information:</span></span>

* <span data-ttu-id="195e3-136">URI de ID. de aplicación (por ejemplo, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` , `api://11111111-1111-1111-1111-111111111111` o el valor personalizado que proporcionó)</span><span class="sxs-lookup"><span data-stu-id="195e3-136">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="195e3-137">Ámbito predeterminado (por ejemplo, `API.Access` )</span><span class="sxs-lookup"><span data-stu-id="195e3-137">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="195e3-138">Registrar una aplicación de cliente</span><span class="sxs-lookup"><span data-stu-id="195e3-138">Register a client app</span></span>

<span data-ttu-id="195e3-139">Siga las instrucciones de [Inicio rápido: registro de una aplicación con la plataforma de Microsoft Identity y los](/azure/active-directory/develop/quickstart-register-app) temas de Azure AAD subsiguientes para registrar una aplicación de AAD para la *aplicación cliente*:</span><span class="sxs-lookup"><span data-stu-id="195e3-139">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register a AAD app for the *Client app*:</span></span>

1. <span data-ttu-id="195e3-140">En **Azure Active Directory**  >  **registros de aplicaciones**, seleccione **nuevo registro**.</span><span class="sxs-lookup"><span data-stu-id="195e3-140">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="195e3-141">Proporcione un **nombre** para la aplicación (por ejemplo, \*\* Blazor cliente AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="195e3-141">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="195e3-142">Elija un **tipo de cuenta compatible**.</span><span class="sxs-lookup"><span data-stu-id="195e3-142">Choose a **Supported account types**.</span></span> <span data-ttu-id="195e3-143">Solo puede seleccionar **cuentas en este directorio de la organización** (un solo inquilino) para esta experiencia.</span><span class="sxs-lookup"><span data-stu-id="195e3-143">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="195e3-144">Deje la lista desplegable **URI de redirección** establecida en **Web**y proporcione el siguiente URI de redirección: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="195e3-144">Leave the **Redirect URI** drop down set to **Web**, and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="195e3-145">El puerto predeterminado para una aplicación que se ejecuta en Kestrel es 5001.</span><span class="sxs-lookup"><span data-stu-id="195e3-145">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="195e3-146">Por IIS Express, el puerto generado aleatoriamente se puede encontrar en las propiedades de la aplicación de servidor en el panel de **depuración** .</span><span class="sxs-lookup"><span data-stu-id="195e3-146">For IIS Express, the randomly generated port can be found in the Server app's properties in the **Debug** panel.</span></span>
1. <span data-ttu-id="195e3-147">Deshabilite **Permissions**la  >  casilla**conceder permisos Grant admin to OpenID y offline_access permisos** .</span><span class="sxs-lookup"><span data-stu-id="195e3-147">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="195e3-148">Seleccione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="195e3-148">Select **Register**.</span></span>

<span data-ttu-id="195e3-149">Registre el identificador de aplicación de la aplicación *cliente* (identificador de cliente) (por ejemplo, `33333333-3333-3333-3333-333333333333` ).</span><span class="sxs-lookup"><span data-stu-id="195e3-149">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>

<span data-ttu-id="195e3-150">En configuración de la plataforma de **autenticación**  >  **Platform configurations**  >  **Web**:</span><span class="sxs-lookup"><span data-stu-id="195e3-150">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="195e3-151">Confirme que el **URI de redirección** de `https://localhost:{PORT}/authentication/login-callback` está presente.</span><span class="sxs-lookup"><span data-stu-id="195e3-151">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="195e3-152">En **concesión implícita**, active las casillas de verificación de **tokens de acceso** y **tokens de identificador**.</span><span class="sxs-lookup"><span data-stu-id="195e3-152">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="195e3-153">Los valores predeterminados restantes de la aplicación son aceptables para esta experiencia.</span><span class="sxs-lookup"><span data-stu-id="195e3-153">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="195e3-154">Seleccione el botón **Guardar**.</span><span class="sxs-lookup"><span data-stu-id="195e3-154">Select the **Save** button.</span></span>

<span data-ttu-id="195e3-155">En **permisos de API**:</span><span class="sxs-lookup"><span data-stu-id="195e3-155">In **API permissions**:</span></span>

1. <span data-ttu-id="195e3-156">Confirme que la aplicación tiene **Microsoft Graph**  >  permiso**User. Read** .</span><span class="sxs-lookup"><span data-stu-id="195e3-156">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="195e3-157">Seleccione **Agregar un permiso** seguido de **mis API**.</span><span class="sxs-lookup"><span data-stu-id="195e3-157">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="195e3-158">Seleccione la *aplicación de API de servidor* en la columna **nombre** (por ejemplo, \*\* Blazor servidor AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="195e3-158">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="195e3-159">Abra la lista de **API** .</span><span class="sxs-lookup"><span data-stu-id="195e3-159">Open the **API** list.</span></span>
1. <span data-ttu-id="195e3-160">Habilite el acceso a la API (por ejemplo, `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="195e3-160">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="195e3-161">Seleccione **Agregar permisos**.</span><span class="sxs-lookup"><span data-stu-id="195e3-161">Select **Add permissions**.</span></span>
1. <span data-ttu-id="195e3-162">Seleccione el botón **conceder contenido de administración para {nombre de inquilino}** .</span><span class="sxs-lookup"><span data-stu-id="195e3-162">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="195e3-163">Seleccione **Sí** para confirmar.</span><span class="sxs-lookup"><span data-stu-id="195e3-163">Select **Yes** to confirm.</span></span>

### <a name="create-the-app"></a><span data-ttu-id="195e3-164">Creación de la aplicación</span><span class="sxs-lookup"><span data-stu-id="195e3-164">Create the app</span></span>

<span data-ttu-id="195e3-165">Reemplace los marcadores de posición en el siguiente comando por la información registrada anteriormente y ejecute el comando en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="195e3-165">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho --tenant-id "{TENANT ID}"
```

<span data-ttu-id="195e3-166">Para especificar la ubicación de salida, que crea una carpeta de proyecto si no existe, incluya la opción output en el comando con una ruta de acceso (por ejemplo, `-o BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="195e3-166">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="195e3-167">El nombre de la carpeta también se convierte en parte del nombre del proyecto.</span><span class="sxs-lookup"><span data-stu-id="195e3-167">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="195e3-168">Pase el URI de ID. de aplicación a la `app-id-uri` opción, pero tenga en cuenta que es posible que se requiera un cambio de configuración en la aplicación cliente, que se describe en la sección [ámbitos de token de acceso](#access-token-scopes) .</span><span class="sxs-lookup"><span data-stu-id="195e3-168">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="195e3-169">Configuración de la aplicación de servidor</span><span class="sxs-lookup"><span data-stu-id="195e3-169">Server app configuration</span></span>

<span data-ttu-id="195e3-170">*Esta sección pertenece a la aplicación de **servidor** de la solución.*</span><span class="sxs-lookup"><span data-stu-id="195e3-170">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="195e3-171">Paquete de autenticación</span><span class="sxs-lookup"><span data-stu-id="195e3-171">Authentication package</span></span>

<span data-ttu-id="195e3-172">La compatibilidad para autenticar y autorizar llamadas a ASP.NET Core API Web la proporciona `Microsoft.AspNetCore.Authentication.AzureAD.UI` :</span><span class="sxs-lookup"><span data-stu-id="195e3-172">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the `Microsoft.AspNetCore.Authentication.AzureAD.UI`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
  Version="3.2.0" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="195e3-173">Compatibilidad con el servicio de autenticación</span><span class="sxs-lookup"><span data-stu-id="195e3-173">Authentication service support</span></span>

<span data-ttu-id="195e3-174">El `AddAuthentication` método configura los servicios de autenticación dentro de la aplicación y configura el controlador de portador JWT como el método de autenticación predeterminado.</span><span class="sxs-lookup"><span data-stu-id="195e3-174">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="195e3-175">El `AddAzureADBearer` método configura los parámetros específicos en el controlador de portador JWT necesario para validar los tokens emitidos por el Azure Active Directory:</span><span class="sxs-lookup"><span data-stu-id="195e3-175">The `AddAzureADBearer` method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

<span data-ttu-id="195e3-176">`UseAuthentication`y `UseAuthorization` Asegúrese de que:</span><span class="sxs-lookup"><span data-stu-id="195e3-176">`UseAuthentication` and `UseAuthorization` ensure that:</span></span>

* <span data-ttu-id="195e3-177">La aplicación intenta analizar y validar los tokens en las solicitudes entrantes.</span><span class="sxs-lookup"><span data-stu-id="195e3-177">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="195e3-178">Se produce un error en cualquier solicitud que intente obtener acceso a un recurso protegido sin credenciales adecuadas.</span><span class="sxs-lookup"><span data-stu-id="195e3-178">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="195e3-179">Usuario. Identity . Name</span><span class="sxs-lookup"><span data-stu-id="195e3-179">User.Identity.Name</span></span>

<span data-ttu-id="195e3-180">De forma predeterminada, la API de la aplicación de servidor se rellena `User.Identity.Name` con el valor del `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` tipo de notificaciones (por ejemplo, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com` ).</span><span class="sxs-lookup"><span data-stu-id="195e3-180">By default, the Server app API populates `User.Identity.Name` with the value from the `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` claim type (for example, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="195e3-181">Para configurar la aplicación para recibir el valor del `name` tipo de notificaciones, configure el [TokenValidationParameters. NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) de <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> en `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="195e3-181">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="195e3-182">Configuración de aplicaciones</span><span class="sxs-lookup"><span data-stu-id="195e3-182">App settings</span></span>

<span data-ttu-id="195e3-183">El archivo *appSettings. JSON* contiene las opciones para configurar el controlador de portador JWT que se usa para validar los tokens de acceso:</span><span class="sxs-lookup"><span data-stu-id="195e3-183">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens:</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{SERVER API APP CLIENT ID}",
  }
}
```

<span data-ttu-id="195e3-184">Ejemplo:</span><span class="sxs-lookup"><span data-stu-id="195e3-184">Example:</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "contoso.onmicrosoft.com",
    "TenantId": "e86c78e2-8bb4-4c41-aefd-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
  }
}
```

### <a name="weatherforecast-controller"></a><span data-ttu-id="195e3-185">Controlador de WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="195e3-185">WeatherForecast controller</span></span>

<span data-ttu-id="195e3-186">El controlador WeatherForecast (*Controllers/WeatherForecastController. CS*) expone una API protegida con el `[Authorize]` atributo que se aplica al controlador.</span><span class="sxs-lookup"><span data-stu-id="195e3-186">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the `[Authorize]` attribute applied to the controller.</span></span> <span data-ttu-id="195e3-187">Es **importante** comprender esto:</span><span class="sxs-lookup"><span data-stu-id="195e3-187">It's **important** to understand that:</span></span>

* <span data-ttu-id="195e3-188">El `[Authorize]` atributo de este controlador de API es lo único que protege esta API frente al acceso no autorizado.</span><span class="sxs-lookup"><span data-stu-id="195e3-188">The `[Authorize]` attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="195e3-189">El `[Authorize]` atributo que se usa en la Blazor aplicación webassembly solo sirve como una sugerencia a la aplicación a la que el usuario debe estar autorizado para que la aplicación funcione correctamente.</span><span class="sxs-lookup"><span data-stu-id="195e3-189">The `[Authorize]` attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

```csharp
[Authorize]
[ApiController]
[Route("[controller]")]
public class WeatherForecastController : ControllerBase
{
    [HttpGet]
    public IEnumerable<WeatherForecast> Get()
    {
        ...
    }
}
```

## <a name="client-app-configuration"></a><span data-ttu-id="195e3-190">Configuración de la aplicación cliente</span><span class="sxs-lookup"><span data-stu-id="195e3-190">Client app configuration</span></span>

<span data-ttu-id="195e3-191">*Esta sección pertenece a la aplicación **cliente** de la solución.*</span><span class="sxs-lookup"><span data-stu-id="195e3-191">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="195e3-192">Paquete de autenticación</span><span class="sxs-lookup"><span data-stu-id="195e3-192">Authentication package</span></span>

<span data-ttu-id="195e3-193">Cuando se crea una aplicación para usar cuentas profesionales o educativas ( `SingleOrg` ), la aplicación recibe automáticamente una referencia de paquete para la [biblioteca de autenticación de Microsoft](/azure/active-directory/develop/msal-overview) ( `Microsoft.Authentication.WebAssembly.Msal` ).</span><span class="sxs-lookup"><span data-stu-id="195e3-193">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="195e3-194">El paquete proporciona un conjunto de primitivas que ayudan a la aplicación a autenticar a los usuarios y obtener tokens para llamar a las API protegidas.</span><span class="sxs-lookup"><span data-stu-id="195e3-194">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="195e3-195">Si agrega la autenticación a una aplicación, agregue manualmente el paquete al archivo de proyecto de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="195e3-195">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="195e3-196">El `Microsoft.Authentication.WebAssembly.Msal` paquete agrega de manera transitiva el `Microsoft.AspNetCore.Components.WebAssembly.Authentication` paquete a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="195e3-196">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="195e3-197">Compatibilidad con el servicio de autenticación</span><span class="sxs-lookup"><span data-stu-id="195e3-197">Authentication service support</span></span>

<span data-ttu-id="195e3-198">`HttpClient`Se ha agregado compatibilidad con las instancias de que incluyen tokens de acceso al hacer solicitudes al proyecto de servidor.</span><span class="sxs-lookup"><span data-stu-id="195e3-198">Support for `HttpClient` instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="195e3-199">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="195e3-199">*Program.cs*:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="195e3-200">La compatibilidad con la autenticación de usuarios se registra en el contenedor de servicios con el `AddMsalAuthentication` método de extensión proporcionado por el `Microsoft.Authentication.WebAssembly.Msal` paquete.</span><span class="sxs-lookup"><span data-stu-id="195e3-200">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="195e3-201">Este método configura los servicios necesarios para que la aplicación interactúe con el Identity proveedor (IP).</span><span class="sxs-lookup"><span data-stu-id="195e3-201">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="195e3-202">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="195e3-202">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="195e3-203">El `AddMsalAuthentication` método acepta una devolución de llamada para configurar los parámetros necesarios para autenticar una aplicación.</span><span class="sxs-lookup"><span data-stu-id="195e3-203">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="195e3-204">Los valores necesarios para configurar la aplicación pueden obtenerse a partir de la configuración de AAD de Azure portal cuando se registra la aplicación.</span><span class="sxs-lookup"><span data-stu-id="195e3-204">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="195e3-205">La configuración se proporciona mediante el archivo *wwwroot/appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="195e3-205">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="195e3-206">Ejemplo:</span><span class="sxs-lookup"><span data-stu-id="195e3-206">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": true
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="195e3-207">Ámbitos de token de acceso</span><span class="sxs-lookup"><span data-stu-id="195e3-207">Access token scopes</span></span>

<span data-ttu-id="195e3-208">Los ámbitos de token de acceso predeterminados representan la lista de ámbitos de token de acceso que son:</span><span class="sxs-lookup"><span data-stu-id="195e3-208">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="195e3-209">Se incluye de forma predeterminada en la solicitud de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="195e3-209">Included by default in the sign in request.</span></span>
* <span data-ttu-id="195e3-210">Se usa para aprovisionar un token de acceso inmediatamente después de la autenticación.</span><span class="sxs-lookup"><span data-stu-id="195e3-210">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="195e3-211">Todos los ámbitos deben pertenecer a la misma aplicación por reglas de Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="195e3-211">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="195e3-212">Se pueden agregar ámbitos adicionales para las aplicaciones de API adicionales según sea necesario:</span><span class="sxs-lookup"><span data-stu-id="195e3-212">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="195e3-213">Para obtener más información, consulte las siguientes secciones del artículo *escenarios adicionales* :</span><span class="sxs-lookup"><span data-stu-id="195e3-213">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="195e3-214">Solicitar tokens de acceso adicionales</span><span class="sxs-lookup"><span data-stu-id="195e3-214">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="195e3-215">Adjuntar tokens a solicitudes salientes</span><span class="sxs-lookup"><span data-stu-id="195e3-215">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)


### <a name="imports-file"></a><span data-ttu-id="195e3-216">Importar archivo</span><span class="sxs-lookup"><span data-stu-id="195e3-216">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="195e3-217">Página de índice</span><span class="sxs-lookup"><span data-stu-id="195e3-217">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="195e3-218">Componente de aplicación</span><span class="sxs-lookup"><span data-stu-id="195e3-218">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="195e3-219">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="195e3-219">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="195e3-220">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="195e3-220">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="195e3-221">Componente de autenticación</span><span class="sxs-lookup"><span data-stu-id="195e3-221">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="195e3-222">Componente FetchData</span><span class="sxs-lookup"><span data-stu-id="195e3-222">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="195e3-223">Ejecución la aplicación</span><span class="sxs-lookup"><span data-stu-id="195e3-223">Run the app</span></span>

<span data-ttu-id="195e3-224">Ejecute la aplicación desde el proyecto de servidor.</span><span class="sxs-lookup"><span data-stu-id="195e3-224">Run the app from the Server project.</span></span> <span data-ttu-id="195e3-225">Al usar Visual Studio, puede:</span><span class="sxs-lookup"><span data-stu-id="195e3-225">When using Visual Studio, either:</span></span>

* <span data-ttu-id="195e3-226">Establezca la lista desplegable **proyectos de inicio** en la barra de herramientas en la aplicación de *API de servidor* y seleccione el botón **Ejecutar** .</span><span class="sxs-lookup"><span data-stu-id="195e3-226">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="195e3-227">Seleccione el proyecto de servidor en **Explorador de soluciones** y seleccione el botón **Ejecutar** en la barra de herramientas o inicie la aplicación en el menú **depurar** .</span><span class="sxs-lookup"><span data-stu-id="195e3-227">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="195e3-228">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="195e3-228">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="195e3-229">Solicitudes de API Web no autenticadas o no autorizadas en una aplicación con un cliente predeterminado seguro</span><span class="sxs-lookup"><span data-stu-id="195e3-229">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/blazor/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="195e3-230">Documentación de la plataforma de identidad de Microsoft</span><span class="sxs-lookup"><span data-stu-id="195e3-230">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
