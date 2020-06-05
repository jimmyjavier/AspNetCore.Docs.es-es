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
ms.openlocfilehash: d5e0e3865702fe4e5cbe49e7f452f367a8a53de9
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2020
ms.locfileid: "84451750"
---
# <a name="account-confirmation-and-password-recovery-in-aspnet-core"></a><span data-ttu-id="16f85-103">Confirmación de las cuentas y recuperación de contraseñas en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="16f85-103">Account confirmation and password recovery in ASP.NET Core</span></span>

<span data-ttu-id="16f85-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant)y [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="16f85-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant), and [Joe Audette](https://twitter.com/joeaudette)</span></span>

<span data-ttu-id="16f85-105">En este tutorial se muestra cómo crear una aplicación ASP.NET Core con confirmación de correo electrónico y restablecimiento de contraseña.</span><span class="sxs-lookup"><span data-stu-id="16f85-105">This tutorial shows how to build an ASP.NET Core app with email confirmation and password reset.</span></span> <span data-ttu-id="16f85-106">Este tutorial **no** es un tema de inicio.</span><span class="sxs-lookup"><span data-stu-id="16f85-106">This tutorial is **not** a beginning topic.</span></span> <span data-ttu-id="16f85-107">Debe estar familiarizado con:</span><span class="sxs-lookup"><span data-stu-id="16f85-107">You should be familiar with:</span></span>

* [<span data-ttu-id="16f85-108">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="16f85-108">ASP.NET Core</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="16f85-109">Autenticación</span><span class="sxs-lookup"><span data-stu-id="16f85-109">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="16f85-110">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="16f85-110">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

<!-- see C:/Dropbox/wrk/Code/SendGridConsole/Program.cs -->

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="16f85-111">Vea [este archivo PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) para obtener la versión ASP.net Core 1,1.</span><span class="sxs-lookup"><span data-stu-id="16f85-111">See [this PDF file](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) for the ASP.NET Core 1.1 version.</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.2"

## <a name="prerequisites"></a><span data-ttu-id="16f85-112">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="16f85-112">Prerequisites</span></span>

[<span data-ttu-id="16f85-113">SDK de .NET Core 3.0 o versiones posteriores</span><span class="sxs-lookup"><span data-stu-id="16f85-113">.NET Core 3.0 SDK or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core/3.0)

## <a name="create-and-test-a-web-app-with-authentication"></a><span data-ttu-id="16f85-114">Crear y probar una aplicación web con autenticación</span><span class="sxs-lookup"><span data-stu-id="16f85-114">Create and test a web app with authentication</span></span>

<span data-ttu-id="16f85-115">Ejecute los siguientes comandos para crear una aplicación web con autenticación.</span><span class="sxs-lookup"><span data-stu-id="16f85-115">Run the following commands to create a web app with authentication.</span></span>

```dotnetcli
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet run
```

<span data-ttu-id="16f85-116">Ejecute la aplicación, seleccione el vínculo **registrar** y registre un usuario.</span><span class="sxs-lookup"><span data-stu-id="16f85-116">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="16f85-117">Una vez registrado, se le redirigirá a la `/Identity/Account/RegisterConfirmation` página a que contiene un vínculo para simular la confirmación de correo electrónico:</span><span class="sxs-lookup"><span data-stu-id="16f85-117">Once registered, you are redirected to the to `/Identity/Account/RegisterConfirmation` page which contains a link to simulate email confirmation:</span></span>

* <span data-ttu-id="16f85-118">Seleccione el `Click here to confirm your account` vínculo.</span><span class="sxs-lookup"><span data-stu-id="16f85-118">Select the `Click here to confirm your account` link.</span></span>
* <span data-ttu-id="16f85-119">Seleccione el vínculo de **Inicio de sesión** e inicie sesión con las mismas credenciales.</span><span class="sxs-lookup"><span data-stu-id="16f85-119">Select the **Login** link and sign-in with the same credentials.</span></span>
* <span data-ttu-id="16f85-120">Seleccione el `Hello YourEmail@provider.com!` vínculo, que le redirigirá a la `/Identity/Account/Manage/PersonalData` página.</span><span class="sxs-lookup"><span data-stu-id="16f85-120">Select the `Hello YourEmail@provider.com!` link, which redirects you to the `/Identity/Account/Manage/PersonalData` page.</span></span>
* <span data-ttu-id="16f85-121">Seleccione la pestaña **datos personales** de la izquierda y, a continuación, seleccione **eliminar**.</span><span class="sxs-lookup"><span data-stu-id="16f85-121">Select the **Personal data** tab on the left, and then select **Delete**.</span></span>

### <a name="configure-an-email-provider"></a><span data-ttu-id="16f85-122">Configuración de un proveedor de correo electrónico</span><span class="sxs-lookup"><span data-stu-id="16f85-122">Configure an email provider</span></span>

<span data-ttu-id="16f85-123">En este tutorial, se usa [SendGrid](https://sendgrid.com) para enviar correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="16f85-123">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="16f85-124">Necesita una cuenta y una clave de SendGrid para enviar el correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="16f85-124">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="16f85-125">Puede usar otros proveedores de correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="16f85-125">You can use other email providers.</span></span> <span data-ttu-id="16f85-126">Se recomienda usar SendGrid u otro servicio de correo electrónico para enviar correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="16f85-126">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="16f85-127">SMTP es difícil de proteger y configurar correctamente.</span><span class="sxs-lookup"><span data-stu-id="16f85-127">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="16f85-128">La cuenta SendGrid My requiere [Agregar un remitente](https://sendgrid.com/docs/ui/sending-email/senders/).</span><span class="sxs-lookup"><span data-stu-id="16f85-128">The SendGrid account my require [adding a Sender](https://sendgrid.com/docs/ui/sending-email/senders/).</span></span>

<span data-ttu-id="16f85-129">Cree una clase para capturar la clave de correo electrónico segura.</span><span class="sxs-lookup"><span data-stu-id="16f85-129">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="16f85-130">Para este ejemplo, cree *Services/AuthMessageSenderOptions. CS*:</span><span class="sxs-lookup"><span data-stu-id="16f85-130">For this sample, create *Services/AuthMessageSenderOptions.cs*:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="16f85-131">Configuración de secretos de usuario de SendGrid</span><span class="sxs-lookup"><span data-stu-id="16f85-131">Configure SendGrid user secrets</span></span>

<span data-ttu-id="16f85-132">Establezca `SendGridUser` y `SendGridKey` con la [herramienta de administrador de secretos](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="16f85-132">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="16f85-133">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="16f85-133">For example:</span></span>

```dotnetcli
dotnet user-secrets set SendGridUser RickAndMSFT
dotnet user-secrets set SendGridKey <key>

Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="16f85-134">En Windows, el administrador de secretos almacena pares de clave/valor en un archivo *Secrets. JSON* en el `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directorio.</span><span class="sxs-lookup"><span data-stu-id="16f85-134">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="16f85-135">El contenido del archivo *Secrets. JSON* no está cifrado.</span><span class="sxs-lookup"><span data-stu-id="16f85-135">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="16f85-136">El marcado siguiente muestra el archivo *Secrets. JSON* .</span><span class="sxs-lookup"><span data-stu-id="16f85-136">The following markup shows the *secrets.json* file.</span></span> <span data-ttu-id="16f85-137">Se ha `SendGridKey` quitado el valor.</span><span class="sxs-lookup"><span data-stu-id="16f85-137">The `SendGridKey` value has been removed.</span></span>

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

<span data-ttu-id="16f85-138">Para obtener más información, vea el [patrón de opciones](xref:fundamentals/configuration/options) y la [configuración](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="16f85-138">For more information, see the [Options pattern](xref:fundamentals/configuration/options) and [configuration](xref:fundamentals/configuration/index).</span></span>

### <a name="install-sendgrid"></a><span data-ttu-id="16f85-139">Instalación de SendGrid</span><span class="sxs-lookup"><span data-stu-id="16f85-139">Install SendGrid</span></span>

<span data-ttu-id="16f85-140">En este tutorial se muestra cómo agregar notificaciones por correo electrónico a través de [SendGrid](https://sendgrid.com/), pero puede enviar correo electrónico mediante SMTP y otros mecanismos.</span><span class="sxs-lookup"><span data-stu-id="16f85-140">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="16f85-141">Instale el `SendGrid` paquete NuGet:</span><span class="sxs-lookup"><span data-stu-id="16f85-141">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="16f85-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="16f85-142">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="16f85-143">En la consola del administrador de paquetes, escriba el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="16f85-143">From the Package Manager Console, enter the following command:</span></span>

```powershell
Install-Package SendGrid
```

# <a name="net-core-cli"></a>[<span data-ttu-id="16f85-144">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="16f85-144">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="16f85-145">En la consola de, escriba el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="16f85-145">From the console, enter the following command:</span></span>

```dotnetcli
dotnet add package SendGrid
```

---

<span data-ttu-id="16f85-146">Consulte Introducción a [SendGrid gratis](https://sendgrid.com/free/) para registrarse para obtener una cuenta gratuita de SendGrid.</span><span class="sxs-lookup"><span data-stu-id="16f85-146">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>

### <a name="implement-iemailsender"></a><span data-ttu-id="16f85-147">Implementación de IEmailSender</span><span class="sxs-lookup"><span data-stu-id="16f85-147">Implement IEmailSender</span></span>

<span data-ttu-id="16f85-148">Para implementar `IEmailSender` , cree *Services/EmailSender. CS* con código similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="16f85-148">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="16f85-149">Configurar el inicio para admitir correo electrónico</span><span class="sxs-lookup"><span data-stu-id="16f85-149">Configure startup to support email</span></span>

<span data-ttu-id="16f85-150">Agregue el código siguiente al `ConfigureServices` método en el archivo *Startup.CS* :</span><span class="sxs-lookup"><span data-stu-id="16f85-150">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="16f85-151">Agregue `EmailSender` como un servicio transitorio.</span><span class="sxs-lookup"><span data-stu-id="16f85-151">Add `EmailSender` as a transient service.</span></span>
* <span data-ttu-id="16f85-152">Registre la `AuthMessageSenderOptions` instancia de configuración.</span><span class="sxs-lookup"><span data-stu-id="16f85-152">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Startup.cs?name=snippet1&highlight=11-15)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="16f85-153">Registro, confirmación de correo electrónico y restablecimiento de contraseña</span><span class="sxs-lookup"><span data-stu-id="16f85-153">Register, confirm email, and reset password</span></span>

<span data-ttu-id="16f85-154">Ejecute la aplicación web y pruebe el flujo de recuperación de la contraseña y la confirmación de la cuenta.</span><span class="sxs-lookup"><span data-stu-id="16f85-154">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="16f85-155">Ejecutar la aplicación y registrar un nuevo usuario</span><span class="sxs-lookup"><span data-stu-id="16f85-155">Run the app and register a new user</span></span>
* <span data-ttu-id="16f85-156">Compruebe su correo electrónico para el vínculo de confirmación de la cuenta.</span><span class="sxs-lookup"><span data-stu-id="16f85-156">Check your email for the account confirmation link.</span></span> <span data-ttu-id="16f85-157">Consulte [depurar correo electrónico](#debug) si no recibe el correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="16f85-157">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="16f85-158">Haga clic en el vínculo para confirmar el correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="16f85-158">Click the link to confirm your email.</span></span>
* <span data-ttu-id="16f85-159">Inicie sesión con su correo electrónico y contraseña.</span><span class="sxs-lookup"><span data-stu-id="16f85-159">Sign in with your email and password.</span></span>
* <span data-ttu-id="16f85-160">Cierre la sesión.</span><span class="sxs-lookup"><span data-stu-id="16f85-160">Sign out.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="16f85-161">Restablecimiento de la contraseña de prueba</span><span class="sxs-lookup"><span data-stu-id="16f85-161">Test password reset</span></span>

* <span data-ttu-id="16f85-162">Si ha iniciado sesión, seleccione **Logout**.</span><span class="sxs-lookup"><span data-stu-id="16f85-162">If you're signed in, select **Logout**.</span></span>
* <span data-ttu-id="16f85-163">Seleccione el vínculo **iniciar sesión** y seleccione el vínculo **¿olvidó su contraseña?** .</span><span class="sxs-lookup"><span data-stu-id="16f85-163">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="16f85-164">Escriba el correo electrónico que usó para registrar la cuenta.</span><span class="sxs-lookup"><span data-stu-id="16f85-164">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="16f85-165">Se envía un correo electrónico con un vínculo para restablecer la contraseña.</span><span class="sxs-lookup"><span data-stu-id="16f85-165">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="16f85-166">Compruebe su correo electrónico y haga clic en el vínculo para restablecer la contraseña.</span><span class="sxs-lookup"><span data-stu-id="16f85-166">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="16f85-167">Una vez que la contraseña se haya restablecido correctamente, puede iniciar sesión con el correo electrónico y la nueva contraseña.</span><span class="sxs-lookup"><span data-stu-id="16f85-167">After your password has been successfully reset, you can sign in with your email and new password.</span></span>

## <a name="change-email-and-activity-timeout"></a><span data-ttu-id="16f85-168">Cambiar el tiempo de espera del correo electrónico y la actividad</span><span class="sxs-lookup"><span data-stu-id="16f85-168">Change email and activity timeout</span></span>

<span data-ttu-id="16f85-169">El tiempo de espera de inactividad predeterminado es de 14 días.</span><span class="sxs-lookup"><span data-stu-id="16f85-169">The default inactivity timeout is 14 days.</span></span> <span data-ttu-id="16f85-170">El código siguiente establece el tiempo de espera de inactividad en 5 días:</span><span class="sxs-lookup"><span data-stu-id="16f85-170">The following code sets the inactivity timeout to 5 days:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a><span data-ttu-id="16f85-171">Cambiar todas las vigencias de los tokens de protección de datos</span><span class="sxs-lookup"><span data-stu-id="16f85-171">Change all data protection token lifespans</span></span>

<span data-ttu-id="16f85-172">El siguiente código cambia el tiempo de espera de todos los tokens de protección de datos a 3 horas:</span><span class="sxs-lookup"><span data-stu-id="16f85-172">The following code changes all data protection tokens timeout period to 3 hours:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAllTokens.cs?name=snippet1&highlight=11-12)]

<span data-ttu-id="16f85-173">Los Identity tokens de usuario integrados (consulte [AspNetCore/src/ Identity /extensions.Core/src/TokenOptions.CS](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) tienen un [tiempo de espera](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs)de un día.</span><span class="sxs-lookup"><span data-stu-id="16f85-173">The built in Identity user tokens (see [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )have a [one day timeout](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span>

### <a name="change-the-email-token-lifespan"></a><span data-ttu-id="16f85-174">Cambiar la duración del token de correo electrónico</span><span class="sxs-lookup"><span data-stu-id="16f85-174">Change the email token lifespan</span></span>

<span data-ttu-id="16f85-175">La duración predeterminada del token de [los Identity tokens de usuario](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) es de [un día](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="16f85-175">The default token lifespan of [the Identity user tokens](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) is [one day](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span> <span data-ttu-id="16f85-176">En esta sección se muestra cómo cambiar la duración del token de correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="16f85-176">This section shows how to change the email token lifespan.</span></span>

<span data-ttu-id="16f85-177">Agregue un [DataProtectorTokenProvider \<TUser> ](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) personalizado y <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions> :</span><span class="sxs-lookup"><span data-stu-id="16f85-177">Add a custom [DataProtectorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) and <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

<span data-ttu-id="16f85-178">Agregue el proveedor personalizado al contenedor de servicio:</span><span class="sxs-lookup"><span data-stu-id="16f85-178">Add the custom provider to the service container:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupEmail.cs?name=snippet1&highlight=10-16)]

### <a name="resend-email-confirmation"></a><span data-ttu-id="16f85-179">Confirmación de reenvío de correo electrónico</span><span class="sxs-lookup"><span data-stu-id="16f85-179">Resend email confirmation</span></span>

<span data-ttu-id="16f85-180">Consulte [este problema de GitHub](https://github.com/dotnet/AspNetCore/issues/5410).</span><span class="sxs-lookup"><span data-stu-id="16f85-180">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5410).</span></span>

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="16f85-181">Depurar correo electrónico</span><span class="sxs-lookup"><span data-stu-id="16f85-181">Debug email</span></span>

<span data-ttu-id="16f85-182">Si no consigue que el correo electrónico funcione:</span><span class="sxs-lookup"><span data-stu-id="16f85-182">If you can't get email working:</span></span>

* <span data-ttu-id="16f85-183">Establezca un punto de interrupción en `EmailSender.Execute` para comprobar que `SendGridClient.SendEmailAsync` se llama a.</span><span class="sxs-lookup"><span data-stu-id="16f85-183">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="16f85-184">Cree una [aplicación de consola para enviar correo electrónico](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) mediante código similar a `EmailSender.Execute` .</span><span class="sxs-lookup"><span data-stu-id="16f85-184">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="16f85-185">Revise la página [actividad de correo electrónico](https://sendgrid.com/docs/User_Guide/email_activity.html) .</span><span class="sxs-lookup"><span data-stu-id="16f85-185">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="16f85-186">Compruebe la carpeta de correo no deseado.</span><span class="sxs-lookup"><span data-stu-id="16f85-186">Check your spam folder.</span></span>
* <span data-ttu-id="16f85-187">Pruebe otro alias de correo electrónico en un proveedor de correo electrónico diferente (Microsoft, Yahoo, gmail, etc.).</span><span class="sxs-lookup"><span data-stu-id="16f85-187">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="16f85-188">Intente enviar a diferentes cuentas de correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="16f85-188">Try sending to different email accounts.</span></span>

<span data-ttu-id="16f85-189">**Un procedimiento** recomendado de seguridad es **no** usar secretos de producción en pruebas y desarrollo.</span><span class="sxs-lookup"><span data-stu-id="16f85-189">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="16f85-190">Si publica la aplicación en Azure, establezca los secretos de SendGrid como configuración de la aplicación en el portal de aplicaciones Web de Azure.</span><span class="sxs-lookup"><span data-stu-id="16f85-190">If you publish the app to Azure, set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="16f85-191">El sistema de configuración está configurado para leer las claves de las variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="16f85-191">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="16f85-192">Combinación de cuentas de inicio de sesión sociales y locales</span><span class="sxs-lookup"><span data-stu-id="16f85-192">Combine social and local login accounts</span></span>

<span data-ttu-id="16f85-193">Para completar esta sección, primero debe habilitar un proveedor de autenticación externo.</span><span class="sxs-lookup"><span data-stu-id="16f85-193">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="16f85-194">Consulte la [autenticación de Facebook, Google y proveedores externos](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="16f85-194">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="16f85-195">Para combinar cuentas locales y sociales, haga clic en el vínculo de correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="16f85-195">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="16f85-196">En la siguiente secuencia, " RickAndMSFT@gmail.com " se crea primero como un inicio de sesión local; sin embargo, puede crear primero la cuenta como un inicio de sesión social y luego agregar un inicio de sesión local.</span><span class="sxs-lookup"><span data-stu-id="16f85-196">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![Aplicación web: RickAndMSFT@gmail.com usuario autenticado](accconfirm/_static/rick.png)

<span data-ttu-id="16f85-198">Haga clic en el vínculo **administrar** .</span><span class="sxs-lookup"><span data-stu-id="16f85-198">Click on the **Manage** link.</span></span> <span data-ttu-id="16f85-199">Observe los 0 externos (inicios de sesión sociales) asociados a esta cuenta.</span><span class="sxs-lookup"><span data-stu-id="16f85-199">Note the 0 external (social logins) associated with this account.</span></span>

![Administrar vista](accconfirm/_static/manage.png)

<span data-ttu-id="16f85-201">Haga clic en el vínculo a otro servicio de inicio de sesión y acepte las solicitudes de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="16f85-201">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="16f85-202">En la imagen siguiente, Facebook es el proveedor de autenticación externo:</span><span class="sxs-lookup"><span data-stu-id="16f85-202">In the following image, Facebook is the external authentication provider:</span></span>

![Administrar la vista de inicios de sesión externos enumerando Facebook](accconfirm/_static/fb.png)

<span data-ttu-id="16f85-204">Las dos cuentas se han combinado.</span><span class="sxs-lookup"><span data-stu-id="16f85-204">The two accounts have been combined.</span></span> <span data-ttu-id="16f85-205">Puede iniciar sesión con cualquiera de las dos cuentas.</span><span class="sxs-lookup"><span data-stu-id="16f85-205">You are able to sign in with either account.</span></span> <span data-ttu-id="16f85-206">Es posible que desee que los usuarios agreguen cuentas locales en caso de que su servicio de autenticación de inicio de sesión social esté inactivo, o más probabilidades de que hayan perdido el acceso a su cuenta de redes sociales.</span><span class="sxs-lookup"><span data-stu-id="16f85-206">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="16f85-207">Habilitar la confirmación de la cuenta después de que un sitio tenga usuarios</span><span class="sxs-lookup"><span data-stu-id="16f85-207">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="16f85-208">La habilitación de la confirmación de cuenta en un sitio con usuarios bloquea a todos los usuarios existentes.</span><span class="sxs-lookup"><span data-stu-id="16f85-208">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="16f85-209">Los usuarios existentes están bloqueados porque sus cuentas no están confirmadas.</span><span class="sxs-lookup"><span data-stu-id="16f85-209">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="16f85-210">Para solucionar el bloqueo de usuario existente, use uno de los métodos siguientes:</span><span class="sxs-lookup"><span data-stu-id="16f85-210">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="16f85-211">Actualice la base de datos para marcar todos los usuarios existentes como confirmados.</span><span class="sxs-lookup"><span data-stu-id="16f85-211">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="16f85-212">Confirme los usuarios existentes.</span><span class="sxs-lookup"><span data-stu-id="16f85-212">Confirm existing users.</span></span> <span data-ttu-id="16f85-213">Por ejemplo, enviar por lotes correos electrónicos con vínculos de confirmación.</span><span class="sxs-lookup"><span data-stu-id="16f85-213">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.0 < aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="16f85-214">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="16f85-214">Prerequisites</span></span>

[<span data-ttu-id="16f85-215">SDK de .NET Core 2,2 o posterior</span><span class="sxs-lookup"><span data-stu-id="16f85-215">.NET Core 2.2 SDK or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)

## <a name="create-a-web--app-and-scaffold-identity"></a><span data-ttu-id="16f85-216">Creación de una aplicación web y scaffoldingIdentity</span><span class="sxs-lookup"><span data-stu-id="16f85-216">Create a web  app and scaffold Identity</span></span>

<span data-ttu-id="16f85-217">Ejecute los siguientes comandos para crear una aplicación web con autenticación.</span><span class="sxs-lookup"><span data-stu-id="16f85-217">Run the following commands to create a web app with authentication.</span></span>

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

## <a name="test-new-user-registration"></a><span data-ttu-id="16f85-218">Prueba del nuevo registro de usuario</span><span class="sxs-lookup"><span data-stu-id="16f85-218">Test new user registration</span></span>

<span data-ttu-id="16f85-219">Ejecute la aplicación, seleccione el vínculo **registrar** y registre un usuario.</span><span class="sxs-lookup"><span data-stu-id="16f85-219">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="16f85-220">En este momento, la única validación en el correo electrónico es con el [`[EmailAddress]`](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) atributo.</span><span class="sxs-lookup"><span data-stu-id="16f85-220">At this point, the only validation on the email is with the [`[EmailAddress]`](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) attribute.</span></span> <span data-ttu-id="16f85-221">Después de enviar el registro, ha iniciado sesión en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="16f85-221">After submitting the registration, you are logged into the app.</span></span> <span data-ttu-id="16f85-222">Más adelante en el tutorial, se actualiza el código para que los nuevos usuarios no puedan iniciar sesión hasta que se valide su correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="16f85-222">Later in the tutorial, the code is updated so new users can't sign in until their email is validated.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<span data-ttu-id="16f85-223">Tenga en cuenta que el campo de la tabla `EmailConfirmed` es `False` .</span><span class="sxs-lookup"><span data-stu-id="16f85-223">Note the table's `EmailConfirmed` field is `False`.</span></span>

<span data-ttu-id="16f85-224">Es posible que desee usar este correo electrónico de nuevo en el paso siguiente cuando la aplicación envíe un correo electrónico de confirmación.</span><span class="sxs-lookup"><span data-stu-id="16f85-224">You might want to use this email again in the next step when the app sends a confirmation email.</span></span> <span data-ttu-id="16f85-225">Haga clic con el botón derecho en la fila y seleccione **eliminar**.</span><span class="sxs-lookup"><span data-stu-id="16f85-225">Right-click on the row and select **Delete**.</span></span> <span data-ttu-id="16f85-226">Al eliminar el alias de correo electrónico, se facilitan los pasos siguientes.</span><span class="sxs-lookup"><span data-stu-id="16f85-226">Deleting the email alias makes it easier in the following steps.</span></span>

<a name="prevent-login-at-registration"></a>

## <a name="require-email-confirmation"></a><span data-ttu-id="16f85-227">Requerir confirmación de correo electrónico</span><span class="sxs-lookup"><span data-stu-id="16f85-227">Require email confirmation</span></span>

<span data-ttu-id="16f85-228">Se recomienda confirmar el correo electrónico de un nuevo registro de usuario.</span><span class="sxs-lookup"><span data-stu-id="16f85-228">It's a best practice to confirm the email of a new user registration.</span></span> <span data-ttu-id="16f85-229">La confirmación por correo electrónico ayuda a comprobar que no está suplantando a otra persona (es decir, que no se han registrado con el correo electrónico de otro usuario).</span><span class="sxs-lookup"><span data-stu-id="16f85-229">Email confirmation helps to verify they're not impersonating someone else (that is, they haven't registered with someone else's email).</span></span> <span data-ttu-id="16f85-230">Supongamos que tiene un foro de discusión y desea evitar que " yli@example.com " se registre como " nolivetto@contoso.com ".</span><span class="sxs-lookup"><span data-stu-id="16f85-230">Suppose you had a discussion forum, and you wanted to prevent "yli@example.com" from registering as "nolivetto@contoso.com".</span></span> <span data-ttu-id="16f85-231">Sin confirmación por correo electrónico, " nolivetto@contoso.com " podría recibir correo electrónico no deseado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="16f85-231">Without email confirmation, "nolivetto@contoso.com" could receive unwanted email from your app.</span></span> <span data-ttu-id="16f85-232">Supongamos que el usuario se registró accidentalmente como " ylo@example.com " y no detectó la falta de ortografía de "Yli".</span><span class="sxs-lookup"><span data-stu-id="16f85-232">Suppose the user accidentally registered as "ylo@example.com" and hadn't noticed the misspelling of "yli".</span></span> <span data-ttu-id="16f85-233">No podrán usar la recuperación de contraseña porque la aplicación no tiene el correo electrónico correcto.</span><span class="sxs-lookup"><span data-stu-id="16f85-233">They wouldn't be able to use password recovery because the app doesn't have their correct email.</span></span> <span data-ttu-id="16f85-234">La confirmación por correo electrónico proporciona una protección limitada de bots.</span><span class="sxs-lookup"><span data-stu-id="16f85-234">Email confirmation provides limited protection from bots.</span></span> <span data-ttu-id="16f85-235">La confirmación por correo electrónico no proporciona protección contra usuarios malintencionados con muchas cuentas de correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="16f85-235">Email confirmation doesn't provide protection from malicious users with many email accounts.</span></span>

<span data-ttu-id="16f85-236">Por lo general, querrá evitar que los nuevos usuarios publiquen datos en el sitio Web antes de que tengan un correo electrónico confirmado.</span><span class="sxs-lookup"><span data-stu-id="16f85-236">You generally want to prevent new users from posting any data to your web site before they have a confirmed email.</span></span>

<span data-ttu-id="16f85-237">Actualización `Startup.ConfigureServices` para requerir un correo electrónico confirmado:</span><span class="sxs-lookup"><span data-stu-id="16f85-237">Update `Startup.ConfigureServices`  to require a confirmed email:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=8-11)]

<span data-ttu-id="16f85-238">`config.SignIn.RequireConfirmedEmail = true;`impide que los usuarios registrados inicien sesión hasta que se confirme su correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="16f85-238">`config.SignIn.RequireConfirmedEmail = true;` prevents registered users from logging in until their email is confirmed.</span></span>

### <a name="configure-email-provider"></a><span data-ttu-id="16f85-239">Configurar proveedor de correo electrónico</span><span class="sxs-lookup"><span data-stu-id="16f85-239">Configure email provider</span></span>

<span data-ttu-id="16f85-240">En este tutorial, se usa [SendGrid](https://sendgrid.com) para enviar correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="16f85-240">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="16f85-241">Necesita una cuenta y una clave de SendGrid para enviar el correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="16f85-241">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="16f85-242">Puede usar otros proveedores de correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="16f85-242">You can use other email providers.</span></span> <span data-ttu-id="16f85-243">ASP.NET Core 2. x incluye `System.Net.Mail` , que le permite enviar correo electrónico desde su aplicación.</span><span class="sxs-lookup"><span data-stu-id="16f85-243">ASP.NET Core 2.x includes `System.Net.Mail`, which allows you to send email from your app.</span></span> <span data-ttu-id="16f85-244">Se recomienda usar SendGrid u otro servicio de correo electrónico para enviar correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="16f85-244">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="16f85-245">SMTP es difícil de proteger y configurar correctamente.</span><span class="sxs-lookup"><span data-stu-id="16f85-245">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="16f85-246">Cree una clase para capturar la clave de correo electrónico segura.</span><span class="sxs-lookup"><span data-stu-id="16f85-246">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="16f85-247">Para este ejemplo, cree *Services/AuthMessageSenderOptions. CS*:</span><span class="sxs-lookup"><span data-stu-id="16f85-247">For this sample, create *Services/AuthMessageSenderOptions.cs*:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="16f85-248">Configuración de secretos de usuario de SendGrid</span><span class="sxs-lookup"><span data-stu-id="16f85-248">Configure SendGrid user secrets</span></span>

<span data-ttu-id="16f85-249">Establezca `SendGridUser` y `SendGridKey` con la [herramienta de administrador de secretos](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="16f85-249">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="16f85-250">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="16f85-250">For example:</span></span>

```console
C:/WebAppl>dotnet user-secrets set SendGridUser RickAndMSFT
info: Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="16f85-251">En Windows, el administrador de secretos almacena pares de clave/valor en un archivo *Secrets. JSON* en el `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directorio.</span><span class="sxs-lookup"><span data-stu-id="16f85-251">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="16f85-252">El contenido del archivo *Secrets. JSON* no está cifrado.</span><span class="sxs-lookup"><span data-stu-id="16f85-252">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="16f85-253">El marcado siguiente muestra el archivo *Secrets. JSON* .</span><span class="sxs-lookup"><span data-stu-id="16f85-253">The following markup shows the *secrets.json* file.</span></span> <span data-ttu-id="16f85-254">Se ha `SendGridKey` quitado el valor.</span><span class="sxs-lookup"><span data-stu-id="16f85-254">The `SendGridKey` value has been removed.</span></span>

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

<span data-ttu-id="16f85-255">Para obtener más información, vea el [patrón de opciones](xref:fundamentals/configuration/options) y la [configuración](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="16f85-255">For more information, see the [Options pattern](xref:fundamentals/configuration/options) and [configuration](xref:fundamentals/configuration/index).</span></span>

### <a name="install-sendgrid"></a><span data-ttu-id="16f85-256">Instalación de SendGrid</span><span class="sxs-lookup"><span data-stu-id="16f85-256">Install SendGrid</span></span>

<span data-ttu-id="16f85-257">En este tutorial se muestra cómo agregar notificaciones por correo electrónico a través de [SendGrid](https://sendgrid.com/), pero puede enviar correo electrónico mediante SMTP y otros mecanismos.</span><span class="sxs-lookup"><span data-stu-id="16f85-257">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="16f85-258">Instale el `SendGrid` paquete NuGet:</span><span class="sxs-lookup"><span data-stu-id="16f85-258">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="16f85-259">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="16f85-259">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="16f85-260">En la consola del administrador de paquetes, escriba el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="16f85-260">From the Package Manager Console, enter the following command:</span></span>

```powershell
Install-Package SendGrid
```

# <a name="net-core-cli"></a>[<span data-ttu-id="16f85-261">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="16f85-261">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="16f85-262">En la consola de, escriba el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="16f85-262">From the console, enter the following command:</span></span>

```dotnetcli
dotnet add package SendGrid
```

---

<span data-ttu-id="16f85-263">Consulte Introducción a [SendGrid gratis](https://sendgrid.com/free/) para registrarse para obtener una cuenta gratuita de SendGrid.</span><span class="sxs-lookup"><span data-stu-id="16f85-263">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>

### <a name="implement-iemailsender"></a><span data-ttu-id="16f85-264">Implementación de IEmailSender</span><span class="sxs-lookup"><span data-stu-id="16f85-264">Implement IEmailSender</span></span>

<span data-ttu-id="16f85-265">Para implementar `IEmailSender` , cree *Services/EmailSender. CS* con código similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="16f85-265">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="16f85-266">Configurar el inicio para admitir correo electrónico</span><span class="sxs-lookup"><span data-stu-id="16f85-266">Configure startup to support email</span></span>

<span data-ttu-id="16f85-267">Agregue el código siguiente al `ConfigureServices` método en el archivo *Startup.CS* :</span><span class="sxs-lookup"><span data-stu-id="16f85-267">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="16f85-268">Agregue `EmailSender` como un servicio transitorio.</span><span class="sxs-lookup"><span data-stu-id="16f85-268">Add `EmailSender` as a transient service.</span></span>
* <span data-ttu-id="16f85-269">Registre la `AuthMessageSenderOptions` instancia de configuración.</span><span class="sxs-lookup"><span data-stu-id="16f85-269">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=15-99)]

## <a name="enable-account-confirmation-and-password-recovery"></a><span data-ttu-id="16f85-270">Habilitar la confirmación de la cuenta y la recuperación de la contraseña</span><span class="sxs-lookup"><span data-stu-id="16f85-270">Enable account confirmation and password recovery</span></span>

<span data-ttu-id="16f85-271">La plantilla tiene el código para la confirmación de la cuenta y la recuperación de la contraseña.</span><span class="sxs-lookup"><span data-stu-id="16f85-271">The template has the code for account confirmation and password recovery.</span></span> <span data-ttu-id="16f85-272">Busque el `OnPostAsync` método en *areas/ Identity /pages/Account/Register.cshtml.CS*.</span><span class="sxs-lookup"><span data-stu-id="16f85-272">Find the `OnPostAsync` method in *Areas/Identity/Pages/Account/Register.cshtml.cs*.</span></span>

<span data-ttu-id="16f85-273">Evite que los usuarios recién registrados inicien sesión automáticamente al comentar la siguiente línea:</span><span class="sxs-lookup"><span data-stu-id="16f85-273">Prevent newly registered users from being automatically signed in by commenting out the following line:</span></span>

```csharp
await _signInManager.SignInAsync(user, isPersistent: false);
```

<span data-ttu-id="16f85-274">El método complete se muestra con la línea resaltada:</span><span class="sxs-lookup"><span data-stu-id="16f85-274">The complete method is shown with the changed line highlighted:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Areas/Identity/Pages/Account/Register.cshtml.cs?highlight=22&name=snippet_Register)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="16f85-275">Registro, confirmación de correo electrónico y restablecimiento de contraseña</span><span class="sxs-lookup"><span data-stu-id="16f85-275">Register, confirm email, and reset password</span></span>

<span data-ttu-id="16f85-276">Ejecute la aplicación web y pruebe el flujo de recuperación de la contraseña y la confirmación de la cuenta.</span><span class="sxs-lookup"><span data-stu-id="16f85-276">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="16f85-277">Ejecutar la aplicación y registrar un nuevo usuario</span><span class="sxs-lookup"><span data-stu-id="16f85-277">Run the app and register a new user</span></span>
* <span data-ttu-id="16f85-278">Compruebe su correo electrónico para el vínculo de confirmación de la cuenta.</span><span class="sxs-lookup"><span data-stu-id="16f85-278">Check your email for the account confirmation link.</span></span> <span data-ttu-id="16f85-279">Consulte [depurar correo electrónico](#debug) si no recibe el correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="16f85-279">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="16f85-280">Haga clic en el vínculo para confirmar el correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="16f85-280">Click the link to confirm your email.</span></span>
* <span data-ttu-id="16f85-281">Inicie sesión con su correo electrónico y contraseña.</span><span class="sxs-lookup"><span data-stu-id="16f85-281">Sign in with your email and password.</span></span>
* <span data-ttu-id="16f85-282">Cierre la sesión.</span><span class="sxs-lookup"><span data-stu-id="16f85-282">Sign out.</span></span>

### <a name="view-the-manage-page"></a><span data-ttu-id="16f85-283">Ver la página administrar</span><span class="sxs-lookup"><span data-stu-id="16f85-283">View the manage page</span></span>

<span data-ttu-id="16f85-284">Seleccione el nombre de usuario en el explorador: ![ ventana del explorador con el nombre de usuario](accconfirm/_static/un.png)</span><span class="sxs-lookup"><span data-stu-id="16f85-284">Select your user name in the browser: ![browser window with user name](accconfirm/_static/un.png)</span></span>

<span data-ttu-id="16f85-285">La página Administrar se muestra con la pestaña **perfil** seleccionada.</span><span class="sxs-lookup"><span data-stu-id="16f85-285">The manage page is displayed with the **Profile** tab selected.</span></span> <span data-ttu-id="16f85-286">El **correo electrónico** muestra una casilla que indica que se ha confirmado el correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="16f85-286">The **Email** shows a check box indicating the email has been confirmed.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="16f85-287">Restablecimiento de la contraseña de prueba</span><span class="sxs-lookup"><span data-stu-id="16f85-287">Test password reset</span></span>

* <span data-ttu-id="16f85-288">Si ha iniciado sesión, seleccione **Logout**.</span><span class="sxs-lookup"><span data-stu-id="16f85-288">If you're signed in, select **Logout**.</span></span>
* <span data-ttu-id="16f85-289">Seleccione el vínculo **iniciar sesión** y seleccione el vínculo **¿olvidó su contraseña?** .</span><span class="sxs-lookup"><span data-stu-id="16f85-289">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="16f85-290">Escriba el correo electrónico que usó para registrar la cuenta.</span><span class="sxs-lookup"><span data-stu-id="16f85-290">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="16f85-291">Se envía un correo electrónico con un vínculo para restablecer la contraseña.</span><span class="sxs-lookup"><span data-stu-id="16f85-291">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="16f85-292">Compruebe su correo electrónico y haga clic en el vínculo para restablecer la contraseña.</span><span class="sxs-lookup"><span data-stu-id="16f85-292">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="16f85-293">Una vez que la contraseña se haya restablecido correctamente, puede iniciar sesión con el correo electrónico y la nueva contraseña.</span><span class="sxs-lookup"><span data-stu-id="16f85-293">After your password has been successfully reset, you can sign in with your email and new password.</span></span>

## <a name="change-email-and-activity-timeout"></a><span data-ttu-id="16f85-294">Cambiar el tiempo de espera del correo electrónico y la actividad</span><span class="sxs-lookup"><span data-stu-id="16f85-294">Change email and activity timeout</span></span>

<span data-ttu-id="16f85-295">El tiempo de espera de inactividad predeterminado es de 14 días.</span><span class="sxs-lookup"><span data-stu-id="16f85-295">The default inactivity timeout is 14 days.</span></span> <span data-ttu-id="16f85-296">El código siguiente establece el tiempo de espera de inactividad en 5 días:</span><span class="sxs-lookup"><span data-stu-id="16f85-296">The following code sets the inactivity timeout to 5 days:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a><span data-ttu-id="16f85-297">Cambiar todas las vigencias de los tokens de protección de datos</span><span class="sxs-lookup"><span data-stu-id="16f85-297">Change all data protection token lifespans</span></span>

<span data-ttu-id="16f85-298">El siguiente código cambia el tiempo de espera de todos los tokens de protección de datos a 3 horas:</span><span class="sxs-lookup"><span data-stu-id="16f85-298">The following code changes all data protection tokens timeout period to 3 hours:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAllTokens.cs?name=snippet1&highlight=15-16)]

<span data-ttu-id="16f85-299">Los Identity tokens de usuario integrados (consulte [AspNetCore/src/ Identity /extensions.Core/src/TokenOptions.CS](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) tienen un [tiempo de espera](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs)de un día.</span><span class="sxs-lookup"><span data-stu-id="16f85-299">The built in Identity user tokens (see [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )have a [one day timeout](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span>

### <a name="change-the-email-token-lifespan"></a><span data-ttu-id="16f85-300">Cambiar la duración del token de correo electrónico</span><span class="sxs-lookup"><span data-stu-id="16f85-300">Change the email token lifespan</span></span>

<span data-ttu-id="16f85-301">La duración predeterminada del token de [los Identity tokens de usuario](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) es de [un día](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="16f85-301">The default token lifespan of [the Identity user tokens](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) is [one day](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span> <span data-ttu-id="16f85-302">En esta sección se muestra cómo cambiar la duración del token de correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="16f85-302">This section shows how to change the email token lifespan.</span></span>

<span data-ttu-id="16f85-303">Agregue un [DataProtectorTokenProvider \<TUser> ](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) personalizado y <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions> :</span><span class="sxs-lookup"><span data-stu-id="16f85-303">Add a custom [DataProtectorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) and <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

<span data-ttu-id="16f85-304">Agregue el proveedor personalizado al contenedor de servicio:</span><span class="sxs-lookup"><span data-stu-id="16f85-304">Add the custom provider to the service container:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupEmail.cs?name=snippet1&highlight=10-13,18)]

### <a name="resend-email-confirmation"></a><span data-ttu-id="16f85-305">Confirmación de reenvío de correo electrónico</span><span class="sxs-lookup"><span data-stu-id="16f85-305">Resend email confirmation</span></span>

<span data-ttu-id="16f85-306">Consulte [este problema de GitHub](https://github.com/dotnet/AspNetCore/issues/5410).</span><span class="sxs-lookup"><span data-stu-id="16f85-306">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5410).</span></span>

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="16f85-307">Depurar correo electrónico</span><span class="sxs-lookup"><span data-stu-id="16f85-307">Debug email</span></span>

<span data-ttu-id="16f85-308">Si no consigue que el correo electrónico funcione:</span><span class="sxs-lookup"><span data-stu-id="16f85-308">If you can't get email working:</span></span>

* <span data-ttu-id="16f85-309">Establezca un punto de interrupción en `EmailSender.Execute` para comprobar que `SendGridClient.SendEmailAsync` se llama a.</span><span class="sxs-lookup"><span data-stu-id="16f85-309">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="16f85-310">Cree una [aplicación de consola para enviar correo electrónico](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) mediante código similar a `EmailSender.Execute` .</span><span class="sxs-lookup"><span data-stu-id="16f85-310">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="16f85-311">Revise la página [actividad de correo electrónico](https://sendgrid.com/docs/User_Guide/email_activity.html) .</span><span class="sxs-lookup"><span data-stu-id="16f85-311">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="16f85-312">Compruebe la carpeta de correo no deseado.</span><span class="sxs-lookup"><span data-stu-id="16f85-312">Check your spam folder.</span></span>
* <span data-ttu-id="16f85-313">Pruebe otro alias de correo electrónico en un proveedor de correo electrónico diferente (Microsoft, Yahoo, gmail, etc.).</span><span class="sxs-lookup"><span data-stu-id="16f85-313">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="16f85-314">Intente enviar a diferentes cuentas de correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="16f85-314">Try sending to different email accounts.</span></span>

<span data-ttu-id="16f85-315">**Un procedimiento** recomendado de seguridad es **no** usar secretos de producción en pruebas y desarrollo.</span><span class="sxs-lookup"><span data-stu-id="16f85-315">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="16f85-316">Si publica la aplicación en Azure, puede establecer los secretos de SendGrid como configuración de la aplicación en el portal de aplicaciones Web de Azure.</span><span class="sxs-lookup"><span data-stu-id="16f85-316">If you publish the app to Azure, you can set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="16f85-317">El sistema de configuración está configurado para leer las claves de las variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="16f85-317">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="16f85-318">Combinación de cuentas de inicio de sesión sociales y locales</span><span class="sxs-lookup"><span data-stu-id="16f85-318">Combine social and local login accounts</span></span>

<span data-ttu-id="16f85-319">Para completar esta sección, primero debe habilitar un proveedor de autenticación externo.</span><span class="sxs-lookup"><span data-stu-id="16f85-319">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="16f85-320">Consulte la [autenticación de Facebook, Google y proveedores externos](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="16f85-320">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="16f85-321">Para combinar cuentas locales y sociales, haga clic en el vínculo de correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="16f85-321">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="16f85-322">En la siguiente secuencia, " RickAndMSFT@gmail.com " se crea primero como un inicio de sesión local; sin embargo, puede crear primero la cuenta como un inicio de sesión social y luego agregar un inicio de sesión local.</span><span class="sxs-lookup"><span data-stu-id="16f85-322">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![Aplicación web: RickAndMSFT@gmail.com usuario autenticado](accconfirm/_static/rick.png)

<span data-ttu-id="16f85-324">Haga clic en el vínculo **administrar** .</span><span class="sxs-lookup"><span data-stu-id="16f85-324">Click on the **Manage** link.</span></span> <span data-ttu-id="16f85-325">Observe los 0 externos (inicios de sesión sociales) asociados a esta cuenta.</span><span class="sxs-lookup"><span data-stu-id="16f85-325">Note the 0 external (social logins) associated with this account.</span></span>

![Administrar vista](accconfirm/_static/manage.png)

<span data-ttu-id="16f85-327">Haga clic en el vínculo a otro servicio de inicio de sesión y acepte las solicitudes de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="16f85-327">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="16f85-328">En la imagen siguiente, Facebook es el proveedor de autenticación externo:</span><span class="sxs-lookup"><span data-stu-id="16f85-328">In the following image, Facebook is the external authentication provider:</span></span>

![Administrar la vista de inicios de sesión externos enumerando Facebook](accconfirm/_static/fb.png)

<span data-ttu-id="16f85-330">Las dos cuentas se han combinado.</span><span class="sxs-lookup"><span data-stu-id="16f85-330">The two accounts have been combined.</span></span> <span data-ttu-id="16f85-331">Puede iniciar sesión con cualquiera de las dos cuentas.</span><span class="sxs-lookup"><span data-stu-id="16f85-331">You are able to sign in with either account.</span></span> <span data-ttu-id="16f85-332">Es posible que desee que los usuarios agreguen cuentas locales en caso de que su servicio de autenticación de inicio de sesión social esté inactivo, o más probabilidades de que hayan perdido el acceso a su cuenta de redes sociales.</span><span class="sxs-lookup"><span data-stu-id="16f85-332">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="16f85-333">Habilitar la confirmación de la cuenta después de que un sitio tenga usuarios</span><span class="sxs-lookup"><span data-stu-id="16f85-333">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="16f85-334">La habilitación de la confirmación de cuenta en un sitio con usuarios bloquea a todos los usuarios existentes.</span><span class="sxs-lookup"><span data-stu-id="16f85-334">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="16f85-335">Los usuarios existentes están bloqueados porque sus cuentas no están confirmadas.</span><span class="sxs-lookup"><span data-stu-id="16f85-335">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="16f85-336">Para solucionar el bloqueo de usuario existente, use uno de los métodos siguientes:</span><span class="sxs-lookup"><span data-stu-id="16f85-336">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="16f85-337">Actualice la base de datos para marcar todos los usuarios existentes como confirmados.</span><span class="sxs-lookup"><span data-stu-id="16f85-337">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="16f85-338">Confirme los usuarios existentes.</span><span class="sxs-lookup"><span data-stu-id="16f85-338">Confirm existing users.</span></span> <span data-ttu-id="16f85-339">Por ejemplo, enviar por lotes correos electrónicos con vínculos de confirmación.</span><span class="sxs-lookup"><span data-stu-id="16f85-339">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end
