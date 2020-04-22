---
title: Proteja una Blazor aplicación hospedada de ASP.NET Core WebAssembly con Identity Server
author: guardrex
description: Para crear Blazor una nueva aplicación hospedada con autenticación desde Visual Studio que usa un back-end [de IdentityServer](https://identityserver.io/)
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/30/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-identity-server
ms.openlocfilehash: 4c51200159ced16132e15bb4a1f0915ca0cf5945
ms.sourcegitcommit: c9d1208e86160615b2d914cce74a839ae41297a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/22/2020
ms.locfileid: "81791616"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-identity-server"></a><span data-ttu-id="a4789-103">Proteja una Blazor aplicación hospedada de ASP.NET Core WebAssembly con Identity Server</span><span class="sxs-lookup"><span data-stu-id="a4789-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Identity Server</span></span>

<span data-ttu-id="a4789-104">Por [Javier Calvarro Nelson](https://github.com/javiercn) y Luke [Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="a4789-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="a4789-105">Para crear Blazor una nueva aplicación hospedada en Visual Studio que use [IdentityServer](https://identityserver.io/) para autenticar usuarios y llamadas a la API:</span><span class="sxs-lookup"><span data-stu-id="a4789-105">To create a new Blazor hosted app in Visual Studio that uses [IdentityServer](https://identityserver.io/) to authenticate users and API calls:</span></span>

1. <span data-ttu-id="a4789-106">Use Visual Studio para \*\* Blazor \*\* crear una nueva aplicación WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="a4789-106">Use Visual Studio to create a new **Blazor WebAssembly** app.</span></span> <span data-ttu-id="a4789-107">Para obtener más información, vea <xref:blazor/get-started>.</span><span class="sxs-lookup"><span data-stu-id="a4789-107">For more information, see <xref:blazor/get-started>.</span></span>
1. <span data-ttu-id="a4789-108">En el cuadro de diálogo **Crear una Blazor nueva aplicación,** seleccione **Cambiar** en la sección **Autenticación.**</span><span class="sxs-lookup"><span data-stu-id="a4789-108">In the **Create a new Blazor app** dialog, select **Change** in the **Authentication** section.</span></span>
1. <span data-ttu-id="a4789-109">Seleccione **Cuentas de usuario individuales seguidas** de **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="a4789-109">Select **Individual User Accounts** followed by **OK**.</span></span>
1. <span data-ttu-id="a4789-110">Seleccione la casilla de verificación **ASP.NET Core hosted** en la sección **Avanzadas.**</span><span class="sxs-lookup"><span data-stu-id="a4789-110">Select the **ASP.NET Core hosted** checkbox in the **Advanced** section.</span></span>
1. <span data-ttu-id="a4789-111">Seleccione el botón **Crear**.</span><span class="sxs-lookup"><span data-stu-id="a4789-111">Select the **Create** button.</span></span>

<span data-ttu-id="a4789-112">Para crear la aplicación en un shell de comandos, ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="a4789-112">To create the app in a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

<span data-ttu-id="a4789-113">Para especificar la ubicación de salida, que crea una carpeta de proyecto si no existe, `-o BlazorSample`incluya la opción de salida en el comando con una ruta de acceso (por ejemplo, ).</span><span class="sxs-lookup"><span data-stu-id="a4789-113">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="a4789-114">El nombre de la carpeta también pasa a formar parte del nombre del proyecto.</span><span class="sxs-lookup"><span data-stu-id="a4789-114">The folder name also becomes part of the project's name.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="a4789-115">Configuración de la aplicación de servidor</span><span class="sxs-lookup"><span data-stu-id="a4789-115">Server app configuration</span></span>

<span data-ttu-id="a4789-116">En las secciones siguientes se describen las adiciones al proyecto cuando se incluye la compatibilidad con la autenticación.</span><span class="sxs-lookup"><span data-stu-id="a4789-116">The following sections describe additions to the project when authentication support is included.</span></span>

### <a name="startup-class"></a><span data-ttu-id="a4789-117">Clase Startup</span><span class="sxs-lookup"><span data-stu-id="a4789-117">Startup class</span></span>

<span data-ttu-id="a4789-118">La `Startup` clase tiene las siguientes adiciones:</span><span class="sxs-lookup"><span data-stu-id="a4789-118">The `Startup` class has the following additions:</span></span>

* <span data-ttu-id="a4789-119">En `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="a4789-119">In `Startup.ConfigureServices`:</span></span>

  * <span data-ttu-id="a4789-120">Identidad:</span><span class="sxs-lookup"><span data-stu-id="a4789-120">Identity:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="a4789-121">IdentityServer con <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> un método auxiliar adicional que configura algunas convenciones de ASP.NET Core predeterminadas sobre IdentityServer:</span><span class="sxs-lookup"><span data-stu-id="a4789-121">IdentityServer with an additional <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method that sets up some default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="a4789-122">Autenticación con <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> un método auxiliar adicional que configura la aplicación para validar tokens JWT generados por IdentityServer:</span><span class="sxs-lookup"><span data-stu-id="a4789-122">Authentication with an additional <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="a4789-123">En `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="a4789-123">In `Startup.Configure`:</span></span>

  * <span data-ttu-id="a4789-124">El middleware de autenticación que es responsable de validar las credenciales de la solicitud y establecer el usuario en el contexto de la solicitud:</span><span class="sxs-lookup"><span data-stu-id="a4789-124">The authentication middleware that is responsible for validating the request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="a4789-125">El middleware IdentityServer que expone los extremos de Open ID Connect (OIDC):</span><span class="sxs-lookup"><span data-stu-id="a4789-125">The IdentityServer middleware that exposes the Open ID Connect (OIDC) endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="a4789-126">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="a4789-126">AddApiAuthorization</span></span>

<span data-ttu-id="a4789-127">El <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> método auxiliar configura [IdentityServer](https://identityserver.io/) para escenarios de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a4789-127">The <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method configures [IdentityServer](https://identityserver.io/) for ASP.NET Core scenarios.</span></span> <span data-ttu-id="a4789-128">IdentityServer es un marco eficaz y extensible para controlar los problemas de seguridad de las aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="a4789-128">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="a4789-129">IdentityServer expone complejidad innecesaria para los escenarios más comunes.</span><span class="sxs-lookup"><span data-stu-id="a4789-129">IdentityServer exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="a4789-130">Por lo tanto, se proporciona un conjunto de convenciones y opciones de configuración que consideramos un buen punto de partida.</span><span class="sxs-lookup"><span data-stu-id="a4789-130">Consequently, a set of conventions and configuration options is provided that we consider a good starting point.</span></span> <span data-ttu-id="a4789-131">Una vez que las necesidades de autenticación cambian, la potencia completa de IdentityServer todavía está disponible para personalizar la autenticación para adaptarse a los requisitos de una aplicación.</span><span class="sxs-lookup"><span data-stu-id="a4789-131">Once your authentication needs change, the full power of IdentityServer is still available to customize authentication to suit an app's requirements.</span></span>

### <a name="addidentityserverjwt"></a><span data-ttu-id="a4789-132">AddIdentityServerJwt</span><span class="sxs-lookup"><span data-stu-id="a4789-132">AddIdentityServerJwt</span></span>

<span data-ttu-id="a4789-133">El <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> método auxiliar configura un esquema de directiva según la aplicación como controlador de autenticación predeterminado.</span><span class="sxs-lookup"><span data-stu-id="a4789-133">The <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="a4789-134">La directiva está configurada para permitir que la identidad controle todas `/Identity`las solicitudes enrutadas a cualquier subruta en el espacio DE URL de identidad.</span><span class="sxs-lookup"><span data-stu-id="a4789-134">The policy is configured to allow Identity to handle all requests routed to any subpath in the Identity URL space `/Identity`.</span></span> <span data-ttu-id="a4789-135">El <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> controla todas las demás solicitudes.</span><span class="sxs-lookup"><span data-stu-id="a4789-135">The <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> handles all other requests.</span></span> <span data-ttu-id="a4789-136">Además, este método:</span><span class="sxs-lookup"><span data-stu-id="a4789-136">Additionally, this method:</span></span>

* <span data-ttu-id="a4789-137">Registra un `{APPLICATION NAME}API` recurso de API con IdentityServer con un ámbito predeterminado de `{APPLICATION NAME}API`.</span><span class="sxs-lookup"><span data-stu-id="a4789-137">Registers an `{APPLICATION NAME}API` API resource with IdentityServer with a default scope of `{APPLICATION NAME}API`.</span></span>
* <span data-ttu-id="a4789-138">Configura el Middleware de token de portador de JWT para validar los tokens emitidos por IdentityServer para la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a4789-138">Configures the JWT Bearer Token Middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="a4789-139">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="a4789-139">WeatherForecastController</span></span>

<span data-ttu-id="a4789-140">En `WeatherForecastController` el (*Controllers/WeatherForecastController.cs*), el [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atributo se aplica a la clase.</span><span class="sxs-lookup"><span data-stu-id="a4789-140">In the `WeatherForecastController` (*Controllers/WeatherForecastController.cs*), the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is applied to the class.</span></span> <span data-ttu-id="a4789-141">El atributo indica que el usuario debe estar autorizado en función de la directiva predeterminada para tener acceso al recurso.</span><span class="sxs-lookup"><span data-stu-id="a4789-141">The attribute indicates that the user must be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="a4789-142">La directiva de autorización predeterminada está configurada para utilizar <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> el esquema de autenticación predeterminado, que se configura mediante el esquema de directivas que se mencionó anteriormente.</span><span class="sxs-lookup"><span data-stu-id="a4789-142">The default authorization policy is configured to use the default authentication scheme, which is set up by <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> to the policy scheme that was mentioned earlier.</span></span> <span data-ttu-id="a4789-143">El método auxiliar <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> se configura como el controlador predeterminado para las solicitudes a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a4789-143">The helper method configures <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> as the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="a4789-144">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="a4789-144">ApplicationDbContext</span></span>

<span data-ttu-id="a4789-145">En `ApplicationDbContext` el (*Data/ApplicationDbContext.cs*), lo mismo <xref:Microsoft.EntityFrameworkCore.DbContext> se utiliza <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> en Identity con la excepción que se extiende para incluir el esquema para IdentityServer.</span><span class="sxs-lookup"><span data-stu-id="a4789-145">In the `ApplicationDbContext` (*Data/ApplicationDbContext.cs*), the same <xref:Microsoft.EntityFrameworkCore.DbContext> is used in Identity with the exception that it extends <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> to include the schema for IdentityServer.</span></span> <span data-ttu-id="a4789-146">La clase <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> se deriva de la clase <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span><span class="sxs-lookup"><span data-stu-id="a4789-146"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> is derived from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span></span>

<span data-ttu-id="a4789-147">Para obtener el control total del esquema de <xref:Microsoft.EntityFrameworkCore.DbContext> base de datos, herede de `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` una `OnModelCreating` de las clases de identidad disponibles y configure el contexto para incluir el esquema de identidad mediante una llamada en el método.</span><span class="sxs-lookup"><span data-stu-id="a4789-147">To gain full control of the database schema, inherit from one of the available Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in the `OnModelCreating` method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="a4789-148">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="a4789-148">OidcConfigurationController</span></span>

<span data-ttu-id="a4789-149">En `OidcConfigurationController` (*Controllers/OidcConfigurationController.cs*), el extremo de cliente se aprovisiona para servir parámetros OIDC.</span><span class="sxs-lookup"><span data-stu-id="a4789-149">In the `OidcConfigurationController` (*Controllers/OidcConfigurationController.cs*), the client endpoint is provisioned to serve OIDC parameters.</span></span>

### <a name="app-settings-files"></a><span data-ttu-id="a4789-150">Archivos de configuración de la aplicación</span><span class="sxs-lookup"><span data-stu-id="a4789-150">App settings files</span></span>

<span data-ttu-id="a4789-151">En el archivo de configuración de la aplicación ( `IdentityServer` *appsettings.json*) en la raíz del proyecto, en la sección se describe la lista de clientes configurados.</span><span class="sxs-lookup"><span data-stu-id="a4789-151">In the app settings file (*appsettings.json*) at the project root, the `IdentityServer` section describes the list of configured clients.</span></span> <span data-ttu-id="a4789-152">En el ejemplo siguiente, hay un único cliente.</span><span class="sxs-lookup"><span data-stu-id="a4789-152">In the following example, there's a single client.</span></span> <span data-ttu-id="a4789-153">El nombre del cliente corresponde al nombre de la `ClientId` aplicación y se asigna por convención al parámetro OAuth.</span><span class="sxs-lookup"><span data-stu-id="a4789-153">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="a4789-154">El perfil indica el tipo de aplicación que se está configurando.</span><span class="sxs-lookup"><span data-stu-id="a4789-154">The profile indicates the app type being configured.</span></span> <span data-ttu-id="a4789-155">El perfil se utiliza internamente para impulsar convenciones que simplifican el proceso de configuración del servidor.</span><span class="sxs-lookup"><span data-stu-id="a4789-155">The profile is used internally to drive conventions that simplify the configuration process for the server.</span></span> <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "BlazorApplicationWithAuthentication.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

<span data-ttu-id="a4789-156">En el archivo de configuración de la aplicación de entorno de desarrollo *(appsettings. Development.json*) en la `IdentityServer` raíz del proyecto, en la sección se describe la clave utilizada para firmar tokens.</span><span class="sxs-lookup"><span data-stu-id="a4789-156">In the Development environment app settings file (*appsettings.Development.json*) at the project root, the `IdentityServer` section describes the key used to sign tokens.</span></span> <!-- When deploying to production, a key needs to be provisioned and deployed alongside the app, as explained in the [Deploy to production](#deploy-to-production) section. -->

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="client-app-configuration"></a><span data-ttu-id="a4789-157">Configuración de la aplicación cliente</span><span class="sxs-lookup"><span data-stu-id="a4789-157">Client app configuration</span></span>

### <a name="authentication-package"></a><span data-ttu-id="a4789-158">Paquete de autenticación</span><span class="sxs-lookup"><span data-stu-id="a4789-158">Authentication package</span></span>

<span data-ttu-id="a4789-159">Cuando se crea una aplicación para`Individual`usar cuentas de usuario individuales ( ), la aplicación recibe automáticamente una referencia de paquete para el `Microsoft.AspNetCore.Components.WebAssembly.Authentication` paquete en el archivo de proyecto de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a4789-159">When an app is created to use Individual User Accounts (`Individual`), the app automatically receives a package reference for the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package in the app's project file.</span></span> <span data-ttu-id="a4789-160">El paquete proporciona un conjunto de primitivas que ayudan a la aplicación a autenticar a los usuarios y obtener tokens para llamar a las API protegidas.</span><span class="sxs-lookup"><span data-stu-id="a4789-160">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="a4789-161">Si agrega autenticación a una aplicación, agregue manualmente el paquete al archivo de proyecto de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="a4789-161">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

<span data-ttu-id="a4789-162">Reemplace `{VERSION}` en la referencia del paquete `Microsoft.AspNetCore.Blazor.Templates` anterior por <xref:blazor/get-started> la versión del paquete que se muestra en el artículo.</span><span class="sxs-lookup"><span data-stu-id="a4789-162">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

### <a name="api-authorization-support"></a><span data-ttu-id="a4789-163">Soporte de autorización de API</span><span class="sxs-lookup"><span data-stu-id="a4789-163">API authorization support</span></span>

<span data-ttu-id="a4789-164">La compatibilidad para autenticar usuarios se conecta al contenedor de `Microsoft.AspNetCore.Components.WebAssembly.Authentication` servicios mediante el método de extensión proporcionado dentro del paquete.</span><span class="sxs-lookup"><span data-stu-id="a4789-164">The support for authenticating users is plugged into the service container by the extension method provided inside the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package.</span></span> <span data-ttu-id="a4789-165">Este método configura todos los servicios necesarios para que la aplicación interactúe con el sistema de autorización existente.</span><span class="sxs-lookup"><span data-stu-id="a4789-165">This method sets up all the services needed for the app to interact with the existing authorization system.</span></span>

```csharp
builder.Services.AddApiAuthorization();
```

<span data-ttu-id="a4789-166">De forma predeterminada, carga la configuración `_configuration/{client-id}`de la aplicación por convención desde .</span><span class="sxs-lookup"><span data-stu-id="a4789-166">By default, it loads the configuration for the app by convention from `_configuration/{client-id}`.</span></span> <span data-ttu-id="a4789-167">Por convención, el identificador de cliente se establece en el nombre del ensamblado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a4789-167">By convention, the client ID is set to the app's assembly name.</span></span> <span data-ttu-id="a4789-168">Esta dirección URL se puede cambiar para que apunte a un punto de conexión independiente llamando a la sobrecarga con opciones.</span><span class="sxs-lookup"><span data-stu-id="a4789-168">This URL can be changed to point to a separate endpoint by calling the overload with options.</span></span>

### <a name="imports-file"></a><span data-ttu-id="a4789-169">Archivo de importaciones</span><span class="sxs-lookup"><span data-stu-id="a4789-169">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="a4789-170">Página de índice</span><span class="sxs-lookup"><span data-stu-id="a4789-170">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a><span data-ttu-id="a4789-171">Componente de la aplicación</span><span class="sxs-lookup"><span data-stu-id="a4789-171">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="a4789-172">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="a4789-172">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="a4789-173">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="a4789-173">LoginDisplay component</span></span>

<span data-ttu-id="a4789-174">El `LoginDisplay` componente (*Shared/LoginDisplay.razor*) `MainLayout` se representa en el componente (*Shared/MainLayout.razor*) y administra los siguientes comportamientos:</span><span class="sxs-lookup"><span data-stu-id="a4789-174">The `LoginDisplay` component (*Shared/LoginDisplay.razor*) is rendered in the `MainLayout` component (*Shared/MainLayout.razor*) and manages the following behaviors:</span></span>

* <span data-ttu-id="a4789-175">Para usuarios autenticados:</span><span class="sxs-lookup"><span data-stu-id="a4789-175">For authenticated users:</span></span>
  * <span data-ttu-id="a4789-176">Muestra el nombre de usuario actual.</span><span class="sxs-lookup"><span data-stu-id="a4789-176">Displays the current user name.</span></span>
  * <span data-ttu-id="a4789-177">Ofrece un enlace a la página de perfil de usuario en ASP.NET Identidad principal.</span><span class="sxs-lookup"><span data-stu-id="a4789-177">Offers a link to the user profile page in ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="a4789-178">Ofrece un botón para cerrar sesión en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a4789-178">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="a4789-179">Para usuarios anónimos:</span><span class="sxs-lookup"><span data-stu-id="a4789-179">For anonymous users:</span></span>
  * <span data-ttu-id="a4789-180">Ofrece la opción de registrarse.</span><span class="sxs-lookup"><span data-stu-id="a4789-180">Offers the option to register.</span></span>
  * <span data-ttu-id="a4789-181">Ofrece la opción de iniciar sesión.</span><span class="sxs-lookup"><span data-stu-id="a4789-181">Offers the option to log in.</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        <a href="authentication/profile">Hello, @context.User.Identity.Name!</a>
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/register">Register</a>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code {
    private async Task BeginSignOut(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```

### <a name="authentication-component"></a><span data-ttu-id="a4789-182">Componente de autenticación</span><span class="sxs-lookup"><span data-stu-id="a4789-182">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="a4789-183">Componente FetchData</span><span class="sxs-lookup"><span data-stu-id="a4789-183">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="a4789-184">Ejecución la aplicación</span><span class="sxs-lookup"><span data-stu-id="a4789-184">Run the app</span></span>

<span data-ttu-id="a4789-185">Ejecute la aplicación desde el proyecto Servidor.</span><span class="sxs-lookup"><span data-stu-id="a4789-185">Run the app from the Server project.</span></span> <span data-ttu-id="a4789-186">Al usar Visual Studio, seleccione el proyecto de servidor en el **Explorador** de soluciones y seleccione el botón **Ejecutar** en la barra de herramientas o inicie la aplicación desde el menú **Depurar.**</span><span class="sxs-lookup"><span data-stu-id="a4789-186">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="a4789-187">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="a4789-187">Additional resources</span></span>

* [<span data-ttu-id="a4789-188">Solicitar tokens de acceso adicionales</span><span class="sxs-lookup"><span data-stu-id="a4789-188">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
