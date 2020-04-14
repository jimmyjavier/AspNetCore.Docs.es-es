---
title: Configuración de inicio de sesión externo de Facebook en ASP.NET Core
author: rick-anderson
description: Tutorial con ejemplos de código que demuestran la integración de la autenticación de usuario de la cuenta de Facebook en una aplicación ASP.NET Core existente.
ms.author: riande
ms.custom: seoapril2019, mvc, seodec18
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/facebook-logins
ms.openlocfilehash: 9b3128addafb41ad6ec44af5cb12e89607e1ae59
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277306"
---
# <a name="facebook-external-login-setup-in-aspnet-core"></a>Configuración de inicio de sesión externo de Facebook en ASP.NET Core

Por [Valeriy Novytskyy](https://github.com/01binary) y [Rick Anderson](https://twitter.com/RickAndMSFT)

<!-- per @rick-anderson and scott addie, don't update images. Remove images and point the customer to the FB set up page. FB needs to maintain  instructions to get key and secret.
-->

En este tutorial con ejemplos de código se muestra cómo permitir que los usuarios inicien sesión con su cuenta de Facebook mediante un proyecto de ejemplo ASP.NET Core 3.0 creado en la [página anterior.](xref:security/authentication/social/index) Comenzamos creando un ID de aplicación de Facebook siguiendo los [pasos oficiales.](https://developers.facebook.com)

## <a name="create-the-app-in-facebook"></a>Crea la aplicación en Facebook

* Agregue el paquete NuGet [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) al proyecto.

* Ve a la página de la [aplicación Desarrolladores](https://developers.facebook.com/apps/) de Facebook e inicia sesión. Si aún no tienes una cuenta de Facebook, usa el enlace **Registrarse para Facebook** en la página de inicio de sesión para crear una.  Una vez que tengas una cuenta de Facebook, sigue las instrucciones para registrarte como desarrollador de Facebook.

* En el menú **Mis aplicaciones,** seleccione **Crear aplicación** para crear un nuevo identificador de aplicación.

   ![Portal de Facebook para desarrolladores abierto en Microsoft Edge](index/_static/FBMyApps.png)

* Rellena el formulario y toca el botón Crear ID de **aplicación.**

  ![Crear un nuevo formulario de ID de aplicación](index/_static/FBNewAppId.png)

* En la nueva tarjeta De la aplicación, seleccione **Agregar un producto**.  En la tarjeta de inicio de sesión de **Facebook,** haz clic en **Configurar** 

  ![Página de configuración del producto](index/_static/FBProductSetup.png)

* El asistente **de inicio rápido se** inicia con Elegir una **plataforma** como primera página. Omita el asistente por ahora haciendo clic en el enlace **Configuración** de inicio de sesión de **FaceBook** en el menú de la parte inferior izquierda:

  ![Omitir inicio rápido](index/_static/FBSkipQuickStart.png)

* Se le presenta la página Configuración de OAuth de **cliente:**

  ![Página Configuración de OAuth de cliente](index/_static/FBOAuthSetup.png)

* Introduzca el URI de desarrollo con */signin-facebook* anexado al campo `https://localhost:44320/signin-facebook`URI de redirección de **OAuth válido** (por ejemplo: ). La autenticación de Facebook configurada más adelante en este tutorial controlará automáticamente las solicitudes en la ruta */signin-facebook* para implementar el flujo de OAuth.

> [!NOTE]
> El URI */signin-facebook* se establece como la devolución de llamada predeterminada del proveedor de autenticación de Facebook. Puede cambiar el URI de devolución de llamada predeterminado al configurar el middleware de autenticación de Facebook a través de la propiedad [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) heredada de la clase [FacebookOptions.](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions)

* Haga clic en **Guardar cambios**.

* Haga clic en el vínculo **Configuración** > **básica** en el panel de navegación izquierdo.

  En esta página, anote `App ID` su `App Secret`archivo . Agregará ambos a la aplicación ASP.NET Core en la siguiente sección:

* Al implementar el sitio, debe volver a visitar la página de configuración de inicio de **sesión** de Facebook y registrar un nuevo URI público.

## <a name="store-the-facebook-app-id-and-secret"></a>Almacena el ID y el secreto de la aplicación de Facebook

Almacene ajustes confidenciales, como el identificador de la aplicación de Facebook y los valores secretos, con [Secret Manager](xref:security/app-secrets). Para este ejemplo, siga estos pasos:

1. Inicializar el proyecto para el almacenamiento secreto según las instrucciones de [Habilitar almacenamiento secreto](xref:security/app-secrets#enable-secret-storage).
1. Almacene la configuración confidencial en el almacén secreto local con las claves `Authentication:Facebook:AppId` secretas y: `Authentication:Facebook:AppSecret`

    ```dotnetcli
    dotnet user-secrets set "Authentication:Facebook:AppId" "<app-id>"
    dotnet user-secrets set "Authentication:Facebook:AppSecret" "<app-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-facebook-authentication"></a>Configurar la autenticación de Facebook

Agrega el servicio `ConfigureServices` de Facebook en el método del archivo *Startup.cs:*

```csharp
services.AddAuthentication().AddFacebook(facebookOptions =>
{
    facebookOptions.AppId = Configuration["Authentication:Facebook:AppId"];
    facebookOptions.AppSecret = Configuration["Authentication:Facebook:AppSecret"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

## <a name="sign-in-with-facebook"></a>Inicia sesión con Facebook

* Ejecute la aplicación y seleccione **Iniciar sesión**. 
* En **Usar otro servicio para iniciar sesión.**, seleccione Facebook.
* Se le redirigirá a **Facebook** para la autenticación.
* Introduce tus credenciales de Facebook.
* Se le redirigirá de nuevo a su sitio donde puede establecer su correo electrónico.

Ahora ha iniciado sesión con sus credenciales de Facebook:

<a name="react"></a>

## <a name="react-to-cancel-authorize-external-sign-in"></a>React para cancelar autorizar el inicio de sesión externo

<xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.AccessDeniedPath>puede proporcionar una ruta de acceso de redirección al agente de usuario cuando el usuario no aprueba la demanda de autorización solicitada.

El código siguiente `AccessDeniedPath` `"/AccessDeniedPathInfo"`establece en :

[!code-csharp[](~/security/authentication/social/social-code/StartupAccessDeniedPath.cs?name=snippetFB)]

Recomendamos `AccessDeniedPath` que la página contenga la siguiente información:

*  Se canceló la autenticación remota.
* Esta aplicación requiere autenticación.
* Para intentar iniciar sesión de nuevo, seleccione el vínculo Inicio de sesión.

### <a name="test-accessdeniedpath"></a>Probar AccessDeniedPath

* Navegue a [facebook.com](https://www.facebook.com/)
* Si ha iniciado sesión, debe cerrar la sesión.
* Ejecute la aplicación y seleccione Inicio de sesión de Facebook.
* Seleccione **No ahora**. Se le redirigirá a `AccessDeniedPath` la página especificada.

<!-- End of React  -->
[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

Consulta la referencia de la API [de FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) para obtener más información sobre las opciones de configuración admitidas por la autenticación de Facebook. Las opciones de configuración se pueden utilizar para:

* Solicitar información diferente sobre el usuario.
* Agregue argumentos de cadena de consulta para personalizar la experiencia de inicio de sesión.

## <a name="troubleshooting"></a>Solución de problemas

* **Sólo ASP.NET Core 2.x:** Si Identity no está `services.AddIdentity` configurado `ConfigureServices`llamando a , al intentar autenticar se producirá *ArgumentException: se debe proporcionar la opción 'SignInScheme'.* La plantilla de proyecto utilizada en este tutorial garantiza que esto se realiza.
* Si la base de datos del sitio no se ha creado aplicando la migración inicial, se produce un error en *una operación* de base de datos al procesar el error de solicitud. Puntee **Aplicar migraciones** para crear la base de datos y actualizar la actualización para continuar más allá del error.

## <a name="next-steps"></a>Pasos siguientes

* Este artículo muestra cómo puedes autenticarte con Facebook. Puede seguir un enfoque similar para autenticarse con otros proveedores enumerados en la [página anterior.](xref:security/authentication/social/index)

* Una vez que publique el sitio web en `AppSecret` la aplicación web de Azure, debe restablecerlo en el portal para desarrolladores de Facebook.

* Establezca `Authentication:Facebook:AppId` la `Authentication:Facebook:AppSecret` configuración y como aplicación en Azure Portal. El sistema de configuración está configurado para leer las claves de las variables de entorno.
