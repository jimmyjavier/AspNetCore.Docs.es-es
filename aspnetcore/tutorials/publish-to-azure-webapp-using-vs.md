---
title: Publicar una aplicación de ASP.NET Core en Azure con Visual Studio
author: rick-anderson
description: Obtenga información sobre cómo publicar una aplicación de ASP.NET Core en Azure App Service con Visual Studio.
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/publish-to-azure-webapp-using-vs
ms.openlocfilehash: 634fb821ef0478dbcf57fdbb991a2e8bbb9402f1
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777090"
---
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio"></a>Publicar una aplicación de ASP.NET Core en Azure con Visual Studio

Por [Rick Anderson](https://twitter.com/RickAndMSFT)
::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

::: moniker-end


Consulte [Publicación de una aplicación web en Azure App Service mediante Visual Studio para Mac](https://docs.microsoft.com/visualstudio/mac/publish-app-svc?view=vsmac-2019) si trabaja en macOS.

Para solucionar un problema de implementación de App Service, vea <xref:test/troubleshoot-azure-iis>.

## <a name="set-up"></a>Configurar

* Abra una [cuenta gratuita de Azure](https://azure.microsoft.com/free/dotnet/) si no tiene una. 

## <a name="create-a-web-app"></a>Creación de una aplicación web

En la página de inicio de Visual Studio, seleccione **Archivo > Nuevo > Proyecto...** .

![menú Archivo](publish-to-azure-webapp-using-vs/_static/file_new_project.png)

Complete el cuadro de diálogo **Nuevo proyecto**:

* En el panel izquierdo, seleccione **.NET Core**.
* En el panel central, seleccione **Aplicación web de ASP.NET Core**.
* Seleccione **Aceptar**.

![Cuadro de diálogo Nuevo proyecto](publish-to-azure-webapp-using-vs/_static/new_prj.png)

En el cuadro de diálogo **Nueva aplicación web de ASP.NET Core**, haga lo siguiente:

* Seleccione **Aplicación web**.
* Seleccione **Cambiar autenticación**.

![Cuadro de diálogo Nuevo proyecto](publish-to-azure-webapp-using-vs/_static/new_prj_2.png)

Se mostrará el cuadro de diálogo **Cambiar autenticación**. 

* Seleccione **Cuentas de usuario individuales**.
* Seleccione **Aceptar** para volver a la **nueva aplicación web de ASP.NET Core** y vuelva a seleccionar **Aceptar**.

![Cuadro de diálogo de autenticación web de ASP.NET Core](publish-to-azure-webapp-using-vs/_static/new_prj_auth.png) 

Visual Studio crea la solución.

## <a name="run-the-app"></a>Ejecutar la aplicación

* Presione CTRL+F5 para ejecutar el proyecto.
* Pruebe los vínculos **Acerca de** y **Contacto**.

![Aplicación web abierta en Microsoft Edge en host local](publish-to-azure-webapp-using-vs/_static/show.png)

### <a name="register-a-user"></a>Registrar un usuario

* Seleccione **Registrar** y registre a un usuario nuevo. Puede usar una dirección de correo electrónico ficticia. Al enviar la información, la página mostrará el error siguiente:

    *"Error interno del servidor: Error en una operación de base de datos al procesar la solicitud. Excepción de SQL: No se puede abrir la base de datos. La aplicación de las migraciones existentes para el contexto de base de datos de la aplicación puede solucionar este problema".*
* Seleccione **Aplicar migraciones** y, una vez actualizada la página, vuelva a cargarla.

![Error interno del servidor: Error en una operación de base de datos al procesar la solicitud. Excepción de SQL: No se puede abrir la base de datos. La aplicación de las migraciones existentes para el contexto de base de datos de la aplicación puede solucionar este problema.](publish-to-azure-webapp-using-vs/_static/mig.png)

La aplicación muestra el correo electrónico usado para registrar al nuevo usuario y el vínculo **Cerrar sesión**.

![Aplicación web abierta en Microsoft Edge. El vínculo Registrar se sustituye por el texto Hello email@domain.com!](publish-to-azure-webapp-using-vs/_static/hello.png)

## <a name="deploy-the-app-to-azure"></a>Implementación de la aplicación en Azure

Desde el Explorador de soluciones, haga clic con el botón derecho en el proyecto y seleccione **Publicar...**

![Menú contextual abierto con el vínculo Publicar resaltado](publish-to-azure-webapp-using-vs/_static/pub.png)

En el cuadro de diálogo **Publicar**:

* Seleccione **Microsoft Azure App Service**.
* Seleccione el icono de engranaje y luego **Crear perfil**.
* Seleccione **Crear perfil**.

![Cuadro de diálogo Publicar](publish-to-azure-webapp-using-vs/_static/maas1.png)

### <a name="create-azure-resources"></a>Crear recursos de Azure

Aparece el cuadro de diálogo **Crear servicio de aplicaciones**:

* Especifique la suscripción.
* Se rellenan los campos de entrada **Nombre de la aplicación**, **Grupo de recursos** y **Plan de App Service**. Puede mantener estos nombres o cambiarlos.

![Cuadro de diálogo App Service](publish-to-azure-webapp-using-vs/_static/newrg1.png)

* Seleccione la pestaña **Servicios** para crear una base de datos.

* Seleccione el icono verde **+** para crear una instancia de SQL Database.

![Crear una base de datos de SQL Database](publish-to-azure-webapp-using-vs/_static/sql.png)

* Seleccione **Nuevo...** en el cuadro de diálogo **Configurar SQL Database** para crear una base de datos.

![Crear una base de datos y un servidor de SQL Database](publish-to-azure-webapp-using-vs/_static/conf.png)

Se mostrará el cuadro de diálogo **Configurar SQL Server**.

* Escriba un nombre de usuario y una contraseña de administrador y seleccione **Aceptar**. Puede conservar el **nombre de servidor** predeterminado. 

> [!NOTE]
> No se permite "admin" como nombre de usuario de administrador.

![Cuadro de diálogo Configurar SQL Server](publish-to-azure-webapp-using-vs/_static/conf_servername.png)

* Seleccione **Aceptar**.

Visual Studio volverá al cuadro de diálogo **Crear un servicio de App Service**.

* Seleccione **Crear** en el cuadro de diálogo **Crear un servicio de App Service**.

![Cuadro de diálogo Configurar base de datos SQL](publish-to-azure-webapp-using-vs/_static/conf_final.png)

Visual Studio crea la aplicación web y SQL Server en Azure. Este paso puede llevar varios minutos. Para más información sobre los recursos creados, vea [Recursos adicionales](#additional-resources).

Cuando termine la implementación, seleccione **Configuración**:

![Cuadro de diálogo Configurar SQL Server](publish-to-azure-webapp-using-vs/_static/set.png)

En la página **Configuración** del cuadro de diálogo **Publicar**, haga lo siguiente:

* Expanda **Bases de datos** y active **Usar esta cadena de conexión en tiempo de ejecución**.
* Expanda **Migraciones de Entity Framework** y active **Aplicar esta migración al publicar**.

* Seleccione **Guardar**. Visual Studio volverá al cuadro de diálogo **Publicar**. 

![Cuadro de diálogo Publicar: Panel Configuración](publish-to-azure-webapp-using-vs/_static/pubs.png)

Haga clic en **Publicar**. Visual Studio publica la aplicación en Azure. Cuando la implementación termine, la aplicación se abre en un explorador.

### <a name="test-your-app-in-azure"></a>Prueba de la aplicación en Azure

* Pruebe los vínculos **Acerca de** y **Contacto**.

* Registre un nuevo usuario.

![Aplicación web abierta en Microsoft Edge en Azure App Service](publish-to-azure-webapp-using-vs/_static/register.png)

### <a name="update-the-app"></a>Actualización de la aplicación

* Edite la página de Razor *Pages/About.cshtml* y modifique su contenido. Por ejemplo, puede editar el párrafo para que incluya el mensaje "¡Hola, ASP.NET Core!":

    [!code-html[About](publish-to-azure-webapp-using-vs/sample/about.cshtml?highlight=9&range=1-9)]

* Haga clic con el botón derecho sobre el proyecto y vuelva a seleccionar **Publicar...** .

![Menú contextual abierto con el vínculo Publicar resaltado](publish-to-azure-webapp-using-vs/_static/pub.png)

* Una vez publicada la aplicación, compruebe que los cambios realizados estén disponibles en Azure.

![Comprobar si se ha completado la tarea](publish-to-azure-webapp-using-vs/_static/final.png)

### <a name="clean-up"></a>Limpieza

Cuando haya terminado de probar la aplicación, vaya a [Azure Portal](https://portal.azure.com/) y elimínela.

* Seleccione **Grupos de recursos** y, luego, el grupo de recursos que haya creado.

![Azure Portal: Grupos de recursos en el menú lateral](publish-to-azure-webapp-using-vs/_static/portalrg.png)

* En la página **Grupos de recursos**, seleccione **Eliminar**.

![Azure Portal: página Grupos de recursos](publish-to-azure-webapp-using-vs/_static/rgd.png)

* Escriba el nombre del grupo de recursos y seleccione **Eliminar**. La aplicación y todos los demás recursos que ha creado en este tutorial se han eliminado de Azure.

### <a name="next-steps"></a>Pasos siguientes

* <xref:host-and-deploy/azure-apps/azure-continuous-deployment>

## <a name="additional-resources"></a>Recursos adicionales

* Para Visual Studio Code, vea [Publicar los perfiles](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles).
* [Azure App Service](/azure/app-service/app-service-web-overview)
* [Grupos de recursos de Azure](/azure/azure-resource-manager/resource-group-overview#resource-groups)
* [Azure SQL Database](/azure/sql-database/)
* <xref:host-and-deploy/visual-studio-publish-profiles>
* <xref:test/troubleshoot-azure-iis>
