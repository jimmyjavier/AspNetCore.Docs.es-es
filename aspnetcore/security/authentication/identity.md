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
# <a name="introduction-to-identity-on-aspnet-core"></a>Introducción a la identidad en ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

Identidad de ASP.NET Core:

* Es una API que admite la funcionalidad de inicio de sesión de la interfaz de usuario.
* Administra usuarios, contraseñas, datos de perfil, roles, notificaciones, tokens, confirmación de correo electrónico, etc.

Los usuarios pueden crear una cuenta con la información de inicio de sesión almacenada en la identidad o pueden utilizar un proveedor de inicio de sesión externo. Entre los proveedores de inicio de sesión externos admitidos se incluyen [Facebook, Google, cuenta de Microsoft y Twitter](xref:security/authentication/social/index).

El [código fuente de identidad](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) está disponible en github. La [identidad scaffolding](xref:security/authentication/scaffold-identity) y ver los archivos generados para revisar la interacción de la plantilla con la identidad.

La identidad se configura normalmente con una base de datos SQL Server para almacenar nombres de usuario, contraseñas y datos de perfil. Como alternativa, se puede usar otro almacén persistente, por ejemplo, Azure Table Storage.

En este tema, aprenderá a usar la identidad para registrarse, iniciar sesión y cerrar la sesión de un usuario. Nota: las plantillas tratan el nombre de usuario y el correo electrónico como los mismos para los usuarios. Para obtener instrucciones más detalladas sobre la creación de aplicaciones que usan la identidad, consulte la sección pasos siguientes al final de este artículo.

La [plataforma de identidad de Microsoft](/azure/active-directory/develop/) es:

* Evolución de la plataforma de desarrollo de Azure Active Directory (Azure AD).
* No relacionado con ASP.NET Core identidad.

[!INCLUDE[](~/includes/IdentityServer4.md)]

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([Cómo descargar)](xref:index#how-to-download-a-sample)).

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a>Creación de una aplicación web con autenticación

Cree un proyecto de aplicación Web de ASP.NET Core con cuentas de usuario individuales.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Seleccione **archivo** > **nuevo** > **proyecto**.
* Seleccione **Aplicación web de ASP.NET Core**. Asigne al proyecto el nombre **WebApp1** para que tenga el mismo espacio de nombres que la descarga del proyecto. Haga clic en **OK**.
* Seleccione una **aplicación web**de ASP.net Core y, a continuación, seleccione **cambiar autenticación**.
* Seleccione **cuentas de usuario individuales** y haga clic en **Aceptar**.

# <a name="net-core-cli"></a>[CLI de .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

El comando anterior crea una aplicación Web de Razor con SQLite. Para crear la aplicación web con LocalDB, ejecute el siguiente comando:

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

El proyecto generado proporciona [ASP.net Core identidad](xref:security/authentication/identity) como una [biblioteca de clases de Razor](xref:razor-pages/ui-class). La biblioteca de clases de Razor de identidad expone puntos de `Identity` conexión con el área. Por ejemplo:

* /Identity/Account/Login
* /Identity/Account/Logout
* /Identity/Account/Manage

### <a name="apply-migrations"></a>Aplicación de migraciones

Aplique las migraciones para inicializar la base de datos.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Ejecute el siguiente comando en la consola del administrador de paquetes (PMC):

`PM> Update-Database`

# <a name="net-core-cli"></a>[CLI de .NET Core](#tab/netcore-cli)

No es necesario realizar migraciones en este paso al usar SQLite. Para LocalDB, ejecute el siguiente comando:

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a>Probar registro e inicio de sesión

Ejecute la aplicación y registre un usuario. En función del tamaño de la pantalla, es posible que tenga que seleccionar el botón de alternancia de navegación para ver los vínculos de **Inicio de sesión** y **registro** .

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a>Configurar servicios de identidad

Los servicios se agregan en `ConfigureServices`. El patrón habitual consiste en llamar a todos los métodos `Add{Service}` y, luego, a todos los métodos `services.Configure{Service}`.

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=10-99)]

El código resaltado anterior configura la identidad con valores de opción predeterminados. Los servicios se ponen a disposición de la aplicación a través de la [inserción de dependencias](xref:fundamentals/dependency-injection).

La identidad se habilita mediante <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>una llamada a. `UseAuthentication`agrega [middleware](xref:fundamentals/middleware/index) de autenticación a la canalización de solicitudes.

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

