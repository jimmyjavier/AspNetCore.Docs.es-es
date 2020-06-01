---
<span data-ttu-id="e56f1-101">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e56f1-101">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e56f1-102">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="e56f1-102">'Blazor'</span></span>
- <span data-ttu-id="e56f1-103">"Identity"</span><span class="sxs-lookup"><span data-stu-id="e56f1-103">'Identity'</span></span>
- <span data-ttu-id="e56f1-104">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="e56f1-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="e56f1-105">"Razor"</span><span class="sxs-lookup"><span data-stu-id="e56f1-105">'Razor'</span></span>
- <span data-ttu-id="e56f1-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="e56f1-106">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-loadstress-testing"></a><span data-ttu-id="e56f1-107">Pruebas de carga y de esfuerzo de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e56f1-107">ASP.NET Core load/stress testing</span></span>

<span data-ttu-id="e56f1-108">Las pruebas de carga y las de esfuerzo son importantes para garantizar que una aplicación web es eficaz y escalable.</span><span class="sxs-lookup"><span data-stu-id="e56f1-108">Load testing and stress testing are important to ensure a web app is performant and scalable.</span></span> <span data-ttu-id="e56f1-109">Sus objetivos son diferentes, aunque con frecuencia comparten pruebas similares.</span><span class="sxs-lookup"><span data-stu-id="e56f1-109">Their goals are different even though they often share similar tests.</span></span>

<span data-ttu-id="e56f1-110">**Pruebas de carga**: prueban si la aplicación puede controlar una determinada carga de usuarios para un escenario concreto y, a la vez, satisfacer el objetivo de respuesta.</span><span class="sxs-lookup"><span data-stu-id="e56f1-110">**Load tests**: Test whether the app can handle a specified load of users for a certain scenario while still satisfying the response goal.</span></span> <span data-ttu-id="e56f1-111">La aplicación se ejecuta en condiciones normales.</span><span class="sxs-lookup"><span data-stu-id="e56f1-111">The app is run under normal conditions.</span></span>

<span data-ttu-id="e56f1-112">**Pruebas de esfuerzo**: prueban la estabilidad de la aplicación al ejecutarse en condiciones extremas, a menudo durante un largo período de tiempo.</span><span class="sxs-lookup"><span data-stu-id="e56f1-112">**Stress tests**: Test app stability when running under extreme conditions, often for a long period of time.</span></span> <span data-ttu-id="e56f1-113">Las pruebas colocan en la aplicación una gran carga de usuarios, ya sea picos o una carga que aumenta gradualmente, o limitan los recursos informáticos de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="e56f1-113">The tests place high user load, either spikes or gradually increasing load, on the app, or they limit the app's computing resources.</span></span>

<span data-ttu-id="e56f1-114">Las pruebas de esfuerzo determinan si una aplicación sometida a un esfuerzo puede recuperarse de un error y volver correctamente al comportamiento esperado.</span><span class="sxs-lookup"><span data-stu-id="e56f1-114">Stress tests determine if an app under stress can recover from failure and gracefully return to expected behavior.</span></span> <span data-ttu-id="e56f1-115">Bajo esfuerzo, la aplicación no se ejecuta en condiciones normales.</span><span class="sxs-lookup"><span data-stu-id="e56f1-115">Under stress, the app isn't run under normal conditions.</span></span>

