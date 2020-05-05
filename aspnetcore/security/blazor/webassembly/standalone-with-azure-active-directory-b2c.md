---
title: Protección de una Blazor aplicación independiente ASP.net Core webassembly con Azure Active Directory B2C
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/24/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory-b2c
ms.openlocfilehash: 0fb4f4176f214d6bf0c005838a0ccbe4487243f2
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767979"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a>Protección de una Blazor aplicación independiente ASP.net Core webassembly con Azure Active Directory B2C

Por [Javier Calvarro Nelson](https://github.com/javiercn) y [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Para crear una Blazor aplicación independiente webassembly que use [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) para la autenticación:

1. Siga las instrucciones de los temas siguientes para crear un inquilino y registrar una aplicación web en Azure Portal:

   * [Cree un inquilino](/azure/active-directory-b2c/tutorial-create-tenant) &ndash; de AAD B2C registre la siguiente información:

     1\. AAD B2C instancia (por ejemplo, `https://contoso.b2clogin.com/`, que incluye la barra diagonal final)<br>
     2\. AAD B2C dominio del inquilino (por ejemplo `contoso.onmicrosoft.com`,)

   * [Registrar una aplicación](/azure/active-directory-b2c/tutorial-register-applications) &ndash; web realice las siguientes selecciones durante el registro de la aplicación:

     1\. Establezca **aplicación web/API Web** en **sí**.<br>
     2\. Establezca **permitir flujo implícito** en **sí**.<br>
     3 \. Agregue una **dirección URL** de `https://localhost:5001/authentication/login-callback`respuesta de.

     Registre el identificador de aplicación (ID. de cliente) ( `11111111-1111-1111-1111-111111111111`por ejemplo,).

   * [Crear flujos](/azure/active-directory-b2c/tutorial-create-user-flows) &ndash; de usuario crea un flujo de usuario de inicio de sesión e inicio de sesión.

     Como mínimo, seleccione el atributo de usuario**nombre para mostrar** de **notificaciones** > de `context.User.Identity.Name` aplicación para rellenar el en el `LoginDisplay` componente (*Shared/LoginDisplay. Razor*).

     Registre el nombre del flujo de usuario de inicio de sesión y de registro creado para la aplicación (por `B2C_1_signupsignin`ejemplo,).

1. Reemplace los marcadores de posición en el siguiente comando por la información registrada anteriormente y ejecute el comando en un shell de comandos:

   ```dotnetcli
   dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{DOMAIN}" -ssp "{SIGN UP OR SIGN IN POLICY}"
   ```

   Para especificar la ubicación de salida, que crea una carpeta de proyecto si no existe, incluya la opción output en el comando con una ruta de acceso ( `-o BlazorSample`por ejemplo,). El nombre de la carpeta también se convierte en parte del nombre del proyecto.

## <a name="authentication-package"></a>Paquete de autenticación

Cuando se crea una aplicación para usar una cuenta de B2C individual`IndividualB2C`(), la aplicación recibe automáticamente una referencia de paquete para la biblioteca de`Microsoft.Authentication.WebAssembly.Msal`autenticación de [Microsoft](/azure/active-directory/develop/msal-overview) (). El paquete proporciona un conjunto de primitivas que ayudan a la aplicación a autenticar a los usuarios y obtener tokens para llamar a las API protegidas.

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
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
});
```

El `AddMsalAuthentication` método acepta una devolución de llamada para configurar los parámetros necesarios para autenticar una aplicación. Los valores necesarios para configurar la aplicación se pueden obtener a partir de la configuración de las cuentas de Microsoft cuando se registra la aplicación.

La configuración se proporciona mediante el archivo *wwwroot/appSettings. JSON* :

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

Ejemplo:

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
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

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Recursos adicionales

* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:security/authentication/azure-ad-b2c>
* [Tutorial: Creación de un inquilino de Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant)
* [Documentación de la plataforma de identidad de Microsoft](/azure/active-directory/develop/)
