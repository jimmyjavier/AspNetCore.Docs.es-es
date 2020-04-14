---
title: Configuración de inicio de sesión externo de Twitter con ASP.NET Core
author: rick-anderson
description: En este tutorial se muestra la integración de la autenticación de usuario de la cuenta de Twitter en una aplicación ASP.NET Core existente.
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/twitter-logins
ms.openlocfilehash: 1f5d667e905e49ae05f5aa31bd5b69ad126f6e28
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277293"
---
# <a name="twitter-external-sign-in-setup-with-aspnet-core"></a>Configuración de inicio de sesión externo de Twitter con ASP.NET Core

Por [Valeriy Novytskyy](https://github.com/01binary) y [Rick Anderson](https://twitter.com/RickAndMSFT)

En este ejemplo se muestra cómo permitir que los usuarios [inicien sesión con su cuenta](https://dev.twitter.com/web/sign-in/desktop-browser) de Twitter mediante un ejemplo ASP.NET proyecto Core 3.0 creado en la [página anterior.](xref:security/authentication/social/index)

## <a name="create-the-app-in-twitter"></a>Crea la aplicación en Twitter

* Agregue el paquete NuGet [Microsoft.AspNetCore.Authentication.Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) al proyecto.

* Navegue [https://apps.twitter.com/](https://apps.twitter.com/) hasta e inicie sesión. Si aún no tienes una cuenta de Twitter, usa el enlace **[Registrarse ahora](https://twitter.com/signup)** para crear una.

* Seleccione **Crear una aplicación**. Rellene el nombre de la **aplicación,** la **descripción** de la aplicación y el URI público del sitio **web** (esto puede ser temporal hasta que registre el nombre de dominio):

* Marca la casilla situada junto a **Habilitar inicio de sesión con Twitter**

* Microsoft.AspNetCore.Identity requiere que los usuarios tengan una dirección de correo electrónico de forma predeterminada. Vaya a la pestaña **Permisos,** haga clic en el botón **Editar** y active la casilla situada junto a **Solicitar dirección de correo electrónico de los usuarios**.

* Escriba el URI `/signin-twitter` de desarrollo anexado en el campo `https://webapp128.azurewebsites.net/signin-twitter`URL de **devolución** de llamada (por ejemplo: ). El esquema de autenticación de Twitter configurado más `/signin-twitter` adelante en este ejemplo controlará automáticamente las solicitudes en ruta para implementar el flujo de OAuth.

  > [!NOTE]
  > El segmento `/signin-twitter` URI se establece como la devolución de llamada predeterminada del proveedor de autenticación de Twitter. Puede cambiar el URI de devolución de llamada predeterminado al configurar el middleware de autenticación de Twitter a través de la propiedad [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) heredada de la clase [TwitterOptions.](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions)

* Rellene el resto del formulario y seleccione **Crear**. Se muestran los nuevos detalles de la aplicación:

## <a name="store-the-twitter-consumer-api-key-and-secret"></a>Almacene la clave y el secreto de la API del consumidor de Twitter

Almacene la configuración confidencial, como la clave de API de consumidor de Twitter y el secreto con [Secret Manager](xref:security/app-secrets). Para este ejemplo, siga estos pasos:

1. Inicializar el proyecto para el almacenamiento secreto según las instrucciones de [Habilitar almacenamiento secreto](xref:security/app-secrets#enable-secret-storage).
1. Almacene la configuración confidencial en el almacén secreto local con las claves `Authentication:Twitter:ConsumerKey` secretas y: `Authentication:Twitter:ConsumerSecret`

    ```dotnetcli
    dotnet user-secrets set "Authentication:Twitter:ConsumerAPIKey" "<consumer-api-key>"
    dotnet user-secrets set "Authentication:Twitter:ConsumerSecret" "<consumer-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

Estos tokens se pueden encontrar en la pestaña **Claves y tokens** de acceso después de crear una nueva aplicación de Twitter:

## <a name="configure-twitter-authentication"></a>Configurar la autenticación de Twitter

Agrega el servicio `ConfigureServices` de Twitter en el método en *Startup.cs* archivo:

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupTwitter3x.cs?name=snippet&highlight=10-15)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

Consulte la referencia de la API [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) para obtener más información sobre las opciones de configuración admitidas por la autenticación de Twitter. Esto se puede utilizar para solicitar información diferente sobre el usuario.

## <a name="sign-in-with-twitter"></a>Inicia sesión con Twitter

Ejecute la aplicación y seleccione **Iniciar sesión**. Aparece una opción para iniciar sesión con Twitter:

Al hacer clic en **Twitter** redirige a Twitter para la autenticación:

Después de introducir sus credenciales de Twitter, se le redirigirá de nuevo al sitio web donde puede establecer su correo electrónico.

Ahora ha iniciado sesión con sus credenciales de Twitter:

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

<!-- 
### React to cancel Authorize External sign-in
Twitter doesn't support AccessDeniedPath
Rather in the twitter setup, you can provide an External sign-in homepage. The external sign-in homepage doesn't support localhost. Tested with https://cors3.azurewebsites.net/ and that works.
-->

## <a name="troubleshooting"></a>Solución de problemas

* **Sólo ASP.NET Core 2.x:** Si Identity no está `services.AddIdentity` configurado `ConfigureServices`llamando a , al intentar autenticar se producirá *ArgumentException: se debe proporcionar la opción 'SignInScheme'.* La plantilla de proyecto utilizada en este ejemplo garantiza que esto se realiza.
* Si la base de datos del sitio no se ha creado aplicando la migración inicial, obtendrá un error en la operación de base de datos al procesar el error de *solicitud.* Puntee **Aplicar migraciones** para crear la base de datos y actualizar la actualización para continuar más allá del error.

## <a name="next-steps"></a>Pasos siguientes

* Este artículo muestra cómo puedes autenticarte con Twitter. Puede seguir un enfoque similar para autenticarse con otros proveedores enumerados en la [página anterior.](xref:security/authentication/social/index)

* Una vez que publique el sitio web en `ConsumerSecret` la aplicación web de Azure, debe restablecerlo en el portal para desarrolladores de Twitter.

* Establezca `Authentication:Twitter:ConsumerKey` la `Authentication:Twitter:ConsumerSecret` configuración y como aplicación en Azure Portal. El sistema de configuración está configurado para leer las claves de las variables de entorno.