La aplicación generada por la plantilla no utiliza la [autorización](xref:security/authorization/secure-data). `app.UseAuthorization`se incluye para asegurarse de que se agrega en el orden correcto para que la aplicación agregue autorización. `UseRouting`se `UseAuthentication`debe `UseAuthorization`llamar a `UseEndpoints` ,, y en el orden mostrado en el código anterior.

Para obtener más información `IdentityOptions` sobre `Startup`y, <xref:Microsoft.AspNetCore.Identity.IdentityOptions> vea e inicio de la [aplicación](xref:fundamentals/startup).

## <a name="scaffold-register-login-and-logout"></a>Registro de scaffolding, Inicio de sesión y cierre de sesión

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Agregue los archivos de registro, Inicio de sesión y cierre de sesión. Siga la [identidad scaffolding en un proyecto Razor con](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instrucciones de autorización para generar el código que se muestra en esta sección.

# <a name="net-core-cli"></a>[CLI de .NET Core](#tab/netcore-cli)

Si ha creado el proyecto con el nombre **WebApp1**, ejecute los siguientes comandos. De lo contrario, use el espacio de `ApplicationDbContext`nombres correcto para:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

PowerShell usa punto y coma como separador de comandos. Al usar PowerShell, use el carácter de escape en la lista de archivos o ponga la lista de archivos entre comillas dobles, como se muestra en el ejemplo anterior.

Para obtener más información sobre la identidad de scaffolding, vea [scaffolding Identity into a Razor Project with Authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).

---

### <a name="examine-register"></a>Examinar registro

Cuando un usuario hace clic en **Register** el vínculo de registro `RegisterModel.OnPostAsync` , se invoca la acción. El usuario lo crea [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) en el `_userManager` objeto. `_userManager`la inserción de dependencias proporciona:

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

Si el usuario se creó correctamente, el usuario inicia sesión en la llamada a `_signInManager.SignInAsync`.

Consulte [confirmación](xref:security/authentication/accconfirm#prevent-login-at-registration) de la cuenta para conocer los pasos para evitar el inicio de sesión inmediato en el registro.

### <a name="log-in"></a>Registro

El formulario de inicio de sesión se muestra cuando:

* Se selecciona el vínculo **iniciar sesión** .
* Un usuario intenta obtener acceso a una página restringida para la que no está autorizado para acceder **o** cuando el sistema no la ha autenticado.

Cuando se envía el formulario de la página de inicio de `OnPostAsync` sesión, se llama a la acción. `PasswordSignInAsync`se llama a en `_signInManager` el objeto (proporcionado por la inserción de dependencias).

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

La clase `Controller` base expone una `User` propiedad a la que se puede tener acceso desde métodos de controlador. Por ejemplo, puede enumerar `User.Claims` y tomar decisiones de autorización. Para obtener más información, vea <xref:security/authorization/introduction>.

### <a name="log-out"></a>Cerrar la sesión

El vínculo de **cierre de sesión** invoca `LogoutModel.OnPost` la acción. 

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Logout.cshtml.cs?highlight=36)]

En el código anterior, el código `return RedirectToPage();` debe ser un redireccionamiento para que el explorador realice una nueva solicitud y se actualice la identidad del usuario.

[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) borra las notificaciones del usuario almacenadas en una cookie.

Post se especifica en *pages/Shared/_LoginPartial. cshtml*:

[!code-csharp[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-identity"></a>Identidad de prueba

Las plantillas predeterminadas del proyecto web permiten el acceso anónimo a las páginas principales. Para probar la identidad, [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)Agregue:

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

Si ha iniciado sesión, cierre la sesión. Ejecute la aplicación y seleccione el vínculo de **privacidad** . Se le redirige a la página de inicio de sesión.

### <a name="explore-identity"></a>Explorar identidad

Para explorar la identidad con más detalle:

* [Crear origen de la interfaz de usuario de identidad completa](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* Examine el origen de cada página y recorra el depurador.

## <a name="identity-components"></a>Componentes de identidad

Todos los paquetes de NuGet que dependen de la identidad se incluyen en la [ASP.net Core marco de trabajo compartido](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).

El paquete principal de identidad es [Microsoft. AspNetCore. Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/). Este paquete contiene el conjunto principal de interfaces para ASP.NET Core identidad y está incluido en `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.

## <a name="migrating-to-aspnet-core-identity"></a>Migrar a ASP.NET Core identidad

Para obtener más información e instrucciones sobre cómo migrar el almacén de identidades existente, vea [migrar autenticación e identidad](xref:migration/identity).

## <a name="setting-password-strength"></a>Establecer la seguridad de la contraseña

Consulte [configuración](#pw) para obtener un ejemplo que establece los requisitos mínimos de contraseña.

## <a name="adddefaultidentity-and-addidentity"></a>AddDefaultIdentity y AddIdentity

<xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*>se presentó en ASP.NET Core 2,1. Llamar `AddDefaultIdentity` a es similar a llamar a lo siguiente:

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

Vea [origen de AddDefaultIdentity](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) para obtener más información.

## <a name="prevent-publish-of-static-identity-assets"></a>Impedir la publicación de recursos de identidad estáticos

Para evitar que se publiquen recursos de identidad estáticos (hojas de estilos y archivos JavaScript para la interfaz de usuario de `ResolveStaticWebAssetsInputsDependsOn` identidad) `RemoveIdentityAssets` en la raíz Web, agregue la siguiente propiedad y destino al archivo de proyecto de la aplicación:

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

## <a name="next-steps"></a>Pasos siguientes

* Consulte [este problema de github](https://github.com/dotnet/AspNetCore.Docs/issues/5131) para obtener información sobre cómo configurar la identidad mediante SQLite.
* [Configuración de Identity](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core identidad es un sistema de pertenencia que agrega funcionalidad de inicio de sesión a ASP.NET Core aplicaciones. Los usuarios pueden crear una cuenta con la información de inicio de sesión almacenada en la identidad o pueden utilizar un proveedor de inicio de sesión externo. Entre los proveedores de inicio de sesión externos admitidos se incluyen [Facebook, Google, cuenta de Microsoft y Twitter](xref:security/authentication/social/index).

La identidad puede configurarse mediante una base de datos de SQL Server para almacenar nombres de usuario, contraseñas y datos de perfil. Como alternativa, se puede usar otro almacén persistente, por ejemplo, Azure Table Storage.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([Cómo descargar)](xref:index#how-to-download-a-sample)).

En este tema, aprenderá a usar la identidad para registrarse, iniciar sesión y cerrar la sesión de un usuario. Para obtener instrucciones más detalladas sobre la creación de aplicaciones que usan la identidad, consulte la sección pasos siguientes al final de este artículo.

<a name="adi"></a>

## <a name="adddefaultidentity-and-addidentity"></a>AddDefaultIdentity y AddIdentity

<xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*>se presentó en ASP.NET Core 2,1. Llamar `AddDefaultIdentity` a es similar a llamar a lo siguiente:

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

Vea [origen de AddDefaultIdentity](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) para obtener más información.

## <a name="create-a-web-app-with-authentication"></a>Creación de una aplicación web con autenticación

Cree un proyecto de aplicación Web de ASP.NET Core con cuentas de usuario individuales.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Seleccione **archivo** > **nuevo** > **proyecto**.
* Seleccione **Aplicación web de ASP.NET Core**. Asigne al proyecto el nombre **WebApp1** para que tenga el mismo espacio de nombres que la descarga del proyecto. Haga clic en **OK**.
* Seleccione una **aplicación web**de ASP.net Core y, a continuación, seleccione **cambiar autenticación**.
* Seleccione **cuentas de usuario individuales** y haga clic en **Aceptar**.

# <a name="net-core-cli"></a>[CLI de .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

El proyecto generado proporciona [ASP.net Core identidad](xref:security/authentication/identity) como una [biblioteca de clases de Razor](xref:razor-pages/ui-class). La biblioteca de clases de Razor de identidad expone puntos de `Identity` conexión con el área. Por ejemplo:

* /Identity/Account/Login
* /Identity/Account/Logout
* /Identity/Account/Manage

### <a name="apply-migrations"></a>Aplicación de migraciones

Aplique las migraciones para inicializar la base de datos.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Ejecute el siguiente comando en la consola del administrador de paquetes (PMC):

```powershell
Update-Database
```

# <a name="net-core-cli"></a>[CLI de .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a>Probar registro e inicio de sesión

Ejecute la aplicación y registre un usuario. En función del tamaño de la pantalla, es posible que tenga que seleccionar el botón de alternancia de navegación para ver los vínculos de **Inicio de sesión** y **registro** .

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a>Configurar servicios de identidad

Los servicios se agregan en `ConfigureServices`. El patrón habitual consiste en llamar a todos los métodos `Add{Service}` y, luego, a todos los métodos `services.Configure{Service}`.

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

El código anterior configura la identidad con valores de opción predeterminados. Los servicios se ponen a disposición de la aplicación a través de la [inserción de dependencias](xref:fundamentals/dependency-injection).

La identidad se habilita llamando a [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_). `UseAuthentication`agrega [middleware](xref:fundamentals/middleware/index) de autenticación a la canalización de solicitudes.

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

Para obtener más información, vea la [clase IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) y el inicio de la [aplicación](xref:fundamentals/startup).

## <a name="scaffold-register-login-and-logout"></a>Registro de scaffolding, Inicio de sesión y cierre de sesión

Siga la [identidad scaffolding en un proyecto Razor con](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instrucciones de autorización para generar el código que se muestra en esta sección.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Agregue los archivos de registro, Inicio de sesión y cierre de sesión.

# <a name="net-core-cli"></a>[CLI de .NET Core](#tab/netcore-cli)

Si ha creado el proyecto con el nombre **WebApp1**, ejecute los siguientes comandos. De lo contrario, use el espacio de `ApplicationDbContext`nombres correcto para:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

PowerShell usa punto y coma como separador de comandos. Al usar PowerShell, use el carácter de escape en la lista de archivos o ponga la lista de archivos entre comillas dobles, como se muestra en el ejemplo anterior.

---

### <a name="examine-register"></a>Examinar registro

Cuando un usuario hace clic en **Register** el vínculo de registro `RegisterModel.OnPostAsync` , se invoca la acción. El usuario lo crea [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) en el `_userManager` objeto. `_userManager`la inserción de dependencias proporciona:

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

Si el usuario se creó correctamente, el usuario inicia sesión en la llamada a `_signInManager.SignInAsync`.

**Nota:** Consulte [confirmación](xref:security/authentication/accconfirm#prevent-login-at-registration) de la cuenta para conocer los pasos para evitar el inicio de sesión inmediato en el registro.

### <a name="log-in"></a>Registro

El formulario de inicio de sesión se muestra cuando:

* Se selecciona el vínculo **iniciar sesión** .
* Un usuario intenta obtener acceso a una página restringida para la que no está autorizado para acceder **o** cuando el sistema no la ha autenticado.

Cuando se envía el formulario de la página de inicio de `OnPostAsync` sesión, se llama a la acción. `PasswordSignInAsync`se llama a en `_signInManager` el objeto (proporcionado por la inserción de dependencias).

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

La clase `Controller` base expone una `User` propiedad a la que se puede tener acceso desde métodos de controlador. Por ejemplo, puede enumerar `User.Claims` y tomar decisiones de autorización. Para obtener más información, vea <xref:security/authorization/introduction>.

### <a name="log-out"></a>Cerrar la sesión

El vínculo de **cierre de sesión** invoca `LogoutModel.OnPost` la acción. 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) borra las notificaciones del usuario almacenadas en una cookie.

Post se especifica en *pages/Shared/_LoginPartial. cshtml*:

[!code-csharp[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-identity"></a>Identidad de prueba

Las plantillas predeterminadas del proyecto web permiten el acceso anónimo a las páginas principales. Para probar la identidad, [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) agregue a la página de privacidad.

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

Si ha iniciado sesión, cierre la sesión. Ejecute la aplicación y seleccione el vínculo de **privacidad** . Se le redirige a la página de inicio de sesión.

### <a name="explore-identity"></a>Explorar identidad

Para explorar la identidad con más detalle:

* [Crear origen de la interfaz de usuario de identidad completa](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* Examine el origen de cada página y recorra el depurador.

## <a name="identity-components"></a>Componentes de identidad

Todos los paquetes de NuGet que dependen de la identidad se incluyen en el [metapaquete Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).

El paquete principal de identidad es [Microsoft. AspNetCore. Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/). Este paquete contiene el conjunto principal de interfaces para ASP.NET Core identidad y está incluido en `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.

## <a name="migrating-to-aspnet-core-identity"></a>Migrar a ASP.NET Core identidad

Para obtener más información e instrucciones sobre cómo migrar el almacén de identidades existente, vea [migrar autenticación e identidad](xref:migration/identity).

## <a name="setting-password-strength"></a>Establecer la seguridad de la contraseña

Consulte [configuración](#pw) para obtener un ejemplo que establece los requisitos mínimos de contraseña.

## <a name="next-steps"></a>Pasos siguientes

* Consulte [este problema de github](https://github.com/dotnet/AspNetCore.Docs/issues/5131) para obtener información sobre cómo configurar la identidad mediante SQLite.
* [Configuración de Identity](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end
