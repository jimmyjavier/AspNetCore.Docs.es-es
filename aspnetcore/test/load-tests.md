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
# <a name="aspnet-core-loadstress-testing"></a>Pruebas de carga y de esfuerzo de ASP.NET Core

Las pruebas de carga y las de esfuerzo son importantes para garantizar que una aplicación web es eficaz y escalable. Sus objetivos son diferentes, aunque con frecuencia comparten pruebas similares.

**Pruebas de carga**: prueban si la aplicación puede controlar una determinada carga de usuarios para un escenario concreto y, a la vez, satisfacer el objetivo de respuesta. La aplicación se ejecuta en condiciones normales.

**Pruebas de esfuerzo**: prueban la estabilidad de la aplicación al ejecutarse en condiciones extremas, a menudo durante un largo período de tiempo. Las pruebas colocan en la aplicación una gran carga de usuarios, ya sea picos o una carga que aumenta gradualmente, o limitan los recursos informáticos de la aplicación.

Las pruebas de esfuerzo determinan si una aplicación sometida a un esfuerzo puede recuperarse de un error y volver correctamente al comportamiento esperado. Bajo esfuerzo, la aplicación no se ejecuta en condiciones normales.

Visual Studio 2019 es la última versión de Visual Studio con características de pruebas de carga. Se recomienda a aquellos clientes que necesiten herramientas de prueba de carga en el futuro usar herramientas alternativas, como Apache JMeter, Akamai CloudTest y BlazeMeter. Para obtener más información, vea [Notas de la versión de Visual Studio 2019](/visualstudio/releases/2019/release-notes-v16.0#test-tools).

## <a name="visual-studio-tools"></a>Visual Studio Tools

Visual Studio permite a los usuarios crear, desarrollar y depurar pruebas de carga y de rendimiento web. Hay una opción disponible para crear pruebas mediante la grabación de acciones en un explorador web.

Para obtener información sobre cómo crear, configurar y ejecutar proyectos de prueba de carga con Visual Studio 2017, vea [Inicio rápido: Creación de un proyecto de prueba de carga](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).

Las pruebas de carga se pueden configurar de modo que se ejecuten en local o en la nube con Azure DevOps.

## <a name="third-party-tools"></a>Herramientas de terceros

La lista siguiente contiene herramientas de rendimiento web de terceros con diversos conjuntos de características:

* [Apache JMeter](https://jmeter.apache.org/)
* [ApacheBench (ab)](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [Gatling](https://gatling.io/)
* [k6](https://k6.io)
* [Locust](https://locust.io/)
* [West Wind WebSurge](https://websurge.west-wind.com/)
* [Netling](https://github.com/hallatore/Netling)
* [Vegeta](https://github.com/tsenart/vegeta)

