---
title: Protección de una Blazor aplicación independiente ASP.net Core webassembly con Azure Active Directory
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/06/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory
ms.openlocfilehash: adc102edb457059f4d9686a48103483547b667fd
ms.sourcegitcommit: 363e3a2a035f4082cb92e7b75ed150ba304258b3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/08/2020
ms.locfileid: "82976862"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-azure-active-directory"></a>Protección de una Blazor aplicación independiente ASP.net Core webassembly con Azure Active Directory

Por [Javier Calvarro Nelson](https://github.com/javiercn) y [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Para crear una Blazor aplicación independiente webassembly que use [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) para la autenticación:

[Cree un inquilino de AAD y una aplicación web](/azure/active-directory/develop/v2-overview):

Registre una aplicación de AAD en el área de**registros de aplicaciones** de **Azure Active Directory** > del Azure Portal:

1. Proporcione un **nombre** para la aplicación (por ejemplo, ** Blazor cliente AAD**).
1. Elija un **tipo de cuenta compatible**. Puede seleccionar **cuentas en este directorio de la organización solo** para esta experiencia.
1. Deje la lista desplegable **URI de redirección** establecida en **Web**y proporcione un URI de redireccionamiento de `https://localhost:5001/authentication/login-callback`.
1. Deshabilite **Permissions** > la casilla**conceder permisos Grant admin to OpenID y offline_access permisos** .
1. Seleccione **Registrar**.

En **Authentication** > **Platform configurations**configuración > de la plataforma de autenticación**Web**:

1. Confirme que el **URI de redirección** de `https://localhost:5001/authentication/login-callback` está presente.
1. En **concesión implícita**, active las casillas de verificación de **tokens de acceso** y **tokens de identificador**.
1. Los valores predeterminados restantes de la aplicación son aceptables para esta experiencia.
1. Seleccione el botón **Guardar**.

Registre la siguiente información:

* IDENTIFICADOR de aplicación (ID. de cliente) ( `11111111-1111-1111-1111-111111111111`por ejemplo,)
* IDENTIFICADOR de directorio (identificador de inquilino) (por `22222222-2222-2222-2222-222222222222`ejemplo,)

Reemplace los marcadores de posición en el siguiente comando por la información registrada anteriormente y ejecute el comando en un shell de comandos:

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

Para especificar la ubicación de salida, que crea una carpeta de proyecto si no existe, incluya la opción output en el comando con una ruta de acceso ( `-o BlazorSample`por ejemplo,). El nombre de la carpeta también se convierte en parte del nombre del proyecto.

## <a name="authentication-package"></a>Paquete de autenticación

Cuando se crea una aplicación para usar cuentas profesionales o educativas (`SingleOrg`), la aplicación recibe automáticamente una referencia de paquete para la biblioteca de autenticación`Microsoft.Authentication.WebAssembly.Msal`de [Microsoft](/azure/active-directory/develop/msal-overview) (). El paquete proporciona un conjunto de primitivas que ayudan a la aplicación a autenticar a los usuarios y obtener tokens para llamar a las API protegidas.

Si agrega la autenticación a una aplicación, agregue manualmente el paquete al archivo de proyecto de la aplicación:

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

Reemplace `{VERSION}` en la referencia de paquete anterior por la versión del `Microsoft.AspNetCore.Blazor.Templates` paquete que se muestra <xref:blazor/get-started> en el artículo.

El `Microsoft.Authentication.WebAssembly.Msal` paquete agrega de manera transitiva el `Microsoft.AspNetCore.Components.WebAssembly.Authentication` paquete a la aplicación.

## <a name="authentication-service-support"></a>Compatibilidad con el servicio de autenticación

La compatibilidad con la autenticación de usuarios se registra en el contenedor de `AddMsalAuthentication` servicios con el método de `Microsoft.Authentication.WebAssembly.Msal` extensión proporcionado por el paquete. Este método configura todos los servicios necesarios para que la aplicación interactúe con el Identity proveedor (IP).

*Program.cs*:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

El `AddMsalAuthentication` método acepta una devolución de llamada para configurar los parámetros necesarios para autenticar una aplicación. Los valores necesarios para configurar la aplicación se pueden obtener a partir de la configuración de las cuentas de Microsoft cuando se registra la aplicación.

La configuración se proporciona mediante el archivo *wwwroot/appSettings. JSON* :

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT ID}"
  }
}
```

Ejemplo:

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd"
  }
}
```

## <a name="access-token-scopes"></a>Ámbitos de token de acceso

La Blazor plantilla webassembly no configura automáticamente la aplicación para solicitar un token de acceso para una API segura. Para aprovisionar un token de acceso como parte del flujo de inicio de sesión, agregue el ámbito a los ámbitos de token de acceso `MsalProviderOptions`predeterminados de:

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

## <a name="imports-file"></a>Importar archivo

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a>Página de índice

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a>Componente de aplicación

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a>Componente RedirectToLogin

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a>Componente LoginDisplay

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a>Componente de autenticación

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Recursos adicionales

* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:security/blazor/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [Documentación de la plataforma de identidad de Microsoft](/azure/active-directory/develop/)
