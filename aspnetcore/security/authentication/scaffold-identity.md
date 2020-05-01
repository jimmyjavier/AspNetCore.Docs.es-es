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
# <a name="scaffold-identity-in-aspnet-core-projects"></a>Identidad de scaffolding en proyectos de ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core proporciona [ASP.net Core Identity](xref:security/authentication/identity) como una [biblioteca de clases de Razor](xref:razor-pages/ui-class). Las aplicaciones que incluyen la identidad pueden aplicar el scaffolding para agregar de forma selectiva el código fuente contenido en la biblioteca de clases Razor de identidad (RCL). Puede que quiera generar código fuente que le permita modificar un código y cambiar el comportamiento; así, por ejemplo, podría indicar al proveedor de scaffolding que generara el código que se usa en el registro. Dicho código generado tendrá prioridad sobre el mismo código en el RCL de Identity. Para obtener el control total de la interfaz de usuario y no usar el valor predeterminado de RCL, consulte la sección creación de un origen de la [interfaz de usuario de identidad completa](#full).

Las aplicaciones que **no** incluyen autenticación pueden aplicar el scaffolding para agregar el paquete de identidad de RCL. Existe la posibilidad de seleccionar el código de Identity que se va a generar.

Aunque el scaffolding genera la mayor parte del código necesario, debe actualizar el proyecto para completar el proceso. En este documento se explican los pasos necesarios para completar una actualización de scaffolding de identidad.

Se recomienda usar un sistema de control de código fuente que muestre las diferencias de archivos y le permite deshacer los cambios. Inspeccione los cambios después de ejecutar el scaffolding de identidad.

