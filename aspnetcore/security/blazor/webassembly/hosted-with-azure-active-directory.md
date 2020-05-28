---
Título: "proteger una Blazor aplicación hospedada de Webassemble de ASP.net Core con Azure Active Directory ' Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: no-LOC:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-azure-active-directory"></a>Protección de una Blazor aplicación hospedada en Webassembly ASP.net Core con Azure Active Directory

Por [Javier Calvarro Nelson](https://github.com/javiercn) y [Luke Latham](https://github.com/guardrex)

En este artículo se describe cómo crear una [ Blazor aplicación hospedada en webassembly](xref:blazor/hosting-models#blazor-webassembly) que usa [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) para la autenticación.

## <a name="register-apps-in-aad-and-create-solution"></a>Registro de aplicaciones en AAD y creación de una solución

### <a name="create-a-tenant"></a>Creación de un inquilino

Siga las instrucciones de [Inicio rápido: configuración de un inquilino](/azure/active-directory/develop/quickstart-create-new-tenant) para crear un inquilino en AAD.

### <a name="register-a-server-api-app"></a>Registrar una aplicación de API de servidor

Siga las instrucciones de [Inicio rápido: registro de una aplicación con la plataforma de Microsoft Identity y los](/azure/active-directory/develop/quickstart-register-app) temas de Azure AAD subsiguientes para registrar una aplicación de AAD para la *aplicación de API de servidor*:

1. En **Azure Active Directory**  >  **registros de aplicaciones**, seleccione **nuevo registro**.
1. Proporcione un **nombre** para la aplicación (por ejemplo, ** Blazor servidor AAD**).
1. Elija un **tipo de cuenta compatible**. Solo puede seleccionar **cuentas en este directorio de la organización** (un solo inquilino) para esta experiencia.
1. La *aplicación de API de servidor* no requiere un **URI de redirección** en este escenario, por lo que deje la lista desplegable establecida en **Web** y no escriba un URI de redirección.
1. Deshabilite **Permissions**la  >  casilla**conceder permisos Grant admin to OpenID y offline_access permisos** .
1. Seleccione **Registrar**.

Registre la siguiente información:

* *Aplicación de API de servidor* IDENTIFICADOR de aplicación (ID. de cliente) (por ejemplo, `11111111-1111-1111-1111-111111111111` )
* IDENTIFICADOR de directorio (identificador de inquilino) (por ejemplo, `222222222-2222-2222-2222-222222222222` )
* Dominio del inquilino de AAD (por ejemplo, `contoso.onmicrosoft.com` ): el dominio está disponible como el **dominio del publicador** en la hoja de **Personalización de marca** del Azure portal de la aplicación registrada.

En **permisos**de la API, quite el **Microsoft Graph**  >  **usuario.** permiso de lectura, ya que la aplicación no requiere el inicio de sesión o el acceso de Perfil de usuario.

En **exponer una API**:

1. Seleccione **Agregar un ámbito**.
1. Selecciona **Guardar y continuar**.
1. Proporcione un **nombre de ámbito** (por ejemplo, `API.Access` ).
1. Proporcione un **nombre para mostrar del consentimiento del administrador** (por ejemplo, `Access API` ).
1. Proporcione una **Descripción del consentimiento del administrador** (por ejemplo, `Allows the app to access server app API endpoints.` ).
1. Confirme que el **Estado** está establecido en **habilitado**.
1. Seleccione la opción **Agregar un ámbito**.

Registre la siguiente información:

* URI de ID. de aplicación (por ejemplo, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` , `api://11111111-1111-1111-1111-111111111111` o el valor personalizado que proporcionó)
* Ámbito predeterminado (por ejemplo, `API.Access` )

### <a name="register-a-client-app"></a>Registrar una aplicación de cliente

Siga las instrucciones de [Inicio rápido: registro de una aplicación con la plataforma de Microsoft Identity y los](/azure/active-directory/develop/quickstart-register-app) temas de Azure AAD subsiguientes para registrar una aplicación de AAD para la *aplicación cliente*:

1. En **Azure Active Directory**  >  **registros de aplicaciones**, seleccione **nuevo registro**.
1. Proporcione un **nombre** para la aplicación (por ejemplo, ** Blazor cliente AAD**).
1. Elija un **tipo de cuenta compatible**. Solo puede seleccionar **cuentas en este directorio de la organización** (un solo inquilino) para esta experiencia.
1. Deje la lista desplegable **URI de redirección** establecida en **Web** y proporcione el siguiente URI de redirección: `https://localhost:{PORT}/authentication/login-callback` . El puerto predeterminado para una aplicación que se ejecuta en Kestrel es 5001. Si la aplicación se ejecuta en otro puerto Kestrel, use el puerto de la aplicación. Por IIS Express, el puerto generado de forma aleatoria para la aplicación se puede encontrar en las propiedades de la aplicación de servidor en el panel de **depuración** . Dado que la aplicación no existe en este momento y no se conoce el puerto IIS Express, vuelva a este paso después de crear la aplicación y actualice el URI de redirección. Aparece un Comentario en la sección [creación de la aplicación](#create-the-app) para recordar IIS Express usuarios a actualizar el URI de redirección.
1. Deshabilite **Permissions**la  >  casilla**conceder permisos Grant admin to OpenID y offline_access permisos** .
1. Seleccione **Registrar**.

Registre el identificador de aplicación de la aplicación *cliente* (identificador de cliente) (por ejemplo, `33333333-3333-3333-3333-333333333333` ).

En configuración de la plataforma de **autenticación**  >  **Platform configurations**  >  **Web**:

1. Confirme que el **URI de redirección** de `https://localhost:{PORT}/authentication/login-callback` está presente.
1. En **concesión implícita**, active las casillas de verificación de **tokens de acceso** y **tokens de identificador**.
1. Los valores predeterminados restantes de la aplicación son aceptables para esta experiencia.
1. Seleccione el botón **Guardar**.

En **permisos de API**:

1. Confirme que la aplicación tiene **Microsoft Graph**  >  permiso**User. Read** .
1. Seleccione **Agregar un permiso** seguido de **mis API**.
1. Seleccione la *aplicación de API de servidor* en la columna **nombre** (por ejemplo, ** Blazor servidor AAD**).
1. Abra la lista de **API** .
1. Habilite el acceso a la API (por ejemplo, `API.Access` ).
1. Seleccione **Agregar permisos**.
1. Seleccione el botón **conceder contenido de administración para {nombre de inquilino}** . Seleccione **Sí** para confirmar.

### <a name="create-the-app"></a>Creación de la aplicación

Reemplace los marcadores de posición en el siguiente comando por la información registrada anteriormente y ejecute el comando en un shell de comandos:

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho --tenant-id "{TENANT ID}"
```

Para especificar la ubicación de salida, que crea una carpeta de proyecto si no existe, incluya la opción output en el comando con una ruta de acceso (por ejemplo, `-o BlazorSample` ). El nombre de la carpeta también se convierte en parte del nombre del proyecto.

> [!NOTE]
> Pase el URI de ID. de aplicación a la `app-id-uri` opción, pero tenga en cuenta que es posible que se requiera un cambio de configuración en la aplicación cliente, que se describe en la sección [ámbitos de token de acceso](#access-token-scopes) .

> [!NOTE]
> En el Azure portal, el URI de redireccionamiento web de configuración de la plataforma de autenticación de la *aplicación cliente* **Authentication**  >  **Platform configurations**  >  **Web**  >  **Redirect URI** se configura para el puerto 5001 para las aplicaciones que se ejecutan en el servidor Kestrel con la configuración predeterminada.
>
> Si la *aplicación cliente* se ejecuta en un puerto de IIS Express aleatorio, el puerto de la aplicación se puede encontrar en las propiedades de la *aplicación de servidor* en el panel **depurar** .
>
> Si el puerto no se configuró anteriormente con el puerto conocido *de la aplicación cliente* , vuelva al registro de la *aplicación cliente* en el Azure portal y actualice el URI de redirección con el puerto correcto.

## <a name="server-app-configuration"></a>Configuración de la aplicación de servidor

*Esta sección pertenece a la aplicación de **servidor** de la solución.*

### <a name="authentication-package"></a>Paquete de autenticación

El soporte para autenticar y autorizar llamadas a ASP.NET Core API Web se proporciona mediante el paquete [Microsoft. AspNetCore. Authentication. AzureAD. UI](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI/) :

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
  Version="3.1.4" />
```

### <a name="authentication-service-support"></a>Compatibilidad con el servicio de autenticación

El `AddAuthentication` método configura los servicios de autenticación dentro de la aplicación y configura el controlador de portador JWT como el método de autenticación predeterminado. El <xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A> método configura los parámetros específicos en el controlador de portador JWT necesario para validar los tokens emitidos por el Azure Active Directory:

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>y <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> Asegúrese de que:

* La aplicación intenta analizar y validar los tokens en las solicitudes entrantes.
* Se produce un error en cualquier solicitud que intente obtener acceso a un recurso protegido sin credenciales adecuadas.

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a>Usuario. Identity . Name

De forma predeterminada, la API de la aplicación de servidor se rellena `User.Identity.Name` con el valor del `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` tipo de notificaciones (por ejemplo, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com` ).

Para configurar la aplicación para recibir el valor del `name` tipo de notificaciones, configure el [TokenValidationParameters. NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) de <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> en `Startup.ConfigureServices` :

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a>Configuración de aplicaciones

El archivo *appSettings. JSON* contiene las opciones para configurar el controlador de portador JWT que se usa para validar los tokens de acceso:

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

Ejemplo:

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

### <a name="weatherforecast-controller"></a>Controlador de WeatherForecast

El controlador WeatherForecast (*Controllers/WeatherForecastController. CS*) expone una API protegida con el [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atributo que se aplica al controlador. Es **importante** comprender esto:

* El [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atributo de este controlador de API es lo único que protege esta API frente al acceso no autorizado.
* El [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atributo que se usa en la Blazor aplicación webassembly solo sirve como una sugerencia a la aplicación a la que el usuario debe estar autorizado para que la aplicación funcione correctamente.

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

## <a name="client-app-configuration"></a>Configuración de la aplicación cliente

*Esta sección pertenece a la aplicación **cliente** de la solución.*

### <a name="authentication-package"></a>Paquete de autenticación

Cuando se crea una aplicación para usar cuentas profesionales o educativas ( `SingleOrg` ), la aplicación recibe automáticamente una referencia de paquete para la [biblioteca de autenticación de Microsoft](/azure/active-directory/develop/msal-overview) ([Microsoft. Authentication. webassembly. msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)). El paquete proporciona un conjunto de primitivas que ayudan a la aplicación a autenticar a los usuarios y obtener tokens para llamar a las API protegidas.

Si agrega la autenticación a una aplicación, agregue manualmente el paquete al archivo de proyecto de la aplicación:

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

El paquete [Microsoft. Authentication. Webassembly. msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) agrega de manera transitiva el paquete [Microsoft. AspNetCore. Components. webassembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) a la aplicación.

### <a name="authentication-service-support"></a>Compatibilidad con el servicio de autenticación

<xref:System.Net.Http.HttpClient>Se ha agregado compatibilidad con las instancias de que incluyen tokens de acceso al hacer solicitudes al proyecto de servidor.

*Program.cs*:

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

La compatibilidad con la autenticación de usuarios se registra en el contenedor de servicios con el <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> método de extensión proporcionado por el paquete [Microsoft. Authentication. webassembly. msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) . Este método configura los servicios necesarios para que la aplicación interactúe con el Identity proveedor (IP).

*Program.cs*:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

El <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> método acepta una devolución de llamada para configurar los parámetros necesarios para autenticar una aplicación. Los valores necesarios para configurar la aplicación pueden obtenerse a partir de la configuración de AAD de Azure portal cuando se registra la aplicación.

La configuración se proporciona mediante el archivo *wwwroot/appSettings. JSON* :

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

Ejemplo:

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": true
  }
}
```

### <a name="access-token-scopes"></a>Ámbitos de token de acceso

Los ámbitos de token de acceso predeterminados representan la lista de ámbitos de token de acceso que son:

* Se incluye de forma predeterminada en la solicitud de inicio de sesión.
* Se usa para aprovisionar un token de acceso inmediatamente después de la autenticación.

Todos los ámbitos deben pertenecer a la misma aplicación por reglas de Azure Active Directory. Se pueden agregar ámbitos adicionales para las aplicaciones de API adicionales según sea necesario:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

Para obtener más información, consulte las siguientes secciones del artículo *escenarios adicionales* :

* [Solicitar tokens de acceso adicionales](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [Adjuntar tokens a solicitudes salientes](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)


### <a name="imports-file"></a>Importar archivo

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a>Página de índice

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a>Componente de aplicación

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a>Componente RedirectToLogin

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a>Componente LoginDisplay

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a>Componente de autenticación

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a>Componente FetchData

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a>Ejecución la aplicación

Ejecute la aplicación desde el proyecto de servidor. Al usar Visual Studio, puede:

* Establezca la lista desplegable **proyectos de inicio** en la barra de herramientas en la aplicación de *API de servidor* y seleccione el botón **Ejecutar** .
* Seleccione el proyecto de servidor en **Explorador de soluciones** y seleccione el botón **Ejecutar** en la barra de herramientas o inicie la aplicación en el menú **depurar** .

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Recursos adicionales

* <xref:security/blazor/webassembly/additional-scenarios>
* [Solicitudes de API Web no autenticadas o no autorizadas en una aplicación con un cliente predeterminado seguro](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/blazor/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [Documentación de la plataforma de identidad de Microsoft](/azure/active-directory/develop/)
