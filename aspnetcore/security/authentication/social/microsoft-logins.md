---
title: Configuración de inicio de sesión externo de la cuenta Microsoft con ASP.NET Core
author: rick-anderson
description: En este ejemplo se muestra la integración de la autenticación de usuario de cuenta Microsoft en una aplicación ASP.NET Core existente.
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/microsoft-logins
ms.openlocfilehash: 32315267e0672b0747917228f08591a15e4449f8
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277254"
---
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a>Configuración de inicio de sesión externo de la cuenta Microsoft con ASP.NET Core

Por [Valeriy Novytskyy](https://github.com/01binary) y [Rick Anderson](https://twitter.com/RickAndMSFT)

En este ejemplo se muestra cómo permitir que los usuarios inicien sesión con su cuenta Microsoft mediante el proyecto ASP.NET Core 3.0 creado en la [página anterior.](xref:security/authentication/social/index)

## <a name="create-the-app-in-microsoft-developer-portal"></a>Cree la aplicación en Microsoft Developer Portal

* Agregue el paquete NuGet [Microsoft.AspNetCore.Authentication.MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) al proyecto.
* Vaya a la página [Azure Portal - Registros](https://go.microsoft.com/fwlink/?linkid=2083908) de aplicaciones y cree o inicie sesión en una cuenta Microsoft:

Si no tiene una cuenta Microsoft, seleccione **Crear una**. Después de iniciar sesión, se le redirigirá a la página **Registros** de aplicaciones:

* Seleccione **Nuevo registro**
* Escriba un **nombre**.
* Seleccione una opción para **Tipos de cuenta compatibles**.  <!-- Accounts for any org work with MS domain accounts. Most folks probably want the last option, personal MS accounts. It took 24 hours after setting this up for the keys to work -->
* En **URI de redirección** `/signin-microsoft` , escriba la dirección URL de desarrollo con anexado. Por ejemplo, `https://localhost:5001/signin-microsoft`. El esquema de autenticación de Microsoft configurado más `/signin-microsoft` adelante en este ejemplo controlará automáticamente las solicitudes en ruta para implementar el flujo de OAuth.
* Seleccione **Registrar**.

### <a name="create-client-secret"></a>Creación de un secreto de cliente

* En el panel izquierdo, seleccione **Certificados y secretos**.
* En **Secretos de cliente**, seleccione Nuevo secreto de **cliente**

  * Agregue una descripción para el secreto de cliente.
  * Seleccione el botón **Agregar.**

* En **Secretos de cliente**, copie el valor del secreto de cliente.

El segmento `/signin-microsoft` URI se establece como la devolución de llamada predeterminada del proveedor de autenticación de Microsoft. Puede cambiar el URI de devolución de llamada predeterminado al configurar el middleware de autenticación de Microsoft a través de la propiedad [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) heredada de la clase [MicrosoftAccountOptions.](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions)

## <a name="store-the-microsoft-client-id-and-secret"></a>Almacene el identificador de cliente y el secreto de Microsoft

Almacene la configuración confidencial, como el identificador de cliente de Microsoft y los valores secretos con [Secret Manager](xref:security/app-secrets). Para este ejemplo, siga estos pasos:

1. Inicializar el proyecto para el almacenamiento secreto según las instrucciones de [Habilitar almacenamiento secreto](xref:security/app-secrets#enable-secret-storage).
1. Almacene la configuración confidencial en el almacén secreto local con las claves `Authentication:Microsoft:ClientId` secretas y: `Authentication:Microsoft:ClientSecret`

    ```dotnetcli
    dotnet user-secrets set "Authentication:Microsoft:ClientId" "<client-id>"
    dotnet user-secrets set "Authentication:Microsoft:ClientSecret" "<client-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-microsoft-account-authentication"></a>Configurar la autenticación de cuenta Microsoft

Agregue el servicio Cuenta `Startup.ConfigureServices`Microsoft a:

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupMS3x.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

Para obtener más información acerca de las opciones de configuración admitidas por la autenticación de cuenta Microsoft, consulte la referencia de la API [MicrosoftAccountOptions.](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) Esto se puede utilizar para solicitar información diferente sobre el usuario.

## <a name="sign-in-with-microsoft-account"></a>Inicie sesión con la cuenta Microsoft

Ejecute la aplicación y haga clic **en Iniciar sesión**. Aparece una opción para iniciar sesión con Microsoft. Al hacer clic en Microsoft, se le redirige a Microsoft para la autenticación. Después de iniciar sesión con tu cuenta Microsoft, se te pedirá que dejes que la aplicación acceda a tu información:

Toque **Sí** y se le redirigirá de nuevo al sitio web donde puede establecer su correo electrónico.

Ahora ha iniciado sesión con sus credenciales de Microsoft:

[!INCLUDE[](includes/chain-auth-providers.md)]

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a>Solución de problemas

* Si el proveedor de la cuenta Microsoft le redirige a una página de error `#` de inicio de sesión, tenga en cuenta los parámetros de cadena de consulta de título y descripción del error que siguen directamente al (hashtag) en el Uri.

  Aunque el mensaje de error parece indicar un problema con la autenticación de Microsoft, la causa más común es que el Uri de la aplicación no coincide con ninguno de los URI de **redirección** especificados para la plataforma **web.**
* Si Identity no está `services.AddIdentity` configurado `ConfigureServices`llamando a , al intentar autenticar se producirá *ArgumentException: se debe proporcionar la opción 'SignInScheme'.* La plantilla de proyecto utilizada en este ejemplo garantiza que esto se realiza.
* Si la base de datos del sitio no se ha creado aplicando la migración inicial, obtendrá un error en la operación de base de datos al procesar el error de *solicitud.* Puntee **Aplicar migraciones** para crear la base de datos y actualizar la actualización para continuar más allá del error.

## <a name="next-steps"></a>Pasos siguientes

* En este artículo se muestra cómo puede autenticarse con Microsoft. Puede seguir un enfoque similar para autenticarse con otros proveedores enumerados en la [página anterior.](xref:security/authentication/social/index)

* Una vez que publique el sitio web en la aplicación web de Azure, cree un nuevo secreto de cliente en el Portal para desarrolladores de Microsoft.

* Establezca `Authentication:Microsoft:ClientId` la `Authentication:Microsoft:ClientSecret` configuración y como aplicación en Azure Portal. El sistema de configuración está configurado para leer las claves de las variables de entorno.
