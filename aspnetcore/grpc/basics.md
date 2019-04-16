---
title: Servicios gRPC con C#
author: juntaoluo
description: Aprenda los conceptos básicos al escribir servicios gRPC con C#.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 03/31/2019
uid: grpc/basics
ms.openlocfilehash: ce2682848dc6a81293545c27f0be779e12a3a600
ms.sourcegitcommit: 5995f44e9e13d7e7aa8d193e2825381c42184e47
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/02/2019
ms.locfileid: "59515633"
---
# <a name="grpc-services-with-c"></a><span data-ttu-id="85b22-103">gRPC servicios con c#\#</span><span class="sxs-lookup"><span data-stu-id="85b22-103">gRPC services with C\#</span></span>

<span data-ttu-id="85b22-104">Este documento describen los conceptos necesarios para escribir [gRPC](https://grpc.io/docs/guides/) aplicaciones en C#.</span><span class="sxs-lookup"><span data-stu-id="85b22-104">This document outlines the concepts needed to write [gRPC](https://grpc.io/docs/guides/) apps in C#.</span></span> <span data-ttu-id="85b22-105">Los temas tratados aquí se aplican a ambos [C-core](https://grpc.io/blog/grpc-stacks)-aplicaciones basadas en ASP.NET Core y basada en gRPC.</span><span class="sxs-lookup"><span data-stu-id="85b22-105">The topics covered here apply to both [C-core](https://grpc.io/blog/grpc-stacks)-based and ASP.NET Core-based gRPC apps.</span></span>

## <a name="proto-file"></a><span data-ttu-id="85b22-106">archivo proto</span><span class="sxs-lookup"><span data-stu-id="85b22-106">proto file</span></span>

<span data-ttu-id="85b22-107">gRPC usa un enfoque de contrato primero al desarrollo de API.</span><span class="sxs-lookup"><span data-stu-id="85b22-107">gRPC uses a contract-first approach to API development.</span></span> <span data-ttu-id="85b22-108">Los búferes de protocolo (protobuf) se usan como el lenguaje de diseño de interfaz (IDL) de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="85b22-108">Protocol buffers (protobuf) are used as the Interface Design Language (IDL) by default.</span></span> <span data-ttu-id="85b22-109">El *.proto* contiene el archivo:</span><span class="sxs-lookup"><span data-stu-id="85b22-109">The *.proto* file contains:</span></span>

* <span data-ttu-id="85b22-110">La definición del servicio gRPC.</span><span class="sxs-lookup"><span data-stu-id="85b22-110">The definition of the gRPC service.</span></span>
* <span data-ttu-id="85b22-111">Los mensajes enviados entre clientes y servidores.</span><span class="sxs-lookup"><span data-stu-id="85b22-111">The messages sent between clients and servers.</span></span>

<span data-ttu-id="85b22-112">Para obtener más información sobre la sintaxis de los archivos protobuf, consulte el [documentación oficial (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3).</span><span class="sxs-lookup"><span data-stu-id="85b22-112">For more information on the syntax of protobuf files, see the [official documentation (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3).</span></span>

<span data-ttu-id="85b22-113">Por ejemplo, considere la *greet.proto* archivo usado en [Introducción gRPC service](xref:tutorials/grpc/grpc-start):</span><span class="sxs-lookup"><span data-stu-id="85b22-113">For example, consider the *greet.proto* file used in [Get started with gRPC service](xref:tutorials/grpc/grpc-start):</span></span>

* <span data-ttu-id="85b22-114">Define un `Greeter` service.</span><span class="sxs-lookup"><span data-stu-id="85b22-114">Defines a `Greeter` service.</span></span>
* <span data-ttu-id="85b22-115">El `Greeter` servicio define un `SayHello` llamar.</span><span class="sxs-lookup"><span data-stu-id="85b22-115">The `Greeter` service defines a `SayHello` call.</span></span>
* <span data-ttu-id="85b22-116">`SayHello` envía un `HelloRequest` del mensaje y recibe un `HelloResponse` mensaje:</span><span class="sxs-lookup"><span data-stu-id="85b22-116">`SayHello` sends a `HelloRequest` message and receives a `HelloResponse` message:</span></span>

[!code-proto[](~/tutorials/grpc/grpc-start/samples/GrpcStart/Protos/greet.proto)]

## <a name="add-a-proto-file-to-a-c-app"></a><span data-ttu-id="85b22-117">Agregar un archivo .proto a una C\# app</span><span class="sxs-lookup"><span data-stu-id="85b22-117">Add a .proto file to a C\# app</span></span>

<span data-ttu-id="85b22-118">El *.proto* archivo está incluido en un proyecto agregándolo a la `<Protobuf>` grupo de elementos:</span><span class="sxs-lookup"><span data-stu-id="85b22-118">The *.proto* file is included in a project by adding it to the `<Protobuf>` item group:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/samples/GrpcStart/GrpcGreeter.Server/GrpcGreeter.Server.csproj?highlight=2&range=7-10)]

## <a name="c-tooling-support-for-proto-files"></a><span data-ttu-id="85b22-119">C#Compatibilidad con herramientas de .proto archivos</span><span class="sxs-lookup"><span data-stu-id="85b22-119">C# Tooling support for .proto files</span></span>

<span data-ttu-id="85b22-120">El paquete de herramientas [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) es necesaria para generar el C# activos desde *.proto* archivos.</span><span class="sxs-lookup"><span data-stu-id="85b22-120">The tooling package [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) is required to generate the C# assets from *.proto* files.</span></span> <span data-ttu-id="85b22-121">Los recursos generados (archivos):</span><span class="sxs-lookup"><span data-stu-id="85b22-121">The generated assets (files):</span></span>

* <span data-ttu-id="85b22-122">Se generan según sea necesario cada vez que se compila el proyecto.</span><span class="sxs-lookup"><span data-stu-id="85b22-122">Are generated on an as-needed basis each time the project is built.</span></span>
* <span data-ttu-id="85b22-123">No se agrega al proyecto o proteger en el control de código fuente.</span><span class="sxs-lookup"><span data-stu-id="85b22-123">Aren't added to the project or checked into source control.</span></span>
* <span data-ttu-id="85b22-124">Son un artefacto de compilación contenido en el *obj* directory.</span><span class="sxs-lookup"><span data-stu-id="85b22-124">Are a build artifact contained in the *obj* directory.</span></span>

<span data-ttu-id="85b22-125">Este paquete es necesario para los proyectos de servidor y cliente.</span><span class="sxs-lookup"><span data-stu-id="85b22-125">This package is required by both the server and client projects.</span></span> <span data-ttu-id="85b22-126">`Grpc.Tools` se pueden agregar mediante el Administrador de paquetes en Visual Studio o agregando un `<PackageReference>` al archivo de proyecto:</span><span class="sxs-lookup"><span data-stu-id="85b22-126">`Grpc.Tools` can be added by using the Package Manager in Visual Studio or adding a `<PackageReference>` to the project file:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/samples/GrpcStart/GrpcGreeter.Server/GrpcGreeter.Server.csproj?highlight=1&range=16)]

