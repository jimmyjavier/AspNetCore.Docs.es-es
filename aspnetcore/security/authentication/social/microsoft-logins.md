---
title: Configuración de inicio de sesión externo de la cuenta Microsoft con ASP.NET Core
author: rick-anderson
description: En este ejemplo se muestra la integración de la autenticación de usuario de cuenta Microsoft en una aplicación ASP.NET Core existente.
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/microsoft-logins
ms.openlocfilehash: 12c86456dad86731b86487a3a4dd725f36677002
ms.sourcegitcommit: f29a12486313e38e0163a643d8a97c8cecc7e871
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/14/2020
ms.locfileid: "81384046"
---
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a><span data-ttu-id="6376a-103">Configuración de inicio de sesión externo de la cuenta Microsoft con ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6376a-103">Microsoft Account external login setup with ASP.NET Core</span></span>

<span data-ttu-id="6376a-104">Por [Valeriy Novytskyy](https://github.com/01binary) y [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="6376a-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="6376a-105">En este ejemplo se muestra cómo permitir que los usuarios inicien sesión con su cuenta de trabajo, escuela o Microsoft personal mediante el proyecto ASP.NET Core 3.0 creado en la [página anterior.](xref:security/authentication/social/index)</span><span class="sxs-lookup"><span data-stu-id="6376a-105">This sample shows you how to enable users to sign in with their work, school, or personal Microsoft account using the ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-microsoft-developer-portal"></a><span data-ttu-id="6376a-106">Cree la aplicación en Microsoft Developer Portal</span><span class="sxs-lookup"><span data-stu-id="6376a-106">Create the app in Microsoft Developer Portal</span></span>

* <span data-ttu-id="6376a-107">Agregue el paquete NuGet [Microsoft.AspNetCore.Authentication.MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) al proyecto.</span><span class="sxs-lookup"><span data-stu-id="6376a-107">Add the [Microsoft.AspNetCore.Authentication.MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) NuGet package to the project.</span></span>
* <span data-ttu-id="6376a-108">Vaya a la página [Azure Portal - Registros](https://go.microsoft.com/fwlink/?linkid=2083908) de aplicaciones y cree o inicie sesión en una cuenta Microsoft:</span><span class="sxs-lookup"><span data-stu-id="6376a-108">Navigate to the [Azure portal - App registrations](https://go.microsoft.com/fwlink/?linkid=2083908) page and create or sign into a Microsoft account:</span></span>

<span data-ttu-id="6376a-109">Si no tiene una cuenta Microsoft, seleccione **Crear una**.</span><span class="sxs-lookup"><span data-stu-id="6376a-109">If you don't have a Microsoft account, select **Create one**.</span></span> <span data-ttu-id="6376a-110">Después de iniciar sesión, se le redirigirá a la página **Registros** de aplicaciones:</span><span class="sxs-lookup"><span data-stu-id="6376a-110">After signing in, you are redirected to the **App registrations** page:</span></span>

* <span data-ttu-id="6376a-111">Seleccione **Nuevo registro**</span><span class="sxs-lookup"><span data-stu-id="6376a-111">Select **New registration**</span></span>
* <span data-ttu-id="6376a-112">Escriba un **nombre**.</span><span class="sxs-lookup"><span data-stu-id="6376a-112">Enter a **Name**.</span></span>
* <span data-ttu-id="6376a-113">Seleccione una opción para **Tipos de cuenta compatibles**.</span><span class="sxs-lookup"><span data-stu-id="6376a-113">Select an option for **Supported account types**.</span></span>  <!-- Accounts for any org work with MS domain accounts. Most folks probably want the last option, personal MS accounts. It took 24 hours after setting this up for the keys to work -->
* <span data-ttu-id="6376a-114">En **URI de redirección** `/signin-microsoft` , escriba la dirección URL de desarrollo con anexado.</span><span class="sxs-lookup"><span data-stu-id="6376a-114">Under **Redirect URI**, enter your development URL with `/signin-microsoft` appended.</span></span> <span data-ttu-id="6376a-115">Por ejemplo, `https://localhost:5001/signin-microsoft`.</span><span class="sxs-lookup"><span data-stu-id="6376a-115">For example, `https://localhost:5001/signin-microsoft`.</span></span> <span data-ttu-id="6376a-116">El esquema de autenticación de Microsoft configurado más `/signin-microsoft` adelante en este ejemplo controlará automáticamente las solicitudes en ruta para implementar el flujo de OAuth.</span><span class="sxs-lookup"><span data-stu-id="6376a-116">The Microsoft authentication scheme configured later in this sample will automatically handle requests at `/signin-microsoft` route to implement the OAuth flow.</span></span>
* <span data-ttu-id="6376a-117">Seleccione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="6376a-117">Select **Register**</span></span>

### <a name="create-client-secret"></a><span data-ttu-id="6376a-118">Creación de un secreto de cliente</span><span class="sxs-lookup"><span data-stu-id="6376a-118">Create client secret</span></span>

* <span data-ttu-id="6376a-119">En el panel izquierdo, seleccione **Certificados y secretos**.</span><span class="sxs-lookup"><span data-stu-id="6376a-119">In the left pane, select **Certificates & secrets**.</span></span>
* <span data-ttu-id="6376a-120">En **Secretos de cliente**, seleccione Nuevo secreto de **cliente**</span><span class="sxs-lookup"><span data-stu-id="6376a-120">Under **Client secrets**, select **New client secret**</span></span>

  * <span data-ttu-id="6376a-121">Agregue una descripción para el secreto de cliente.</span><span class="sxs-lookup"><span data-stu-id="6376a-121">Add a description for the client secret.</span></span>
  * <span data-ttu-id="6376a-122">Seleccione el botón **Agregar.**</span><span class="sxs-lookup"><span data-stu-id="6376a-122">Select the **Add** button.</span></span>

* <span data-ttu-id="6376a-123">En **Secretos de cliente**, copie el valor del secreto de cliente.</span><span class="sxs-lookup"><span data-stu-id="6376a-123">Under **Client secrets**, copy the value of the client secret.</span></span>

<span data-ttu-id="6376a-124">El segmento `/signin-microsoft` URI se establece como la devolución de llamada predeterminada del proveedor de autenticación de Microsoft.</span><span class="sxs-lookup"><span data-stu-id="6376a-124">The URI segment `/signin-microsoft` is set as the default callback of the Microsoft authentication provider.</span></span> <span data-ttu-id="6376a-125">Puede cambiar el URI de devolución de llamada predeterminado al configurar el middleware de autenticación de Microsoft a través de la propiedad [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) heredada de la clase [MicrosoftAccountOptions.](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions)</span><span class="sxs-lookup"><span data-stu-id="6376a-125">You can change the default callback URI while configuring the Microsoft authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) class.</span></span>

## <a name="store-the-microsoft-client-id-and-secret"></a><span data-ttu-id="6376a-126">Almacene el identificador de cliente y el secreto de Microsoft</span><span class="sxs-lookup"><span data-stu-id="6376a-126">Store the Microsoft client ID and secret</span></span>

<span data-ttu-id="6376a-127">Almacene la configuración confidencial, como el identificador de cliente de Microsoft y los valores secretos con [Secret Manager](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="6376a-127">Store sensitive settings such as the Microsoft client ID and secret values with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="6376a-128">Para este ejemplo, siga estos pasos:</span><span class="sxs-lookup"><span data-stu-id="6376a-128">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="6376a-129">Inicializar el proyecto para el almacenamiento secreto según las instrucciones de [Habilitar almacenamiento secreto](xref:security/app-secrets#enable-secret-storage).</span><span class="sxs-lookup"><span data-stu-id="6376a-129">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="6376a-130">Almacene la configuración confidencial en el almacén secreto local con las claves `Authentication:Microsoft:ClientId` secretas y: `Authentication:Microsoft:ClientSecret`</span><span class="sxs-lookup"><span data-stu-id="6376a-130">Store the sensitive settings in the local secret store with the secret keys `Authentication:Microsoft:ClientId` and `Authentication:Microsoft:ClientSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Microsoft:ClientId" "<client-id>"
    dotnet user-secrets set "Authentication:Microsoft:ClientSecret" "<client-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-microsoft-account-authentication"></a><span data-ttu-id="6376a-131">Configurar la autenticación de cuenta Microsoft</span><span class="sxs-lookup"><span data-stu-id="6376a-131">Configure Microsoft Account Authentication</span></span>

<span data-ttu-id="6376a-132">Agregue el servicio Cuenta `Startup.ConfigureServices`Microsoft a:</span><span class="sxs-lookup"><span data-stu-id="6376a-132">Add the Microsoft Account service to the `Startup.ConfigureServices`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupMS3x.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

<span data-ttu-id="6376a-133">Para obtener más información acerca de las opciones de configuración admitidas por la autenticación de cuenta Microsoft, consulte la referencia de la API [MicrosoftAccountOptions.](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions)</span><span class="sxs-lookup"><span data-stu-id="6376a-133">For more information about configuration options supported by Microsoft Account authentication, see the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) API reference.</span></span> <span data-ttu-id="6376a-134">Esto se puede utilizar para solicitar información diferente sobre el usuario.</span><span class="sxs-lookup"><span data-stu-id="6376a-134">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-microsoft-account"></a><span data-ttu-id="6376a-135">Inicie sesión con la cuenta Microsoft</span><span class="sxs-lookup"><span data-stu-id="6376a-135">Sign in with Microsoft Account</span></span>

<span data-ttu-id="6376a-136">Ejecute la aplicación y haga clic **en Iniciar sesión**.</span><span class="sxs-lookup"><span data-stu-id="6376a-136">Run the app and click **Log in**.</span></span> <span data-ttu-id="6376a-137">Aparece una opción para iniciar sesión con Microsoft.</span><span class="sxs-lookup"><span data-stu-id="6376a-137">An option to sign in with Microsoft appears.</span></span> <span data-ttu-id="6376a-138">Al hacer clic en Microsoft, se le redirige a Microsoft para la autenticación.</span><span class="sxs-lookup"><span data-stu-id="6376a-138">When you click on Microsoft, you are redirected to Microsoft for authentication.</span></span> <span data-ttu-id="6376a-139">Después de iniciar sesión con tu cuenta Microsoft, se te pedirá que dejes que la aplicación acceda a tu información:</span><span class="sxs-lookup"><span data-stu-id="6376a-139">After signing in with your Microsoft Account, you will be prompted to let the app access your info:</span></span>

<span data-ttu-id="6376a-140">Toque **Sí** y se le redirigirá de nuevo al sitio web donde puede establecer su correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="6376a-140">Tap **Yes** and you will be redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="6376a-141">Ahora ha iniciado sesión con sus credenciales de Microsoft:</span><span class="sxs-lookup"><span data-stu-id="6376a-141">You are now logged in using your Microsoft credentials:</span></span>

[!INCLUDE[](includes/chain-auth-providers.md)]

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a><span data-ttu-id="6376a-142">Solución de problemas</span><span class="sxs-lookup"><span data-stu-id="6376a-142">Troubleshooting</span></span>

* <span data-ttu-id="6376a-143">Si el proveedor de la cuenta Microsoft le redirige a una página de error `#` de inicio de sesión, tenga en cuenta los parámetros de cadena de consulta de título y descripción del error que siguen directamente al (hashtag) en el Uri.</span><span class="sxs-lookup"><span data-stu-id="6376a-143">If the Microsoft Account provider redirects you to a sign in error page, note the error title and description query string parameters directly following the `#` (hashtag) in the Uri.</span></span>

  <span data-ttu-id="6376a-144">Aunque el mensaje de error parece indicar un problema con la autenticación de Microsoft, la causa más común es que el Uri de la aplicación no coincide con ninguno de los URI de **redirección** especificados para la plataforma **web.**</span><span class="sxs-lookup"><span data-stu-id="6376a-144">Although the error message seems to indicate a problem with Microsoft authentication, the most common cause is your application Uri not matching any of the **Redirect URIs** specified for the **Web** platform.</span></span>
* <span data-ttu-id="6376a-145">Si Identity no está `services.AddIdentity` configurado `ConfigureServices`llamando a , al intentar autenticar se producirá *ArgumentException: se debe proporcionar la opción 'SignInScheme'.*</span><span class="sxs-lookup"><span data-stu-id="6376a-145">If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="6376a-146">La plantilla de proyecto utilizada en este ejemplo garantiza que esto se realiza.</span><span class="sxs-lookup"><span data-stu-id="6376a-146">The project template used in this sample ensures that this is done.</span></span>
* <span data-ttu-id="6376a-147">Si la base de datos del sitio no se ha creado aplicando la migración inicial, obtendrá un error en la operación de base de datos al procesar el error de *solicitud.*</span><span class="sxs-lookup"><span data-stu-id="6376a-147">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="6376a-148">Puntee **Aplicar migraciones** para crear la base de datos y actualizar la actualización para continuar más allá del error.</span><span class="sxs-lookup"><span data-stu-id="6376a-148">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="6376a-149">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="6376a-149">Next steps</span></span>

* <span data-ttu-id="6376a-150">En este artículo se muestra cómo puede autenticarse con Microsoft.</span><span class="sxs-lookup"><span data-stu-id="6376a-150">This article showed how you can authenticate with Microsoft.</span></span> <span data-ttu-id="6376a-151">Puede seguir un enfoque similar para autenticarse con otros proveedores enumerados en la [página anterior.](xref:security/authentication/social/index)</span><span class="sxs-lookup"><span data-stu-id="6376a-151">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="6376a-152">Una vez que publique el sitio web en la aplicación web de Azure, cree un nuevo secreto de cliente en el Portal para desarrolladores de Microsoft.</span><span class="sxs-lookup"><span data-stu-id="6376a-152">Once you publish your web site to Azure web app, create a new client secrets in the Microsoft Developer Portal.</span></span>

* <span data-ttu-id="6376a-153">Establezca `Authentication:Microsoft:ClientId` la `Authentication:Microsoft:ClientSecret` configuración y como aplicación en Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="6376a-153">Set the `Authentication:Microsoft:ClientId` and `Authentication:Microsoft:ClientSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="6376a-154">El sistema de configuración está configurado para leer las claves de las variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="6376a-154">The configuration system is set up to read keys from environment variables.</span></span>
