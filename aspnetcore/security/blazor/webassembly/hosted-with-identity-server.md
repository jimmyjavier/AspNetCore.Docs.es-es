---
title: Protección de una Blazor aplicación hospedada en Webassembly ASP.net Core con Identity Server
author: guardrex
description: Para crear una nueva Blazor aplicación hospedada con autenticación desde dentro de Visual Studio que usa un back-end de [IdentityServer](https://identityserver.io/)
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/11/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/hosted-with-identity-server
ms.openlocfilehash: 2ab43ac5f4de398c57707de23a06a1650f6140cb
ms.sourcegitcommit: 1250c90c8d87c2513532be5683640b65bfdf9ddb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/12/2020
ms.locfileid: "83153635"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-identity-server"></a><span data-ttu-id="67873-103">Protección de una Blazor aplicación hospedada en Webassembly ASP.net Core con Identity Server</span><span class="sxs-lookup"><span data-stu-id="67873-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Identity Server</span></span>

<span data-ttu-id="67873-104">Por [Javier Calvarro Nelson](https://github.com/javiercn) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="67873-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="67873-105">Para crear una nueva Blazor aplicación hospedada en Visual Studio que use [IdentityServer](https://identityserver.io/) para autenticar a los usuarios y las llamadas a la API:</span><span class="sxs-lookup"><span data-stu-id="67873-105">To create a new Blazor hosted app in Visual Studio that uses [IdentityServer](https://identityserver.io/) to authenticate users and API calls:</span></span>

1. <span data-ttu-id="67873-106">Use Visual Studio para crear una nueva aplicación de \*\* Blazor webassembly\*\* .</span><span class="sxs-lookup"><span data-stu-id="67873-106">Use Visual Studio to create a new **Blazor WebAssembly** app.</span></span> <span data-ttu-id="67873-107">Para obtener más información, vea <xref:blazor/get-started>.</span><span class="sxs-lookup"><span data-stu-id="67873-107">For more information, see <xref:blazor/get-started>.</span></span>
1. <span data-ttu-id="67873-108">En el cuadro de diálogo **crear una nueva Blazor aplicación** , seleccione **cambiar** en la sección **autenticación** .</span><span class="sxs-lookup"><span data-stu-id="67873-108">In the **Create a new Blazor app** dialog, select **Change** in the **Authentication** section.</span></span>
1. <span data-ttu-id="67873-109">Seleccione **cuentas de usuario individuales** seguidos de **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="67873-109">Select **Individual User Accounts** followed by **OK**.</span></span>
1. <span data-ttu-id="67873-110">Active la casilla **ASP.net Core hospedado** en la sección **avanzadas** .</span><span class="sxs-lookup"><span data-stu-id="67873-110">Select the **ASP.NET Core hosted** checkbox in the **Advanced** section.</span></span>
1. <span data-ttu-id="67873-111">Seleccione el botón **Crear**.</span><span class="sxs-lookup"><span data-stu-id="67873-111">Select the **Create** button.</span></span>

<span data-ttu-id="67873-112">Para crear la aplicación en un shell de comandos, ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="67873-112">To create the app in a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

<span data-ttu-id="67873-113">Para especificar la ubicación de salida, que crea una carpeta de proyecto si no existe, incluya la opción output en el comando con una ruta de acceso (por ejemplo, `-o BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="67873-113">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="67873-114">El nombre de la carpeta también se convierte en parte del nombre del proyecto.</span><span class="sxs-lookup"><span data-stu-id="67873-114">The folder name also becomes part of the project's name.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="67873-115">Configuración de la aplicación de servidor</span><span class="sxs-lookup"><span data-stu-id="67873-115">Server app configuration</span></span>

<span data-ttu-id="67873-116">En las secciones siguientes se describen las adiciones al proyecto cuando se incluye compatibilidad con la autenticación.</span><span class="sxs-lookup"><span data-stu-id="67873-116">The following sections describe additions to the project when authentication support is included.</span></span>

### <a name="startup-class"></a><span data-ttu-id="67873-117">Clase Startup</span><span class="sxs-lookup"><span data-stu-id="67873-117">Startup class</span></span>

<span data-ttu-id="67873-118">La `Startup` clase tiene las siguientes adiciones:</span><span class="sxs-lookup"><span data-stu-id="67873-118">The `Startup` class has the following additions:</span></span>

* <span data-ttu-id="67873-119">En `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="67873-119">In `Startup.ConfigureServices`:</span></span>

  * Identity<span data-ttu-id="67873-120">:</span><span class="sxs-lookup"><span data-stu-id="67873-120">:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="67873-121">IdentityServer con un <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> método auxiliar adicional que configura algunas convenciones de ASP.net Core predeterminadas en la parte superior de IdentityServer:</span><span class="sxs-lookup"><span data-stu-id="67873-121">IdentityServer with an additional <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method that sets up some default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="67873-122">Autenticación con un <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> método auxiliar adicional que configura la aplicación para validar los tokens JWT generados por IdentityServer:</span><span class="sxs-lookup"><span data-stu-id="67873-122">Authentication with an additional <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="67873-123">En `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="67873-123">In `Startup.Configure`:</span></span>

  * <span data-ttu-id="67873-124">El middleware de autenticación responsable de validar las credenciales de solicitud y establecer el usuario en el contexto de la solicitud:</span><span class="sxs-lookup"><span data-stu-id="67873-124">The authentication middleware that is responsible for validating the request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="67873-125">El middleware IdentityServer que expone los puntos de conexión de Open ID Connect (OIDC):</span><span class="sxs-lookup"><span data-stu-id="67873-125">The IdentityServer middleware that exposes the Open ID Connect (OIDC) endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

  * <span data-ttu-id="67873-126">Middleware de autenticación y autorización:</span><span class="sxs-lookup"><span data-stu-id="67873-126">Authentication and Authorization Middleware:</span></span>

    ```csharp
    app.UseAuthentication();
    app.UseAuthorization();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="67873-127">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="67873-127">AddApiAuthorization</span></span>

<span data-ttu-id="67873-128">El <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> método auxiliar configura [IdentityServer](https://identityserver.io/) para escenarios ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="67873-128">The <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method configures [IdentityServer](https://identityserver.io/) for ASP.NET Core scenarios.</span></span> <span data-ttu-id="67873-129">IdentityServer es un marco de trabajo eficaz y extensible para controlar los problemas de seguridad de las aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="67873-129">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="67873-130">IdentityServer expone una complejidad innecesaria para los escenarios más comunes.</span><span class="sxs-lookup"><span data-stu-id="67873-130">IdentityServer exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="67873-131">Por lo tanto, se proporciona un conjunto de convenciones y opciones de configuración que consideramos un buen punto de partida.</span><span class="sxs-lookup"><span data-stu-id="67873-131">Consequently, a set of conventions and configuration options is provided that we consider a good starting point.</span></span> <span data-ttu-id="67873-132">Una vez que cambie la autenticación, toda la potencia de IdentityServer sigue estando disponible para personalizar la autenticación para adaptarse a los requisitos de una aplicación.</span><span class="sxs-lookup"><span data-stu-id="67873-132">Once your authentication needs change, the full power of IdentityServer is still available to customize authentication to suit an app's requirements.</span></span>

### <a name="addidentityserverjwt"></a><span data-ttu-id="67873-133">AddIdentityServerJwt</span><span class="sxs-lookup"><span data-stu-id="67873-133">AddIdentityServerJwt</span></span>

<span data-ttu-id="67873-134">El <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> método auxiliar configura un esquema de directiva para la aplicación como el controlador de autenticación predeterminado.</span><span class="sxs-lookup"><span data-stu-id="67873-134">The <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="67873-135">La Directiva está configurada para permitir Identity que controle todas las solicitudes enrutadas a cualquier subruta de acceso en el espacio de la Identity dirección URL `/Identity` .</span><span class="sxs-lookup"><span data-stu-id="67873-135">The policy is configured to allow Identity to handle all requests routed to any subpath in the Identity URL space `/Identity`.</span></span> <span data-ttu-id="67873-136"><xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler>Controla todas las demás solicitudes.</span><span class="sxs-lookup"><span data-stu-id="67873-136">The <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> handles all other requests.</span></span> <span data-ttu-id="67873-137">Además, este método:</span><span class="sxs-lookup"><span data-stu-id="67873-137">Additionally, this method:</span></span>

* <span data-ttu-id="67873-138">Registra un `{APPLICATION NAME}API` recurso de API con IdentityServer con un ámbito predeterminado de `{APPLICATION NAME}API` .</span><span class="sxs-lookup"><span data-stu-id="67873-138">Registers an `{APPLICATION NAME}API` API resource with IdentityServer with a default scope of `{APPLICATION NAME}API`.</span></span>
* <span data-ttu-id="67873-139">Configura el middleware del token de portador de JWT para validar los tokens emitidos por IdentityServer para la aplicación.</span><span class="sxs-lookup"><span data-stu-id="67873-139">Configures the JWT Bearer Token Middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="67873-140">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="67873-140">WeatherForecastController</span></span>

<span data-ttu-id="67873-141">En `WeatherForecastController` (*Controllers/WeatherForecastController. CS*), el [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atributo se aplica a la clase.</span><span class="sxs-lookup"><span data-stu-id="67873-141">In the `WeatherForecastController` (*Controllers/WeatherForecastController.cs*), the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is applied to the class.</span></span> <span data-ttu-id="67873-142">El atributo indica que el usuario debe estar autorizado en función de la directiva predeterminada para tener acceso al recurso.</span><span class="sxs-lookup"><span data-stu-id="67873-142">The attribute indicates that the user must be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="67873-143">La Directiva de autorización predeterminada está configurada para usar el esquema de autenticación predeterminado, que se configura por <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> el esquema de directivas mencionado anteriormente.</span><span class="sxs-lookup"><span data-stu-id="67873-143">The default authorization policy is configured to use the default authentication scheme, which is set up by <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> to the policy scheme that was mentioned earlier.</span></span> <span data-ttu-id="67873-144">El método auxiliar configura <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> como el controlador predeterminado para las solicitudes a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="67873-144">The helper method configures <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> as the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="67873-145">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="67873-145">ApplicationDbContext</span></span>

<span data-ttu-id="67873-146">En `ApplicationDbContext` (*Data/ApplicationDbContext. CS*), lo mismo <xref:Microsoft.EntityFrameworkCore.DbContext> se usa en Identity con la excepción de que se extiende <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> para incluir el esquema de IdentityServer.</span><span class="sxs-lookup"><span data-stu-id="67873-146">In the `ApplicationDbContext` (*Data/ApplicationDbContext.cs*), the same <xref:Microsoft.EntityFrameworkCore.DbContext> is used in Identity with the exception that it extends <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> to include the schema for IdentityServer.</span></span> <span data-ttu-id="67873-147">La clase <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> se deriva de la clase <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span><span class="sxs-lookup"><span data-stu-id="67873-147"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> is derived from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span></span>

<span data-ttu-id="67873-148">Para obtener el control total del esquema de la base de datos, herede de una de las Identity <xref:Microsoft.EntityFrameworkCore.DbContext> clases disponibles y configure el contexto para incluir el esquema llamando a Identity `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` en el `OnModelCreating` método.</span><span class="sxs-lookup"><span data-stu-id="67873-148">To gain full control of the database schema, inherit from one of the available Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in the `OnModelCreating` method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="67873-149">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="67873-149">OidcConfigurationController</span></span>

<span data-ttu-id="67873-150">En `OidcConfigurationController` (*Controllers/OidcConfigurationController. CS*), el punto de conexión del cliente se aprovisiona para servir los parámetros de OIDC.</span><span class="sxs-lookup"><span data-stu-id="67873-150">In the `OidcConfigurationController` (*Controllers/OidcConfigurationController.cs*), the client endpoint is provisioned to serve OIDC parameters.</span></span>

### <a name="app-settings-files"></a><span data-ttu-id="67873-151">Archivos de configuración de la aplicación</span><span class="sxs-lookup"><span data-stu-id="67873-151">App settings files</span></span>

<span data-ttu-id="67873-152">En el archivo de configuración de la aplicación (*appSettings. JSON*) en la raíz del proyecto, en la `IdentityServer` sección se describe la lista de clientes configurados.</span><span class="sxs-lookup"><span data-stu-id="67873-152">In the app settings file (*appsettings.json*) at the project root, the `IdentityServer` section describes the list of configured clients.</span></span> <span data-ttu-id="67873-153">En el ejemplo siguiente, hay un solo cliente.</span><span class="sxs-lookup"><span data-stu-id="67873-153">In the following example, there's a single client.</span></span> <span data-ttu-id="67873-154">El nombre de cliente se corresponde con el nombre de la aplicación y se asigna por Convención al parámetro de OAuth `ClientId` .</span><span class="sxs-lookup"><span data-stu-id="67873-154">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="67873-155">El perfil indica el tipo de aplicación que se está configurando.</span><span class="sxs-lookup"><span data-stu-id="67873-155">The profile indicates the app type being configured.</span></span> <span data-ttu-id="67873-156">El perfil se usa internamente para controlar las convenciones que simplifican el proceso de configuración del servidor.</span><span class="sxs-lookup"><span data-stu-id="67873-156">The profile is used internally to drive conventions that simplify the configuration process for the server.</span></span> <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

## <a name="client-app-configuration"></a><span data-ttu-id="67873-157">Configuración de la aplicación cliente</span><span class="sxs-lookup"><span data-stu-id="67873-157">Client app configuration</span></span>

### <a name="authentication-package"></a><span data-ttu-id="67873-158">Paquete de autenticación</span><span class="sxs-lookup"><span data-stu-id="67873-158">Authentication package</span></span>

<span data-ttu-id="67873-159">Cuando se crea una aplicación para usar cuentas de usuario individuales ( `Individual` ), la aplicación recibe automáticamente una referencia de paquete para el `Microsoft.AspNetCore.Components.WebAssembly.Authentication` paquete en el archivo de proyecto de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="67873-159">When an app is created to use Individual User Accounts (`Individual`), the app automatically receives a package reference for the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package in the app's project file.</span></span> <span data-ttu-id="67873-160">El paquete proporciona un conjunto de primitivas que ayudan a la aplicación a autenticar a los usuarios y obtener tokens para llamar a las API protegidas.</span><span class="sxs-lookup"><span data-stu-id="67873-160">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="67873-161">Si agrega la autenticación a una aplicación, agregue manualmente el paquete al archivo de proyecto de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="67873-161">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

<span data-ttu-id="67873-162">Reemplace `{VERSION}` en la referencia de paquete anterior por la versión del `Microsoft.AspNetCore.Blazor.Templates` paquete que se muestra en el <xref:blazor/get-started> artículo.</span><span class="sxs-lookup"><span data-stu-id="67873-162">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

### <a name="api-authorization-support"></a><span data-ttu-id="67873-163">Compatibilidad con la autorización de API</span><span class="sxs-lookup"><span data-stu-id="67873-163">API authorization support</span></span>

<span data-ttu-id="67873-164">La compatibilidad con la autenticación de usuarios está conectada en el contenedor de servicios mediante el método de extensión proporcionado en el `Microsoft.AspNetCore.Components.WebAssembly.Authentication` paquete.</span><span class="sxs-lookup"><span data-stu-id="67873-164">The support for authenticating users is plugged into the service container by the extension method provided inside the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package.</span></span> <span data-ttu-id="67873-165">Este método configura todos los servicios necesarios para que la aplicación interactúe con el sistema de autorización existente.</span><span class="sxs-lookup"><span data-stu-id="67873-165">This method sets up all the services needed for the app to interact with the existing authorization system.</span></span>

```csharp
builder.Services.AddApiAuthorization();
```

<span data-ttu-id="67873-166">De forma predeterminada, carga la configuración de la aplicación por Convención desde `_configuration/{client-id}` .</span><span class="sxs-lookup"><span data-stu-id="67873-166">By default, it loads the configuration for the app by convention from `_configuration/{client-id}`.</span></span> <span data-ttu-id="67873-167">Por Convención, el identificador de cliente se establece en el nombre de ensamblado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="67873-167">By convention, the client ID is set to the app's assembly name.</span></span> <span data-ttu-id="67873-168">Esta dirección URL se puede cambiar para que apunte a un punto de conexión independiente mediante una llamada a la sobrecarga con opciones.</span><span class="sxs-lookup"><span data-stu-id="67873-168">This URL can be changed to point to a separate endpoint by calling the overload with options.</span></span>

### <a name="imports-file"></a><span data-ttu-id="67873-169">Importar archivo</span><span class="sxs-lookup"><span data-stu-id="67873-169">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="67873-170">Página de índice</span><span class="sxs-lookup"><span data-stu-id="67873-170">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a><span data-ttu-id="67873-171">Componente de aplicación</span><span class="sxs-lookup"><span data-stu-id="67873-171">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="67873-172">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="67873-172">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="67873-173">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="67873-173">LoginDisplay component</span></span>

<span data-ttu-id="67873-174">El `LoginDisplay` componente (*Shared/LoginDisplay. Razor*) se representa en el `MainLayout` componente (*Shared/MainLayout. Razor*) y administra los comportamientos siguientes:</span><span class="sxs-lookup"><span data-stu-id="67873-174">The `LoginDisplay` component (*Shared/LoginDisplay.razor*) is rendered in the `MainLayout` component (*Shared/MainLayout.razor*) and manages the following behaviors:</span></span>

* <span data-ttu-id="67873-175">Para usuarios autenticados:</span><span class="sxs-lookup"><span data-stu-id="67873-175">For authenticated users:</span></span>
  * <span data-ttu-id="67873-176">Muestra el nombre de usuario actual.</span><span class="sxs-lookup"><span data-stu-id="67873-176">Displays the current user name.</span></span>
  * <span data-ttu-id="67873-177">Proporciona un vínculo a la página de Perfil de usuario en ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="67873-177">Offers a link to the user profile page in ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="67873-178">Ofrece un botón para cerrar la sesión de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="67873-178">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="67873-179">Para usuarios anónimos:</span><span class="sxs-lookup"><span data-stu-id="67873-179">For anonymous users:</span></span>
  * <span data-ttu-id="67873-180">Ofrece la opción de registro.</span><span class="sxs-lookup"><span data-stu-id="67873-180">Offers the option to register.</span></span>
  * <span data-ttu-id="67873-181">Ofrece la opción de iniciar sesión.</span><span class="sxs-lookup"><span data-stu-id="67873-181">Offers the option to log in.</span></span>

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

### <a name="authentication-component"></a><span data-ttu-id="67873-182">Componente de autenticación</span><span class="sxs-lookup"><span data-stu-id="67873-182">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="67873-183">Componente FetchData</span><span class="sxs-lookup"><span data-stu-id="67873-183">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="67873-184">Ejecución la aplicación</span><span class="sxs-lookup"><span data-stu-id="67873-184">Run the app</span></span>

<span data-ttu-id="67873-185">Ejecute la aplicación desde el proyecto de servidor.</span><span class="sxs-lookup"><span data-stu-id="67873-185">Run the app from the Server project.</span></span> <span data-ttu-id="67873-186">Al usar Visual Studio, seleccione el proyecto de servidor en **Explorador de soluciones** y seleccione el botón **Ejecutar** en la barra de herramientas o inicie la aplicación en el menú **depurar** .</span><span class="sxs-lookup"><span data-stu-id="67873-186">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="67873-187">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="67873-187">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="67873-188">Solicitudes de API Web no autenticadas o no autorizadas en una aplicación con un cliente predeterminado seguro</span><span class="sxs-lookup"><span data-stu-id="67873-188">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
