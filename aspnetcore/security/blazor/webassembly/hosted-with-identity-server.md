---
title: Protección de una Blazor aplicación hospedada en webassembly ASP.net Core con Identity Server
author: guardrex
description: Para crear una nueva Blazor aplicación hospedada con autenticación desde dentro de Visual Studio que usa un back-end de [IdentityServer](https://identityserver.io/)
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/22/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-identity-server
ms.openlocfilehash: f8de07e2e21ca19b5c4e95839e7b7e621c335ad0
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/24/2020
ms.locfileid: "82110956"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-identity-server"></a><span data-ttu-id="2732f-103">Protección de una Blazor aplicación hospedada en webassembly ASP.net Core con Identity Server</span><span class="sxs-lookup"><span data-stu-id="2732f-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Identity Server</span></span>

<span data-ttu-id="2732f-104">Por [Javier Calvarro Nelson](https://github.com/javiercn) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="2732f-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

> [!NOTE]
> <span data-ttu-id="2732f-105">Las instrucciones de este artículo se aplican a ASP.NET Core 3,2 Preview 4.</span><span class="sxs-lookup"><span data-stu-id="2732f-105">The guidance in this article applies to ASP.NET Core 3.2 Preview 4.</span></span> <span data-ttu-id="2732f-106">Este tema se actualizará a la versión preliminar 5 del viernes, 24 de abril.</span><span class="sxs-lookup"><span data-stu-id="2732f-106">This topic will be updated to cover Preview 5 on Friday, April 24.</span></span>

<span data-ttu-id="2732f-107">Para crear una nueva Blazor aplicación hospedada en Visual Studio que use [IdentityServer](https://identityserver.io/) para autenticar a los usuarios y las llamadas a la API:</span><span class="sxs-lookup"><span data-stu-id="2732f-107">To create a new Blazor hosted app in Visual Studio that uses [IdentityServer](https://identityserver.io/) to authenticate users and API calls:</span></span>

1. <span data-ttu-id="2732f-108">Use Visual Studio para crear una nueva \*\* Blazor aplicación de webassembly\*\* .</span><span class="sxs-lookup"><span data-stu-id="2732f-108">Use Visual Studio to create a new **Blazor WebAssembly** app.</span></span> <span data-ttu-id="2732f-109">Para obtener más información, vea <xref:blazor/get-started>.</span><span class="sxs-lookup"><span data-stu-id="2732f-109">For more information, see <xref:blazor/get-started>.</span></span>
1. <span data-ttu-id="2732f-110">En el cuadro de diálogo **crear una nueva Blazor aplicación** , seleccione **cambiar** en la sección **autenticación** .</span><span class="sxs-lookup"><span data-stu-id="2732f-110">In the **Create a new Blazor app** dialog, select **Change** in the **Authentication** section.</span></span>
1. <span data-ttu-id="2732f-111">Seleccione **cuentas de usuario individuales** seguidos de **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="2732f-111">Select **Individual User Accounts** followed by **OK**.</span></span>
1. <span data-ttu-id="2732f-112">Active la casilla **ASP.net Core hospedado** en la sección **avanzadas** .</span><span class="sxs-lookup"><span data-stu-id="2732f-112">Select the **ASP.NET Core hosted** checkbox in the **Advanced** section.</span></span>
1. <span data-ttu-id="2732f-113">Seleccione el botón **Crear**.</span><span class="sxs-lookup"><span data-stu-id="2732f-113">Select the **Create** button.</span></span>

<span data-ttu-id="2732f-114">Para crear la aplicación en un shell de comandos, ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="2732f-114">To create the app in a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

<span data-ttu-id="2732f-115">Para especificar la ubicación de salida, que crea una carpeta de proyecto si no existe, incluya la opción output en el comando con una ruta de acceso ( `-o BlazorSample`por ejemplo,).</span><span class="sxs-lookup"><span data-stu-id="2732f-115">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="2732f-116">El nombre de la carpeta también se convierte en parte del nombre del proyecto.</span><span class="sxs-lookup"><span data-stu-id="2732f-116">The folder name also becomes part of the project's name.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="2732f-117">Configuración de la aplicación de servidor</span><span class="sxs-lookup"><span data-stu-id="2732f-117">Server app configuration</span></span>

<span data-ttu-id="2732f-118">En las secciones siguientes se describen las adiciones al proyecto cuando se incluye compatibilidad con la autenticación.</span><span class="sxs-lookup"><span data-stu-id="2732f-118">The following sections describe additions to the project when authentication support is included.</span></span>

### <a name="startup-class"></a><span data-ttu-id="2732f-119">Clase Startup</span><span class="sxs-lookup"><span data-stu-id="2732f-119">Startup class</span></span>

<span data-ttu-id="2732f-120">La `Startup` clase tiene las siguientes adiciones:</span><span class="sxs-lookup"><span data-stu-id="2732f-120">The `Startup` class has the following additions:</span></span>

* <span data-ttu-id="2732f-121">En `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="2732f-121">In `Startup.ConfigureServices`:</span></span>

  * <span data-ttu-id="2732f-122">Identidad:</span><span class="sxs-lookup"><span data-stu-id="2732f-122">Identity:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="2732f-123">IdentityServer con un método <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> auxiliar adicional que configura algunas convenciones de ASP.net Core predeterminadas en la parte superior de IdentityServer:</span><span class="sxs-lookup"><span data-stu-id="2732f-123">IdentityServer with an additional <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method that sets up some default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="2732f-124">Autenticación con un método <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> auxiliar adicional que configura la aplicación para validar los tokens JWT generados por IdentityServer:</span><span class="sxs-lookup"><span data-stu-id="2732f-124">Authentication with an additional <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="2732f-125">En `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="2732f-125">In `Startup.Configure`:</span></span>

  * <span data-ttu-id="2732f-126">El middleware de autenticación responsable de validar las credenciales de solicitud y establecer el usuario en el contexto de la solicitud:</span><span class="sxs-lookup"><span data-stu-id="2732f-126">The authentication middleware that is responsible for validating the request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="2732f-127">El middleware IdentityServer que expone los puntos de conexión de Open ID Connect (OIDC):</span><span class="sxs-lookup"><span data-stu-id="2732f-127">The IdentityServer middleware that exposes the Open ID Connect (OIDC) endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="2732f-128">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="2732f-128">AddApiAuthorization</span></span>

<span data-ttu-id="2732f-129">El <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> método auxiliar configura [IdentityServer](https://identityserver.io/) para escenarios ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="2732f-129">The <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method configures [IdentityServer](https://identityserver.io/) for ASP.NET Core scenarios.</span></span> <span data-ttu-id="2732f-130">IdentityServer es un marco de trabajo eficaz y extensible para controlar los problemas de seguridad de las aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="2732f-130">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="2732f-131">IdentityServer expone una complejidad innecesaria para los escenarios más comunes.</span><span class="sxs-lookup"><span data-stu-id="2732f-131">IdentityServer exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="2732f-132">Por lo tanto, se proporciona un conjunto de convenciones y opciones de configuración que consideramos un buen punto de partida.</span><span class="sxs-lookup"><span data-stu-id="2732f-132">Consequently, a set of conventions and configuration options is provided that we consider a good starting point.</span></span> <span data-ttu-id="2732f-133">Una vez que cambie la autenticación, toda la potencia de IdentityServer sigue estando disponible para personalizar la autenticación para adaptarse a los requisitos de una aplicación.</span><span class="sxs-lookup"><span data-stu-id="2732f-133">Once your authentication needs change, the full power of IdentityServer is still available to customize authentication to suit an app's requirements.</span></span>

### <a name="addidentityserverjwt"></a><span data-ttu-id="2732f-134">AddIdentityServerJwt</span><span class="sxs-lookup"><span data-stu-id="2732f-134">AddIdentityServerJwt</span></span>

<span data-ttu-id="2732f-135">El <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> método auxiliar configura un esquema de directiva para la aplicación como el controlador de autenticación predeterminado.</span><span class="sxs-lookup"><span data-stu-id="2732f-135">The <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="2732f-136">La Directiva está configurada para permitir que la identidad controle todas las solicitudes enrutadas a cualquier subruta en el espacio `/Identity`de la dirección URL de identidad.</span><span class="sxs-lookup"><span data-stu-id="2732f-136">The policy is configured to allow Identity to handle all requests routed to any subpath in the Identity URL space `/Identity`.</span></span> <span data-ttu-id="2732f-137"><xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> Controla todas las demás solicitudes.</span><span class="sxs-lookup"><span data-stu-id="2732f-137">The <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> handles all other requests.</span></span> <span data-ttu-id="2732f-138">Además, este método:</span><span class="sxs-lookup"><span data-stu-id="2732f-138">Additionally, this method:</span></span>

* <span data-ttu-id="2732f-139">Registra un `{APPLICATION NAME}API` recurso de API con IdentityServer con un ámbito predeterminado de `{APPLICATION NAME}API`.</span><span class="sxs-lookup"><span data-stu-id="2732f-139">Registers an `{APPLICATION NAME}API` API resource with IdentityServer with a default scope of `{APPLICATION NAME}API`.</span></span>
* <span data-ttu-id="2732f-140">Configura el middleware del token de portador de JWT para validar los tokens emitidos por IdentityServer para la aplicación.</span><span class="sxs-lookup"><span data-stu-id="2732f-140">Configures the JWT Bearer Token Middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="2732f-141">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="2732f-141">WeatherForecastController</span></span>

<span data-ttu-id="2732f-142">En `WeatherForecastController` (*Controllers/WeatherForecastController. CS*), el [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atributo se aplica a la clase.</span><span class="sxs-lookup"><span data-stu-id="2732f-142">In the `WeatherForecastController` (*Controllers/WeatherForecastController.cs*), the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is applied to the class.</span></span> <span data-ttu-id="2732f-143">El atributo indica que el usuario debe estar autorizado en función de la directiva predeterminada para tener acceso al recurso.</span><span class="sxs-lookup"><span data-stu-id="2732f-143">The attribute indicates that the user must be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="2732f-144">La Directiva de autorización predeterminada está configurada para usar el esquema de autenticación predeterminado, que <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> se configura por el esquema de directivas mencionado anteriormente.</span><span class="sxs-lookup"><span data-stu-id="2732f-144">The default authorization policy is configured to use the default authentication scheme, which is set up by <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> to the policy scheme that was mentioned earlier.</span></span> <span data-ttu-id="2732f-145">El método auxiliar configura <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> como el controlador predeterminado para las solicitudes a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="2732f-145">The helper method configures <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> as the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="2732f-146">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="2732f-146">ApplicationDbContext</span></span>

<span data-ttu-id="2732f-147">En `ApplicationDbContext` (*Data/ApplicationDbContext. CS*), lo mismo <xref:Microsoft.EntityFrameworkCore.DbContext> se usa en la identidad, con la excepción de que <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> se extiende para incluir el esquema de IdentityServer.</span><span class="sxs-lookup"><span data-stu-id="2732f-147">In the `ApplicationDbContext` (*Data/ApplicationDbContext.cs*), the same <xref:Microsoft.EntityFrameworkCore.DbContext> is used in Identity with the exception that it extends <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> to include the schema for IdentityServer.</span></span> <span data-ttu-id="2732f-148">La clase <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> se deriva de la clase <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span><span class="sxs-lookup"><span data-stu-id="2732f-148"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> is derived from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span></span>

<span data-ttu-id="2732f-149">Para obtener el control total del esquema de la base de datos, herede de una <xref:Microsoft.EntityFrameworkCore.DbContext> de las clases de identidad disponibles y configure el contexto para `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` incluir el `OnModelCreating` esquema de identidad mediante una llamada a en el método.</span><span class="sxs-lookup"><span data-stu-id="2732f-149">To gain full control of the database schema, inherit from one of the available Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in the `OnModelCreating` method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="2732f-150">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="2732f-150">OidcConfigurationController</span></span>

<span data-ttu-id="2732f-151">En `OidcConfigurationController` (*Controllers/OidcConfigurationController. CS*), el punto de conexión del cliente se aprovisiona para servir los parámetros de OIDC.</span><span class="sxs-lookup"><span data-stu-id="2732f-151">In the `OidcConfigurationController` (*Controllers/OidcConfigurationController.cs*), the client endpoint is provisioned to serve OIDC parameters.</span></span>

### <a name="app-settings-files"></a><span data-ttu-id="2732f-152">Archivos de configuración de la aplicación</span><span class="sxs-lookup"><span data-stu-id="2732f-152">App settings files</span></span>

<span data-ttu-id="2732f-153">En el archivo de configuración de la aplicación (*appSettings. JSON*) en la raíz `IdentityServer` del proyecto, en la sección se describe la lista de clientes configurados.</span><span class="sxs-lookup"><span data-stu-id="2732f-153">In the app settings file (*appsettings.json*) at the project root, the `IdentityServer` section describes the list of configured clients.</span></span> <span data-ttu-id="2732f-154">En el ejemplo siguiente, hay un solo cliente.</span><span class="sxs-lookup"><span data-stu-id="2732f-154">In the following example, there's a single client.</span></span> <span data-ttu-id="2732f-155">El nombre de cliente se corresponde con el nombre de la aplicación y se asigna por `ClientId` Convención al parámetro de OAuth.</span><span class="sxs-lookup"><span data-stu-id="2732f-155">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="2732f-156">El perfil indica el tipo de aplicación que se está configurando.</span><span class="sxs-lookup"><span data-stu-id="2732f-156">The profile indicates the app type being configured.</span></span> <span data-ttu-id="2732f-157">El perfil se usa internamente para controlar las convenciones que simplifican el proceso de configuración del servidor.</span><span class="sxs-lookup"><span data-stu-id="2732f-157">The profile is used internally to drive conventions that simplify the configuration process for the server.</span></span> <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "BlazorApplicationWithAuthentication.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

<span data-ttu-id="2732f-158">En el archivo de configuración de la aplicación del entorno de desarrollo (*appSettings. Development. JSON*) en la raíz del proyecto, `IdentityServer` la sección describe la clave que se usa para firmar los tokens.</span><span class="sxs-lookup"><span data-stu-id="2732f-158">In the Development environment app settings file (*appsettings.Development.json*) at the project root, the `IdentityServer` section describes the key used to sign tokens.</span></span> <!-- When deploying to production, a key needs to be provisioned and deployed alongside the app, as explained in the [Deploy to production](#deploy-to-production) section. -->

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="client-app-configuration"></a><span data-ttu-id="2732f-159">Configuración de la aplicación cliente</span><span class="sxs-lookup"><span data-stu-id="2732f-159">Client app configuration</span></span>

### <a name="authentication-package"></a><span data-ttu-id="2732f-160">Paquete de autenticación</span><span class="sxs-lookup"><span data-stu-id="2732f-160">Authentication package</span></span>

<span data-ttu-id="2732f-161">Cuando se crea una aplicación para usar cuentas de usuario individuales`Individual`(), la aplicación recibe automáticamente una referencia de paquete `Microsoft.AspNetCore.Components.WebAssembly.Authentication` para el paquete en el archivo de proyecto de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="2732f-161">When an app is created to use Individual User Accounts (`Individual`), the app automatically receives a package reference for the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package in the app's project file.</span></span> <span data-ttu-id="2732f-162">El paquete proporciona un conjunto de primitivas que ayudan a la aplicación a autenticar a los usuarios y obtener tokens para llamar a las API protegidas.</span><span class="sxs-lookup"><span data-stu-id="2732f-162">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="2732f-163">Si agrega la autenticación a una aplicación, agregue manualmente el paquete al archivo de proyecto de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="2732f-163">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

<span data-ttu-id="2732f-164">Reemplace `{VERSION}` en la referencia de paquete anterior por la versión del `Microsoft.AspNetCore.Blazor.Templates` paquete que se muestra <xref:blazor/get-started> en el artículo.</span><span class="sxs-lookup"><span data-stu-id="2732f-164">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

### <a name="api-authorization-support"></a><span data-ttu-id="2732f-165">Compatibilidad con la autorización de API</span><span class="sxs-lookup"><span data-stu-id="2732f-165">API authorization support</span></span>

<span data-ttu-id="2732f-166">La compatibilidad con la autenticación de usuarios está conectada en el contenedor de servicios mediante el método de extensión proporcionado `Microsoft.AspNetCore.Components.WebAssembly.Authentication` en el paquete.</span><span class="sxs-lookup"><span data-stu-id="2732f-166">The support for authenticating users is plugged into the service container by the extension method provided inside the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package.</span></span> <span data-ttu-id="2732f-167">Este método configura todos los servicios necesarios para que la aplicación interactúe con el sistema de autorización existente.</span><span class="sxs-lookup"><span data-stu-id="2732f-167">This method sets up all the services needed for the app to interact with the existing authorization system.</span></span>

```csharp
builder.Services.AddApiAuthorization();
```

<span data-ttu-id="2732f-168">De forma predeterminada, carga la configuración de la aplicación por Convención desde `_configuration/{client-id}`.</span><span class="sxs-lookup"><span data-stu-id="2732f-168">By default, it loads the configuration for the app by convention from `_configuration/{client-id}`.</span></span> <span data-ttu-id="2732f-169">Por Convención, el identificador de cliente se establece en el nombre de ensamblado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="2732f-169">By convention, the client ID is set to the app's assembly name.</span></span> <span data-ttu-id="2732f-170">Esta dirección URL se puede cambiar para que apunte a un punto de conexión independiente mediante una llamada a la sobrecarga con opciones.</span><span class="sxs-lookup"><span data-stu-id="2732f-170">This URL can be changed to point to a separate endpoint by calling the overload with options.</span></span>

### <a name="imports-file"></a><span data-ttu-id="2732f-171">Importar archivo</span><span class="sxs-lookup"><span data-stu-id="2732f-171">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="2732f-172">Página de índice</span><span class="sxs-lookup"><span data-stu-id="2732f-172">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a><span data-ttu-id="2732f-173">Componente de aplicación</span><span class="sxs-lookup"><span data-stu-id="2732f-173">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="2732f-174">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="2732f-174">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="2732f-175">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="2732f-175">LoginDisplay component</span></span>

<span data-ttu-id="2732f-176">El `LoginDisplay` componente (*Shared/LoginDisplay. Razor*) se representa en el `MainLayout` componente (*Shared/MainLayout. Razor*) y administra los comportamientos siguientes:</span><span class="sxs-lookup"><span data-stu-id="2732f-176">The `LoginDisplay` component (*Shared/LoginDisplay.razor*) is rendered in the `MainLayout` component (*Shared/MainLayout.razor*) and manages the following behaviors:</span></span>

* <span data-ttu-id="2732f-177">Para usuarios autenticados:</span><span class="sxs-lookup"><span data-stu-id="2732f-177">For authenticated users:</span></span>
  * <span data-ttu-id="2732f-178">Muestra el nombre de usuario actual.</span><span class="sxs-lookup"><span data-stu-id="2732f-178">Displays the current user name.</span></span>
  * <span data-ttu-id="2732f-179">Proporciona un vínculo a la página de Perfil de usuario de ASP.NET Core identidad.</span><span class="sxs-lookup"><span data-stu-id="2732f-179">Offers a link to the user profile page in ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="2732f-180">Ofrece un botón para cerrar la sesión de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="2732f-180">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="2732f-181">Para usuarios anónimos:</span><span class="sxs-lookup"><span data-stu-id="2732f-181">For anonymous users:</span></span>
  * <span data-ttu-id="2732f-182">Ofrece la opción de registro.</span><span class="sxs-lookup"><span data-stu-id="2732f-182">Offers the option to register.</span></span>
  * <span data-ttu-id="2732f-183">Ofrece la opción de iniciar sesión.</span><span class="sxs-lookup"><span data-stu-id="2732f-183">Offers the option to log in.</span></span>

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

### <a name="authentication-component"></a><span data-ttu-id="2732f-184">Componente de autenticación</span><span class="sxs-lookup"><span data-stu-id="2732f-184">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="2732f-185">Componente FetchData</span><span class="sxs-lookup"><span data-stu-id="2732f-185">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="2732f-186">Ejecución la aplicación</span><span class="sxs-lookup"><span data-stu-id="2732f-186">Run the app</span></span>

<span data-ttu-id="2732f-187">Ejecute la aplicación desde el proyecto de servidor.</span><span class="sxs-lookup"><span data-stu-id="2732f-187">Run the app from the Server project.</span></span> <span data-ttu-id="2732f-188">Al usar Visual Studio, seleccione el proyecto de servidor en **Explorador de soluciones** y seleccione el botón **Ejecutar** en la barra de herramientas o inicie la aplicación en el menú **depurar** .</span><span class="sxs-lookup"><span data-stu-id="2732f-188">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="2732f-189">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="2732f-189">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
