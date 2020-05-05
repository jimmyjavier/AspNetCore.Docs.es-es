---
title: Confirmación de las cuentas y recuperación de contraseñas en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo crear una aplicación ASP.NET Core con confirmación por correo electrónico y restablecimiento de contraseña.
ms.author: riande
ms.date: 03/11/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/accconfirm
ms.openlocfilehash: b7856a3004cfc76acfb485ff8f1fadf87f5aa904
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777116"
---
# <a name="account-confirmation-and-password-recovery-in-aspnet-core"></a><span data-ttu-id="9c1d5-103">Confirmación de las cuentas y recuperación de contraseñas en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9c1d5-103">Account confirmation and password recovery in ASP.NET Core</span></span>

<span data-ttu-id="9c1d5-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant)y [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="9c1d5-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant), and [Joe Audette](https://twitter.com/joeaudette)</span></span>

<span data-ttu-id="9c1d5-105">En este tutorial se muestra cómo crear una aplicación ASP.NET Core con confirmación de correo electrónico y restablecimiento de contraseña.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-105">This tutorial shows how to build an ASP.NET Core app with email confirmation and password reset.</span></span> <span data-ttu-id="9c1d5-106">Este tutorial **no** es un tema de inicio.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-106">This tutorial is **not** a beginning topic.</span></span> <span data-ttu-id="9c1d5-107">Debe estar familiarizado con:</span><span class="sxs-lookup"><span data-stu-id="9c1d5-107">You should be familiar with:</span></span>

* [<span data-ttu-id="9c1d5-108">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9c1d5-108">ASP.NET Core</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="9c1d5-109">Autenticación</span><span class="sxs-lookup"><span data-stu-id="9c1d5-109">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="9c1d5-110">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="9c1d5-110">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

<!-- see C:/Dropbox/wrk/Code/SendGridConsole/Program.cs -->

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="9c1d5-111">Vea [este archivo PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) para obtener la versión ASP.net Core 1,1.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-111">See [this PDF file](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) for the ASP.NET Core 1.1 version.</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.2"

## <a name="prerequisites"></a><span data-ttu-id="9c1d5-112">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="9c1d5-112">Prerequisites</span></span>

[<span data-ttu-id="9c1d5-113">SDK de .NET Core 3.0 o versiones posteriores</span><span class="sxs-lookup"><span data-stu-id="9c1d5-113">.NET Core 3.0 SDK or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core/3.0)

## <a name="create-and-test-a-web-app-with-authentication"></a><span data-ttu-id="9c1d5-114">Crear y probar una aplicación web con autenticación</span><span class="sxs-lookup"><span data-stu-id="9c1d5-114">Create and test a web app with authentication</span></span>

<span data-ttu-id="9c1d5-115">Ejecute los siguientes comandos para crear una aplicación web con autenticación.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-115">Run the following commands to create a web app with authentication.</span></span>

```dotnetcli
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet run
```

<span data-ttu-id="9c1d5-116">Ejecute la aplicación, seleccione el vínculo **registrar** y registre un usuario.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-116">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="9c1d5-117">Una vez registrado, se le redirigirá `/Identity/Account/RegisterConfirmation` a la página a que contiene un vínculo para simular la confirmación de correo electrónico:</span><span class="sxs-lookup"><span data-stu-id="9c1d5-117">Once registered, you are redirected to the to `/Identity/Account/RegisterConfirmation` page which contains a link to simulate email confirmation:</span></span>

* <span data-ttu-id="9c1d5-118">Seleccione el `Click here to confirm your account` vínculo.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-118">Select the `Click here to confirm your account` link.</span></span>
* <span data-ttu-id="9c1d5-119">Seleccione el vínculo de **Inicio de sesión** e inicie sesión con las mismas credenciales.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-119">Select the **Login** link and sign-in with the same credentials.</span></span>
* <span data-ttu-id="9c1d5-120">Seleccione el `Hello YourEmail@provider.com!` vínculo, que le redirigirá a la `/Identity/Account/Manage/PersonalData` página.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-120">Select the `Hello YourEmail@provider.com!` link, which redirects you to the `/Identity/Account/Manage/PersonalData` page.</span></span>
* <span data-ttu-id="9c1d5-121">Seleccione la pestaña **datos personales** de la izquierda y, a continuación, seleccione **eliminar**.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-121">Select the **Personal data** tab on the left, and then select **Delete**.</span></span>

### <a name="configure-an-email-provider"></a><span data-ttu-id="9c1d5-122">Configuración de un proveedor de correo electrónico</span><span class="sxs-lookup"><span data-stu-id="9c1d5-122">Configure an email provider</span></span>

<span data-ttu-id="9c1d5-123">En este tutorial, se usa [SendGrid](https://sendgrid.com) para enviar correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-123">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="9c1d5-124">Necesita una cuenta y una clave de SendGrid para enviar el correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-124">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="9c1d5-125">Puede usar otros proveedores de correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-125">You can use other email providers.</span></span> <span data-ttu-id="9c1d5-126">Se recomienda usar SendGrid u otro servicio de correo electrónico para enviar correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-126">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="9c1d5-127">SMTP es difícil de proteger y configurar correctamente.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-127">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="9c1d5-128">Cree una clase para capturar la clave de correo electrónico segura.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-128">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="9c1d5-129">Para este ejemplo, cree *Services/AuthMessageSenderOptions. CS*:</span><span class="sxs-lookup"><span data-stu-id="9c1d5-129">For this sample, create *Services/AuthMessageSenderOptions.cs*:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="9c1d5-130">Configuración de secretos de usuario de SendGrid</span><span class="sxs-lookup"><span data-stu-id="9c1d5-130">Configure SendGrid user secrets</span></span>

<span data-ttu-id="9c1d5-131">Establezca `SendGridUser` y `SendGridKey` con la [herramienta de administrador de secretos](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="9c1d5-131">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="9c1d5-132">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="9c1d5-132">For example:</span></span>

```dotnetcli
dotnet user-secrets set SendGridUser RickAndMSFT
dotnet user-secrets set SendGridKey <key>

Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="9c1d5-133">En Windows, el administrador de secretos almacena pares de clave/valor en un archivo *Secrets. JSON* en el `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directorio.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-133">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="9c1d5-134">El contenido del archivo *Secrets. JSON* no está cifrado.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-134">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="9c1d5-135">El marcado siguiente muestra el archivo *Secrets. JSON* .</span><span class="sxs-lookup"><span data-stu-id="9c1d5-135">The following markup shows the *secrets.json* file.</span></span> <span data-ttu-id="9c1d5-136">Se `SendGridKey` ha quitado el valor.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-136">The `SendGridKey` value has been removed.</span></span>

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

<span data-ttu-id="9c1d5-137">Para obtener más información, vea el [patrón de opciones](xref:fundamentals/configuration/options) y la [configuración](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="9c1d5-137">For more information, see the [Options pattern](xref:fundamentals/configuration/options) and [configuration](xref:fundamentals/configuration/index).</span></span>

### <a name="install-sendgrid"></a><span data-ttu-id="9c1d5-138">Instalación de SendGrid</span><span class="sxs-lookup"><span data-stu-id="9c1d5-138">Install SendGrid</span></span>

<span data-ttu-id="9c1d5-139">En este tutorial se muestra cómo agregar notificaciones por correo electrónico a través de [SendGrid](https://sendgrid.com/), pero puede enviar correo electrónico mediante SMTP y otros mecanismos.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-139">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="9c1d5-140">Instale el `SendGrid` paquete NuGet:</span><span class="sxs-lookup"><span data-stu-id="9c1d5-140">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9c1d5-141">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9c1d5-141">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9c1d5-142">En la consola del administrador de paquetes, escriba el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="9c1d5-142">From the Package Manager Console, enter the following command:</span></span>

```powershell
Install-Package SendGrid
```

# <a name="net-core-cli"></a>[<span data-ttu-id="9c1d5-143">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="9c1d5-143">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="9c1d5-144">En la consola de, escriba el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="9c1d5-144">From the console, enter the following command:</span></span>

```dotnetcli
dotnet add package SendGrid
```

---

<span data-ttu-id="9c1d5-145">Consulte Introducción a [SendGrid gratis](https://sendgrid.com/free/) para registrarse para obtener una cuenta gratuita de SendGrid.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-145">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>

### <a name="implement-iemailsender"></a><span data-ttu-id="9c1d5-146">Implementación de IEmailSender</span><span class="sxs-lookup"><span data-stu-id="9c1d5-146">Implement IEmailSender</span></span>

<span data-ttu-id="9c1d5-147">Para implementar `IEmailSender`, cree *Services/EmailSender. CS* con código similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="9c1d5-147">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="9c1d5-148">Configurar el inicio para admitir correo electrónico</span><span class="sxs-lookup"><span data-stu-id="9c1d5-148">Configure startup to support email</span></span>

<span data-ttu-id="9c1d5-149">Agregue el código siguiente al `ConfigureServices` método en el archivo *Startup.CS* :</span><span class="sxs-lookup"><span data-stu-id="9c1d5-149">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="9c1d5-150">Agregue `EmailSender` como un servicio transitorio.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-150">Add `EmailSender` as a transient service.</span></span>
* <span data-ttu-id="9c1d5-151">Registre la `AuthMessageSenderOptions` instancia de configuración.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-151">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Startup.cs?name=snippet1&highlight=11-15)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="9c1d5-152">Registro, confirmación de correo electrónico y restablecimiento de contraseña</span><span class="sxs-lookup"><span data-stu-id="9c1d5-152">Register, confirm email, and reset password</span></span>

<span data-ttu-id="9c1d5-153">Ejecute la aplicación web y pruebe el flujo de recuperación de la contraseña y la confirmación de la cuenta.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-153">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="9c1d5-154">Ejecutar la aplicación y registrar un nuevo usuario</span><span class="sxs-lookup"><span data-stu-id="9c1d5-154">Run the app and register a new user</span></span>
* <span data-ttu-id="9c1d5-155">Compruebe su correo electrónico para el vínculo de confirmación de la cuenta.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-155">Check your email for the account confirmation link.</span></span> <span data-ttu-id="9c1d5-156">Consulte [depurar correo electrónico](#debug) si no recibe el correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-156">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="9c1d5-157">Haga clic en el vínculo para confirmar el correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-157">Click the link to confirm your email.</span></span>
* <span data-ttu-id="9c1d5-158">Inicie sesión con su correo electrónico y contraseña.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-158">Sign in with your email and password.</span></span>
* <span data-ttu-id="9c1d5-159">Cierre la sesión.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-159">Sign out.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="9c1d5-160">Restablecimiento de la contraseña de prueba</span><span class="sxs-lookup"><span data-stu-id="9c1d5-160">Test password reset</span></span>

* <span data-ttu-id="9c1d5-161">Si ha iniciado sesión, seleccione **Logout**.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-161">If you're signed in, select **Logout**.</span></span>
* <span data-ttu-id="9c1d5-162">Seleccione el vínculo **iniciar sesión** y seleccione el vínculo **¿olvidó su contraseña?** .</span><span class="sxs-lookup"><span data-stu-id="9c1d5-162">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="9c1d5-163">Escriba el correo electrónico que usó para registrar la cuenta.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-163">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="9c1d5-164">Se envía un correo electrónico con un vínculo para restablecer la contraseña.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-164">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="9c1d5-165">Compruebe su correo electrónico y haga clic en el vínculo para restablecer la contraseña.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-165">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="9c1d5-166">Una vez que la contraseña se haya restablecido correctamente, puede iniciar sesión con el correo electrónico y la nueva contraseña.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-166">After your password has been successfully reset, you can sign in with your email and new password.</span></span>

## <a name="change-email-and-activity-timeout"></a><span data-ttu-id="9c1d5-167">Cambiar el tiempo de espera del correo electrónico y la actividad</span><span class="sxs-lookup"><span data-stu-id="9c1d5-167">Change email and activity timeout</span></span>

<span data-ttu-id="9c1d5-168">El tiempo de espera de inactividad predeterminado es de 14 días.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-168">The default inactivity timeout is 14 days.</span></span> <span data-ttu-id="9c1d5-169">El código siguiente establece el tiempo de espera de inactividad en 5 días:</span><span class="sxs-lookup"><span data-stu-id="9c1d5-169">The following code sets the inactivity timeout to 5 days:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a><span data-ttu-id="9c1d5-170">Cambiar todas las vigencias de los tokens de protección de datos</span><span class="sxs-lookup"><span data-stu-id="9c1d5-170">Change all data protection token lifespans</span></span>

<span data-ttu-id="9c1d5-171">El siguiente código cambia el tiempo de espera de todos los tokens de protección de datos a 3 horas:</span><span class="sxs-lookup"><span data-stu-id="9c1d5-171">The following code changes all data protection tokens timeout period to 3 hours:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAllTokens.cs?name=snippet1&highlight=11-12)]

<span data-ttu-id="9c1d5-172">Los tokens de usuario de identidad integrados (consulte [AspNetCore/src/Identity/Extensions. Core/src/TokenOptions. CS](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) tienen un [tiempo de espera de un día](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="9c1d5-172">The built in Identity user tokens (see [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )have a [one day timeout](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span>

### <a name="change-the-email-token-lifespan"></a><span data-ttu-id="9c1d5-173">Cambiar la duración del token de correo electrónico</span><span class="sxs-lookup"><span data-stu-id="9c1d5-173">Change the email token lifespan</span></span>

<span data-ttu-id="9c1d5-174">La duración predeterminada del token de [los tokens de usuario de identidad](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) es [un día](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="9c1d5-174">The default token lifespan of [the Identity user tokens](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) is [one day](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span> <span data-ttu-id="9c1d5-175">En esta sección se muestra cómo cambiar la duración del token de correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-175">This section shows how to change the email token lifespan.</span></span>

<span data-ttu-id="9c1d5-176">Agregue un [DataProtectorTokenProvider\<de TUser personalizado>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) y: <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions></span><span class="sxs-lookup"><span data-stu-id="9c1d5-176">Add a custom [DataProtectorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) and <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

<span data-ttu-id="9c1d5-177">Agregue el proveedor personalizado al contenedor de servicio:</span><span class="sxs-lookup"><span data-stu-id="9c1d5-177">Add the custom provider to the service container:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupEmail.cs?name=snippet1&highlight=10-16)]

### <a name="resend-email-confirmation"></a><span data-ttu-id="9c1d5-178">Confirmación de reenvío de correo electrónico</span><span class="sxs-lookup"><span data-stu-id="9c1d5-178">Resend email confirmation</span></span>

<span data-ttu-id="9c1d5-179">Consulte [este problema de GitHub](https://github.com/dotnet/AspNetCore/issues/5410).</span><span class="sxs-lookup"><span data-stu-id="9c1d5-179">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5410).</span></span>

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="9c1d5-180">Depurar correo electrónico</span><span class="sxs-lookup"><span data-stu-id="9c1d5-180">Debug email</span></span>

<span data-ttu-id="9c1d5-181">Si no consigue que el correo electrónico funcione:</span><span class="sxs-lookup"><span data-stu-id="9c1d5-181">If you can't get email working:</span></span>

* <span data-ttu-id="9c1d5-182">Establezca un punto de `EmailSender.Execute` interrupción en `SendGridClient.SendEmailAsync` para comprobar que se llama a.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-182">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="9c1d5-183">Cree una [aplicación de consola para enviar correo electrónico](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) mediante código `EmailSender.Execute`similar a.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-183">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="9c1d5-184">Revise la página [actividad de correo electrónico](https://sendgrid.com/docs/User_Guide/email_activity.html) .</span><span class="sxs-lookup"><span data-stu-id="9c1d5-184">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="9c1d5-185">Compruebe la carpeta de correo no deseado.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-185">Check your spam folder.</span></span>
* <span data-ttu-id="9c1d5-186">Pruebe otro alias de correo electrónico en un proveedor de correo electrónico diferente (Microsoft, Yahoo, gmail, etc.).</span><span class="sxs-lookup"><span data-stu-id="9c1d5-186">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="9c1d5-187">Intente enviar a diferentes cuentas de correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-187">Try sending to different email accounts.</span></span>

<span data-ttu-id="9c1d5-188">**Un procedimiento** recomendado de seguridad es **no** usar secretos de producción en pruebas y desarrollo.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-188">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="9c1d5-189">Si publica la aplicación en Azure, establezca los secretos de SendGrid como configuración de la aplicación en el portal de aplicaciones Web de Azure.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-189">If you publish the app to Azure, set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="9c1d5-190">El sistema de configuración está configurado para leer las claves de las variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-190">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="9c1d5-191">Combinación de cuentas de inicio de sesión sociales y locales</span><span class="sxs-lookup"><span data-stu-id="9c1d5-191">Combine social and local login accounts</span></span>

<span data-ttu-id="9c1d5-192">Para completar esta sección, primero debe habilitar un proveedor de autenticación externo.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-192">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="9c1d5-193">Consulte la [autenticación de Facebook, Google y proveedores externos](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="9c1d5-193">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="9c1d5-194">Para combinar cuentas locales y sociales, haga clic en el vínculo de correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-194">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="9c1d5-195">En la siguiente secuencia, "RickAndMSFT@gmail.com" se crea primero como un inicio de sesión local; sin embargo, puede crear primero la cuenta como un inicio de sesión social y luego agregar un inicio de sesión local.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-195">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![Aplicación web: RickAndMSFT@gmail.com usuario autenticado](accconfirm/_static/rick.png)

<span data-ttu-id="9c1d5-197">Haga clic en el vínculo **administrar** .</span><span class="sxs-lookup"><span data-stu-id="9c1d5-197">Click on the **Manage** link.</span></span> <span data-ttu-id="9c1d5-198">Observe los 0 externos (inicios de sesión sociales) asociados a esta cuenta.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-198">Note the 0 external (social logins) associated with this account.</span></span>

![Administrar vista](accconfirm/_static/manage.png)

<span data-ttu-id="9c1d5-200">Haga clic en el vínculo a otro servicio de inicio de sesión y acepte las solicitudes de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-200">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="9c1d5-201">En la imagen siguiente, Facebook es el proveedor de autenticación externo:</span><span class="sxs-lookup"><span data-stu-id="9c1d5-201">In the following image, Facebook is the external authentication provider:</span></span>

![Administrar la vista de inicios de sesión externos enumerando Facebook](accconfirm/_static/fb.png)

<span data-ttu-id="9c1d5-203">Las dos cuentas se han combinado.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-203">The two accounts have been combined.</span></span> <span data-ttu-id="9c1d5-204">Puede iniciar sesión con cualquiera de las dos cuentas.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-204">You are able to sign in with either account.</span></span> <span data-ttu-id="9c1d5-205">Es posible que desee que los usuarios agreguen cuentas locales en caso de que su servicio de autenticación de inicio de sesión social esté inactivo, o más probabilidades de que hayan perdido el acceso a su cuenta de redes sociales.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-205">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="9c1d5-206">Habilitar la confirmación de la cuenta después de que un sitio tenga usuarios</span><span class="sxs-lookup"><span data-stu-id="9c1d5-206">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="9c1d5-207">La habilitación de la confirmación de cuenta en un sitio con usuarios bloquea a todos los usuarios existentes.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-207">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="9c1d5-208">Los usuarios existentes están bloqueados porque sus cuentas no están confirmadas.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-208">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="9c1d5-209">Para solucionar el bloqueo de usuario existente, use uno de los métodos siguientes:</span><span class="sxs-lookup"><span data-stu-id="9c1d5-209">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="9c1d5-210">Actualice la base de datos para marcar todos los usuarios existentes como confirmados.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-210">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="9c1d5-211">Confirme los usuarios existentes.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-211">Confirm existing users.</span></span> <span data-ttu-id="9c1d5-212">Por ejemplo, enviar por lotes correos electrónicos con vínculos de confirmación.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-212">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.0 < aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="9c1d5-213">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="9c1d5-213">Prerequisites</span></span>

[<span data-ttu-id="9c1d5-214">SDK de .NET Core 2,2 o posterior</span><span class="sxs-lookup"><span data-stu-id="9c1d5-214">.NET Core 2.2 SDK or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)

## <a name="create-a-web--app-and-scaffold-identity"></a><span data-ttu-id="9c1d5-215">Creación de una aplicación web y una identidad scaffolding</span><span class="sxs-lookup"><span data-stu-id="9c1d5-215">Create a web  app and scaffold Identity</span></span>

<span data-ttu-id="9c1d5-216">Ejecute los siguientes comandos para crear una aplicación web con autenticación.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-216">Run the following commands to create a web app with authentication.</span></span>

```dotnetcli
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator identity -dc WebPWrecover.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout;Account.ConfirmEmail"
dotnet ef database drop -f
dotnet ef database update
dotnet run

```

## <a name="test-new-user-registration"></a><span data-ttu-id="9c1d5-217">Prueba del nuevo registro de usuario</span><span class="sxs-lookup"><span data-stu-id="9c1d5-217">Test new user registration</span></span>

<span data-ttu-id="9c1d5-218">Ejecute la aplicación, seleccione el vínculo **registrar** y registre un usuario.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-218">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="9c1d5-219">En este momento, la única validación en el correo electrónico es con [`[EmailAddress]`](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) el atributo.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-219">At this point, the only validation on the email is with the [`[EmailAddress]`](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) attribute.</span></span> <span data-ttu-id="9c1d5-220">Después de enviar el registro, ha iniciado sesión en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-220">After submitting the registration, you are logged into the app.</span></span> <span data-ttu-id="9c1d5-221">Más adelante en el tutorial, se actualiza el código para que los nuevos usuarios no puedan iniciar sesión hasta que se valide su correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-221">Later in the tutorial, the code is updated so new users can't sign in until their email is validated.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<span data-ttu-id="9c1d5-222">Tenga en cuenta que `EmailConfirmed` el campo `False`de la tabla es.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-222">Note the table's `EmailConfirmed` field is `False`.</span></span>

<span data-ttu-id="9c1d5-223">Es posible que desee usar este correo electrónico de nuevo en el paso siguiente cuando la aplicación envíe un correo electrónico de confirmación.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-223">You might want to use this email again in the next step when the app sends a confirmation email.</span></span> <span data-ttu-id="9c1d5-224">Haga clic con el botón derecho en la fila y seleccione **eliminar**.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-224">Right-click on the row and select **Delete**.</span></span> <span data-ttu-id="9c1d5-225">Al eliminar el alias de correo electrónico, se facilitan los pasos siguientes.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-225">Deleting the email alias makes it easier in the following steps.</span></span>

<a name="prevent-login-at-registration"></a>

## <a name="require-email-confirmation"></a><span data-ttu-id="9c1d5-226">Requerir confirmación de correo electrónico</span><span class="sxs-lookup"><span data-stu-id="9c1d5-226">Require email confirmation</span></span>

<span data-ttu-id="9c1d5-227">Se recomienda confirmar el correo electrónico de un nuevo registro de usuario.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-227">It's a best practice to confirm the email of a new user registration.</span></span> <span data-ttu-id="9c1d5-228">La confirmación por correo electrónico ayuda a comprobar que no está suplantando a otra persona (es decir, que no se han registrado con el correo electrónico de otro usuario).</span><span class="sxs-lookup"><span data-stu-id="9c1d5-228">Email confirmation helps to verify they're not impersonating someone else (that is, they haven't registered with someone else's email).</span></span> <span data-ttu-id="9c1d5-229">Supongamos que tiene un foro de discusión y desea evitar queyli@example.com"" se registre como "nolivetto@contoso.com".</span><span class="sxs-lookup"><span data-stu-id="9c1d5-229">Suppose you had a discussion forum, and you wanted to prevent "yli@example.com" from registering as "nolivetto@contoso.com".</span></span> <span data-ttu-id="9c1d5-230">Sin confirmación por correo electróniconolivetto@contoso.com, "" podría recibir correo electrónico no deseado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-230">Without email confirmation, "nolivetto@contoso.com" could receive unwanted email from your app.</span></span> <span data-ttu-id="9c1d5-231">Supongamos que el usuario se registró accidentalmente como "ylo@example.com" y no detectó la falta de ortografía de "Yli".</span><span class="sxs-lookup"><span data-stu-id="9c1d5-231">Suppose the user accidentally registered as "ylo@example.com" and hadn't noticed the misspelling of "yli".</span></span> <span data-ttu-id="9c1d5-232">No podrán usar la recuperación de contraseña porque la aplicación no tiene el correo electrónico correcto.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-232">They wouldn't be able to use password recovery because the app doesn't have their correct email.</span></span> <span data-ttu-id="9c1d5-233">La confirmación por correo electrónico proporciona una protección limitada de bots.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-233">Email confirmation provides limited protection from bots.</span></span> <span data-ttu-id="9c1d5-234">La confirmación por correo electrónico no proporciona protección contra usuarios malintencionados con muchas cuentas de correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-234">Email confirmation doesn't provide protection from malicious users with many email accounts.</span></span>

<span data-ttu-id="9c1d5-235">Por lo general, querrá evitar que los nuevos usuarios publiquen datos en el sitio Web antes de que tengan un correo electrónico confirmado.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-235">You generally want to prevent new users from posting any data to your web site before they have a confirmed email.</span></span>

<span data-ttu-id="9c1d5-236">Actualización `Startup.ConfigureServices` para requerir un correo electrónico confirmado:</span><span class="sxs-lookup"><span data-stu-id="9c1d5-236">Update `Startup.ConfigureServices`  to require a confirmed email:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=8-11)]

<span data-ttu-id="9c1d5-237">`config.SignIn.RequireConfirmedEmail = true;`impide que los usuarios registrados inicien sesión hasta que se confirme su correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-237">`config.SignIn.RequireConfirmedEmail = true;` prevents registered users from logging in until their email is confirmed.</span></span>

### <a name="configure-email-provider"></a><span data-ttu-id="9c1d5-238">Configurar proveedor de correo electrónico</span><span class="sxs-lookup"><span data-stu-id="9c1d5-238">Configure email provider</span></span>

<span data-ttu-id="9c1d5-239">En este tutorial, se usa [SendGrid](https://sendgrid.com) para enviar correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-239">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="9c1d5-240">Necesita una cuenta y una clave de SendGrid para enviar el correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-240">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="9c1d5-241">Puede usar otros proveedores de correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-241">You can use other email providers.</span></span> <span data-ttu-id="9c1d5-242">ASP.NET Core 2. x incluye `System.Net.Mail`, que le permite enviar correo electrónico desde su aplicación.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-242">ASP.NET Core 2.x includes `System.Net.Mail`, which allows you to send email from your app.</span></span> <span data-ttu-id="9c1d5-243">Se recomienda usar SendGrid u otro servicio de correo electrónico para enviar correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-243">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="9c1d5-244">SMTP es difícil de proteger y configurar correctamente.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-244">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="9c1d5-245">Cree una clase para capturar la clave de correo electrónico segura.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-245">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="9c1d5-246">Para este ejemplo, cree *Services/AuthMessageSenderOptions. CS*:</span><span class="sxs-lookup"><span data-stu-id="9c1d5-246">For this sample, create *Services/AuthMessageSenderOptions.cs*:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="9c1d5-247">Configuración de secretos de usuario de SendGrid</span><span class="sxs-lookup"><span data-stu-id="9c1d5-247">Configure SendGrid user secrets</span></span>

<span data-ttu-id="9c1d5-248">Establezca `SendGridUser` y `SendGridKey` con la [herramienta de administrador de secretos](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="9c1d5-248">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="9c1d5-249">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="9c1d5-249">For example:</span></span>

```console
C:/WebAppl>dotnet user-secrets set SendGridUser RickAndMSFT
info: Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="9c1d5-250">En Windows, el administrador de secretos almacena pares de clave/valor en un archivo *Secrets. JSON* en el `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directorio.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-250">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="9c1d5-251">El contenido del archivo *Secrets. JSON* no está cifrado.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-251">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="9c1d5-252">El marcado siguiente muestra el archivo *Secrets. JSON* .</span><span class="sxs-lookup"><span data-stu-id="9c1d5-252">The following markup shows the *secrets.json* file.</span></span> <span data-ttu-id="9c1d5-253">Se `SendGridKey` ha quitado el valor.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-253">The `SendGridKey` value has been removed.</span></span>

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

<span data-ttu-id="9c1d5-254">Para obtener más información, vea el [patrón de opciones](xref:fundamentals/configuration/options) y la [configuración](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="9c1d5-254">For more information, see the [Options pattern](xref:fundamentals/configuration/options) and [configuration](xref:fundamentals/configuration/index).</span></span>

### <a name="install-sendgrid"></a><span data-ttu-id="9c1d5-255">Instalación de SendGrid</span><span class="sxs-lookup"><span data-stu-id="9c1d5-255">Install SendGrid</span></span>

<span data-ttu-id="9c1d5-256">En este tutorial se muestra cómo agregar notificaciones por correo electrónico a través de [SendGrid](https://sendgrid.com/), pero puede enviar correo electrónico mediante SMTP y otros mecanismos.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-256">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="9c1d5-257">Instale el `SendGrid` paquete NuGet:</span><span class="sxs-lookup"><span data-stu-id="9c1d5-257">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9c1d5-258">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9c1d5-258">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9c1d5-259">En la consola del administrador de paquetes, escriba el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="9c1d5-259">From the Package Manager Console, enter the following command:</span></span>

```powershell
Install-Package SendGrid
```

# <a name="net-core-cli"></a>[<span data-ttu-id="9c1d5-260">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="9c1d5-260">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="9c1d5-261">En la consola de, escriba el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="9c1d5-261">From the console, enter the following command:</span></span>

```dotnetcli
dotnet add package SendGrid
```

---

<span data-ttu-id="9c1d5-262">Consulte Introducción a [SendGrid gratis](https://sendgrid.com/free/) para registrarse para obtener una cuenta gratuita de SendGrid.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-262">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>

### <a name="implement-iemailsender"></a><span data-ttu-id="9c1d5-263">Implementación de IEmailSender</span><span class="sxs-lookup"><span data-stu-id="9c1d5-263">Implement IEmailSender</span></span>

<span data-ttu-id="9c1d5-264">Para implementar `IEmailSender`, cree *Services/EmailSender. CS* con código similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="9c1d5-264">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="9c1d5-265">Configurar el inicio para admitir correo electrónico</span><span class="sxs-lookup"><span data-stu-id="9c1d5-265">Configure startup to support email</span></span>

<span data-ttu-id="9c1d5-266">Agregue el código siguiente al `ConfigureServices` método en el archivo *Startup.CS* :</span><span class="sxs-lookup"><span data-stu-id="9c1d5-266">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="9c1d5-267">Agregue `EmailSender` como un servicio transitorio.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-267">Add `EmailSender` as a transient service.</span></span>
* <span data-ttu-id="9c1d5-268">Registre la `AuthMessageSenderOptions` instancia de configuración.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-268">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=15-99)]

## <a name="enable-account-confirmation-and-password-recovery"></a><span data-ttu-id="9c1d5-269">Habilitar la confirmación de la cuenta y la recuperación de la contraseña</span><span class="sxs-lookup"><span data-stu-id="9c1d5-269">Enable account confirmation and password recovery</span></span>

<span data-ttu-id="9c1d5-270">La plantilla tiene el código para la confirmación de la cuenta y la recuperación de la contraseña.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-270">The template has the code for account confirmation and password recovery.</span></span> <span data-ttu-id="9c1d5-271">Busque el `OnPostAsync` método en *areasIdentity//pages/Account/Register.cshtml.CS*.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-271">Find the `OnPostAsync` method in *Areas/Identity/Pages/Account/Register.cshtml.cs*.</span></span>

<span data-ttu-id="9c1d5-272">Evite que los usuarios recién registrados inicien sesión automáticamente al comentar la siguiente línea:</span><span class="sxs-lookup"><span data-stu-id="9c1d5-272">Prevent newly registered users from being automatically signed in by commenting out the following line:</span></span>

```csharp
await _signInManager.SignInAsync(user, isPersistent: false);
```

<span data-ttu-id="9c1d5-273">El método complete se muestra con la línea resaltada:</span><span class="sxs-lookup"><span data-stu-id="9c1d5-273">The complete method is shown with the changed line highlighted:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Areas/Identity/Pages/Account/Register.cshtml.cs?highlight=22&name=snippet_Register)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="9c1d5-274">Registro, confirmación de correo electrónico y restablecimiento de contraseña</span><span class="sxs-lookup"><span data-stu-id="9c1d5-274">Register, confirm email, and reset password</span></span>

<span data-ttu-id="9c1d5-275">Ejecute la aplicación web y pruebe el flujo de recuperación de la contraseña y la confirmación de la cuenta.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-275">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="9c1d5-276">Ejecutar la aplicación y registrar un nuevo usuario</span><span class="sxs-lookup"><span data-stu-id="9c1d5-276">Run the app and register a new user</span></span>
* <span data-ttu-id="9c1d5-277">Compruebe su correo electrónico para el vínculo de confirmación de la cuenta.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-277">Check your email for the account confirmation link.</span></span> <span data-ttu-id="9c1d5-278">Consulte [depurar correo electrónico](#debug) si no recibe el correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-278">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="9c1d5-279">Haga clic en el vínculo para confirmar el correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-279">Click the link to confirm your email.</span></span>
* <span data-ttu-id="9c1d5-280">Inicie sesión con su correo electrónico y contraseña.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-280">Sign in with your email and password.</span></span>
* <span data-ttu-id="9c1d5-281">Cierre la sesión.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-281">Sign out.</span></span>

### <a name="view-the-manage-page"></a><span data-ttu-id="9c1d5-282">Ver la página administrar</span><span class="sxs-lookup"><span data-stu-id="9c1d5-282">View the manage page</span></span>

<span data-ttu-id="9c1d5-283">Seleccione el nombre de usuario en el explorador ![: ventana del explorador con el nombre de usuario](accconfirm/_static/un.png)</span><span class="sxs-lookup"><span data-stu-id="9c1d5-283">Select your user name in the browser: ![browser window with user name](accconfirm/_static/un.png)</span></span>

<span data-ttu-id="9c1d5-284">La página Administrar se muestra con la pestaña **perfil** seleccionada.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-284">The manage page is displayed with the **Profile** tab selected.</span></span> <span data-ttu-id="9c1d5-285">El **correo electrónico** muestra una casilla que indica que se ha confirmado el correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-285">The **Email** shows a check box indicating the email has been confirmed.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="9c1d5-286">Restablecimiento de la contraseña de prueba</span><span class="sxs-lookup"><span data-stu-id="9c1d5-286">Test password reset</span></span>

* <span data-ttu-id="9c1d5-287">Si ha iniciado sesión, seleccione **Logout**.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-287">If you're signed in, select **Logout**.</span></span>
* <span data-ttu-id="9c1d5-288">Seleccione el vínculo **iniciar sesión** y seleccione el vínculo **¿olvidó su contraseña?** .</span><span class="sxs-lookup"><span data-stu-id="9c1d5-288">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="9c1d5-289">Escriba el correo electrónico que usó para registrar la cuenta.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-289">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="9c1d5-290">Se envía un correo electrónico con un vínculo para restablecer la contraseña.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-290">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="9c1d5-291">Compruebe su correo electrónico y haga clic en el vínculo para restablecer la contraseña.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-291">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="9c1d5-292">Una vez que la contraseña se haya restablecido correctamente, puede iniciar sesión con el correo electrónico y la nueva contraseña.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-292">After your password has been successfully reset, you can sign in with your email and new password.</span></span>

## <a name="change-email-and-activity-timeout"></a><span data-ttu-id="9c1d5-293">Cambiar el tiempo de espera del correo electrónico y la actividad</span><span class="sxs-lookup"><span data-stu-id="9c1d5-293">Change email and activity timeout</span></span>

<span data-ttu-id="9c1d5-294">El tiempo de espera de inactividad predeterminado es de 14 días.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-294">The default inactivity timeout is 14 days.</span></span> <span data-ttu-id="9c1d5-295">El código siguiente establece el tiempo de espera de inactividad en 5 días:</span><span class="sxs-lookup"><span data-stu-id="9c1d5-295">The following code sets the inactivity timeout to 5 days:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a><span data-ttu-id="9c1d5-296">Cambiar todas las vigencias de los tokens de protección de datos</span><span class="sxs-lookup"><span data-stu-id="9c1d5-296">Change all data protection token lifespans</span></span>

<span data-ttu-id="9c1d5-297">El siguiente código cambia el tiempo de espera de todos los tokens de protección de datos a 3 horas:</span><span class="sxs-lookup"><span data-stu-id="9c1d5-297">The following code changes all data protection tokens timeout period to 3 hours:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAllTokens.cs?name=snippet1&highlight=15-16)]

<span data-ttu-id="9c1d5-298">Los tokens Identity de usuario integrados (consulte [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.CS](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) tienen un [tiempo de espera](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs)de un día.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-298">The built in Identity user tokens (see [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )have a [one day timeout](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span>

### <a name="change-the-email-token-lifespan"></a><span data-ttu-id="9c1d5-299">Cambiar la duración del token de correo electrónico</span><span class="sxs-lookup"><span data-stu-id="9c1d5-299">Change the email token lifespan</span></span>

<span data-ttu-id="9c1d5-300">La duración predeterminada del token [de Identity los tokens de usuario](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) es de [un día](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="9c1d5-300">The default token lifespan of [the Identity user tokens](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) is [one day](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span> <span data-ttu-id="9c1d5-301">En esta sección se muestra cómo cambiar la duración del token de correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-301">This section shows how to change the email token lifespan.</span></span>

<span data-ttu-id="9c1d5-302">Agregue un [DataProtectorTokenProvider\<de TUser personalizado>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) y: <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions></span><span class="sxs-lookup"><span data-stu-id="9c1d5-302">Add a custom [DataProtectorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) and <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

<span data-ttu-id="9c1d5-303">Agregue el proveedor personalizado al contenedor de servicio:</span><span class="sxs-lookup"><span data-stu-id="9c1d5-303">Add the custom provider to the service container:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupEmail.cs?name=snippet1&highlight=10-13,18)]

### <a name="resend-email-confirmation"></a><span data-ttu-id="9c1d5-304">Confirmación de reenvío de correo electrónico</span><span class="sxs-lookup"><span data-stu-id="9c1d5-304">Resend email confirmation</span></span>

<span data-ttu-id="9c1d5-305">Consulte [este problema de GitHub](https://github.com/dotnet/AspNetCore/issues/5410).</span><span class="sxs-lookup"><span data-stu-id="9c1d5-305">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5410).</span></span>

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="9c1d5-306">Depurar correo electrónico</span><span class="sxs-lookup"><span data-stu-id="9c1d5-306">Debug email</span></span>

<span data-ttu-id="9c1d5-307">Si no consigue que el correo electrónico funcione:</span><span class="sxs-lookup"><span data-stu-id="9c1d5-307">If you can't get email working:</span></span>

* <span data-ttu-id="9c1d5-308">Establezca un punto de `EmailSender.Execute` interrupción en `SendGridClient.SendEmailAsync` para comprobar que se llama a.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-308">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="9c1d5-309">Cree una [aplicación de consola para enviar correo electrónico](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) mediante código `EmailSender.Execute`similar a.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-309">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="9c1d5-310">Revise la página [actividad de correo electrónico](https://sendgrid.com/docs/User_Guide/email_activity.html) .</span><span class="sxs-lookup"><span data-stu-id="9c1d5-310">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="9c1d5-311">Compruebe la carpeta de correo no deseado.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-311">Check your spam folder.</span></span>
* <span data-ttu-id="9c1d5-312">Pruebe otro alias de correo electrónico en un proveedor de correo electrónico diferente (Microsoft, Yahoo, gmail, etc.).</span><span class="sxs-lookup"><span data-stu-id="9c1d5-312">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="9c1d5-313">Intente enviar a diferentes cuentas de correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-313">Try sending to different email accounts.</span></span>

<span data-ttu-id="9c1d5-314">**Un procedimiento** recomendado de seguridad es **no** usar secretos de producción en pruebas y desarrollo.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-314">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="9c1d5-315">Si publica la aplicación en Azure, puede establecer los secretos de SendGrid como configuración de la aplicación en el portal de aplicaciones Web de Azure.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-315">If you publish the app to Azure, you can set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="9c1d5-316">El sistema de configuración está configurado para leer las claves de las variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-316">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="9c1d5-317">Combinación de cuentas de inicio de sesión sociales y locales</span><span class="sxs-lookup"><span data-stu-id="9c1d5-317">Combine social and local login accounts</span></span>

<span data-ttu-id="9c1d5-318">Para completar esta sección, primero debe habilitar un proveedor de autenticación externo.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-318">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="9c1d5-319">Consulte la [autenticación de Facebook, Google y proveedores externos](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="9c1d5-319">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="9c1d5-320">Para combinar cuentas locales y sociales, haga clic en el vínculo de correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-320">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="9c1d5-321">En la siguiente secuencia, "RickAndMSFT@gmail.com" se crea primero como un inicio de sesión local; sin embargo, puede crear primero la cuenta como un inicio de sesión social y luego agregar un inicio de sesión local.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-321">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![Aplicación web: RickAndMSFT@gmail.com usuario autenticado](accconfirm/_static/rick.png)

<span data-ttu-id="9c1d5-323">Haga clic en el vínculo **administrar** .</span><span class="sxs-lookup"><span data-stu-id="9c1d5-323">Click on the **Manage** link.</span></span> <span data-ttu-id="9c1d5-324">Observe los 0 externos (inicios de sesión sociales) asociados a esta cuenta.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-324">Note the 0 external (social logins) associated with this account.</span></span>

![Administrar vista](accconfirm/_static/manage.png)

<span data-ttu-id="9c1d5-326">Haga clic en el vínculo a otro servicio de inicio de sesión y acepte las solicitudes de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-326">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="9c1d5-327">En la imagen siguiente, Facebook es el proveedor de autenticación externo:</span><span class="sxs-lookup"><span data-stu-id="9c1d5-327">In the following image, Facebook is the external authentication provider:</span></span>

![Administrar la vista de inicios de sesión externos enumerando Facebook](accconfirm/_static/fb.png)

<span data-ttu-id="9c1d5-329">Las dos cuentas se han combinado.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-329">The two accounts have been combined.</span></span> <span data-ttu-id="9c1d5-330">Puede iniciar sesión con cualquiera de las dos cuentas.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-330">You are able to sign in with either account.</span></span> <span data-ttu-id="9c1d5-331">Es posible que desee que los usuarios agreguen cuentas locales en caso de que su servicio de autenticación de inicio de sesión social esté inactivo, o más probabilidades de que hayan perdido el acceso a su cuenta de redes sociales.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-331">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="9c1d5-332">Habilitar la confirmación de la cuenta después de que un sitio tenga usuarios</span><span class="sxs-lookup"><span data-stu-id="9c1d5-332">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="9c1d5-333">La habilitación de la confirmación de cuenta en un sitio con usuarios bloquea a todos los usuarios existentes.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-333">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="9c1d5-334">Los usuarios existentes están bloqueados porque sus cuentas no están confirmadas.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-334">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="9c1d5-335">Para solucionar el bloqueo de usuario existente, use uno de los métodos siguientes:</span><span class="sxs-lookup"><span data-stu-id="9c1d5-335">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="9c1d5-336">Actualice la base de datos para marcar todos los usuarios existentes como confirmados.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-336">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="9c1d5-337">Confirme los usuarios existentes.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-337">Confirm existing users.</span></span> <span data-ttu-id="9c1d5-338">Por ejemplo, enviar por lotes correos electrónicos con vínculos de confirmación.</span><span class="sxs-lookup"><span data-stu-id="9c1d5-338">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end
