---
title: Scaffolding Identity en proyectos de ASP.net Core
author: rick-anderson
description: Obtenga información sobre cómo Identity aplicar la técnica scaffolding en un proyecto ASP.net Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/1/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/scaffold-identity
ms.openlocfilehash: 6f1ff69863e14c73e90496ea61188387f5267b19
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2020
ms.locfileid: "82768395"
---
# <a name="scaffold-identity-in-aspnet-core-projects"></a><span data-ttu-id="6ceb8-103">Scaffolding Identity en proyectos de ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="6ceb8-103">Scaffold Identity in ASP.NET Core projects</span></span>

<span data-ttu-id="6ceb8-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="6ceb8-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6ceb8-105">ASP.net Core proporciona [ASP.net Core Identity ](xref:security/authentication/identity) como una [ Razor biblioteca de clases](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="6ceb8-105">ASP.NET Core provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="6ceb8-106">Las aplicaciones que Identity incluyen pueden aplicar el scaffolding para agregar selectivamente el código fuente incluido en la Identity Razor biblioteca de clases (RCL).</span><span class="sxs-lookup"><span data-stu-id="6ceb8-106">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="6ceb8-107">Puede que quiera generar código fuente que le permita modificar un código y cambiar el comportamiento;</span><span class="sxs-lookup"><span data-stu-id="6ceb8-107">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="6ceb8-108">así, por ejemplo, podría indicar al proveedor de scaffolding que generara el código que se usa en el registro.</span><span class="sxs-lookup"><span data-stu-id="6ceb8-108">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="6ceb8-109">El código generado tiene prioridad sobre el mismo código en Identity el RCL.</span><span class="sxs-lookup"><span data-stu-id="6ceb8-109">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="6ceb8-110">Para obtener el control total de la interfaz de usuario y no usar el valor predeterminado de RCL, consulte la sección creación de un origen de la [interfaz de usuario de identidad completa](#full).</span><span class="sxs-lookup"><span data-stu-id="6ceb8-110">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="6ceb8-111">Las aplicaciones que **no** incluyen autenticación pueden aplicar el scaffolding para agregar el paquete RCL Identity .</span><span class="sxs-lookup"><span data-stu-id="6ceb8-111">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="6ceb8-112">Tiene la opción de seleccionar Identity el código que se va a generar.</span><span class="sxs-lookup"><span data-stu-id="6ceb8-112">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="6ceb8-113">Aunque el scaffolding genera la mayor parte del código necesario, debe actualizar el proyecto para completar el proceso.</span><span class="sxs-lookup"><span data-stu-id="6ceb8-113">Although the scaffolder generates most of the necessary code, you need to update your project to complete the process.</span></span> <span data-ttu-id="6ceb8-114">En este documento se explican los pasos necesarios Identity para completar una actualización de scaffolding.</span><span class="sxs-lookup"><span data-stu-id="6ceb8-114">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="6ceb8-115">Se recomienda usar un sistema de control de código fuente que muestre las diferencias de archivos y le permite deshacer los cambios.</span><span class="sxs-lookup"><span data-stu-id="6ceb8-115">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="6ceb8-116">Inspeccione los cambios después de Identity ejecutar el scaffolding.</span><span class="sxs-lookup"><span data-stu-id="6ceb8-116">Inspect the changes after running the Identity scaffolder.</span></span>

<span data-ttu-id="6ceb8-117">Los servicios son necesarios cuando se usa la [autenticación en dos fases](xref:security/authentication/identity-enable-qrcodes), la [confirmación de la cuenta y la recuperación de contraseña](xref:security/authentication/accconfirm), y otras características de seguridad con. Identity</span><span class="sxs-lookup"><span data-stu-id="6ceb8-117">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="6ceb8-118">Los servicios o los códigos auxiliares de servicio no Identityse generan al aplicar scaffolding.</span><span class="sxs-lookup"><span data-stu-id="6ceb8-118">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="6ceb8-119">Los servicios para habilitar estas características deben agregarse manualmente.</span><span class="sxs-lookup"><span data-stu-id="6ceb8-119">Services to enable these features must be added manually.</span></span> <span data-ttu-id="6ceb8-120">Por ejemplo, vea [requerir confirmación de correo electrónico](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="6ceb8-120">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

<span data-ttu-id="6ceb8-121">Cuando se scaffolding Identity con un nuevo contexto de datos en un proyecto con cuentas individuales existentes:</span><span class="sxs-lookup"><span data-stu-id="6ceb8-121">When scaffolding Identity with a new data context into a project with existing individual accounts:</span></span>

* <span data-ttu-id="6ceb8-122">En `Startup.ConfigureServices`, quite las llamadas a:</span><span class="sxs-lookup"><span data-stu-id="6ceb8-122">In `Startup.ConfigureServices`, remove the calls to:</span></span>
  * `AddDbContext`
  * `AddDefaultIdentity`

<span data-ttu-id="6ceb8-123">Por ejemplo, `AddDbContext` y `AddDefaultIdentity` están comentados en el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="6ceb8-123">For example, `AddDbContext` and `AddDefaultIdentity` are commented out in the following code:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRemove.cs?name=snippet)]

