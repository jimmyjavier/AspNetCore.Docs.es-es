---
title: Identidad de scaffolding en proyectos de ASP.NET Core
author: rick-anderson
description: Aprenda a aplicar una identidad scaffolding en un proyecto ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/1/2020
uid: security/authentication/scaffold-identity
ms.openlocfilehash: ac95035b114274ddaa6ccb0b5b6e3da98885e39e
ms.sourcegitcommit: 6318d2bdd63116e178c34492a904be85ec9ac108
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/30/2020
ms.locfileid: "82604732"
---
# <a name="scaffold-identity-in-aspnet-core-projects"></a><span data-ttu-id="71085-103">Identidad de scaffolding en proyectos de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="71085-103">Scaffold Identity in ASP.NET Core projects</span></span>

<span data-ttu-id="71085-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="71085-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="71085-105">ASP.NET Core proporciona [ASP.net Core Identity](xref:security/authentication/identity) como una [biblioteca de clases de Razor](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="71085-105">ASP.NET Core provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="71085-106">Las aplicaciones que incluyen la identidad pueden aplicar el scaffolding para agregar de forma selectiva el código fuente contenido en la biblioteca de clases Razor de identidad (RCL).</span><span class="sxs-lookup"><span data-stu-id="71085-106">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="71085-107">Puede que quiera generar código fuente que le permita modificar un código y cambiar el comportamiento;</span><span class="sxs-lookup"><span data-stu-id="71085-107">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="71085-108">así, por ejemplo, podría indicar al proveedor de scaffolding que generara el código que se usa en el registro.</span><span class="sxs-lookup"><span data-stu-id="71085-108">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="71085-109">Dicho código generado tendrá prioridad sobre el mismo código en el RCL de Identity.</span><span class="sxs-lookup"><span data-stu-id="71085-109">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="71085-110">Para obtener el control total de la interfaz de usuario y no usar el valor predeterminado de RCL, consulte la sección creación de un origen de la [interfaz de usuario de identidad completa](#full).</span><span class="sxs-lookup"><span data-stu-id="71085-110">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="71085-111">Las aplicaciones que **no** incluyen autenticación pueden aplicar el scaffolding para agregar el paquete de identidad de RCL.</span><span class="sxs-lookup"><span data-stu-id="71085-111">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="71085-112">Existe la posibilidad de seleccionar el código de Identity que se va a generar.</span><span class="sxs-lookup"><span data-stu-id="71085-112">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="71085-113">Aunque el scaffolding genera la mayor parte del código necesario, debe actualizar el proyecto para completar el proceso.</span><span class="sxs-lookup"><span data-stu-id="71085-113">Although the scaffolder generates most of the necessary code, you need to update your project to complete the process.</span></span> <span data-ttu-id="71085-114">En este documento se explican los pasos necesarios para completar una actualización de scaffolding de identidad.</span><span class="sxs-lookup"><span data-stu-id="71085-114">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="71085-115">Se recomienda usar un sistema de control de código fuente que muestre las diferencias de archivos y le permite deshacer los cambios.</span><span class="sxs-lookup"><span data-stu-id="71085-115">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="71085-116">Inspeccione los cambios después de ejecutar el scaffolding de identidad.</span><span class="sxs-lookup"><span data-stu-id="71085-116">Inspect the changes after running the Identity scaffolder.</span></span>

<span data-ttu-id="71085-117">Los servicios son necesarios cuando se usa la [autenticación en dos fases](xref:security/authentication/identity-enable-qrcodes), la [confirmación de la cuenta y la recuperación de contraseña](xref:security/authentication/accconfirm)y otras características de seguridad con identidad.</span><span class="sxs-lookup"><span data-stu-id="71085-117">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="71085-118">Los servicios o los códigos auxiliares de servicio no se generan cuando se trata de una identidad de scaffolding.</span><span class="sxs-lookup"><span data-stu-id="71085-118">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="71085-119">Los servicios para habilitar estas características deben agregarse manualmente.</span><span class="sxs-lookup"><span data-stu-id="71085-119">Services to enable these features must be added manually.</span></span> <span data-ttu-id="71085-120">Por ejemplo, vea [requerir confirmación de correo electrónico](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="71085-120">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

<span data-ttu-id="71085-121">Al aplicar scaffolding a la identidad con un nuevo contexto de datos en un proyecto con cuentas individuales existentes:</span><span class="sxs-lookup"><span data-stu-id="71085-121">When scaffolding Identity with a new data context into a project with existing individual accounts:</span></span>

* <span data-ttu-id="71085-122">En `Startup.ConfigureServices`, quite las llamadas a:</span><span class="sxs-lookup"><span data-stu-id="71085-122">In `Startup.ConfigureServices`, remove the calls to:</span></span>
  * `AddDbContext`
  * `AddDefaultIdentity`

<span data-ttu-id="71085-123">Por ejemplo, `AddDbContext` y `AddDefaultIdentity` están comentados en el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="71085-123">For example, `AddDbContext` and `AddDefaultIdentity` are commented out in the following code:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRemove.cs?name=snippet)]

<span data-ttu-id="71085-124">El código anterior comenta el código que está duplicado en *areas/Identity/IdentityHostingStartup. cs.*</span><span class="sxs-lookup"><span data-stu-id="71085-124">The preceeding code comments out the code that is duplicated in *Areas/Identity/IdentityHostingStartup.cs*</span></span>

<span data-ttu-id="71085-125">Normalmente, las aplicaciones que se crearon con cuentas individuales ***no*** deben crear un nuevo contexto de datos.</span><span class="sxs-lookup"><span data-stu-id="71085-125">Typically, apps that were created with individual accounts should ***not*** create a new data context.</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="71085-126">Identidad de scaffolding en un proyecto vacío</span><span class="sxs-lookup"><span data-stu-id="71085-126">Scaffold identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="71085-127">Actualice la `Startup` clase con código similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="71085-127">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="71085-128">Identidad scaffolding en un proyecto Razor sin autorización existente</span><span class="sxs-lookup"><span data-stu-id="71085-128">Scaffold identity into a Razor project without existing authorization</span></span>

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

<span data-ttu-id="71085-129">La identidad se configura en *areas/Identity/IdentityHostingStartup. CS*.</span><span class="sxs-lookup"><span data-stu-id="71085-129">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="71085-130">para obtener más información, vea [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="71085-130">for more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="71085-131">Migraciones, UseAuthentication y diseño</span><span class="sxs-lookup"><span data-stu-id="71085-131">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="71085-132">Enable authentication (Habilitar autenticación)</span><span class="sxs-lookup"><span data-stu-id="71085-132">Enable authentication</span></span>

<span data-ttu-id="71085-133">Actualice la `Startup` clase con código similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="71085-133">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="71085-134">Cambios de diseño</span><span class="sxs-lookup"><span data-stu-id="71085-134">Layout changes</span></span>

<span data-ttu-id="71085-135">Opcional: agregue el inicio de sesión`_LoginPartial`parcial () al archivo de diseño:</span><span class="sxs-lookup"><span data-stu-id="71085-135">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[Main](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="71085-136">Identidad de scaffolding en un proyecto de Razor con autorización</span><span class="sxs-lookup"><span data-stu-id="71085-136">Scaffold identity into a Razor project with authorization</span></span>

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
<span data-ttu-id="71085-137">Algunas opciones de identidad están configuradas en *areas/Identity/IdentityHostingStartup. CS*.</span><span class="sxs-lookup"><span data-stu-id="71085-137">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="71085-138">Para obtener más información, vea [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="71085-138">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="71085-139">Identidad de scaffolding en un proyecto de MVC sin autorización existente</span><span class="sxs-lookup"><span data-stu-id="71085-139">Scaffold identity into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="71085-140">Opcional: agregue el inicio de sesión`_LoginPartial`parcial () al archivo *views/Shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="71085-140">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[Main](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* <span data-ttu-id="71085-141">Mueva el archivo *pages/Shared/_LoginPartial. cshtml* a *views/Shared/_LoginPartial. cshtml*</span><span class="sxs-lookup"><span data-stu-id="71085-141">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="71085-142">La identidad se configura en *areas/Identity/IdentityHostingStartup. CS*.</span><span class="sxs-lookup"><span data-stu-id="71085-142">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="71085-143">Para obtener más información, vea IHostingStartup.</span><span class="sxs-lookup"><span data-stu-id="71085-143">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="71085-144">Actualice la `Startup` clase con código similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="71085-144">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="71085-145">Identidad de scaffolding en un proyecto de MVC con autorización</span><span class="sxs-lookup"><span data-stu-id="71085-145">Scaffold identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="71085-146">Crear origen de la interfaz de usuario de identidad completa</span><span class="sxs-lookup"><span data-stu-id="71085-146">Create full identity UI source</span></span>

<span data-ttu-id="71085-147">Para mantener el control total de la interfaz de usuario de identidad, ejecute el scaffolding de identidad y seleccione **invalidar todos los archivos**.</span><span class="sxs-lookup"><span data-stu-id="71085-147">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="71085-148">En el código resaltado siguiente se muestran los cambios para reemplazar la interfaz de usuario de identidad predeterminada por la identidad en una aplicación Web de ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="71085-148">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="71085-149">Puede que desee hacer esto para tener un control total de la interfaz de usuario de identidad.</span><span class="sxs-lookup"><span data-stu-id="71085-149">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="71085-150">La identidad predeterminada se reemplaza en el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="71085-150">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="71085-151">El código siguiente establece los valores de [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)y [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span><span class="sxs-lookup"><span data-stu-id="71085-151">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="71085-152">Registre una `IEmailSender` implementación, por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="71085-152">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a><span data-ttu-id="71085-153">Deshabilitar registro (página)</span><span class="sxs-lookup"><span data-stu-id="71085-153">Disable register page</span></span>

<span data-ttu-id="71085-154">Para deshabilitar el registro de usuario:</span><span class="sxs-lookup"><span data-stu-id="71085-154">To disable user registration:</span></span>

* <span data-ttu-id="71085-155">Identidad scaffolding.</span><span class="sxs-lookup"><span data-stu-id="71085-155">Scaffold Identity.</span></span> <span data-ttu-id="71085-156">Incluya account. Register, Account. login y account. RegisterConfirmation.</span><span class="sxs-lookup"><span data-stu-id="71085-156">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="71085-157">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="71085-157">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="71085-158">Actualice *areas/Identity/pages/Account/Register. cshtml. CS* para que los usuarios no puedan registrarse desde este punto de conexión:</span><span class="sxs-lookup"><span data-stu-id="71085-158">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="71085-159">Actualice *areas/Identity/pages/Account/Register. cshtml* para que sean coherentes con los cambios anteriores:</span><span class="sxs-lookup"><span data-stu-id="71085-159">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="71085-160">Comente o quite el vínculo de registro de las *áreas/identidad/páginas/cuenta/inicio de sesión. cshtml.*</span><span class="sxs-lookup"><span data-stu-id="71085-160">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="71085-161">Actualice la página *áreas/identidad/páginas/cuenta/RegisterConfirmation* .</span><span class="sxs-lookup"><span data-stu-id="71085-161">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="71085-162">Quite el código y los vínculos del archivo cshtml.</span><span class="sxs-lookup"><span data-stu-id="71085-162">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="71085-163">Quite el código de confirmación de `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="71085-163">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="71085-164">Usar otra aplicación para agregar usuarios</span><span class="sxs-lookup"><span data-stu-id="71085-164">Use another app to add users</span></span>

<span data-ttu-id="71085-165">Proporcionar un mecanismo para agregar usuarios fuera de la aplicación Web.</span><span class="sxs-lookup"><span data-stu-id="71085-165">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="71085-166">Entre las opciones para agregar usuarios se incluyen:</span><span class="sxs-lookup"><span data-stu-id="71085-166">Options to add users include:</span></span>

* <span data-ttu-id="71085-167">Una aplicación Web de administración dedicada.</span><span class="sxs-lookup"><span data-stu-id="71085-167">A dedicated admin web app.</span></span>
* <span data-ttu-id="71085-168">Una aplicación de consola.</span><span class="sxs-lookup"><span data-stu-id="71085-168">A console app.</span></span>

<span data-ttu-id="71085-169">En el código siguiente se describe un enfoque para agregar usuarios:</span><span class="sxs-lookup"><span data-stu-id="71085-169">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="71085-170">Una lista de usuarios se lee en la memoria.</span><span class="sxs-lookup"><span data-stu-id="71085-170">A list of users is read into memory.</span></span>
* <span data-ttu-id="71085-171">Se genera una contraseña única segura para cada usuario.</span><span class="sxs-lookup"><span data-stu-id="71085-171">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="71085-172">El usuario se agrega a la base de datos de identidad.</span><span class="sxs-lookup"><span data-stu-id="71085-172">The user is added to the Identity database.</span></span>
* <span data-ttu-id="71085-173">Se notifica al usuario y se le indica que cambie la contraseña.</span><span class="sxs-lookup"><span data-stu-id="71085-173">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="71085-174">En el código siguiente se describe cómo agregar un usuario:</span><span class="sxs-lookup"><span data-stu-id="71085-174">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="71085-175">Se puede seguir un enfoque similar para escenarios de producción.</span><span class="sxs-lookup"><span data-stu-id="71085-175">A similar approach can be followed for production scenarios.</span></span>

## <a name="prevent-publish-of-static-identity-assets"></a><span data-ttu-id="71085-176">Impedir la publicación de recursos de identidad estáticos</span><span class="sxs-lookup"><span data-stu-id="71085-176">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="71085-177">Para evitar la publicación de recursos de identidad estáticos en la raíz web, consulte <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>.</span><span class="sxs-lookup"><span data-stu-id="71085-177">To prevent publishing static Identity assets to the web root, see <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="71085-178">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="71085-178">Additional resources</span></span>

* [<span data-ttu-id="71085-179">Cambios en el código de autenticación en ASP.NET Core 2,1 y versiones posteriores</span><span class="sxs-lookup"><span data-stu-id="71085-179">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="71085-180">ASP.NET Core 2,1 y versiones posteriores proporcionan [ASP.net Core identidad](xref:security/authentication/identity) como una [biblioteca de clases de Razor](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="71085-180">ASP.NET Core 2.1 and later provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="71085-181">Las aplicaciones que incluyen la identidad pueden aplicar el scaffolding para agregar de forma selectiva el código fuente contenido en la biblioteca de clases Razor de identidad (RCL).</span><span class="sxs-lookup"><span data-stu-id="71085-181">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="71085-182">Puede que quiera generar código fuente que le permita modificar un código y cambiar el comportamiento;</span><span class="sxs-lookup"><span data-stu-id="71085-182">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="71085-183">así, por ejemplo, podría indicar al proveedor de scaffolding que generara el código que se usa en el registro.</span><span class="sxs-lookup"><span data-stu-id="71085-183">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="71085-184">Dicho código generado tendrá prioridad sobre el mismo código en el RCL de Identity.</span><span class="sxs-lookup"><span data-stu-id="71085-184">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="71085-185">Para obtener el control total de la interfaz de usuario y no usar el valor predeterminado de RCL, consulte la sección creación de un origen de la [interfaz de usuario de identidad completa](#full).</span><span class="sxs-lookup"><span data-stu-id="71085-185">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="71085-186">Las aplicaciones que **no** incluyen autenticación pueden aplicar el scaffolding para agregar el paquete de identidad de RCL.</span><span class="sxs-lookup"><span data-stu-id="71085-186">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="71085-187">Existe la posibilidad de seleccionar el código de Identity que se va a generar.</span><span class="sxs-lookup"><span data-stu-id="71085-187">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="71085-188">Aunque el scaffolding genera la mayor parte del código necesario, tendrá que actualizar el proyecto para completar el proceso.</span><span class="sxs-lookup"><span data-stu-id="71085-188">Although the scaffolder generates most of the necessary code, you'll have to update your project to complete the process.</span></span> <span data-ttu-id="71085-189">En este documento se explican los pasos necesarios para completar una actualización de scaffolding de identidad.</span><span class="sxs-lookup"><span data-stu-id="71085-189">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="71085-190">Cuando se ejecuta el scaffolding de identidad, se crea un archivo *ScaffoldingReadme. txt* en el directorio del proyecto.</span><span class="sxs-lookup"><span data-stu-id="71085-190">When the Identity scaffolder is run, a *ScaffoldingReadme.txt* file is created in the project directory.</span></span> <span data-ttu-id="71085-191">El archivo *ScaffoldingReadme. txt* contiene instrucciones generales sobre lo que se necesita para completar la actualización del scaffolding de identidad.</span><span class="sxs-lookup"><span data-stu-id="71085-191">The *ScaffoldingReadme.txt* file contains general instructions on what's needed to complete the Identity scaffolding update.</span></span> <span data-ttu-id="71085-192">Este documento contiene instrucciones más completas que el archivo *ScaffoldingReadme. txt* .</span><span class="sxs-lookup"><span data-stu-id="71085-192">This document contains more complete instructions than the *ScaffoldingReadme.txt* file.</span></span>

<span data-ttu-id="71085-193">Se recomienda usar un sistema de control de código fuente que muestre las diferencias de archivos y le permite deshacer los cambios.</span><span class="sxs-lookup"><span data-stu-id="71085-193">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="71085-194">Inspeccione los cambios después de ejecutar el scaffolding de identidad.</span><span class="sxs-lookup"><span data-stu-id="71085-194">Inspect the changes after running the Identity scaffolder.</span></span>

> [!NOTE]
> <span data-ttu-id="71085-195">Los servicios son necesarios cuando se usa la [autenticación en dos fases](xref:security/authentication/identity-enable-qrcodes), la [confirmación de la cuenta y la recuperación de contraseña](xref:security/authentication/accconfirm)y otras características de seguridad con identidad.</span><span class="sxs-lookup"><span data-stu-id="71085-195">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="71085-196">Los servicios o los códigos auxiliares de servicio no se generan cuando se trata de una identidad de scaffolding.</span><span class="sxs-lookup"><span data-stu-id="71085-196">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="71085-197">Los servicios para habilitar estas características deben agregarse manualmente.</span><span class="sxs-lookup"><span data-stu-id="71085-197">Services to enable these features must be added manually.</span></span> <span data-ttu-id="71085-198">Por ejemplo, vea [requerir confirmación de correo electrónico](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="71085-198">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="71085-199">Identidad de scaffolding en un proyecto vacío</span><span class="sxs-lookup"><span data-stu-id="71085-199">Scaffold identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="71085-200">Agregue las siguientes llamadas resaltadas `Startup` a la clase:</span><span class="sxs-lookup"><span data-stu-id="71085-200">Add the following highlighted calls to the `Startup` class:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="71085-201">Identidad scaffolding en un proyecto Razor sin autorización existente</span><span class="sxs-lookup"><span data-stu-id="71085-201">Scaffold identity into a Razor project without existing authorization</span></span>

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

<span data-ttu-id="71085-202">La identidad se configura en *areas/Identity/IdentityHostingStartup. CS*.</span><span class="sxs-lookup"><span data-stu-id="71085-202">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="71085-203">para obtener más información, vea [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="71085-203">for more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="71085-204">Migraciones, UseAuthentication y diseño</span><span class="sxs-lookup"><span data-stu-id="71085-204">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="71085-205">Enable authentication (Habilitar autenticación)</span><span class="sxs-lookup"><span data-stu-id="71085-205">Enable authentication</span></span>

<span data-ttu-id="71085-206">En el `Configure` método de la `Startup` clase, llame [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) a UseAuthentication `UseStaticFiles`después de:</span><span class="sxs-lookup"><span data-stu-id="71085-206">In the `Configure` method of the `Startup` class, call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="71085-207">Cambios de diseño</span><span class="sxs-lookup"><span data-stu-id="71085-207">Layout changes</span></span>

<span data-ttu-id="71085-208">Opcional: agregue el inicio de sesión`_LoginPartial`parcial () al archivo de diseño:</span><span class="sxs-lookup"><span data-stu-id="71085-208">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[Main](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="71085-209">Identidad de scaffolding en un proyecto de Razor con autorización</span><span class="sxs-lookup"><span data-stu-id="71085-209">Scaffold identity into a Razor project with authorization</span></span>

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
<span data-ttu-id="71085-210">Algunas opciones de identidad están configuradas en *areas/Identity/IdentityHostingStartup. CS*.</span><span class="sxs-lookup"><span data-stu-id="71085-210">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="71085-211">Para obtener más información, vea [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="71085-211">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="71085-212">Identidad de scaffolding en un proyecto de MVC sin autorización existente</span><span class="sxs-lookup"><span data-stu-id="71085-212">Scaffold identity into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="71085-213">Opcional: agregue el inicio de sesión`_LoginPartial`parcial () al archivo *views/Shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="71085-213">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* <span data-ttu-id="71085-214">Mueva el archivo *pages/Shared/_LoginPartial. cshtml* a *views/Shared/_LoginPartial. cshtml*</span><span class="sxs-lookup"><span data-stu-id="71085-214">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="71085-215">La identidad se configura en *areas/Identity/IdentityHostingStartup. CS*.</span><span class="sxs-lookup"><span data-stu-id="71085-215">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="71085-216">Para obtener más información, vea IHostingStartup.</span><span class="sxs-lookup"><span data-stu-id="71085-216">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="71085-217">Llame [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) a UseAuthentication `UseStaticFiles`después de:</span><span class="sxs-lookup"><span data-stu-id="71085-217">Call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="71085-218">Identidad de scaffolding en un proyecto de MVC con autorización</span><span class="sxs-lookup"><span data-stu-id="71085-218">Scaffold identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="71085-219">Elimine las *páginas/carpetas compartidas* y los archivos de esa carpeta.</span><span class="sxs-lookup"><span data-stu-id="71085-219">Delete the *Pages/Shared* folder and the files in that folder.</span></span>

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="71085-220">Crear origen de la interfaz de usuario de identidad completa</span><span class="sxs-lookup"><span data-stu-id="71085-220">Create full identity UI source</span></span>

<span data-ttu-id="71085-221">Para mantener el control total de la interfaz de usuario de identidad, ejecute el scaffolding de identidad y seleccione **invalidar todos los archivos**.</span><span class="sxs-lookup"><span data-stu-id="71085-221">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="71085-222">En el código resaltado siguiente se muestran los cambios para reemplazar la interfaz de usuario de identidad predeterminada por la identidad en una aplicación Web de ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="71085-222">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="71085-223">Puede que desee hacer esto para tener un control total de la interfaz de usuario de identidad.</span><span class="sxs-lookup"><span data-stu-id="71085-223">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="71085-224">La identidad predeterminada se reemplaza en el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="71085-224">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="71085-225">El código siguiente establece los valores de [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)y [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span><span class="sxs-lookup"><span data-stu-id="71085-225">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="71085-226">Registre una `IEmailSender` implementación, por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="71085-226">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a><span data-ttu-id="71085-227">Deshabilitar registro (página)</span><span class="sxs-lookup"><span data-stu-id="71085-227">Disable register page</span></span>

<span data-ttu-id="71085-228">Para deshabilitar el registro de usuario:</span><span class="sxs-lookup"><span data-stu-id="71085-228">To disable user registration:</span></span>

* <span data-ttu-id="71085-229">Identidad scaffolding.</span><span class="sxs-lookup"><span data-stu-id="71085-229">Scaffold Identity.</span></span> <span data-ttu-id="71085-230">Incluya account. Register, Account. login y account. RegisterConfirmation.</span><span class="sxs-lookup"><span data-stu-id="71085-230">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="71085-231">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="71085-231">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="71085-232">Actualice *areas/Identity/pages/Account/Register. cshtml. CS* para que los usuarios no puedan registrarse desde este punto de conexión:</span><span class="sxs-lookup"><span data-stu-id="71085-232">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="71085-233">Actualice *areas/Identity/pages/Account/Register. cshtml* para que sean coherentes con los cambios anteriores:</span><span class="sxs-lookup"><span data-stu-id="71085-233">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="71085-234">Comente o quite el vínculo de registro de las *áreas/identidad/páginas/cuenta/inicio de sesión. cshtml.*</span><span class="sxs-lookup"><span data-stu-id="71085-234">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="71085-235">Actualice la página *áreas/identidad/páginas/cuenta/RegisterConfirmation* .</span><span class="sxs-lookup"><span data-stu-id="71085-235">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="71085-236">Quite el código y los vínculos del archivo cshtml.</span><span class="sxs-lookup"><span data-stu-id="71085-236">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="71085-237">Quite el código de confirmación de `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="71085-237">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="71085-238">Usar otra aplicación para agregar usuarios</span><span class="sxs-lookup"><span data-stu-id="71085-238">Use another app to add users</span></span>

<span data-ttu-id="71085-239">Proporcionar un mecanismo para agregar usuarios fuera de la aplicación Web.</span><span class="sxs-lookup"><span data-stu-id="71085-239">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="71085-240">Entre las opciones para agregar usuarios se incluyen:</span><span class="sxs-lookup"><span data-stu-id="71085-240">Options to add users include:</span></span>

* <span data-ttu-id="71085-241">Una aplicación Web de administración dedicada.</span><span class="sxs-lookup"><span data-stu-id="71085-241">A dedicated admin web app.</span></span>
* <span data-ttu-id="71085-242">Una aplicación de consola.</span><span class="sxs-lookup"><span data-stu-id="71085-242">A console app.</span></span>

<span data-ttu-id="71085-243">En el código siguiente se describe un enfoque para agregar usuarios:</span><span class="sxs-lookup"><span data-stu-id="71085-243">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="71085-244">Una lista de usuarios se lee en la memoria.</span><span class="sxs-lookup"><span data-stu-id="71085-244">A list of users is read into memory.</span></span>
* <span data-ttu-id="71085-245">Se genera una contraseña única segura para cada usuario.</span><span class="sxs-lookup"><span data-stu-id="71085-245">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="71085-246">El usuario se agrega a la base de datos de identidad.</span><span class="sxs-lookup"><span data-stu-id="71085-246">The user is added to the Identity database.</span></span>
* <span data-ttu-id="71085-247">Se notifica al usuario y se le indica que cambie la contraseña.</span><span class="sxs-lookup"><span data-stu-id="71085-247">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="71085-248">En el código siguiente se describe cómo agregar un usuario:</span><span class="sxs-lookup"><span data-stu-id="71085-248">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="71085-249">Se puede seguir un enfoque similar para escenarios de producción.</span><span class="sxs-lookup"><span data-stu-id="71085-249">A similar approach can be followed for production scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="71085-250">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="71085-250">Additional resources</span></span>

* [<span data-ttu-id="71085-251">Cambios en el código de autenticación en ASP.NET Core 2,1 y versiones posteriores</span><span class="sxs-lookup"><span data-stu-id="71085-251">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end