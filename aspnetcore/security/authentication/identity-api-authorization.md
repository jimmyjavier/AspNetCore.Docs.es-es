---
title: Introducción a la autenticación para aplicaciones de una sola página en ASP.NET Core
author: javiercn
description: Se Identity usa con una aplicación de una sola página hospedada dentro de una aplicación ASP.net Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: scaddie
ms.custom: mvc
ms.date: 11/08/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/identity/spa
ms.openlocfilehash: 178f85df0d35027cddb4314f9dabe26af8483ce6
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775679"
---
# <a name="authentication-and-authorization-for-spas"></a><span data-ttu-id="4eb60-103">Autenticación y autorización para spa</span><span class="sxs-lookup"><span data-stu-id="4eb60-103">Authentication and authorization for SPAs</span></span>

<span data-ttu-id="4eb60-104">ASP.NET Core 3,0 o posterior ofrece autenticación en aplicaciones de una sola página (Spa) mediante la compatibilidad con la autorización de la API.</span><span class="sxs-lookup"><span data-stu-id="4eb60-104">ASP.NET Core 3.0 or later offers authentication in Single Page Apps (SPAs) using the support for API authorization.</span></span> <span data-ttu-id="4eb60-105">ASP.NET Core Identity para la autenticación y el almacenamiento de usuarios se combinan con [IdentityServer](https://identityserver.io/) para implementar Open ID Connect.</span><span class="sxs-lookup"><span data-stu-id="4eb60-105">ASP.NET Core Identity for authenticating and storing users is combined with [IdentityServer](https://identityserver.io/) for implementing Open ID Connect.</span></span>

<span data-ttu-id="4eb60-106">Se ha agregado un parámetro de autenticación a las plantillas de proyecto **angular** y **reAct** que es similar al parámetro de autenticación en las plantillas de proyecto **aplicación web (controlador de vista de modelos)** (MVC) y **aplicación web** (Razor páginas).</span><span class="sxs-lookup"><span data-stu-id="4eb60-106">An authentication parameter was added to the **Angular** and **React** project templates that is similar to the authentication parameter in the **Web Application (Model-View-Controller)** (MVC) and **Web Application** (Razor Pages) project templates.</span></span> <span data-ttu-id="4eb60-107">Los valores de parámetro permitidos son **None** y **individual**.</span><span class="sxs-lookup"><span data-stu-id="4eb60-107">The allowed parameter values are **None** and **Individual**.</span></span> <span data-ttu-id="4eb60-108">En este momento, la plantilla de proyecto **reAct. js y Redux** no admite el parámetro de autenticación.</span><span class="sxs-lookup"><span data-stu-id="4eb60-108">The **React.js and Redux** project template doesn't support the authentication parameter at this time.</span></span>

## <a name="create-an-app-with-api-authorization-support"></a><span data-ttu-id="4eb60-109">Creación de una aplicación con compatibilidad con la autorización de API</span><span class="sxs-lookup"><span data-stu-id="4eb60-109">Create an app with API authorization support</span></span>

<span data-ttu-id="4eb60-110">Se puede usar la autenticación y autorización de usuario con angular y reAct Spa.</span><span class="sxs-lookup"><span data-stu-id="4eb60-110">User authentication and authorization can be used with both Angular and React SPAs.</span></span> <span data-ttu-id="4eb60-111">Abra un shell de comandos y ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="4eb60-111">Open a command shell, and run the following command:</span></span>

<span data-ttu-id="4eb60-112">**Angular**:</span><span class="sxs-lookup"><span data-stu-id="4eb60-112">**Angular**:</span></span>

```dotnetcli
dotnet new angular -o <output_directory_name> -au Individual
```

<span data-ttu-id="4eb60-113">**ReAct**:</span><span class="sxs-lookup"><span data-stu-id="4eb60-113">**React**:</span></span>

```dotnetcli
dotnet new react -o <output_directory_name> -au Individual
```

<span data-ttu-id="4eb60-114">El comando anterior crea una aplicación ASP.NET Core con un directorio *ClientApp* que contiene el Spa.</span><span class="sxs-lookup"><span data-stu-id="4eb60-114">The preceding command creates an ASP.NET Core app with a *ClientApp* directory containing the SPA.</span></span>

## <a name="general-description-of-the-aspnet-core-components-of-the-app"></a><span data-ttu-id="4eb60-115">Descripción general de los componentes de ASP.NET Core de la aplicación</span><span class="sxs-lookup"><span data-stu-id="4eb60-115">General description of the ASP.NET Core components of the app</span></span>

<span data-ttu-id="4eb60-116">En las secciones siguientes se describen las adiciones al proyecto cuando se incluye compatibilidad con la autenticación:</span><span class="sxs-lookup"><span data-stu-id="4eb60-116">The following sections describe additions to the project when authentication support is included:</span></span>

### <a name="startup-class"></a><span data-ttu-id="4eb60-117">Clase Startup</span><span class="sxs-lookup"><span data-stu-id="4eb60-117">Startup class</span></span>

<span data-ttu-id="4eb60-118">La `Startup` clase tiene las siguientes adiciones:</span><span class="sxs-lookup"><span data-stu-id="4eb60-118">The `Startup` class has the following additions:</span></span>

* <span data-ttu-id="4eb60-119">Dentro del `Startup.ConfigureServices` método:</span><span class="sxs-lookup"><span data-stu-id="4eb60-119">Inside the `Startup.ConfigureServices` method:</span></span>
  * Identity<span data-ttu-id="4eb60-120">con la interfaz de usuario predeterminada:</span><span class="sxs-lookup"><span data-stu-id="4eb60-120"> with the default UI:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="4eb60-121">IdentityServer con un método `AddApiAuthorization` auxiliar adicional que configura algunas convenciones de ASP.net Core predeterminadas en la parte superior de IdentityServer:</span><span class="sxs-lookup"><span data-stu-id="4eb60-121">IdentityServer with an additional `AddApiAuthorization` helper method that sets up some default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="4eb60-122">Autenticación con un método `AddIdentityServerJwt` auxiliar adicional que configura la aplicación para validar los tokens JWT generados por IdentityServer:</span><span class="sxs-lookup"><span data-stu-id="4eb60-122">Authentication with an additional `AddIdentityServerJwt` helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="4eb60-123">Dentro del `Startup.Configure` método:</span><span class="sxs-lookup"><span data-stu-id="4eb60-123">Inside the `Startup.Configure` method:</span></span>
  * <span data-ttu-id="4eb60-124">El middleware de autenticación responsable de validar las credenciales de solicitud y establecer el usuario en el contexto de la solicitud:</span><span class="sxs-lookup"><span data-stu-id="4eb60-124">The authentication middleware that is responsible for validating the request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="4eb60-125">El middleware IdentityServer que expone los puntos de conexión de Open ID Connect:</span><span class="sxs-lookup"><span data-stu-id="4eb60-125">The IdentityServer middleware that exposes the Open ID Connect endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="4eb60-126">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="4eb60-126">AddApiAuthorization</span></span>

<span data-ttu-id="4eb60-127">Este método auxiliar configura IdentityServer para usar la configuración admitida.</span><span class="sxs-lookup"><span data-stu-id="4eb60-127">This helper method configures IdentityServer to use our supported configuration.</span></span> <span data-ttu-id="4eb60-128">IdentityServer es un marco de trabajo eficaz y extensible para controlar los problemas de seguridad de las aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="4eb60-128">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="4eb60-129">Al mismo tiempo, esto expone una complejidad innecesaria para los escenarios más comunes.</span><span class="sxs-lookup"><span data-stu-id="4eb60-129">At the same time, that exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="4eb60-130">Por lo tanto, se le proporciona un conjunto de convenciones y opciones de configuración que se consideran un buen punto de partida.</span><span class="sxs-lookup"><span data-stu-id="4eb60-130">Consequently, a set of conventions and configuration options is provided to you that are considered a good starting point.</span></span> <span data-ttu-id="4eb60-131">Una vez que cambie la autenticación, toda la capacidad de IdentityServer sigue estando disponible para personalizar la autenticación para satisfacer sus necesidades.</span><span class="sxs-lookup"><span data-stu-id="4eb60-131">Once your authentication needs change, the full power of IdentityServer is still available to customize authentication to suit your needs.</span></span>

### <a name="addidentityserverjwt"></a><span data-ttu-id="4eb60-132">AddIdentityServerJwt</span><span class="sxs-lookup"><span data-stu-id="4eb60-132">AddIdentityServerJwt</span></span>

<span data-ttu-id="4eb60-133">Este método auxiliar configura un esquema de directiva para la aplicación como el controlador de autenticación predeterminado.</span><span class="sxs-lookup"><span data-stu-id="4eb60-133">This helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="4eb60-134">La Directiva está configurada Identity para permitir que todas las solicitudes se enruten a Identity cualquier subruta enIdentityel espacio de la dirección URL "/".</span><span class="sxs-lookup"><span data-stu-id="4eb60-134">The policy is configured to let Identity handle all requests routed to any subpath in the Identity URL space "/Identity".</span></span> <span data-ttu-id="4eb60-135">`JwtBearerHandler` Controla todas las demás solicitudes.</span><span class="sxs-lookup"><span data-stu-id="4eb60-135">The `JwtBearerHandler` handles all other requests.</span></span> <span data-ttu-id="4eb60-136">Además, este método registra un `<<ApplicationName>>API` recurso de API con IdentityServer con un ámbito predeterminado de `<<ApplicationName>>API` y configura el middleware de token de portador de JWT para validar los tokens emitidos por IdentityServer para la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4eb60-136">Additionally, this method registers an `<<ApplicationName>>API` API resource with IdentityServer with a default scope of `<<ApplicationName>>API` and configures the JWT Bearer token middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="4eb60-137">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="4eb60-137">WeatherForecastController</span></span>

<span data-ttu-id="4eb60-138">En el archivo *Controllers\WeatherForecastController.CS* , observe el `[Authorize]` atributo que se aplica a la clase, que indica que el usuario debe ser autorizado en función de la directiva predeterminada para tener acceso al recurso.</span><span class="sxs-lookup"><span data-stu-id="4eb60-138">In the *Controllers\WeatherForecastController.cs* file, notice the `[Authorize]` attribute applied to the class that indicates that the user needs to be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="4eb60-139">La Directiva de autorización predeterminada está configurada para usar el esquema de autenticación predeterminado, que se configura `AddIdentityServerJwt` por el esquema de directivas mencionado anteriormente, configurando `JwtBearerHandler` el método de este auxiliar como el controlador predeterminado para las solicitudes a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4eb60-139">The default authorization policy happens to be configured to use the default authentication scheme, which is set up by `AddIdentityServerJwt` to the policy scheme that was mentioned above, making the `JwtBearerHandler` configured by such helper method the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="4eb60-140">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="4eb60-140">ApplicationDbContext</span></span>

<span data-ttu-id="4eb60-141">En el archivo *Data\ApplicationDbContext.CS* , observe lo mismo `DbContext` que se usa Identity en con la excepción de que `ApiAuthorizationDbContext` extiende (una clase más derivada `IdentityDbContext`de) para incluir el esquema de IdentityServer.</span><span class="sxs-lookup"><span data-stu-id="4eb60-141">In the *Data\ApplicationDbContext.cs* file, notice the same `DbContext` is used in Identity with the exception that it extends `ApiAuthorizationDbContext` (a more derived class from `IdentityDbContext`) to include the schema for IdentityServer.</span></span>

<span data-ttu-id="4eb60-142">Para obtener el control total del esquema de la base de datos, herede de Identity `DbContext` una de las clases disponibles y configure Identity el contexto para `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` incluir el `OnModelCreating` esquema mediante una llamada a en el método.</span><span class="sxs-lookup"><span data-stu-id="4eb60-142">To gain full control of the database schema, inherit from one of the available Identity `DbContext` classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` on the `OnModelCreating` method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="4eb60-143">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="4eb60-143">OidcConfigurationController</span></span>

<span data-ttu-id="4eb60-144">En el archivo *Controllers\OidcConfigurationController.CS* , observe el punto de conexión aprovisionado para atender los parámetros OIDC que el cliente necesita usar.</span><span class="sxs-lookup"><span data-stu-id="4eb60-144">In the *Controllers\OidcConfigurationController.cs* file, notice the endpoint that's provisioned to serve the OIDC parameters that the client needs to use.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="4eb60-145">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="4eb60-145">appsettings.json</span></span>

<span data-ttu-id="4eb60-146">En el archivo *appSettings. JSON* de la raíz del proyecto, hay una nueva `IdentityServer` sección en la que se describe la lista de clientes configurados.</span><span class="sxs-lookup"><span data-stu-id="4eb60-146">In the *appsettings.json* file of the project root, there's a new `IdentityServer` section that describes the list of configured clients.</span></span> <span data-ttu-id="4eb60-147">En el ejemplo siguiente, hay un solo cliente.</span><span class="sxs-lookup"><span data-stu-id="4eb60-147">In the following example, there's a single client.</span></span> <span data-ttu-id="4eb60-148">El nombre de cliente se corresponde con el nombre de la aplicación y se asigna por `ClientId` Convención al parámetro de OAuth.</span><span class="sxs-lookup"><span data-stu-id="4eb60-148">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="4eb60-149">El perfil indica el tipo de aplicación que se está configurando.</span><span class="sxs-lookup"><span data-stu-id="4eb60-149">The profile indicates the app type being configured.</span></span> <span data-ttu-id="4eb60-150">Se usa internamente para controlar las convenciones que simplifican el proceso de configuración del servidor.</span><span class="sxs-lookup"><span data-stu-id="4eb60-150">It's used internally to drive conventions that simplify the configuration process for the server.</span></span> <span data-ttu-id="4eb60-151">Hay varios perfiles disponibles, como se explica en la sección [perfiles de aplicación](#application-profiles) .</span><span class="sxs-lookup"><span data-stu-id="4eb60-151">There are several profiles available, as explained in the [Application profiles](#application-profiles) section.</span></span>

```json
"IdentityServer": {
  "Clients": {
    "angularindividualpreview3final": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

### <a name="appsettingsdevelopmentjson"></a><span data-ttu-id="4eb60-152">appSettings. Desarrollo. JSON</span><span class="sxs-lookup"><span data-stu-id="4eb60-152">appsettings.Development.json</span></span>

<span data-ttu-id="4eb60-153">En el *appSettings. Archivo Development. JSON* de la raíz del proyecto, hay una `IdentityServer` sección que describe la clave que se usa para firmar los tokens.</span><span class="sxs-lookup"><span data-stu-id="4eb60-153">In the *appsettings.Development.json* file of the project root, there's an `IdentityServer` section that describes the key used to sign tokens.</span></span> <span data-ttu-id="4eb60-154">Al realizar la implementación en producción, es necesario aprovisionar e implementar una clave junto con la aplicación, como se explica en la sección [implementación en producción](#deploy-to-production) .</span><span class="sxs-lookup"><span data-stu-id="4eb60-154">When deploying to production, a key needs to be provisioned and deployed alongside the app, as explained in the [Deploy to production](#deploy-to-production) section.</span></span>

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="general-description-of-the-angular-app"></a><span data-ttu-id="4eb60-155">Descripción general de la aplicación angular</span><span class="sxs-lookup"><span data-stu-id="4eb60-155">General description of the Angular app</span></span>

<span data-ttu-id="4eb60-156">La compatibilidad con la autenticación y la autorización de API en la plantilla de angular reside en su propio módulo angular en el directorio *ClientApp\src\api-Authorization*</span><span class="sxs-lookup"><span data-stu-id="4eb60-156">The authentication and API authorization support in the Angular template resides in its own Angular module in the *ClientApp\src\api-authorization* directory.</span></span> <span data-ttu-id="4eb60-157">El módulo se compone de los siguientes elementos:</span><span class="sxs-lookup"><span data-stu-id="4eb60-157">The module is composed of the following elements:</span></span>

* <span data-ttu-id="4eb60-158">3 componentes:</span><span class="sxs-lookup"><span data-stu-id="4eb60-158">3 components:</span></span>
  * <span data-ttu-id="4eb60-159">*login. Component. ts*: controla el flujo de inicio de sesión de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4eb60-159">*login.component.ts*: Handles the app's login flow.</span></span>
  * <span data-ttu-id="4eb60-160">*logout. Component. ts*: controla el flujo de cierre de sesión de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4eb60-160">*logout.component.ts*: Handles the app's logout flow.</span></span>
  * <span data-ttu-id="4eb60-161">*login-menu. Component. ts*: un widget que muestra uno de los siguientes conjuntos de vínculos:</span><span class="sxs-lookup"><span data-stu-id="4eb60-161">*login-menu.component.ts*: A widget that displays one of the following sets of links:</span></span>
    * <span data-ttu-id="4eb60-162">Los vínculos de administración de perfiles de usuario y cierre de sesión cuando el usuario está autenticado.</span><span class="sxs-lookup"><span data-stu-id="4eb60-162">User profile management and log out links when the user is authenticated.</span></span>
    * <span data-ttu-id="4eb60-163">Los vínculos registro e inicio de sesión cuando el usuario no está autenticado.</span><span class="sxs-lookup"><span data-stu-id="4eb60-163">Registration and log in links when the user isn't authenticated.</span></span>
* <span data-ttu-id="4eb60-164">Una protección `AuthorizeGuard` de ruta que se puede Agregar a las rutas y requiere que un usuario se autentique antes de visitar la ruta.</span><span class="sxs-lookup"><span data-stu-id="4eb60-164">A route guard `AuthorizeGuard` that can be added to routes and requires a user to be authenticated before visiting the route.</span></span>
* <span data-ttu-id="4eb60-165">Un interceptor HTTP `AuthorizeInterceptor` que asocia el token de acceso a las solicitudes HTTP salientes que se dirigen a la API cuando se autentica el usuario.</span><span class="sxs-lookup"><span data-stu-id="4eb60-165">An HTTP interceptor `AuthorizeInterceptor` that attaches the access token to outgoing HTTP requests targeting the API when the user is authenticated.</span></span>
* <span data-ttu-id="4eb60-166">Un servicio `AuthorizeService` que controla los detalles de nivel inferior del proceso de autenticación y expone información sobre el usuario autenticado al resto de la aplicación para su consumo.</span><span class="sxs-lookup"><span data-stu-id="4eb60-166">A service `AuthorizeService` that handles the lower-level details of the authentication process and exposes information about the authenticated user to the rest of the app for consumption.</span></span>
* <span data-ttu-id="4eb60-167">Un módulo angular que define rutas asociadas a las partes de autenticación de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4eb60-167">An Angular module that defines routes associated with the authentication parts of the app.</span></span> <span data-ttu-id="4eb60-168">Expone el componente de menú Inicio de sesión, el interceptor, la protección y el servicio para su consumo desde el resto de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4eb60-168">It exposes the login menu component, the interceptor, the guard, and the service for consumption from the rest of the app.</span></span>

## <a name="general-description-of-the-react-app"></a><span data-ttu-id="4eb60-169">Descripción general de la aplicación reAct</span><span class="sxs-lookup"><span data-stu-id="4eb60-169">General description of the React app</span></span>

<span data-ttu-id="4eb60-170">La compatibilidad con la autenticación y la autorización de API en la plantilla reAct reside en el directorio *ClientApp\src\components\api-Authorization*</span><span class="sxs-lookup"><span data-stu-id="4eb60-170">The support for authentication and API authorization in the React template resides in the *ClientApp\src\components\api-authorization* directory.</span></span> <span data-ttu-id="4eb60-171">Se compone de los siguientes elementos:</span><span class="sxs-lookup"><span data-stu-id="4eb60-171">It's composed of the following elements:</span></span>

* <span data-ttu-id="4eb60-172">4 componentes:</span><span class="sxs-lookup"><span data-stu-id="4eb60-172">4 components:</span></span>
  * <span data-ttu-id="4eb60-173">*Login. js*: controla el flujo de inicio de sesión de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4eb60-173">*Login.js*: Handles the app's login flow.</span></span>
  * <span data-ttu-id="4eb60-174">*Logout. js*: controla el flujo de cierre de sesión de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4eb60-174">*Logout.js*: Handles the app's logout flow.</span></span>
  * <span data-ttu-id="4eb60-175">*LoginMenu. js*: un widget que muestra uno de los siguientes conjuntos de vínculos:</span><span class="sxs-lookup"><span data-stu-id="4eb60-175">*LoginMenu.js*: A widget that displays one of the following sets of links:</span></span>
    * <span data-ttu-id="4eb60-176">Los vínculos de administración de perfiles de usuario y cierre de sesión cuando el usuario está autenticado.</span><span class="sxs-lookup"><span data-stu-id="4eb60-176">User profile management and log out links when the user is authenticated.</span></span>
    * <span data-ttu-id="4eb60-177">Los vínculos registro e inicio de sesión cuando el usuario no está autenticado.</span><span class="sxs-lookup"><span data-stu-id="4eb60-177">Registration and log in links when the user isn't authenticated.</span></span>
  * <span data-ttu-id="4eb60-178">*AuthorizeRoute. js*: componente de ruta que requiere que un usuario se autentique antes de representar el componente indicado en el `Component` parámetro.</span><span class="sxs-lookup"><span data-stu-id="4eb60-178">*AuthorizeRoute.js*: A route component that requires a user to be authenticated before rendering the component indicated in the `Component` parameter.</span></span>
* <span data-ttu-id="4eb60-179">Instancia exportada `authService` de la `AuthorizeService` clase que controla los detalles de nivel inferior del proceso de autenticación y expone información sobre el usuario autenticado al resto de la aplicación para su consumo.</span><span class="sxs-lookup"><span data-stu-id="4eb60-179">An exported `authService` instance of class `AuthorizeService` that handles the lower-level details of the authentication process and exposes information about the authenticated user to the rest of the app for consumption.</span></span>

<span data-ttu-id="4eb60-180">Ahora que ha visto los componentes principales de la solución, puede profundizar en los escenarios individuales de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4eb60-180">Now that you've seen the main components of the solution, you can take a deeper look at individual scenarios for the app.</span></span>

## <a name="require-authorization-on-a-new-api"></a><span data-ttu-id="4eb60-181">Requerir autorización en una nueva API</span><span class="sxs-lookup"><span data-stu-id="4eb60-181">Require authorization on a new API</span></span>

<span data-ttu-id="4eb60-182">De forma predeterminada, el sistema está configurado para requerir fácilmente la autorización para las nuevas API.</span><span class="sxs-lookup"><span data-stu-id="4eb60-182">By default, the system is configured to easily require authorization for new APIs.</span></span> <span data-ttu-id="4eb60-183">Para ello, cree un nuevo controlador y agregue el `[Authorize]` atributo a la clase de controlador o a cualquier acción dentro del controlador.</span><span class="sxs-lookup"><span data-stu-id="4eb60-183">To do so, create a new controller and add the `[Authorize]` attribute to the controller class or to any action within the controller.</span></span>

## <a name="customize-the-api-authentication-handler"></a><span data-ttu-id="4eb60-184">Personalización del controlador de autenticación de API</span><span class="sxs-lookup"><span data-stu-id="4eb60-184">Customize the API authentication handler</span></span>

<span data-ttu-id="4eb60-185">Para personalizar la configuración del controlador de JWT de la API, configure su <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> instancia:</span><span class="sxs-lookup"><span data-stu-id="4eb60-185">To customize the configuration of the API's JWT handler, configure its <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> instance:</span></span>

```csharp
services.AddAuthentication()
    .AddIdentityServerJwt();

services.Configure<JwtBearerOptions>(
    IdentityServerJwtConstants.IdentityServerJwtBearerScheme,
    options =>
    {
        ...
    });
```

<span data-ttu-id="4eb60-186">El controlador JWT de la API genera eventos que permiten controlar el proceso de autenticación `JwtBearerEvents`mediante.</span><span class="sxs-lookup"><span data-stu-id="4eb60-186">The API's JWT handler raises events that enable control over the authentication process using `JwtBearerEvents`.</span></span> <span data-ttu-id="4eb60-187">Para proporcionar compatibilidad con la autorización de `AddIdentityServerJwt` API, registra sus propios controladores de eventos.</span><span class="sxs-lookup"><span data-stu-id="4eb60-187">To provide support for API authorization, `AddIdentityServerJwt` registers its own event handlers.</span></span>

<span data-ttu-id="4eb60-188">Para personalizar el control de un evento, ajuste el controlador de eventos existente con lógica adicional, según sea necesario.</span><span class="sxs-lookup"><span data-stu-id="4eb60-188">To customize the handling of an event, wrap the existing event handler with additional logic as required.</span></span> <span data-ttu-id="4eb60-189">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="4eb60-189">For example:</span></span>

```csharp
services.Configure<JwtBearerOptions>(
    IdentityServerJwtConstants.IdentityServerJwtBearerScheme,
    options =>
    {
        var onTokenValidated = options.Events.OnTokenValidated;       
        
        options.Events.OnTokenValidated = async context =>
        {
            await onTokenValidated(context);
            ...
        }
    });
```

<span data-ttu-id="4eb60-190">En el código anterior, el `OnTokenValidated` controlador de eventos se reemplaza con una implementación personalizada.</span><span class="sxs-lookup"><span data-stu-id="4eb60-190">In the preceding code, the `OnTokenValidated` event handler is replaced with a custom implementation.</span></span> <span data-ttu-id="4eb60-191">Esta implementación:</span><span class="sxs-lookup"><span data-stu-id="4eb60-191">This implementation:</span></span>

1. <span data-ttu-id="4eb60-192">Llama a la implementación original proporcionada por la compatibilidad con la autorización de la API.</span><span class="sxs-lookup"><span data-stu-id="4eb60-192">Calls the original implementation provided by the API authorization support.</span></span>
1. <span data-ttu-id="4eb60-193">Ejecute su propia lógica personalizada.</span><span class="sxs-lookup"><span data-stu-id="4eb60-193">Run its own custom logic.</span></span>

## <a name="protect-a-client-side-route-angular"></a><span data-ttu-id="4eb60-194">Protección de una ruta de lado cliente (angular)</span><span class="sxs-lookup"><span data-stu-id="4eb60-194">Protect a client-side route (Angular)</span></span>

<span data-ttu-id="4eb60-195">La protección de una ruta del lado cliente se realiza mediante la adición de la protección de autorización a la lista de protecciones que se deben ejecutar al configurar una ruta.</span><span class="sxs-lookup"><span data-stu-id="4eb60-195">Protecting a client-side route is done by adding the authorize guard to the list of guards to run when configuring a route.</span></span> <span data-ttu-id="4eb60-196">Por ejemplo, puede ver cómo se configura la `fetch-data` ruta dentro del módulo de angular de la aplicación principal:</span><span class="sxs-lookup"><span data-stu-id="4eb60-196">As an example, you can see how the `fetch-data` route is configured within the main app Angular module:</span></span>

```typescript
RouterModule.forRoot([
  // ...
  { path: 'fetch-data', component: FetchDataComponent, canActivate: [AuthorizeGuard] },
])
```

<span data-ttu-id="4eb60-197">Es importante mencionar que la protección de una ruta no protege el punto de conexión real (lo que `[Authorize]` requiere que se le aplique un atributo), sino que solo impide que el usuario navegue hasta la ruta de la aplicación del lado cliente cuando no se autentica.</span><span class="sxs-lookup"><span data-stu-id="4eb60-197">It's important to mention that protecting a route doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it) but that it only prevents the user from navigating to the given client-side route when it isn't authenticated.</span></span>

## <a name="authenticate-api-requests-angular"></a><span data-ttu-id="4eb60-198">Autenticar solicitudes de API (angular)</span><span class="sxs-lookup"><span data-stu-id="4eb60-198">Authenticate API requests (Angular)</span></span>

<span data-ttu-id="4eb60-199">La autenticación de las solicitudes a las API hospedadas junto a la aplicación se realiza automáticamente mediante el uso del interceptor de cliente HTTP definido por la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4eb60-199">Authenticating requests to APIs hosted alongside the app is done automatically through the use of the HTTP client interceptor defined by the app.</span></span>

## <a name="protect-a-client-side-route-react"></a><span data-ttu-id="4eb60-200">Proteger una ruta del lado cliente (reAct)</span><span class="sxs-lookup"><span data-stu-id="4eb60-200">Protect a client-side route (React)</span></span>

<span data-ttu-id="4eb60-201">Proteja una ruta del lado cliente mediante el `AuthorizeRoute` componente en lugar del componente sin formato `Route` .</span><span class="sxs-lookup"><span data-stu-id="4eb60-201">Protect a client-side route by using the `AuthorizeRoute` component instead of the plain `Route` component.</span></span> <span data-ttu-id="4eb60-202">Por ejemplo, observe cómo se `fetch-data` configura la ruta dentro del `App` componente:</span><span class="sxs-lookup"><span data-stu-id="4eb60-202">For example, notice how the `fetch-data` route is configured within the `App` component:</span></span>

```jsx
<AuthorizeRoute path='/fetch-data' component={FetchData} />
```

<span data-ttu-id="4eb60-203">Protección de una ruta:</span><span class="sxs-lookup"><span data-stu-id="4eb60-203">Protecting a route:</span></span>

* <span data-ttu-id="4eb60-204">No protege el punto de conexión real (que sigue `[Authorize]` necesitando un atributo aplicado).</span><span class="sxs-lookup"><span data-stu-id="4eb60-204">Doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it).</span></span>
* <span data-ttu-id="4eb60-205">Solo impide que el usuario navegue hasta la ruta del lado cliente determinada cuando no se autentica.</span><span class="sxs-lookup"><span data-stu-id="4eb60-205">Only prevents the user from navigating to the given client-side route when it isn't authenticated.</span></span>

## <a name="authenticate-api-requests-react"></a><span data-ttu-id="4eb60-206">Autenticar solicitudes de API (reAct)</span><span class="sxs-lookup"><span data-stu-id="4eb60-206">Authenticate API requests (React)</span></span>

<span data-ttu-id="4eb60-207">La autenticación de solicitudes con reAct se realiza mediante la importación `authService` en primer lugar `AuthorizeService`de la instancia de.</span><span class="sxs-lookup"><span data-stu-id="4eb60-207">Authenticating requests with React is done by first importing the `authService` instance from the `AuthorizeService`.</span></span> <span data-ttu-id="4eb60-208">El token de acceso se recupera del `authService` y se adjunta a la solicitud, como se muestra a continuación.</span><span class="sxs-lookup"><span data-stu-id="4eb60-208">The access token is retrieved from the `authService` and is attached to the request as shown below.</span></span> <span data-ttu-id="4eb60-209">En los componentes de reAct, este trabajo se realiza normalmente `componentDidMount` en el método de ciclo de vida o como resultado de alguna interacción del usuario.</span><span class="sxs-lookup"><span data-stu-id="4eb60-209">In React components, this work is typically done in the `componentDidMount` lifecycle method or as the result from some user interaction.</span></span>

### <a name="import-the-authservice-into-your-component"></a><span data-ttu-id="4eb60-210">Importar la authService en el componente</span><span class="sxs-lookup"><span data-stu-id="4eb60-210">Import the authService into your component</span></span>

```javascript
import authService from './api-authorization/AuthorizeService'
```

### <a name="retrieve-and-attach-the-access-token-to-the-response"></a><span data-ttu-id="4eb60-211">Recuperar y adjuntar el token de acceso a la respuesta</span><span class="sxs-lookup"><span data-stu-id="4eb60-211">Retrieve and attach the access token to the response</span></span>

```javascript
async populateWeatherData() {
  const token = await authService.getAccessToken();
  const response = await fetch('api/SampleData/WeatherForecasts', {
    headers: !token ? {} : { 'Authorization': `Bearer ${token}` }
  });
  const data = await response.json();
  this.setState({ forecasts: data, loading: false });
}
```

## <a name="deploy-to-production"></a><span data-ttu-id="4eb60-212">Implementación en producción</span><span class="sxs-lookup"><span data-stu-id="4eb60-212">Deploy to production</span></span>

<span data-ttu-id="4eb60-213">Para implementar la aplicación en producción, se deben aprovisionar los siguientes recursos:</span><span class="sxs-lookup"><span data-stu-id="4eb60-213">To deploy the app to production, the following resources need to be provisioned:</span></span>

* <span data-ttu-id="4eb60-214">Una base de datos para Identity almacenar las cuentas de usuario y las concesiones de IdentityServer.</span><span class="sxs-lookup"><span data-stu-id="4eb60-214">A database to store the Identity user accounts and the IdentityServer grants.</span></span>
* <span data-ttu-id="4eb60-215">Un certificado de producción que se utilizará para firmar los tokens.</span><span class="sxs-lookup"><span data-stu-id="4eb60-215">A production certificate to use for signing tokens.</span></span>
  * <span data-ttu-id="4eb60-216">No hay requisitos específicos para este certificado; puede ser un certificado autofirmado o un certificado aprovisionado a través de una entidad de certificación.</span><span class="sxs-lookup"><span data-stu-id="4eb60-216">There are no specific requirements for this certificate; it can be a self-signed certificate or a certificate provisioned through a CA authority.</span></span>
  * <span data-ttu-id="4eb60-217">Se puede generar a través de herramientas estándar como PowerShell o OpenSSL.</span><span class="sxs-lookup"><span data-stu-id="4eb60-217">It can be generated through standard tools like PowerShell or OpenSSL.</span></span>
  * <span data-ttu-id="4eb60-218">Se puede instalar en el almacén de certificados en los equipos de destino o implementarse como un archivo *. pfx* con una contraseña segura.</span><span class="sxs-lookup"><span data-stu-id="4eb60-218">It can be installed into the certificate store on the target machines or deployed as a *.pfx* file with a strong password.</span></span>

### <a name="example-deploy-to-azure-websites"></a><span data-ttu-id="4eb60-219">Ejemplo: implementación en Azure websites</span><span class="sxs-lookup"><span data-stu-id="4eb60-219">Example: Deploy to Azure Websites</span></span>

<span data-ttu-id="4eb60-220">En esta sección se describe la implementación de la aplicación en sitios web de Azure mediante un certificado almacenado en el almacén de certificados.</span><span class="sxs-lookup"><span data-stu-id="4eb60-220">This section describes deploying the app to Azure websites using a certificate stored in the certificate store.</span></span> <span data-ttu-id="4eb60-221">Para modificar la aplicación para cargar un certificado desde el almacén de certificados, el plan de App Service debe estar al menos en el nivel estándar al configurar en un paso posterior.</span><span class="sxs-lookup"><span data-stu-id="4eb60-221">To modify the app to load a certificate from the certificate store, the App Service plan needs to be on at least the Standard tier when you configure in a later step.</span></span> <span data-ttu-id="4eb60-222">En el archivo *appSettings. JSON* de la aplicación, modifique `IdentityServer` la sección para incluir los detalles de la clave:</span><span class="sxs-lookup"><span data-stu-id="4eb60-222">In the app's *appsettings.json* file, modify the `IdentityServer` section to include the key details:</span></span>

```json
"IdentityServer": {
  "Key": {
    "Type": "Store",
    "StoreName": "My",
    "StoreLocation": "CurrentUser",
    "Name": "CN=MyApplication"
  }
}
```

* <span data-ttu-id="4eb60-223">El nombre del almacén representa el nombre del almacén de certificados donde se almacena el certificado.</span><span class="sxs-lookup"><span data-stu-id="4eb60-223">The store name represents the name of the certificate store where the certificate is stored.</span></span> <span data-ttu-id="4eb60-224">En este caso, apunta al almacén de usuarios personales.</span><span class="sxs-lookup"><span data-stu-id="4eb60-224">In this case, it points to the personal user store.</span></span>
* <span data-ttu-id="4eb60-225">La ubicación del almacén representa dónde se debe cargar el certificado`CurrentUser` ( `LocalMachine`o).</span><span class="sxs-lookup"><span data-stu-id="4eb60-225">The store location represents where to load the certificate from (`CurrentUser` or `LocalMachine`).</span></span>
* <span data-ttu-id="4eb60-226">La propiedad Name en el certificado corresponde al sujeto distintivo del certificado.</span><span class="sxs-lookup"><span data-stu-id="4eb60-226">The name property on certificate corresponds with the distinguished subject for the certificate.</span></span>

<span data-ttu-id="4eb60-227">Para implementar en Azure websites, implemente la aplicación siguiendo los pasos descritos en [implementación de la aplicación en Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure) para crear los recursos de Azure necesarios e implementar la aplicación en producción.</span><span class="sxs-lookup"><span data-stu-id="4eb60-227">To deploy to Azure Websites, deploy the app following the steps in [Deploy the app to Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure) to create the necessary Azure resources and deploy the app to production.</span></span>

<span data-ttu-id="4eb60-228">Después de seguir las instrucciones anteriores, la aplicación se implementa en Azure pero todavía no es funcional.</span><span class="sxs-lookup"><span data-stu-id="4eb60-228">After following the preceding instructions, the app is deployed to Azure but isn't yet functional.</span></span> <span data-ttu-id="4eb60-229">Todavía es necesario configurar el certificado usado por la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4eb60-229">The certificate used by the app still needs to be set up.</span></span> <span data-ttu-id="4eb60-230">Busque la huella digital del certificado que se va a usar y siga los pasos descritos en [carga de certificados](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code).</span><span class="sxs-lookup"><span data-stu-id="4eb60-230">Locate the thumbprint for the certificate to be used, and follow the steps described in [Load your certificates](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code).</span></span>

<span data-ttu-id="4eb60-231">Aunque estos pasos mencionan SSL, hay una sección de **certificados privados** en el portal donde puede cargar el certificado aprovisionado para usarlo con la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4eb60-231">While these steps mention SSL, there's a **Private certificates** section on the portal where you can upload the provisioned certificate to use with the app.</span></span>

<span data-ttu-id="4eb60-232">Después de este paso, reinicie la aplicación y debe ser funcional.</span><span class="sxs-lookup"><span data-stu-id="4eb60-232">After this step, restart the app and it should be functional.</span></span>

## <a name="other-configuration-options"></a><span data-ttu-id="4eb60-233">Otras opciones de configuración</span><span class="sxs-lookup"><span data-stu-id="4eb60-233">Other configuration options</span></span>

<span data-ttu-id="4eb60-234">La compatibilidad con la autorización de API se basa en IdentityServer con un conjunto de convenciones, valores predeterminados y mejoras para simplificar la experiencia de spa.</span><span class="sxs-lookup"><span data-stu-id="4eb60-234">The support for API authorization builds on top of IdentityServer with a set of conventions, default values, and enhancements to simplify the experience for SPAs.</span></span> <span data-ttu-id="4eb60-235">No es necesario decir que toda la capacidad de IdentityServer está disponible en segundo plano si las integraciones de ASP.NET Core no cubren su escenario.</span><span class="sxs-lookup"><span data-stu-id="4eb60-235">Needless to say, the full power of IdentityServer is available behind the scenes if the ASP.NET Core integrations don't cover your scenario.</span></span> <span data-ttu-id="4eb60-236">La compatibilidad con ASP.NET Core se centra en las aplicaciones de "primera parte", donde se crean e implementan todas las aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="4eb60-236">The ASP.NET Core support is focused on "first-party" apps, where all the apps are created and deployed by our organization.</span></span> <span data-ttu-id="4eb60-237">Como tal, no se ofrece soporte técnico para elementos como el consentimiento o la Federación.</span><span class="sxs-lookup"><span data-stu-id="4eb60-237">As such, support isn't offered for things like consent or federation.</span></span> <span data-ttu-id="4eb60-238">Para esos escenarios, use IdentityServer y siga su documentación.</span><span class="sxs-lookup"><span data-stu-id="4eb60-238">For those scenarios, use IdentityServer and follow their documentation.</span></span>

### <a name="application-profiles"></a><span data-ttu-id="4eb60-239">Perfiles de aplicación</span><span class="sxs-lookup"><span data-stu-id="4eb60-239">Application profiles</span></span>

<span data-ttu-id="4eb60-240">Los perfiles de aplicación son configuraciones predefinidas para aplicaciones que definen aún más sus parámetros.</span><span class="sxs-lookup"><span data-stu-id="4eb60-240">Application profiles are predefined configurations for apps that further define their parameters.</span></span> <span data-ttu-id="4eb60-241">En este momento, se admiten los siguientes perfiles:</span><span class="sxs-lookup"><span data-stu-id="4eb60-241">At this time, the following profiles are supported:</span></span>

* <span data-ttu-id="4eb60-242">`IdentityServerSPA`: Representa una SPA hospedada junto con IdentityServer como una sola unidad.</span><span class="sxs-lookup"><span data-stu-id="4eb60-242">`IdentityServerSPA`: Represents a SPA hosted alongside IdentityServer as a single unit.</span></span>
  * <span data-ttu-id="4eb60-243">El `redirect_uri` valor predeterminado es `/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="4eb60-243">The `redirect_uri` defaults to `/authentication/login-callback`.</span></span>
  * <span data-ttu-id="4eb60-244">El `post_logout_redirect_uri` valor predeterminado es `/authentication/logout-callback`.</span><span class="sxs-lookup"><span data-stu-id="4eb60-244">The `post_logout_redirect_uri` defaults to `/authentication/logout-callback`.</span></span>
  * <span data-ttu-id="4eb60-245">El conjunto de ámbitos incluye `openid`, `profile`y cada ámbito definido para las API de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4eb60-245">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the app.</span></span>
  * <span data-ttu-id="4eb60-246">El conjunto de tipos de respuesta OIDC permitidos es `id_token token` o cada uno de`id_token`ellos `token`individualmente (,).</span><span class="sxs-lookup"><span data-stu-id="4eb60-246">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="4eb60-247">El modo de respuesta permitido `fragment`es.</span><span class="sxs-lookup"><span data-stu-id="4eb60-247">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="4eb60-248">`SPA`: Representa un SPA que no está hospedado con IdentityServer.</span><span class="sxs-lookup"><span data-stu-id="4eb60-248">`SPA`: Represents a SPA that isn't hosted with IdentityServer.</span></span>
  * <span data-ttu-id="4eb60-249">El conjunto de ámbitos incluye `openid`, `profile`y cada ámbito definido para las API de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4eb60-249">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the app.</span></span>
  * <span data-ttu-id="4eb60-250">El conjunto de tipos de respuesta OIDC permitidos es `id_token token` o cada uno de`id_token`ellos `token`individualmente (,).</span><span class="sxs-lookup"><span data-stu-id="4eb60-250">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="4eb60-251">El modo de respuesta permitido `fragment`es.</span><span class="sxs-lookup"><span data-stu-id="4eb60-251">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="4eb60-252">`IdentityServerJwt`: Representa una API que se hospeda junto con IdentityServer.</span><span class="sxs-lookup"><span data-stu-id="4eb60-252">`IdentityServerJwt`: Represents an API that is hosted alongside with IdentityServer.</span></span>
  * <span data-ttu-id="4eb60-253">La aplicación está configurada para tener un solo ámbito que tenga como valor predeterminado el nombre de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4eb60-253">The app is configured to have a single scope that defaults to the app name.</span></span>
* <span data-ttu-id="4eb60-254">`API`: Representa una API que no está hospedada con IdentityServer.</span><span class="sxs-lookup"><span data-stu-id="4eb60-254">`API`: Represents an API that isn't hosted with IdentityServer.</span></span>
  * <span data-ttu-id="4eb60-255">La aplicación está configurada para tener un solo ámbito que tenga como valor predeterminado el nombre de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4eb60-255">The app is configured to have a single scope that defaults to the app name.</span></span>

### <a name="configuration-through-appsettings"></a><span data-ttu-id="4eb60-256">Configuración a través de AppSettings</span><span class="sxs-lookup"><span data-stu-id="4eb60-256">Configuration through AppSettings</span></span>

<span data-ttu-id="4eb60-257">Configure las aplicaciones a través del sistema de configuración agregándolas a la lista `Clients` de `Resources`o.</span><span class="sxs-lookup"><span data-stu-id="4eb60-257">Configure the apps through the configuration system by adding them to the list of `Clients` or `Resources`.</span></span>

<span data-ttu-id="4eb60-258">Configure cada propiedad `redirect_uri` y `post_logout_redirect_uri` del cliente, tal y como se muestra en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="4eb60-258">Configure each client's `redirect_uri` and `post_logout_redirect_uri` property, as shown in the following example:</span></span>

```json
"IdentityServer": {
  "Clients": {
    "MySPA": {
      "Profile": "SPA",
      "RedirectUri": "https://www.example.com/authentication/login-callback",
      "LogoutUri": "https://www.example.com/authentication/logout-callback"
    }
  }
}
```

<span data-ttu-id="4eb60-259">Al configurar recursos, puede configurar los ámbitos para el recurso, tal y como se muestra a continuación:</span><span class="sxs-lookup"><span data-stu-id="4eb60-259">When configuring resources, you can configure the scopes for the resource as shown below:</span></span>

```json
"IdentityServer": {
  "Resources": {
    "MyExternalApi": {
      "Profile": "API",
      "Scopes": "a b c"
    }
  }
}
```

### <a name="configuration-through-code"></a><span data-ttu-id="4eb60-260">Configuración mediante código</span><span class="sxs-lookup"><span data-stu-id="4eb60-260">Configuration through code</span></span>

<span data-ttu-id="4eb60-261">También puede configurar los clientes y los recursos a través del código mediante una `AddApiAuthorization` sobrecarga de que toma una acción para configurar las opciones.</span><span class="sxs-lookup"><span data-stu-id="4eb60-261">You can also configure the clients and resources through code using an overload of `AddApiAuthorization` that takes an action to configure options.</span></span>

```csharp
AddApiAuthorization<ApplicationUser, ApplicationDbContext>(options =>
{
    options.Clients.AddSPA(
        "My SPA", spa =>
        spa.WithRedirectUri("http://www.example.com/authentication/login-callback")
           .WithLogoutRedirectUri(
               "http://www.example.com/authentication/logout-callback"));

    options.ApiResources.AddApiResource("MyExternalApi", resource =>
        resource.WithScopes("a", "b", "c"));
});
```

## <a name="additional-resources"></a><span data-ttu-id="4eb60-262">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="4eb60-262">Additional resources</span></span>

* <xref:spa/angular>
* <xref:spa/react>
* <xref:security/authentication/scaffold-identity>