<span data-ttu-id="6ceb8-124">El código anterior comenta el código que está duplicado en *areas/Identity/IdentityHostingStartup.CS*</span><span class="sxs-lookup"><span data-stu-id="6ceb8-124">The preceeding code comments out the code that is duplicated in *Areas/Identity/IdentityHostingStartup.cs*</span></span>

<span data-ttu-id="6ceb8-125">Normalmente, las aplicaciones que se crearon con cuentas individuales ***no*** deben crear un nuevo contexto de datos.</span><span class="sxs-lookup"><span data-stu-id="6ceb8-125">Typically, apps that were created with individual accounts should ***not*** create a new data context.</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="6ceb8-126">Identidad de scaffolding en un proyecto vacío</span><span class="sxs-lookup"><span data-stu-id="6ceb8-126">Scaffold identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="6ceb8-127">Actualice la `Startup` clase con código similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="6ceb8-127">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="6ceb8-128">Identidad de scaffolding en Razor un proyecto sin autorización existente</span><span class="sxs-lookup"><span data-stu-id="6ceb8-128">Scaffold identity into a Razor project without existing authorization</span></span>

<!--  Updated for 3.0
set projNam=RPnoAuth
set projType=webapp

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef database drop
dotnet ef migrations add CreateIdentitySchema0
dotnet ef database update
-->

<!-- ERROR
There is already an object named 'AspNetRoles' in the database.

Fixed via dotnet ef database drop
before dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Identity<span data-ttu-id="6ceb8-129">está configurado en *areasIdentity//IdentityHostingStartup.CS*.</span><span class="sxs-lookup"><span data-stu-id="6ceb8-129"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="6ceb8-130">para obtener más información, vea [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="6ceb8-130">for more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="6ceb8-131">Migraciones, UseAuthentication y diseño</span><span class="sxs-lookup"><span data-stu-id="6ceb8-131">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="6ceb8-132">Enable authentication (Habilitar autenticación)</span><span class="sxs-lookup"><span data-stu-id="6ceb8-132">Enable authentication</span></span>

<span data-ttu-id="6ceb8-133">Actualice la `Startup` clase con código similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="6ceb8-133">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="6ceb8-134">Cambios de diseño</span><span class="sxs-lookup"><span data-stu-id="6ceb8-134">Layout changes</span></span>

