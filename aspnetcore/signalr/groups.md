---
title: Administrar usuarios y grupos enSignalR
author: bradygaster
description: Información general de SignalR ASP.net Core la administración de usuarios y grupos.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/groups
ms.openlocfilehash: af498575899fcfa407aba9f9f49c0bfeabadc7a7
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776303"
---
# <a name="manage-users-and-groups-in-signalr"></a>Administrar usuarios y grupos enSignalR

Por [Brennan Conroy](https://github.com/BrennanConroy)

SignalRpermite que los mensajes se envíen a todas las conexiones asociadas a un usuario específico, así como a grupos de conexiones con nombre.

[Ver o descargar el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/groups/sample/) [(cómo descargarlo)](xref:index#how-to-download-a-sample)

## <a name="users-in-signalr"></a>Usuarios deSignalR

SignalRpermite enviar mensajes a todas las conexiones asociadas a un usuario específico. De forma predeterminada SignalR , usa `ClaimTypes.NameIdentifier` el objeto `ClaimsPrincipal` de la clase asociada a la conexión como identificador de usuario. Un solo usuario puede tener varias conexiones a una SignalR aplicación. Por ejemplo, un usuario podría estar conectado a su escritorio, así como a su teléfono. Cada dispositivo tiene una conexión SignalR independiente, pero todos están asociados al mismo usuario. Si se envía un mensaje al usuario, todas las conexiones asociadas a ese usuario reciben el mensaje. La `Context.UserIdentifier` propiedad del centro puede tener acceso al identificador de usuario de una conexión.

Envíe un mensaje a un usuario específico pasando el identificador de usuario a la `User` función en el método de concentrador, tal como se muestra en el ejemplo siguiente:

> [!NOTE]
> El identificador de usuario distingue entre mayúsculas y minúsculas.

[!code-csharp[Configure service](groups/sample/hubs/chathub.cs?range=29-32)]

## <a name="groups-in-signalr"></a>Grupos enSignalR

Un grupo es una colección de conexiones asociadas a un nombre. Los mensajes se pueden enviar a todas las conexiones de un grupo. Los grupos son el método recomendado para enviar a una conexión o varias conexiones, ya que la aplicación administra los grupos. Una conexión puede ser miembro de varios grupos. Esto hace que los grupos sean ideales para algo como una aplicación de chat, donde cada habitación puede representarse como un grupo. Las conexiones se pueden agregar o quitar de los grupos a `AddToGroupAsync` través `RemoveFromGroupAsync` de los métodos y.

[!code-csharp[Hub methods](groups/sample/hubs/chathub.cs?range=15-27)]

La pertenencia a grupos no se conserva cuando se vuelve a conectar una conexión. La conexión debe volver a unirse al grupo cuando se restablece. No es posible contar los miembros de un grupo, puesto que esta información no está disponible si la aplicación se escala a varios servidores.

Para proteger el acceso a los recursos al usar grupos, use la funcionalidad de [autenticación y autorización](xref:signalr/authn-and-authz) en ASP.net Core. Si solo agrega usuarios a un grupo cuando las credenciales son válidas para ese grupo, los mensajes enviados a ese grupo solo se dirigirán a los usuarios autorizados. Sin embargo, los grupos no son una característica de seguridad. Las notificaciones de autenticación tienen características que los grupos no, como la expiración y la revocación. Si se revoca el permiso de un usuario para obtener acceso al grupo, debe detectarlo manualmente y quitarlo del grupo.

> [!NOTE]
> Los nombres de grupo distinguen mayúsculas de minúsculas.

## <a name="related-resources"></a>Recursos relacionados

* [Introducción](xref:tutorials/signalr)
* [Concentradores](xref:signalr/hubs)
* [Publicación en Azure](xref:signalr/publish-to-azure-web-app)
