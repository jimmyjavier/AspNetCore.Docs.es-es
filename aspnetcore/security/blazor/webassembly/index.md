---
title: Protección de WebAssembly de Blazor en ASP.NET Core
author: guardrex
description: Obtenga información sobre cómo proteger aplicaciones WebAssemlby de Blazor como aplicaciones de página única (SPA).
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
uid: security/blazor/webassembly/index
ms.openlocfilehash: e8ea5e6b6d7e28906e6109e6730ac25f190b4191
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2020
ms.locfileid: "82768005"
---
# <a name="secure-aspnet-core-blazor-webassembly"></a>Protección de WebAssembly de Blazor en ASP.NET Core

Por [Javier Calvarro Nelson](https://github.com/javiercn)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Las aplicaciones WebAssembly de Blazor se protegen de la misma manera que las aplicaciones de página única (SPA). Hay varios métodos para autenticar a los usuarios en las SPA, pero el enfoque más común y completo consiste en usar una implementación basada en el [protocolo OAuth 2.0](https://oauth.net/), como [Open ID Connect (OIDC)](https://openid.net/connect/).

## <a name="authentication-library"></a>Biblioteca de autenticación

WebAssembly de Blazor permite autenticar y autorizar aplicaciones mediante OIDC a través de la biblioteca `Microsoft.AspNetCore.Components.WebAssembly.Authentication`. La biblioteca proporciona un conjunto de primitivas para la autenticación sin problemas en back-ends de ASP.NET Core. La biblioteca integra ASP.NET Core Identity con compatibilidad con la autorización de API basada en [Identity Server](https://identityserver.io/). La biblioteca puede realizar la autenticación sobre cualquier proveedor de Identity (IP) de terceros que admita OIDC, que se denominan proveedores de OpenID (OP).

La compatibilidad con la autenticación en WebAssembly de Blazor se basa en la biblioteca *oidc-client.js*, que se usa para administrar los detalles del protocolo de autenticación subyacente.

Existen otras opciones para la autenticación de las SPA, como el uso de cookies de SameSite. Sin embargo, el diseño de ingeniería de WebAssembly de Blazor se limita a OAuth y OIDC como mejor opción para la autenticación en las aplicaciones WebAssembly de Blazor. Se ha elegido la [autenticación basada en tokens](xref:security/anti-request-forgery#token-based-authentication) basada en [JSON Web Token (JWT)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) antes que la [autenticación basada en cookies](xref:security/anti-request-forgery#cookie-based-authentication) por razones funcionales y de seguridad:

* El uso de un protocolo basado en tokens ofrece una superficie expuesta a ataques más pequeña, ya que los tokens no se envían en todas las solicitudes.
* Los puntos de conexión de servidor no requieren protección contra la [Falsificación de solicitudes entre sitios (CSRF)](xref:security/anti-request-forgery), ya que los tokens se envían de forma explícita. Esto permite hospedar aplicaciones WebAssembly de Blazor junto con aplicaciones de páginas de Razor Pages o MVC.
* Los tokens tienen permisos más restringidos que las cookies. Por ejemplo, los tokens no se pueden usar para administrar la cuenta de usuario o cambiar su contraseña a menos que esa funcionalidad se implemente de forma explícita.
* Los tokens tienen una duración corta, una hora de forma predeterminada, lo que limita la ventana de ataque. Los tokens también se pueden revocar en cualquier momento.
* Los JWT independientes ofrecen garantías al cliente y al servidor sobre el proceso de autenticación. Por ejemplo, un cliente tiene los medios para detectar y validar que los tokens que recibe son legítimos y se han emitido como parte de un proceso de autenticación determinado. Si un tercero intenta cambiar un token en medio del proceso de autenticación, el cliente puede detectar el token cambiado y evitar usarlo.
* Los tokens con OAuth y OIDC no se basan en que el agente de usuario se comporte correctamente para asegurarse de que la aplicación sea segura.
* Los protocolos basados en tokens, como OAuth y OIDC, permiten autenticar y autorizar aplicaciones independientes y hospedadas con el mismo conjunto de características de seguridad.

## <a name="authentication-process-with-oidc"></a>Proceso de autenticación con OIDC

La biblioteca `Microsoft.AspNetCore.Components.WebAssembly.Authentication` ofrece varias primitivas para implementar la autenticación y autorización mediante OIDC. En términos generales, la autenticación funciona de la siguiente manera:

* Cuando un usuario anónimo selecciona el botón de inicio de sesión o solicita una página con el atributo [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) aplicado, se le redirige a la página de inicio de sesión de la aplicación (`/authentication/login`).
* En la página de inicio de sesión, la biblioteca de autenticación se prepara para un redireccionamiento al punto de conexión de autorización. El punto de conexión de autorización está fuera de la aplicación WebAssembly de Blazor y se puede hospedar en un origen independiente. El punto de conexión es responsable de determinar si el usuario está autenticado y de emitir uno o más tokens en respuesta. La biblioteca de autenticación proporciona una devolución de llamada de inicio de sesión para recibir la respuesta de autenticación.
  * Si el usuario no está autenticado, se le redirige al sistema de autenticación subyacente, que normalmente es ASP.NET Core Identity.
  * Si el usuario ya se ha autenticado, el punto de conexión de autorización genera los tokens adecuados y devuelve al explorador al punto de conexión de devolución de llamada de inicio de sesión (`/authentication/login-callback`).
* Cuando la aplicación WebAssembly de Blazor carga el punto de conexión de devolución de llamada de inicio de sesión (`/authentication/login-callback`), se procesa la respuesta de autenticación.
  * Si el proceso de autenticación se completa correctamente, el usuario se autentica y, opcionalmente, se devuelve a la dirección URL protegida original que haya solicitado.
  * Si por algún motivo se produce un error en el proceso de autenticación, se envía al usuario a la página de inicio de sesión con errores (`/authentication/login-failed`) y se muestra un error.

## <a name="additional-resources"></a>Recursos adicionales

* Los artículos de esta *información general* ofrecen detalles sobre la autenticación de usuarios en WebAssembly de Blazor con proveedores específicos.
* <xref:security/blazor/webassembly/additional-scenarios>
