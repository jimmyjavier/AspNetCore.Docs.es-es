---
title: Protección de una Blazor aplicación hospedada en Webassembly ASP.net Core con Azure Active Directory
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/11/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: 6ff95f0c5c925cbafef2b997a6cb23aeb15ff1aa
ms.sourcegitcommit: 1250c90c8d87c2513532be5683640b65bfdf9ddb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/12/2020
ms.locfileid: "83153962"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-azure-active-directory"></a>Protección de una Blazor aplicación hospedada en Webassembly ASP.net Core con Azure Active Directory

Por [Javier Calvarro Nelson](https://github.com/javiercn) y [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

En este artículo se describe cómo crear una [ Blazor aplicación hospedada en webassembly](xref:blazor/hosting-models#blazor-webassembly) que usa [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) para la autenticación.

## <a name="register-apps-in-aad-and-create-solution"></a>Registro de aplicaciones en AAD y creación de una solución

### <a name="create-a-tenant"></a>Creación de un inquilino

Siga las instrucciones de [Inicio rápido: configuración de un inquilino](/azure/active-directory/develop/quickstart-create-new-tenant) para crear un inquilino en AAD.

### <a name="register-a-server-api-app"></a>Registrar una aplicación de API de servidor

Siga las instrucciones de [Inicio rápido: registro de una aplicación con la plataforma de Microsoft Identity y los](/azure/active-directory/develop/quickstart-register-app) temas de Azure AAD subsiguientes para registrar una aplicación de AAD para la *aplicación de API de servidor* en el área de **Azure Active Directory**  >  **registros de aplicaciones** de Azure Active Directory del Azure Portal:

1. Seleccione **Nuevo registro**.
1. Proporcione un **nombre** para la aplicación (por ejemplo, ** Blazor servidor AAD**).
1. Elija un **tipo de cuenta compatible**. Solo puede seleccionar **cuentas en este directorio de la organización** (un solo inquilino) para esta experiencia.
1. La *aplicación de API de servidor* no requiere un **URI de redirección** en este escenario, por lo que deje la lista desplegable establecida en **Web** y no escriba un URI de redirección.
1. Deshabilite **Permissions**la  >  casilla**conceder permisos Grant admin to OpenID y offline_access permisos** .
1. Seleccione **Registrar**.

En **permisos**de la API, quite el **Microsoft Graph**  >  **usuario.** permiso de lectura, ya que la aplicación no requiere el inicio de sesión o el acceso de Perfil de UER.

En **exponer una API**:

1. Seleccione **Agregar un ámbito**.
1. Selecciona **Guardar y continuar**.
1. Proporcione un **nombre de ámbito** (por ejemplo, `API.Access` ).
1. Proporcione un **nombre para mostrar del consentimiento del administrador** (por ejemplo, `Access API` ).
1. Proporcione una **Descripción del consentimiento del administrador** (por ejemplo, `Allows the app to access server app API endpoints.` ).
1. Confirme que el **Estado** está establecido en **habilitado**.
1. Seleccione la opción **Agregar un ámbito**.

Registre la siguiente información:

* *Aplicación de API de servidor* IDENTIFICADOR de aplicación (ID. de cliente) (por ejemplo, `11111111-1111-1111-1111-111111111111` )
* URI de ID. de aplicación (por ejemplo, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` , `api://11111111-1111-1111-1111-111111111111` o el valor personalizado que proporcionó)
* IDENTIFICADOR de directorio (identificador de inquilino) (por ejemplo, `222222222-2222-2222-2222-222222222222` )
* Dominio del inquilino de AAD (por ejemplo, `contoso.onmicrosoft.com` )
* Ámbito predeterminado (por ejemplo, `API.Access` )

### <a name="register-a-client-app"></a>Registrar una aplicación de cliente

Siga las instrucciones de [Inicio rápido: registro de una aplicación con la plataforma de Microsoft Identity y los](/azure/active-directory/develop/quickstart-register-app) temas de Azure AAD subsiguientes para registrar una aplicación de AAD para la *aplicación cliente* en el **Azure Active Directory**  >  área de**registros de aplicaciones** de Azure Active Directory del Azure Portal:

1. Seleccione **Nuevo registro**.
1. Proporcione un **nombre** para la aplicación (por ejemplo, ** Blazor cliente AAD**).
1. Elija un **tipo de cuenta compatible**. Solo puede seleccionar **cuentas en este directorio de la organización** (un solo inquilino) para esta experiencia.
1. Deje la lista desplegable **URI de redirección** establecida en **Web**y proporcione un URI de redireccionamiento de `https://localhost:5001/authentication/login-callback` .
1. Deshabilite **Permissions**la  >  casilla**conceder permisos Grant admin to OpenID y offline_access permisos** .
1. Seleccione **Registrar**.

En configuración de la plataforma de **autenticación**  >  **Platform configurations**  >  **Web**:

1. Confirme que el **URI de redirección** de `https://localhost:5001/authentication/login-callback` está presente.
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

Registre el identificador de aplicación de la aplicación *cliente* (identificador de cliente) (por ejemplo, `33333333-3333-3333-3333-333333333333` ).

### <a name="create-the-app"></a>Creación de la aplicación

Reemplace los marcadores de posición en el siguiente comando por la información registrada anteriormente y ejecute el comando en un shell de comandos:

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho --tenant-id "{TENANT ID}"
```

Para especificar la ubicación de salida, que crea una carpeta de proyecto si no existe, incluya la opción output en el comando con una ruta de acceso (por ejemplo, `-o BlazorSample` ). El nombre de la carpeta también se convierte en parte del nombre del proyecto.

> [!NOTE]
> Pase el URI de ID. de aplicación a la `app-id-uri` opción, pero tenga en cuenta que es posible que se requiera un cambio de configuración en la aplicación cliente, que se describe en la sección [ámbitos de token de acceso](#access-token-scopes) .

## <a name="server-app-configuration"></a>Configuración de la aplicación de servidor

*Esta sección pertenece a la aplicación de **servidor** de la solución.*

### <a name="authentication-package"></a>Paquete de autenticación

La compatibilidad para autenticar y autorizar llamadas a ASP.NET Core API Web la proporciona `Microsoft.AspNetCore.Authentication.AzureAD.UI` :

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
    Version="{VERSION}" />
```

### <a name="authentication-service-support"></a>Compatibilidad con el servicio de autenticación

El `AddAuthentication` método configura los servicios de autenticación dentro de la aplicación y configura el controlador de portador JWT como el método de autenticación predeterminado. El `AddAzureADBearer` método configura los parámetros específicos en el controlador de portador JWT necesario para validar los tokens emitidos por el Azure Active Directory:

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

`UseAuthentication`y `UseAuthorization` Asegúrese de que:

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
services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a>Configuración de aplicaciones

El archivo *appSettings. JSON* contiene las opciones para configurar el controlador de portador JWT que se usa para validar los tokens de acceso.

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

El controlador WeatherForecast (*Controllers/WeatherForecastController. CS*) expone una API protegida con el `[Authorize]` atributo que se aplica al controlador. Es **importante** comprender esto:

* El `[Authorize]` atributo de este controlador de API es lo único que protege esta API frente al acceso no autorizado.
* El `[Authorize]` atributo que se usa en la Blazor aplicación webassembly solo sirve como una sugerencia a la aplicación a la que el usuario debe estar autorizado para que la aplicación funcione correctamente.

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

Cuando se crea una aplicación para usar cuentas profesionales o educativas ( `SingleOrg` ), la aplicación recibe automáticamente una referencia de paquete para la [biblioteca de autenticación de Microsoft](/azure/active-directory/develop/msal-overview) ( `Microsoft.Authentication.WebAssembly.Msal` ). El paquete proporciona un conjunto de primitivas que ayudan a la aplicación a autenticar a los usuarios y obtener tokens para llamar a las API protegidas.

Si agrega la autenticación a una aplicación, agregue manualmente el paquete al archivo de proyecto de la aplicación:

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

Reemplace `{VERSION}` en la referencia de paquete anterior por la versión del `Microsoft.AspNetCore.Blazor.Templates` paquete que se muestra en el <xref:blazor/get-started> artículo.

El `Microsoft.Authentication.WebAssembly.Msal` paquete agrega de manera transitiva el `Microsoft.AspNetCore.Components.WebAssembly.Authentication` paquete a la aplicación.

### <a name="authentication-service-support"></a>Compatibilidad con el servicio de autenticación

`HttpClient`Se ha agregado compatibilidad con las instancias de que incluyen tokens de acceso al hacer solicitudes al proyecto de servidor.

*Program.cs*:

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

La compatibilidad con la autenticación de usuarios se registra en el contenedor de servicios con el `AddMsalAuthentication` método de extensión proporcionado por el `Microsoft.Authentication.WebAssembly.Msal` paquete. Este método configura todos los servicios necesarios para que la aplicación interactúe con el Identity proveedor (IP).

*Program.cs*:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

El `AddMsalAuthentication` método acepta una devolución de llamada para configurar los parámetros necesarios para autenticar una aplicación. Los valores necesarios para configurar la aplicación pueden obtenerse a partir de la configuración de AAD de Azure portal cuando se registra la aplicación.

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

> [!NOTE]
> Si el Azure Portal proporciona un URI de ámbito y **la aplicación produce una excepción no controlada** cuando recibe una respuesta *401 no autorizada* de la API, intente usar un URI de ámbito que no incluya el esquema y el host. Por ejemplo, el Azure Portal puede proporcionar uno de los siguientes formatos de URI de ámbito:
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> Proporcione el URI del ámbito sin el esquema y el host:
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

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

Ejecute la aplicación desde el proyecto de servidor. Al usar Visual Studio, seleccione el proyecto de servidor en **Explorador de soluciones** y seleccione el botón **Ejecutar** en la barra de herramientas o inicie la aplicación en el menú **depurar** .

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
