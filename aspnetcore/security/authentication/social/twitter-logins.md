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
# <a name="twitter-external-sign-in-setup-with-aspnet-core"></a><span data-ttu-id="a7a01-103">Configuración de inicio de sesión externo de Twitter con ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a7a01-103">Twitter external sign-in setup with ASP.NET Core</span></span>

<span data-ttu-id="a7a01-104">Por [Valeriy Novytskyy](https://github.com/01binary) y [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a7a01-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="a7a01-105">En este ejemplo se muestra cómo permitir que los usuarios [inicien sesión con su cuenta](https://dev.twitter.com/web/sign-in/desktop-browser) de Twitter mediante un ejemplo ASP.NET proyecto Core 3.0 creado en la [página anterior.](xref:security/authentication/social/index)</span><span class="sxs-lookup"><span data-stu-id="a7a01-105">This sample shows how to enable users to [sign in with their Twitter account](https://dev.twitter.com/web/sign-in/desktop-browser) using a sample ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-twitter"></a><span data-ttu-id="a7a01-106">Crea la aplicación en Twitter</span><span class="sxs-lookup"><span data-stu-id="a7a01-106">Create the app in Twitter</span></span>

* <span data-ttu-id="a7a01-107">Agregue el paquete NuGet [Microsoft.AspNetCore.Authentication.Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) al proyecto.</span><span class="sxs-lookup"><span data-stu-id="a7a01-107">Add the [Microsoft.AspNetCore.Authentication.Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) NuGet package to the project.</span></span>

* <span data-ttu-id="a7a01-108">Navegue [https://apps.twitter.com/](https://apps.twitter.com/) hasta e inicie sesión.</span><span class="sxs-lookup"><span data-stu-id="a7a01-108">Navigate to [https://apps.twitter.com/](https://apps.twitter.com/) and sign in.</span></span> <span data-ttu-id="a7a01-109">Si aún no tienes una cuenta de Twitter, usa el enlace **[Registrarse ahora](https://twitter.com/signup)** para crear una.</span><span class="sxs-lookup"><span data-stu-id="a7a01-109">If you don't already have a Twitter account, use the **[Sign up now](https://twitter.com/signup)** link to create one.</span></span>

* <span data-ttu-id="a7a01-110">Seleccione **Crear una aplicación**.</span><span class="sxs-lookup"><span data-stu-id="a7a01-110">Select **Create an app**.</span></span> <span data-ttu-id="a7a01-111">Rellene el nombre de la **aplicación,** la **descripción** de la aplicación y el URI público del sitio **web** (esto puede ser temporal hasta que registre el nombre de dominio):</span><span class="sxs-lookup"><span data-stu-id="a7a01-111">Fill out the **App name**, **Application description** and public **Website** URI (this can be temporary until you register the domain name):</span></span>

* <span data-ttu-id="a7a01-112">Marca la casilla situada junto a **Habilitar inicio de sesión con Twitter**</span><span class="sxs-lookup"><span data-stu-id="a7a01-112">Check the box next to **Enable Sign in with Twitter**</span></span>

* <span data-ttu-id="a7a01-113">Microsoft.AspNetCore.Identity requiere que los usuarios tengan una dirección de correo electrónico de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="a7a01-113">Microsoft.AspNetCore.Identity requires users to have an email address by default.</span></span> <span data-ttu-id="a7a01-114">Vaya a la pestaña **Permisos,** haga clic en el botón **Editar** y active la casilla situada junto a **Solicitar dirección de correo electrónico de los usuarios**.</span><span class="sxs-lookup"><span data-stu-id="a7a01-114">Go to the **Permissions** tab, click the **Edit** button and check the box next to **Request email address from users**.</span></span>

* <span data-ttu-id="a7a01-115">Escriba el URI `/signin-twitter` de desarrollo anexado en el campo `https://webapp128.azurewebsites.net/signin-twitter`URL de **devolución** de llamada (por ejemplo: ).</span><span class="sxs-lookup"><span data-stu-id="a7a01-115">Enter your development URI with `/signin-twitter` appended into the **Callback URLs** field (for example: `https://webapp128.azurewebsites.net/signin-twitter`).</span></span> <span data-ttu-id="a7a01-116">El esquema de autenticación de Twitter configurado más `/signin-twitter` adelante en este ejemplo controlará automáticamente las solicitudes en ruta para implementar el flujo de OAuth.</span><span class="sxs-lookup"><span data-stu-id="a7a01-116">The Twitter authentication scheme configured later in this sample will automatically handle requests at `/signin-twitter` route to implement the OAuth flow.</span></span>

  > [!NOTE]
  > <span data-ttu-id="a7a01-117">El segmento `/signin-twitter` URI se establece como la devolución de llamada predeterminada del proveedor de autenticación de Twitter.</span><span class="sxs-lookup"><span data-stu-id="a7a01-117">The URI segment `/signin-twitter` is set as the default callback of the Twitter authentication provider.</span></span> <span data-ttu-id="a7a01-118">Puede cambiar el URI de devolución de llamada predeterminado al configurar el middleware de autenticación de Twitter a través de la propiedad [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) heredada de la clase [TwitterOptions.](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions)</span><span class="sxs-lookup"><span data-stu-id="a7a01-118">You can change the default callback URI while configuring the Twitter authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) class.</span></span>

* <span data-ttu-id="a7a01-119">Rellene el resto del formulario y seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="a7a01-119">Fill out the rest of the form and select **Create**.</span></span> <span data-ttu-id="a7a01-120">Se muestran los nuevos detalles de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="a7a01-120">New application details are displayed:</span></span>

## <a name="store-the-twitter-consumer-api-key-and-secret"></a><span data-ttu-id="a7a01-121">Almacene la clave y el secreto de la API del consumidor de Twitter</span><span class="sxs-lookup"><span data-stu-id="a7a01-121">Store the Twitter consumer API key and secret</span></span>

<span data-ttu-id="a7a01-122">Almacene la configuración confidencial, como la clave de API de consumidor de Twitter y el secreto con [Secret Manager](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="a7a01-122">Store sensitive settings such as the Twitter consumer API key and secret with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="a7a01-123">Para este ejemplo, siga estos pasos:</span><span class="sxs-lookup"><span data-stu-id="a7a01-123">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="a7a01-124">Inicializar el proyecto para el almacenamiento secreto según las instrucciones de [Habilitar almacenamiento secreto](xref:security/app-secrets#enable-secret-storage).</span><span class="sxs-lookup"><span data-stu-id="a7a01-124">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="a7a01-125">Almacene la configuración confidencial en el almacén secreto local con las claves `Authentication:Twitter:ConsumerKey` secretas y: `Authentication:Twitter:ConsumerSecret`</span><span class="sxs-lookup"><span data-stu-id="a7a01-125">Store the sensitive settings in the local secret store with the secrets keys `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Twitter:ConsumerAPIKey" "<consumer-api-key>"
    dotnet user-secrets set "Authentication:Twitter:ConsumerSecret" "<consumer-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="a7a01-126">Estos tokens se pueden encontrar en la pestaña **Claves y tokens** de acceso después de crear una nueva aplicación de Twitter:</span><span class="sxs-lookup"><span data-stu-id="a7a01-126">These tokens can be found on the **Keys and Access Tokens** tab after creating a new Twitter application:</span></span>

## <a name="configure-twitter-authentication"></a><span data-ttu-id="a7a01-127">Configurar la autenticación de Twitter</span><span class="sxs-lookup"><span data-stu-id="a7a01-127">Configure Twitter Authentication</span></span>

<span data-ttu-id="a7a01-128">Agrega el servicio `ConfigureServices` de Twitter en el método en *Startup.cs* archivo:</span><span class="sxs-lookup"><span data-stu-id="a7a01-128">Add the Twitter service in the `ConfigureServices` method in *Startup.cs* file:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupTwitter3x.cs?name=snippet&highlight=10-15)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="a7a01-129">Consulte la referencia de la API [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) para obtener más información sobre las opciones de configuración admitidas por la autenticación de Twitter.</span><span class="sxs-lookup"><span data-stu-id="a7a01-129">See the [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) API reference for more information on configuration options supported by Twitter authentication.</span></span> <span data-ttu-id="a7a01-130">Esto se puede utilizar para solicitar información diferente sobre el usuario.</span><span class="sxs-lookup"><span data-stu-id="a7a01-130">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-twitter"></a><span data-ttu-id="a7a01-131">Inicia sesión con Twitter</span><span class="sxs-lookup"><span data-stu-id="a7a01-131">Sign in with Twitter</span></span>

<span data-ttu-id="a7a01-132">Ejecute la aplicación y seleccione **Iniciar sesión**.</span><span class="sxs-lookup"><span data-stu-id="a7a01-132">Run the app and select **Log in**.</span></span> <span data-ttu-id="a7a01-133">Aparece una opción para iniciar sesión con Twitter:</span><span class="sxs-lookup"><span data-stu-id="a7a01-133">An option to sign in with Twitter appears:</span></span>

<span data-ttu-id="a7a01-134">Al hacer clic en **Twitter** redirige a Twitter para la autenticación:</span><span class="sxs-lookup"><span data-stu-id="a7a01-134">Clicking on **Twitter** redirects to Twitter for authentication:</span></span>

<span data-ttu-id="a7a01-135">Después de introducir sus credenciales de Twitter, se le redirigirá de nuevo al sitio web donde puede establecer su correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="a7a01-135">After entering your Twitter credentials, you are redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="a7a01-136">Ahora ha iniciado sesión con sus credenciales de Twitter:</span><span class="sxs-lookup"><span data-stu-id="a7a01-136">You are now logged in using your Twitter credentials:</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

<!-- 
### React to cancel Authorize External sign-in
Twitter doesn't support AccessDeniedPath
Rather in the twitter setup, you can provide an External sign-in homepage. The external sign-in homepage doesn't support localhost. Tested with https://cors3.azurewebsites.net/ and that works.
-->

## <a name="troubleshooting"></a><span data-ttu-id="a7a01-137">Solución de problemas</span><span class="sxs-lookup"><span data-stu-id="a7a01-137">Troubleshooting</span></span>

* <span data-ttu-id="a7a01-138">**Sólo ASP.NET Core 2.x:** Si Identity no está `services.AddIdentity` configurado `ConfigureServices`llamando a , al intentar autenticar se producirá *ArgumentException: se debe proporcionar la opción 'SignInScheme'.*</span><span class="sxs-lookup"><span data-stu-id="a7a01-138">**ASP.NET Core 2.x only:** If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="a7a01-139">La plantilla de proyecto utilizada en este ejemplo garantiza que esto se realiza.</span><span class="sxs-lookup"><span data-stu-id="a7a01-139">The project template used in this sample ensures that this is done.</span></span>
* <span data-ttu-id="a7a01-140">Si la base de datos del sitio no se ha creado aplicando la migración inicial, obtendrá un error en la operación de base de datos al procesar el error de *solicitud.*</span><span class="sxs-lookup"><span data-stu-id="a7a01-140">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="a7a01-141">Puntee **Aplicar migraciones** para crear la base de datos y actualizar la actualización para continuar más allá del error.</span><span class="sxs-lookup"><span data-stu-id="a7a01-141">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="a7a01-142">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="a7a01-142">Next steps</span></span>

* <span data-ttu-id="a7a01-143">Este artículo muestra cómo puedes autenticarte con Twitter.</span><span class="sxs-lookup"><span data-stu-id="a7a01-143">This article showed how you can authenticate with Twitter.</span></span> <span data-ttu-id="a7a01-144">Puede seguir un enfoque similar para autenticarse con otros proveedores enumerados en la [página anterior.](xref:security/authentication/social/index)</span><span class="sxs-lookup"><span data-stu-id="a7a01-144">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="a7a01-145">Una vez que publique el sitio web en `ConsumerSecret` la aplicación web de Azure, debe restablecerlo en el portal para desarrolladores de Twitter.</span><span class="sxs-lookup"><span data-stu-id="a7a01-145">Once you publish your web site to Azure web app, you should reset the `ConsumerSecret` in the Twitter developer portal.</span></span>

* <span data-ttu-id="a7a01-146">Establezca `Authentication:Twitter:ConsumerKey` la `Authentication:Twitter:ConsumerSecret` configuración y como aplicación en Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="a7a01-146">Set the `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="a7a01-147">El sistema de configuración está configurado para leer las claves de las variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="a7a01-147">The configuration system is set up to read keys from environment variables.</span></span>
