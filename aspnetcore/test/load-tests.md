---
title: Pruebas de carga y de esfuerzo de ASP.NET Core
author: Jeremy-Meng
description: Obtenga información sobre varias destacables herramientas y enfoques para realizar pruebas de carga y de esfuerzo en aplicaciones ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: test/loadtests
ms.openlocfilehash: f0c930a7689c7288a58cfc3ca630d3cd324bf2cd
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/09/2020
ms.locfileid: "84106811"
---
# <a name="aspnet-core-loadstress-testing"></a><span data-ttu-id="55d98-103">Pruebas de carga y de esfuerzo de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="55d98-103">ASP.NET Core load/stress testing</span></span>

<span data-ttu-id="55d98-104">Las pruebas de carga y las de esfuerzo son importantes para garantizar que una aplicación web es eficaz y escalable.</span><span class="sxs-lookup"><span data-stu-id="55d98-104">Load testing and stress testing are important to ensure a web app is performant and scalable.</span></span> <span data-ttu-id="55d98-105">Sus objetivos son diferentes, aunque con frecuencia comparten pruebas similares.</span><span class="sxs-lookup"><span data-stu-id="55d98-105">Their goals are different even though they often share similar tests.</span></span>

<span data-ttu-id="55d98-106">**Pruebas de carga**: prueban si la aplicación puede controlar una determinada carga de usuarios para un escenario concreto y, a la vez, satisfacer el objetivo de respuesta.</span><span class="sxs-lookup"><span data-stu-id="55d98-106">**Load tests**: Test whether the app can handle a specified load of users for a certain scenario while still satisfying the response goal.</span></span> <span data-ttu-id="55d98-107">La aplicación se ejecuta en condiciones normales.</span><span class="sxs-lookup"><span data-stu-id="55d98-107">The app is run under normal conditions.</span></span>

<span data-ttu-id="55d98-108">**Pruebas de esfuerzo**: prueban la estabilidad de la aplicación al ejecutarse en condiciones extremas, a menudo durante un largo período de tiempo.</span><span class="sxs-lookup"><span data-stu-id="55d98-108">**Stress tests**: Test app stability when running under extreme conditions, often for a long period of time.</span></span> <span data-ttu-id="55d98-109">Las pruebas colocan en la aplicación una gran carga de usuarios, ya sea picos o una carga que aumenta gradualmente, o limitan los recursos informáticos de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="55d98-109">The tests place high user load, either spikes or gradually increasing load, on the app, or they limit the app's computing resources.</span></span>

<span data-ttu-id="55d98-110">Las pruebas de esfuerzo determinan si una aplicación sometida a un esfuerzo puede recuperarse de un error y volver correctamente al comportamiento esperado.</span><span class="sxs-lookup"><span data-stu-id="55d98-110">Stress tests determine if an app under stress can recover from failure and gracefully return to expected behavior.</span></span> <span data-ttu-id="55d98-111">Bajo esfuerzo, la aplicación no se ejecuta en condiciones normales.</span><span class="sxs-lookup"><span data-stu-id="55d98-111">Under stress, the app isn't run under normal conditions.</span></span>

<span data-ttu-id="55d98-112">Visual Studio 2019 es la última versión de Visual Studio con características de pruebas de carga.</span><span class="sxs-lookup"><span data-stu-id="55d98-112">Visual Studio 2019 is the last version of Visual Studio with load test features.</span></span> <span data-ttu-id="55d98-113">Se recomienda a aquellos clientes que necesiten herramientas de prueba de carga en el futuro usar herramientas alternativas, como Apache JMeter, Akamai CloudTest y BlazeMeter.</span><span class="sxs-lookup"><span data-stu-id="55d98-113">For customers requiring load testing tools in the future, we recommend alternate tools, such as Apache JMeter, Akamai CloudTest, and BlazeMeter.</span></span> <span data-ttu-id="55d98-114">Para obtener más información, vea [Notas de la versión de Visual Studio 2019](/visualstudio/releases/2019/release-notes-v16.0#test-tools).</span><span class="sxs-lookup"><span data-stu-id="55d98-114">For more information, see the [Visual Studio 2019 Release Notes](/visualstudio/releases/2019/release-notes-v16.0#test-tools).</span></span>

## <a name="visual-studio-tools"></a><span data-ttu-id="55d98-115">Visual Studio Tools</span><span class="sxs-lookup"><span data-stu-id="55d98-115">Visual Studio tools</span></span>

<span data-ttu-id="55d98-116">Visual Studio permite a los usuarios crear, desarrollar y depurar pruebas de carga y de rendimiento web.</span><span class="sxs-lookup"><span data-stu-id="55d98-116">Visual Studio allows users to create, develop, and debug web performance and load tests.</span></span> <span data-ttu-id="55d98-117">Hay una opción disponible para crear pruebas mediante la grabación de acciones en un explorador web.</span><span class="sxs-lookup"><span data-stu-id="55d98-117">An option is available to create tests by recording actions in a web browser.</span></span>

<span data-ttu-id="55d98-118">Para obtener información sobre cómo crear, configurar y ejecutar proyectos de prueba de carga con Visual Studio 2017, vea [Inicio rápido: Creación de un proyecto de prueba de carga](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).</span><span class="sxs-lookup"><span data-stu-id="55d98-118">For information on how to create, configure, and run a load test projects using Visual Studio 2017, see [Quickstart: Create a load test project](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).</span></span>

<span data-ttu-id="55d98-119">Las pruebas de carga se pueden configurar de modo que se ejecuten en local o en la nube con Azure DevOps.</span><span class="sxs-lookup"><span data-stu-id="55d98-119">Load tests can be configured to run on-premise or run in the cloud using Azure DevOps.</span></span>

## <a name="third-party-tools"></a><span data-ttu-id="55d98-120">Herramientas de terceros</span><span class="sxs-lookup"><span data-stu-id="55d98-120">Third-party tools</span></span>

<span data-ttu-id="55d98-121">La lista siguiente contiene herramientas de rendimiento web de terceros con diversos conjuntos de características:</span><span class="sxs-lookup"><span data-stu-id="55d98-121">The following list contains third-party web performance tools with various feature sets:</span></span>

* [<span data-ttu-id="55d98-122">Apache JMeter</span><span class="sxs-lookup"><span data-stu-id="55d98-122">Apache JMeter</span></span>](https://jmeter.apache.org/)
* [<span data-ttu-id="55d98-123">ApacheBench (ab)</span><span class="sxs-lookup"><span data-stu-id="55d98-123">ApacheBench (ab)</span></span>](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [<span data-ttu-id="55d98-124">Gatling</span><span class="sxs-lookup"><span data-stu-id="55d98-124">Gatling</span></span>](https://gatling.io/)
* [<span data-ttu-id="55d98-125">k6</span><span class="sxs-lookup"><span data-stu-id="55d98-125">k6</span></span>](https://k6.io)
* [<span data-ttu-id="55d98-126">Locust</span><span class="sxs-lookup"><span data-stu-id="55d98-126">Locust</span></span>](https://locust.io/)
* [<span data-ttu-id="55d98-127">West Wind WebSurge</span><span class="sxs-lookup"><span data-stu-id="55d98-127">West Wind WebSurge</span></span>](https://websurge.west-wind.com/)
* [<span data-ttu-id="55d98-128">Netling</span><span class="sxs-lookup"><span data-stu-id="55d98-128">Netling</span></span>](https://github.com/hallatore/Netling)
* [<span data-ttu-id="55d98-129">Vegeta</span><span class="sxs-lookup"><span data-stu-id="55d98-129">Vegeta</span></span>](https://github.com/tsenart/vegeta)

