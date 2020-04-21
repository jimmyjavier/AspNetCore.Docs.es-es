---
title: Proteja una Blazor aplicación hospedada de ASP.NET Core WebAssembly con Azure Active Directory
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: a80be8d145b7c58be35e2c353a448db7e234e20b
ms.sourcegitcommit: 5547d920f322e5a823575c031529e4755ab119de
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661825"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-azure-active-directory"></a>Proteja una Blazor aplicación hospedada de ASP.NET Core WebAssembly con Azure Active Directory

Por [Javier Calvarro Nelson](https://github.com/javiercn) y Luke [Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

En este artículo se [ Blazor ](xref:blazor/hosting-models#blazor-webassembly) describe cómo crear una aplicación hospedada de WebAssembly que usa [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) para la autenticación.

## <a name="register-apps-in-aad-b2c-and-create-solution"></a>Registre aplicaciones en AAD B2C y cree una solución

### <a name="create-a-tenant"></a>Creación de un inquilino

Siga las instrucciones de [Inicio rápido: configure un inquilino](/azure/active-directory/develop/quickstart-create-new-tenant) para crear un inquilino en AAD.

### <a name="register-a-server-api-app"></a>Registrar una aplicación de API de servidor

Siga las instrucciones de [Inicio rápido: registre una aplicación con la plataforma](/azure/active-directory/develop/quickstart-register-app) de identidad de Microsoft y los temas de Azure AAD posteriores para registrar una aplicación de AAD para la aplicación *API* de servidor en el área**Registros** de aplicaciones de **Azure Active Directory** > de Azure Portal:

1. Seleccione **Nuevo registro**.
1. Proporcione un **nombre** para la aplicación (por ejemplo, ** Blazor AAD**de servidor).
1. Elija un **tipo de cuenta admitida.** Puede seleccionar **Cuentas solo en este directorio de organización** (inquilino único) para esta experiencia.
1. La *aplicación API* de servidor no requiere un URI de **redirección** en este escenario, así que deje el menú desplegable establecido en **Web** y no escriba un URI de redirección.
1. Deshabilite la casilla de verificación **Permisos** > **de concesión de permisos para abrir y offline_access permisos.**
1. Seleccione **Registrar**.

En **Permisos**de API , quite el permiso**User.Read** de **Microsoft Graph,** > ya que la aplicación no requiere acceso al inicio de sesión ni al perfil de uer.

En **Exponer una API:**

1. Seleccione **Agregar un ámbito**.
1. Seleccione **Guardar y continuar**.
1. Proporcione un **nombre de** `API.Access`ámbito (por ejemplo, ).
1. Proporcione un **nombre para mostrar** `Access API`del consentimiento de administrador (por ejemplo, ).
1. Proporcione una **descripción** del `Allows the app to access server app API endpoints.`consentimiento del administrador (por ejemplo, ).
1. Confirme que el **estado** está establecido en **Habilitado**.
1. Seleccione la opción **Agregar un ámbito**.

Registre la siguiente información:

* *Aplicación API de servidor* ID de aplicación (ID de `11111111-1111-1111-1111-111111111111`cliente) (por ejemplo, )
* URI de identificador de `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` `api://11111111-1111-1111-1111-111111111111`aplicación (por ejemplo, , , o el valor personalizado que proporcionó)
* ID de directorio (ID de `222222222-2222-2222-2222-222222222222`inquilino) (por ejemplo, )
* Dominio de inquilino de `contoso.onmicrosoft.com`AAD (por ejemplo, )
* Alcance predeterminado (por `API.Access`ejemplo, )

### <a name="register-a-client-app"></a>Registrar una aplicación de cliente

Siga las instrucciones de [Inicio rápido: registre una aplicación con la plataforma](/azure/active-directory/develop/quickstart-register-app) de identidad de Microsoft y los temas de Azure AAD posteriores para registrar una aplicación de AAD para la aplicación *cliente* en el área**Registros** de aplicaciones de **Azure Active Directory** > de Azure Portal:

1. Seleccione **Nuevo registro**.
1. Proporcione un **nombre** para la aplicación (por ejemplo, ** Blazor Client AAD**).
1. Elija un **tipo de cuenta admitida.** Puede seleccionar **Cuentas solo en este directorio de organización** (inquilino único) para esta experiencia.
1. Deje el descenso desplegable URI de **redirección** establecido `https://localhost:5001/authentication/login-callback`en **Web**y proporcione un URI de redireccionamiento de .
1. Deshabilite la casilla de verificación **Permisos** > **de concesión de permisos para abrir y offline_access permisos.**
1. Seleccione **Registrar**.

En la**Web****de configuraciones** > de la plataforma de **autenticación:** > 

1. Confirme que el `https://localhost:5001/authentication/login-callback` URI de **redireccionamiento** está presente.
1. En **Implicit grant**, active las casillas de los tokens de **acceso** y los tokens **de identificador.**
1. Los valores predeterminados restantes de la aplicación son aceptables para esta experiencia.
1. Seleccione el botón **Guardar**.

En **Permisos de API:**

1. Confirme que la aplicación tiene el permiso **Microsoft Graph** > **User.Read.**
1. Seleccione **Agregar un permiso** seguido de Mis **API**.
1. Seleccione la *aplicación API* de servidor en la columna **Nombre** (por ejemplo, ** Blazor AAD de servidor).**
1. Abra la lista **de API.**
1. Habilite el acceso a la `API.Access`API (por ejemplo, ).
1. Seleccione **Agregar permisos**.
1. Seleccione el botón Conceder contenido de administrador para el botón **"NOMBRE DEL INQUILINO".** Seleccione **Sí** para confirmar la acción.

Registre el identificador de aplicación de *aplicación* `33333333-3333-3333-3333-333333333333`cliente (ID de cliente) (por ejemplo, ).

### <a name="create-the-app"></a>Creación de la aplicación

Reemplace los marcadores de posición del siguiente comando por la información registrada anteriormente y ejecute el comando en un shell de comandos:

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho --tenant-id "{TENANT ID}"
```

Para especificar la ubicación de salida, que crea una carpeta de proyecto si no existe, `-o BlazorSample`incluya la opción de salida en el comando con una ruta de acceso (por ejemplo, ). El nombre de la carpeta también pasa a formar parte del nombre del proyecto.

> [!NOTE]
> Pase el URI de `app-id-uri` identificador de aplicación a la opción, pero tenga en cuenta que puede ser necesario un cambio de configuración en la aplicación cliente, que se describe en la sección [Ámbitos](#access-token-scopes) de token de acceso.

## <a name="server-app-configuration"></a>Configuración de la aplicación de servidor

*Esta sección pertenece a la aplicación **de servidor** de la solución.*

### <a name="authentication-package"></a>Paquete de autenticación

El soporte para autenticar y autorizar llamadas a ASP.NET API `Microsoft.AspNetCore.Authentication.AzureAD.UI`web principales es proporcionado por:

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
    Version="3.1.0" />
```

### <a name="authentication-service-support"></a>Soporte de servicio de autenticación

El `AddAuthentication` método configura los servicios de autenticación dentro de la aplicación y configura el controlador JWT Bearer como el método de autenticación predeterminado. El `AddAzureADBearer` método configura los parámetros específicos en el controlador de JWT Bearer necesarios para validar los tokens emitidos por Azure Active Directory:

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

`UseAuthentication`y `UseAuthorization` asegurar se que:

* La aplicación intenta analizar y validar tokens en las solicitudes entrantes.
* Se produce un error en cualquier solicitud que intente tener acceso a un recurso protegido sin las credenciales adecuadas.

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a>User.Identity.Name

De forma predeterminada, la `User.Identity.Name` API de la `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` aplicación de servidor `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`se rellena con el valor del tipo de notificación (por ejemplo, ).

Para configurar la aplicación para `name` recibir el valor del tipo de notificación, `Startup.ConfigureServices`configure [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) de <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in :

```csharp
services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a>Configuración de la aplicación

El archivo *appsettings.json* contiene las opciones para configurar el controlador portador de JWT utilizado para validar tokens de acceso.

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{API CLIENT ID}",
  }
}
```

### <a name="weatherforecast-controller"></a>Controlador WeatherForecast

El controlador WeatherForecast (*Controllers/WeatherForecastController.cs*) expone `[Authorize]` una API protegida con el atributo aplicado al controlador. Es **importante** entender que:

* El `[Authorize]` atributo de este controlador de API es lo único que protege esta API del acceso no autorizado.
* El `[Authorize]` atributo utilizado Blazor en la aplicación WebAssembly solo sirve como sugerencia a la aplicación de que el usuario debe estar autorizado para que la aplicación funcione correctamente.

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

Cuando se crea una aplicación para`SingleOrg`usar cuentas de trabajo o educativas (`Microsoft.Authentication.WebAssembly.Msal`), la aplicación recibe automáticamente una referencia de paquete para la biblioteca de autenticación de [Microsoft](/azure/active-directory/develop/msal-overview) ( ). El paquete proporciona un conjunto de primitivas que ayudan a la aplicación a autenticar a los usuarios y obtener tokens para llamar a las API protegidas.

Si agrega autenticación a una aplicación, agregue manualmente el paquete al archivo de proyecto de la aplicación:

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

Reemplace `{VERSION}` en la referencia del paquete `Microsoft.AspNetCore.Blazor.Templates` anterior por <xref:blazor/get-started> la versión del paquete que se muestra en el artículo.

El `Microsoft.Authentication.WebAssembly.Msal` paquete agrega transitivamente el `Microsoft.AspNetCore.Components.WebAssembly.Authentication` paquete a la aplicación.

### <a name="authentication-service-support"></a>Soporte de servicio de autenticación

La compatibilidad con la autenticación de usuarios `AddMsalAuthentication` se registra `Microsoft.Authentication.WebAssembly.Msal` en el contenedor de servicios con el método de extensión proporcionado por el paquete. Este método configura todos los servicios necesarios para que la aplicación interactúe con el proveedor de identidades (IP).

*Program.cs*:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "https://login.microsoftonline.com/{TENANT ID}";
    authentication.ClientId = "{CLIENT ID}";
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

El `AddMsalAuthentication` método acepta una devolución de llamada para configurar los parámetros necesarios para autenticar una aplicación. Los valores necesarios para configurar la aplicación se pueden obtener de la configuración de AAD de Azure Portal al registrar la aplicación.

### <a name="access-token-scopes"></a>Acceso a ámbitos de token

Los ámbitos de token de acceso predeterminados representan la lista de ámbitos de token de acceso que son:

* Incluido de forma predeterminada en la solicitud de inicio de sesión.
* Se utiliza para aprovisionar un token de acceso inmediatamente después de la autenticación.

Todos los ámbitos deben pertenecer a la misma aplicación según las reglas de Azure Active Directory. Se pueden agregar ámbitos adicionales para aplicaciones de API adicionales según sea necesario:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> Si Azure Portal proporciona un URI de ámbito y **la aplicación produce una excepción no controlada** cuando recibe una respuesta *401 Unauthorized* de la API, intente usar un URI de ámbito que no incluya el esquema y el host. Por ejemplo, Azure Portal puede proporcionar uno de los siguientes formatos de URI de ámbito:
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> Proporcione el URI de ámbito sin el esquema y el host:
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

Para obtener más información, vea <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.

### <a name="imports-file"></a>Archivo de importaciones

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a>Página de índice

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a>Componente de la aplicación

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a>Componente RedirectToLogin

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a>Componente LoginDisplay

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a>Componente de autenticación

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a>Componente FetchData

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a>Ejecutar la aplicación

Ejecute la aplicación desde el proyecto Servidor. Al usar Visual Studio, seleccione el proyecto de servidor en el **Explorador** de soluciones y seleccione el botón **Ejecutar** en la barra de herramientas o inicie la aplicación desde el menú **Depurar.**

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Recursos adicionales

* [Solicitar tokens de acceso adicionales](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* <xref:security/authentication/azure-active-directory/index>
* [Documentación de la plataforma de identidad de Microsoft](/azure/active-directory/develop/)
