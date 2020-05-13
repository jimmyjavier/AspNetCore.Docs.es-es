---
title: Protección de una Blazor aplicación independiente ASP.net Core Webassembly con cuentas Microsoft
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
uid: security/blazor/webassembly/standalone-with-microsoft-accounts
ms.openlocfilehash: 9fc93cc02129081ac6c777677a0c8d6397724e53
ms.sourcegitcommit: 1250c90c8d87c2513532be5683640b65bfdf9ddb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/12/2020
ms.locfileid: "83153577"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-microsoft-accounts"></a>Protección de una Blazor aplicación independiente ASP.net Core Webassembly con cuentas Microsoft

Por [Javier Calvarro Nelson](https://github.com/javiercn) y [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Para crear una Blazor aplicación independiente Webassembly que use [cuentas de Microsoft con Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) para la autenticación:

1. [Creación de un inquilino de AAD y una aplicación Web](/azure/active-directory/develop/v2-overview)

   Registre una aplicación de AAD en **Azure Active Directory**el  >  área de**registros de aplicaciones** de Azure Active Directory del Azure Portal:

   1\. Proporcione un **nombre** para la aplicación (por ejemplo, ** Blazor cliente AAD**).<br>
   2\. En **tipos de cuenta compatibles**, seleccione **cuentas en cualquier directorio de la organización**.<br>
   3 \. Deje la lista desplegable **URI de redirección** establecida en **Web**y proporcione un URI de redireccionamiento de `https://localhost:5001/authentication/login-callback` .<br>
   4 \. Deshabilite **Permissions**la  >  casilla**conceder permisos Grant admin to OpenID y offline_access permisos** .<br>
   5 \. Seleccione **Registrar**.

   En configuración de la plataforma de **autenticación**  >  **Platform configurations**  >  **Web**:

   1\. Confirme que el **URI de redirección** de `https://localhost:5001/authentication/login-callback` está presente.<br>
   2\. En **concesión implícita**, active las casillas de verificación de **tokens de acceso** y **tokens de identificador**.<br>
   3 \. Los valores predeterminados restantes de la aplicación son aceptables para esta experiencia.<br>
   4 \. Seleccione el botón **Guardar**.

   Registre el identificador de aplicación (ID. de cliente) (por ejemplo, `11111111-1111-1111-1111-111111111111` ).

1. Reemplace los marcadores de posición en el siguiente comando por la información registrada anteriormente y ejecute el comando en un shell de comandos:

   ```dotnetcli
   dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common"
   ```

   Para especificar la ubicación de salida, que crea una carpeta de proyecto si no existe, incluya la opción output en el comando con una ruta de acceso (por ejemplo, `-o BlazorSample` ). El nombre de la carpeta también se convierte en parte del nombre del proyecto.

Después de crear la aplicación, debe poder:

* Inicie sesión en la aplicación con una cuenta de Microsoft.
* Solicite tokens de acceso para las API de Microsoft con el mismo enfoque que para Blazor las aplicaciones independientes, siempre y cuando haya configurado la aplicación correctamente. Para obtener más información, consulte [Inicio rápido: configurar una aplicación para exponer API Web](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).

## <a name="authentication-package"></a>Paquete de autenticación

Cuando se crea una aplicación para usar cuentas profesionales o educativas ( `SingleOrg` ), la aplicación recibe automáticamente una referencia de paquete para la [biblioteca de autenticación de Microsoft](/azure/active-directory/develop/msal-overview) ( `Microsoft.Authentication.WebAssembly.Msal` ). El paquete proporciona un conjunto de primitivas que ayudan a la aplicación a autenticar a los usuarios y obtener tokens para llamar a las API protegidas.

Si agrega la autenticación a una aplicación, agregue manualmente el paquete al archivo de proyecto de la aplicación:

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

Reemplace `{VERSION}` en la referencia de paquete anterior por la versión del `Microsoft.AspNetCore.Blazor.Templates` paquete que se muestra en el <xref:blazor/get-started> artículo.

El `Microsoft.Authentication.WebAssembly.Msal` paquete agrega de manera transitiva el `Microsoft.AspNetCore.Components.WebAssembly.Authentication` paquete a la aplicación.

## <a name="authentication-service-support"></a>Compatibilidad con el servicio de autenticación

La compatibilidad con la autenticación de usuarios se registra en el contenedor de servicios con el `AddMsalAuthentication` método de extensión proporcionado por el `Microsoft.Authentication.WebAssembly.Msal` paquete. Este método configura todos los servicios necesarios para que la aplicación interactúe con el Identity proveedor (IP).

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
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "{CLIENT ID}"
  }
}
```

Ejemplo:

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd"
  }
}
```

## <a name="access-token-scopes"></a>Ámbitos de token de acceso

La Blazor plantilla Webassembly no configura automáticamente la aplicación para solicitar un token de acceso para una API segura. Para aprovisionar un token de acceso como parte del flujo de inicio de sesión, agregue el ámbito a los ámbitos de token de acceso predeterminados de `MsalProviderOptions` :

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
* [Solicitudes de API Web no autenticadas o no autorizadas en una aplicación con un cliente predeterminado seguro](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/blazor/webassembly/aad-groups-roles>
* [Inicio rápido: Registro de una aplicación en la plataforma de identidad de Microsoft](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [Inicio rápido: Configuración de una aplicación para exponer las API web](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)
