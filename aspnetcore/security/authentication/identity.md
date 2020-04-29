---
title: Introducción a la identidad en ASP.NET Core
author: rick-anderson
description: Usar Identity con una aplicación ASP.NET Core. Obtenga información sobre cómo establecer los requisitos de contraseña (RequireDigit, RequiredLength, RequiredUniqueChars, etc.).
ms.author: riande
ms.date: 01/15/2020
uid: security/authentication/identity
ms.openlocfilehash: 4bc5f206b3aee7c2d34055703acc5b6c5218f964
ms.sourcegitcommit: 56861af66bb364a5d60c3c72d133d854b4cf292d
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2020
ms.locfileid: "82205948"
---
# <a name="introduction-to-identity-on-aspnet-core"></a><span data-ttu-id="81487-104">Introducción a la identidad en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="81487-104">Introduction to Identity on ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="81487-105">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="81487-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="81487-106">Identidad de ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="81487-106">ASP.NET Core Identity:</span></span>

* <span data-ttu-id="81487-107">Es una API que admite la funcionalidad de inicio de sesión de la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="81487-107">Is an API that supports user interface (UI) login functionality.</span></span>
* <span data-ttu-id="81487-108">Administra usuarios, contraseñas, datos de perfil, roles, notificaciones, tokens, confirmación de correo electrónico, etc.</span><span class="sxs-lookup"><span data-stu-id="81487-108">Manages users, passwords, profile data, roles, claims, tokens, email confirmation, and more.</span></span>

