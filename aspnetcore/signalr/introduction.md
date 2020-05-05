---
title: Introducción a ASP.NET Core SignalR
author: bradygaster
description: Obtenga información sobre cómo SignalR la biblioteca de ASP.net Core simplifica la incorporación de funcionalidad en tiempo real a las aplicaciones.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/27/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/introduction
ms.openlocfilehash: 4e968659d23330c4b1403ab998dda9c9e6ebe080
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775783"
---
# <a name="introduction-to-aspnet-core-signalr"></a><span data-ttu-id="2a906-103">Introducción a ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="2a906-103">Introduction to ASP.NET Core SignalR</span></span>

## <a name="what-is-signalr"></a><span data-ttu-id="2a906-104">¿Qué SignalRes?</span><span class="sxs-lookup"><span data-stu-id="2a906-104">What is SignalR?</span></span>

<span data-ttu-id="2a906-105">ASP.NET Core SignalR es una biblioteca de código abierto que simplifica la incorporación de funcionalidades Web en tiempo real a las aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="2a906-105">ASP.NET Core SignalR is an open-source library that simplifies adding real-time web functionality to apps.</span></span> <span data-ttu-id="2a906-106">La funcionalidad web en tiempo real permite que el código del lado servidor Inserte contenido a los clientes al instante.</span><span class="sxs-lookup"><span data-stu-id="2a906-106">Real-time web functionality enables server-side code to push content to clients instantly.</span></span>

<span data-ttu-id="2a906-107">Buenos candidatos para SignalR:</span><span class="sxs-lookup"><span data-stu-id="2a906-107">Good candidates for SignalR:</span></span>

* <span data-ttu-id="2a906-108">Aplicaciones que requieren actualizaciones desde el servidor con mucha frecuencia.</span><span class="sxs-lookup"><span data-stu-id="2a906-108">Apps that require high frequency updates from the server.</span></span> <span data-ttu-id="2a906-109">Algunos ejemplos son juegos, redes sociales, aplicaciones de votación, subastas, mapas y GPS.</span><span class="sxs-lookup"><span data-stu-id="2a906-109">Examples are gaming, social networks, voting, auction, maps, and GPS apps.</span></span>
* <span data-ttu-id="2a906-110">Paneles y aplicaciones de supervisión.</span><span class="sxs-lookup"><span data-stu-id="2a906-110">Dashboards and monitoring apps.</span></span> <span data-ttu-id="2a906-111">Los ejemplos incluyen paneles empresariales, actualizaciones de venta instantáneas o alertas de viaje.</span><span class="sxs-lookup"><span data-stu-id="2a906-111">Examples include company dashboards, instant sales updates, or travel alerts.</span></span>
* <span data-ttu-id="2a906-112">Aplicaciones de colaboración.</span><span class="sxs-lookup"><span data-stu-id="2a906-112">Collaborative apps.</span></span> <span data-ttu-id="2a906-113">Las aplicaciones de pizarra y el software de reuniones de equipo son ejemplos de aplicaciones de colaboración.</span><span class="sxs-lookup"><span data-stu-id="2a906-113">Whiteboard apps and team meeting software are examples of collaborative apps.</span></span>
* <span data-ttu-id="2a906-114">Aplicaciones que requieren notificaciones.</span><span class="sxs-lookup"><span data-stu-id="2a906-114">Apps that require notifications.</span></span> <span data-ttu-id="2a906-115">Redes sociales, correo electrónico, chat, juegos, alertas de viaje y muchas otras aplicaciones utilizan notificaciones.</span><span class="sxs-lookup"><span data-stu-id="2a906-115">Social networks, email, chat, games, travel alerts, and many other apps use notifications.</span></span>

