---
title: Migración de la autenticación de pertenencia de ASP.NET a ASP.NET Core 2,0Identity
author: isaac2004
description: Obtenga información sobre cómo migrar aplicaciones existentes de ASP.NET mediante la autenticación de Identitypertenencia a ASP.net Core 2,0.
ms.author: scaddie
ms.custom: mvc
ms.date: 01/10/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/proper-to-2x/membership-to-core-identity
ms.openlocfilehash: b5205ef69943f3744bba8381701008369dd0843c
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774514"
---
# <a name="migrate-from-aspnet-membership-authentication-to-aspnet-core-20-identity"></a><span data-ttu-id="8a3cb-103">Migración de la autenticación de pertenencia de ASP.NET a ASP.NET Core identidad 2,0</span><span class="sxs-lookup"><span data-stu-id="8a3cb-103">Migrate from ASP.NET Membership authentication to ASP.NET Core 2.0 Identity</span></span>

<span data-ttu-id="8a3cb-104">Por [Isaac Levin](https://isaaclevin.com)</span><span class="sxs-lookup"><span data-stu-id="8a3cb-104">By [Isaac Levin](https://isaaclevin.com)</span></span>

<span data-ttu-id="8a3cb-105">En este artículo se muestra cómo migrar el esquema de la base de datos para aplicaciones ASP.NET que usan la autenticación de pertenencia a ASP.NET Core identidad 2,0.</span><span class="sxs-lookup"><span data-stu-id="8a3cb-105">This article demonstrates migrating the database schema for ASP.NET apps using Membership authentication to ASP.NET Core 2.0 Identity.</span></span>

> [!NOTE]
> <span data-ttu-id="8a3cb-106">En este documento se proporcionan los pasos necesarios para migrar el esquema de la base de datos para las aplicaciones basadas en la pertenencia a ASP.NET al esquema de la base de datos usado para la identidad ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8a3cb-106">This document provides the steps needed to migrate the database schema for ASP.NET Membership-based apps to the database schema used for ASP.NET Core Identity.</span></span> <span data-ttu-id="8a3cb-107">Para obtener más información sobre la migración de la autenticación basada en pertenencia de ASP.NET a ASP.NET Identity, vea [migrar una aplicación existente de la pertenencia a SQL a ASP.net Identity](/aspnet/identity/overview/migrations/migrating-an-existing-website-from-sql-membership-to-aspnet-identity).</span><span class="sxs-lookup"><span data-stu-id="8a3cb-107">For more information about migrating from ASP.NET Membership-based authentication to ASP.NET Identity, see [Migrate an existing app from SQL Membership to ASP.NET Identity](/aspnet/identity/overview/migrations/migrating-an-existing-website-from-sql-membership-to-aspnet-identity).</span></span> <span data-ttu-id="8a3cb-108">Para obtener más información acerca de la identidad de ASP.NET Core, consulte [Introducción a la identidad en ASP.net Core](xref:security/authentication/identity).</span><span class="sxs-lookup"><span data-stu-id="8a3cb-108">For more information about ASP.NET Core Identity, see [Introduction to Identity on ASP.NET Core](xref:security/authentication/identity).</span></span>

## <a name="review-of-membership-schema"></a><span data-ttu-id="8a3cb-109">Revisión del esquema de pertenencia</span><span class="sxs-lookup"><span data-stu-id="8a3cb-109">Review of Membership schema</span></span>

<span data-ttu-id="8a3cb-110">Antes de ASP.NET 2,0, los desarrolladores tenían que crear todo el proceso de autenticación y autorización para sus aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="8a3cb-110">Prior to ASP.NET 2.0, developers were tasked with creating the entire authentication and authorization process for their apps.</span></span> <span data-ttu-id="8a3cb-111">Con ASP.NET 2,0, se presentó la pertenencia, que proporciona una solución reutilizable para controlar la seguridad en las aplicaciones de ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="8a3cb-111">With ASP.NET 2.0, Membership was introduced, providing a boilerplate solution to handling security within ASP.NET apps.</span></span> <span data-ttu-id="8a3cb-112">Los desarrolladores ahora podían arrancar un esquema en una base de datos de SQL Server con el comando [Aspnet_regsql. exe](https://msdn.microsoft.com/library/ms229862.aspx) .</span><span class="sxs-lookup"><span data-stu-id="8a3cb-112">Developers were now able to bootstrap a schema into a SQL Server database with the [aspnet_regsql.exe](https://msdn.microsoft.com/library/ms229862.aspx) command.</span></span> <span data-ttu-id="8a3cb-113">Después de ejecutar este comando, se crearon las tablas siguientes en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="8a3cb-113">After running this command, the following tables were created in the database.</span></span>

  ![Tablas de pertenencia](identity/_static/membership-tables.png)

<span data-ttu-id="8a3cb-115">Para migrar las aplicaciones existentes a ASP.NET Core identidad 2,0, los datos de estas tablas deben migrarse a las tablas que utiliza el nuevo esquema de identidad.</span><span class="sxs-lookup"><span data-stu-id="8a3cb-115">To migrate existing apps to ASP.NET Core 2.0 Identity, the data in these tables needs to be migrated to the tables used by the new Identity schema.</span></span>

## <a name="aspnet-core-identity-20-schema"></a><span data-ttu-id="8a3cb-116">ASP.NET Core esquema de identidad 2,0</span><span class="sxs-lookup"><span data-stu-id="8a3cb-116">ASP.NET Core Identity 2.0 schema</span></span>

<span data-ttu-id="8a3cb-117">ASP.NET Core 2,0 sigue el principio de [identidad](/aspnet/identity/index) introducido en ASP.net 4,5.</span><span class="sxs-lookup"><span data-stu-id="8a3cb-117">ASP.NET Core 2.0 follows the [Identity](/aspnet/identity/index) principle introduced in ASP.NET 4.5.</span></span> <span data-ttu-id="8a3cb-118">Aunque se comparte el principio, la implementación entre los marcos de trabajo es diferente, incluso entre las versiones de ASP.NET Core (consulte migración de la [autenticación y la identidad a ASP.NET Core 2,0](xref:migration/1x-to-2x/index)).</span><span class="sxs-lookup"><span data-stu-id="8a3cb-118">Though the principle is shared, the implementation between the frameworks is different, even between versions of ASP.NET Core (see [Migrate authentication and Identity to ASP.NET Core 2.0](xref:migration/1x-to-2x/index)).</span></span>

<span data-ttu-id="8a3cb-119">La manera más rápida de ver el esquema de ASP.NET Core identidad 2,0 es crear una nueva aplicación ASP.NET Core 2,0.</span><span class="sxs-lookup"><span data-stu-id="8a3cb-119">The fastest way to view the schema for ASP.NET Core 2.0 Identity is to create a new ASP.NET Core 2.0 app.</span></span> <span data-ttu-id="8a3cb-120">Siga estos pasos en Visual Studio 2017:</span><span class="sxs-lookup"><span data-stu-id="8a3cb-120">Follow these steps in Visual Studio 2017:</span></span>

1. <span data-ttu-id="8a3cb-121">Seleccione **File (Archivo)**  > **New (Nuevo)**  > **Project (Proyecto)** .</span><span class="sxs-lookup"><span data-stu-id="8a3cb-121">Select **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="8a3cb-122">Cree un nuevo proyecto de **aplicación Web de ASP.net Core** llamado *CoreIdentitySample*.</span><span class="sxs-lookup"><span data-stu-id="8a3cb-122">Create a new **ASP.NET Core Web Application** project named *CoreIdentitySample*.</span></span>
1. <span data-ttu-id="8a3cb-123">Seleccione **ASP.NET Core 2,0** en la lista desplegable y, a continuación, seleccione **aplicación web**.</span><span class="sxs-lookup"><span data-stu-id="8a3cb-123">Select **ASP.NET Core 2.0** in the dropdown and then select **Web Application**.</span></span> <span data-ttu-id="8a3cb-124">Esta plantilla genera una aplicación [Razor pages](xref:razor-pages/index) .</span><span class="sxs-lookup"><span data-stu-id="8a3cb-124">This template produces a [Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="8a3cb-125">Antes de hacer clic en **Aceptar**, haga clic en **cambiar autenticación**.</span><span class="sxs-lookup"><span data-stu-id="8a3cb-125">Before clicking **OK**, click **Change Authentication**.</span></span>
1. <span data-ttu-id="8a3cb-126">Elija **las cuentas de usuario individuales** para las plantillas de identidad.</span><span class="sxs-lookup"><span data-stu-id="8a3cb-126">Choose **Individual User Accounts** for the Identity templates.</span></span> <span data-ttu-id="8a3cb-127">Por último, haga clic en **Aceptar**y en **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="8a3cb-127">Finally, click **OK**, then **OK**.</span></span> <span data-ttu-id="8a3cb-128">Visual Studio crea un proyecto mediante la plantilla de identidad de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8a3cb-128">Visual Studio creates a project using the ASP.NET Core Identity template.</span></span>
1. <span data-ttu-id="8a3cb-129">Seleccione **herramientas** > administrador de**paquetes** > NuGet**consola del administrador** de paquetes para abrir la ventana de la consola del administrador de **paquetes** (PMC).</span><span class="sxs-lookup"><span data-stu-id="8a3cb-129">Select **Tools** > **NuGet Package Manager** > **Package Manager Console** to open the **Package Manager Console** (PMC) window.</span></span>
1. <span data-ttu-id="8a3cb-130">Vaya a la raíz del proyecto en PMC y ejecute el comando de [Entity Framework (EF) Core](/ef/core) `Update-Database` .</span><span class="sxs-lookup"><span data-stu-id="8a3cb-130">Navigate to the project root in PMC, and run the [Entity Framework (EF) Core](/ef/core) `Update-Database` command.</span></span>

    <span data-ttu-id="8a3cb-131">ASP.NET Core identidad 2,0 usa EF Core para interactuar con la base de datos que almacena los datos de autenticación.</span><span class="sxs-lookup"><span data-stu-id="8a3cb-131">ASP.NET Core 2.0 Identity uses EF Core to interact with the database storing the authentication data.</span></span> <span data-ttu-id="8a3cb-132">Para que la aplicación recién creada funcione, debe haber una base de datos para almacenar estos datos.</span><span class="sxs-lookup"><span data-stu-id="8a3cb-132">In order for the newly created app to work, there needs to be a database to store this data.</span></span> <span data-ttu-id="8a3cb-133">Después de crear una nueva aplicación, la manera más rápida de inspeccionar el esquema en un entorno de base de datos es crear la base de datos mediante [migraciones de EF Core](/ef/core/managing-schemas/migrations/).</span><span class="sxs-lookup"><span data-stu-id="8a3cb-133">After creating a new app, the fastest way to inspect the schema in a database environment is to create the database using [EF Core Migrations](/ef/core/managing-schemas/migrations/).</span></span> <span data-ttu-id="8a3cb-134">Este proceso crea una base de datos, ya sea localmente o en cualquier otro lugar, que imite ese esquema.</span><span class="sxs-lookup"><span data-stu-id="8a3cb-134">This process creates a database, either locally or elsewhere, which mimics that schema.</span></span> <span data-ttu-id="8a3cb-135">Revise la documentación anterior para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="8a3cb-135">Review the preceding documentation for more information.</span></span>

    <span data-ttu-id="8a3cb-136">EF Core comandos usan la cadena de conexión para la base de datos especificada en *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="8a3cb-136">EF Core commands use the connection string for the database specified in *appsettings.json*.</span></span> <span data-ttu-id="8a3cb-137">La siguiente cadena de conexión tiene como destino una base de datos en *localhost* denominada *asp-net-Core-Identity*.</span><span class="sxs-lookup"><span data-stu-id="8a3cb-137">The following connection string targets a database on *localhost* named *asp-net-core-identity*.</span></span> <span data-ttu-id="8a3cb-138">En esta configuración, EF Core se configura para utilizar la `DefaultConnection` cadena de conexión.</span><span class="sxs-lookup"><span data-stu-id="8a3cb-138">In this setting, EF Core is configured to use the `DefaultConnection` connection string.</span></span>

    ```json
    {
      "ConnectionStrings": {
        "DefaultConnection": "Server=localhost;Database=aspnet-core-identity;Trusted_Connection=True;MultipleActiveResultSets=true"
      }
    }
    ```

1. <span data-ttu-id="8a3cb-139">Seleccione **Ver** > **Explorador de objetos de SQL Server**.</span><span class="sxs-lookup"><span data-stu-id="8a3cb-139">Select **View** > **SQL Server Object Explorer**.</span></span> <span data-ttu-id="8a3cb-140">Expanda el nodo correspondiente al nombre de la base de `ConnectionStrings:DefaultConnection` datos especificado en la propiedad de *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="8a3cb-140">Expand the node corresponding to the database name specified in the `ConnectionStrings:DefaultConnection` property of *appsettings.json*.</span></span>

    <span data-ttu-id="8a3cb-141">El `Update-Database` comando creó la base de datos especificada con el esquema y los datos necesarios para la inicialización de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8a3cb-141">The `Update-Database` command created the database specified with the schema and any data needed for app initialization.</span></span> <span data-ttu-id="8a3cb-142">En la imagen siguiente se muestra la estructura de tabla que se crea con los pasos anteriores.</span><span class="sxs-lookup"><span data-stu-id="8a3cb-142">The following image depicts the table structure that's created with the preceding steps.</span></span>

    ![Tablas de identidad](identity/_static/identity-tables.png)

## <a name="migrate-the-schema"></a><span data-ttu-id="8a3cb-144">Migración del esquema</span><span class="sxs-lookup"><span data-stu-id="8a3cb-144">Migrate the schema</span></span>

<span data-ttu-id="8a3cb-145">Existen sutiles diferencias en las estructuras de tabla y los campos de pertenencia y ASP.NET Core identidad.</span><span class="sxs-lookup"><span data-stu-id="8a3cb-145">There are subtle differences in the table structures and fields for both Membership and ASP.NET Core Identity.</span></span> <span data-ttu-id="8a3cb-146">El patrón ha cambiado substancialmente en lo que respecta a la autenticación y autorización con aplicaciones ASP.NET y ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8a3cb-146">The pattern has changed substantially for authentication/authorization with ASP.NET and ASP.NET Core apps.</span></span> <span data-ttu-id="8a3cb-147">Los objetos de clave que se siguen usando con la identidad son *usuarios* y *roles*.</span><span class="sxs-lookup"><span data-stu-id="8a3cb-147">The key objects that are still used with Identity are *Users* and *Roles*.</span></span> <span data-ttu-id="8a3cb-148">Aquí se muestran las tablas de asignación de *usuarios*, *roles*y *UserRoles*.</span><span class="sxs-lookup"><span data-stu-id="8a3cb-148">Here are mapping tables for *Users*, *Roles*, and *UserRoles*.</span></span>

### <a name="users"></a><span data-ttu-id="8a3cb-149">Usuarios</span><span class="sxs-lookup"><span data-stu-id="8a3cb-149">Users</span></span>

|<span data-ttu-id="8a3cb-150">*Identidad<br>(DBO. AspNetUsers*</span><span class="sxs-lookup"><span data-stu-id="8a3cb-150">*Identity<br>(dbo.AspNetUsers)*</span></span>        ||<span data-ttu-id="8a3cb-151">*Pertenencia<br>(dbo. aspnet_Users/dbo. aspnet_Membership)*</span><span class="sxs-lookup"><span data-stu-id="8a3cb-151">*Membership<br>(dbo.aspnet_Users / dbo.aspnet_Membership)*</span></span>||
|----------------------------------------|-----------------------------------------------------------|
|<span data-ttu-id="8a3cb-152">**Nombre del campo**</span><span class="sxs-lookup"><span data-stu-id="8a3cb-152">**Field Name**</span></span>                 |<span data-ttu-id="8a3cb-153">**Tipo**</span><span class="sxs-lookup"><span data-stu-id="8a3cb-153">**Type**</span></span>|<span data-ttu-id="8a3cb-154">**Nombre del campo**</span><span class="sxs-lookup"><span data-stu-id="8a3cb-154">**Field Name**</span></span>                                    |<span data-ttu-id="8a3cb-155">**Tipo**</span><span class="sxs-lookup"><span data-stu-id="8a3cb-155">**Type**</span></span>|
|`Id`                           |<span data-ttu-id="8a3cb-156">string</span><span class="sxs-lookup"><span data-stu-id="8a3cb-156">string</span></span>  |`aspnet_Users.UserId`                             |<span data-ttu-id="8a3cb-157">string</span><span class="sxs-lookup"><span data-stu-id="8a3cb-157">string</span></span>  |
|`UserName`                     |<span data-ttu-id="8a3cb-158">string</span><span class="sxs-lookup"><span data-stu-id="8a3cb-158">string</span></span>  |`aspnet_Users.UserName`                           |<span data-ttu-id="8a3cb-159">string</span><span class="sxs-lookup"><span data-stu-id="8a3cb-159">string</span></span>  |
|`Email`                        |<span data-ttu-id="8a3cb-160">string</span><span class="sxs-lookup"><span data-stu-id="8a3cb-160">string</span></span>  |`aspnet_Membership.Email`                         |<span data-ttu-id="8a3cb-161">string</span><span class="sxs-lookup"><span data-stu-id="8a3cb-161">string</span></span>  |
|`NormalizedUserName`           |<span data-ttu-id="8a3cb-162">string</span><span class="sxs-lookup"><span data-stu-id="8a3cb-162">string</span></span>  |`aspnet_Users.LoweredUserName`                    |<span data-ttu-id="8a3cb-163">string</span><span class="sxs-lookup"><span data-stu-id="8a3cb-163">string</span></span>  |
|`NormalizedEmail`              |<span data-ttu-id="8a3cb-164">string</span><span class="sxs-lookup"><span data-stu-id="8a3cb-164">string</span></span>  |`aspnet_Membership.LoweredEmail`                  |<span data-ttu-id="8a3cb-165">string</span><span class="sxs-lookup"><span data-stu-id="8a3cb-165">string</span></span>  |
|`PhoneNumber`                  |<span data-ttu-id="8a3cb-166">string</span><span class="sxs-lookup"><span data-stu-id="8a3cb-166">string</span></span>  |`aspnet_Users.MobileAlias`                        |<span data-ttu-id="8a3cb-167">string</span><span class="sxs-lookup"><span data-stu-id="8a3cb-167">string</span></span>  |
|`LockoutEnabled`               |<span data-ttu-id="8a3cb-168">bit</span><span class="sxs-lookup"><span data-stu-id="8a3cb-168">bit</span></span>     |`aspnet_Membership.IsLockedOut`                   |<span data-ttu-id="8a3cb-169">bit</span><span class="sxs-lookup"><span data-stu-id="8a3cb-169">bit</span></span>     |

> [!NOTE]
> <span data-ttu-id="8a3cb-170">No todas las asignaciones de campos se parecen a las relaciones uno a uno de la pertenencia a ASP.NET Core identidad.</span><span class="sxs-lookup"><span data-stu-id="8a3cb-170">Not all the field mappings resemble one-to-one relationships from Membership to ASP.NET Core Identity.</span></span> <span data-ttu-id="8a3cb-171">La tabla anterior toma el esquema de usuario de pertenencia predeterminado y lo asigna al esquema de identidad de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8a3cb-171">The preceding table takes the default Membership User schema and maps it to the ASP.NET Core Identity schema.</span></span> <span data-ttu-id="8a3cb-172">Cualquier otro campo personalizado que se usara para la pertenencia debe asignarse manualmente.</span><span class="sxs-lookup"><span data-stu-id="8a3cb-172">Any other custom fields that were used for Membership need to be mapped manually.</span></span> <span data-ttu-id="8a3cb-173">En esta asignación, no hay ninguna asignación para las contraseñas, ya que los criterios de contraseña y las sales de contraseña no se migran entre los dos.</span><span class="sxs-lookup"><span data-stu-id="8a3cb-173">In this mapping, there's no map for passwords, as both password criteria and password salts don't migrate between the two.</span></span> <span data-ttu-id="8a3cb-174">**Se recomienda dejar la contraseña como NULL y pedir a los usuarios que restablezcan sus contraseñas.**</span><span class="sxs-lookup"><span data-stu-id="8a3cb-174">**It's recommended to leave the password as null and to ask users to reset their passwords.**</span></span> <span data-ttu-id="8a3cb-175">En ASP.NET Core identidad, `LockoutEnd` debe establecerse en una fecha futura si el usuario está bloqueado. Esto se muestra en el script de migración.</span><span class="sxs-lookup"><span data-stu-id="8a3cb-175">In ASP.NET Core Identity, `LockoutEnd` should be set to some date in the future if the user is locked out. This is shown in the migration script.</span></span>

### <a name="roles"></a><span data-ttu-id="8a3cb-176">Roles</span><span class="sxs-lookup"><span data-stu-id="8a3cb-176">Roles</span></span>

|<span data-ttu-id="8a3cb-177">*Identidad<br>(DBO. AspNetRoles)*</span><span class="sxs-lookup"><span data-stu-id="8a3cb-177">*Identity<br>(dbo.AspNetRoles)*</span></span>        ||<span data-ttu-id="8a3cb-178">*Pertenencia<br>(dbo. aspnet_Roles)*</span><span class="sxs-lookup"><span data-stu-id="8a3cb-178">*Membership<br>(dbo.aspnet_Roles)*</span></span>||
|----------------------------------------|-----------------------------------|
|<span data-ttu-id="8a3cb-179">**Nombre del campo**</span><span class="sxs-lookup"><span data-stu-id="8a3cb-179">**Field Name**</span></span>                 |<span data-ttu-id="8a3cb-180">**Tipo**</span><span class="sxs-lookup"><span data-stu-id="8a3cb-180">**Type**</span></span>|<span data-ttu-id="8a3cb-181">**Nombre del campo**</span><span class="sxs-lookup"><span data-stu-id="8a3cb-181">**Field Name**</span></span>   |<span data-ttu-id="8a3cb-182">**Tipo**</span><span class="sxs-lookup"><span data-stu-id="8a3cb-182">**Type**</span></span>         |
|`Id`                           |<span data-ttu-id="8a3cb-183">string</span><span class="sxs-lookup"><span data-stu-id="8a3cb-183">string</span></span>  |`RoleId`         | <span data-ttu-id="8a3cb-184">string</span><span class="sxs-lookup"><span data-stu-id="8a3cb-184">string</span></span>          |
|`Name`                         |<span data-ttu-id="8a3cb-185">string</span><span class="sxs-lookup"><span data-stu-id="8a3cb-185">string</span></span>  |`RoleName`       | <span data-ttu-id="8a3cb-186">string</span><span class="sxs-lookup"><span data-stu-id="8a3cb-186">string</span></span>          |
|`NormalizedName`               |<span data-ttu-id="8a3cb-187">string</span><span class="sxs-lookup"><span data-stu-id="8a3cb-187">string</span></span>  |`LoweredRoleName`| <span data-ttu-id="8a3cb-188">string</span><span class="sxs-lookup"><span data-stu-id="8a3cb-188">string</span></span>          |

### <a name="user-roles"></a><span data-ttu-id="8a3cb-189">Roles de usuario</span><span class="sxs-lookup"><span data-stu-id="8a3cb-189">User Roles</span></span>

|<span data-ttu-id="8a3cb-190">*Identidad<br>(DBO. AspNetUserRoles*</span><span class="sxs-lookup"><span data-stu-id="8a3cb-190">*Identity<br>(dbo.AspNetUserRoles)*</span></span>||<span data-ttu-id="8a3cb-191">*Pertenencia<br>(dbo. aspnet_UsersInRoles)*</span><span class="sxs-lookup"><span data-stu-id="8a3cb-191">*Membership<br>(dbo.aspnet_UsersInRoles)*</span></span>||
|------------------------------------|------------------------------------------|
|<span data-ttu-id="8a3cb-192">**Nombre del campo**</span><span class="sxs-lookup"><span data-stu-id="8a3cb-192">**Field Name**</span></span>           |<span data-ttu-id="8a3cb-193">**Tipo**</span><span class="sxs-lookup"><span data-stu-id="8a3cb-193">**Type**</span></span>  |<span data-ttu-id="8a3cb-194">**Nombre del campo**</span><span class="sxs-lookup"><span data-stu-id="8a3cb-194">**Field Name**</span></span>|<span data-ttu-id="8a3cb-195">**Tipo**</span><span class="sxs-lookup"><span data-stu-id="8a3cb-195">**Type**</span></span>                   |
|`RoleId`                 |<span data-ttu-id="8a3cb-196">string</span><span class="sxs-lookup"><span data-stu-id="8a3cb-196">string</span></span>    |`RoleId`      |<span data-ttu-id="8a3cb-197">string</span><span class="sxs-lookup"><span data-stu-id="8a3cb-197">string</span></span>                     |
|`UserId`                 |<span data-ttu-id="8a3cb-198">string</span><span class="sxs-lookup"><span data-stu-id="8a3cb-198">string</span></span>    |`UserId`      |<span data-ttu-id="8a3cb-199">string</span><span class="sxs-lookup"><span data-stu-id="8a3cb-199">string</span></span>                     |

<span data-ttu-id="8a3cb-200">Haga referencia a las tablas de asignación anteriores al crear un script de migración para *usuarios* y *roles*.</span><span class="sxs-lookup"><span data-stu-id="8a3cb-200">Reference the preceding mapping tables when creating a migration script for *Users* and *Roles*.</span></span> <span data-ttu-id="8a3cb-201">En el ejemplo siguiente se da por supuesto que tiene dos bases de datos en un servidor de base de datos.</span><span class="sxs-lookup"><span data-stu-id="8a3cb-201">The following example assumes you have two databases on a database server.</span></span> <span data-ttu-id="8a3cb-202">Una base de datos contiene los datos y el esquema de pertenencia de ASP.NET existente.</span><span class="sxs-lookup"><span data-stu-id="8a3cb-202">One database contains the existing ASP.NET Membership schema and data.</span></span> <span data-ttu-id="8a3cb-203">La otra base de datos *CoreIdentitySample* se creó con los pasos descritos anteriormente.</span><span class="sxs-lookup"><span data-stu-id="8a3cb-203">The other *CoreIdentitySample* database was created using steps described earlier.</span></span> <span data-ttu-id="8a3cb-204">Los comentarios se incluyen en línea para obtener más detalles.</span><span class="sxs-lookup"><span data-stu-id="8a3cb-204">Comments are included inline for more details.</span></span>

```sql
-- THIS SCRIPT NEEDS TO RUN FROM THE CONTEXT OF THE MEMBERSHIP DB
BEGIN TRANSACTION MigrateUsersAndRoles
USE aspnetdb

-- INSERT USERS
INSERT INTO CoreIdentitySample.dbo.AspNetUsers
            (Id,
             UserName,
             NormalizedUserName,
             PasswordHash,
             SecurityStamp,
             EmailConfirmed,
             PhoneNumber,
             PhoneNumberConfirmed,
             TwoFactorEnabled,
             LockoutEnd,
             LockoutEnabled,
             AccessFailedCount,
             Email,
             NormalizedEmail)
SELECT aspnet_Users.UserId,
       aspnet_Users.UserName,
       -- The NormalizedUserName value is upper case in ASP.NET Core Identity
       UPPER(aspnet_Users.UserName),
       -- Creates an empty password since passwords don't map between the 2 schemas
       '',
       /*
        The SecurityStamp token is used to verify the state of an account and
        is subject to change at any time. It should be initialized as a new ID.
       */
       NewID(),
       /*
        EmailConfirmed is set when a new user is created and confirmed via email.
        Users must have this set during migration to reset passwords.
       */
       1,
       aspnet_Users.MobileAlias,
       CASE
         WHEN aspnet_Users.MobileAlias IS NULL THEN 0
         ELSE 1
       END,
       -- 2FA likely wasn't setup in Membership for users, so setting as false.
       0,
       CASE
         -- Setting lockout date to time in the future (1,000 years)
         WHEN aspnet_Membership.IsLockedOut = 1 THEN Dateadd(year, 1000,
                                                     Sysutcdatetime())
         ELSE NULL
       END,
       aspnet_Membership.IsLockedOut,
       /*
        AccessFailedAccount is used to track failed logins. This is stored in
        Membership in multiple columns. Setting to 0 arbitrarily.
       */
       0,
       aspnet_Membership.Email,
       -- The NormalizedEmail value is upper case in ASP.NET Core Identity
       UPPER(aspnet_Membership.Email)
FROM   aspnet_Users
       LEFT OUTER JOIN aspnet_Membership
                    ON aspnet_Membership.ApplicationId =
                       aspnet_Users.ApplicationId
                       AND aspnet_Users.UserId = aspnet_Membership.UserId
       LEFT OUTER JOIN CoreIdentitySample.dbo.AspNetUsers
                    ON aspnet_Membership.UserId = AspNetUsers.Id
WHERE  AspNetUsers.Id IS NULL

-- INSERT ROLES
INSERT INTO CoreIdentitySample.dbo.AspNetRoles(Id, Name)
SELECT RoleId, RoleName
FROM aspnet_Roles;

-- INSERT USER ROLES
INSERT INTO CoreIdentitySample.dbo.AspNetUserRoles(UserId, RoleId)
SELECT UserId, RoleId
FROM aspnet_UsersInRoles;

IF @@ERROR <> 0
  BEGIN
    ROLLBACK TRANSACTION MigrateUsersAndRoles
    RETURN
  END

COMMIT TRANSACTION MigrateUsersAndRoles
```

<span data-ttu-id="8a3cb-205">Después de completar el script anterior, la aplicación Identity ASP.net Core creada anteriormente se rellena con usuarios de pertenencia.</span><span class="sxs-lookup"><span data-stu-id="8a3cb-205">After completion of the preceding script, the ASP.NET Core Identity app created earlier is populated with Membership users.</span></span> <span data-ttu-id="8a3cb-206">Los usuarios deben cambiar sus contraseñas antes de iniciar sesión.</span><span class="sxs-lookup"><span data-stu-id="8a3cb-206">Users need to change their passwords before logging in.</span></span>

> [!NOTE]
> <span data-ttu-id="8a3cb-207">Si el sistema de pertenencia tuviera usuarios con nombres de usuario que no coincidían con su dirección de correo electrónico, se requieren cambios en la aplicación que se creó anteriormente para dar cabida a este.</span><span class="sxs-lookup"><span data-stu-id="8a3cb-207">If the Membership system had users with user names that didn't match their email address, changes are required to the app created earlier to accommodate this.</span></span> <span data-ttu-id="8a3cb-208">La plantilla predeterminada espera `UserName` y `Email` para ser la misma.</span><span class="sxs-lookup"><span data-stu-id="8a3cb-208">The default template expects `UserName` and `Email` to be the same.</span></span> <span data-ttu-id="8a3cb-209">En situaciones en las que son diferentes, el proceso de inicio de sesión debe modificarse `UserName` para usar en `Email`lugar de.</span><span class="sxs-lookup"><span data-stu-id="8a3cb-209">For situations in which they're different, the login process needs to be modified to use `UserName` instead of `Email`.</span></span>

<span data-ttu-id="8a3cb-210">En la `PageModel` página de inicio de sesión, que se encuentra en *Pages\Account\Login.cshtml.CS*, quite el `[EmailAddress]` atributo de la propiedad *email* .</span><span class="sxs-lookup"><span data-stu-id="8a3cb-210">In the `PageModel` of the Login Page, located at *Pages\Account\Login.cshtml.cs*, remove the `[EmailAddress]` attribute from the *Email* property.</span></span> <span data-ttu-id="8a3cb-211">Cambie el nombre por *nombre de usuario*.</span><span class="sxs-lookup"><span data-stu-id="8a3cb-211">Rename it to *UserName*.</span></span> <span data-ttu-id="8a3cb-212">Esto requiere un cambio siempre `EmailAddress` que se mencione, en la *vista* y *PageModel*.</span><span class="sxs-lookup"><span data-stu-id="8a3cb-212">This requires a change wherever `EmailAddress` is mentioned, in the *View* and *PageModel*.</span></span> <span data-ttu-id="8a3cb-213">El resultado será similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="8a3cb-213">The result looks like the following:</span></span>

 ![Inicio de sesión fijo](identity/_static/fixed-login.png)

## <a name="next-steps"></a><span data-ttu-id="8a3cb-215">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="8a3cb-215">Next steps</span></span>

<span data-ttu-id="8a3cb-216">En este tutorial, ha aprendido cómo trasladar a los usuarios de la pertenencia Identityde SQL a ASP.net Core 2,0.</span><span class="sxs-lookup"><span data-stu-id="8a3cb-216">In this tutorial, you learned how to port users from SQL membership to ASP.NET Core 2.0 Identity.</span></span> <span data-ttu-id="8a3cb-217">Para obtener más información sobre IdentityASP.net Core, vea [Introducción Identitya ](xref:security/authentication/identity).</span><span class="sxs-lookup"><span data-stu-id="8a3cb-217">For more information regarding ASP.NET Core Identity, see [Introduction to Identity](xref:security/authentication/identity).</span></span>