<span data-ttu-id="81487-109">Los usuarios pueden crear una cuenta con la información de inicio de sesión almacenada en la identidad o pueden utilizar un proveedor de inicio de sesión externo.</span><span class="sxs-lookup"><span data-stu-id="81487-109">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="81487-110">Entre los proveedores de inicio de sesión externos admitidos se incluyen [Facebook, Google, cuenta de Microsoft y Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="81487-110">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="81487-111">El [código fuente de identidad](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) está disponible en github.</span><span class="sxs-lookup"><span data-stu-id="81487-111">The [Identity source code](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) is available on GitHub.</span></span> <span data-ttu-id="81487-112">La [identidad scaffolding](xref:security/authentication/scaffold-identity) y ver los archivos generados para revisar la interacción de la plantilla con la identidad.</span><span class="sxs-lookup"><span data-stu-id="81487-112">[Scaffold Identity](xref:security/authentication/scaffold-identity) and view the generated files to review the template interaction with Identity.</span></span>

<span data-ttu-id="81487-113">La identidad se configura normalmente con una base de datos SQL Server para almacenar nombres de usuario, contraseñas y datos de perfil.</span><span class="sxs-lookup"><span data-stu-id="81487-113">Identity is typically configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="81487-114">Como alternativa, se puede usar otro almacén persistente, por ejemplo, Azure Table Storage.</span><span class="sxs-lookup"><span data-stu-id="81487-114">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="81487-115">En este tema, aprenderá a usar la identidad para registrarse, iniciar sesión y cerrar la sesión de un usuario.</span><span class="sxs-lookup"><span data-stu-id="81487-115">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="81487-116">Nota: las plantillas tratan el nombre de usuario y el correo electrónico como los mismos para los usuarios.</span><span class="sxs-lookup"><span data-stu-id="81487-116">Note: the templates treat username and email as the same for users.</span></span> <span data-ttu-id="81487-117">Para obtener instrucciones más detalladas sobre la creación de aplicaciones que usan la identidad, consulte la sección pasos siguientes al final de este artículo.</span><span class="sxs-lookup"><span data-stu-id="81487-117">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<span data-ttu-id="81487-118">La [plataforma de identidad de Microsoft](/azure/active-directory/develop/) es:</span><span class="sxs-lookup"><span data-stu-id="81487-118">[Microsoft identity platform](/azure/active-directory/develop/) is:</span></span>

* <span data-ttu-id="81487-119">Evolución de la plataforma de desarrollo de Azure Active Directory (Azure AD).</span><span class="sxs-lookup"><span data-stu-id="81487-119">An evolution of the Azure Active Directory (Azure AD) developer platform.</span></span>
* <span data-ttu-id="81487-120">No relacionado con ASP.NET Core identidad.</span><span class="sxs-lookup"><span data-stu-id="81487-120">Unrelated to ASP.NET Core Identity.</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

<span data-ttu-id="81487-121">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([Cómo descargar)](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="81487-121">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="81487-122">Creación de una aplicación web con autenticación</span><span class="sxs-lookup"><span data-stu-id="81487-122">Create a Web app with authentication</span></span>

<span data-ttu-id="81487-123">Cree un proyecto de aplicación Web de ASP.NET Core con cuentas de usuario individuales.</span><span class="sxs-lookup"><span data-stu-id="81487-123">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="81487-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="81487-124">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="81487-125">Seleccione **archivo** > **nuevo** > **proyecto**.</span><span class="sxs-lookup"><span data-stu-id="81487-125">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="81487-126">Seleccione **Aplicación web de ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="81487-126">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="81487-127">Asigne al proyecto el nombre **WebApp1** para que tenga el mismo espacio de nombres que la descarga del proyecto.</span><span class="sxs-lookup"><span data-stu-id="81487-127">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="81487-128">Haga clic en **OK**.</span><span class="sxs-lookup"><span data-stu-id="81487-128">Click **OK**.</span></span>
* <span data-ttu-id="81487-129">Seleccione una **aplicación web**de ASP.net Core y, a continuación, seleccione **cambiar autenticación**.</span><span class="sxs-lookup"><span data-stu-id="81487-129">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="81487-130">Seleccione **cuentas de usuario individuales** y haga clic en **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="81487-130">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="81487-131">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="81487-131">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

<span data-ttu-id="81487-132">El comando anterior crea una aplicación Web de Razor con SQLite.</span><span class="sxs-lookup"><span data-stu-id="81487-132">The preceding command creates a Razor web app using SQLite.</span></span> <span data-ttu-id="81487-133">Para crear la aplicación web con LocalDB, ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="81487-133">To create the web app with LocalDB, run the following command:</span></span>

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

<span data-ttu-id="81487-134">El proyecto generado proporciona [ASP.net Core identidad](xref:security/authentication/identity) como una [biblioteca de clases de Razor](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="81487-134">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="81487-135">La biblioteca de clases de Razor de identidad expone puntos de `Identity` conexión con el área.</span><span class="sxs-lookup"><span data-stu-id="81487-135">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="81487-136">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="81487-136">For example:</span></span>

* <span data-ttu-id="81487-137">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="81487-137">/Identity/Account/Login</span></span>
* <span data-ttu-id="81487-138">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="81487-138">/Identity/Account/Logout</span></span>
* <span data-ttu-id="81487-139">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="81487-139">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="81487-140">Aplicación de migraciones</span><span class="sxs-lookup"><span data-stu-id="81487-140">Apply migrations</span></span>

<span data-ttu-id="81487-141">Aplique las migraciones para inicializar la base de datos.</span><span class="sxs-lookup"><span data-stu-id="81487-141">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="81487-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="81487-142">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="81487-143">Ejecute el siguiente comando en la consola del administrador de paquetes (PMC):</span><span class="sxs-lookup"><span data-stu-id="81487-143">Run the following command in the Package Manager Console (PMC):</span></span>

`PM> Update-Database`

# <a name="net-core-cli"></a>[<span data-ttu-id="81487-144">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="81487-144">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="81487-145">No es necesario realizar migraciones en este paso al usar SQLite.</span><span class="sxs-lookup"><span data-stu-id="81487-145">Migrations are not necessary at this step when using SQLite.</span></span> <span data-ttu-id="81487-146">Para LocalDB, ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="81487-146">For LocalDB, run the following command:</span></span>

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="81487-147">Probar registro e inicio de sesión</span><span class="sxs-lookup"><span data-stu-id="81487-147">Test Register and Login</span></span>

<span data-ttu-id="81487-148">Ejecute la aplicación y registre un usuario.</span><span class="sxs-lookup"><span data-stu-id="81487-148">Run the app and register a user.</span></span> <span data-ttu-id="81487-149">En función del tamaño de la pantalla, es posible que tenga que seleccionar el botón de alternancia de navegación para ver los vínculos de **Inicio de sesión** y **registro** .</span><span class="sxs-lookup"><span data-stu-id="81487-149">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="81487-150">Configurar servicios de identidad</span><span class="sxs-lookup"><span data-stu-id="81487-150">Configure Identity services</span></span>

<span data-ttu-id="81487-151">Los servicios se agregan en `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="81487-151">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="81487-152">El patrón habitual consiste en llamar a todos los métodos `Add{Service}` y, luego, a todos los métodos `services.Configure{Service}`.</span><span class="sxs-lookup"><span data-stu-id="81487-152">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=10-99)]

<span data-ttu-id="81487-153">El código resaltado anterior configura la identidad con valores de opción predeterminados.</span><span class="sxs-lookup"><span data-stu-id="81487-153">The preceding highlighted code configures Identity with default option values.</span></span> <span data-ttu-id="81487-154">Los servicios se ponen a disposición de la aplicación a través de la [inserción de dependencias](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="81487-154">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="81487-155">La identidad se habilita mediante <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>una llamada a.</span><span class="sxs-lookup"><span data-stu-id="81487-155">Identity is enabled by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>.</span></span> <span data-ttu-id="81487-156">`UseAuthentication`agrega [middleware](xref:fundamentals/middleware/index) de autenticación a la canalización de solicitudes.</span><span class="sxs-lookup"><span data-stu-id="81487-156">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

<span data-ttu-id="81487-157">La aplicación generada por la plantilla no utiliza la [autorización](xref:security/authorization/secure-data).</span><span class="sxs-lookup"><span data-stu-id="81487-157">The template-generated app doesn't use [authorization](xref:security/authorization/secure-data).</span></span> <span data-ttu-id="81487-158">`app.UseAuthorization`se incluye para asegurarse de que se agrega en el orden correcto para que la aplicación agregue autorización.</span><span class="sxs-lookup"><span data-stu-id="81487-158">`app.UseAuthorization` is included to ensure it's added in the correct order should the app add authorization.</span></span> <span data-ttu-id="81487-159">`UseRouting`se `UseAuthentication`debe `UseAuthorization`llamar a `UseEndpoints` ,, y en el orden mostrado en el código anterior.</span><span class="sxs-lookup"><span data-stu-id="81487-159">`UseRouting`, `UseAuthentication`, `UseAuthorization`, and `UseEndpoints` must be called in the order shown in the preceding code.</span></span>

<span data-ttu-id="81487-160">Para obtener más información `IdentityOptions` sobre `Startup`y, <xref:Microsoft.AspNetCore.Identity.IdentityOptions> vea e inicio de la [aplicación](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="81487-160">For more information on `IdentityOptions` and `Startup`, see <xref:Microsoft.AspNetCore.Identity.IdentityOptions> and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="81487-161">Registro de scaffolding, Inicio de sesión y cierre de sesión</span><span class="sxs-lookup"><span data-stu-id="81487-161">Scaffold Register, Login, and LogOut</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="81487-162">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="81487-162">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="81487-163">Agregue los archivos de registro, Inicio de sesión y cierre de sesión.</span><span class="sxs-lookup"><span data-stu-id="81487-163">Add the Register, Login, and LogOut files.</span></span> <span data-ttu-id="81487-164">Siga la [identidad scaffolding en un proyecto Razor con](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instrucciones de autorización para generar el código que se muestra en esta sección.</span><span class="sxs-lookup"><span data-stu-id="81487-164">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="81487-165">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="81487-165">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="81487-166">Si ha creado el proyecto con el nombre **WebApp1**, ejecute los siguientes comandos.</span><span class="sxs-lookup"><span data-stu-id="81487-166">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="81487-167">De lo contrario, use el espacio de `ApplicationDbContext`nombres correcto para:</span><span class="sxs-lookup"><span data-stu-id="81487-167">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="81487-168">PowerShell usa punto y coma como separador de comandos.</span><span class="sxs-lookup"><span data-stu-id="81487-168">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="81487-169">Al usar PowerShell, use el carácter de escape en la lista de archivos o ponga la lista de archivos entre comillas dobles, como se muestra en el ejemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="81487-169">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

<span data-ttu-id="81487-170">Para obtener más información sobre la identidad de scaffolding, vea [scaffolding Identity into a Razor Project with Authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="81487-170">For more information on scaffolding Identity, see [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="81487-171">Examinar registro</span><span class="sxs-lookup"><span data-stu-id="81487-171">Examine Register</span></span>

<span data-ttu-id="81487-172">Cuando un usuario hace clic en **Register** el vínculo de registro `RegisterModel.OnPostAsync` , se invoca la acción.</span><span class="sxs-lookup"><span data-stu-id="81487-172">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="81487-173">El usuario lo crea [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) en el `_userManager` objeto.</span><span class="sxs-lookup"><span data-stu-id="81487-173">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object.</span></span> <span data-ttu-id="81487-174">`_userManager`la inserción de dependencias proporciona:</span><span class="sxs-lookup"><span data-stu-id="81487-174">`_userManager` is provided by dependency injection):</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

<span data-ttu-id="81487-175">Si el usuario se creó correctamente, el usuario inicia sesión en la llamada a `_signInManager.SignInAsync`.</span><span class="sxs-lookup"><span data-stu-id="81487-175">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="81487-176">Consulte [confirmación](xref:security/authentication/accconfirm#prevent-login-at-registration) de la cuenta para conocer los pasos para evitar el inicio de sesión inmediato en el registro.</span><span class="sxs-lookup"><span data-stu-id="81487-176">See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="81487-177">Registro</span><span class="sxs-lookup"><span data-stu-id="81487-177">Log in</span></span>

<span data-ttu-id="81487-178">El formulario de inicio de sesión se muestra cuando:</span><span class="sxs-lookup"><span data-stu-id="81487-178">The Login form is displayed when:</span></span>

* <span data-ttu-id="81487-179">Se selecciona el vínculo **iniciar sesión** .</span><span class="sxs-lookup"><span data-stu-id="81487-179">The **Log in** link is selected.</span></span>
* <span data-ttu-id="81487-180">Un usuario intenta obtener acceso a una página restringida para la que no está autorizado para acceder **o** cuando el sistema no la ha autenticado.</span><span class="sxs-lookup"><span data-stu-id="81487-180">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="81487-181">Cuando se envía el formulario de la página de inicio de `OnPostAsync` sesión, se llama a la acción.</span><span class="sxs-lookup"><span data-stu-id="81487-181">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="81487-182">`PasswordSignInAsync`se llama a en `_signInManager` el objeto (proporcionado por la inserción de dependencias).</span><span class="sxs-lookup"><span data-stu-id="81487-182">`PasswordSignInAsync` is called on the `_signInManager` object (provided by dependency injection).</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="81487-183">La clase `Controller` base expone una `User` propiedad a la que se puede tener acceso desde métodos de controlador.</span><span class="sxs-lookup"><span data-stu-id="81487-183">The base `Controller` class exposes a `User` property that can be accessed from controller methods.</span></span> <span data-ttu-id="81487-184">Por ejemplo, puede enumerar `User.Claims` y tomar decisiones de autorización.</span><span class="sxs-lookup"><span data-stu-id="81487-184">For instance, you can enumerate `User.Claims` and make authorization decisions.</span></span> <span data-ttu-id="81487-185">Para obtener más información, vea <xref:security/authorization/introduction>.</span><span class="sxs-lookup"><span data-stu-id="81487-185">For more information, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="81487-186">Cerrar la sesión</span><span class="sxs-lookup"><span data-stu-id="81487-186">Log out</span></span>

<span data-ttu-id="81487-187">El vínculo de **cierre de sesión** invoca `LogoutModel.OnPost` la acción.</span><span class="sxs-lookup"><span data-stu-id="81487-187">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Logout.cshtml.cs?highlight=36)]

<span data-ttu-id="81487-188">En el código anterior, el código `return RedirectToPage();` debe ser un redireccionamiento para que el explorador realice una nueva solicitud y se actualice la identidad del usuario.</span><span class="sxs-lookup"><span data-stu-id="81487-188">In the preceding code, the code `return RedirectToPage();` needs to be a redirect so that the browser performs a new request and the identity for the user gets updated.</span></span>

<span data-ttu-id="81487-189">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) borra las notificaciones del usuario almacenadas en una cookie.</span><span class="sxs-lookup"><span data-stu-id="81487-189">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="81487-190">Post se especifica en *pages/Shared/_LoginPartial. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="81487-190">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-identity"></a><span data-ttu-id="81487-191">Identidad de prueba</span><span class="sxs-lookup"><span data-stu-id="81487-191">Test Identity</span></span>

<span data-ttu-id="81487-192">Las plantillas predeterminadas del proyecto web permiten el acceso anónimo a las páginas principales.</span><span class="sxs-lookup"><span data-stu-id="81487-192">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="81487-193">Para probar la identidad, [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)Agregue:</span><span class="sxs-lookup"><span data-stu-id="81487-193">To test Identity, add [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute):</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="81487-194">Si ha iniciado sesión, cierre la sesión. Ejecute la aplicación y seleccione el vínculo de **privacidad** .</span><span class="sxs-lookup"><span data-stu-id="81487-194">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="81487-195">Se le redirige a la página de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="81487-195">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="81487-196">Explorar identidad</span><span class="sxs-lookup"><span data-stu-id="81487-196">Explore Identity</span></span>

<span data-ttu-id="81487-197">Para explorar la identidad con más detalle:</span><span class="sxs-lookup"><span data-stu-id="81487-197">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="81487-198">Crear origen de la interfaz de usuario de identidad completa</span><span class="sxs-lookup"><span data-stu-id="81487-198">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="81487-199">Examine el origen de cada página y recorra el depurador.</span><span class="sxs-lookup"><span data-stu-id="81487-199">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a><span data-ttu-id="81487-200">Componentes de identidad</span><span class="sxs-lookup"><span data-stu-id="81487-200">Identity Components</span></span>

<span data-ttu-id="81487-201">Todos los paquetes de NuGet que dependen de la identidad se incluyen en la [ASP.net Core marco de trabajo compartido](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span><span class="sxs-lookup"><span data-stu-id="81487-201">All the Identity-dependent NuGet packages are included in the [ASP.NET Core shared framework](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span></span>

<span data-ttu-id="81487-202">El paquete principal de identidad es [Microsoft. AspNetCore. Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span><span class="sxs-lookup"><span data-stu-id="81487-202">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="81487-203">Este paquete contiene el conjunto principal de interfaces para ASP.NET Core identidad y está incluido en `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span><span class="sxs-lookup"><span data-stu-id="81487-203">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="81487-204">Migrar a ASP.NET Core identidad</span><span class="sxs-lookup"><span data-stu-id="81487-204">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="81487-205">Para obtener más información e instrucciones sobre cómo migrar el almacén de identidades existente, vea [migrar autenticación e identidad](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="81487-205">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="81487-206">Establecer la seguridad de la contraseña</span><span class="sxs-lookup"><span data-stu-id="81487-206">Setting password strength</span></span>

<span data-ttu-id="81487-207">Consulte [configuración](#pw) para obtener un ejemplo que establece los requisitos mínimos de contraseña.</span><span class="sxs-lookup"><span data-stu-id="81487-207">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="81487-208">AddDefaultIdentity y AddIdentity</span><span class="sxs-lookup"><span data-stu-id="81487-208">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="81487-209"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*>se presentó en ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="81487-209"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="81487-210">Llamar `AddDefaultIdentity` a es similar a llamar a lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="81487-210">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="81487-211">Vea [origen de AddDefaultIdentity](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="81487-211">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="prevent-publish-of-static-identity-assets"></a><span data-ttu-id="81487-212">Impedir la publicación de recursos de identidad estáticos</span><span class="sxs-lookup"><span data-stu-id="81487-212">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="81487-213">Para evitar que se publiquen recursos de identidad estáticos (hojas de estilos y archivos JavaScript para la interfaz de usuario de `ResolveStaticWebAssetsInputsDependsOn` identidad) `RemoveIdentityAssets` en la raíz Web, agregue la siguiente propiedad y destino al archivo de proyecto de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="81487-213">To prevent publishing static Identity assets (stylesheets and JavaScript files for Identity UI) to the web root, add the following `ResolveStaticWebAssetsInputsDependsOn` property and `RemoveIdentityAssets` target to the app's project file:</span></span>

```xml
<PropertyGroup>
  <ResolveStaticWebAssetsInputsDependsOn>RemoveIdentityAssets</ResolveStaticWebAssetsInputsDependsOn>
</PropertyGroup>

<Target Name="RemoveIdentityAssets">
  <ItemGroup>
    <StaticWebAsset Remove="@(StaticWebAsset)" Condition="%(SourceId) == 'Microsoft.AspNetCore.Identity.UI'" />
  </ItemGroup>
</Target>
```

## <a name="next-steps"></a><span data-ttu-id="81487-214">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="81487-214">Next Steps</span></span>

* <span data-ttu-id="81487-215">Consulte [este problema de github](https://github.com/dotnet/AspNetCore.Docs/issues/5131) para obtener información sobre cómo configurar la identidad mediante SQLite.</span><span class="sxs-lookup"><span data-stu-id="81487-215">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* [<span data-ttu-id="81487-216">Configuración de Identity</span><span class="sxs-lookup"><span data-stu-id="81487-216">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="81487-217">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="81487-217">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="81487-218">ASP.NET Core identidad es un sistema de pertenencia que agrega funcionalidad de inicio de sesión a ASP.NET Core aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="81487-218">ASP.NET Core Identity is a membership system that adds login functionality to ASP.NET Core apps.</span></span> <span data-ttu-id="81487-219">Los usuarios pueden crear una cuenta con la información de inicio de sesión almacenada en la identidad o pueden utilizar un proveedor de inicio de sesión externo.</span><span class="sxs-lookup"><span data-stu-id="81487-219">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="81487-220">Entre los proveedores de inicio de sesión externos admitidos se incluyen [Facebook, Google, cuenta de Microsoft y Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="81487-220">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="81487-221">La identidad puede configurarse mediante una base de datos de SQL Server para almacenar nombres de usuario, contraseñas y datos de perfil.</span><span class="sxs-lookup"><span data-stu-id="81487-221">Identity can be configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="81487-222">Como alternativa, se puede usar otro almacén persistente, por ejemplo, Azure Table Storage.</span><span class="sxs-lookup"><span data-stu-id="81487-222">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="81487-223">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([Cómo descargar)](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="81487-223">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="81487-224">En este tema, aprenderá a usar la identidad para registrarse, iniciar sesión y cerrar la sesión de un usuario.</span><span class="sxs-lookup"><span data-stu-id="81487-224">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="81487-225">Para obtener instrucciones más detalladas sobre la creación de aplicaciones que usan la identidad, consulte la sección pasos siguientes al final de este artículo.</span><span class="sxs-lookup"><span data-stu-id="81487-225">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<a name="adi"></a>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="81487-226">AddDefaultIdentity y AddIdentity</span><span class="sxs-lookup"><span data-stu-id="81487-226">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="81487-227"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*>se presentó en ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="81487-227"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="81487-228">Llamar `AddDefaultIdentity` a es similar a llamar a lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="81487-228">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="81487-229">Vea [origen de AddDefaultIdentity](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="81487-229">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="81487-230">Creación de una aplicación web con autenticación</span><span class="sxs-lookup"><span data-stu-id="81487-230">Create a Web app with authentication</span></span>

<span data-ttu-id="81487-231">Cree un proyecto de aplicación Web de ASP.NET Core con cuentas de usuario individuales.</span><span class="sxs-lookup"><span data-stu-id="81487-231">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="81487-232">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="81487-232">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="81487-233">Seleccione **archivo** > **nuevo** > **proyecto**.</span><span class="sxs-lookup"><span data-stu-id="81487-233">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="81487-234">Seleccione **Aplicación web de ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="81487-234">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="81487-235">Asigne al proyecto el nombre **WebApp1** para que tenga el mismo espacio de nombres que la descarga del proyecto.</span><span class="sxs-lookup"><span data-stu-id="81487-235">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="81487-236">Haga clic en **OK**.</span><span class="sxs-lookup"><span data-stu-id="81487-236">Click **OK**.</span></span>
* <span data-ttu-id="81487-237">Seleccione una **aplicación web**de ASP.net Core y, a continuación, seleccione **cambiar autenticación**.</span><span class="sxs-lookup"><span data-stu-id="81487-237">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="81487-238">Seleccione **cuentas de usuario individuales** y haga clic en **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="81487-238">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="81487-239">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="81487-239">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

<span data-ttu-id="81487-240">El proyecto generado proporciona [ASP.net Core identidad](xref:security/authentication/identity) como una [biblioteca de clases de Razor](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="81487-240">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="81487-241">La biblioteca de clases de Razor de identidad expone puntos de `Identity` conexión con el área.</span><span class="sxs-lookup"><span data-stu-id="81487-241">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="81487-242">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="81487-242">For example:</span></span>

* <span data-ttu-id="81487-243">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="81487-243">/Identity/Account/Login</span></span>
* <span data-ttu-id="81487-244">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="81487-244">/Identity/Account/Logout</span></span>
* <span data-ttu-id="81487-245">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="81487-245">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="81487-246">Aplicación de migraciones</span><span class="sxs-lookup"><span data-stu-id="81487-246">Apply migrations</span></span>

<span data-ttu-id="81487-247">Aplique las migraciones para inicializar la base de datos.</span><span class="sxs-lookup"><span data-stu-id="81487-247">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="81487-248">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="81487-248">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="81487-249">Ejecute el siguiente comando en la consola del administrador de paquetes (PMC):</span><span class="sxs-lookup"><span data-stu-id="81487-249">Run the following command in the Package Manager Console (PMC):</span></span>

```powershell
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="81487-250">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="81487-250">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="81487-251">Probar registro e inicio de sesión</span><span class="sxs-lookup"><span data-stu-id="81487-251">Test Register and Login</span></span>

<span data-ttu-id="81487-252">Ejecute la aplicación y registre un usuario.</span><span class="sxs-lookup"><span data-stu-id="81487-252">Run the app and register a user.</span></span> <span data-ttu-id="81487-253">En función del tamaño de la pantalla, es posible que tenga que seleccionar el botón de alternancia de navegación para ver los vínculos de **Inicio de sesión** y **registro** .</span><span class="sxs-lookup"><span data-stu-id="81487-253">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="81487-254">Configurar servicios de identidad</span><span class="sxs-lookup"><span data-stu-id="81487-254">Configure Identity services</span></span>

<span data-ttu-id="81487-255">Los servicios se agregan en `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="81487-255">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="81487-256">El patrón habitual consiste en llamar a todos los métodos `Add{Service}` y, luego, a todos los métodos `services.Configure{Service}`.</span><span class="sxs-lookup"><span data-stu-id="81487-256">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

<span data-ttu-id="81487-257">El código anterior configura la identidad con valores de opción predeterminados.</span><span class="sxs-lookup"><span data-stu-id="81487-257">The preceding code configures Identity with default option values.</span></span> <span data-ttu-id="81487-258">Los servicios se ponen a disposición de la aplicación a través de la [inserción de dependencias](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="81487-258">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="81487-259">La identidad se habilita llamando a [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="81487-259">Identity is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="81487-260">`UseAuthentication`agrega [middleware](xref:fundamentals/middleware/index) de autenticación a la canalización de solicitudes.</span><span class="sxs-lookup"><span data-stu-id="81487-260">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

<span data-ttu-id="81487-261">Para obtener más información, vea la [clase IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) y el inicio de la [aplicación](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="81487-261">For more information, see the [IdentityOptions Class](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="81487-262">Registro de scaffolding, Inicio de sesión y cierre de sesión</span><span class="sxs-lookup"><span data-stu-id="81487-262">Scaffold Register, Login, and LogOut</span></span>

<span data-ttu-id="81487-263">Siga la [identidad scaffolding en un proyecto Razor con](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instrucciones de autorización para generar el código que se muestra en esta sección.</span><span class="sxs-lookup"><span data-stu-id="81487-263">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="81487-264">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="81487-264">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="81487-265">Agregue los archivos de registro, Inicio de sesión y cierre de sesión.</span><span class="sxs-lookup"><span data-stu-id="81487-265">Add the Register, Login, and LogOut files.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="81487-266">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="81487-266">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="81487-267">Si ha creado el proyecto con el nombre **WebApp1**, ejecute los siguientes comandos.</span><span class="sxs-lookup"><span data-stu-id="81487-267">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="81487-268">De lo contrario, use el espacio de `ApplicationDbContext`nombres correcto para:</span><span class="sxs-lookup"><span data-stu-id="81487-268">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="81487-269">PowerShell usa punto y coma como separador de comandos.</span><span class="sxs-lookup"><span data-stu-id="81487-269">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="81487-270">Al usar PowerShell, use el carácter de escape en la lista de archivos o ponga la lista de archivos entre comillas dobles, como se muestra en el ejemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="81487-270">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="81487-271">Examinar registro</span><span class="sxs-lookup"><span data-stu-id="81487-271">Examine Register</span></span>

<span data-ttu-id="81487-272">Cuando un usuario hace clic en **Register** el vínculo de registro `RegisterModel.OnPostAsync` , se invoca la acción.</span><span class="sxs-lookup"><span data-stu-id="81487-272">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="81487-273">El usuario lo crea [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) en el `_userManager` objeto.</span><span class="sxs-lookup"><span data-stu-id="81487-273">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object.</span></span> <span data-ttu-id="81487-274">`_userManager`la inserción de dependencias proporciona:</span><span class="sxs-lookup"><span data-stu-id="81487-274">`_userManager` is provided by dependency injection):</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

<span data-ttu-id="81487-275">Si el usuario se creó correctamente, el usuario inicia sesión en la llamada a `_signInManager.SignInAsync`.</span><span class="sxs-lookup"><span data-stu-id="81487-275">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="81487-276">**Nota:** Consulte [confirmación](xref:security/authentication/accconfirm#prevent-login-at-registration) de la cuenta para conocer los pasos para evitar el inicio de sesión inmediato en el registro.</span><span class="sxs-lookup"><span data-stu-id="81487-276">**Note:** See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="81487-277">Registro</span><span class="sxs-lookup"><span data-stu-id="81487-277">Log in</span></span>

<span data-ttu-id="81487-278">El formulario de inicio de sesión se muestra cuando:</span><span class="sxs-lookup"><span data-stu-id="81487-278">The Login form is displayed when:</span></span>

* <span data-ttu-id="81487-279">Se selecciona el vínculo **iniciar sesión** .</span><span class="sxs-lookup"><span data-stu-id="81487-279">The **Log in** link is selected.</span></span>
* <span data-ttu-id="81487-280">Un usuario intenta obtener acceso a una página restringida para la que no está autorizado para acceder **o** cuando el sistema no la ha autenticado.</span><span class="sxs-lookup"><span data-stu-id="81487-280">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="81487-281">Cuando se envía el formulario de la página de inicio de `OnPostAsync` sesión, se llama a la acción.</span><span class="sxs-lookup"><span data-stu-id="81487-281">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="81487-282">`PasswordSignInAsync`se llama a en `_signInManager` el objeto (proporcionado por la inserción de dependencias).</span><span class="sxs-lookup"><span data-stu-id="81487-282">`PasswordSignInAsync` is called on the `_signInManager` object (provided by dependency injection).</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="81487-283">La clase `Controller` base expone una `User` propiedad a la que se puede tener acceso desde métodos de controlador.</span><span class="sxs-lookup"><span data-stu-id="81487-283">The base `Controller` class exposes a `User` property that you can access from controller methods.</span></span> <span data-ttu-id="81487-284">Por ejemplo, puede enumerar `User.Claims` y tomar decisiones de autorización.</span><span class="sxs-lookup"><span data-stu-id="81487-284">For instance, you can enumerate `User.Claims` and make authorization decisions.</span></span> <span data-ttu-id="81487-285">Para obtener más información, vea <xref:security/authorization/introduction>.</span><span class="sxs-lookup"><span data-stu-id="81487-285">For more information, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="81487-286">Cerrar la sesión</span><span class="sxs-lookup"><span data-stu-id="81487-286">Log out</span></span>

<span data-ttu-id="81487-287">El vínculo de **cierre de sesión** invoca `LogoutModel.OnPost` la acción.</span><span class="sxs-lookup"><span data-stu-id="81487-287">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

<span data-ttu-id="81487-288">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) borra las notificaciones del usuario almacenadas en una cookie.</span><span class="sxs-lookup"><span data-stu-id="81487-288">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="81487-289">Post se especifica en *pages/Shared/_LoginPartial. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="81487-289">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-identity"></a><span data-ttu-id="81487-290">Identidad de prueba</span><span class="sxs-lookup"><span data-stu-id="81487-290">Test Identity</span></span>

<span data-ttu-id="81487-291">Las plantillas predeterminadas del proyecto web permiten el acceso anónimo a las páginas principales.</span><span class="sxs-lookup"><span data-stu-id="81487-291">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="81487-292">Para probar la identidad, [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) agregue a la página de privacidad.</span><span class="sxs-lookup"><span data-stu-id="81487-292">To test Identity, add [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) to the Privacy page.</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="81487-293">Si ha iniciado sesión, cierre la sesión. Ejecute la aplicación y seleccione el vínculo de **privacidad** .</span><span class="sxs-lookup"><span data-stu-id="81487-293">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="81487-294">Se le redirige a la página de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="81487-294">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="81487-295">Explorar identidad</span><span class="sxs-lookup"><span data-stu-id="81487-295">Explore Identity</span></span>

<span data-ttu-id="81487-296">Para explorar la identidad con más detalle:</span><span class="sxs-lookup"><span data-stu-id="81487-296">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="81487-297">Crear origen de la interfaz de usuario de identidad completa</span><span class="sxs-lookup"><span data-stu-id="81487-297">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="81487-298">Examine el origen de cada página y recorra el depurador.</span><span class="sxs-lookup"><span data-stu-id="81487-298">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a><span data-ttu-id="81487-299">Componentes de identidad</span><span class="sxs-lookup"><span data-stu-id="81487-299">Identity Components</span></span>

<span data-ttu-id="81487-300">Todos los paquetes de NuGet que dependen de la identidad se incluyen en el [metapaquete Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="81487-300">All the Identity dependent NuGet packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="81487-301">El paquete principal de identidad es [Microsoft. AspNetCore. Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span><span class="sxs-lookup"><span data-stu-id="81487-301">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="81487-302">Este paquete contiene el conjunto principal de interfaces para ASP.NET Core identidad y está incluido en `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span><span class="sxs-lookup"><span data-stu-id="81487-302">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="81487-303">Migrar a ASP.NET Core identidad</span><span class="sxs-lookup"><span data-stu-id="81487-303">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="81487-304">Para obtener más información e instrucciones sobre cómo migrar el almacén de identidades existente, vea [migrar autenticación e identidad](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="81487-304">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="81487-305">Establecer la seguridad de la contraseña</span><span class="sxs-lookup"><span data-stu-id="81487-305">Setting password strength</span></span>

<span data-ttu-id="81487-306">Consulte [configuración](#pw) para obtener un ejemplo que establece los requisitos mínimos de contraseña.</span><span class="sxs-lookup"><span data-stu-id="81487-306">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="next-steps"></a><span data-ttu-id="81487-307">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="81487-307">Next Steps</span></span>

* <span data-ttu-id="81487-308">Consulte [este problema de github](https://github.com/dotnet/AspNetCore.Docs/issues/5131) para obtener información sobre cómo configurar la identidad mediante SQLite.</span><span class="sxs-lookup"><span data-stu-id="81487-308">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* [<span data-ttu-id="81487-309">Configuración de Identity</span><span class="sxs-lookup"><span data-stu-id="81487-309">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end