Los servicios son necesarios cuando se usa la [autenticación en dos fases](xref:security/authentication/identity-enable-qrcodes), la [confirmación de la cuenta y la recuperación de contraseña](xref:security/authentication/accconfirm)y otras características de seguridad con identidad. Los servicios o los códigos auxiliares de servicio no se generan cuando se trata de una identidad de scaffolding. Los servicios para habilitar estas características deben agregarse manualmente. Por ejemplo, vea [requerir confirmación de correo electrónico](xref:security/authentication/accconfirm#require-email-confirmation).

Al aplicar scaffolding a la identidad con un nuevo contexto de datos en un proyecto con cuentas individuales existentes:

* En `Startup.ConfigureServices`, quite las llamadas a:
  * `AddDbContext`
  * `AddDefaultIdentity`

Por ejemplo, `AddDbContext` y `AddDefaultIdentity` están comentados en el código siguiente:

[!code-csharp[](scaffold-identity/3.1sample/StartupRemove.cs?name=snippet)]

El código anterior comenta el código que está duplicado en *areas/Identity/IdentityHostingStartup. cs.*

Normalmente, las aplicaciones que se crearon con cuentas individuales ***no*** deben crear un nuevo contexto de datos.

## <a name="scaffold-identity-into-an-empty-project"></a>Identidad de scaffolding en un proyecto vacío

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Actualice la `Startup` clase con código similar al siguiente:

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a>Identidad scaffolding en un proyecto Razor sin autorización existente

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

La identidad se configura en *areas/Identity/IdentityHostingStartup. CS*. para obtener más información, vea [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a>Migraciones, UseAuthentication y diseño

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a>Enable authentication (Habilitar autenticación)

Actualice la `Startup` clase con código similar al siguiente:

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a>Cambios de diseño

Opcional: agregue el inicio de sesión`_LoginPartial`parcial () al archivo de diseño:

[!code-html[Main](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a>Identidad de scaffolding en un proyecto de Razor con autorización

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
Algunas opciones de identidad están configuradas en *areas/Identity/IdentityHostingStartup. CS*. Para obtener más información, vea [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a>Identidad de scaffolding en un proyecto de MVC sin autorización existente

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

Opcional: agregue el inicio de sesión`_LoginPartial`parcial () al archivo *views/Shared/_Layout. cshtml* :

[!code-html[Main](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* Mueva el archivo *pages/Shared/_LoginPartial. cshtml* a *views/Shared/_LoginPartial. cshtml*

La identidad se configura en *areas/Identity/IdentityHostingStartup. CS*. Para obtener más información, vea IHostingStartup.

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

Actualice la `Startup` clase con código similar al siguiente:

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a>Identidad de scaffolding en un proyecto de MVC con autorización

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a>Crear origen de la interfaz de usuario de identidad completa

Para mantener el control total de la interfaz de usuario de identidad, ejecute el scaffolding de identidad y seleccione **invalidar todos los archivos**.

En el código resaltado siguiente se muestran los cambios para reemplazar la interfaz de usuario de identidad predeterminada por la identidad en una aplicación Web de ASP.NET Core 2,1. Puede que desee hacer esto para tener un control total de la interfaz de usuario de identidad.

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

La identidad predeterminada se reemplaza en el código siguiente:

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

El código siguiente establece los valores de [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)y [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

Registre una `IEmailSender` implementación, por ejemplo:

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a>Deshabilitar registro (página)

Para deshabilitar el registro de usuario:

* Identidad scaffolding. Incluya account. Register, Account. login y account. RegisterConfirmation. Por ejemplo:

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* Actualice *areas/Identity/pages/Account/Register. cshtml. CS* para que los usuarios no puedan registrarse desde este punto de conexión:

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* Actualice *areas/Identity/pages/Account/Register. cshtml* para que sean coherentes con los cambios anteriores:

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* Comente o quite el vínculo de registro de las *áreas/identidad/páginas/cuenta/inicio de sesión. cshtml.*

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* Actualice la página *áreas/identidad/páginas/cuenta/RegisterConfirmation* .

  * Quite el código y los vínculos del archivo cshtml.
  * Quite el código de confirmación de `PageModel`:

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
  
### <a name="use-another-app-to-add-users"></a>Usar otra aplicación para agregar usuarios

Proporcionar un mecanismo para agregar usuarios fuera de la aplicación Web. Entre las opciones para agregar usuarios se incluyen:

* Una aplicación Web de administración dedicada.
* Una aplicación de consola.

En el código siguiente se describe un enfoque para agregar usuarios:

* Una lista de usuarios se lee en la memoria.
* Se genera una contraseña única segura para cada usuario.
* El usuario se agrega a la base de datos de identidad.
* Se notifica al usuario y se le indica que cambie la contraseña.

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

En el código siguiente se describe cómo agregar un usuario:

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

Se puede seguir un enfoque similar para escenarios de producción.

## <a name="prevent-publish-of-static-identity-assets"></a>Impedir la publicación de recursos de identidad estáticos

Para evitar la publicación de recursos de identidad estáticos en la raíz web, consulte <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>.

## <a name="additional-resources"></a>Recursos adicionales

* [Cambios en el código de autenticación en ASP.NET Core 2,1 y versiones posteriores](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core 2,1 y versiones posteriores proporcionan [ASP.net Core identidad](xref:security/authentication/identity) como una [biblioteca de clases de Razor](xref:razor-pages/ui-class). Las aplicaciones que incluyen la identidad pueden aplicar el scaffolding para agregar de forma selectiva el código fuente contenido en la biblioteca de clases Razor de identidad (RCL). Puede que quiera generar código fuente que le permita modificar un código y cambiar el comportamiento; así, por ejemplo, podría indicar al proveedor de scaffolding que generara el código que se usa en el registro. Dicho código generado tendrá prioridad sobre el mismo código en el RCL de Identity. Para obtener el control total de la interfaz de usuario y no usar el valor predeterminado de RCL, consulte la sección creación de un origen de la [interfaz de usuario de identidad completa](#full).

Las aplicaciones que **no** incluyen autenticación pueden aplicar el scaffolding para agregar el paquete de identidad de RCL. Existe la posibilidad de seleccionar el código de Identity que se va a generar.

Aunque el scaffolding genera la mayor parte del código necesario, tendrá que actualizar el proyecto para completar el proceso. En este documento se explican los pasos necesarios para completar una actualización de scaffolding de identidad.

Cuando se ejecuta el scaffolding de identidad, se crea un archivo *ScaffoldingReadme. txt* en el directorio del proyecto. El archivo *ScaffoldingReadme. txt* contiene instrucciones generales sobre lo que se necesita para completar la actualización del scaffolding de identidad. Este documento contiene instrucciones más completas que el archivo *ScaffoldingReadme. txt* .

Se recomienda usar un sistema de control de código fuente que muestre las diferencias de archivos y le permite deshacer los cambios. Inspeccione los cambios después de ejecutar el scaffolding de identidad.

> [!NOTE]
> Los servicios son necesarios cuando se usa la [autenticación en dos fases](xref:security/authentication/identity-enable-qrcodes), la [confirmación de la cuenta y la recuperación de contraseña](xref:security/authentication/accconfirm)y otras características de seguridad con identidad. Los servicios o los códigos auxiliares de servicio no se generan cuando se trata de una identidad de scaffolding. Los servicios para habilitar estas características deben agregarse manualmente. Por ejemplo, vea [requerir confirmación de correo electrónico](xref:security/authentication/accconfirm#require-email-confirmation).

## <a name="scaffold-identity-into-an-empty-project"></a>Identidad de scaffolding en un proyecto vacío

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Agregue las siguientes llamadas resaltadas `Startup` a la clase:

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a>Identidad scaffolding en un proyecto Razor sin autorización existente

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

La identidad se configura en *areas/Identity/IdentityHostingStartup. CS*. para obtener más información, vea [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a>Migraciones, UseAuthentication y diseño

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a>Enable authentication (Habilitar autenticación)

En el `Configure` método de la `Startup` clase, llame [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) a UseAuthentication `UseStaticFiles`después de:

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a>Cambios de diseño

Opcional: agregue el inicio de sesión`_LoginPartial`parcial () al archivo de diseño:

[!code-html[Main](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a>Identidad de scaffolding en un proyecto de Razor con autorización

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
Algunas opciones de identidad están configuradas en *areas/Identity/IdentityHostingStartup. CS*. Para obtener más información, vea [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a>Identidad de scaffolding en un proyecto de MVC sin autorización existente

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

Opcional: agregue el inicio de sesión`_LoginPartial`parcial () al archivo *views/Shared/_Layout. cshtml* :

[!code-html[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* Mueva el archivo *pages/Shared/_LoginPartial. cshtml* a *views/Shared/_LoginPartial. cshtml*

La identidad se configura en *areas/Identity/IdentityHostingStartup. CS*. Para obtener más información, vea IHostingStartup.

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

Llame [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) a UseAuthentication `UseStaticFiles`después de:

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a>Identidad de scaffolding en un proyecto de MVC con autorización

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

Elimine las *páginas/carpetas compartidas* y los archivos de esa carpeta.

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a>Crear origen de la interfaz de usuario de identidad completa

Para mantener el control total de la interfaz de usuario de identidad, ejecute el scaffolding de identidad y seleccione **invalidar todos los archivos**.

En el código resaltado siguiente se muestran los cambios para reemplazar la interfaz de usuario de identidad predeterminada por la identidad en una aplicación Web de ASP.NET Core 2,1. Puede que desee hacer esto para tener un control total de la interfaz de usuario de identidad.

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

La identidad predeterminada se reemplaza en el código siguiente:

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

El código siguiente establece los valores de [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)y [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

Registre una `IEmailSender` implementación, por ejemplo:

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a>Deshabilitar registro (página)

Para deshabilitar el registro de usuario:

* Identidad scaffolding. Incluya account. Register, Account. login y account. RegisterConfirmation. Por ejemplo:

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* Actualice *areas/Identity/pages/Account/Register. cshtml. CS* para que los usuarios no puedan registrarse desde este punto de conexión:

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* Actualice *areas/Identity/pages/Account/Register. cshtml* para que sean coherentes con los cambios anteriores:

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* Comente o quite el vínculo de registro de las *áreas/identidad/páginas/cuenta/inicio de sesión. cshtml.*

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* Actualice la página *áreas/identidad/páginas/cuenta/RegisterConfirmation* .

  * Quite el código y los vínculos del archivo cshtml.
  * Quite el código de confirmación de `PageModel`:

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
  
### <a name="use-another-app-to-add-users"></a>Usar otra aplicación para agregar usuarios

Proporcionar un mecanismo para agregar usuarios fuera de la aplicación Web. Entre las opciones para agregar usuarios se incluyen:

* Una aplicación Web de administración dedicada.
* Una aplicación de consola.

En el código siguiente se describe un enfoque para agregar usuarios:

* Una lista de usuarios se lee en la memoria.
* Se genera una contraseña única segura para cada usuario.
* El usuario se agrega a la base de datos de identidad.
* Se notifica al usuario y se le indica que cambie la contraseña.

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

En el código siguiente se describe cómo agregar un usuario:

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

Se puede seguir un enfoque similar para escenarios de producción.

## <a name="additional-resources"></a>Recursos adicionales

* [Cambios en el código de autenticación en ASP.NET Core 2,1 y versiones posteriores](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end