<span data-ttu-id="6ceb8-135">Opcional: agregue el inicio de sesión`_LoginPartial`parcial () al archivo de diseño:</span><span class="sxs-lookup"><span data-stu-id="6ceb8-135">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[Main](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="6ceb8-136">Identidad de scaffolding en Razor un proyecto con autorización</span><span class="sxs-lookup"><span data-stu-id="6ceb8-136">Scaffold identity into a Razor project with authorization</span></span>

<!--
Use >=2.1: dotnet new webapp -au Individual -o RPauth
Use = 2.0: dotnet new razor -au Individual -o RPauth
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]
<span data-ttu-id="6ceb8-137">Algunas Identity opciones se configuran en *areas/Identity/IdentityHostingStartup.CS*.</span><span class="sxs-lookup"><span data-stu-id="6ceb8-137">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="6ceb8-138">Para obtener más información, vea [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="6ceb8-138">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="6ceb8-139">Identidad de scaffolding en un proyecto de MVC sin autorización existente</span><span class="sxs-lookup"><span data-stu-id="6ceb8-139">Scaffold identity into an MVC project without existing authorization</span></span>

<!--
set projNam=MvcNoAuth
set projType=mvc
set version=2.1.0

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v %version%
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="6ceb8-140">Opcional: agregue el inicio de sesión`_LoginPartial`parcial () al archivo *views/Shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="6ceb8-140">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[Main](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* <span data-ttu-id="6ceb8-141">Mueva el archivo *pages/Shared/_LoginPartial. cshtml* a *views/Shared/_LoginPartial. cshtml*</span><span class="sxs-lookup"><span data-stu-id="6ceb8-141">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

Identity<span data-ttu-id="6ceb8-142">está configurado en *areasIdentity//IdentityHostingStartup.CS*.</span><span class="sxs-lookup"><span data-stu-id="6ceb8-142"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="6ceb8-143">Para obtener más información, vea IHostingStartup.</span><span class="sxs-lookup"><span data-stu-id="6ceb8-143">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="6ceb8-144">Actualice la `Startup` clase con código similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="6ceb8-144">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="6ceb8-145">Identidad de scaffolding en un proyecto de MVC con autorización</span><span class="sxs-lookup"><span data-stu-id="6ceb8-145">Scaffold identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="6ceb8-146">Crear origen de la interfaz de usuario de identidad completa</span><span class="sxs-lookup"><span data-stu-id="6ceb8-146">Create full identity UI source</span></span>

<span data-ttu-id="6ceb8-147">Para mantener el control total de Identity la interfaz de usuario Identity , ejecute el scaffolding y seleccione **invalidar todos los archivos**.</span><span class="sxs-lookup"><span data-stu-id="6ceb8-147">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="6ceb8-148">En el código resaltado siguiente se muestran los cambios Identity para reemplazar Identity la interfaz de usuario predeterminada por en una aplicación Web ASP.net Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="6ceb8-148">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="6ceb8-149">Puede que desee hacer esto para tener un control total de la Identity interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="6ceb8-149">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="6ceb8-150">El valor Identity predeterminado se reemplaza en el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="6ceb8-150">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="6ceb8-151">El código siguiente establece los valores de [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)y [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span><span class="sxs-lookup"><span data-stu-id="6ceb8-151">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="6ceb8-152">Registre una `IEmailSender` implementación, por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="6ceb8-152">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a><span data-ttu-id="6ceb8-153">Deshabilitar registro (página)</span><span class="sxs-lookup"><span data-stu-id="6ceb8-153">Disable register page</span></span>

<span data-ttu-id="6ceb8-154">Para deshabilitar el registro de usuario:</span><span class="sxs-lookup"><span data-stu-id="6ceb8-154">To disable user registration:</span></span>

* <span data-ttu-id="6ceb8-155">Scaffold Identity.</span><span class="sxs-lookup"><span data-stu-id="6ceb8-155">Scaffold Identity.</span></span> <span data-ttu-id="6ceb8-156">Incluya account. Register, Account. login y account. RegisterConfirmation.</span><span class="sxs-lookup"><span data-stu-id="6ceb8-156">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="6ceb8-157">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="6ceb8-157">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="6ceb8-158">Actualice *areasIdentity//pages/Account/Register.cshtml.CS* para que los usuarios no puedan registrarse desde este punto de conexión:</span><span class="sxs-lookup"><span data-stu-id="6ceb8-158">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="6ceb8-159">Actualice *areasIdentity//pages/Account/Register.cshtml* para que sean coherentes con los cambios anteriores:</span><span class="sxs-lookup"><span data-stu-id="6ceb8-159">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="6ceb8-160">Comente o quite el vínculo de registro de las *áreasIdentity//pages/Account/login.cshtml*</span><span class="sxs-lookup"><span data-stu-id="6ceb8-160">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="6ceb8-161">Actualice la página *áreasIdentity//pages/Account/RegisterConfirmation* .</span><span class="sxs-lookup"><span data-stu-id="6ceb8-161">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="6ceb8-162">Quite el código y los vínculos del archivo cshtml.</span><span class="sxs-lookup"><span data-stu-id="6ceb8-162">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="6ceb8-163">Quite el código de confirmación de `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="6ceb8-163">Remove the confirmation code from the `PageModel`:</span></span>

  ```csharp
   [AllowAnonymous]
    public class RegisterConfirmationModel : PageModel
    {
        public IActionResult OnGet()
        {  
            return Page();
        }
    }
  ```
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="6ceb8-164">Usar otra aplicación para agregar usuarios</span><span class="sxs-lookup"><span data-stu-id="6ceb8-164">Use another app to add users</span></span>

<span data-ttu-id="6ceb8-165">Proporcionar un mecanismo para agregar usuarios fuera de la aplicación Web.</span><span class="sxs-lookup"><span data-stu-id="6ceb8-165">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="6ceb8-166">Entre las opciones para agregar usuarios se incluyen:</span><span class="sxs-lookup"><span data-stu-id="6ceb8-166">Options to add users include:</span></span>

* <span data-ttu-id="6ceb8-167">Una aplicación Web de administración dedicada.</span><span class="sxs-lookup"><span data-stu-id="6ceb8-167">A dedicated admin web app.</span></span>
* <span data-ttu-id="6ceb8-168">Una aplicación de consola.</span><span class="sxs-lookup"><span data-stu-id="6ceb8-168">A console app.</span></span>

<span data-ttu-id="6ceb8-169">En el código siguiente se describe un enfoque para agregar usuarios:</span><span class="sxs-lookup"><span data-stu-id="6ceb8-169">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="6ceb8-170">Una lista de usuarios se lee en la memoria.</span><span class="sxs-lookup"><span data-stu-id="6ceb8-170">A list of users is read into memory.</span></span>
* <span data-ttu-id="6ceb8-171">Se genera una contraseña única segura para cada usuario.</span><span class="sxs-lookup"><span data-stu-id="6ceb8-171">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="6ceb8-172">El usuario se agrega a la Identity base de datos.</span><span class="sxs-lookup"><span data-stu-id="6ceb8-172">The user is added to the Identity database.</span></span>
* <span data-ttu-id="6ceb8-173">Se notifica al usuario y se le indica que cambie la contraseña.</span><span class="sxs-lookup"><span data-stu-id="6ceb8-173">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="6ceb8-174">En el código siguiente se describe cómo agregar un usuario:</span><span class="sxs-lookup"><span data-stu-id="6ceb8-174">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="6ceb8-175">Se puede seguir un enfoque similar para escenarios de producción.</span><span class="sxs-lookup"><span data-stu-id="6ceb8-175">A similar approach can be followed for production scenarios.</span></span>

## <a name="prevent-publish-of-static-identity-assets"></a><span data-ttu-id="6ceb8-176">Impedir la publicación de Identity recursos estáticos</span><span class="sxs-lookup"><span data-stu-id="6ceb8-176">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="6ceb8-177">Para evitar la publicación Identity de recursos estáticos en la raíz <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>Web, vea.</span><span class="sxs-lookup"><span data-stu-id="6ceb8-177">To prevent publishing static Identity assets to the web root, see <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6ceb8-178">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="6ceb8-178">Additional resources</span></span>

* [<span data-ttu-id="6ceb8-179">Cambios en el código de autenticación en ASP.NET Core 2,1 y versiones posteriores</span><span class="sxs-lookup"><span data-stu-id="6ceb8-179">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="6ceb8-180">ASP.net Core 2,1 y versiones posteriores [proporcionan Identity ASP.net Core](xref:security/authentication/identity) como una [ Razor biblioteca de clases](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="6ceb8-180">ASP.NET Core 2.1 and later provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="6ceb8-181">Las aplicaciones que Identity incluyen pueden aplicar el scaffolding para agregar selectivamente el código fuente incluido en la Identity Razor biblioteca de clases (RCL).</span><span class="sxs-lookup"><span data-stu-id="6ceb8-181">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="6ceb8-182">Puede que quiera generar código fuente que le permita modificar un código y cambiar el comportamiento;</span><span class="sxs-lookup"><span data-stu-id="6ceb8-182">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="6ceb8-183">así, por ejemplo, podría indicar al proveedor de scaffolding que generara el código que se usa en el registro.</span><span class="sxs-lookup"><span data-stu-id="6ceb8-183">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="6ceb8-184">El código generado tiene prioridad sobre el mismo código en Identity el RCL.</span><span class="sxs-lookup"><span data-stu-id="6ceb8-184">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="6ceb8-185">Para obtener el control total de la interfaz de usuario y no usar el valor predeterminado de RCL, consulte la sección creación de un origen de la [interfaz de usuario de identidad completa](#full).</span><span class="sxs-lookup"><span data-stu-id="6ceb8-185">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="6ceb8-186">Las aplicaciones que **no** incluyen autenticación pueden aplicar el scaffolding para agregar el paquete RCL Identity .</span><span class="sxs-lookup"><span data-stu-id="6ceb8-186">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="6ceb8-187">Tiene la opción de seleccionar Identity el código que se va a generar.</span><span class="sxs-lookup"><span data-stu-id="6ceb8-187">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="6ceb8-188">Aunque el scaffolding genera la mayor parte del código necesario, tendrá que actualizar el proyecto para completar el proceso.</span><span class="sxs-lookup"><span data-stu-id="6ceb8-188">Although the scaffolder generates most of the necessary code, you'll have to update your project to complete the process.</span></span> <span data-ttu-id="6ceb8-189">En este documento se explican los pasos necesarios Identity para completar una actualización de scaffolding.</span><span class="sxs-lookup"><span data-stu-id="6ceb8-189">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="6ceb8-190">Cuando se Identity ejecuta el scaffolding, se crea un archivo *ScaffoldingReadme. txt* en el directorio del proyecto.</span><span class="sxs-lookup"><span data-stu-id="6ceb8-190">When the Identity scaffolder is run, a *ScaffoldingReadme.txt* file is created in the project directory.</span></span> <span data-ttu-id="6ceb8-191">El archivo *ScaffoldingReadme. txt* contiene instrucciones generales sobre lo que se necesita para completar Identity la actualización de scaffolding.</span><span class="sxs-lookup"><span data-stu-id="6ceb8-191">The *ScaffoldingReadme.txt* file contains general instructions on what's needed to complete the Identity scaffolding update.</span></span> <span data-ttu-id="6ceb8-192">Este documento contiene instrucciones más completas que el archivo *ScaffoldingReadme. txt* .</span><span class="sxs-lookup"><span data-stu-id="6ceb8-192">This document contains more complete instructions than the *ScaffoldingReadme.txt* file.</span></span>

<span data-ttu-id="6ceb8-193">Se recomienda usar un sistema de control de código fuente que muestre las diferencias de archivos y le permite deshacer los cambios.</span><span class="sxs-lookup"><span data-stu-id="6ceb8-193">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="6ceb8-194">Inspeccione los cambios después de Identity ejecutar el scaffolding.</span><span class="sxs-lookup"><span data-stu-id="6ceb8-194">Inspect the changes after running the Identity scaffolder.</span></span>

> [!NOTE]
> <span data-ttu-id="6ceb8-195">Los servicios son necesarios cuando se usa la [autenticación en dos fases](xref:security/authentication/identity-enable-qrcodes), la [confirmación de la cuenta y la recuperación de contraseña](xref:security/authentication/accconfirm), y otras características de seguridad con. Identity</span><span class="sxs-lookup"><span data-stu-id="6ceb8-195">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="6ceb8-196">Los servicios o los códigos auxiliares de servicio no Identityse generan al aplicar scaffolding.</span><span class="sxs-lookup"><span data-stu-id="6ceb8-196">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="6ceb8-197">Los servicios para habilitar estas características deben agregarse manualmente.</span><span class="sxs-lookup"><span data-stu-id="6ceb8-197">Services to enable these features must be added manually.</span></span> <span data-ttu-id="6ceb8-198">Por ejemplo, vea [requerir confirmación de correo electrónico](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="6ceb8-198">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="6ceb8-199">Identidad de scaffolding en un proyecto vacío</span><span class="sxs-lookup"><span data-stu-id="6ceb8-199">Scaffold identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="6ceb8-200">Agregue las siguientes llamadas resaltadas `Startup` a la clase:</span><span class="sxs-lookup"><span data-stu-id="6ceb8-200">Add the following highlighted calls to the `Startup` class:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="6ceb8-201">Identidad de scaffolding en Razor un proyecto sin autorización existente</span><span class="sxs-lookup"><span data-stu-id="6ceb8-201">Scaffold identity into a Razor project without existing authorization</span></span>

<!--  Updated for 3.0
set projNam=RPnoAuth
set projType=webapp

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Identity<span data-ttu-id="6ceb8-202">está configurado en *areasIdentity//IdentityHostingStartup.CS*.</span><span class="sxs-lookup"><span data-stu-id="6ceb8-202"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="6ceb8-203">para obtener más información, vea [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="6ceb8-203">for more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="6ceb8-204">Migraciones, UseAuthentication y diseño</span><span class="sxs-lookup"><span data-stu-id="6ceb8-204">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="6ceb8-205">Enable authentication (Habilitar autenticación)</span><span class="sxs-lookup"><span data-stu-id="6ceb8-205">Enable authentication</span></span>

<span data-ttu-id="6ceb8-206">En el `Configure` método de la `Startup` clase, llame [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) a UseAuthentication `UseStaticFiles`después de:</span><span class="sxs-lookup"><span data-stu-id="6ceb8-206">In the `Configure` method of the `Startup` class, call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="6ceb8-207">Cambios de diseño</span><span class="sxs-lookup"><span data-stu-id="6ceb8-207">Layout changes</span></span>

<span data-ttu-id="6ceb8-208">Opcional: agregue el inicio de sesión`_LoginPartial`parcial () al archivo de diseño:</span><span class="sxs-lookup"><span data-stu-id="6ceb8-208">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[Main](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="6ceb8-209">Identidad de scaffolding en Razor un proyecto con autorización</span><span class="sxs-lookup"><span data-stu-id="6ceb8-209">Scaffold identity into a Razor project with authorization</span></span>

<!--
Use >=2.1: dotnet new webapp -au Individual -o RPauth
Use = 2.0: dotnet new razor -au Individual -o RPauth
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]
<span data-ttu-id="6ceb8-210">Algunas Identity opciones se configuran en *areas/Identity/IdentityHostingStartup.CS*.</span><span class="sxs-lookup"><span data-stu-id="6ceb8-210">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="6ceb8-211">Para obtener más información, vea [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="6ceb8-211">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="6ceb8-212">Identidad de scaffolding en un proyecto de MVC sin autorización existente</span><span class="sxs-lookup"><span data-stu-id="6ceb8-212">Scaffold identity into an MVC project without existing authorization</span></span>

<!--
set projNam=MvcNoAuth
set projType=mvc
set version=2.1.0

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v %version%
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="6ceb8-213">Opcional: agregue el inicio de sesión`_LoginPartial`parcial () al archivo *views/Shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="6ceb8-213">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* <span data-ttu-id="6ceb8-214">Mueva el archivo *pages/Shared/_LoginPartial. cshtml* a *views/Shared/_LoginPartial. cshtml*</span><span class="sxs-lookup"><span data-stu-id="6ceb8-214">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

Identity<span data-ttu-id="6ceb8-215">está configurado en *areasIdentity//IdentityHostingStartup.CS*.</span><span class="sxs-lookup"><span data-stu-id="6ceb8-215"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="6ceb8-216">Para obtener más información, vea IHostingStartup.</span><span class="sxs-lookup"><span data-stu-id="6ceb8-216">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="6ceb8-217">Llame [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) a UseAuthentication `UseStaticFiles`después de:</span><span class="sxs-lookup"><span data-stu-id="6ceb8-217">Call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="6ceb8-218">Identidad de scaffolding en un proyecto de MVC con autorización</span><span class="sxs-lookup"><span data-stu-id="6ceb8-218">Scaffold identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="6ceb8-219">Elimine las *páginas/carpetas compartidas* y los archivos de esa carpeta.</span><span class="sxs-lookup"><span data-stu-id="6ceb8-219">Delete the *Pages/Shared* folder and the files in that folder.</span></span>

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="6ceb8-220">Crear origen de la interfaz de usuario de identidad completa</span><span class="sxs-lookup"><span data-stu-id="6ceb8-220">Create full identity UI source</span></span>

<span data-ttu-id="6ceb8-221">Para mantener el control total de Identity la interfaz de usuario Identity , ejecute el scaffolding y seleccione **invalidar todos los archivos**.</span><span class="sxs-lookup"><span data-stu-id="6ceb8-221">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="6ceb8-222">En el código resaltado siguiente se muestran los cambios Identity para reemplazar Identity la interfaz de usuario predeterminada por en una aplicación Web ASP.net Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="6ceb8-222">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="6ceb8-223">Puede que desee hacer esto para tener un control total de la Identity interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="6ceb8-223">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="6ceb8-224">El valor Identity predeterminado se reemplaza en el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="6ceb8-224">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="6ceb8-225">El código siguiente establece los valores de [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)y [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span><span class="sxs-lookup"><span data-stu-id="6ceb8-225">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="6ceb8-226">Registre una `IEmailSender` implementación, por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="6ceb8-226">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a><span data-ttu-id="6ceb8-227">Deshabilitar registro (página)</span><span class="sxs-lookup"><span data-stu-id="6ceb8-227">Disable register page</span></span>

<span data-ttu-id="6ceb8-228">Para deshabilitar el registro de usuario:</span><span class="sxs-lookup"><span data-stu-id="6ceb8-228">To disable user registration:</span></span>

* <span data-ttu-id="6ceb8-229">Scaffold Identity.</span><span class="sxs-lookup"><span data-stu-id="6ceb8-229">Scaffold Identity.</span></span> <span data-ttu-id="6ceb8-230">Incluya account. Register, Account. login y account. RegisterConfirmation.</span><span class="sxs-lookup"><span data-stu-id="6ceb8-230">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="6ceb8-231">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="6ceb8-231">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="6ceb8-232">Actualice *areasIdentity//pages/Account/Register.cshtml.CS* para que los usuarios no puedan registrarse desde este punto de conexión:</span><span class="sxs-lookup"><span data-stu-id="6ceb8-232">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="6ceb8-233">Actualice *areasIdentity//pages/Account/Register.cshtml* para que sean coherentes con los cambios anteriores:</span><span class="sxs-lookup"><span data-stu-id="6ceb8-233">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="6ceb8-234">Comente o quite el vínculo de registro de las *áreasIdentity//pages/Account/login.cshtml*</span><span class="sxs-lookup"><span data-stu-id="6ceb8-234">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="6ceb8-235">Actualice la página *áreasIdentity//pages/Account/RegisterConfirmation* .</span><span class="sxs-lookup"><span data-stu-id="6ceb8-235">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="6ceb8-236">Quite el código y los vínculos del archivo cshtml.</span><span class="sxs-lookup"><span data-stu-id="6ceb8-236">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="6ceb8-237">Quite el código de confirmación de `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="6ceb8-237">Remove the confirmation code from the `PageModel`:</span></span>

  ```csharp
   [AllowAnonymous]
    public class RegisterConfirmationModel : PageModel
    {
        public IActionResult OnGet()
        {  
            return Page();
        }
    }
  ```
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="6ceb8-238">Usar otra aplicación para agregar usuarios</span><span class="sxs-lookup"><span data-stu-id="6ceb8-238">Use another app to add users</span></span>

<span data-ttu-id="6ceb8-239">Proporcionar un mecanismo para agregar usuarios fuera de la aplicación Web.</span><span class="sxs-lookup"><span data-stu-id="6ceb8-239">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="6ceb8-240">Entre las opciones para agregar usuarios se incluyen:</span><span class="sxs-lookup"><span data-stu-id="6ceb8-240">Options to add users include:</span></span>

* <span data-ttu-id="6ceb8-241">Una aplicación Web de administración dedicada.</span><span class="sxs-lookup"><span data-stu-id="6ceb8-241">A dedicated admin web app.</span></span>
* <span data-ttu-id="6ceb8-242">Una aplicación de consola.</span><span class="sxs-lookup"><span data-stu-id="6ceb8-242">A console app.</span></span>

<span data-ttu-id="6ceb8-243">En el código siguiente se describe un enfoque para agregar usuarios:</span><span class="sxs-lookup"><span data-stu-id="6ceb8-243">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="6ceb8-244">Una lista de usuarios se lee en la memoria.</span><span class="sxs-lookup"><span data-stu-id="6ceb8-244">A list of users is read into memory.</span></span>
* <span data-ttu-id="6ceb8-245">Se genera una contraseña única segura para cada usuario.</span><span class="sxs-lookup"><span data-stu-id="6ceb8-245">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="6ceb8-246">El usuario se agrega a la Identity base de datos.</span><span class="sxs-lookup"><span data-stu-id="6ceb8-246">The user is added to the Identity database.</span></span>
* <span data-ttu-id="6ceb8-247">Se notifica al usuario y se le indica que cambie la contraseña.</span><span class="sxs-lookup"><span data-stu-id="6ceb8-247">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="6ceb8-248">En el código siguiente se describe cómo agregar un usuario:</span><span class="sxs-lookup"><span data-stu-id="6ceb8-248">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="6ceb8-249">Se puede seguir un enfoque similar para escenarios de producción.</span><span class="sxs-lookup"><span data-stu-id="6ceb8-249">A similar approach can be followed for production scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6ceb8-250">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="6ceb8-250">Additional resources</span></span>

* [<span data-ttu-id="6ceb8-251">Cambios en el código de autenticación en ASP.NET Core 2,1 y versiones posteriores</span><span class="sxs-lookup"><span data-stu-id="6ceb8-251">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end