<span data-ttu-id="85b22-127">El paquete de herramientas no es necesario en tiempo de ejecución, por lo que la dependencia está marcada con `PrivateAssets="All"`.</span><span class="sxs-lookup"><span data-stu-id="85b22-127">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`.</span></span>

## <a name="generated-c-assets"></a><span data-ttu-id="85b22-128">Genera C# activos</span><span class="sxs-lookup"><span data-stu-id="85b22-128">Generated C# assets</span></span>

<span data-ttu-id="85b22-129">El paquete de herramientas genera la C# tipos que representan los mensajes definidos en el que se incluyen *.proto* archivos.</span><span class="sxs-lookup"><span data-stu-id="85b22-129">The tooling package generates the C# types representing the messages defined in the included *.proto* files.</span></span>

<span data-ttu-id="85b22-130">Para los recursos de servidor, se genera un tipo base abstracto de servicio.</span><span class="sxs-lookup"><span data-stu-id="85b22-130">For server-side assets, an abstract service base type is generated.</span></span> <span data-ttu-id="85b22-131">El tipo base contiene las definiciones de todas las llamadas gRPC contenidas en el *.proto* archivo.</span><span class="sxs-lookup"><span data-stu-id="85b22-131">The base type contains the definitions of all the gRPC calls contained in the *.proto* file.</span></span> <span data-ttu-id="85b22-132">Crear una implementación de servicio concreta que se deriva este tipo base e implementa la lógica para las llamadas gRPC.</span><span class="sxs-lookup"><span data-stu-id="85b22-132">Create a concrete service implementation that derives from this base type and implements the logic for the gRPC calls.</span></span> <span data-ttu-id="85b22-133">Para el `greet.proto`, en el ejemplo se ha descrito anteriormente, abstracta `GreeterBase` tipo que contiene un virtual `SayHello` se genera el método.</span><span class="sxs-lookup"><span data-stu-id="85b22-133">For the `greet.proto`, the example described previously, an abstract `GreeterBase` type that contains a virtual `SayHello` method is generated.</span></span> <span data-ttu-id="85b22-134">Una implementación concreta `GreeterService` invalida el método e implementa la lógica que administra la llamada gRPC.</span><span class="sxs-lookup"><span data-stu-id="85b22-134">A concrete implementation `GreeterService` overrides the method and implements the logic handling the gRPC call.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/samples/GrpcStart/GrpcGreeter.Server/Services/GreeterService.cs?name=snippet)]

<span data-ttu-id="85b22-135">Para los recursos del lado cliente, se genera un tipo concreto de cliente.</span><span class="sxs-lookup"><span data-stu-id="85b22-135">For client-side assets, a concrete client type is generated.</span></span> <span data-ttu-id="85b22-136">Llama la gRPC el *.proto* archivo se traducen a métodos en el tipo concreto, que se puede llamar.</span><span class="sxs-lookup"><span data-stu-id="85b22-136">The gRPC calls in the *.proto* file are translated into methods on the concrete type, which can be called.</span></span> <span data-ttu-id="85b22-137">Para el `greet.proto`, en el ejemplo se ha descrito anteriormente, un hormigón `GreeterClient` tipo se genera.</span><span class="sxs-lookup"><span data-stu-id="85b22-137">For the `greet.proto`, the example described previously, a concrete `GreeterClient` type is generated.</span></span> <span data-ttu-id="85b22-138">Llame a `GreeterClient.SayHello` para iniciar una llamada gRPC al servidor.</span><span class="sxs-lookup"><span data-stu-id="85b22-138">Call `GreeterClient.SayHello` to initiate a gRPC call to the server.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/samples/GrpcStart/GrpcGreeter.Client/Program.cs?highlight=9-11&name=snippet)]

<span data-ttu-id="85b22-139">De forma predeterminada, los recursos de servidor y cliente se generan para cada *.proto* archivo incluido en el `<Protobuf>` grupo de elementos.</span><span class="sxs-lookup"><span data-stu-id="85b22-139">By default, server and client assets are generated for each *.proto* file included in the `<Protobuf>` item group.</span></span> <span data-ttu-id="85b22-140">Para asegurarse de que solo los recursos de servidor se generan en un proyecto de servidor, el `GrpcServices` atributo está establecido en `Server`.</span><span class="sxs-lookup"><span data-stu-id="85b22-140">To ensure only the server assets are generated in a server project, the `GrpcServices` attribute is set to `Server`.</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/samples/GrpcStart/GrpcGreeter.Server/GrpcGreeter.Server.csproj?highlight=2&range=7-10)]

<span data-ttu-id="85b22-141">De forma similar, el atributo está establecido en `Client` en proyectos de cliente.</span><span class="sxs-lookup"><span data-stu-id="85b22-141">Similarly, the attribute is set to `Client` in client projects.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="85b22-142">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="85b22-142">Additional resources</span></span>

* <xref:grpc/index>
* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/aspnetcore>
* <xref:grpc/migration>