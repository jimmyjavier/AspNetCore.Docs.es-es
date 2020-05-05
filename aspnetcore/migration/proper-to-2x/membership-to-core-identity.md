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
# <a name="migrate-from-aspnet-membership-authentication-to-aspnet-core-20-identity"></a>Migración de la autenticación de pertenencia de ASP.NET a ASP.NET Core identidad 2,0

Por [Isaac Levin](https://isaaclevin.com)

En este artículo se muestra cómo migrar el esquema de la base de datos para aplicaciones ASP.NET que usan la autenticación de pertenencia a ASP.NET Core identidad 2,0.

> [!NOTE]
> En este documento se proporcionan los pasos necesarios para migrar el esquema de la base de datos para las aplicaciones basadas en la pertenencia a ASP.NET al esquema de la base de datos usado para la identidad ASP.NET Core. Para obtener más información sobre la migración de la autenticación basada en pertenencia de ASP.NET a ASP.NET Identity, vea [migrar una aplicación existente de la pertenencia a SQL a ASP.net Identity](/aspnet/identity/overview/migrations/migrating-an-existing-website-from-sql-membership-to-aspnet-identity). Para obtener más información acerca de la identidad de ASP.NET Core, consulte [Introducción a la identidad en ASP.net Core](xref:security/authentication/identity).

## <a name="review-of-membership-schema"></a>Revisión del esquema de pertenencia

Antes de ASP.NET 2,0, los desarrolladores tenían que crear todo el proceso de autenticación y autorización para sus aplicaciones. Con ASP.NET 2,0, se presentó la pertenencia, que proporciona una solución reutilizable para controlar la seguridad en las aplicaciones de ASP.NET. Los desarrolladores ahora podían arrancar un esquema en una base de datos de SQL Server con el comando [Aspnet_regsql. exe](https://msdn.microsoft.com/library/ms229862.aspx) . Después de ejecutar este comando, se crearon las tablas siguientes en la base de datos.

  ![Tablas de pertenencia](identity/_static/membership-tables.png)

Para migrar las aplicaciones existentes a ASP.NET Core identidad 2,0, los datos de estas tablas deben migrarse a las tablas que utiliza el nuevo esquema de identidad.

## <a name="aspnet-core-identity-20-schema"></a>ASP.NET Core esquema de identidad 2,0

ASP.NET Core 2,0 sigue el principio de [identidad](/aspnet/identity/index) introducido en ASP.net 4,5. Aunque se comparte el principio, la implementación entre los marcos de trabajo es diferente, incluso entre las versiones de ASP.NET Core (consulte migración de la [autenticación y la identidad a ASP.NET Core 2,0](xref:migration/1x-to-2x/index)).

La manera más rápida de ver el esquema de ASP.NET Core identidad 2,0 es crear una nueva aplicación ASP.NET Core 2,0. Siga estos pasos en Visual Studio 2017:

1. Seleccione **File (Archivo)**  > **New (Nuevo)**  > **Project (Proyecto)** .
1. Cree un nuevo proyecto de **aplicación Web de ASP.net Core** llamado *CoreIdentitySample*.
1. Seleccione **ASP.NET Core 2,0** en la lista desplegable y, a continuación, seleccione **aplicación web**. Esta plantilla genera una aplicación [Razor pages](xref:razor-pages/index) . Antes de hacer clic en **Aceptar**, haga clic en **cambiar autenticación**.
1. Elija **las cuentas de usuario individuales** para las plantillas de identidad. Por último, haga clic en **Aceptar**y en **Aceptar**. Visual Studio crea un proyecto mediante la plantilla de identidad de ASP.NET Core.
1. Seleccione **herramientas** > administrador de**paquetes** > NuGet**consola del administrador** de paquetes para abrir la ventana de la consola del administrador de **paquetes** (PMC).
1. Vaya a la raíz del proyecto en PMC y ejecute el comando de [Entity Framework (EF) Core](/ef/core) `Update-Database` .

    ASP.NET Core identidad 2,0 usa EF Core para interactuar con la base de datos que almacena los datos de autenticación. Para que la aplicación recién creada funcione, debe haber una base de datos para almacenar estos datos. Después de crear una nueva aplicación, la manera más rápida de inspeccionar el esquema en un entorno de base de datos es crear la base de datos mediante [migraciones de EF Core](/ef/core/managing-schemas/migrations/). Este proceso crea una base de datos, ya sea localmente o en cualquier otro lugar, que imite ese esquema. Revise la documentación anterior para obtener más información.

    EF Core comandos usan la cadena de conexión para la base de datos especificada en *appSettings. JSON*. La siguiente cadena de conexión tiene como destino una base de datos en *localhost* denominada *asp-net-Core-Identity*. En esta configuración, EF Core se configura para utilizar la `DefaultConnection` cadena de conexión.

    ```json
    {
      "ConnectionStrings": {
        "DefaultConnection": "Server=localhost;Database=aspnet-core-identity;Trusted_Connection=True;MultipleActiveResultSets=true"
      }
    }
    ```

1. Seleccione **Ver** > **Explorador de objetos de SQL Server**. Expanda el nodo correspondiente al nombre de la base de `ConnectionStrings:DefaultConnection` datos especificado en la propiedad de *appSettings. JSON*.

    El `Update-Database` comando creó la base de datos especificada con el esquema y los datos necesarios para la inicialización de la aplicación. En la imagen siguiente se muestra la estructura de tabla que se crea con los pasos anteriores.

    ![Tablas de identidad](identity/_static/identity-tables.png)

## <a name="migrate-the-schema"></a>Migración del esquema

Existen sutiles diferencias en las estructuras de tabla y los campos de pertenencia y ASP.NET Core identidad. El patrón ha cambiado substancialmente en lo que respecta a la autenticación y autorización con aplicaciones ASP.NET y ASP.NET Core. Los objetos de clave que se siguen usando con la identidad son *usuarios* y *roles*. Aquí se muestran las tablas de asignación de *usuarios*, *roles*y *UserRoles*.

### <a name="users"></a>Usuarios

|*Identidad<br>(DBO. AspNetUsers*        ||*Pertenencia<br>(dbo. aspnet_Users/dbo. aspnet_Membership)*||
|----------------------------------------|-----------------------------------------------------------|
|**Nombre del campo**                 |**Tipo**|**Nombre del campo**                                    |**Tipo**|
|`Id`                           |string  |`aspnet_Users.UserId`                             |string  |
|`UserName`                     |string  |`aspnet_Users.UserName`                           |string  |
|`Email`                        |string  |`aspnet_Membership.Email`                         |string  |
|`NormalizedUserName`           |string  |`aspnet_Users.LoweredUserName`                    |string  |
|`NormalizedEmail`              |string  |`aspnet_Membership.LoweredEmail`                  |string  |
|`PhoneNumber`                  |string  |`aspnet_Users.MobileAlias`                        |string  |
|`LockoutEnabled`               |bit     |`aspnet_Membership.IsLockedOut`                   |bit     |

> [!NOTE]
> No todas las asignaciones de campos se parecen a las relaciones uno a uno de la pertenencia a ASP.NET Core identidad. La tabla anterior toma el esquema de usuario de pertenencia predeterminado y lo asigna al esquema de identidad de ASP.NET Core. Cualquier otro campo personalizado que se usara para la pertenencia debe asignarse manualmente. En esta asignación, no hay ninguna asignación para las contraseñas, ya que los criterios de contraseña y las sales de contraseña no se migran entre los dos. **Se recomienda dejar la contraseña como NULL y pedir a los usuarios que restablezcan sus contraseñas.** En ASP.NET Core identidad, `LockoutEnd` debe establecerse en una fecha futura si el usuario está bloqueado. Esto se muestra en el script de migración.

### <a name="roles"></a>Roles

|*Identidad<br>(DBO. AspNetRoles)*        ||*Pertenencia<br>(dbo. aspnet_Roles)*||
|----------------------------------------|-----------------------------------|
|**Nombre del campo**                 |**Tipo**|**Nombre del campo**   |**Tipo**         |
|`Id`                           |string  |`RoleId`         | string          |
|`Name`                         |string  |`RoleName`       | string          |
|`NormalizedName`               |string  |`LoweredRoleName`| string          |

### <a name="user-roles"></a>Roles de usuario

|*Identidad<br>(DBO. AspNetUserRoles*||*Pertenencia<br>(dbo. aspnet_UsersInRoles)*||
|------------------------------------|------------------------------------------|
|**Nombre del campo**           |**Tipo**  |**Nombre del campo**|**Tipo**                   |
|`RoleId`                 |string    |`RoleId`      |string                     |
|`UserId`                 |string    |`UserId`      |string                     |

Haga referencia a las tablas de asignación anteriores al crear un script de migración para *usuarios* y *roles*. En el ejemplo siguiente se da por supuesto que tiene dos bases de datos en un servidor de base de datos. Una base de datos contiene los datos y el esquema de pertenencia de ASP.NET existente. La otra base de datos *CoreIdentitySample* se creó con los pasos descritos anteriormente. Los comentarios se incluyen en línea para obtener más detalles.

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

Después de completar el script anterior, la aplicación Identity ASP.net Core creada anteriormente se rellena con usuarios de pertenencia. Los usuarios deben cambiar sus contraseñas antes de iniciar sesión.

> [!NOTE]
> Si el sistema de pertenencia tuviera usuarios con nombres de usuario que no coincidían con su dirección de correo electrónico, se requieren cambios en la aplicación que se creó anteriormente para dar cabida a este. La plantilla predeterminada espera `UserName` y `Email` para ser la misma. En situaciones en las que son diferentes, el proceso de inicio de sesión debe modificarse `UserName` para usar en `Email`lugar de.

En la `PageModel` página de inicio de sesión, que se encuentra en *Pages\Account\Login.cshtml.CS*, quite el `[EmailAddress]` atributo de la propiedad *email* . Cambie el nombre por *nombre de usuario*. Esto requiere un cambio siempre `EmailAddress` que se mencione, en la *vista* y *PageModel*. El resultado será similar al siguiente:

 ![Inicio de sesión fijo](identity/_static/fixed-login.png)

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido cómo trasladar a los usuarios de la pertenencia Identityde SQL a ASP.net Core 2,0. Para obtener más información sobre IdentityASP.net Core, vea [Introducción Identitya ](xref:security/authentication/identity).
