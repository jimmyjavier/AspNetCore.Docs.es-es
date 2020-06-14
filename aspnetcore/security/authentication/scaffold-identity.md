---
title: Scaffolding Identity en proyectos de ASP.net Core
author: rick-anderson
description: Obtenga información sobre cómo aplicar la técnica scaffolding Identity en un proyecto ASP.net Core.
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
ms.openlocfilehash: 36afa8ece58843b434ebfba6305bffdb9eb9bca0
ms.sourcegitcommit: d243fadeda20ad4f142ea60301ae5f5e0d41ed60
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/12/2020
ms.locfileid: "84724294"
---
# <a name="scaffold-identity-in-aspnet-core-projects"></a><span data-ttu-id="84526-103">Scaffolding Identity en proyectos de ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="84526-103">Scaffold Identity in ASP.NET Core projects</span></span>

<span data-ttu-id="84526-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="84526-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="84526-105">ASP.NET Core proporciona [ASP.net Core Identity ](xref:security/authentication/identity) como una [ Razor biblioteca de clases](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="84526-105">ASP.NET Core provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="84526-106">Las aplicaciones que incluyen Identity pueden aplicar el scaffolding para agregar selectivamente el código fuente incluido en la Identity Razor biblioteca de clases (RCL).</span><span class="sxs-lookup"><span data-stu-id="84526-106">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="84526-107">Puede que quiera generar código fuente que le permita modificar un código y cambiar el comportamiento;</span><span class="sxs-lookup"><span data-stu-id="84526-107">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="84526-108">así, por ejemplo, podría indicar al proveedor de scaffolding que generara el código que se usa en el registro.</span><span class="sxs-lookup"><span data-stu-id="84526-108">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="84526-109">El código generado tiene prioridad sobre el mismo código de la RCL de Identity.</span><span class="sxs-lookup"><span data-stu-id="84526-109">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="84526-110">Para obtener un control total de la interfaz de usuario y no usar el valor predeterminado de RCL, consulte la sección creación de un [ Identity origen de IU completo](#full).</span><span class="sxs-lookup"><span data-stu-id="84526-110">To gain full control of the UI and not use the default RCL, see the section [Create full Identity UI source](#full).</span></span>

<span data-ttu-id="84526-111">Las aplicaciones que **no** incluyen autenticación pueden aplicar el scaffolding para agregar el paquete RCL Identity .</span><span class="sxs-lookup"><span data-stu-id="84526-111">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="84526-112">Tiene la opción de seleccionar el código de Identity que se va a generar.</span><span class="sxs-lookup"><span data-stu-id="84526-112">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="84526-113">Aunque el scaffolding genera la mayor parte del código necesario, debe actualizar el proyecto para completar el proceso.</span><span class="sxs-lookup"><span data-stu-id="84526-113">Although the scaffolder generates most of the necessary code, you need to update your project to complete the process.</span></span> <span data-ttu-id="84526-114">En este documento se explican los pasos necesarios para completar una Identity actualización de scaffolding.</span><span class="sxs-lookup"><span data-stu-id="84526-114">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="84526-115">Se recomienda usar un sistema de control de código fuente que muestre las diferencias de archivos y le permite deshacer los cambios.</span><span class="sxs-lookup"><span data-stu-id="84526-115">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="84526-116">Inspeccione los cambios después de ejecutar el Identity scaffolding.</span><span class="sxs-lookup"><span data-stu-id="84526-116">Inspect the changes after running the Identity scaffolder.</span></span>

<span data-ttu-id="84526-117">Los servicios son necesarios cuando se usa la [autenticación en dos fases](xref:security/authentication/identity-enable-qrcodes), la [confirmación de la cuenta y la recuperación de contraseña](xref:security/authentication/accconfirm), y otras características de seguridad con Identity .</span><span class="sxs-lookup"><span data-stu-id="84526-117">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="84526-118">Los servicios o los códigos auxiliares de servicio no se generan al aplicar scaffolding Identity .</span><span class="sxs-lookup"><span data-stu-id="84526-118">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="84526-119">Los servicios para habilitar estas características deben agregarse manualmente.</span><span class="sxs-lookup"><span data-stu-id="84526-119">Services to enable these features must be added manually.</span></span> <span data-ttu-id="84526-120">Por ejemplo, vea [requerir confirmación de correo electrónico](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="84526-120">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

<span data-ttu-id="84526-121">Cuando se scaffolding Identity con un nuevo contexto de datos en un proyecto con cuentas individuales existentes:</span><span class="sxs-lookup"><span data-stu-id="84526-121">When scaffolding Identity with a new data context into a project with existing individual accounts:</span></span>

* <span data-ttu-id="84526-122">En `Startup.ConfigureServices` , quite las llamadas a:</span><span class="sxs-lookup"><span data-stu-id="84526-122">In `Startup.ConfigureServices`, remove the calls to:</span></span>
  * `AddDbContext`
  * `AddDefaultIdentity`

<span data-ttu-id="84526-123">Por ejemplo, `AddDbContext` y `AddDefaultIdentity` están comentados en el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="84526-123">For example, `AddDbContext` and `AddDefaultIdentity` are commented out in the following code:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRemove.cs?name=snippet)]

<span data-ttu-id="84526-124">El código anterior comenta el código que está duplicado en *areas/ Identity /IdentityHostingStartup.CS*</span><span class="sxs-lookup"><span data-stu-id="84526-124">The preceeding code comments out the code that is duplicated in *Areas/Identity/IdentityHostingStartup.cs*</span></span>

<span data-ttu-id="84526-125">Normalmente, las aplicaciones que se crearon con cuentas individuales ***no*** deben crear un nuevo contexto de datos.</span><span class="sxs-lookup"><span data-stu-id="84526-125">Typically, apps that were created with individual accounts should ***not*** create a new data context.</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="84526-126">Scaffolding Identity en un proyecto vacío</span><span class="sxs-lookup"><span data-stu-id="84526-126">Scaffold Identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="84526-127">Actualice la `Startup` clase con código similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="84526-127">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="84526-128">Scaffolding Identity en un Razor proyecto sin autorización existente</span><span class="sxs-lookup"><span data-stu-id="84526-128">Scaffold Identity into a Razor project without existing authorization</span></span>

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

Identity<span data-ttu-id="84526-129">está configurado en *areas/ Identity /IdentityHostingStartup.CS*.</span><span class="sxs-lookup"><span data-stu-id="84526-129"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="84526-130">Para obtener más información, vea [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="84526-130">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="84526-131">Migraciones, UseAuthentication y diseño</span><span class="sxs-lookup"><span data-stu-id="84526-131">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="84526-132">Enable authentication (Habilitar autenticación)</span><span class="sxs-lookup"><span data-stu-id="84526-132">Enable authentication</span></span>

<span data-ttu-id="84526-133">Actualice la `Startup` clase con código similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="84526-133">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="84526-134">Cambios de diseño</span><span class="sxs-lookup"><span data-stu-id="84526-134">Layout changes</span></span>

<span data-ttu-id="84526-135">Opcional: agregue el inicio de sesión parcial ( `_LoginPartial` ) al archivo de diseño:</span><span class="sxs-lookup"><span data-stu-id="84526-135">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="84526-136">Scaffolding Identity en un Razor proyecto con autorización</span><span class="sxs-lookup"><span data-stu-id="84526-136">Scaffold Identity into a Razor project with authorization</span></span>

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

<span data-ttu-id="84526-137">Algunas Identity opciones se configuran en *areas/ Identity /IdentityHostingStartup.CS*.</span><span class="sxs-lookup"><span data-stu-id="84526-137">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="84526-138">Para obtener más información, vea [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="84526-138">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="84526-139">Scaffolding Identity en un proyecto de MVC sin autorización existente</span><span class="sxs-lookup"><span data-stu-id="84526-139">Scaffold Identity into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="84526-140">Opcional: agregue el inicio de sesión parcial ( `_LoginPartial` ) al archivo *views/Shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="84526-140">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* <span data-ttu-id="84526-141">Mueva el archivo *pages/Shared/_LoginPartial. cshtml* a *views/Shared/_LoginPartial. cshtml*</span><span class="sxs-lookup"><span data-stu-id="84526-141">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

Identity<span data-ttu-id="84526-142">está configurado en *areas/ Identity /IdentityHostingStartup.CS*.</span><span class="sxs-lookup"><span data-stu-id="84526-142"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="84526-143">Para obtener más información, vea IHostingStartup.</span><span class="sxs-lookup"><span data-stu-id="84526-143">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="84526-144">Actualice la `Startup` clase con código similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="84526-144">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="84526-145">Scaffolding Identity en un proyecto de MVC con autorización</span><span class="sxs-lookup"><span data-stu-id="84526-145">Scaffold Identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

## <a name="scaffold-identity-into-a-blazor-server-project-without-existing-authorization"></a><span data-ttu-id="84526-146">Scaffolding Identity en un Blazor proyecto de servidor sin autorización existente</span><span class="sxs-lookup"><span data-stu-id="84526-146">Scaffold Identity into a Blazor Server project without existing authorization</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Identity<span data-ttu-id="84526-147">está configurado en *areas/ Identity /IdentityHostingStartup.CS*.</span><span class="sxs-lookup"><span data-stu-id="84526-147"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="84526-148">Para obtener más información, vea [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="84526-148">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

### <a name="migrations"></a><span data-ttu-id="84526-149">Migraciones</span><span class="sxs-lookup"><span data-stu-id="84526-149">Migrations</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

### <a name="pass-an-xsrf-token-to-the-app"></a><span data-ttu-id="84526-150">Pasar un token XSRF a la aplicación</span><span class="sxs-lookup"><span data-stu-id="84526-150">Pass an XSRF token to the app</span></span>

<span data-ttu-id="84526-151">Los tokens se pueden pasar a los componentes:</span><span class="sxs-lookup"><span data-stu-id="84526-151">Tokens can be passed to components:</span></span>

* <span data-ttu-id="84526-152">Cuando los tokens de autenticación se aprovisionan y se guardan en la cookie de autenticación, se pueden pasar a los componentes.</span><span class="sxs-lookup"><span data-stu-id="84526-152">When authentication tokens are provisioned and saved to the authentication cookie, they can be passed to components.</span></span>
* Razor<span data-ttu-id="84526-153">los componentes no pueden usar `HttpContext` directamente, por lo que no hay manera de obtener un [token de falsificación de la solicitud (XSRF)](xref:security/anti-request-forgery) para publicar el Identity punto de conexión de cierre de sesión en `/Identity/Account/Logout` .</span><span class="sxs-lookup"><span data-stu-id="84526-153"> components can't use `HttpContext` directly, so there's no way to obtain an [anti-request forgery (XSRF) token](xref:security/anti-request-forgery) to POST to Identity's logout endpoint at `/Identity/Account/Logout`.</span></span> <span data-ttu-id="84526-154">Un token XSRF se puede pasar a los componentes.</span><span class="sxs-lookup"><span data-stu-id="84526-154">An XSRF token can be passed to components.</span></span>

<span data-ttu-id="84526-155">Para obtener más información, vea <xref:security/blazor/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.</span><span class="sxs-lookup"><span data-stu-id="84526-155">For more information, see <xref:security/blazor/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.</span></span>

<span data-ttu-id="84526-156">En el archivo *pages/_Host. cshtml* , establezca el token después de agregarlo a las `InitialApplicationState` `TokenProvider` clases y:</span><span class="sxs-lookup"><span data-stu-id="84526-156">In the *Pages/_Host.cshtml* file, establish the token after adding it to the `InitialApplicationState` and `TokenProvider` classes:</span></span>

```csharp
@inject Microsoft.AspNetCore.Antiforgery.IAntiforgery Xsrf

...

var tokens = new InitialApplicationState
{
    ...

    XsrfToken = Xsrf.GetAndStoreTokens(HttpContext).RequestToken
};
```

<span data-ttu-id="84526-157">Actualice el `App` componente (*app. Razor*) para asignar `InitialState.XsrfToken` :</span><span class="sxs-lookup"><span data-stu-id="84526-157">Update the `App` component (*App.razor*) to assign the `InitialState.XsrfToken`:</span></span>

```csharp
@inject TokenProvider TokenProvider

...

TokenProvider.XsrfToken = InitialState.XsrfToken;
```

<span data-ttu-id="84526-158">El `TokenProvider` servicio que se muestra en el tema se usa en el `LoginDisplay` componente en la siguiente sección [diseño y cambios de flujo de autenticación](#layout-and-authentication-flow-changes) .</span><span class="sxs-lookup"><span data-stu-id="84526-158">The `TokenProvider` service demonstrated in the topic is used in the `LoginDisplay` component in the following [Layout and authentication flow changes](#layout-and-authentication-flow-changes) section.</span></span>

### <a name="enable-authentication"></a><span data-ttu-id="84526-159">Enable authentication (Habilitar autenticación)</span><span class="sxs-lookup"><span data-stu-id="84526-159">Enable authentication</span></span>

<span data-ttu-id="84526-160">En la `Startup` clase:</span><span class="sxs-lookup"><span data-stu-id="84526-160">In the `Startup` class:</span></span>

* <span data-ttu-id="84526-161">Confirme que Razor se han agregado los servicios de páginas en `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="84526-161">Confirm that Razor Pages services are added in `Startup.ConfigureServices`.</span></span>
* <span data-ttu-id="84526-162">Si usa [TokenProvider](xref:security/blazor/server/additional-scenarios#pass-tokens-to-a-blazor-server-app), registre el servicio.</span><span class="sxs-lookup"><span data-stu-id="84526-162">If using the [TokenProvider](xref:security/blazor/server/additional-scenarios#pass-tokens-to-a-blazor-server-app), register the service.</span></span>
* <span data-ttu-id="84526-163">Llame a `UseDatabaseErrorPage` en el generador de aplicaciones en `Startup.Configure` para el entorno de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="84526-163">Call `UseDatabaseErrorPage` on the application builder in `Startup.Configure` for the Development environment.</span></span>
* <span data-ttu-id="84526-164">Llame a `UseAuthentication` y `UseAuthorization` después de `UseRouting` .</span><span class="sxs-lookup"><span data-stu-id="84526-164">Call `UseAuthentication` and `UseAuthorization` after `UseRouting`.</span></span>
* <span data-ttu-id="84526-165">Agregue un extremo para Razor las páginas.</span><span class="sxs-lookup"><span data-stu-id="84526-165">Add an endpoint for Razor Pages.</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupBlazor.cs?highlight=3,6,14,27-28,32)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-and-authentication-flow-changes"></a><span data-ttu-id="84526-166">Cambios en el flujo de la autenticación y el diseño</span><span class="sxs-lookup"><span data-stu-id="84526-166">Layout and authentication flow changes</span></span>

<span data-ttu-id="84526-167">Agregue un `RedirectToLogin` componente (*RedirectToLogin. Razor*) a la carpeta *compartida* de la aplicación en la raíz del proyecto:</span><span class="sxs-lookup"><span data-stu-id="84526-167">Add a `RedirectToLogin` component (*RedirectToLogin.razor*) to the app's *Shared* folder in the project root:</span></span>

```razor
@inject NavigationManager Navigation
@code {
    protected override void OnInitialized()
    {
        Navigation.NavigateTo("Identity/Account/Login?returnUrl=" +
            Uri.EscapeDataString(Navigation.Uri), true);
    }
}
```

Agregue un `LoginDisplay` componente (*LoginDisplay. Razor*) a la carpeta *compartida* de la aplicación. <span data-ttu-id="84526-169">El [servicio TokenProvider](xref:security/blazor/server/additional-scenarios#pass-tokens-to-a-blazor-server-app) proporciona el token XSRF para el formulario HTML que se envía al Identity punto de conexión de cierre de sesión:</span><span class="sxs-lookup"><span data-stu-id="84526-169">The [TokenProvider service](xref:security/blazor/server/additional-scenarios#pass-tokens-to-a-blazor-server-app) provides the XSRF token for the HTML form that POSTs to Identity's logout endpoint:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@inject NavigationManager Navigation
@inject TokenProvider TokenProvider

<AuthorizeView>
    <Authorized>
        <a href="Identity/Account/Manage/Index">
            Hello, @context.User.Identity.Name!
        </a>
        <form action="/Identity/Account/Logout?returnUrl=%2F" method="post">
            <button class="nav-link btn btn-link" type="submit">Logout</button>
            <input name="__RequestVerificationToken" type="hidden" 
                value="@TokenProvider.XsrfToken">
        </form>
    </Authorized>
    <NotAuthorized>
        <a href="Identity/Account/Register">Register</a>
        <a href="Identity/Account/Login">Login</a>
    </NotAuthorized>
</AuthorizeView>
```

<span data-ttu-id="84526-170">En el `MainLayout` componente (*Shared/MainLayout. Razor*), agregue el `LoginDisplay` componente al `<div>` contenido del elemento de la fila superior:</span><span class="sxs-lookup"><span data-stu-id="84526-170">In the `MainLayout` component (*Shared/MainLayout.razor*), add the `LoginDisplay` component to the top-row `<div>` element's content:</span></span>

```razor
<div class="top-row px-4 auth">
    <LoginDisplay />
    <a href="https://docs.microsoft.com/aspnet/" target="_blank">About</a>
</div>
```

### <a name="style-authentication-endpoints"></a><span data-ttu-id="84526-171">Extremos de autenticación de estilo</span><span class="sxs-lookup"><span data-stu-id="84526-171">Style authentication endpoints</span></span>

<span data-ttu-id="84526-172">Dado Blazor que el servidor usa páginas Razor Identity de páginas, el estilo de la interfaz de usuario cambia cuando un visitante navega entre Identity páginas y componentes.</span><span class="sxs-lookup"><span data-stu-id="84526-172">Because Blazor Server uses Razor Pages Identity pages, the styling of the UI changes when a visitor navigates between Identity pages and components.</span></span> <span data-ttu-id="84526-173">Tiene dos opciones para abordar los estilos de Incongruous:</span><span class="sxs-lookup"><span data-stu-id="84526-173">You have two options to address the incongruous styles:</span></span>

#### <a name="build-identity-components"></a><span data-ttu-id="84526-174">Componentes de compilación Identity</span><span class="sxs-lookup"><span data-stu-id="84526-174">Build Identity components</span></span>

<span data-ttu-id="84526-175">Un enfoque para utilizar los componentes de en Identity lugar de las páginas es crear Identity componentes de.</span><span class="sxs-lookup"><span data-stu-id="84526-175">An approach to using components for Identity instead of pages is to build Identity components.</span></span> <span data-ttu-id="84526-176">Dado `SignInManager` `UserManager` que y no se admiten en Razor componentes, use puntos de conexión de API en la Blazor aplicación de servidor para procesar las acciones de la cuenta de usuario.</span><span class="sxs-lookup"><span data-stu-id="84526-176">Because `SignInManager` and `UserManager` aren't supported in Razor components, use API endpoints in the Blazor Server app to process user account actions.</span></span>

#### <a name="use-a-custom-layout-with-blazor-app-styles"></a><span data-ttu-id="84526-177">Usar un diseño personalizado con Blazor estilos de aplicación</span><span class="sxs-lookup"><span data-stu-id="84526-177">Use a custom layout with Blazor app styles</span></span>

<span data-ttu-id="84526-178">El Identity diseño y los estilos de las páginas se pueden modificar para generar páginas que utilicen el Blazor tema predeterminado.</span><span class="sxs-lookup"><span data-stu-id="84526-178">The Identity pages layout and styles can be modified to produce pages that use the default Blazor theme.</span></span>

> [!NOTE]
> <span data-ttu-id="84526-179">El ejemplo de esta sección es simplemente un punto de partida para la personalización.</span><span class="sxs-lookup"><span data-stu-id="84526-179">The example in this section is merely a starting point for customization.</span></span> <span data-ttu-id="84526-180">Es probable que se necesite trabajo adicional para la mejor experiencia de usuario.</span><span class="sxs-lookup"><span data-stu-id="84526-180">Additional work is likely required for the best user experience.</span></span>

<span data-ttu-id="84526-181">Cree un nuevo `NavMenu_IdentityLayout` componente (*compartido/NavMenu_IdentityLayout. Razor*).</span><span class="sxs-lookup"><span data-stu-id="84526-181">Create a new `NavMenu_IdentityLayout` component (*Shared/NavMenu_IdentityLayout.razor*).</span></span> <span data-ttu-id="84526-182">Para el marcado y el código del componente, use el mismo contenido del componente de la aplicación `NavMenu` (*Shared/NavMenu. Razor*).</span><span class="sxs-lookup"><span data-stu-id="84526-182">For the markup and code of the component, use the same content of the app's `NavMenu` component (*Shared/NavMenu.razor*).</span></span> <span data-ttu-id="84526-183">Desseccione cualquier `NavLink` s en componentes que no se puedan alcanzar de forma anónima porque las redirecciones automáticas del `RedirectToLogin` componente no se realizan correctamente para los componentes que requieren autenticación o autorización.</span><span class="sxs-lookup"><span data-stu-id="84526-183">Strip out any `NavLink`s to components that can't be reached anonymously because automatic redirects in the `RedirectToLogin` component fail for components requiring authentication or authorization.</span></span>

<span data-ttu-id="84526-184">En el archivo *pages/Shared/layout. cshtml* , realice los cambios siguientes:</span><span class="sxs-lookup"><span data-stu-id="84526-184">In the *Pages/Shared/Layout.cshtml* file, make the following changes:</span></span>

* <span data-ttu-id="84526-185">Agregue Razor directivas a la parte superior del archivo para usar las aplicaciones auxiliares de etiquetas y los componentes de la aplicación en la carpeta *compartida* :</span><span class="sxs-lookup"><span data-stu-id="84526-185">Add Razor directives to the top of the file to use Tag Helpers and the app's components in the *Shared* folder:</span></span>

  ```cshtml
  @addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
  @using {APPLICATION ASSEMBLY}.Shared
  ```

  <span data-ttu-id="84526-186">Reemplace `{APPLICATION ASSEMBLY}` con el nombre de ensamblado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="84526-186">Replace `{APPLICATION ASSEMBLY}` with the app's assembly name.</span></span>

* <span data-ttu-id="84526-187">Agregue una `<base>` etiqueta y una Blazor hoja de estilos `<link>` al `<head>` contenido:</span><span class="sxs-lookup"><span data-stu-id="84526-187">Add a `<base>` tag and Blazor stylesheet `<link>` to the `<head>` content:</span></span>

  ```cshtml
  <base href="~/" />
  <link rel="stylesheet" href="~/css/site.css" />
  ```

* <span data-ttu-id="84526-188">Cambie el contenido de la `<body>` etiqueta a lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="84526-188">Change the content of the `<body>` tag to the following:</span></span>

  ```cshtml
  <div class="sidebar" style="float:left">
      <component type="typeof(NavMenu_IdentityLayout)" 
          render-mode="ServerPrerendered" />
  </div>

  <div class="main" style="padding-left:250px">
      <div class="top-row px-4">
          @{
              var result = Engine.FindView(ViewContext, "_LoginPartial", 
                  isMainPage: false);
          }
          @if (result.Success)
          {
              await Html.RenderPartialAsync("_LoginPartial");
          }
          else
          {
              throw new InvalidOperationException("The default Identity UI " +
                  "layout requires a partial view '_LoginPartial'.");
          }
          <a href="https://docs.microsoft.com/aspnet/" target="_blank">About</a>
      </div>

      <div class="content px-4">
          @RenderBody()
      </div>
  </div>

  <script src="~/Identity/lib/jquery/dist/jquery.min.js"></script>
  <script src="~/Identity/lib/bootstrap/dist/js/bootstrap.bundle.min.js"></script>
  <script src="~/Identity/js/site.js" asp-append-version="true"></script>
  @RenderSection("Scripts", required: false)
  <script src="_framework/blazor.server.js"></script>
  ```

## <a name="scaffold-identity-into-a-blazor-server-project-with-authorization"></a><span data-ttu-id="84526-189">Scaffolding Identity en un Blazor proyecto de servidor con autorización</span><span class="sxs-lookup"><span data-stu-id="84526-189">Scaffold Identity into a Blazor Server project with authorization</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="84526-190">Algunas Identity opciones se configuran en *areas/ Identity /IdentityHostingStartup.CS*.</span><span class="sxs-lookup"><span data-stu-id="84526-190">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="84526-191">Para obtener más información, vea [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="84526-191">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="84526-192">Crear Identity origen de IU completo</span><span class="sxs-lookup"><span data-stu-id="84526-192">Create full Identity UI source</span></span>

<span data-ttu-id="84526-193">Para mantener el control total de la Identity interfaz de usuario, ejecute el Identity scaffolding y seleccione **invalidar todos los archivos**.</span><span class="sxs-lookup"><span data-stu-id="84526-193">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="84526-194">En el código resaltado siguiente se muestran los cambios para reemplazar la Identity interfaz de usuario predeterminada por Identity en una aplicación web ASP.net Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="84526-194">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="84526-195">Puede que desee hacer esto para tener un control total de la Identity interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="84526-195">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="84526-196">El valor predeterminado Identity se reemplaza en el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="84526-196">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="84526-197">El código siguiente establece los valores de [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)y [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span><span class="sxs-lookup"><span data-stu-id="84526-197">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="84526-198">Registre una `IEmailSender` implementación, por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="84526-198">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-a-page"></a><span data-ttu-id="84526-199">Deshabilitar una página</span><span class="sxs-lookup"><span data-stu-id="84526-199">Disable a page</span></span>

<span data-ttu-id="84526-200">En esta sección se muestra cómo deshabilitar la página de registro, pero se puede usar el enfoque para deshabilitar cualquier página.</span><span class="sxs-lookup"><span data-stu-id="84526-200">This sections show how to disable the register page but the approach can be used to disable any page.</span></span>

<span data-ttu-id="84526-201">Para deshabilitar el registro de usuario:</span><span class="sxs-lookup"><span data-stu-id="84526-201">To disable user registration:</span></span>

* <span data-ttu-id="84526-202">Scaffold Identity .</span><span class="sxs-lookup"><span data-stu-id="84526-202">Scaffold Identity.</span></span> <span data-ttu-id="84526-203">Incluya account. Register, Account. login y account. RegisterConfirmation.</span><span class="sxs-lookup"><span data-stu-id="84526-203">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="84526-204">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="84526-204">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="84526-205">Actualice *areas/ Identity /pages/Account/Register.cshtml.CS* para que los usuarios no puedan registrarse desde este punto de conexión:</span><span class="sxs-lookup"><span data-stu-id="84526-205">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="84526-206">Actualice *areas/ Identity /pages/Account/Register.cshtml* para que sean coherentes con los cambios anteriores:</span><span class="sxs-lookup"><span data-stu-id="84526-206">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="84526-207">Comente o quite el vínculo de registro de las *áreas/ Identity /pages/Account/login.cshtml*</span><span class="sxs-lookup"><span data-stu-id="84526-207">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

  ```cshtml
  @*
  <p>
      <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
  </p>
  *@
  ```

* <span data-ttu-id="84526-208">Actualice la página *áreas/ Identity /pages/Account/RegisterConfirmation* .</span><span class="sxs-lookup"><span data-stu-id="84526-208">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="84526-209">Quite el código y los vínculos del archivo cshtml.</span><span class="sxs-lookup"><span data-stu-id="84526-209">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="84526-210">Quite el código de confirmación de `PageModel` :</span><span class="sxs-lookup"><span data-stu-id="84526-210">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="84526-211">Usar otra aplicación para agregar usuarios</span><span class="sxs-lookup"><span data-stu-id="84526-211">Use another app to add users</span></span>

<span data-ttu-id="84526-212">Proporcionar un mecanismo para agregar usuarios fuera de la aplicación Web.</span><span class="sxs-lookup"><span data-stu-id="84526-212">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="84526-213">Entre las opciones para agregar usuarios se incluyen:</span><span class="sxs-lookup"><span data-stu-id="84526-213">Options to add users include:</span></span>

* <span data-ttu-id="84526-214">Una aplicación Web de administración dedicada.</span><span class="sxs-lookup"><span data-stu-id="84526-214">A dedicated admin web app.</span></span>
* <span data-ttu-id="84526-215">Una aplicación de consola.</span><span class="sxs-lookup"><span data-stu-id="84526-215">A console app.</span></span>

<span data-ttu-id="84526-216">En el código siguiente se describe un enfoque para agregar usuarios:</span><span class="sxs-lookup"><span data-stu-id="84526-216">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="84526-217">Una lista de usuarios se lee en la memoria.</span><span class="sxs-lookup"><span data-stu-id="84526-217">A list of users is read into memory.</span></span>
* <span data-ttu-id="84526-218">Se genera una contraseña única segura para cada usuario.</span><span class="sxs-lookup"><span data-stu-id="84526-218">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="84526-219">El usuario se agrega a la Identity base de datos.</span><span class="sxs-lookup"><span data-stu-id="84526-219">The user is added to the Identity database.</span></span>
* <span data-ttu-id="84526-220">Se notifica al usuario y se le indica que cambie la contraseña.</span><span class="sxs-lookup"><span data-stu-id="84526-220">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="84526-221">En el código siguiente se describe cómo agregar un usuario:</span><span class="sxs-lookup"><span data-stu-id="84526-221">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="84526-222">Se puede seguir un enfoque similar para escenarios de producción.</span><span class="sxs-lookup"><span data-stu-id="84526-222">A similar approach can be followed for production scenarios.</span></span>

## <a name="prevent-publish-of-static-identity-assets"></a><span data-ttu-id="84526-223">Impedir la publicación de Identity recursos estáticos</span><span class="sxs-lookup"><span data-stu-id="84526-223">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="84526-224">Para evitar la publicación de recursos estáticos Identity en la raíz Web, vea <xref:security/authentication/identity#prevent-publish-of-static-identity-assets> .</span><span class="sxs-lookup"><span data-stu-id="84526-224">To prevent publishing static Identity assets to the web root, see <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="84526-225">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="84526-225">Additional resources</span></span>

* [<span data-ttu-id="84526-226">Cambios en el código de autenticación en ASP.NET Core 2,1 y versiones posteriores</span><span class="sxs-lookup"><span data-stu-id="84526-226">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="84526-227">ASP.NET Core 2,1 y versiones posteriores [proporcionan Identity ASP.net Core](xref:security/authentication/identity) como una [ Razor biblioteca de clases](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="84526-227">ASP.NET Core 2.1 and later provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="84526-228">Las aplicaciones que incluyen Identity pueden aplicar el scaffolding para agregar selectivamente el código fuente incluido en la Identity Razor biblioteca de clases (RCL).</span><span class="sxs-lookup"><span data-stu-id="84526-228">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="84526-229">Puede que quiera generar código fuente que le permita modificar un código y cambiar el comportamiento;</span><span class="sxs-lookup"><span data-stu-id="84526-229">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="84526-230">así, por ejemplo, podría indicar al proveedor de scaffolding que generara el código que se usa en el registro.</span><span class="sxs-lookup"><span data-stu-id="84526-230">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="84526-231">El código generado tiene prioridad sobre el mismo código de la RCL de Identity.</span><span class="sxs-lookup"><span data-stu-id="84526-231">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="84526-232">Para obtener el control total de la interfaz de usuario y no usar el valor predeterminado de RCL, consulte la sección creación de un origen de la [interfaz de usuario de identidad completa](#full).</span><span class="sxs-lookup"><span data-stu-id="84526-232">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="84526-233">Las aplicaciones que **no** incluyen autenticación pueden aplicar el scaffolding para agregar el paquete RCL Identity .</span><span class="sxs-lookup"><span data-stu-id="84526-233">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="84526-234">Tiene la opción de seleccionar el código de Identity que se va a generar.</span><span class="sxs-lookup"><span data-stu-id="84526-234">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="84526-235">Aunque el scaffolding genera la mayor parte del código necesario, tendrá que actualizar el proyecto para completar el proceso.</span><span class="sxs-lookup"><span data-stu-id="84526-235">Although the scaffolder generates most of the necessary code, you'll have to update your project to complete the process.</span></span> <span data-ttu-id="84526-236">En este documento se explican los pasos necesarios para completar una Identity actualización de scaffolding.</span><span class="sxs-lookup"><span data-stu-id="84526-236">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="84526-237">Cuando Identity se ejecuta el scaffolding, se crea un archivo de *ScaffoldingReadme.txt* en el directorio del proyecto.</span><span class="sxs-lookup"><span data-stu-id="84526-237">When the Identity scaffolder is run, a *ScaffoldingReadme.txt* file is created in the project directory.</span></span> <span data-ttu-id="84526-238">El archivo de *ScaffoldingReadme.txt* contiene instrucciones generales sobre lo que se necesita para completar la Identity actualización de scaffolding.</span><span class="sxs-lookup"><span data-stu-id="84526-238">The *ScaffoldingReadme.txt* file contains general instructions on what's needed to complete the Identity scaffolding update.</span></span> <span data-ttu-id="84526-239">Este documento contiene instrucciones más completas que el archivo de *ScaffoldingReadme.txt* .</span><span class="sxs-lookup"><span data-stu-id="84526-239">This document contains more complete instructions than the *ScaffoldingReadme.txt* file.</span></span>

<span data-ttu-id="84526-240">Se recomienda usar un sistema de control de código fuente que muestre las diferencias de archivos y le permite deshacer los cambios.</span><span class="sxs-lookup"><span data-stu-id="84526-240">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="84526-241">Inspeccione los cambios después de ejecutar el Identity scaffolding.</span><span class="sxs-lookup"><span data-stu-id="84526-241">Inspect the changes after running the Identity scaffolder.</span></span>

> [!NOTE]
> <span data-ttu-id="84526-242">Los servicios son necesarios cuando se usa la [autenticación en dos fases](xref:security/authentication/identity-enable-qrcodes), la [confirmación de la cuenta y la recuperación de contraseña](xref:security/authentication/accconfirm), y otras características de seguridad con Identity .</span><span class="sxs-lookup"><span data-stu-id="84526-242">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="84526-243">Los servicios o los códigos auxiliares de servicio no se generan al aplicar scaffolding Identity .</span><span class="sxs-lookup"><span data-stu-id="84526-243">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="84526-244">Los servicios para habilitar estas características deben agregarse manualmente.</span><span class="sxs-lookup"><span data-stu-id="84526-244">Services to enable these features must be added manually.</span></span> <span data-ttu-id="84526-245">Por ejemplo, vea [requerir confirmación de correo electrónico](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="84526-245">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="84526-246">Scaffolding Identity en un proyecto vacío</span><span class="sxs-lookup"><span data-stu-id="84526-246">Scaffold Identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="84526-247">Agregue las siguientes llamadas resaltadas a la `Startup` clase:</span><span class="sxs-lookup"><span data-stu-id="84526-247">Add the following highlighted calls to the `Startup` class:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="84526-248">Scaffolding Identity en un Razor proyecto sin autorización existente</span><span class="sxs-lookup"><span data-stu-id="84526-248">Scaffold Identity into a Razor project without existing authorization</span></span>

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

Identity<span data-ttu-id="84526-249">está configurado en *areas/ Identity /IdentityHostingStartup.CS*.</span><span class="sxs-lookup"><span data-stu-id="84526-249"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="84526-250">Para obtener más información, vea [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="84526-250">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="84526-251">Migraciones, UseAuthentication y diseño</span><span class="sxs-lookup"><span data-stu-id="84526-251">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="84526-252">Enable authentication (Habilitar autenticación)</span><span class="sxs-lookup"><span data-stu-id="84526-252">Enable authentication</span></span>

<span data-ttu-id="84526-253">En el `Configure` método de la `Startup` clase, llame a [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) después de `UseStaticFiles` :</span><span class="sxs-lookup"><span data-stu-id="84526-253">In the `Configure` method of the `Startup` class, call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="84526-254">Cambios de diseño</span><span class="sxs-lookup"><span data-stu-id="84526-254">Layout changes</span></span>

<span data-ttu-id="84526-255">Opcional: agregue el inicio de sesión parcial ( `_LoginPartial` ) al archivo de diseño:</span><span class="sxs-lookup"><span data-stu-id="84526-255">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="84526-256">Scaffolding Identity en un Razor proyecto con autorización</span><span class="sxs-lookup"><span data-stu-id="84526-256">Scaffold Identity into a Razor project with authorization</span></span>

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

<span data-ttu-id="84526-257">Algunas Identity opciones se configuran en *areas/ Identity /IdentityHostingStartup.CS*.</span><span class="sxs-lookup"><span data-stu-id="84526-257">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="84526-258">Para obtener más información, vea [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="84526-258">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="84526-259">Scaffolding Identity en un proyecto de MVC sin autorización existente</span><span class="sxs-lookup"><span data-stu-id="84526-259">Scaffold Identity into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="84526-260">Opcional: agregue el inicio de sesión parcial ( `_LoginPartial` ) al archivo *views/Shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="84526-260">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* <span data-ttu-id="84526-261">Mueva el archivo *pages/Shared/_LoginPartial. cshtml* a *views/Shared/_LoginPartial. cshtml*</span><span class="sxs-lookup"><span data-stu-id="84526-261">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

Identity<span data-ttu-id="84526-262">está configurado en *areas/ Identity /IdentityHostingStartup.CS*.</span><span class="sxs-lookup"><span data-stu-id="84526-262"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="84526-263">Para obtener más información, vea IHostingStartup.</span><span class="sxs-lookup"><span data-stu-id="84526-263">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="84526-264">Llame a [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) después de `UseStaticFiles` :</span><span class="sxs-lookup"><span data-stu-id="84526-264">Call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="84526-265">Scaffolding Identity en un proyecto de MVC con autorización</span><span class="sxs-lookup"><span data-stu-id="84526-265">Scaffold Identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="84526-266">Elimine las *páginas/carpetas compartidas* y los archivos de esa carpeta.</span><span class="sxs-lookup"><span data-stu-id="84526-266">Delete the *Pages/Shared* folder and the files in that folder.</span></span>

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="84526-267">Crear Identity origen de IU completo</span><span class="sxs-lookup"><span data-stu-id="84526-267">Create full Identity UI source</span></span>

<span data-ttu-id="84526-268">Para mantener el control total de la Identity interfaz de usuario, ejecute el Identity scaffolding y seleccione **invalidar todos los archivos**.</span><span class="sxs-lookup"><span data-stu-id="84526-268">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="84526-269">En el código resaltado siguiente se muestran los cambios para reemplazar la Identity interfaz de usuario predeterminada por Identity en una aplicación web ASP.net Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="84526-269">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="84526-270">Puede que desee hacer esto para tener un control total de la Identity interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="84526-270">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="84526-271">El valor predeterminado Identity se reemplaza en el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="84526-271">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="84526-272">El código siguiente establece los valores de [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)y [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span><span class="sxs-lookup"><span data-stu-id="84526-272">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="84526-273">Registre una `IEmailSender` implementación, por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="84526-273">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a><span data-ttu-id="84526-274">Deshabilitar registro (página)</span><span class="sxs-lookup"><span data-stu-id="84526-274">Disable register page</span></span>

<span data-ttu-id="84526-275">Para deshabilitar el registro de usuario:</span><span class="sxs-lookup"><span data-stu-id="84526-275">To disable user registration:</span></span>

* <span data-ttu-id="84526-276">Scaffold Identity .</span><span class="sxs-lookup"><span data-stu-id="84526-276">Scaffold Identity.</span></span> <span data-ttu-id="84526-277">Incluya account. Register, Account. login y account. RegisterConfirmation.</span><span class="sxs-lookup"><span data-stu-id="84526-277">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="84526-278">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="84526-278">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="84526-279">Actualice *areas/ Identity /pages/Account/Register.cshtml.CS* para que los usuarios no puedan registrarse desde este punto de conexión:</span><span class="sxs-lookup"><span data-stu-id="84526-279">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="84526-280">Actualice *areas/ Identity /pages/Account/Register.cshtml* para que sean coherentes con los cambios anteriores:</span><span class="sxs-lookup"><span data-stu-id="84526-280">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="84526-281">Comente o quite el vínculo de registro de las *áreas/ Identity /pages/Account/login.cshtml*</span><span class="sxs-lookup"><span data-stu-id="84526-281">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="84526-282">Actualice la página *áreas/ Identity /pages/Account/RegisterConfirmation* .</span><span class="sxs-lookup"><span data-stu-id="84526-282">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="84526-283">Quite el código y los vínculos del archivo cshtml.</span><span class="sxs-lookup"><span data-stu-id="84526-283">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="84526-284">Quite el código de confirmación de `PageModel` :</span><span class="sxs-lookup"><span data-stu-id="84526-284">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="84526-285">Usar otra aplicación para agregar usuarios</span><span class="sxs-lookup"><span data-stu-id="84526-285">Use another app to add users</span></span>

<span data-ttu-id="84526-286">Proporcionar un mecanismo para agregar usuarios fuera de la aplicación Web.</span><span class="sxs-lookup"><span data-stu-id="84526-286">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="84526-287">Entre las opciones para agregar usuarios se incluyen:</span><span class="sxs-lookup"><span data-stu-id="84526-287">Options to add users include:</span></span>

* <span data-ttu-id="84526-288">Una aplicación Web de administración dedicada.</span><span class="sxs-lookup"><span data-stu-id="84526-288">A dedicated admin web app.</span></span>
* <span data-ttu-id="84526-289">Una aplicación de consola.</span><span class="sxs-lookup"><span data-stu-id="84526-289">A console app.</span></span>

<span data-ttu-id="84526-290">En el código siguiente se describe un enfoque para agregar usuarios:</span><span class="sxs-lookup"><span data-stu-id="84526-290">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="84526-291">Una lista de usuarios se lee en la memoria.</span><span class="sxs-lookup"><span data-stu-id="84526-291">A list of users is read into memory.</span></span>
* <span data-ttu-id="84526-292">Se genera una contraseña única segura para cada usuario.</span><span class="sxs-lookup"><span data-stu-id="84526-292">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="84526-293">El usuario se agrega a la Identity base de datos.</span><span class="sxs-lookup"><span data-stu-id="84526-293">The user is added to the Identity database.</span></span>
* <span data-ttu-id="84526-294">Se notifica al usuario y se le indica que cambie la contraseña.</span><span class="sxs-lookup"><span data-stu-id="84526-294">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="84526-295">En el código siguiente se describe cómo agregar un usuario:</span><span class="sxs-lookup"><span data-stu-id="84526-295">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="84526-296">Se puede seguir un enfoque similar para escenarios de producción.</span><span class="sxs-lookup"><span data-stu-id="84526-296">A similar approach can be followed for production scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="84526-297">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="84526-297">Additional resources</span></span>

* [<span data-ttu-id="84526-298">Cambios en el código de autenticación en ASP.NET Core 2,1 y versiones posteriores</span><span class="sxs-lookup"><span data-stu-id="84526-298">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end