SignalR<span data-ttu-id="2a906-116">proporciona una API para crear [llamadas a procedimiento remoto (RPC)](https://wikipedia.org/wiki/Remote_procedure_call)de servidor a cliente.</span><span class="sxs-lookup"><span data-stu-id="2a906-116"> provides an API for creating server-to-client [remote procedure calls (RPC)](https://wikipedia.org/wiki/Remote_procedure_call).</span></span> <span data-ttu-id="2a906-117">Las RPC llaman a funciones de JavaScript en los clientes desde el código de .NET Core del lado servidor.</span><span class="sxs-lookup"><span data-stu-id="2a906-117">The RPCs call JavaScript functions on clients from server-side .NET Core code.</span></span>

<span data-ttu-id="2a906-118">Estas son algunas características de SignalR para ASP.net Core:</span><span class="sxs-lookup"><span data-stu-id="2a906-118">Here are some features of SignalR for ASP.NET Core:</span></span>

* <span data-ttu-id="2a906-119">Controla la administración de conexiones automáticamente.</span><span class="sxs-lookup"><span data-stu-id="2a906-119">Handles connection management automatically.</span></span>
* <span data-ttu-id="2a906-120">Envía mensajes a todos los clientes conectados simultáneamente.</span><span class="sxs-lookup"><span data-stu-id="2a906-120">Sends messages to all connected clients simultaneously.</span></span> <span data-ttu-id="2a906-121">Por ejemplo, un salón de chat.</span><span class="sxs-lookup"><span data-stu-id="2a906-121">For example, a chat room.</span></span>
* <span data-ttu-id="2a906-122">Envía mensajes a clientes o grupos de clientes específicos.</span><span class="sxs-lookup"><span data-stu-id="2a906-122">Sends messages to specific clients or groups of clients.</span></span>
* <span data-ttu-id="2a906-123">Escala para controlar el aumento del tráfico.</span><span class="sxs-lookup"><span data-stu-id="2a906-123">Scales to handle increasing traffic.</span></span>

<span data-ttu-id="2a906-124">El origen se hospeda en un [ SignalR repositorio de github](https://github.com/dotnet/AspNetCore/tree/master/src/SignalR).</span><span class="sxs-lookup"><span data-stu-id="2a906-124">The source is hosted in a [SignalR repository on GitHub](https://github.com/dotnet/AspNetCore/tree/master/src/SignalR).</span></span>

## <a name="transports"></a><span data-ttu-id="2a906-125">Transportes</span><span class="sxs-lookup"><span data-stu-id="2a906-125">Transports</span></span>

SignalR<span data-ttu-id="2a906-126">admite las siguientes técnicas para controlar la comunicación en tiempo real (en orden de reserva correcta):</span><span class="sxs-lookup"><span data-stu-id="2a906-126"> supports the following techniques for handling real-time communication (in order of graceful fallback):</span></span>

* [<span data-ttu-id="2a906-127">Websocket</span><span class="sxs-lookup"><span data-stu-id="2a906-127">WebSockets</span></span>](https://tools.ietf.org/html/rfc7118)
* <span data-ttu-id="2a906-128">Eventos enviados por el servidor</span><span class="sxs-lookup"><span data-stu-id="2a906-128">Server-Sent Events</span></span>
* <span data-ttu-id="2a906-129">Sondeo largo</span><span class="sxs-lookup"><span data-stu-id="2a906-129">Long Polling</span></span>

SignalR<span data-ttu-id="2a906-130">elige automáticamente el mejor método de transporte que se encuentra dentro de las capacidades del servidor y del cliente.</span><span class="sxs-lookup"><span data-stu-id="2a906-130"> automatically chooses the best transport method that is within the capabilities of the server and client.</span></span>

## <a name="hubs"></a><span data-ttu-id="2a906-131">Concentradores</span><span class="sxs-lookup"><span data-stu-id="2a906-131">Hubs</span></span>

SignalR<span data-ttu-id="2a906-132">usa *hubs* para la comunicación entre clientes y servidores.</span><span class="sxs-lookup"><span data-stu-id="2a906-132"> uses *hubs* to communicate between clients and servers.</span></span>

<span data-ttu-id="2a906-133">Un concentrador es una canalización de alto nivel que permite a un cliente y un servidor llamar a métodos entre sí.</span><span class="sxs-lookup"><span data-stu-id="2a906-133">A hub is a high-level pipeline that allows a client and server to call methods on each other.</span></span> SignalR<span data-ttu-id="2a906-134">controla el envío automático de los límites de la máquina, lo que permite a los clientes llamar a métodos en el servidor y viceversa.</span><span class="sxs-lookup"><span data-stu-id="2a906-134"> handles the dispatching across machine boundaries automatically, allowing clients to call methods on the server and vice versa.</span></span> <span data-ttu-id="2a906-135">Puede pasar parámetros fuertemente tipados a métodos, lo que permite el enlace de modelos.</span><span class="sxs-lookup"><span data-stu-id="2a906-135">You can pass strongly-typed parameters to methods, which enables model binding.</span></span> SignalR<span data-ttu-id="2a906-136">proporciona dos protocolos de concentrador integrados: un protocolo de texto basado en JSON y un protocolo binario basado en [MessagePack](https://msgpack.org/).</span><span class="sxs-lookup"><span data-stu-id="2a906-136"> provides two built-in hub protocols: a text protocol based on JSON and a binary protocol based on [MessagePack](https://msgpack.org/).</span></span>  <span data-ttu-id="2a906-137">MessagePack suele crear mensajes más pequeños en comparación con JSON.</span><span class="sxs-lookup"><span data-stu-id="2a906-137">MessagePack generally creates smaller messages compared to JSON.</span></span> <span data-ttu-id="2a906-138">Los exploradores más antiguos deben admitir el [nivel 2 de XHR](https://caniuse.com/#feat=xhr2) para proporcionar compatibilidad con el protocolo MessagePack.</span><span class="sxs-lookup"><span data-stu-id="2a906-138">Older browsers must support [XHR level 2](https://caniuse.com/#feat=xhr2) to provide MessagePack protocol support.</span></span>

<span data-ttu-id="2a906-139">Los concentradores llaman a código de cliente mediante el envío de mensajes que contienen el nombre y los parámetros del método del lado cliente.</span><span class="sxs-lookup"><span data-stu-id="2a906-139">Hubs call client-side code by sending messages that contain the name and parameters of the client-side method.</span></span> <span data-ttu-id="2a906-140">Los objetos enviados como parámetros de método se deserializan mediante el protocolo configurado.</span><span class="sxs-lookup"><span data-stu-id="2a906-140">Objects sent as method parameters are deserialized using the configured protocol.</span></span> <span data-ttu-id="2a906-141">El cliente intenta hacer coincidir el nombre con un método en el código del lado cliente.</span><span class="sxs-lookup"><span data-stu-id="2a906-141">The client tries to match the name to a method in the client-side code.</span></span> <span data-ttu-id="2a906-142">Cuando el cliente encuentra una coincidencia, llama al método y le pasa los datos del parámetro deserializado.</span><span class="sxs-lookup"><span data-stu-id="2a906-142">When the client finds a match, it calls the method and passes to it the deserialized parameter data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2a906-143">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="2a906-143">Additional resources</span></span>

* <span data-ttu-id="2a906-144">[Introducción a SignalR for ASP.net Core](xref:tutorials/signalr)</span><span class="sxs-lookup"><span data-stu-id="2a906-144">[Get started with SignalR for ASP.NET Core](xref:tutorials/signalr)</span></span>
* [<span data-ttu-id="2a906-145">Plataformas compatibles</span><span class="sxs-lookup"><span data-stu-id="2a906-145">Supported Platforms</span></span>](xref:signalr/supported-platforms)
* [<span data-ttu-id="2a906-146">Concentradores</span><span class="sxs-lookup"><span data-stu-id="2a906-146">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="2a906-147">Cliente de JavaScript</span><span class="sxs-lookup"><span data-stu-id="2a906-147">JavaScript client</span></span>](xref:signalr/javascript-client)
