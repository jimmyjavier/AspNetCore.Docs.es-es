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
# <a name="manage-users-and-groups-in-signalr"></a><span data-ttu-id="621a2-103">Administrar usuarios y grupos enSignalR</span><span class="sxs-lookup"><span data-stu-id="621a2-103">Manage users and groups in SignalR</span></span>

<span data-ttu-id="621a2-104">Por [Brennan Conroy](https://github.com/BrennanConroy)</span><span class="sxs-lookup"><span data-stu-id="621a2-104">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

SignalR<span data-ttu-id="621a2-105">permite que los mensajes se envíen a todas las conexiones asociadas a un usuario específico, así como a grupos de conexiones con nombre.</span><span class="sxs-lookup"><span data-stu-id="621a2-105"> allows messages to be sent to all connections associated with a specific user, as well as to named groups of connections.</span></span>

<span data-ttu-id="621a2-106">[Ver o descargar el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/groups/sample/) [(cómo descargarlo)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="621a2-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/groups/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="users-in-signalr"></a><span data-ttu-id="621a2-107">Usuarios deSignalR</span><span class="sxs-lookup"><span data-stu-id="621a2-107">Users in SignalR</span></span>

SignalR<span data-ttu-id="621a2-108">permite enviar mensajes a todas las conexiones asociadas a un usuario específico.</span><span class="sxs-lookup"><span data-stu-id="621a2-108"> allows you to send messages to all connections associated with a specific user.</span></span> <span data-ttu-id="621a2-109">De forma predeterminada SignalR , usa `ClaimTypes.NameIdentifier` el objeto `ClaimsPrincipal` de la clase asociada a la conexión como identificador de usuario.</span><span class="sxs-lookup"><span data-stu-id="621a2-109">By default, SignalR uses the `ClaimTypes.NameIdentifier` from the `ClaimsPrincipal` associated with the connection as the user identifier.</span></span> <span data-ttu-id="621a2-110">Un solo usuario puede tener varias conexiones a una SignalR aplicación.</span><span class="sxs-lookup"><span data-stu-id="621a2-110">A single user can have multiple connections to a SignalR app.</span></span> <span data-ttu-id="621a2-111">Por ejemplo, un usuario podría estar conectado a su escritorio, así como a su teléfono.</span><span class="sxs-lookup"><span data-stu-id="621a2-111">For example, a user could be connected on their desktop as well as their phone.</span></span> <span data-ttu-id="621a2-112">Cada dispositivo tiene una conexión SignalR independiente, pero todos están asociados al mismo usuario.</span><span class="sxs-lookup"><span data-stu-id="621a2-112">Each device has a separate SignalR connection, but they're all associated with the same user.</span></span> <span data-ttu-id="621a2-113">Si se envía un mensaje al usuario, todas las conexiones asociadas a ese usuario reciben el mensaje.</span><span class="sxs-lookup"><span data-stu-id="621a2-113">If a message is sent to the user, all of the connections associated with that user receive the message.</span></span> <span data-ttu-id="621a2-114">La `Context.UserIdentifier` propiedad del centro puede tener acceso al identificador de usuario de una conexión.</span><span class="sxs-lookup"><span data-stu-id="621a2-114">The user identifier for a connection can be accessed by the `Context.UserIdentifier` property in your hub.</span></span>

<span data-ttu-id="621a2-115">Envíe un mensaje a un usuario específico pasando el identificador de usuario a la `User` función en el método de concentrador, tal como se muestra en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="621a2-115">Send a message to a specific user by passing the user identifier to the `User` function in your hub method as shown in the following example:</span></span>

> [!NOTE]
> <span data-ttu-id="621a2-116">El identificador de usuario distingue entre mayúsculas y minúsculas.</span><span class="sxs-lookup"><span data-stu-id="621a2-116">The user identifier is case-sensitive.</span></span>

[!code-csharp[Configure service](groups/sample/hubs/chathub.cs?range=29-32)]

## <a name="groups-in-signalr"></a><span data-ttu-id="621a2-117">Grupos enSignalR</span><span class="sxs-lookup"><span data-stu-id="621a2-117">Groups in SignalR</span></span>

<span data-ttu-id="621a2-118">Un grupo es una colección de conexiones asociadas a un nombre.</span><span class="sxs-lookup"><span data-stu-id="621a2-118">A group is a collection of connections associated with a name.</span></span> <span data-ttu-id="621a2-119">Los mensajes se pueden enviar a todas las conexiones de un grupo.</span><span class="sxs-lookup"><span data-stu-id="621a2-119">Messages can be sent to all connections in a group.</span></span> <span data-ttu-id="621a2-120">Los grupos son el método recomendado para enviar a una conexión o varias conexiones, ya que la aplicación administra los grupos.</span><span class="sxs-lookup"><span data-stu-id="621a2-120">Groups are the recommended way to send to a connection or multiple connections because the groups are managed by the application.</span></span> <span data-ttu-id="621a2-121">Una conexión puede ser miembro de varios grupos.</span><span class="sxs-lookup"><span data-stu-id="621a2-121">A connection can be a member of multiple groups.</span></span> <span data-ttu-id="621a2-122">Esto hace que los grupos sean ideales para algo como una aplicación de chat, donde cada habitación puede representarse como un grupo.</span><span class="sxs-lookup"><span data-stu-id="621a2-122">This makes groups ideal for something like a chat application, where each room can be represented as a group.</span></span> <span data-ttu-id="621a2-123">Las conexiones se pueden agregar o quitar de los grupos a `AddToGroupAsync` través `RemoveFromGroupAsync` de los métodos y.</span><span class="sxs-lookup"><span data-stu-id="621a2-123">Connections can be added to or removed from groups via the `AddToGroupAsync` and `RemoveFromGroupAsync` methods.</span></span>

[!code-csharp[Hub methods](groups/sample/hubs/chathub.cs?range=15-27)]

<span data-ttu-id="621a2-124">La pertenencia a grupos no se conserva cuando se vuelve a conectar una conexión.</span><span class="sxs-lookup"><span data-stu-id="621a2-124">Group membership isn't preserved when a connection reconnects.</span></span> <span data-ttu-id="621a2-125">La conexión debe volver a unirse al grupo cuando se restablece.</span><span class="sxs-lookup"><span data-stu-id="621a2-125">The connection needs to rejoin the group when it's re-established.</span></span> <span data-ttu-id="621a2-126">No es posible contar los miembros de un grupo, puesto que esta información no está disponible si la aplicación se escala a varios servidores.</span><span class="sxs-lookup"><span data-stu-id="621a2-126">It's not possible to count the members of a group, since this information is not available if the application is scaled to multiple servers.</span></span>

<span data-ttu-id="621a2-127">Para proteger el acceso a los recursos al usar grupos, use la funcionalidad de [autenticación y autorización](xref:signalr/authn-and-authz) en ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="621a2-127">To protect access to resources while using groups, use [authentication and authorization](xref:signalr/authn-and-authz) functionality in ASP.NET Core.</span></span> <span data-ttu-id="621a2-128">Si solo agrega usuarios a un grupo cuando las credenciales son válidas para ese grupo, los mensajes enviados a ese grupo solo se dirigirán a los usuarios autorizados.</span><span class="sxs-lookup"><span data-stu-id="621a2-128">If you only add users to a group when the credentials are valid for that group, messages sent to that group will only go to authorized users.</span></span> <span data-ttu-id="621a2-129">Sin embargo, los grupos no son una característica de seguridad.</span><span class="sxs-lookup"><span data-stu-id="621a2-129">However, groups are not a security feature.</span></span> <span data-ttu-id="621a2-130">Las notificaciones de autenticación tienen características que los grupos no, como la expiración y la revocación.</span><span class="sxs-lookup"><span data-stu-id="621a2-130">Authentication claims have features that groups do not, such as expiry and revocation.</span></span> <span data-ttu-id="621a2-131">Si se revoca el permiso de un usuario para obtener acceso al grupo, debe detectarlo manualmente y quitarlo del grupo.</span><span class="sxs-lookup"><span data-stu-id="621a2-131">If a user's permission to access the group is revoked, you have to manually detect that and remove them from the group.</span></span>

> [!NOTE]
> <span data-ttu-id="621a2-132">Los nombres de grupo distinguen mayúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="621a2-132">Group names are case-sensitive.</span></span>

## <a name="related-resources"></a><span data-ttu-id="621a2-133">Recursos relacionados</span><span class="sxs-lookup"><span data-stu-id="621a2-133">Related resources</span></span>

* [<span data-ttu-id="621a2-134">Introducción</span><span class="sxs-lookup"><span data-stu-id="621a2-134">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="621a2-135">Concentradores</span><span class="sxs-lookup"><span data-stu-id="621a2-135">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="621a2-136">Publicación en Azure</span><span class="sxs-lookup"><span data-stu-id="621a2-136">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
