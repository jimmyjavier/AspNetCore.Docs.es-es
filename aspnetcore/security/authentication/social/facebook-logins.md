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
# <a name="facebook-external-login-setup-in-aspnet-core"></a><span data-ttu-id="574b9-103">Configuración de inicio de sesión externo de Facebook en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="574b9-103">Facebook external login setup in ASP.NET Core</span></span>

<span data-ttu-id="574b9-104">Por [Valeriy Novytskyy](https://github.com/01binary) y [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="574b9-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<!-- per @rick-anderson and scott addie, don't update images. Remove images and point the customer to the FB set up page. FB needs to maintain  instructions to get key and secret.
-->

<span data-ttu-id="574b9-105">En este tutorial con ejemplos de código se muestra cómo permitir que los usuarios inicien sesión con su cuenta de Facebook mediante un proyecto de ejemplo ASP.NET Core 3.0 creado en la [página anterior.](xref:security/authentication/social/index)</span><span class="sxs-lookup"><span data-stu-id="574b9-105">This tutorial with code examples shows how to enable your users to sign in with their Facebook account using a sample ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span> <span data-ttu-id="574b9-106">Comenzamos creando un ID de aplicación de Facebook siguiendo los [pasos oficiales.](https://developers.facebook.com)</span><span class="sxs-lookup"><span data-stu-id="574b9-106">We start by creating a Facebook App ID by following the [official steps](https://developers.facebook.com).</span></span>

## <a name="create-the-app-in-facebook"></a><span data-ttu-id="574b9-107">Crea la aplicación en Facebook</span><span class="sxs-lookup"><span data-stu-id="574b9-107">Create the app in Facebook</span></span>

* <span data-ttu-id="574b9-108">Agregue el paquete NuGet [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) al proyecto.</span><span class="sxs-lookup"><span data-stu-id="574b9-108">Add the [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) NuGet package to the project.</span></span>

* <span data-ttu-id="574b9-109">Ve a la página de la [aplicación Desarrolladores](https://developers.facebook.com/apps/) de Facebook e inicia sesión.</span><span class="sxs-lookup"><span data-stu-id="574b9-109">Navigate to the [Facebook Developers app](https://developers.facebook.com/apps/) page and sign in.</span></span> <span data-ttu-id="574b9-110">Si aún no tienes una cuenta de Facebook, usa el enlace **Registrarse para Facebook** en la página de inicio de sesión para crear una.</span><span class="sxs-lookup"><span data-stu-id="574b9-110">If you don't already have a Facebook account, use the **Sign up for Facebook** link on the login page to create one.</span></span>  <span data-ttu-id="574b9-111">Una vez que tengas una cuenta de Facebook, sigue las instrucciones para registrarte como desarrollador de Facebook.</span><span class="sxs-lookup"><span data-stu-id="574b9-111">Once you have a Facebook account, follow the instructions to register as a Facebook Developer.</span></span>

* <span data-ttu-id="574b9-112">En el menú **Mis aplicaciones,** seleccione **Crear aplicación** para crear un nuevo identificador de aplicación.</span><span class="sxs-lookup"><span data-stu-id="574b9-112">From the **My Apps** menu select **Create App** to create a new App ID.</span></span>

   ![Portal de Facebook para desarrolladores abierto en Microsoft Edge](index/_static/FBMyApps.png)

* <span data-ttu-id="574b9-114">Rellena el formulario y toca el botón Crear ID de **aplicación.**</span><span class="sxs-lookup"><span data-stu-id="574b9-114">Fill out the form and tap the **Create App ID** button.</span></span>

  ![Crear un nuevo formulario de ID de aplicación](index/_static/FBNewAppId.png)

* <span data-ttu-id="574b9-116">En la nueva tarjeta De la aplicación, seleccione **Agregar un producto**.</span><span class="sxs-lookup"><span data-stu-id="574b9-116">On the new App card, select **Add a Product**.</span></span>  <span data-ttu-id="574b9-117">En la tarjeta de inicio de sesión de **Facebook,** haz clic en **Configurar**</span><span class="sxs-lookup"><span data-stu-id="574b9-117">On the **Facebook Login** card, click **Set Up**</span></span> 

  ![Página de configuración del producto](index/_static/FBProductSetup.png)

* <span data-ttu-id="574b9-119">El asistente **de inicio rápido se** inicia con Elegir una **plataforma** como primera página.</span><span class="sxs-lookup"><span data-stu-id="574b9-119">The **Quickstart** wizard launches with **Choose a Platform** as the first page.</span></span> <span data-ttu-id="574b9-120">Omita el asistente por ahora haciendo clic en el enlace **Configuración** de inicio de sesión de **FaceBook** en el menú de la parte inferior izquierda:</span><span class="sxs-lookup"><span data-stu-id="574b9-120">Bypass the wizard for now by clicking the **FaceBook Login** **Settings** link in the menu on the lower left:</span></span>

  ![Omitir inicio rápido](index/_static/FBSkipQuickStart.png)

* <span data-ttu-id="574b9-122">Se le presenta la página Configuración de OAuth de **cliente:**</span><span class="sxs-lookup"><span data-stu-id="574b9-122">You are presented with the **Client OAuth Settings** page:</span></span>

  ![Página Configuración de OAuth de cliente](index/_static/FBOAuthSetup.png)

* <span data-ttu-id="574b9-124">Introduzca el URI de desarrollo con */signin-facebook* anexado al campo `https://localhost:44320/signin-facebook`URI de redirección de **OAuth válido** (por ejemplo: ).</span><span class="sxs-lookup"><span data-stu-id="574b9-124">Enter your development URI with */signin-facebook* appended into the **Valid OAuth Redirect URIs** field (for example: `https://localhost:44320/signin-facebook`).</span></span> <span data-ttu-id="574b9-125">La autenticación de Facebook configurada más adelante en este tutorial controlará automáticamente las solicitudes en la ruta */signin-facebook* para implementar el flujo de OAuth.</span><span class="sxs-lookup"><span data-stu-id="574b9-125">The Facebook authentication configured later in this tutorial will automatically handle requests at */signin-facebook* route to implement the OAuth flow.</span></span>

> [!NOTE]
> <span data-ttu-id="574b9-126">El URI */signin-facebook* se establece como la devolución de llamada predeterminada del proveedor de autenticación de Facebook.</span><span class="sxs-lookup"><span data-stu-id="574b9-126">The URI */signin-facebook* is set as the default callback of the Facebook authentication provider.</span></span> <span data-ttu-id="574b9-127">Puede cambiar el URI de devolución de llamada predeterminado al configurar el middleware de autenticación de Facebook a través de la propiedad [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) heredada de la clase [FacebookOptions.](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions)</span><span class="sxs-lookup"><span data-stu-id="574b9-127">You can change the default callback URI while configuring the Facebook authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [FacebookOptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions) class.</span></span>

* <span data-ttu-id="574b9-128">Haga clic en **Guardar cambios**.</span><span class="sxs-lookup"><span data-stu-id="574b9-128">Click **Save Changes**.</span></span>

* <span data-ttu-id="574b9-129">Haga clic en el vínculo **Configuración** > **básica** en el panel de navegación izquierdo.</span><span class="sxs-lookup"><span data-stu-id="574b9-129">Click **Settings** > **Basic** link in the left navigation.</span></span>

  <span data-ttu-id="574b9-130">En esta página, anote `App ID` su `App Secret`archivo .</span><span class="sxs-lookup"><span data-stu-id="574b9-130">On this page, make a note of your `App ID` and your `App Secret`.</span></span> <span data-ttu-id="574b9-131">Agregará ambos a la aplicación ASP.NET Core en la siguiente sección:</span><span class="sxs-lookup"><span data-stu-id="574b9-131">You will add both into your ASP.NET Core application in the next section:</span></span>

* <span data-ttu-id="574b9-132">Al implementar el sitio, debe volver a visitar la página de configuración de inicio de **sesión** de Facebook y registrar un nuevo URI público.</span><span class="sxs-lookup"><span data-stu-id="574b9-132">When deploying the site you need to revisit the **Facebook Login** setup page and register a new public URI.</span></span>

## <a name="store-the-facebook-app-id-and-secret"></a><span data-ttu-id="574b9-133">Almacena el ID y el secreto de la aplicación de Facebook</span><span class="sxs-lookup"><span data-stu-id="574b9-133">Store the Facebook app ID and secret</span></span>

<span data-ttu-id="574b9-134">Almacene ajustes confidenciales, como el identificador de la aplicación de Facebook y los valores secretos, con [Secret Manager](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="574b9-134">Store sensitive settings such as the Facebook app ID and secret values with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="574b9-135">Para este ejemplo, siga estos pasos:</span><span class="sxs-lookup"><span data-stu-id="574b9-135">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="574b9-136">Inicializar el proyecto para el almacenamiento secreto según las instrucciones de [Habilitar almacenamiento secreto](xref:security/app-secrets#enable-secret-storage).</span><span class="sxs-lookup"><span data-stu-id="574b9-136">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="574b9-137">Almacene la configuración confidencial en el almacén secreto local con las claves `Authentication:Facebook:AppId` secretas y: `Authentication:Facebook:AppSecret`</span><span class="sxs-lookup"><span data-stu-id="574b9-137">Store the sensitive settings in the local secret store with the secret keys `Authentication:Facebook:AppId` and `Authentication:Facebook:AppSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Facebook:AppId" "<app-id>"
    dotnet user-secrets set "Authentication:Facebook:AppSecret" "<app-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-facebook-authentication"></a><span data-ttu-id="574b9-138">Configurar la autenticación de Facebook</span><span class="sxs-lookup"><span data-stu-id="574b9-138">Configure Facebook Authentication</span></span>

<span data-ttu-id="574b9-139">Agrega el servicio `ConfigureServices` de Facebook en el método del archivo *Startup.cs:*</span><span class="sxs-lookup"><span data-stu-id="574b9-139">Add the Facebook service in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

```csharp
services.AddAuthentication().AddFacebook(facebookOptions =>
{
    facebookOptions.AppId = Configuration["Authentication:Facebook:AppId"];
    facebookOptions.AppSecret = Configuration["Authentication:Facebook:AppSecret"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

## <a name="sign-in-with-facebook"></a><span data-ttu-id="574b9-140">Inicia sesión con Facebook</span><span class="sxs-lookup"><span data-stu-id="574b9-140">Sign in with Facebook</span></span>

* <span data-ttu-id="574b9-141">Ejecute la aplicación y seleccione **Iniciar sesión**.</span><span class="sxs-lookup"><span data-stu-id="574b9-141">Run the app and select **Log in**.</span></span> 
* <span data-ttu-id="574b9-142">En **Usar otro servicio para iniciar sesión.**, seleccione Facebook.</span><span class="sxs-lookup"><span data-stu-id="574b9-142">Under **Use another service to log in.**, select Facebook.</span></span>
* <span data-ttu-id="574b9-143">Se le redirigirá a **Facebook** para la autenticación.</span><span class="sxs-lookup"><span data-stu-id="574b9-143">You are redirected to **Facebook** for authentication.</span></span>
* <span data-ttu-id="574b9-144">Introduce tus credenciales de Facebook.</span><span class="sxs-lookup"><span data-stu-id="574b9-144">Enter your Facebook credentials.</span></span>
* <span data-ttu-id="574b9-145">Se le redirigirá de nuevo a su sitio donde puede establecer su correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="574b9-145">You are redirected back to your site where you can set your email.</span></span>

<span data-ttu-id="574b9-146">Ahora ha iniciado sesión con sus credenciales de Facebook:</span><span class="sxs-lookup"><span data-stu-id="574b9-146">You are now logged in using your Facebook credentials:</span></span>

<a name="react"></a>

## <a name="react-to-cancel-authorize-external-sign-in"></a><span data-ttu-id="574b9-147">React para cancelar autorizar el inicio de sesión externo</span><span class="sxs-lookup"><span data-stu-id="574b9-147">React to cancel authorize external sign-in</span></span>

<span data-ttu-id="574b9-148"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.AccessDeniedPath>puede proporcionar una ruta de acceso de redirección al agente de usuario cuando el usuario no aprueba la demanda de autorización solicitada.</span><span class="sxs-lookup"><span data-stu-id="574b9-148"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.AccessDeniedPath> can provide a redirect path to the user agent when the user doesn't approve the requested authorization demand.</span></span>

<span data-ttu-id="574b9-149">El código siguiente `AccessDeniedPath` `"/AccessDeniedPathInfo"`establece en :</span><span class="sxs-lookup"><span data-stu-id="574b9-149">The following code sets the `AccessDeniedPath` to `"/AccessDeniedPathInfo"`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/StartupAccessDeniedPath.cs?name=snippetFB)]

<span data-ttu-id="574b9-150">Recomendamos `AccessDeniedPath` que la página contenga la siguiente información:</span><span class="sxs-lookup"><span data-stu-id="574b9-150">We recommend the `AccessDeniedPath` page contain the following information:</span></span>

*  <span data-ttu-id="574b9-151">Se canceló la autenticación remota.</span><span class="sxs-lookup"><span data-stu-id="574b9-151">Remote authentication was canceled.</span></span>
* <span data-ttu-id="574b9-152">Esta aplicación requiere autenticación.</span><span class="sxs-lookup"><span data-stu-id="574b9-152">This app requires authentication.</span></span>
* <span data-ttu-id="574b9-153">Para intentar iniciar sesión de nuevo, seleccione el vínculo Inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="574b9-153">To try sign-in again, select the Login link.</span></span>

### <a name="test-accessdeniedpath"></a><span data-ttu-id="574b9-154">Probar AccessDeniedPath</span><span class="sxs-lookup"><span data-stu-id="574b9-154">Test AccessDeniedPath</span></span>

* <span data-ttu-id="574b9-155">Navegue a [facebook.com](https://www.facebook.com/)</span><span class="sxs-lookup"><span data-stu-id="574b9-155">Navigate to [facebook.com](https://www.facebook.com/)</span></span>
* <span data-ttu-id="574b9-156">Si ha iniciado sesión, debe cerrar la sesión.</span><span class="sxs-lookup"><span data-stu-id="574b9-156">If you are signed in, you must sign out.</span></span>
* <span data-ttu-id="574b9-157">Ejecute la aplicación y seleccione Inicio de sesión de Facebook.</span><span class="sxs-lookup"><span data-stu-id="574b9-157">Run the app and select Facebook sign-in.</span></span>
* <span data-ttu-id="574b9-158">Seleccione **No ahora**.</span><span class="sxs-lookup"><span data-stu-id="574b9-158">Select **Not now**.</span></span> <span data-ttu-id="574b9-159">Se le redirigirá a `AccessDeniedPath` la página especificada.</span><span class="sxs-lookup"><span data-stu-id="574b9-159">You are redirected to the specified `AccessDeniedPath` page.</span></span>

<!-- End of React  -->
[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="574b9-160">Consulta la referencia de la API [de FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) para obtener más información sobre las opciones de configuración admitidas por la autenticación de Facebook.</span><span class="sxs-lookup"><span data-stu-id="574b9-160">See the [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) API reference for more information on configuration options supported by Facebook authentication.</span></span> <span data-ttu-id="574b9-161">Las opciones de configuración se pueden utilizar para:</span><span class="sxs-lookup"><span data-stu-id="574b9-161">Configuration options can be used to:</span></span>

* <span data-ttu-id="574b9-162">Solicitar información diferente sobre el usuario.</span><span class="sxs-lookup"><span data-stu-id="574b9-162">Request different information about the user.</span></span>
* <span data-ttu-id="574b9-163">Agregue argumentos de cadena de consulta para personalizar la experiencia de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="574b9-163">Add query string arguments to customize the login experience.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="574b9-164">Solución de problemas</span><span class="sxs-lookup"><span data-stu-id="574b9-164">Troubleshooting</span></span>

* <span data-ttu-id="574b9-165">**Sólo ASP.NET Core 2.x:** Si Identity no está `services.AddIdentity` configurado `ConfigureServices`llamando a , al intentar autenticar se producirá *ArgumentException: se debe proporcionar la opción 'SignInScheme'.*</span><span class="sxs-lookup"><span data-stu-id="574b9-165">**ASP.NET Core 2.x only:** If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="574b9-166">La plantilla de proyecto utilizada en este tutorial garantiza que esto se realiza.</span><span class="sxs-lookup"><span data-stu-id="574b9-166">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="574b9-167">Si la base de datos del sitio no se ha creado aplicando la migración inicial, se produce un error en *una operación* de base de datos al procesar el error de solicitud.</span><span class="sxs-lookup"><span data-stu-id="574b9-167">If the site database has not been created by applying the initial migration, you get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="574b9-168">Puntee **Aplicar migraciones** para crear la base de datos y actualizar la actualización para continuar más allá del error.</span><span class="sxs-lookup"><span data-stu-id="574b9-168">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="574b9-169">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="574b9-169">Next steps</span></span>

* <span data-ttu-id="574b9-170">Este artículo muestra cómo puedes autenticarte con Facebook.</span><span class="sxs-lookup"><span data-stu-id="574b9-170">This article showed how you can authenticate with Facebook.</span></span> <span data-ttu-id="574b9-171">Puede seguir un enfoque similar para autenticarse con otros proveedores enumerados en la [página anterior.](xref:security/authentication/social/index)</span><span class="sxs-lookup"><span data-stu-id="574b9-171">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="574b9-172">Una vez que publique el sitio web en `AppSecret` la aplicación web de Azure, debe restablecerlo en el portal para desarrolladores de Facebook.</span><span class="sxs-lookup"><span data-stu-id="574b9-172">Once you publish your web site to Azure web app, you should reset the `AppSecret` in the Facebook developer portal.</span></span>

* <span data-ttu-id="574b9-173">Establezca `Authentication:Facebook:AppId` la `Authentication:Facebook:AppSecret` configuración y como aplicación en Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="574b9-173">Set the `Authentication:Facebook:AppId` and `Authentication:Facebook:AppSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="574b9-174">El sistema de configuración está configurado para leer las claves de las variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="574b9-174">The configuration system is set up to read keys from environment variables.</span></span>
