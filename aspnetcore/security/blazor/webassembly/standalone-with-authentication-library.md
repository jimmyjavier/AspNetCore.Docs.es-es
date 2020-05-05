---
title: Protección de una Blazor aplicación independiente ASP.net Core webassembly con la biblioteca de autenticación
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
uid: security/blazor/webassembly/standalone-with-authentication-library
ms.openlocfilehash: 6907a1213a6a9089e2aed885093c2fd38f972ad0
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2020
ms.locfileid: "82768057"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-the-authentication-library"></a>Protección de una Blazor aplicación independiente ASP.net Core webassembly con la biblioteca de autenticación

Por [Javier Calvarro Nelson](https://github.com/javiercn) y [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

*Por Azure Active Directory (AAD) y Azure Active Directory B2C (AAD B2C), no siga las instrucciones de este tema. Vea los temas de AAD y AAD B2C en este nodo de tabla de contenido.*

Para crear una Blazor aplicación independiente webassembly que `Microsoft.AspNetCore.Components.WebAssembly.Authentication` use la biblioteca, ejecute el siguiente comando en un shell de comandos:

```dotnetcli
dotnet new blazorwasm -au Individual
```

Para especificar la ubicación de salida, que crea una carpeta de proyecto si no existe, incluya la opción output en el comando con una ruta de acceso ( `-o BlazorSample`por ejemplo,). El nombre de la carpeta también se convierte en parte del nombre del proyecto.

En Visual Studio, [cree una Blazor aplicación webassembly](xref:blazor/get-started). Establezca la **autenticación** en **cuentas de usuario individuales** con la opción **almacenar cuentas de usuario en la aplicación** .

## <a name="authentication-package"></a>Paquete de autenticación

Cuando se crea una aplicación para usar cuentas de usuario individuales, la aplicación recibe automáticamente una referencia de paquete `Microsoft.AspNetCore.Components.WebAssembly.Authentication` para el paquete en el archivo de proyecto de la aplicación. El paquete proporciona un conjunto de primitivas que ayudan a la aplicación a autenticar a los usuarios y obtener tokens para llamar a las API protegidas.

Si agrega la autenticación a una aplicación, agregue manualmente el paquete al archivo de proyecto de la aplicación:

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

Reemplace `{VERSION}` en la referencia de paquete anterior por la versión del `Microsoft.AspNetCore.Blazor.Templates` paquete que se muestra <xref:blazor/get-started> en el artículo.

## <a name="authentication-service-support"></a>Compatibilidad con el servicio de autenticación

La compatibilidad con la autenticación de usuarios se registra en el contenedor de `AddOidcAuthentication` servicios con el método de `Microsoft.AspNetCore.Components.WebAssembly.Authentication` extensión proporcionado por el paquete. Este método configura todos los servicios necesarios para que la aplicación interactúe con el Identity proveedor (IP).

*Program.cs*:

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    builder.Configuration.Bind("Local", options.ProviderOptions);
});
```

La configuración se proporciona mediante el archivo *wwwroot/appSettings. JSON* :

```json
{
    "Local": {
        "Authority": "{AUTHORITY}",
        "ClientId": "{CLIENT ID}"
    }
}
```

La compatibilidad con la autenticación para aplicaciones independientes se ofrece mediante Open ID Connect (OIDC). El `AddOidcAuthentication` método acepta una devolución de llamada para configurar los parámetros necesarios para autenticar una aplicación mediante OIDC. Los valores necesarios para configurar la aplicación pueden obtenerse a partir de la dirección IP compatible con OIDC. Obtenga los valores al registrar la aplicación, que suele producirse en su portal en línea.

## <a name="access-token-scopes"></a>Ámbitos de token de acceso

La Blazor plantilla webassembly no configura automáticamente la aplicación para solicitar un token de acceso para una API segura. Para aprovisionar un token de acceso como parte del flujo de inicio de sesión, agregue el ámbito a los ámbitos de token predeterminados de `OidcProviderOptions`:

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
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
> options.ProviderOptions.DefaultScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

Para obtener más información, consulte las siguientes secciones del artículo *escenarios adicionales* :

* [Solicitar tokens de acceso adicionales](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [Adjuntar tokens a solicitudes salientes](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a>Importar archivo

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a>Página de índice

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

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