<span data-ttu-id="e56f1-116">Visual Studio 2019 es la última versión de Visual Studio con características de pruebas de carga.</span><span class="sxs-lookup"><span data-stu-id="e56f1-116">Visual Studio 2019 is the last version of Visual Studio with load test features.</span></span> <span data-ttu-id="e56f1-117">Se recomienda a aquellos clientes que necesiten herramientas de prueba de carga en el futuro usar herramientas alternativas, como Apache JMeter, Akamai CloudTest y BlazeMeter.</span><span class="sxs-lookup"><span data-stu-id="e56f1-117">For customers requiring load testing tools in the future, we recommend alternate tools, such as Apache JMeter, Akamai CloudTest, and BlazeMeter.</span></span> <span data-ttu-id="e56f1-118">Para obtener más información, vea [Notas de la versión de Visual Studio 2019](/visualstudio/releases/2019/release-notes-v16.0#test-tools).</span><span class="sxs-lookup"><span data-stu-id="e56f1-118">For more information, see the [Visual Studio 2019 Release Notes](/visualstudio/releases/2019/release-notes-v16.0#test-tools).</span></span>

## <a name="visual-studio-tools"></a><span data-ttu-id="e56f1-119">Visual Studio Tools</span><span class="sxs-lookup"><span data-stu-id="e56f1-119">Visual Studio tools</span></span>

<span data-ttu-id="e56f1-120">Visual Studio permite a los usuarios crear, desarrollar y depurar pruebas de carga y de rendimiento web.</span><span class="sxs-lookup"><span data-stu-id="e56f1-120">Visual Studio allows users to create, develop, and debug web performance and load tests.</span></span> <span data-ttu-id="e56f1-121">Hay una opción disponible para crear pruebas mediante la grabación de acciones en un explorador web.</span><span class="sxs-lookup"><span data-stu-id="e56f1-121">An option is available to create tests by recording actions in a web browser.</span></span>

<span data-ttu-id="e56f1-122">Para obtener información sobre cómo crear, configurar y ejecutar proyectos de prueba de carga con Visual Studio 2017, vea [Inicio rápido: Creación de un proyecto de prueba de carga](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).</span><span class="sxs-lookup"><span data-stu-id="e56f1-122">For information on how to create, configure, and run a load test projects using Visual Studio 2017, see [Quickstart: Create a load test project](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).</span></span>

<span data-ttu-id="e56f1-123">Las pruebas de carga se pueden configurar de modo que se ejecuten en local o en la nube con Azure DevOps.</span><span class="sxs-lookup"><span data-stu-id="e56f1-123">Load tests can be configured to run on-premise or run in the cloud using Azure DevOps.</span></span>

## <a name="third-party-tools"></a><span data-ttu-id="e56f1-124">Herramientas de terceros</span><span class="sxs-lookup"><span data-stu-id="e56f1-124">Third-party tools</span></span>

<span data-ttu-id="e56f1-125">La lista siguiente contiene herramientas de rendimiento web de terceros con diversos conjuntos de características:</span><span class="sxs-lookup"><span data-stu-id="e56f1-125">The following list contains third-party web performance tools with various feature sets:</span></span>

* [<span data-ttu-id="e56f1-126">Apache JMeter</span><span class="sxs-lookup"><span data-stu-id="e56f1-126">Apache JMeter</span></span>](https://jmeter.apache.org/)
* [<span data-ttu-id="e56f1-127">ApacheBench (ab)</span><span class="sxs-lookup"><span data-stu-id="e56f1-127">ApacheBench (ab)</span></span>](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [<span data-ttu-id="e56f1-128">Gatling</span><span class="sxs-lookup"><span data-stu-id="e56f1-128">Gatling</span></span>](https://gatling.io/)
* [<span data-ttu-id="e56f1-129">k6</span><span class="sxs-lookup"><span data-stu-id="e56f1-129">k6</span></span>](https://k6.io)
* [<span data-ttu-id="e56f1-130">Locust</span><span class="sxs-lookup"><span data-stu-id="e56f1-130">Locust</span></span>](https://locust.io/)
* [<span data-ttu-id="e56f1-131">West Wind WebSurge</span><span class="sxs-lookup"><span data-stu-id="e56f1-131">West Wind WebSurge</span></span>](https://websurge.west-wind.com/)
* [<span data-ttu-id="e56f1-132">Netling</span><span class="sxs-lookup"><span data-stu-id="e56f1-132">Netling</span></span>](https://github.com/hallatore/Netling)
* [<span data-ttu-id="e56f1-133">Vegeta</span><span class="sxs-lookup"><span data-stu-id="e56f1-133">Vegeta</span></span>](https://github.com/tsenart/vegeta)

