---
<span data-ttu-id="39000-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="39000-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39000-102">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="39000-102">'Blazor'</span></span>
- <span data-ttu-id="39000-103">"Identity"</span><span class="sxs-lookup"><span data-stu-id="39000-103">'Identity'</span></span>
- <span data-ttu-id="39000-104">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="39000-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="39000-105">"Razor"</span><span class="sxs-lookup"><span data-stu-id="39000-105">'Razor'</span></span>
- <span data-ttu-id="39000-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="39000-106">'SignalR' uid:</span></span> 

---
# <a name="integration-tests-in-aspnet-core"></a><span data-ttu-id="39000-107">Pruebas de integración en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="39000-107">Integration tests in ASP.NET Core</span></span>

<span data-ttu-id="39000-108">Por [Javier Calvarro Nelson](https://github.com/javiercn), [Steve Smith](https://ardalis.com/) y [Jos van der Til](https://jvandertil.nl)</span><span class="sxs-lookup"><span data-stu-id="39000-108">By [Javier Calvarro Nelson](https://github.com/javiercn), [Steve Smith](https://ardalis.com/), and [Jos van der Til](https://jvandertil.nl)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="39000-109">Las pruebas de integración garantizan que los componentes de una aplicación funcionan correctamente en un nivel que incluye la infraestructura auxiliar de la aplicación, como la base de datos, el sistema de archivos y la red.</span><span class="sxs-lookup"><span data-stu-id="39000-109">Integration tests ensure that an app's components function correctly at a level that includes the app's supporting infrastructure, such as the database, file system, and network.</span></span> <span data-ttu-id="39000-110">ASP.NET Core admite las pruebas de integración mediante un marco de pruebas unitarias con un host web de prueba y un servidor de pruebas en memoria.</span><span class="sxs-lookup"><span data-stu-id="39000-110">ASP.NET Core supports integration tests using a unit test framework with a test web host and an in-memory test server.</span></span>

<span data-ttu-id="39000-111">En este artículo se da por hecho un conocimiento básico de las pruebas unitarias.</span><span class="sxs-lookup"><span data-stu-id="39000-111">This topic assumes a basic understanding of unit tests.</span></span> <span data-ttu-id="39000-112">Si no está familiarizado con los conceptos de las pruebas, vea el tema [Pruebas unitaria en .NET Core y .NET Standard](/dotnet/core/testing/) y su contenido vinculado.</span><span class="sxs-lookup"><span data-stu-id="39000-112">If unfamiliar with test concepts, see the [Unit Testing in .NET Core and .NET Standard](/dotnet/core/testing/) topic and its linked content.</span></span>

<span data-ttu-id="39000-113">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="39000-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="39000-114">La aplicación de ejemplo es una aplicación de Razor Pages que da por hecho un conocimiento básico de Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="39000-114">The sample app is a Razor Pages app and assumes a basic understanding of Razor Pages.</span></span> <span data-ttu-id="39000-115">Si no está familiarizado con Razor Pages, consulte los temas siguientes:</span><span class="sxs-lookup"><span data-stu-id="39000-115">If unfamiliar with Razor Pages, see the following topics:</span></span>

* <span data-ttu-id="39000-116">[Introducción a Razor Pages](xref:razor-pages/index)</span><span class="sxs-lookup"><span data-stu-id="39000-116">[Introduction to Razor Pages](xref:razor-pages/index)</span></span>
* <span data-ttu-id="39000-117">[Primeros pasos con Razor Pages](xref:tutorials/razor-pages/razor-pages-start)</span><span class="sxs-lookup"><span data-stu-id="39000-117">[Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start)</span></span>
* <span data-ttu-id="39000-118">Pruebas unitarias de [Razor Pages](xref:test/razor-pages-tests)</span><span class="sxs-lookup"><span data-stu-id="39000-118">[Razor Pages unit tests](xref:test/razor-pages-tests)</span></span>

> [!NOTE]
> <span data-ttu-id="39000-119">Para probar SPA, se recomienda una herramienta como [Selenium](https://www.seleniumhq.org/), que puede automatizar un explorador.</span><span class="sxs-lookup"><span data-stu-id="39000-119">For testing SPAs, we recommended a tool such as [Selenium](https://www.seleniumhq.org/), which can automate a browser.</span></span>

## <a name="introduction-to-integration-tests"></a><span data-ttu-id="39000-120">Introducción a las pruebas de integración</span><span class="sxs-lookup"><span data-stu-id="39000-120">Introduction to integration tests</span></span>

<span data-ttu-id="39000-121">Las pruebas de integración evalúan los componentes de una aplicación en un nivel más amplio que las [pruebas unitarias](/dotnet/core/testing/).</span><span class="sxs-lookup"><span data-stu-id="39000-121">Integration tests evaluate an app's components on a broader level than [unit tests](/dotnet/core/testing/).</span></span> <span data-ttu-id="39000-122">Las pruebas unitarias se usan para probar componentes de software aislados, como métodos de clase individuales.</span><span class="sxs-lookup"><span data-stu-id="39000-122">Unit tests are used to test isolated software components, such as individual class methods.</span></span> <span data-ttu-id="39000-123">Las pruebas de integración confirman que dos o más componentes de una aplicación funcionan juntos para generar un resultado esperado, lo que posiblemente incluya a todos los componentes necesarios para procesar por completo una solicitud.</span><span class="sxs-lookup"><span data-stu-id="39000-123">Integration tests confirm that two or more app components work together to produce an expected result, possibly including every component required to fully process a request.</span></span>

<span data-ttu-id="39000-124">Estas pruebas más amplias se usan para probar la infraestructura de la aplicación y todo el marco, lo que a menudo incluye los siguientes componentes:</span><span class="sxs-lookup"><span data-stu-id="39000-124">These broader tests are used to test the app's infrastructure and whole framework, often including the following components:</span></span>

* <span data-ttu-id="39000-125">Base de datos</span><span class="sxs-lookup"><span data-stu-id="39000-125">Database</span></span>
* <span data-ttu-id="39000-126">Sistema de archivos</span><span class="sxs-lookup"><span data-stu-id="39000-126">File system</span></span>
* <span data-ttu-id="39000-127">Dispositivos de red</span><span class="sxs-lookup"><span data-stu-id="39000-127">Network appliances</span></span>
* <span data-ttu-id="39000-128">Canalización de solicitud-respuesta</span><span class="sxs-lookup"><span data-stu-id="39000-128">Request-response pipeline</span></span>

<span data-ttu-id="39000-129">Las pruebas unitarias usan componentes fabricados, conocidos como *emulaciones* u *objetos ficticios*, en lugar de componentes de las infraestructura.</span><span class="sxs-lookup"><span data-stu-id="39000-129">Unit tests use fabricated components, known as *fakes* or *mock objects*, in place of infrastructure components.</span></span>

<span data-ttu-id="39000-130">A diferencia de las pruebas unitarias, las pruebas de integración:</span><span class="sxs-lookup"><span data-stu-id="39000-130">In contrast to unit tests, integration tests:</span></span>

* <span data-ttu-id="39000-131">Usan los componentes reales que emplea la aplicación en producción.</span><span class="sxs-lookup"><span data-stu-id="39000-131">Use the actual components that the app uses in production.</span></span>
* <span data-ttu-id="39000-132">Necesitan más código y procesamiento de datos.</span><span class="sxs-lookup"><span data-stu-id="39000-132">Require more code and data processing.</span></span>
* <span data-ttu-id="39000-133">Tardan más en ejecutarse.</span><span class="sxs-lookup"><span data-stu-id="39000-133">Take longer to run.</span></span>

<span data-ttu-id="39000-134">Por lo tanto, limite el uso de pruebas de integración a los escenarios de infraestructura más importantes.</span><span class="sxs-lookup"><span data-stu-id="39000-134">Therefore, limit the use of integration tests to the most important infrastructure scenarios.</span></span> <span data-ttu-id="39000-135">Si un comportamiento se puede probar mediante una prueba unitaria o una prueba de integración, opte por la prueba unitaria.</span><span class="sxs-lookup"><span data-stu-id="39000-135">If a behavior can be tested using either a unit test or an integration test, choose the unit test.</span></span>

> [!TIP]
> <span data-ttu-id="39000-136">No escriba pruebas de integración para cada posible permutación de datos y acceso a archivos con bases de datos y sistemas de archivos.</span><span class="sxs-lookup"><span data-stu-id="39000-136">Don't write integration tests for every possible permutation of data and file access with databases and file systems.</span></span> <span data-ttu-id="39000-137">Independientemente de cuántas ubicaciones de una aplicación interactúen con bases de datos y sistemas de archivos, un conjunto centrado de pruebas de integración de lectura, escritura, actualización y eliminación suele ser capaz de probar adecuadamente los componentes de sistema de archivos y base de datos.</span><span class="sxs-lookup"><span data-stu-id="39000-137">Regardless of how many places across an app interact with databases and file systems, a focused set of read, write, update, and delete integration tests are usually capable of adequately testing database and file system components.</span></span> <span data-ttu-id="39000-138">Use pruebas unitarias para las pruebas rutinarias de lógica de métodos que interactúan con estos componentes.</span><span class="sxs-lookup"><span data-stu-id="39000-138">Use unit tests for routine tests of method logic that interact with these components.</span></span> <span data-ttu-id="39000-139">En las pruebas unitarias, el uso de simulaciones o emulaciones de infraestructura da lugar a una ejecución más rápida de esas pruebas.</span><span class="sxs-lookup"><span data-stu-id="39000-139">In unit tests, the use of infrastructure fakes/mocks result in faster test execution.</span></span>

> [!NOTE]
> <span data-ttu-id="39000-140">En las conversaciones de las pruebas de integración, el proyecto probado suele denominarse *Sistema a prueba* o "SUT" para abreviar.</span><span class="sxs-lookup"><span data-stu-id="39000-140">In discussions of integration tests, the tested project is frequently called the *System Under Test*, or "SUT" for short.</span></span>
>
> <span data-ttu-id="39000-141">*En este tema se usa "SUT" para referirse a la aplicación ASP.NET Core probada.*</span><span class="sxs-lookup"><span data-stu-id="39000-141">*"SUT" is used throughout this topic to refer to the tested ASP.NET Core app.*</span></span>

## <a name="aspnet-core-integration-tests"></a><span data-ttu-id="39000-142">Pruebas de integración de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="39000-142">ASP.NET Core integration tests</span></span>

<span data-ttu-id="39000-143">Para las pruebas de integración en ASP.NET Core se necesita lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="39000-143">Integration tests in ASP.NET Core require the following:</span></span>

* <span data-ttu-id="39000-144">Un proyecto de prueba, que se usa para contener y ejecutar las pruebas.</span><span class="sxs-lookup"><span data-stu-id="39000-144">A test project is used to contain and execute the tests.</span></span> <span data-ttu-id="39000-145">El proyecto de prueba tiene una referencia al SUT.</span><span class="sxs-lookup"><span data-stu-id="39000-145">The test project has a reference to the SUT.</span></span>
* <span data-ttu-id="39000-146">El proyecto de prueba crea un host web de prueba para el SUT y usa un cliente de servidor de pruebas para controlar las solicitudes y las respuestas con el SUT.</span><span class="sxs-lookup"><span data-stu-id="39000-146">The test project creates a test web host for the SUT and uses a test server client to handle requests and responses with the SUT.</span></span>
* <span data-ttu-id="39000-147">Se usa un ejecutor de pruebas para ejecutar las pruebas y notificar los resultados de estas.</span><span class="sxs-lookup"><span data-stu-id="39000-147">A test runner is used to execute the tests and report the test results.</span></span>

<span data-ttu-id="39000-148">Las pruebas de integración siguen una secuencia de eventos que incluye los pasos de prueba normales *Arrange*, *Act* y *Assert*:</span><span class="sxs-lookup"><span data-stu-id="39000-148">Integration tests follow a sequence of events that include the usual *Arrange*, *Act*, and *Assert* test steps:</span></span>

1. <span data-ttu-id="39000-149">Se configura el host web del SUT.</span><span class="sxs-lookup"><span data-stu-id="39000-149">The SUT's web host is configured.</span></span>
1. <span data-ttu-id="39000-150">Se crea un cliente de servidor de pruebas para enviar solicitudes a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="39000-150">A test server client is created to submit requests to the app.</span></span>
1. <span data-ttu-id="39000-151">Se ejecuta el paso de prueba *Arrange*: la aplicación de prueba prepara una solicitud.</span><span class="sxs-lookup"><span data-stu-id="39000-151">The *Arrange* test step is executed: The test app prepares a request.</span></span>
1. <span data-ttu-id="39000-152">Se ejecuta el paso de prueba *Act*: el cliente envía la solicitud y recibe la respuesta.</span><span class="sxs-lookup"><span data-stu-id="39000-152">The *Act* test step is executed: The client submits the request and receives the response.</span></span>
1. <span data-ttu-id="39000-153">Se ejecuta el paso de prueba *Assert*: la respuesta *real* se valida como *correcta* o *errónea* en función de una respuesta *esperada*.</span><span class="sxs-lookup"><span data-stu-id="39000-153">The *Assert* test step is executed: The *actual* response is validated as a *pass* or *fail* based on an *expected* response.</span></span>
1. <span data-ttu-id="39000-154">El proceso continúa hasta que se ejecutan todas las pruebas.</span><span class="sxs-lookup"><span data-stu-id="39000-154">The process continues until all of the tests are executed.</span></span>
1. <span data-ttu-id="39000-155">Se notifican los resultados de la prueba.</span><span class="sxs-lookup"><span data-stu-id="39000-155">The test results are reported.</span></span>

<span data-ttu-id="39000-156">Normalmente, el host web de prueba se configura de manera diferente al host web normal de la aplicación para las series de pruebas.</span><span class="sxs-lookup"><span data-stu-id="39000-156">Usually, the test web host is configured differently than the app's normal web host for the test runs.</span></span> <span data-ttu-id="39000-157">Por ejemplo, puede usarse una base de datos diferente u otra configuración de aplicación para las pruebas.</span><span class="sxs-lookup"><span data-stu-id="39000-157">For example, a different database or different app settings might be used for the tests.</span></span>

<span data-ttu-id="39000-158">Los componentes de infraestructura, como el host web de prueba y el servidor de pruebas en memoria ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), se proporcionan o se administran mediante el paquete [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing).</span><span class="sxs-lookup"><span data-stu-id="39000-158">Infrastructure components, such as the test web host and in-memory test server ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), are provided or managed by the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package.</span></span> <span data-ttu-id="39000-159">El uso de este paquete simplifica la creación y ejecución de pruebas.</span><span class="sxs-lookup"><span data-stu-id="39000-159">Use of this package streamlines test creation and execution.</span></span>

<span data-ttu-id="39000-160">El paquete `Microsoft.AspNetCore.Mvc.Testing` controla las siguientes tareas:</span><span class="sxs-lookup"><span data-stu-id="39000-160">The `Microsoft.AspNetCore.Mvc.Testing` package handles the following tasks:</span></span>

* <span data-ttu-id="39000-161">Copia el archivo de dependencias ( *.deps*) del SUT en el directorio *bin* del proyecto de prueba.</span><span class="sxs-lookup"><span data-stu-id="39000-161">Copies the dependencies file (*.deps*) from the SUT into the test project's *bin* directory.</span></span>
* <span data-ttu-id="39000-162">Establece la [raíz de contenido](xref:fundamentals/index#content-root) en la raíz de proyecto del SUT de modo que se puedan encontrar archivos estáticos y páginas o vistas cuando se ejecuten las pruebas.</span><span class="sxs-lookup"><span data-stu-id="39000-162">Sets the [content root](xref:fundamentals/index#content-root) to the SUT's project root so that static files and pages/views are found when the tests are executed.</span></span>
* <span data-ttu-id="39000-163">Proporciona la clase [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) para simplificar el arranque del SUT con `TestServer`.</span><span class="sxs-lookup"><span data-stu-id="39000-163">Provides the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) class to streamline bootstrapping the SUT with `TestServer`.</span></span>

<span data-ttu-id="39000-164">En la documentación de las [pruebas unitarias](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) se explica cómo configurar un proyecto de prueba y un ejecutor de pruebas, además de incluirse instrucciones detalladas sobre cómo ejecutar pruebas y recomendaciones sobre cómo asignar nombres a las pruebas y las clases de prueba.</span><span class="sxs-lookup"><span data-stu-id="39000-164">The [unit tests](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) documentation describes how to set up a test project and test runner, along with detailed instructions on how to run tests and recommendations for how to name tests and test classes.</span></span>

> [!NOTE]
> <span data-ttu-id="39000-165">Al crear un proyecto de prueba para una aplicación, separe las pruebas unitarias de las pruebas de integración en proyectos diferentes.</span><span class="sxs-lookup"><span data-stu-id="39000-165">When creating a test project for an app, separate the unit tests from the integration tests into different projects.</span></span> <span data-ttu-id="39000-166">Esto ayuda a garantizar que los componentes de pruebas de infraestructura no se incluyan accidentalmente en las pruebas unitarias.</span><span class="sxs-lookup"><span data-stu-id="39000-166">This helps ensure that infrastructure testing components aren't accidentally included in the unit tests.</span></span> <span data-ttu-id="39000-167">La separación de las pruebas unitarias y de integración también permite controlar qué conjunto de pruebas se ejecuta.</span><span class="sxs-lookup"><span data-stu-id="39000-167">Separation of unit and integration tests also allows control over which set of tests are run.</span></span>

<span data-ttu-id="39000-168">Prácticamente no hay ninguna diferencia entre la configuración de las pruebas de aplicaciones Razor Pages y MVC.</span><span class="sxs-lookup"><span data-stu-id="39000-168">There's virtually no difference between the configuration for tests of Razor Pages apps and MVC apps.</span></span> <span data-ttu-id="39000-169">La única diferencia es cómo se asigna nombre a las pruebas.</span><span class="sxs-lookup"><span data-stu-id="39000-169">The only difference is in how the tests are named.</span></span> <span data-ttu-id="39000-170">En una aplicación Razor Pages, las pruebas de puntos de conexión de página normalmente se nombran según la clase del modelo de página (por ejemplo, `IndexPageTests` para probar la integración de los componentes de la página Index).</span><span class="sxs-lookup"><span data-stu-id="39000-170">In a Razor Pages app, tests of page endpoints are usually named after the page model class (for example, `IndexPageTests` to test component integration for the Index page).</span></span> <span data-ttu-id="39000-171">En una aplicación de MVC, las pruebas suelen organizarse por clases de controlador y denominarse según los controladores que prueban (por ejemplo, `HomeControllerTests` para probar la integración de componentes del controlador Home).</span><span class="sxs-lookup"><span data-stu-id="39000-171">In an MVC app, tests are usually organized by controller classes and named after the controllers they test (for example, `HomeControllerTests` to test component integration for the Home controller).</span></span>

## <a name="test-app-prerequisites"></a><span data-ttu-id="39000-172">Requisitos previos de la aplicación de prueba</span><span class="sxs-lookup"><span data-stu-id="39000-172">Test app prerequisites</span></span>

<span data-ttu-id="39000-173">El proyecto de prueba debe:</span><span class="sxs-lookup"><span data-stu-id="39000-173">The test project must:</span></span>

* <span data-ttu-id="39000-174">Hacer referencia al paquete [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing).</span><span class="sxs-lookup"><span data-stu-id="39000-174">Reference the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package.</span></span>
* <span data-ttu-id="39000-175">Especificar el SDK web en el archivo de proyecto (`<Project Sdk="Microsoft.NET.Sdk.Web">`).</span><span class="sxs-lookup"><span data-stu-id="39000-175">Specify the Web SDK in the project file (`<Project Sdk="Microsoft.NET.Sdk.Web">`).</span></span>

<span data-ttu-id="39000-176">Estos requisitos previos se pueden observar en la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/).</span><span class="sxs-lookup"><span data-stu-id="39000-176">These prerequisites can be seen in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/).</span></span> <span data-ttu-id="39000-177">Examine el archivo *tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj*.</span><span class="sxs-lookup"><span data-stu-id="39000-177">Inspect the *tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj* file.</span></span> <span data-ttu-id="39000-178">La aplicación de ejemplo usa el marco de pruebas [xUnit](https://xunit.github.io/) y la biblioteca de análisis [AngleSharp](https://anglesharp.github.io/), así que la aplicación de ejemplo también hace referencia a:</span><span class="sxs-lookup"><span data-stu-id="39000-178">The sample app uses the [xUnit](https://xunit.github.io/) test framework and the [AngleSharp](https://anglesharp.github.io/) parser library, so the sample app also references:</span></span>

* [<span data-ttu-id="39000-179">xunit</span><span class="sxs-lookup"><span data-stu-id="39000-179">xunit</span></span>](https://www.nuget.org/packages/xunit)
* [<span data-ttu-id="39000-180">xunit.runner.visualstudio</span><span class="sxs-lookup"><span data-stu-id="39000-180">xunit.runner.visualstudio</span></span>](https://www.nuget.org/packages/xunit.runner.visualstudio)
* [<span data-ttu-id="39000-181">AngleSharp</span><span class="sxs-lookup"><span data-stu-id="39000-181">AngleSharp</span></span>](https://www.nuget.org/packages/AngleSharp)

<span data-ttu-id="39000-182">También se usa Entity Framework Core en las pruebas.</span><span class="sxs-lookup"><span data-stu-id="39000-182">Entity Framework Core is also used in the tests.</span></span> <span data-ttu-id="39000-183">La aplicación hace referencia a:</span><span class="sxs-lookup"><span data-stu-id="39000-183">The app references:</span></span>

* [<span data-ttu-id="39000-184">Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="39000-184">Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore)
* <span data-ttu-id="39000-185">[Microsoft.AspNetCore.Identity.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity.EntityFrameworkCore)</span><span class="sxs-lookup"><span data-stu-id="39000-185">[Microsoft.AspNetCore.Identity.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity.EntityFrameworkCore)</span></span>
* [<span data-ttu-id="39000-186">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="39000-186">Microsoft.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore)
* [<span data-ttu-id="39000-187">Microsoft.EntityFrameworkCore.InMemory</span><span class="sxs-lookup"><span data-stu-id="39000-187">Microsoft.EntityFrameworkCore.InMemory</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory)
* [<span data-ttu-id="39000-188">Microsoft.EntityFrameworkCore.Tools</span><span class="sxs-lookup"><span data-stu-id="39000-188">Microsoft.EntityFrameworkCore.Tools</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools)

## <a name="sut-environment"></a><span data-ttu-id="39000-189">Entorno del SUT</span><span class="sxs-lookup"><span data-stu-id="39000-189">SUT environment</span></span>

<span data-ttu-id="39000-190">Si el entorno del [SUT](xref:fundamentals/environments) no está establecido, el valor predeterminado del entorno es Desarrollo.</span><span class="sxs-lookup"><span data-stu-id="39000-190">If the SUT's [environment](xref:fundamentals/environments) isn't set, the environment defaults to Development.</span></span>

## <a name="basic-tests-with-the-default-webapplicationfactory"></a><span data-ttu-id="39000-191">Pruebas básicas con el valor predeterminado WebApplicationFactory</span><span class="sxs-lookup"><span data-stu-id="39000-191">Basic tests with the default WebApplicationFactory</span></span>

<span data-ttu-id="39000-192">[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) se usa para crear una instancia de [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) para las pruebas de integración.</span><span class="sxs-lookup"><span data-stu-id="39000-192">[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) is used to create a [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) for the integration tests.</span></span> <span data-ttu-id="39000-193">`TEntryPoint` es la clase de punto de entrada del SUT, normalmente la clase `Startup`.</span><span class="sxs-lookup"><span data-stu-id="39000-193">`TEntryPoint` is the entry point class of the SUT, usually the `Startup` class.</span></span>

<span data-ttu-id="39000-194">Las clases de prueba implementan una interfaz de *accesorio de clase* ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) para indicar que la clase contiene pruebas y proporcionan instancias de objeto compartidas en las pruebas de la clase.</span><span class="sxs-lookup"><span data-stu-id="39000-194">Test classes implement a *class fixture* interface ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) to indicate the class contains tests and provide shared object instances across the tests in the class.</span></span>

<span data-ttu-id="39000-195">La siguiente clase de prueba, `BasicTests`, usa `WebApplicationFactory` para arrancar el SUT y proporcionar un valor [HttpClient](/dotnet/api/system.net.http.httpclient) a un método de prueba, `Get_EndpointsReturnSuccessAndCorrectContentType`.</span><span class="sxs-lookup"><span data-stu-id="39000-195">The following test class, `BasicTests`, uses the `WebApplicationFactory` to bootstrap the SUT and provide an [HttpClient](/dotnet/api/system.net.http.httpclient) to a test method, `Get_EndpointsReturnSuccessAndCorrectContentType`.</span></span> <span data-ttu-id="39000-196">El método comprueba si el código de estado de la respuesta es correcto (códigos de estado del rango 200-299) y si el encabezado `Content-Type` es `text/html; charset=utf-8` para varias páginas de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="39000-196">The method checks if the response status code is successful (status codes in the range 200-299) and the `Content-Type` header is `text/html; charset=utf-8` for several app pages.</span></span>

<span data-ttu-id="39000-197">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) crea una instancia de `HttpClient` que sigue automáticamente los redireccionamientos y controla las cookies.</span><span class="sxs-lookup"><span data-stu-id="39000-197">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) creates an instance of `HttpClient` that automatically follows redirects and handles cookies.</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

<span data-ttu-id="39000-198">De forma predeterminada, las cookies no esenciales no se conservan entre solicitudes si está habilitada la [directiva de consentimiento de RGPD](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="39000-198">By default, non-essential cookies aren't preserved across requests when the [GDPR consent policy](xref:security/gdpr) is enabled.</span></span> <span data-ttu-id="39000-199">Para conservar las cookies no esenciales, como las usadas por el proveedor TempData, márquelas como esenciales en las pruebas.</span><span class="sxs-lookup"><span data-stu-id="39000-199">To preserve non-essential cookies, such as those used by the TempData provider, mark them as essential in your tests.</span></span> <span data-ttu-id="39000-200">Para obtener instrucciones sobre cómo marcar una cookie como esencial, vea [Cookies esenciales](xref:security/gdpr#essential-cookies).</span><span class="sxs-lookup"><span data-stu-id="39000-200">For instructions on marking a cookie as essential, see [Essential cookies](xref:security/gdpr#essential-cookies).</span></span>

## <a name="customize-webapplicationfactory"></a><span data-ttu-id="39000-201">Personalización de WebApplicationFactory</span><span class="sxs-lookup"><span data-stu-id="39000-201">Customize WebApplicationFactory</span></span>

<span data-ttu-id="39000-202">La configuración de host web se puede crear independientemente de las clases de prueba al heredar de `WebApplicationFactory` para crear una o más fábricas personalizadas:</span><span class="sxs-lookup"><span data-stu-id="39000-202">Web host configuration can be created independently of the test classes by inheriting from `WebApplicationFactory` to create one or more custom factories:</span></span>

1. <span data-ttu-id="39000-203">Herede de `WebApplicationFactory` e invalide [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost).</span><span class="sxs-lookup"><span data-stu-id="39000-203">Inherit from `WebApplicationFactory` and override [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost).</span></span> <span data-ttu-id="39000-204">[IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) permite la configuración de la colección de servicios con [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices):</span><span class="sxs-lookup"><span data-stu-id="39000-204">The [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) allows the configuration of the service collection with [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices):</span></span>

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   <span data-ttu-id="39000-205">El método `InitializeDbForTests` realiza la propagación de la base de datos de la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples).</span><span class="sxs-lookup"><span data-stu-id="39000-205">Database seeding in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is performed by the `InitializeDbForTests` method.</span></span> <span data-ttu-id="39000-206">El método se describe en la sección [Ejemplo de pruebas de integración: organización de la aplicación de prueba](#test-app-organization).</span><span class="sxs-lookup"><span data-stu-id="39000-206">The method is described in the [Integration tests sample: Test app organization](#test-app-organization) section.</span></span>

   <span data-ttu-id="39000-207">El contexto de la base de datos del SUT se registra en su método `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="39000-207">The SUT's database context is registered in its `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="39000-208">La devolución de llamada `builder.ConfigureServices` de la aplicación de prueba se ejecuta *después* de que se ejecute el código `Startup.ConfigureServices` de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="39000-208">The test app's `builder.ConfigureServices` callback is executed *after* the app's `Startup.ConfigureServices` code is executed.</span></span> <span data-ttu-id="39000-209">El orden de ejecución es un cambio importante para el [host genérico](xref:fundamentals/host/generic-host) con la versión de ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="39000-209">The execution order is a breaking change for the [Generic Host](xref:fundamentals/host/generic-host) with the release of ASP.NET Core 3.0.</span></span> <span data-ttu-id="39000-210">Para usar una base de datos diferente para las pruebas a la base de datos de la aplicación, el contexto de la base de datos de la aplicación debe reemplazarse en `builder.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="39000-210">To use a different database for the tests than the app's database, the app's database context must be replaced in `builder.ConfigureServices`.</span></span>

   <span data-ttu-id="39000-211">En el caso de sistemas a prueba que todavía usan el [host web}(xref:fundamentals/host/web-host), la devolución de llamada `builder.ConfigureServices` de la aplicación de prueba se ejecuta *antes que* el código `Startup.ConfigureServices` del sistema a prueba.</span><span class="sxs-lookup"><span data-stu-id="39000-211">For SUTs that still use the [Web Host}(xref:fundamentals/host/web-host), the test app's `builder.ConfigureServices` callback is executed *before* the SUT's `Startup.ConfigureServices` code.</span></span> <span data-ttu-id="39000-212">La devolución de llamada `builder.ConfigureTestServices` de la aplicación de prueba se ejecuta *después*.</span><span class="sxs-lookup"><span data-stu-id="39000-212">The test app's `builder.ConfigureTestServices` callback is executed *after*.</span></span>

   <span data-ttu-id="39000-213">La aplicación de ejemplo busca el descriptor de servicio del contexto de la base de datos y usa el descriptor para quitar el registro del servicio.</span><span class="sxs-lookup"><span data-stu-id="39000-213">The sample app finds the service descriptor for the database context and uses the descriptor to remove the service registration.</span></span> <span data-ttu-id="39000-214">Luego, la fábrica agrega una nueva instancia de `ApplicationDbContext` que usa una base de datos en memoria para las pruebas.</span><span class="sxs-lookup"><span data-stu-id="39000-214">Next, the factory adds a new `ApplicationDbContext` that uses an in-memory database for the tests.</span></span>

   <span data-ttu-id="39000-215">Para conectarse a una base de datos diferente a la base de datos en memoria, cambie la llamada a `UseInMemoryDatabase` para conectar el contexto a otra base de datos.</span><span class="sxs-lookup"><span data-stu-id="39000-215">To connect to a different database than the in-memory database, change the `UseInMemoryDatabase` call to connect the context to a different database.</span></span> <span data-ttu-id="39000-216">Para usar una base de datos de prueba de SQL Server:</span><span class="sxs-lookup"><span data-stu-id="39000-216">To use a SQL Server test database:</span></span>

   * <span data-ttu-id="39000-217">Haga referencia al paquete NuGet [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/) del archivo de proyecto.</span><span class="sxs-lookup"><span data-stu-id="39000-217">Reference the [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/) NuGet package in the project file.</span></span>
   * <span data-ttu-id="39000-218">Llame a `UseSqlServer` con una cadena de conexión a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="39000-218">Call `UseSqlServer` with a connection string to the database.</span></span>

   ```csharp
   services.AddDbContext<ApplicationDbContext>((options, context) => 
   {
       context.UseSqlServer(
           Configuration.GetConnectionString("TestingDbConnectionString"));
   });
   ```

2. <span data-ttu-id="39000-219">Use la instancia de `CustomWebApplicationFactory` personalizada en las clases de prueba.</span><span class="sxs-lookup"><span data-stu-id="39000-219">Use the custom `CustomWebApplicationFactory` in test classes.</span></span> <span data-ttu-id="39000-220">En el ejemplo siguiente se usa la fábrica en la clase `IndexPageTests`:</span><span class="sxs-lookup"><span data-stu-id="39000-220">The following example uses the factory in the `IndexPageTests` class:</span></span>

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   <span data-ttu-id="39000-221">El cliente de la aplicación de ejemplo está configurado para evitar que `HttpClient` siga los redireccionamientos.</span><span class="sxs-lookup"><span data-stu-id="39000-221">The sample app's client is configured to prevent the `HttpClient` from following redirects.</span></span> <span data-ttu-id="39000-222">Como se explica más adelante en la sección [Autenticación ficticia](#mock-authentication), esto permite que las pruebas comprueben el resultado de la primera respuesta de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="39000-222">As explained later in the [Mock authentication](#mock-authentication) section, this permits tests to check the result of the app's first response.</span></span> <span data-ttu-id="39000-223">La primera respuesta es un redireccionamiento en muchas de estas pruebas con un encabezado `Location`.</span><span class="sxs-lookup"><span data-stu-id="39000-223">The first response is a redirect in many of these tests with a `Location` header.</span></span>

3. <span data-ttu-id="39000-224">Una prueba típica usa los métodos auxiliares y `HttpClient` para procesar la solicitud y la respuesta:</span><span class="sxs-lookup"><span data-stu-id="39000-224">A typical test uses the `HttpClient` and helper methods to process the request and the response:</span></span>

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

<span data-ttu-id="39000-225">Cualquier solicitud POST al SUT debe satisfacer la comprobación antifalsificación que realiza automáticamente el [sistema antifalsificación de protección de datos](xref:security/data-protection/introduction) de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="39000-225">Any POST request to the SUT must satisfy the antiforgery check that's automatically made by the app's [data protection antiforgery system](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="39000-226">Para organizar la solicitud POST de una prueba, la aplicación de prueba debe:</span><span class="sxs-lookup"><span data-stu-id="39000-226">In order to arrange for a test's POST request, the test app must:</span></span>

1. <span data-ttu-id="39000-227">Realizar una solicitud para la página.</span><span class="sxs-lookup"><span data-stu-id="39000-227">Make a request for the page.</span></span>
1. <span data-ttu-id="39000-228">Analizar la cookie antifalsificación y solicitar el token de validación de la respuesta.</span><span class="sxs-lookup"><span data-stu-id="39000-228">Parse the antiforgery cookie and request validation token from the response.</span></span>
1. <span data-ttu-id="39000-229">Realizar la solicitud POST con la cookie antifalsificación y el token de validación de la solicitud aplicados.</span><span class="sxs-lookup"><span data-stu-id="39000-229">Make the POST request with the antiforgery cookie and request validation token in place.</span></span>

<span data-ttu-id="39000-230">Los métodos de extensión auxiliares `SendAsync` (*Helpers/HttpClientExtensions.cs*) y el método auxiliar `GetDocumentAsync` (*Helpers/HtmlHelpers.cs*) de la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) usan el analizador [AngleSharp](https://anglesharp.github.io/) para controlar la comprobación antifalsificación con los métodos siguientes:</span><span class="sxs-lookup"><span data-stu-id="39000-230">The `SendAsync` helper extension methods (*Helpers/HttpClientExtensions.cs*) and the `GetDocumentAsync` helper method (*Helpers/HtmlHelpers.cs*) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) use the [AngleSharp](https://anglesharp.github.io/) parser to handle the antiforgery check with the following methods:</span></span>

* <span data-ttu-id="39000-231">`GetDocumentAsync` &ndash; Recibe [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) y devuelve una instancia de `IHtmlDocument`.</span><span class="sxs-lookup"><span data-stu-id="39000-231">`GetDocumentAsync` &ndash; Receives the [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) and returns an `IHtmlDocument`.</span></span> <span data-ttu-id="39000-232">`GetDocumentAsync` usa una fábrica que prepara una *respuesta virtual* basada en la instancia de `HttpResponseMessage` original.</span><span class="sxs-lookup"><span data-stu-id="39000-232">`GetDocumentAsync` uses a factory that prepares a *virtual response* based on the original `HttpResponseMessage`.</span></span> <span data-ttu-id="39000-233">Para obtener más información, vea la [documentación de AngleSharp](https://github.com/AngleSharp/AngleSharp#documentation).</span><span class="sxs-lookup"><span data-stu-id="39000-233">For more information, see the [AngleSharp documentation](https://github.com/AngleSharp/AngleSharp#documentation).</span></span>
* <span data-ttu-id="39000-234">Los métodos de extensión `SendAsync` de `HttpClient` componen una instancia de [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) y llaman a [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) para enviar solicitudes al SUT.</span><span class="sxs-lookup"><span data-stu-id="39000-234">`SendAsync` extension methods for the `HttpClient` compose an [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) and call [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) to submit requests to the SUT.</span></span> <span data-ttu-id="39000-235">Las sobrecargas de `SendAsync` aceptan el formulario HTML (`IHtmlFormElement`) y lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="39000-235">Overloads for `SendAsync` accept the HTML form (`IHtmlFormElement`) and the following:</span></span>
  * <span data-ttu-id="39000-236">Botón Enviar del formulario (`IHtmlElement`)</span><span class="sxs-lookup"><span data-stu-id="39000-236">Submit button of the form (`IHtmlElement`)</span></span>
  * <span data-ttu-id="39000-237">Colección de valores del formulario (`IEnumerable<KeyValuePair<string, string>>`)</span><span class="sxs-lookup"><span data-stu-id="39000-237">Form values collection (`IEnumerable<KeyValuePair<string, string>>`)</span></span>
  * <span data-ttu-id="39000-238">Botón Enviar (`IHtmlElement`) y valores del formulario (`IEnumerable<KeyValuePair<string, string>>`)</span><span class="sxs-lookup"><span data-stu-id="39000-238">Submit button (`IHtmlElement`) and form values (`IEnumerable<KeyValuePair<string, string>>`)</span></span>

> [!NOTE]
> <span data-ttu-id="39000-239">[AngleSharp](https://anglesharp.github.io/) es una biblioteca de análisis de terceros que se usa con fines de demostración en este tema y en la aplicación de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="39000-239">[AngleSharp](https://anglesharp.github.io/) is a third-party parsing library used for demonstration purposes in this topic and the sample app.</span></span> <span data-ttu-id="39000-240">AngleSharp no es compatible con las pruebas de integración de aplicaciones ASP.NET Core ni necesario.</span><span class="sxs-lookup"><span data-stu-id="39000-240">AngleSharp isn't supported or required for integration testing of ASP.NET Core apps.</span></span> <span data-ttu-id="39000-241">Se pueden usar otros analizadores, como [Html Agility Pack (HAP)](https://html-agility-pack.net/).</span><span class="sxs-lookup"><span data-stu-id="39000-241">Other parsers can be used, such as the [Html Agility Pack (HAP)](https://html-agility-pack.net/).</span></span> <span data-ttu-id="39000-242">Otro enfoque consiste en escribir código para controlar el token de comprobación de solicitudes y la cookie antifalsificación del sistema antifalsificación directamente.</span><span class="sxs-lookup"><span data-stu-id="39000-242">Another approach is to write code to handle the antiforgery system's request verification token and antiforgery cookie directly.</span></span>

## <a name="customize-the-client-with-withwebhostbuilder"></a><span data-ttu-id="39000-243">Personalización del cliente con WithWebHostBuilder</span><span class="sxs-lookup"><span data-stu-id="39000-243">Customize the client with WithWebHostBuilder</span></span>

<span data-ttu-id="39000-244">Cuando se requiere configuración adicional en un método de prueba, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) crea una nueva instancia de `WebApplicationFactory` con un elemento [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) que se personaliza aún más mediante la configuración.</span><span class="sxs-lookup"><span data-stu-id="39000-244">When additional configuration is required within a test method, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) creates a new `WebApplicationFactory` with an [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) that is further customized by configuration.</span></span>

<span data-ttu-id="39000-245">El método de prueba `Post_DeleteMessageHandler_ReturnsRedirectToRoot` de la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) muestra el uso de `WithWebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="39000-245">The `Post_DeleteMessageHandler_ReturnsRedirectToRoot` test method of the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) demonstrates the use of `WithWebHostBuilder`.</span></span> <span data-ttu-id="39000-246">Esta prueba realiza una eliminación de registros en la base de datos al desencadenar un envío de formulario en el SUT.</span><span class="sxs-lookup"><span data-stu-id="39000-246">This test performs a record delete in the database by triggering a form submission in the SUT.</span></span>

<span data-ttu-id="39000-247">Dado que otra prueba en la clase `IndexPageTests` realiza una operación que elimina todos los registros de la base de datos y puede ejecutarse antes que el método `Post_DeleteMessageHandler_ReturnsRedirectToRoot`, la base de datos se vuelve a propagar en este método de prueba para asegurarse de que un registro está presente para que lo elimine el SUT.</span><span class="sxs-lookup"><span data-stu-id="39000-247">Because another test in the `IndexPageTests` class performs an operation that deletes all of the records in the database and may run before the `Post_DeleteMessageHandler_ReturnsRedirectToRoot` method, the database is reseeded in this test method to ensure that a record is present for the SUT to delete.</span></span> <span data-ttu-id="39000-248">La selección del primer botón de eliminación del formulario `messages` en el SUT se simula en la solicitud al SUT:</span><span class="sxs-lookup"><span data-stu-id="39000-248">Selecting the first delete button of the `messages` form in the SUT is simulated in the request to the SUT:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a><span data-ttu-id="39000-249">Opciones de cliente</span><span class="sxs-lookup"><span data-stu-id="39000-249">Client options</span></span>

<span data-ttu-id="39000-250">En la tabla siguiente se muestran los elementos [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) predeterminados disponibles al crear instancias de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="39000-250">The following table shows the default [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) available when creating `HttpClient` instances.</span></span>

| <span data-ttu-id="39000-251">Opción</span><span class="sxs-lookup"><span data-stu-id="39000-251">Option</span></span> | <span data-ttu-id="39000-252">Descripción</span><span class="sxs-lookup"><span data-stu-id="39000-252">Description</span></span> | <span data-ttu-id="39000-253">Default</span><span class="sxs-lookup"><span data-stu-id="39000-253">Default</span></span> |
| ---
<span data-ttu-id="39000-254">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="39000-254">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39000-255">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="39000-255">'Blazor'</span></span>
- <span data-ttu-id="39000-256">"Identity"</span><span class="sxs-lookup"><span data-stu-id="39000-256">'Identity'</span></span>
- <span data-ttu-id="39000-257">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="39000-257">'Let's Encrypt'</span></span>
- <span data-ttu-id="39000-258">"Razor"</span><span class="sxs-lookup"><span data-stu-id="39000-258">'Razor'</span></span>
- <span data-ttu-id="39000-259">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="39000-259">'SignalR' uid:</span></span> 

<span data-ttu-id="39000-260">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="39000-260">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39000-261">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="39000-261">'Blazor'</span></span>
- <span data-ttu-id="39000-262">"Identity"</span><span class="sxs-lookup"><span data-stu-id="39000-262">'Identity'</span></span>
- <span data-ttu-id="39000-263">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="39000-263">'Let's Encrypt'</span></span>
- <span data-ttu-id="39000-264">"Razor"</span><span class="sxs-lookup"><span data-stu-id="39000-264">'Razor'</span></span>
- <span data-ttu-id="39000-265">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="39000-265">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39000-266">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="39000-266">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39000-267">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="39000-267">'Blazor'</span></span>
- <span data-ttu-id="39000-268">"Identity"</span><span class="sxs-lookup"><span data-stu-id="39000-268">'Identity'</span></span>
- <span data-ttu-id="39000-269">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="39000-269">'Let's Encrypt'</span></span>
- <span data-ttu-id="39000-270">"Razor"</span><span class="sxs-lookup"><span data-stu-id="39000-270">'Razor'</span></span>
- <span data-ttu-id="39000-271">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="39000-271">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39000-272">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="39000-272">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39000-273">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="39000-273">'Blazor'</span></span>
- <span data-ttu-id="39000-274">"Identity"</span><span class="sxs-lookup"><span data-stu-id="39000-274">'Identity'</span></span>
- <span data-ttu-id="39000-275">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="39000-275">'Let's Encrypt'</span></span>
- <span data-ttu-id="39000-276">"Razor"</span><span class="sxs-lookup"><span data-stu-id="39000-276">'Razor'</span></span>
- <span data-ttu-id="39000-277">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="39000-277">'SignalR' uid:</span></span> 

<span data-ttu-id="39000-278">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="39000-278">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39000-279">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="39000-279">'Blazor'</span></span>
- <span data-ttu-id="39000-280">"Identity"</span><span class="sxs-lookup"><span data-stu-id="39000-280">'Identity'</span></span>
- <span data-ttu-id="39000-281">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="39000-281">'Let's Encrypt'</span></span>
- <span data-ttu-id="39000-282">"Razor"</span><span class="sxs-lookup"><span data-stu-id="39000-282">'Razor'</span></span>
- <span data-ttu-id="39000-283">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="39000-283">'SignalR' uid:</span></span> 

<span data-ttu-id="39000-284">---- | | [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | Obtiene o establece si las instancias `HttpClient` deben seguir automáticamente las respuestas de redireccionamiento.</span><span class="sxs-lookup"><span data-stu-id="39000-284">---- | | [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | Gets or sets whether or not `HttpClient` instances should automatically follow redirect responses.</span></span><span data-ttu-id="39000-285"> | `true` | | [BaseAddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | Obtiene o establece la dirección base de las instancias `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="39000-285"> | `true` | | [BaseAddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | Gets or sets the base address of `HttpClient` instances.</span></span><span data-ttu-id="39000-286"> | `http://localhost` | | [HandleCookies](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | Obtiene o establece si las instancias `HttpClient` deben administrar las cookies.</span><span class="sxs-lookup"><span data-stu-id="39000-286"> | `http://localhost` | | [HandleCookies](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | Gets or sets whether `HttpClient` instances should handle cookies.</span></span><span data-ttu-id="39000-287"> | `true` | | [MaxAutomaticRedirections](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | Obtiene o establece el número máximo de respuestas de redireccionamiento que deben seguir las instancias `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="39000-287"> | `true` | | [MaxAutomaticRedirections](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | Gets or sets the maximum number of redirect responses that `HttpClient` instances should follow.</span></span> <span data-ttu-id="39000-288">| 7 |</span><span class="sxs-lookup"><span data-stu-id="39000-288">| 7 |</span></span>

<span data-ttu-id="39000-289">Cree la clase `WebApplicationFactoryClientOptions` y pásela al método [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) (los valores predeterminados se muestran en el ejemplo de código):</span><span class="sxs-lookup"><span data-stu-id="39000-289">Create the `WebApplicationFactoryClientOptions` class and pass it to the [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) method (default values are shown in the code example):</span></span>

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a><span data-ttu-id="39000-290">Inserción de servicios ficticios</span><span class="sxs-lookup"><span data-stu-id="39000-290">Inject mock services</span></span>

<span data-ttu-id="39000-291">Los servicios se pueden invalidar en una prueba con una llamada a [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) en el generador de hosts.</span><span class="sxs-lookup"><span data-stu-id="39000-291">Services can be overridden in a test with a call to [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) on the host builder.</span></span> <span data-ttu-id="39000-292">**Para insertar servicios ficticios, el SUT debe tener una clase `Startup` con un método `Startup.ConfigureServices`.**</span><span class="sxs-lookup"><span data-stu-id="39000-292">**To inject mock services, the SUT must have a `Startup` class with a `Startup.ConfigureServices` method.**</span></span>

<span data-ttu-id="39000-293">El SUT de ejemplo incluye un servicio con ámbito que devuelve una cita.</span><span class="sxs-lookup"><span data-stu-id="39000-293">The sample SUT includes a scoped service that returns a quote.</span></span> <span data-ttu-id="39000-294">La cita se inserta en un campo oculto de la página Index cuando se solicita esta.</span><span class="sxs-lookup"><span data-stu-id="39000-294">The quote is embedded in a hidden field on the Index page when the Index page is requested.</span></span>

<span data-ttu-id="39000-295">*Services/IQuoteService.cs*:</span><span class="sxs-lookup"><span data-stu-id="39000-295">*Services/IQuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

<span data-ttu-id="39000-296">*Services/QuoteService.cs*:</span><span class="sxs-lookup"><span data-stu-id="39000-296">*Services/QuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

<span data-ttu-id="39000-297">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="39000-297">*Startup.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

<span data-ttu-id="39000-298">*Páginas/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="39000-298">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

<span data-ttu-id="39000-299">*Pages/Index.cs*:</span><span class="sxs-lookup"><span data-stu-id="39000-299">*Pages/Index.cs*:</span></span>

[!code-cshtml[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

<span data-ttu-id="39000-300">El siguiente marcado se genera cuando se ejecuta la aplicación SUT:</span><span class="sxs-lookup"><span data-stu-id="39000-300">The following markup is generated when the SUT app is run:</span></span>

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

<span data-ttu-id="39000-301">Para probar el servicio y la inserción de citas en una prueba de integración, la prueba inserta un servicio ficticio en el SUT.</span><span class="sxs-lookup"><span data-stu-id="39000-301">To test the service and quote injection in an integration test, a mock service is injected into the SUT by the test.</span></span> <span data-ttu-id="39000-302">El servicio ficticio reemplaza el elemento `QuoteService` de la aplicación por un servicio proporcionado por la aplicación de prueba, denominado `TestQuoteService`:</span><span class="sxs-lookup"><span data-stu-id="39000-302">The mock service replaces the app's `QuoteService` with a service provided by the test app, called `TestQuoteService`:</span></span>

<span data-ttu-id="39000-303">*IntegrationTests.IndexPageTests.cs*:</span><span class="sxs-lookup"><span data-stu-id="39000-303">*IntegrationTests.IndexPageTests.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

<span data-ttu-id="39000-304">Se llama a `ConfigureTestServices` y se registra el servicio con ámbito:</span><span class="sxs-lookup"><span data-stu-id="39000-304">`ConfigureTestServices` is called, and the scoped service is registered:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

<span data-ttu-id="39000-305">El marcado producido durante la ejecución de la prueba refleja el texto de la cita proporcionado por `TestQuoteService`, por lo que la aserción pasa:</span><span class="sxs-lookup"><span data-stu-id="39000-305">The markup produced during the test's execution reflects the quote text supplied by `TestQuoteService`, thus the assertion passes:</span></span>

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="mock-authentication"></a><span data-ttu-id="39000-306">Autenticación ficticia</span><span class="sxs-lookup"><span data-stu-id="39000-306">Mock authentication</span></span>

<span data-ttu-id="39000-307">Las pruebas de la clase `AuthTests` comprueban que un punto de conexión seguro:</span><span class="sxs-lookup"><span data-stu-id="39000-307">Tests in the `AuthTests` class check that a secure endpoint:</span></span>

* <span data-ttu-id="39000-308">Redirige a un usuario no autenticado a la página de inicio de sesión de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="39000-308">Redirects an unauthenticated user to the app's Login page.</span></span>
* <span data-ttu-id="39000-309">Devuelve el contenido de un usuario autenticado.</span><span class="sxs-lookup"><span data-stu-id="39000-309">Returns content for an authenticated user.</span></span>

<span data-ttu-id="39000-310">En el SUT, la página `/SecurePage` usa una convención [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) para aplicar un elemento [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) a la página.</span><span class="sxs-lookup"><span data-stu-id="39000-310">In the SUT, the `/SecurePage` page uses an [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) convention to apply an [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) to the page.</span></span> <span data-ttu-id="39000-311">Para más información, consulte las convenciones de autorización de [Razor Pages](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).</span><span class="sxs-lookup"><span data-stu-id="39000-311">For more information, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

<span data-ttu-id="39000-312">En la prueba `Get_SecurePageRedirectsAnUnauthenticatedUser`, [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) se establece de modo que no permita los redireccionamientos al establecer [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) en `false`:</span><span class="sxs-lookup"><span data-stu-id="39000-312">In the `Get_SecurePageRedirectsAnUnauthenticatedUser` test, a [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) is set to disallow redirects by setting [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) to `false`:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet2)]

<span data-ttu-id="39000-313">Al no permitir que el cliente siga el redireccionamiento, se pueden realizar las siguientes comprobaciones:</span><span class="sxs-lookup"><span data-stu-id="39000-313">By disallowing the client to follow the redirect, the following checks can be made:</span></span>

* <span data-ttu-id="39000-314">El código de estado devuelto por el SUT puede comprobarse con el resultado esperado [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode), no el código de estado final después del redireccionamiento a la página de inicio de sesión, que sería [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode).</span><span class="sxs-lookup"><span data-stu-id="39000-314">The status code returned by the SUT can be checked against the expected [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode) result, not the final status code after the redirect to the Login page, which would be [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode).</span></span>
* <span data-ttu-id="39000-315">Se comprueba el valor del encabezado `Location` en los encabezados de respuesta para confirmar que empieza con `http://localhost/Identity/Account/Login`, no la respuesta de la página de inicio de sesión final, donde el encabezado `Location` no estaría presente.</span><span class="sxs-lookup"><span data-stu-id="39000-315">The `Location` header value in the response headers is checked to confirm that it starts with `http://localhost/Identity/Account/Login`, not the final Login page response, where the `Location` header wouldn't be present.</span></span>

<span data-ttu-id="39000-316">La aplicación de prueba puede simular un elemento <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> en [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) para probar aspectos de autenticación y autorización.</span><span class="sxs-lookup"><span data-stu-id="39000-316">The test app can mock an <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> in [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) in order to test aspects of authentication and authorization.</span></span> <span data-ttu-id="39000-317">Un escenario mínimo devuelve [AuthenticateResult.Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*):</span><span class="sxs-lookup"><span data-stu-id="39000-317">A minimal scenario returns an [AuthenticateResult.Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*):</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet4&highlight=11-18)]

<span data-ttu-id="39000-318">Se llama a `TestAuthHandler` para autenticar a un usuario cuando el esquema de autenticación está establecido en `Test`, donde `AddAuthentication` está registrado para `ConfigureTestServices`:</span><span class="sxs-lookup"><span data-stu-id="39000-318">The `TestAuthHandler` is called to authenticate a user when the authentication scheme is set to `Test` where `AddAuthentication` is registered for `ConfigureTestServices`:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet3&highlight=7-12)]

<span data-ttu-id="39000-319">Para obtener más información sobre `WebApplicationFactoryClientOptions`, vea la sección [Opciones de cliente](#client-options).</span><span class="sxs-lookup"><span data-stu-id="39000-319">For more information on `WebApplicationFactoryClientOptions`, see the [Client options](#client-options) section.</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="39000-320">Establecimiento del entorno</span><span class="sxs-lookup"><span data-stu-id="39000-320">Set the environment</span></span>

<span data-ttu-id="39000-321">De forma predeterminada, el entorno de host y aplicación del SUT se configura para usar el entorno Desarrollo.</span><span class="sxs-lookup"><span data-stu-id="39000-321">By default, the SUT's host and app environment is configured to use the Development environment.</span></span> <span data-ttu-id="39000-322">Para invalidar el entorno del sistema a prueba al usar `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="39000-322">To override the SUT's environment when using `IHostBuilder`:</span></span>

* <span data-ttu-id="39000-323">Establezca la variable de entorno `ASPNETCORE_ENVIRONMENT` (por ejemplo, `Staging`, `Production` u otro valor personalizado, como `Testing`).</span><span class="sxs-lookup"><span data-stu-id="39000-323">Set the `ASPNETCORE_ENVIRONMENT` environment variable (for example, `Staging`, `Production`, or other custom value, such as `Testing`).</span></span>
* <span data-ttu-id="39000-324">Invalide `CreateHostBuilder` en la aplicación de prueba para leer las variables de entorno con el prefijo `ASPNETCORE`.</span><span class="sxs-lookup"><span data-stu-id="39000-324">Override `CreateHostBuilder` in the test app to read environment variables prefixed with `ASPNETCORE`.</span></span>

```csharp
protected override IHostBuilder CreateHostBuilder() =>
    base.CreateHostBuilder()
        .ConfigureHostConfiguration(
            config => config.AddEnvironmentVariables("ASPNETCORE"));
```

<span data-ttu-id="39000-325">Si el sistema a prueba usa el host web (`IWebHostBuilder`), invalide `CreateWebHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="39000-325">If the SUT uses the Web Host (`IWebHostBuilder`), override `CreateWebHostBuilder`:</span></span>

```csharp
protected override IWebHostBuilder CreateWebHostBuilder() =>
    base.CreateWebHostBuilder().UseEnvironment("Testing");
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a><span data-ttu-id="39000-326">Cómo deduce la infraestructura de prueba la ruta de acceso raíz del contenido de la aplicación</span><span class="sxs-lookup"><span data-stu-id="39000-326">How the test infrastructure infers the app content root path</span></span>

<span data-ttu-id="39000-327">El constructor `WebApplicationFactory` deduce la ruta de acceso [raíz del contenido](xref:fundamentals/index#content-root) de la aplicación al buscar un elemento [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) en el ensamblado que contiene las pruebas de integración con una clave igual a `System.Reflection.Assembly.FullName` del ensamblado `TEntryPoint`.</span><span class="sxs-lookup"><span data-stu-id="39000-327">The `WebApplicationFactory` constructor infers the app [content root](xref:fundamentals/index#content-root) path by searching for a [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) on the assembly containing the integration tests with a key equal to the `TEntryPoint` assembly `System.Reflection.Assembly.FullName`.</span></span> <span data-ttu-id="39000-328">En caso de que no se encuentre la clave correcta, `WebApplicationFactory` vuelve a buscar un archivo de solución ( *.sln*) y anexa el nombre de ensamblado `TEntryPoint` al directorio de la solución.</span><span class="sxs-lookup"><span data-stu-id="39000-328">In case an attribute with the correct key isn't found, `WebApplicationFactory` falls back to searching for a solution file (*.sln*) and appends the `TEntryPoint` assembly name to the solution directory.</span></span> <span data-ttu-id="39000-329">El directorio raíz de la aplicación (la ruta de acceso raíz del contenido) se usa para detectar los archivos de contenido y las vistas.</span><span class="sxs-lookup"><span data-stu-id="39000-329">The app root directory (the content root path) is used to discover views and content files.</span></span>

## <a name="disable-shadow-copying"></a><span data-ttu-id="39000-330">Deshabilitación de la creación de instantáneas</span><span class="sxs-lookup"><span data-stu-id="39000-330">Disable shadow copying</span></span>

<span data-ttu-id="39000-331">La creación de instantáneas hace que las pruebas se ejecuten en un directorio diferente al de salida.</span><span class="sxs-lookup"><span data-stu-id="39000-331">Shadow copying causes the tests to execute in a different directory than the output directory.</span></span> <span data-ttu-id="39000-332">Para que las pruebas funcionen correctamente, se debe deshabilitar la creación de instantáneas.</span><span class="sxs-lookup"><span data-stu-id="39000-332">For tests to work properly, shadow copying must be disabled.</span></span> <span data-ttu-id="39000-333">La [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) usa xUnit y deshabilita la creación de instantáneas para xUnit al incluir un archivo *xunit.runner.json* con la opción de configuración correcta.</span><span class="sxs-lookup"><span data-stu-id="39000-333">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) uses xUnit and disables shadow copying for xUnit by including an *xunit.runner.json* file with the correct configuration setting.</span></span> <span data-ttu-id="39000-334">Para obtener más información, vea [Configuración de xUnit con JSON](https://xunit.github.io/docs/configuring-with-json.html).</span><span class="sxs-lookup"><span data-stu-id="39000-334">For more information, see [Configuring xUnit with JSON](https://xunit.github.io/docs/configuring-with-json.html).</span></span>

<span data-ttu-id="39000-335">Agregue el archivo *xunit.runner.json* a la raíz del proyecto de prueba con el siguiente contenido:</span><span class="sxs-lookup"><span data-stu-id="39000-335">Add the *xunit.runner.json* file to root of the test project with the following content:</span></span>

```json
{
  "shadowCopy": false
}
```

## <a name="disposal-of-objects"></a><span data-ttu-id="39000-336">Eliminación de objetos</span><span class="sxs-lookup"><span data-stu-id="39000-336">Disposal of objects</span></span>

<span data-ttu-id="39000-337">Una vez ejecutadas las pruebas de la implementación `IClassFixture`, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) y [HttpClient](/dotnet/api/system.net.http.httpclient) se eliminan cuando xUnit quita [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1).</span><span class="sxs-lookup"><span data-stu-id="39000-337">After the tests of the `IClassFixture` implementation are executed, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) and [HttpClient](/dotnet/api/system.net.http.httpclient) are disposed when xUnit disposes of the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1).</span></span> <span data-ttu-id="39000-338">Si los objetos cuya instancia ha creado el desarrollador requieren eliminación, quítelos de la implementación `IClassFixture`.</span><span class="sxs-lookup"><span data-stu-id="39000-338">If objects instantiated by the developer require disposal, dispose of them in the `IClassFixture` implementation.</span></span> <span data-ttu-id="39000-339">Para obtener más información, vea [Implementar un método Dispose](/dotnet/standard/garbage-collection/implementing-dispose).</span><span class="sxs-lookup"><span data-stu-id="39000-339">For more information, see [Implementing a Dispose method](/dotnet/standard/garbage-collection/implementing-dispose).</span></span>

## <a name="integration-tests-sample"></a><span data-ttu-id="39000-340">Ejemplo de pruebas de integración</span><span class="sxs-lookup"><span data-stu-id="39000-340">Integration tests sample</span></span>

<span data-ttu-id="39000-341">La [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) se compone de dos aplicaciones:</span><span class="sxs-lookup"><span data-stu-id="39000-341">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is composed of two apps:</span></span>

| <span data-ttu-id="39000-342">Aplicación</span><span class="sxs-lookup"><span data-stu-id="39000-342">App</span></span> | <span data-ttu-id="39000-343">Directorio del proyecto</span><span class="sxs-lookup"><span data-stu-id="39000-343">Project directory</span></span> | <span data-ttu-id="39000-344">Descripción</span><span class="sxs-lookup"><span data-stu-id="39000-344">Description</span></span> |
| --- | ---
<span data-ttu-id="39000-345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="39000-345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39000-346">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="39000-346">'Blazor'</span></span>
- <span data-ttu-id="39000-347">"Identity"</span><span class="sxs-lookup"><span data-stu-id="39000-347">'Identity'</span></span>
- <span data-ttu-id="39000-348">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="39000-348">'Let's Encrypt'</span></span>
- <span data-ttu-id="39000-349">"Razor"</span><span class="sxs-lookup"><span data-stu-id="39000-349">'Razor'</span></span>
- <span data-ttu-id="39000-350">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="39000-350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39000-351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="39000-351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39000-352">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="39000-352">'Blazor'</span></span>
- <span data-ttu-id="39000-353">"Identity"</span><span class="sxs-lookup"><span data-stu-id="39000-353">'Identity'</span></span>
- <span data-ttu-id="39000-354">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="39000-354">'Let's Encrypt'</span></span>
- <span data-ttu-id="39000-355">"Razor"</span><span class="sxs-lookup"><span data-stu-id="39000-355">'Razor'</span></span>
- <span data-ttu-id="39000-356">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="39000-356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39000-357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="39000-357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39000-358">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="39000-358">'Blazor'</span></span>
- <span data-ttu-id="39000-359">"Identity"</span><span class="sxs-lookup"><span data-stu-id="39000-359">'Identity'</span></span>
- <span data-ttu-id="39000-360">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="39000-360">'Let's Encrypt'</span></span>
- <span data-ttu-id="39000-361">"Razor"</span><span class="sxs-lookup"><span data-stu-id="39000-361">'Razor'</span></span>
- <span data-ttu-id="39000-362">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="39000-362">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39000-363">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="39000-363">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39000-364">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="39000-364">'Blazor'</span></span>
- <span data-ttu-id="39000-365">"Identity"</span><span class="sxs-lookup"><span data-stu-id="39000-365">'Identity'</span></span>
- <span data-ttu-id="39000-366">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="39000-366">'Let's Encrypt'</span></span>
- <span data-ttu-id="39000-367">"Razor"</span><span class="sxs-lookup"><span data-stu-id="39000-367">'Razor'</span></span>
- <span data-ttu-id="39000-368">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="39000-368">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39000-369">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="39000-369">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39000-370">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="39000-370">'Blazor'</span></span>
- <span data-ttu-id="39000-371">"Identity"</span><span class="sxs-lookup"><span data-stu-id="39000-371">'Identity'</span></span>
- <span data-ttu-id="39000-372">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="39000-372">'Let's Encrypt'</span></span>
- <span data-ttu-id="39000-373">"Razor"</span><span class="sxs-lookup"><span data-stu-id="39000-373">'Razor'</span></span>
- <span data-ttu-id="39000-374">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="39000-374">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39000-375">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="39000-375">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39000-376">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="39000-376">'Blazor'</span></span>
- <span data-ttu-id="39000-377">"Identity"</span><span class="sxs-lookup"><span data-stu-id="39000-377">'Identity'</span></span>
- <span data-ttu-id="39000-378">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="39000-378">'Let's Encrypt'</span></span>
- <span data-ttu-id="39000-379">"Razor"</span><span class="sxs-lookup"><span data-stu-id="39000-379">'Razor'</span></span>
- <span data-ttu-id="39000-380">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="39000-380">'SignalR' uid:</span></span> 

<span data-ttu-id="39000-381">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="39000-381">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39000-382">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="39000-382">'Blazor'</span></span>
- <span data-ttu-id="39000-383">"Identity"</span><span class="sxs-lookup"><span data-stu-id="39000-383">'Identity'</span></span>
- <span data-ttu-id="39000-384">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="39000-384">'Let's Encrypt'</span></span>
- <span data-ttu-id="39000-385">"Razor"</span><span class="sxs-lookup"><span data-stu-id="39000-385">'Razor'</span></span>
- <span data-ttu-id="39000-386">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="39000-386">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39000-387">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="39000-387">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39000-388">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="39000-388">'Blazor'</span></span>
- <span data-ttu-id="39000-389">"Identity"</span><span class="sxs-lookup"><span data-stu-id="39000-389">'Identity'</span></span>
- <span data-ttu-id="39000-390">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="39000-390">'Let's Encrypt'</span></span>
- <span data-ttu-id="39000-391">"Razor"</span><span class="sxs-lookup"><span data-stu-id="39000-391">'Razor'</span></span>
- <span data-ttu-id="39000-392">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="39000-392">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39000-393">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="39000-393">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39000-394">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="39000-394">'Blazor'</span></span>
- <span data-ttu-id="39000-395">"Identity"</span><span class="sxs-lookup"><span data-stu-id="39000-395">'Identity'</span></span>
- <span data-ttu-id="39000-396">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="39000-396">'Let's Encrypt'</span></span>
- <span data-ttu-id="39000-397">"Razor"</span><span class="sxs-lookup"><span data-stu-id="39000-397">'Razor'</span></span>
- <span data-ttu-id="39000-398">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="39000-398">'SignalR' uid:</span></span> 

<span data-ttu-id="39000-399">------ | | Aplicación de mensaje (el sistema a prueba) | *src/RazorPagesProject* | Permite a un usuario agregar, analizar mensajes y eliminarlos todos o uno solo.</span><span class="sxs-lookup"><span data-stu-id="39000-399">------ | | Message app (the SUT) | *src/RazorPagesProject* | Allows a user to add, delete one, delete all, and analyze messages.</span></span> <span data-ttu-id="39000-400">| | Aplicación de prueba | *tests/RazorPagesProject.Tests* | Se usa para la prueba de integración del sistema a prueba.</span><span class="sxs-lookup"><span data-stu-id="39000-400">| | Test app | *tests/RazorPagesProject.Tests* | Used to integration test the SUT.</span></span> |

<span data-ttu-id="39000-401">Las pruebas se pueden ejecutar con las características de prueba integradas de un IDE, como [Visual Studio](https://visualstudio.microsoft.com).</span><span class="sxs-lookup"><span data-stu-id="39000-401">The tests can be run using the built-in test features of an IDE, such as [Visual Studio](https://visualstudio.microsoft.com).</span></span> <span data-ttu-id="39000-402">Si usa [Visual Studio Code](https://code.visualstudio.com/) o la línea de comandos, ejecute el siguiente comando en un símbolo del sistema del directorio *tests/RazorPagesProject.Tests*:</span><span class="sxs-lookup"><span data-stu-id="39000-402">If using [Visual Studio Code](https://code.visualstudio.com/) or the command line, execute the following command at a command prompt in the *tests/RazorPagesProject.Tests* directory:</span></span>

```console
dotnet test
```

### <a name="message-app-sut-organization"></a><span data-ttu-id="39000-403">Organización de aplicación de mensajes (SUT)</span><span class="sxs-lookup"><span data-stu-id="39000-403">Message app (SUT) organization</span></span>

<span data-ttu-id="39000-404">El sistema a prueba es un sistema de mensajes de Razor Pages con las siguientes características:</span><span class="sxs-lookup"><span data-stu-id="39000-404">The SUT is a Razor Pages message system with the following characteristics:</span></span>

* <span data-ttu-id="39000-405">La página Index de la aplicación (*Pages/Index.cshtml* y *Pages/Index.cshtml.cs*) proporciona una interfaz de usuario y métodos de modelo de página para controlar la adición, la eliminación y el análisis de mensajes (promedio de palabras por mensaje).</span><span class="sxs-lookup"><span data-stu-id="39000-405">The Index page of the app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) provides a UI and page model methods to control the addition, deletion, and analysis of messages (average words per message).</span></span>
* <span data-ttu-id="39000-406">La clase `Message` (*Data/Message.cs*) describe un mensaje con dos propiedades: `Id` (clave) y `Text` (mensaje).</span><span class="sxs-lookup"><span data-stu-id="39000-406">A message is described by the `Message` class (*Data/Message.cs*) with two properties: `Id` (key) and `Text` (message).</span></span> <span data-ttu-id="39000-407">Se necesita la propiedad `Text`, que está limitada a 200 caracteres.</span><span class="sxs-lookup"><span data-stu-id="39000-407">The `Text` property is required and limited to 200 characters.</span></span>
* <span data-ttu-id="39000-408">Los mensajes se almacenan mediante [Base de datos en memoria de Entity Framework](/ef/core/providers/in-memory/)&#8224;.</span><span class="sxs-lookup"><span data-stu-id="39000-408">Messages are stored using [Entity Framework's in-memory database](/ef/core/providers/in-memory/)&#8224;.</span></span>
* <span data-ttu-id="39000-409">La aplicación contiene una capa de acceso a datos (DAL) en su clase de contexto de base de datos, `AppDbContext` (*Data/AppDbContext.cs*).</span><span class="sxs-lookup"><span data-stu-id="39000-409">The app contains a data access layer (DAL) in its database context class, `AppDbContext` (*Data/AppDbContext.cs*).</span></span>
* <span data-ttu-id="39000-410">Si la base de datos está vacía al inicio de una aplicación, el almacén de mensajes se inicializa con tres mensajes.</span><span class="sxs-lookup"><span data-stu-id="39000-410">If the database is empty on app startup, the message store is initialized with three messages.</span></span>
* <span data-ttu-id="39000-411">La aplicación incluye un elemento `/SecurePage` al que solo puede acceder un usuario autenticado.</span><span class="sxs-lookup"><span data-stu-id="39000-411">The app includes a `/SecurePage` that can only be accessed by an authenticated user.</span></span>

<span data-ttu-id="39000-412">&#8224;En el tema de EF, [Pruebas con InMemory](/ef/core/miscellaneous/testing/in-memory), se explica cómo usar una base de datos en memoria para las pruebas con MSTest.</span><span class="sxs-lookup"><span data-stu-id="39000-412">&#8224;The EF topic, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explains how to use an in-memory database for tests with MSTest.</span></span> <span data-ttu-id="39000-413">En este tema se usa el marco de pruebas [xUnit](https://xunit.github.io/).</span><span class="sxs-lookup"><span data-stu-id="39000-413">This topic uses the [xUnit](https://xunit.github.io/) test framework.</span></span> <span data-ttu-id="39000-414">Los conceptos y las implementaciones de prueba de diferentes marcos de pruebas son similares, pero no idénticos.</span><span class="sxs-lookup"><span data-stu-id="39000-414">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span>

<span data-ttu-id="39000-415">Aunque la aplicación no usa el patrón del repositorio y no es un buen ejemplo del [patrón de unidad de trabajo (UoW)](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages admite estos patrones de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="39000-415">Although the app doesn't use the repository pattern and isn't an effective example of the [Unit of Work (UoW) pattern](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages supports these patterns of development.</span></span> <span data-ttu-id="39000-416">Para obtener más información, vea [Diseño del nivel de persistencia de infraestructura](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) y [Lógica del controlador de pruebas](/aspnet/core/mvc/controllers/testing) (el ejemplo implementa el patrón del repositorio).</span><span class="sxs-lookup"><span data-stu-id="39000-416">For more information, see [Designing the infrastructure persistence layer](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) and [Test controller logic](/aspnet/core/mvc/controllers/testing) (the sample implements the repository pattern).</span></span>

### <a name="test-app-organization"></a><span data-ttu-id="39000-417">Organización de la aplicación de prueba</span><span class="sxs-lookup"><span data-stu-id="39000-417">Test app organization</span></span>

<span data-ttu-id="39000-418">La aplicación de prueba es una aplicación de consola dentro del directorio *tests/RazorPagesProject.Tests*.</span><span class="sxs-lookup"><span data-stu-id="39000-418">The test app is a console app inside the *tests/RazorPagesProject.Tests* directory.</span></span>

| <span data-ttu-id="39000-419">Directorio de la aplicación de prueba</span><span class="sxs-lookup"><span data-stu-id="39000-419">Test app directory</span></span> | <span data-ttu-id="39000-420">Descripción</span><span class="sxs-lookup"><span data-stu-id="39000-420">Description</span></span> |
| ---
<span data-ttu-id="39000-421">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="39000-421">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39000-422">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="39000-422">'Blazor'</span></span>
- <span data-ttu-id="39000-423">"Identity"</span><span class="sxs-lookup"><span data-stu-id="39000-423">'Identity'</span></span>
- <span data-ttu-id="39000-424">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="39000-424">'Let's Encrypt'</span></span>
- <span data-ttu-id="39000-425">"Razor"</span><span class="sxs-lookup"><span data-stu-id="39000-425">'Razor'</span></span>
- <span data-ttu-id="39000-426">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="39000-426">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39000-427">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="39000-427">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39000-428">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="39000-428">'Blazor'</span></span>
- <span data-ttu-id="39000-429">"Identity"</span><span class="sxs-lookup"><span data-stu-id="39000-429">'Identity'</span></span>
- <span data-ttu-id="39000-430">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="39000-430">'Let's Encrypt'</span></span>
- <span data-ttu-id="39000-431">"Razor"</span><span class="sxs-lookup"><span data-stu-id="39000-431">'Razor'</span></span>
- <span data-ttu-id="39000-432">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="39000-432">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39000-433">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="39000-433">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39000-434">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="39000-434">'Blazor'</span></span>
- <span data-ttu-id="39000-435">"Identity"</span><span class="sxs-lookup"><span data-stu-id="39000-435">'Identity'</span></span>
- <span data-ttu-id="39000-436">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="39000-436">'Let's Encrypt'</span></span>
- <span data-ttu-id="39000-437">"Razor"</span><span class="sxs-lookup"><span data-stu-id="39000-437">'Razor'</span></span>
- <span data-ttu-id="39000-438">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="39000-438">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39000-439">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="39000-439">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39000-440">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="39000-440">'Blazor'</span></span>
- <span data-ttu-id="39000-441">"Identity"</span><span class="sxs-lookup"><span data-stu-id="39000-441">'Identity'</span></span>
- <span data-ttu-id="39000-442">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="39000-442">'Let's Encrypt'</span></span>
- <span data-ttu-id="39000-443">"Razor"</span><span class="sxs-lookup"><span data-stu-id="39000-443">'Razor'</span></span>
- <span data-ttu-id="39000-444">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="39000-444">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39000-445">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="39000-445">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39000-446">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="39000-446">'Blazor'</span></span>
- <span data-ttu-id="39000-447">"Identity"</span><span class="sxs-lookup"><span data-stu-id="39000-447">'Identity'</span></span>
- <span data-ttu-id="39000-448">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="39000-448">'Let's Encrypt'</span></span>
- <span data-ttu-id="39000-449">"Razor"</span><span class="sxs-lookup"><span data-stu-id="39000-449">'Razor'</span></span>
- <span data-ttu-id="39000-450">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="39000-450">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39000-451">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="39000-451">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39000-452">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="39000-452">'Blazor'</span></span>
- <span data-ttu-id="39000-453">"Identity"</span><span class="sxs-lookup"><span data-stu-id="39000-453">'Identity'</span></span>
- <span data-ttu-id="39000-454">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="39000-454">'Let's Encrypt'</span></span>
- <span data-ttu-id="39000-455">"Razor"</span><span class="sxs-lookup"><span data-stu-id="39000-455">'Razor'</span></span>
- <span data-ttu-id="39000-456">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="39000-456">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39000-457">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="39000-457">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39000-458">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="39000-458">'Blazor'</span></span>
- <span data-ttu-id="39000-459">"Identity"</span><span class="sxs-lookup"><span data-stu-id="39000-459">'Identity'</span></span>
- <span data-ttu-id="39000-460">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="39000-460">'Let's Encrypt'</span></span>
- <span data-ttu-id="39000-461">"Razor"</span><span class="sxs-lookup"><span data-stu-id="39000-461">'Razor'</span></span>
- <span data-ttu-id="39000-462">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="39000-462">'SignalR' uid:</span></span> 

<span data-ttu-id="39000-463">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="39000-463">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39000-464">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="39000-464">'Blazor'</span></span>
- <span data-ttu-id="39000-465">"Identity"</span><span class="sxs-lookup"><span data-stu-id="39000-465">'Identity'</span></span>
- <span data-ttu-id="39000-466">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="39000-466">'Let's Encrypt'</span></span>
- <span data-ttu-id="39000-467">"Razor"</span><span class="sxs-lookup"><span data-stu-id="39000-467">'Razor'</span></span>
- <span data-ttu-id="39000-468">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="39000-468">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39000-469">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="39000-469">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39000-470">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="39000-470">'Blazor'</span></span>
- <span data-ttu-id="39000-471">"Identity"</span><span class="sxs-lookup"><span data-stu-id="39000-471">'Identity'</span></span>
- <span data-ttu-id="39000-472">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="39000-472">'Let's Encrypt'</span></span>
- <span data-ttu-id="39000-473">"Razor"</span><span class="sxs-lookup"><span data-stu-id="39000-473">'Razor'</span></span>
- <span data-ttu-id="39000-474">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="39000-474">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39000-475">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="39000-475">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39000-476">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="39000-476">'Blazor'</span></span>
- <span data-ttu-id="39000-477">"Identity"</span><span class="sxs-lookup"><span data-stu-id="39000-477">'Identity'</span></span>
- <span data-ttu-id="39000-478">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="39000-478">'Let's Encrypt'</span></span>
- <span data-ttu-id="39000-479">"Razor"</span><span class="sxs-lookup"><span data-stu-id="39000-479">'Razor'</span></span>
- <span data-ttu-id="39000-480">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="39000-480">'SignalR' uid:</span></span> 

<span data-ttu-id="39000-481">------ | | *AuthTests* | Contiene métodos de prueba para:</span><span class="sxs-lookup"><span data-stu-id="39000-481">------ | | *AuthTests* | Contains test methods for:</span></span><ul><li><span data-ttu-id="39000-482">Acceder a una página segura mediante un usuario no autenticado.</span><span class="sxs-lookup"><span data-stu-id="39000-482">Accessing a secure page by an unauthenticated user.</span></span></li><li><span data-ttu-id="39000-483">Acceder a una página segura mediante un usuario autenticado con una simulación <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>.</span><span class="sxs-lookup"><span data-stu-id="39000-483">Accessing a secure page by an authenticated user with a mock <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>.</span></span></li><li><span data-ttu-id="39000-484">Obtener un perfil de usuario de GitHub y comprobar el inicio de sesión de usuario del perfil.</span><span class="sxs-lookup"><span data-stu-id="39000-484">Obtaining a GitHub user profile and checking the profile's user login.</span></span></li></ul> <span data-ttu-id="39000-485">| | *BasicTests* | Contiene un método de prueba para el enrutamiento y el tipo de contenido.</span><span class="sxs-lookup"><span data-stu-id="39000-485">| | *BasicTests* | Contains a test method for routing and content type.</span></span> <span data-ttu-id="39000-486">| | *IntegrationTests* | Contiene las pruebas de integración de la página Index que usa la clase `WebApplicationFactory` personalizada.</span><span class="sxs-lookup"><span data-stu-id="39000-486">| | *IntegrationTests* | Contains the integration tests for the Index page using custom `WebApplicationFactory` class.</span></span> <span data-ttu-id="39000-487">| | *Asistentes o utilidades* | </span><span class="sxs-lookup"><span data-stu-id="39000-487">| | *Helpers/Utilities* | </span></span><ul><li><span data-ttu-id="39000-488">*Utilities.cs* contiene el método `InitializeDbForTests` que se usa para propagar la base de datos con datos de prueba.</span><span class="sxs-lookup"><span data-stu-id="39000-488">*Utilities.cs* contains the `InitializeDbForTests` method used to seed the database with test data.</span></span></li><li><span data-ttu-id="39000-489">*HtmlHelpers.cs* proporciona un método para devolver un elemento `IHtmlDocument` de AngleSharp para que lo usen los métodos de prueba.</span><span class="sxs-lookup"><span data-stu-id="39000-489">*HtmlHelpers.cs* provides a method to return an AngleSharp `IHtmlDocument` for use by the test methods.</span></span></li><li><span data-ttu-id="39000-490">*HttpClientExtensions.cs* proporciona sobrecargas para que `SendAsync` envíe solicitudes al SUT.</span><span class="sxs-lookup"><span data-stu-id="39000-490">*HttpClientExtensions.cs* provide overloads for `SendAsync` to submit requests to the SUT.</span></span></li></ul> |

<span data-ttu-id="39000-491">El marco de pruebas es [xUnit](https://xunit.github.io/).</span><span class="sxs-lookup"><span data-stu-id="39000-491">The test framework is [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="39000-492">Las pruebas de integración se llevan a cabo con [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost), que incluye [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span><span class="sxs-lookup"><span data-stu-id="39000-492">Integration tests are conducted using the [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost), which includes the [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span></span> <span data-ttu-id="39000-493">Dado que el paquete [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) se usa para configurar el host de prueba y el servidor de pruebas, los paquetes `TestHost` y `TestServer` no requieren referencias de paquete directas en el archivo de proyecto o la configuración de desarrollador de la aplicación de prueba en la aplicación de prueba.</span><span class="sxs-lookup"><span data-stu-id="39000-493">Because the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package is used to configure the test host and test server, the `TestHost` and `TestServer` packages don't require direct package references in the test app's project file or developer configuration in the test app.</span></span>

<span data-ttu-id="39000-494">**Propagación de la base de datos para pruebas**</span><span class="sxs-lookup"><span data-stu-id="39000-494">**Seeding the database for testing**</span></span>

<span data-ttu-id="39000-495">Las pruebas de integración suelen requerir un pequeño conjunto de datos en la base de datos antes de la ejecución de la prueba.</span><span class="sxs-lookup"><span data-stu-id="39000-495">Integration tests usually require a small dataset in the database prior to the test execution.</span></span> <span data-ttu-id="39000-496">Por ejemplo, una prueba de eliminación consiste en la eliminación de un registro de base de datos, por lo que la base de datos debe tener al menos un registro para que la solicitud de eliminación se realice correctamente.</span><span class="sxs-lookup"><span data-stu-id="39000-496">For example, a delete test calls for a database record deletion, so the database must have at least one record for the delete request to succeed.</span></span>

<span data-ttu-id="39000-497">La aplicación de ejemplo propaga la base de datos con tres mensajes en *Utilities.cs* que las pruebas pueden usar al ejecutarse:</span><span class="sxs-lookup"><span data-stu-id="39000-497">The sample app seeds the database with three messages in *Utilities.cs* that tests can use when they execute:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

<span data-ttu-id="39000-498">El contexto de la base de datos del SUT se registra en su método `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="39000-498">The SUT's database context is registered in its `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="39000-499">La devolución de llamada `builder.ConfigureServices` de la aplicación de prueba se ejecuta *después* de que se ejecute el código `Startup.ConfigureServices` de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="39000-499">The test app's `builder.ConfigureServices` callback is executed *after* the app's `Startup.ConfigureServices` code is executed.</span></span> <span data-ttu-id="39000-500">Para usar otra base de datos para las pruebas, el contexto de la base de datos de la aplicación debe reemplazarse en `builder.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="39000-500">To use a different database for the tests, the app's database context must be replaced in `builder.ConfigureServices`.</span></span> <span data-ttu-id="39000-501">Para obtener más información, vea la sección [Personalización de WebApplicationFactory](#customize-webapplicationfactory).</span><span class="sxs-lookup"><span data-stu-id="39000-501">For more information, see the [Customize WebApplicationFactory](#customize-webapplicationfactory) section.</span></span>

<span data-ttu-id="39000-502">En el caso de sistemas a prueba que todavía usan el [host web}(xref:fundamentals/host/web-host), la devolución de llamada `builder.ConfigureServices` de la aplicación de prueba se ejecuta *antes que* el código `Startup.ConfigureServices` del sistema a prueba.</span><span class="sxs-lookup"><span data-stu-id="39000-502">For SUTs that still use the [Web Host}(xref:fundamentals/host/web-host), the test app's `builder.ConfigureServices` callback is executed *before* the SUT's `Startup.ConfigureServices` code.</span></span> <span data-ttu-id="39000-503">La devolución de llamada `builder.ConfigureTestServices` de la aplicación de prueba se ejecuta *después*.</span><span class="sxs-lookup"><span data-stu-id="39000-503">The test app's `builder.ConfigureTestServices` callback is executed *after*.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="39000-504">Las pruebas de integración garantizan que los componentes de una aplicación funcionan correctamente en un nivel que incluye la infraestructura auxiliar de la aplicación, como la base de datos, el sistema de archivos y la red.</span><span class="sxs-lookup"><span data-stu-id="39000-504">Integration tests ensure that an app's components function correctly at a level that includes the app's supporting infrastructure, such as the database, file system, and network.</span></span> <span data-ttu-id="39000-505">ASP.NET Core admite las pruebas de integración mediante un marco de pruebas unitarias con un host web de prueba y un servidor de pruebas en memoria.</span><span class="sxs-lookup"><span data-stu-id="39000-505">ASP.NET Core supports integration tests using a unit test framework with a test web host and an in-memory test server.</span></span>

<span data-ttu-id="39000-506">En este artículo se da por hecho un conocimiento básico de las pruebas unitarias.</span><span class="sxs-lookup"><span data-stu-id="39000-506">This topic assumes a basic understanding of unit tests.</span></span> <span data-ttu-id="39000-507">Si no está familiarizado con los conceptos de las pruebas, vea el tema [Pruebas unitaria en .NET Core y .NET Standard](/dotnet/core/testing/) y su contenido vinculado.</span><span class="sxs-lookup"><span data-stu-id="39000-507">If unfamiliar with test concepts, see the [Unit Testing in .NET Core and .NET Standard](/dotnet/core/testing/) topic and its linked content.</span></span>

<span data-ttu-id="39000-508">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="39000-508">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="39000-509">La aplicación de ejemplo es una aplicación de Razor Pages que da por hecho un conocimiento básico de Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="39000-509">The sample app is a Razor Pages app and assumes a basic understanding of Razor Pages.</span></span> <span data-ttu-id="39000-510">Si no está familiarizado con Razor Pages, consulte los temas siguientes:</span><span class="sxs-lookup"><span data-stu-id="39000-510">If unfamiliar with Razor Pages, see the following topics:</span></span>

* <span data-ttu-id="39000-511">[Introducción a Razor Pages](xref:razor-pages/index)</span><span class="sxs-lookup"><span data-stu-id="39000-511">[Introduction to Razor Pages](xref:razor-pages/index)</span></span>
* <span data-ttu-id="39000-512">[Primeros pasos con Razor Pages](xref:tutorials/razor-pages/razor-pages-start)</span><span class="sxs-lookup"><span data-stu-id="39000-512">[Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start)</span></span>
* <span data-ttu-id="39000-513">Pruebas unitarias de [Razor Pages](xref:test/razor-pages-tests)</span><span class="sxs-lookup"><span data-stu-id="39000-513">[Razor Pages unit tests](xref:test/razor-pages-tests)</span></span>

> [!NOTE]
> <span data-ttu-id="39000-514">Para probar SPA, se recomienda una herramienta como [Selenium](https://www.seleniumhq.org/), que puede automatizar un explorador.</span><span class="sxs-lookup"><span data-stu-id="39000-514">For testing SPAs, we recommended a tool such as [Selenium](https://www.seleniumhq.org/), which can automate a browser.</span></span>

## <a name="introduction-to-integration-tests"></a><span data-ttu-id="39000-515">Introducción a las pruebas de integración</span><span class="sxs-lookup"><span data-stu-id="39000-515">Introduction to integration tests</span></span>

<span data-ttu-id="39000-516">Las pruebas de integración evalúan los componentes de una aplicación en un nivel más amplio que las [pruebas unitarias](/dotnet/core/testing/).</span><span class="sxs-lookup"><span data-stu-id="39000-516">Integration tests evaluate an app's components on a broader level than [unit tests](/dotnet/core/testing/).</span></span> <span data-ttu-id="39000-517">Las pruebas unitarias se usan para probar componentes de software aislados, como métodos de clase individuales.</span><span class="sxs-lookup"><span data-stu-id="39000-517">Unit tests are used to test isolated software components, such as individual class methods.</span></span> <span data-ttu-id="39000-518">Las pruebas de integración confirman que dos o más componentes de una aplicación funcionan juntos para generar un resultado esperado, lo que posiblemente incluya a todos los componentes necesarios para procesar por completo una solicitud.</span><span class="sxs-lookup"><span data-stu-id="39000-518">Integration tests confirm that two or more app components work together to produce an expected result, possibly including every component required to fully process a request.</span></span>

<span data-ttu-id="39000-519">Estas pruebas más amplias se usan para probar la infraestructura de la aplicación y todo el marco, lo que a menudo incluye los siguientes componentes:</span><span class="sxs-lookup"><span data-stu-id="39000-519">These broader tests are used to test the app's infrastructure and whole framework, often including the following components:</span></span>

* <span data-ttu-id="39000-520">Base de datos</span><span class="sxs-lookup"><span data-stu-id="39000-520">Database</span></span>
* <span data-ttu-id="39000-521">Sistema de archivos</span><span class="sxs-lookup"><span data-stu-id="39000-521">File system</span></span>
* <span data-ttu-id="39000-522">Dispositivos de red</span><span class="sxs-lookup"><span data-stu-id="39000-522">Network appliances</span></span>
* <span data-ttu-id="39000-523">Canalización de solicitud-respuesta</span><span class="sxs-lookup"><span data-stu-id="39000-523">Request-response pipeline</span></span>

<span data-ttu-id="39000-524">Las pruebas unitarias usan componentes fabricados, conocidos como *emulaciones* u *objetos ficticios*, en lugar de componentes de las infraestructura.</span><span class="sxs-lookup"><span data-stu-id="39000-524">Unit tests use fabricated components, known as *fakes* or *mock objects*, in place of infrastructure components.</span></span>

<span data-ttu-id="39000-525">A diferencia de las pruebas unitarias, las pruebas de integración:</span><span class="sxs-lookup"><span data-stu-id="39000-525">In contrast to unit tests, integration tests:</span></span>

* <span data-ttu-id="39000-526">Usan los componentes reales que emplea la aplicación en producción.</span><span class="sxs-lookup"><span data-stu-id="39000-526">Use the actual components that the app uses in production.</span></span>
* <span data-ttu-id="39000-527">Necesitan más código y procesamiento de datos.</span><span class="sxs-lookup"><span data-stu-id="39000-527">Require more code and data processing.</span></span>
* <span data-ttu-id="39000-528">Tardan más en ejecutarse.</span><span class="sxs-lookup"><span data-stu-id="39000-528">Take longer to run.</span></span>

<span data-ttu-id="39000-529">Por lo tanto, limite el uso de pruebas de integración a los escenarios de infraestructura más importantes.</span><span class="sxs-lookup"><span data-stu-id="39000-529">Therefore, limit the use of integration tests to the most important infrastructure scenarios.</span></span> <span data-ttu-id="39000-530">Si un comportamiento se puede probar mediante una prueba unitaria o una prueba de integración, opte por la prueba unitaria.</span><span class="sxs-lookup"><span data-stu-id="39000-530">If a behavior can be tested using either a unit test or an integration test, choose the unit test.</span></span>

> [!TIP]
> <span data-ttu-id="39000-531">No escriba pruebas de integración para cada posible permutación de datos y acceso a archivos con bases de datos y sistemas de archivos.</span><span class="sxs-lookup"><span data-stu-id="39000-531">Don't write integration tests for every possible permutation of data and file access with databases and file systems.</span></span> <span data-ttu-id="39000-532">Independientemente de cuántas ubicaciones de una aplicación interactúen con bases de datos y sistemas de archivos, un conjunto centrado de pruebas de integración de lectura, escritura, actualización y eliminación suele ser capaz de probar adecuadamente los componentes de sistema de archivos y base de datos.</span><span class="sxs-lookup"><span data-stu-id="39000-532">Regardless of how many places across an app interact with databases and file systems, a focused set of read, write, update, and delete integration tests are usually capable of adequately testing database and file system components.</span></span> <span data-ttu-id="39000-533">Use pruebas unitarias para las pruebas rutinarias de lógica de métodos que interactúan con estos componentes.</span><span class="sxs-lookup"><span data-stu-id="39000-533">Use unit tests for routine tests of method logic that interact with these components.</span></span> <span data-ttu-id="39000-534">En las pruebas unitarias, el uso de simulaciones o emulaciones de infraestructura da lugar a una ejecución más rápida de esas pruebas.</span><span class="sxs-lookup"><span data-stu-id="39000-534">In unit tests, the use of infrastructure fakes/mocks result in faster test execution.</span></span>

> [!NOTE]
> <span data-ttu-id="39000-535">En las conversaciones de las pruebas de integración, el proyecto probado suele denominarse *Sistema a prueba* o "SUT" para abreviar.</span><span class="sxs-lookup"><span data-stu-id="39000-535">In discussions of integration tests, the tested project is frequently called the *System Under Test*, or "SUT" for short.</span></span>
>
> <span data-ttu-id="39000-536">*En este tema se usa "SUT" para referirse a la aplicación ASP.NET Core probada.*</span><span class="sxs-lookup"><span data-stu-id="39000-536">*"SUT" is used throughout this topic to refer to the tested ASP.NET Core app.*</span></span>

## <a name="aspnet-core-integration-tests"></a><span data-ttu-id="39000-537">Pruebas de integración de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="39000-537">ASP.NET Core integration tests</span></span>

<span data-ttu-id="39000-538">Para las pruebas de integración en ASP.NET Core se necesita lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="39000-538">Integration tests in ASP.NET Core require the following:</span></span>

* <span data-ttu-id="39000-539">Un proyecto de prueba, que se usa para contener y ejecutar las pruebas.</span><span class="sxs-lookup"><span data-stu-id="39000-539">A test project is used to contain and execute the tests.</span></span> <span data-ttu-id="39000-540">El proyecto de prueba tiene una referencia al SUT.</span><span class="sxs-lookup"><span data-stu-id="39000-540">The test project has a reference to the SUT.</span></span>
* <span data-ttu-id="39000-541">El proyecto de prueba crea un host web de prueba para el SUT y usa un cliente de servidor de pruebas para controlar las solicitudes y las respuestas con el SUT.</span><span class="sxs-lookup"><span data-stu-id="39000-541">The test project creates a test web host for the SUT and uses a test server client to handle requests and responses with the SUT.</span></span>
* <span data-ttu-id="39000-542">Se usa un ejecutor de pruebas para ejecutar las pruebas y notificar los resultados de estas.</span><span class="sxs-lookup"><span data-stu-id="39000-542">A test runner is used to execute the tests and report the test results.</span></span>

<span data-ttu-id="39000-543">Las pruebas de integración siguen una secuencia de eventos que incluye los pasos de prueba normales *Arrange*, *Act* y *Assert*:</span><span class="sxs-lookup"><span data-stu-id="39000-543">Integration tests follow a sequence of events that include the usual *Arrange*, *Act*, and *Assert* test steps:</span></span>

1. <span data-ttu-id="39000-544">Se configura el host web del SUT.</span><span class="sxs-lookup"><span data-stu-id="39000-544">The SUT's web host is configured.</span></span>
1. <span data-ttu-id="39000-545">Se crea un cliente de servidor de pruebas para enviar solicitudes a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="39000-545">A test server client is created to submit requests to the app.</span></span>
1. <span data-ttu-id="39000-546">Se ejecuta el paso de prueba *Arrange*: la aplicación de prueba prepara una solicitud.</span><span class="sxs-lookup"><span data-stu-id="39000-546">The *Arrange* test step is executed: The test app prepares a request.</span></span>
1. <span data-ttu-id="39000-547">Se ejecuta el paso de prueba *Act*: el cliente envía la solicitud y recibe la respuesta.</span><span class="sxs-lookup"><span data-stu-id="39000-547">The *Act* test step is executed: The client submits the request and receives the response.</span></span>
1. <span data-ttu-id="39000-548">Se ejecuta el paso de prueba *Assert*: la respuesta *real* se valida como *correcta* o *errónea* en función de una respuesta *esperada*.</span><span class="sxs-lookup"><span data-stu-id="39000-548">The *Assert* test step is executed: The *actual* response is validated as a *pass* or *fail* based on an *expected* response.</span></span>
1. <span data-ttu-id="39000-549">El proceso continúa hasta que se ejecutan todas las pruebas.</span><span class="sxs-lookup"><span data-stu-id="39000-549">The process continues until all of the tests are executed.</span></span>
1. <span data-ttu-id="39000-550">Se notifican los resultados de la prueba.</span><span class="sxs-lookup"><span data-stu-id="39000-550">The test results are reported.</span></span>

<span data-ttu-id="39000-551">Normalmente, el host web de prueba se configura de manera diferente al host web normal de la aplicación para las series de pruebas.</span><span class="sxs-lookup"><span data-stu-id="39000-551">Usually, the test web host is configured differently than the app's normal web host for the test runs.</span></span> <span data-ttu-id="39000-552">Por ejemplo, puede usarse una base de datos diferente u otra configuración de aplicación para las pruebas.</span><span class="sxs-lookup"><span data-stu-id="39000-552">For example, a different database or different app settings might be used for the tests.</span></span>

<span data-ttu-id="39000-553">Los componentes de infraestructura, como el host web de prueba y el servidor de pruebas en memoria ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), se proporcionan o se administran mediante el paquete [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing).</span><span class="sxs-lookup"><span data-stu-id="39000-553">Infrastructure components, such as the test web host and in-memory test server ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), are provided or managed by the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package.</span></span> <span data-ttu-id="39000-554">El uso de este paquete simplifica la creación y ejecución de pruebas.</span><span class="sxs-lookup"><span data-stu-id="39000-554">Use of this package streamlines test creation and execution.</span></span>

<span data-ttu-id="39000-555">El paquete `Microsoft.AspNetCore.Mvc.Testing` controla las siguientes tareas:</span><span class="sxs-lookup"><span data-stu-id="39000-555">The `Microsoft.AspNetCore.Mvc.Testing` package handles the following tasks:</span></span>

* <span data-ttu-id="39000-556">Copia el archivo de dependencias ( *.deps*) del SUT en el directorio *bin* del proyecto de prueba.</span><span class="sxs-lookup"><span data-stu-id="39000-556">Copies the dependencies file (*.deps*) from the SUT into the test project's *bin* directory.</span></span>
* <span data-ttu-id="39000-557">Establece la [raíz de contenido](xref:fundamentals/index#content-root) en la raíz de proyecto del SUT de modo que se puedan encontrar archivos estáticos y páginas o vistas cuando se ejecuten las pruebas.</span><span class="sxs-lookup"><span data-stu-id="39000-557">Sets the [content root](xref:fundamentals/index#content-root) to the SUT's project root so that static files and pages/views are found when the tests are executed.</span></span>
* <span data-ttu-id="39000-558">Proporciona la clase [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) para simplificar el arranque del SUT con `TestServer`.</span><span class="sxs-lookup"><span data-stu-id="39000-558">Provides the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) class to streamline bootstrapping the SUT with `TestServer`.</span></span>

<span data-ttu-id="39000-559">En la documentación de las [pruebas unitarias](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) se explica cómo configurar un proyecto de prueba y un ejecutor de pruebas, además de incluirse instrucciones detalladas sobre cómo ejecutar pruebas y recomendaciones sobre cómo asignar nombres a las pruebas y las clases de prueba.</span><span class="sxs-lookup"><span data-stu-id="39000-559">The [unit tests](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) documentation describes how to set up a test project and test runner, along with detailed instructions on how to run tests and recommendations for how to name tests and test classes.</span></span>

> [!NOTE]
> <span data-ttu-id="39000-560">Al crear un proyecto de prueba para una aplicación, separe las pruebas unitarias de las pruebas de integración en proyectos diferentes.</span><span class="sxs-lookup"><span data-stu-id="39000-560">When creating a test project for an app, separate the unit tests from the integration tests into different projects.</span></span> <span data-ttu-id="39000-561">Esto ayuda a garantizar que los componentes de pruebas de infraestructura no se incluyan accidentalmente en las pruebas unitarias.</span><span class="sxs-lookup"><span data-stu-id="39000-561">This helps ensure that infrastructure testing components aren't accidentally included in the unit tests.</span></span> <span data-ttu-id="39000-562">La separación de las pruebas unitarias y de integración también permite controlar qué conjunto de pruebas se ejecuta.</span><span class="sxs-lookup"><span data-stu-id="39000-562">Separation of unit and integration tests also allows control over which set of tests are run.</span></span>

<span data-ttu-id="39000-563">Prácticamente no hay ninguna diferencia entre la configuración de las pruebas de aplicaciones Razor Pages y MVC.</span><span class="sxs-lookup"><span data-stu-id="39000-563">There's virtually no difference between the configuration for tests of Razor Pages apps and MVC apps.</span></span> <span data-ttu-id="39000-564">La única diferencia es cómo se asigna nombre a las pruebas.</span><span class="sxs-lookup"><span data-stu-id="39000-564">The only difference is in how the tests are named.</span></span> <span data-ttu-id="39000-565">En una aplicación Razor Pages, las pruebas de puntos de conexión de página normalmente se nombran según la clase del modelo de página (por ejemplo, `IndexPageTests` para probar la integración de los componentes de la página Index).</span><span class="sxs-lookup"><span data-stu-id="39000-565">In a Razor Pages app, tests of page endpoints are usually named after the page model class (for example, `IndexPageTests` to test component integration for the Index page).</span></span> <span data-ttu-id="39000-566">En una aplicación de MVC, las pruebas suelen organizarse por clases de controlador y denominarse según los controladores que prueban (por ejemplo, `HomeControllerTests` para probar la integración de componentes del controlador Home).</span><span class="sxs-lookup"><span data-stu-id="39000-566">In an MVC app, tests are usually organized by controller classes and named after the controllers they test (for example, `HomeControllerTests` to test component integration for the Home controller).</span></span>

## <a name="test-app-prerequisites"></a><span data-ttu-id="39000-567">Requisitos previos de la aplicación de prueba</span><span class="sxs-lookup"><span data-stu-id="39000-567">Test app prerequisites</span></span>

<span data-ttu-id="39000-568">El proyecto de prueba debe:</span><span class="sxs-lookup"><span data-stu-id="39000-568">The test project must:</span></span>

* <span data-ttu-id="39000-569">Haga referencia a los siguientes paquetes:</span><span class="sxs-lookup"><span data-stu-id="39000-569">Reference the following packages:</span></span>
  * [<span data-ttu-id="39000-570">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="39000-570">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)
  * [<span data-ttu-id="39000-571">Microsoft.AspNetCore.Mvc.Testing</span><span class="sxs-lookup"><span data-stu-id="39000-571">Microsoft.AspNetCore.Mvc.Testing</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/)
* <span data-ttu-id="39000-572">Especificar el SDK web en el archivo de proyecto (`<Project Sdk="Microsoft.NET.Sdk.Web">`).</span><span class="sxs-lookup"><span data-stu-id="39000-572">Specify the Web SDK in the project file (`<Project Sdk="Microsoft.NET.Sdk.Web">`).</span></span> <span data-ttu-id="39000-573">El SDK web es necesario cuando se hace referencia al [metapaquete Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="39000-573">The Web SDK is required when referencing the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="39000-574">Estos requisitos previos se pueden observar en la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/).</span><span class="sxs-lookup"><span data-stu-id="39000-574">These prerequisites can be seen in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/).</span></span> <span data-ttu-id="39000-575">Examine el archivo *tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj*.</span><span class="sxs-lookup"><span data-stu-id="39000-575">Inspect the *tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj* file.</span></span> <span data-ttu-id="39000-576">La aplicación de ejemplo usa el marco de pruebas [xUnit](https://xunit.github.io/) y la biblioteca de análisis [AngleSharp](https://anglesharp.github.io/), así que la aplicación de ejemplo también hace referencia a:</span><span class="sxs-lookup"><span data-stu-id="39000-576">The sample app uses the [xUnit](https://xunit.github.io/) test framework and the [AngleSharp](https://anglesharp.github.io/) parser library, so the sample app also references:</span></span>

* [<span data-ttu-id="39000-577">xunit</span><span class="sxs-lookup"><span data-stu-id="39000-577">xunit</span></span>](https://www.nuget.org/packages/xunit/)
* [<span data-ttu-id="39000-578">xunit.runner.visualstudio</span><span class="sxs-lookup"><span data-stu-id="39000-578">xunit.runner.visualstudio</span></span>](https://www.nuget.org/packages/xunit.runner.visualstudio/)
* [<span data-ttu-id="39000-579">AngleSharp</span><span class="sxs-lookup"><span data-stu-id="39000-579">AngleSharp</span></span>](https://www.nuget.org/packages/AngleSharp/)

## <a name="sut-environment"></a><span data-ttu-id="39000-580">Entorno del SUT</span><span class="sxs-lookup"><span data-stu-id="39000-580">SUT environment</span></span>

<span data-ttu-id="39000-581">Si el entorno del [SUT](xref:fundamentals/environments) no está establecido, el valor predeterminado del entorno es Desarrollo.</span><span class="sxs-lookup"><span data-stu-id="39000-581">If the SUT's [environment](xref:fundamentals/environments) isn't set, the environment defaults to Development.</span></span>

## <a name="basic-tests-with-the-default-webapplicationfactory"></a><span data-ttu-id="39000-582">Pruebas básicas con el valor predeterminado WebApplicationFactory</span><span class="sxs-lookup"><span data-stu-id="39000-582">Basic tests with the default WebApplicationFactory</span></span>

<span data-ttu-id="39000-583">[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) se usa para crear una instancia de [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) para las pruebas de integración.</span><span class="sxs-lookup"><span data-stu-id="39000-583">[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) is used to create a [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) for the integration tests.</span></span> <span data-ttu-id="39000-584">`TEntryPoint` es la clase de punto de entrada del SUT, normalmente la clase `Startup`.</span><span class="sxs-lookup"><span data-stu-id="39000-584">`TEntryPoint` is the entry point class of the SUT, usually the `Startup` class.</span></span>

<span data-ttu-id="39000-585">Las clases de prueba implementan una interfaz de *accesorio de clase* ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) para indicar que la clase contiene pruebas y proporcionan instancias de objeto compartidas en las pruebas de la clase.</span><span class="sxs-lookup"><span data-stu-id="39000-585">Test classes implement a *class fixture* interface ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) to indicate the class contains tests and provide shared object instances across the tests in the class.</span></span>

<span data-ttu-id="39000-586">La siguiente clase de prueba, `BasicTests`, usa `WebApplicationFactory` para arrancar el SUT y proporcionar un valor [HttpClient](/dotnet/api/system.net.http.httpclient) a un método de prueba, `Get_EndpointsReturnSuccessAndCorrectContentType`.</span><span class="sxs-lookup"><span data-stu-id="39000-586">The following test class, `BasicTests`, uses the `WebApplicationFactory` to bootstrap the SUT and provide an [HttpClient](/dotnet/api/system.net.http.httpclient) to a test method, `Get_EndpointsReturnSuccessAndCorrectContentType`.</span></span> <span data-ttu-id="39000-587">El método comprueba si el código de estado de la respuesta es correcto (códigos de estado del rango 200-299) y si el encabezado `Content-Type` es `text/html; charset=utf-8` para varias páginas de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="39000-587">The method checks if the response status code is successful (status codes in the range 200-299) and the `Content-Type` header is `text/html; charset=utf-8` for several app pages.</span></span>

<span data-ttu-id="39000-588">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) crea una instancia de `HttpClient` que sigue automáticamente los redireccionamientos y controla las cookies.</span><span class="sxs-lookup"><span data-stu-id="39000-588">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) creates an instance of `HttpClient` that automatically follows redirects and handles cookies.</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

<span data-ttu-id="39000-589">De forma predeterminada, las cookies no esenciales no se conservan entre solicitudes si está habilitada la [directiva de consentimiento de RGPD](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="39000-589">By default, non-essential cookies aren't preserved across requests when the [GDPR consent policy](xref:security/gdpr) is enabled.</span></span> <span data-ttu-id="39000-590">Para conservar las cookies no esenciales, como las usadas por el proveedor TempData, márquelas como esenciales en las pruebas.</span><span class="sxs-lookup"><span data-stu-id="39000-590">To preserve non-essential cookies, such as those used by the TempData provider, mark them as essential in your tests.</span></span> <span data-ttu-id="39000-591">Para obtener instrucciones sobre cómo marcar una cookie como esencial, vea [Cookies esenciales](xref:security/gdpr#essential-cookies).</span><span class="sxs-lookup"><span data-stu-id="39000-591">For instructions on marking a cookie as essential, see [Essential cookies](xref:security/gdpr#essential-cookies).</span></span>

## <a name="customize-webapplicationfactory"></a><span data-ttu-id="39000-592">Personalización de WebApplicationFactory</span><span class="sxs-lookup"><span data-stu-id="39000-592">Customize WebApplicationFactory</span></span>

<span data-ttu-id="39000-593">La configuración de host web se puede crear independientemente de las clases de prueba al heredar de `WebApplicationFactory` para crear una o más fábricas personalizadas:</span><span class="sxs-lookup"><span data-stu-id="39000-593">Web host configuration can be created independently of the test classes by inheriting from `WebApplicationFactory` to create one or more custom factories:</span></span>

1. <span data-ttu-id="39000-594">Herede de `WebApplicationFactory` e invalide [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost).</span><span class="sxs-lookup"><span data-stu-id="39000-594">Inherit from `WebApplicationFactory` and override [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost).</span></span> <span data-ttu-id="39000-595">[IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) permite la configuración de la colección de servicios con [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices), que se ejecuta antes del elemento `Startup.ConfigureServices` de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="39000-595">The [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) allows the configuration of the service collection with [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices), which is executed before the app's `Startup.ConfigureServices`.</span></span> <span data-ttu-id="39000-596">[IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) permite reemplazar y modificar los servicios registrados en la colección de servicios con [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices):</span><span class="sxs-lookup"><span data-stu-id="39000-596">The [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) allows overriding and modifying registered services in the service collection with [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices):</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   <span data-ttu-id="39000-597">El método `InitializeDbForTests` realiza la propagación de la base de datos de la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples).</span><span class="sxs-lookup"><span data-stu-id="39000-597">Database seeding in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is performed by the `InitializeDbForTests` method.</span></span> <span data-ttu-id="39000-598">El método se describe en la sección [Ejemplo de pruebas de integración: organización de la aplicación de prueba](#test-app-organization).</span><span class="sxs-lookup"><span data-stu-id="39000-598">The method is described in the [Integration tests sample: Test app organization](#test-app-organization) section.</span></span>

2. <span data-ttu-id="39000-599">Use la instancia de `CustomWebApplicationFactory` personalizada en las clases de prueba.</span><span class="sxs-lookup"><span data-stu-id="39000-599">Use the custom `CustomWebApplicationFactory` in test classes.</span></span> <span data-ttu-id="39000-600">En el ejemplo siguiente se usa la fábrica en la clase `IndexPageTests`:</span><span class="sxs-lookup"><span data-stu-id="39000-600">The following example uses the factory in the `IndexPageTests` class:</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   <span data-ttu-id="39000-601">El cliente de la aplicación de ejemplo está configurado para evitar que `HttpClient` siga los redireccionamientos.</span><span class="sxs-lookup"><span data-stu-id="39000-601">The sample app's client is configured to prevent the `HttpClient` from following redirects.</span></span> <span data-ttu-id="39000-602">Como se explica más adelante en la sección [Autenticación ficticia](#mock-authentication), esto permite que las pruebas comprueben el resultado de la primera respuesta de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="39000-602">As explained later in the [Mock authentication](#mock-authentication) section, this permits tests to check the result of the app's first response.</span></span> <span data-ttu-id="39000-603">La primera respuesta es un redireccionamiento en muchas de estas pruebas con un encabezado `Location`.</span><span class="sxs-lookup"><span data-stu-id="39000-603">The first response is a redirect in many of these tests with a `Location` header.</span></span>

3. <span data-ttu-id="39000-604">Una prueba típica usa los métodos auxiliares y `HttpClient` para procesar la solicitud y la respuesta:</span><span class="sxs-lookup"><span data-stu-id="39000-604">A typical test uses the `HttpClient` and helper methods to process the request and the response:</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

<span data-ttu-id="39000-605">Cualquier solicitud POST al SUT debe satisfacer la comprobación antifalsificación que realiza automáticamente el [sistema antifalsificación de protección de datos](xref:security/data-protection/introduction) de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="39000-605">Any POST request to the SUT must satisfy the antiforgery check that's automatically made by the app's [data protection antiforgery system](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="39000-606">Para organizar la solicitud POST de una prueba, la aplicación de prueba debe:</span><span class="sxs-lookup"><span data-stu-id="39000-606">In order to arrange for a test's POST request, the test app must:</span></span>

1. <span data-ttu-id="39000-607">Realizar una solicitud para la página.</span><span class="sxs-lookup"><span data-stu-id="39000-607">Make a request for the page.</span></span>
1. <span data-ttu-id="39000-608">Analizar la cookie antifalsificación y solicitar el token de validación de la respuesta.</span><span class="sxs-lookup"><span data-stu-id="39000-608">Parse the antiforgery cookie and request validation token from the response.</span></span>
1. <span data-ttu-id="39000-609">Realizar la solicitud POST con la cookie antifalsificación y el token de validación de la solicitud aplicados.</span><span class="sxs-lookup"><span data-stu-id="39000-609">Make the POST request with the antiforgery cookie and request validation token in place.</span></span>

<span data-ttu-id="39000-610">Los métodos de extensión auxiliares `SendAsync` (*Helpers/HttpClientExtensions.cs*) y el método auxiliar `GetDocumentAsync` (*Helpers/HtmlHelpers.cs*) de la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) usan el analizador [AngleSharp](https://anglesharp.github.io/) para controlar la comprobación antifalsificación con los métodos siguientes:</span><span class="sxs-lookup"><span data-stu-id="39000-610">The `SendAsync` helper extension methods (*Helpers/HttpClientExtensions.cs*) and the `GetDocumentAsync` helper method (*Helpers/HtmlHelpers.cs*) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) use the [AngleSharp](https://anglesharp.github.io/) parser to handle the antiforgery check with the following methods:</span></span>

* <span data-ttu-id="39000-611">`GetDocumentAsync` &ndash; Recibe [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) y devuelve una instancia de `IHtmlDocument`.</span><span class="sxs-lookup"><span data-stu-id="39000-611">`GetDocumentAsync` &ndash; Receives the [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) and returns an `IHtmlDocument`.</span></span> <span data-ttu-id="39000-612">`GetDocumentAsync` usa una fábrica que prepara una *respuesta virtual* basada en la instancia de `HttpResponseMessage` original.</span><span class="sxs-lookup"><span data-stu-id="39000-612">`GetDocumentAsync` uses a factory that prepares a *virtual response* based on the original `HttpResponseMessage`.</span></span> <span data-ttu-id="39000-613">Para obtener más información, vea la [documentación de AngleSharp](https://github.com/AngleSharp/AngleSharp#documentation).</span><span class="sxs-lookup"><span data-stu-id="39000-613">For more information, see the [AngleSharp documentation](https://github.com/AngleSharp/AngleSharp#documentation).</span></span>
* <span data-ttu-id="39000-614">Los métodos de extensión `SendAsync` de `HttpClient` componen una instancia de [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) y llaman a [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) para enviar solicitudes al SUT.</span><span class="sxs-lookup"><span data-stu-id="39000-614">`SendAsync` extension methods for the `HttpClient` compose an [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) and call [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) to submit requests to the SUT.</span></span> <span data-ttu-id="39000-615">Las sobrecargas de `SendAsync` aceptan el formulario HTML (`IHtmlFormElement`) y lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="39000-615">Overloads for `SendAsync` accept the HTML form (`IHtmlFormElement`) and the following:</span></span>
  * <span data-ttu-id="39000-616">Botón Enviar del formulario (`IHtmlElement`)</span><span class="sxs-lookup"><span data-stu-id="39000-616">Submit button of the form (`IHtmlElement`)</span></span>
  * <span data-ttu-id="39000-617">Colección de valores del formulario (`IEnumerable<KeyValuePair<string, string>>`)</span><span class="sxs-lookup"><span data-stu-id="39000-617">Form values collection (`IEnumerable<KeyValuePair<string, string>>`)</span></span>
  * <span data-ttu-id="39000-618">Botón Enviar (`IHtmlElement`) y valores del formulario (`IEnumerable<KeyValuePair<string, string>>`)</span><span class="sxs-lookup"><span data-stu-id="39000-618">Submit button (`IHtmlElement`) and form values (`IEnumerable<KeyValuePair<string, string>>`)</span></span>

> [!NOTE]
> <span data-ttu-id="39000-619">[AngleSharp](https://anglesharp.github.io/) es una biblioteca de análisis de terceros que se usa con fines de demostración en este tema y en la aplicación de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="39000-619">[AngleSharp](https://anglesharp.github.io/) is a third-party parsing library used for demonstration purposes in this topic and the sample app.</span></span> <span data-ttu-id="39000-620">AngleSharp no es compatible con las pruebas de integración de aplicaciones ASP.NET Core ni necesario.</span><span class="sxs-lookup"><span data-stu-id="39000-620">AngleSharp isn't supported or required for integration testing of ASP.NET Core apps.</span></span> <span data-ttu-id="39000-621">Se pueden usar otros analizadores, como [Html Agility Pack (HAP)](https://html-agility-pack.net/).</span><span class="sxs-lookup"><span data-stu-id="39000-621">Other parsers can be used, such as the [Html Agility Pack (HAP)](https://html-agility-pack.net/).</span></span> <span data-ttu-id="39000-622">Otro enfoque consiste en escribir código para controlar el token de comprobación de solicitudes y la cookie antifalsificación del sistema antifalsificación directamente.</span><span class="sxs-lookup"><span data-stu-id="39000-622">Another approach is to write code to handle the antiforgery system's request verification token and antiforgery cookie directly.</span></span>

## <a name="customize-the-client-with-withwebhostbuilder"></a><span data-ttu-id="39000-623">Personalización del cliente con WithWebHostBuilder</span><span class="sxs-lookup"><span data-stu-id="39000-623">Customize the client with WithWebHostBuilder</span></span>

<span data-ttu-id="39000-624">Cuando se requiere configuración adicional en un método de prueba, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) crea una nueva instancia de `WebApplicationFactory` con un elemento [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) que se personaliza aún más mediante la configuración.</span><span class="sxs-lookup"><span data-stu-id="39000-624">When additional configuration is required within a test method, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) creates a new `WebApplicationFactory` with an [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) that is further customized by configuration.</span></span>

<span data-ttu-id="39000-625">El método de prueba `Post_DeleteMessageHandler_ReturnsRedirectToRoot` de la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) muestra el uso de `WithWebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="39000-625">The `Post_DeleteMessageHandler_ReturnsRedirectToRoot` test method of the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) demonstrates the use of `WithWebHostBuilder`.</span></span> <span data-ttu-id="39000-626">Esta prueba realiza una eliminación de registros en la base de datos al desencadenar un envío de formulario en el SUT.</span><span class="sxs-lookup"><span data-stu-id="39000-626">This test performs a record delete in the database by triggering a form submission in the SUT.</span></span>

<span data-ttu-id="39000-627">Dado que otra prueba en la clase `IndexPageTests` realiza una operación que elimina todos los registros de la base de datos y puede ejecutarse antes que el método `Post_DeleteMessageHandler_ReturnsRedirectToRoot`, la base de datos se vuelve a propagar en este método de prueba para asegurarse de que un registro está presente para que lo elimine el SUT.</span><span class="sxs-lookup"><span data-stu-id="39000-627">Because another test in the `IndexPageTests` class performs an operation that deletes all of the records in the database and may run before the `Post_DeleteMessageHandler_ReturnsRedirectToRoot` method, the database is reseeded in this test method to ensure that a record is present for the SUT to delete.</span></span> <span data-ttu-id="39000-628">La selección del primer botón de eliminación del formulario `messages` en el SUT se simula en la solicitud al SUT:</span><span class="sxs-lookup"><span data-stu-id="39000-628">Selecting the first delete button of the `messages` form in the SUT is simulated in the request to the SUT:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a><span data-ttu-id="39000-629">Opciones de cliente</span><span class="sxs-lookup"><span data-stu-id="39000-629">Client options</span></span>

<span data-ttu-id="39000-630">En la tabla siguiente se muestran los elementos [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) predeterminados disponibles al crear instancias de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="39000-630">The following table shows the default [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) available when creating `HttpClient` instances.</span></span>

| <span data-ttu-id="39000-631">Opción</span><span class="sxs-lookup"><span data-stu-id="39000-631">Option</span></span> | <span data-ttu-id="39000-632">Descripción</span><span class="sxs-lookup"><span data-stu-id="39000-632">Description</span></span> | <span data-ttu-id="39000-633">Default</span><span class="sxs-lookup"><span data-stu-id="39000-633">Default</span></span> |
| ---
<span data-ttu-id="39000-634">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="39000-634">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39000-635">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="39000-635">'Blazor'</span></span>
- <span data-ttu-id="39000-636">"Identity"</span><span class="sxs-lookup"><span data-stu-id="39000-636">'Identity'</span></span>
- <span data-ttu-id="39000-637">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="39000-637">'Let's Encrypt'</span></span>
- <span data-ttu-id="39000-638">"Razor"</span><span class="sxs-lookup"><span data-stu-id="39000-638">'Razor'</span></span>
- <span data-ttu-id="39000-639">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="39000-639">'SignalR' uid:</span></span> 

<span data-ttu-id="39000-640">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="39000-640">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39000-641">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="39000-641">'Blazor'</span></span>
- <span data-ttu-id="39000-642">"Identity"</span><span class="sxs-lookup"><span data-stu-id="39000-642">'Identity'</span></span>
- <span data-ttu-id="39000-643">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="39000-643">'Let's Encrypt'</span></span>
- <span data-ttu-id="39000-644">"Razor"</span><span class="sxs-lookup"><span data-stu-id="39000-644">'Razor'</span></span>
- <span data-ttu-id="39000-645">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="39000-645">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39000-646">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="39000-646">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39000-647">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="39000-647">'Blazor'</span></span>
- <span data-ttu-id="39000-648">"Identity"</span><span class="sxs-lookup"><span data-stu-id="39000-648">'Identity'</span></span>
- <span data-ttu-id="39000-649">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="39000-649">'Let's Encrypt'</span></span>
- <span data-ttu-id="39000-650">"Razor"</span><span class="sxs-lookup"><span data-stu-id="39000-650">'Razor'</span></span>
- <span data-ttu-id="39000-651">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="39000-651">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39000-652">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="39000-652">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39000-653">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="39000-653">'Blazor'</span></span>
- <span data-ttu-id="39000-654">"Identity"</span><span class="sxs-lookup"><span data-stu-id="39000-654">'Identity'</span></span>
- <span data-ttu-id="39000-655">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="39000-655">'Let's Encrypt'</span></span>
- <span data-ttu-id="39000-656">"Razor"</span><span class="sxs-lookup"><span data-stu-id="39000-656">'Razor'</span></span>
- <span data-ttu-id="39000-657">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="39000-657">'SignalR' uid:</span></span> 

<span data-ttu-id="39000-658">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="39000-658">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39000-659">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="39000-659">'Blazor'</span></span>
- <span data-ttu-id="39000-660">"Identity"</span><span class="sxs-lookup"><span data-stu-id="39000-660">'Identity'</span></span>
- <span data-ttu-id="39000-661">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="39000-661">'Let's Encrypt'</span></span>
- <span data-ttu-id="39000-662">"Razor"</span><span class="sxs-lookup"><span data-stu-id="39000-662">'Razor'</span></span>
- <span data-ttu-id="39000-663">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="39000-663">'SignalR' uid:</span></span> 

<span data-ttu-id="39000-664">---- | | [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | Obtiene o establece si las instancias `HttpClient` deben seguir automáticamente las respuestas de redireccionamiento.</span><span class="sxs-lookup"><span data-stu-id="39000-664">---- | | [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | Gets or sets whether or not `HttpClient` instances should automatically follow redirect responses.</span></span><span data-ttu-id="39000-665"> | `true` | | [BaseAddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | Obtiene o establece la dirección base de las instancias `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="39000-665"> | `true` | | [BaseAddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | Gets or sets the base address of `HttpClient` instances.</span></span><span data-ttu-id="39000-666"> | `http://localhost` | | [HandleCookies](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | Obtiene o establece si las instancias `HttpClient` deben administrar las cookies.</span><span class="sxs-lookup"><span data-stu-id="39000-666"> | `http://localhost` | | [HandleCookies](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | Gets or sets whether `HttpClient` instances should handle cookies.</span></span><span data-ttu-id="39000-667"> | `true` | | [MaxAutomaticRedirections](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | Obtiene o establece el número máximo de respuestas de redireccionamiento que deben seguir las instancias `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="39000-667"> | `true` | | [MaxAutomaticRedirections](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | Gets or sets the maximum number of redirect responses that `HttpClient` instances should follow.</span></span> <span data-ttu-id="39000-668">| 7 |</span><span class="sxs-lookup"><span data-stu-id="39000-668">| 7 |</span></span>

<span data-ttu-id="39000-669">Cree la clase `WebApplicationFactoryClientOptions` y pásela al método [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) (los valores predeterminados se muestran en el ejemplo de código):</span><span class="sxs-lookup"><span data-stu-id="39000-669">Create the `WebApplicationFactoryClientOptions` class and pass it to the [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) method (default values are shown in the code example):</span></span>

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a><span data-ttu-id="39000-670">Inserción de servicios ficticios</span><span class="sxs-lookup"><span data-stu-id="39000-670">Inject mock services</span></span>

<span data-ttu-id="39000-671">Los servicios se pueden invalidar en una prueba con una llamada a [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) en el generador de hosts.</span><span class="sxs-lookup"><span data-stu-id="39000-671">Services can be overridden in a test with a call to [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) on the host builder.</span></span> <span data-ttu-id="39000-672">**Para insertar servicios ficticios, el SUT debe tener una clase `Startup` con un método `Startup.ConfigureServices`.**</span><span class="sxs-lookup"><span data-stu-id="39000-672">**To inject mock services, the SUT must have a `Startup` class with a `Startup.ConfigureServices` method.**</span></span>

<span data-ttu-id="39000-673">El SUT de ejemplo incluye un servicio con ámbito que devuelve una cita.</span><span class="sxs-lookup"><span data-stu-id="39000-673">The sample SUT includes a scoped service that returns a quote.</span></span> <span data-ttu-id="39000-674">La cita se inserta en un campo oculto de la página Index cuando se solicita esta.</span><span class="sxs-lookup"><span data-stu-id="39000-674">The quote is embedded in a hidden field on the Index page when the Index page is requested.</span></span>

<span data-ttu-id="39000-675">*Services/IQuoteService.cs*:</span><span class="sxs-lookup"><span data-stu-id="39000-675">*Services/IQuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

<span data-ttu-id="39000-676">*Services/QuoteService.cs*:</span><span class="sxs-lookup"><span data-stu-id="39000-676">*Services/QuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

<span data-ttu-id="39000-677">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="39000-677">*Startup.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

<span data-ttu-id="39000-678">*Páginas/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="39000-678">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

<span data-ttu-id="39000-679">*Pages/Index.cs*:</span><span class="sxs-lookup"><span data-stu-id="39000-679">*Pages/Index.cs*:</span></span>

[!code-cshtml[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

<span data-ttu-id="39000-680">El siguiente marcado se genera cuando se ejecuta la aplicación SUT:</span><span class="sxs-lookup"><span data-stu-id="39000-680">The following markup is generated when the SUT app is run:</span></span>

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

<span data-ttu-id="39000-681">Para probar el servicio y la inserción de citas en una prueba de integración, la prueba inserta un servicio ficticio en el SUT.</span><span class="sxs-lookup"><span data-stu-id="39000-681">To test the service and quote injection in an integration test, a mock service is injected into the SUT by the test.</span></span> <span data-ttu-id="39000-682">El servicio ficticio reemplaza el elemento `QuoteService` de la aplicación por un servicio proporcionado por la aplicación de prueba, denominado `TestQuoteService`:</span><span class="sxs-lookup"><span data-stu-id="39000-682">The mock service replaces the app's `QuoteService` with a service provided by the test app, called `TestQuoteService`:</span></span>

<span data-ttu-id="39000-683">*IntegrationTests.IndexPageTests.cs*:</span><span class="sxs-lookup"><span data-stu-id="39000-683">*IntegrationTests.IndexPageTests.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

<span data-ttu-id="39000-684">Se llama a `ConfigureTestServices` y se registra el servicio con ámbito:</span><span class="sxs-lookup"><span data-stu-id="39000-684">`ConfigureTestServices` is called, and the scoped service is registered:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

<span data-ttu-id="39000-685">El marcado producido durante la ejecución de la prueba refleja el texto de la cita proporcionado por `TestQuoteService`, por lo que la aserción pasa:</span><span class="sxs-lookup"><span data-stu-id="39000-685">The markup produced during the test's execution reflects the quote text supplied by `TestQuoteService`, thus the assertion passes:</span></span>

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="mock-authentication"></a><span data-ttu-id="39000-686">Autenticación ficticia</span><span class="sxs-lookup"><span data-stu-id="39000-686">Mock authentication</span></span>

<span data-ttu-id="39000-687">Las pruebas de la clase `AuthTests` comprueban que un punto de conexión seguro:</span><span class="sxs-lookup"><span data-stu-id="39000-687">Tests in the `AuthTests` class check that a secure endpoint:</span></span>

* <span data-ttu-id="39000-688">Redirige a un usuario no autenticado a la página de inicio de sesión de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="39000-688">Redirects an unauthenticated user to the app's Login page.</span></span>
* <span data-ttu-id="39000-689">Devuelve el contenido de un usuario autenticado.</span><span class="sxs-lookup"><span data-stu-id="39000-689">Returns content for an authenticated user.</span></span>

<span data-ttu-id="39000-690">En el SUT, la página `/SecurePage` usa una convención [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) para aplicar un elemento [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) a la página.</span><span class="sxs-lookup"><span data-stu-id="39000-690">In the SUT, the `/SecurePage` page uses an [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) convention to apply an [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) to the page.</span></span> <span data-ttu-id="39000-691">Para más información, consulte las convenciones de autorización de [Razor Pages](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).</span><span class="sxs-lookup"><span data-stu-id="39000-691">For more information, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

<span data-ttu-id="39000-692">En la prueba `Get_SecurePageRedirectsAnUnauthenticatedUser`, [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) se establece de modo que no permita los redireccionamientos al establecer [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) en `false`:</span><span class="sxs-lookup"><span data-stu-id="39000-692">In the `Get_SecurePageRedirectsAnUnauthenticatedUser` test, a [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) is set to disallow redirects by setting [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) to `false`:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet2)]

<span data-ttu-id="39000-693">Al no permitir que el cliente siga el redireccionamiento, se pueden realizar las siguientes comprobaciones:</span><span class="sxs-lookup"><span data-stu-id="39000-693">By disallowing the client to follow the redirect, the following checks can be made:</span></span>

* <span data-ttu-id="39000-694">El código de estado devuelto por el SUT puede comprobarse con el resultado esperado [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode), no el código de estado final después del redireccionamiento a la página de inicio de sesión, que sería [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode).</span><span class="sxs-lookup"><span data-stu-id="39000-694">The status code returned by the SUT can be checked against the expected [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode) result, not the final status code after the redirect to the Login page, which would be [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode).</span></span>
* <span data-ttu-id="39000-695">Se comprueba el valor del encabezado `Location` en los encabezados de respuesta para confirmar que empieza con `http://localhost/Identity/Account/Login`, no la respuesta de la página de inicio de sesión final, donde el encabezado `Location` no estaría presente.</span><span class="sxs-lookup"><span data-stu-id="39000-695">The `Location` header value in the response headers is checked to confirm that it starts with `http://localhost/Identity/Account/Login`, not the final Login page response, where the `Location` header wouldn't be present.</span></span>

<span data-ttu-id="39000-696">La aplicación de prueba puede simular un elemento <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> en [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) para probar aspectos de autenticación y autorización.</span><span class="sxs-lookup"><span data-stu-id="39000-696">The test app can mock an <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> in [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) in order to test aspects of authentication and authorization.</span></span> <span data-ttu-id="39000-697">Un escenario mínimo devuelve [AuthenticateResult.Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*):</span><span class="sxs-lookup"><span data-stu-id="39000-697">A minimal scenario returns an [AuthenticateResult.Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*):</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet4&highlight=11-18)]

<span data-ttu-id="39000-698">Se llama a `TestAuthHandler` para autenticar a un usuario cuando el esquema de autenticación está establecido en `Test`, donde `AddAuthentication` está registrado para `ConfigureTestServices`:</span><span class="sxs-lookup"><span data-stu-id="39000-698">The `TestAuthHandler` is called to authenticate a user when the authentication scheme is set to `Test` where `AddAuthentication` is registered for `ConfigureTestServices`:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet3&highlight=7-12)]

<span data-ttu-id="39000-699">Para obtener más información sobre `WebApplicationFactoryClientOptions`, vea la sección [Opciones de cliente](#client-options).</span><span class="sxs-lookup"><span data-stu-id="39000-699">For more information on `WebApplicationFactoryClientOptions`, see the [Client options](#client-options) section.</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="39000-700">Establecimiento del entorno</span><span class="sxs-lookup"><span data-stu-id="39000-700">Set the environment</span></span>

<span data-ttu-id="39000-701">De forma predeterminada, el entorno de host y aplicación del SUT se configura para usar el entorno Desarrollo.</span><span class="sxs-lookup"><span data-stu-id="39000-701">By default, the SUT's host and app environment is configured to use the Development environment.</span></span> <span data-ttu-id="39000-702">Para invalidar el entorno del SUT:</span><span class="sxs-lookup"><span data-stu-id="39000-702">To override the SUT's environment:</span></span>

* <span data-ttu-id="39000-703">Establezca la variable de entorno `ASPNETCORE_ENVIRONMENT` (por ejemplo, `Staging`, `Production` u otro valor personalizado, como `Testing`).</span><span class="sxs-lookup"><span data-stu-id="39000-703">Set the `ASPNETCORE_ENVIRONMENT` environment variable (for example, `Staging`, `Production`, or other custom value, such as `Testing`).</span></span>
* <span data-ttu-id="39000-704">Reemplace `CreateWebHostBuilder` en la aplicación de prueba para leer la variable de entorno `ASPNETCORE_ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="39000-704">Override `CreateWebHostBuilder` in the test app to read the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span>

```csharp
public class CustomWebApplicationFactory<TStartup> 
    : WebApplicationFactory<TStartup> where TStartup: class
{
    protected override IWebHostBuilder CreateWebHostBuilder()
    {
        return base.CreateWebHostBuilder()
            .UseEnvironment(
                Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT"));
    }

    ...
}
```

<span data-ttu-id="39000-705">El entorno también puede establecerse directamente en el generador de hosts en un valor de <xref:Microsoft.AspNetCore.Mvc.Testing.WebApplicationFactory%601> personalizado:</span><span class="sxs-lookup"><span data-stu-id="39000-705">The environment can also be set directly on the host builder in a custom <xref:Microsoft.AspNetCore.Mvc.Testing.WebApplicationFactory%601>:</span></span>

```csharp
public class CustomWebApplicationFactory<TStartup> 
    : WebApplicationFactory<TStartup> where TStartup: class
{
    protected override void ConfigureWebHost(IWebHostBuilder builder)
    {
        builder.UseEnvironment(
            Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT"));
    
        ...
    }

    ...
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a><span data-ttu-id="39000-706">Cómo deduce la infraestructura de prueba la ruta de acceso raíz del contenido de la aplicación</span><span class="sxs-lookup"><span data-stu-id="39000-706">How the test infrastructure infers the app content root path</span></span>

<span data-ttu-id="39000-707">El constructor `WebApplicationFactory` deduce la ruta de acceso [raíz del contenido](xref:fundamentals/index#content-root) de la aplicación al buscar un elemento [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) en el ensamblado que contiene las pruebas de integración con una clave igual a `System.Reflection.Assembly.FullName` del ensamblado `TEntryPoint`.</span><span class="sxs-lookup"><span data-stu-id="39000-707">The `WebApplicationFactory` constructor infers the app [content root](xref:fundamentals/index#content-root) path by searching for a [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) on the assembly containing the integration tests with a key equal to the `TEntryPoint` assembly `System.Reflection.Assembly.FullName`.</span></span> <span data-ttu-id="39000-708">En caso de que no se encuentre la clave correcta, `WebApplicationFactory` vuelve a buscar un archivo de solución ( *.sln*) y anexa el nombre de ensamblado `TEntryPoint` al directorio de la solución.</span><span class="sxs-lookup"><span data-stu-id="39000-708">In case an attribute with the correct key isn't found, `WebApplicationFactory` falls back to searching for a solution file (*.sln*) and appends the `TEntryPoint` assembly name to the solution directory.</span></span> <span data-ttu-id="39000-709">El directorio raíz de la aplicación (la ruta de acceso raíz del contenido) se usa para detectar los archivos de contenido y las vistas.</span><span class="sxs-lookup"><span data-stu-id="39000-709">The app root directory (the content root path) is used to discover views and content files.</span></span>

## <a name="disable-shadow-copying"></a><span data-ttu-id="39000-710">Deshabilitación de la creación de instantáneas</span><span class="sxs-lookup"><span data-stu-id="39000-710">Disable shadow copying</span></span>

<span data-ttu-id="39000-711">La creación de instantáneas hace que las pruebas se ejecuten en un directorio diferente al de salida.</span><span class="sxs-lookup"><span data-stu-id="39000-711">Shadow copying causes the tests to execute in a different directory than the output directory.</span></span> <span data-ttu-id="39000-712">Para que las pruebas funcionen correctamente, se debe deshabilitar la creación de instantáneas.</span><span class="sxs-lookup"><span data-stu-id="39000-712">For tests to work properly, shadow copying must be disabled.</span></span> <span data-ttu-id="39000-713">La [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) usa xUnit y deshabilita la creación de instantáneas para xUnit al incluir un archivo *xunit.runner.json* con la opción de configuración correcta.</span><span class="sxs-lookup"><span data-stu-id="39000-713">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) uses xUnit and disables shadow copying for xUnit by including an *xunit.runner.json* file with the correct configuration setting.</span></span> <span data-ttu-id="39000-714">Para obtener más información, vea [Configuración de xUnit con JSON](https://xunit.github.io/docs/configuring-with-json.html).</span><span class="sxs-lookup"><span data-stu-id="39000-714">For more information, see [Configuring xUnit with JSON](https://xunit.github.io/docs/configuring-with-json.html).</span></span>

<span data-ttu-id="39000-715">Agregue el archivo *xunit.runner.json* a la raíz del proyecto de prueba con el siguiente contenido:</span><span class="sxs-lookup"><span data-stu-id="39000-715">Add the *xunit.runner.json* file to root of the test project with the following content:</span></span>

```json
{
  "shadowCopy": false
}
```

<span data-ttu-id="39000-716">Si usa Visual Studio, establezca la propiedad **Copiar en el directorio de salida** del archivo en **Copiar siempre**.</span><span class="sxs-lookup"><span data-stu-id="39000-716">If using Visual Studio, set the file's **Copy to Output Directory** property to **Copy always**.</span></span> <span data-ttu-id="39000-717">Si no usa Visual Studio, agregue un destino `Content` al archivo de proyecto de la aplicación de prueba:</span><span class="sxs-lookup"><span data-stu-id="39000-717">If not using Visual Studio, add a `Content` target to the test app's project file:</span></span>

```xml
<ItemGroup>
  <Content Update="xunit.runner.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
  </Content>
</ItemGroup>
```

## <a name="disposal-of-objects"></a><span data-ttu-id="39000-718">Eliminación de objetos</span><span class="sxs-lookup"><span data-stu-id="39000-718">Disposal of objects</span></span>

<span data-ttu-id="39000-719">Una vez ejecutadas las pruebas de la implementación `IClassFixture`, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) y [HttpClient](/dotnet/api/system.net.http.httpclient) se eliminan cuando xUnit quita [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1).</span><span class="sxs-lookup"><span data-stu-id="39000-719">After the tests of the `IClassFixture` implementation are executed, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) and [HttpClient](/dotnet/api/system.net.http.httpclient) are disposed when xUnit disposes of the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1).</span></span> <span data-ttu-id="39000-720">Si los objetos cuya instancia ha creado el desarrollador requieren eliminación, quítelos de la implementación `IClassFixture`.</span><span class="sxs-lookup"><span data-stu-id="39000-720">If objects instantiated by the developer require disposal, dispose of them in the `IClassFixture` implementation.</span></span> <span data-ttu-id="39000-721">Para obtener más información, vea [Implementar un método Dispose](/dotnet/standard/garbage-collection/implementing-dispose).</span><span class="sxs-lookup"><span data-stu-id="39000-721">For more information, see [Implementing a Dispose method](/dotnet/standard/garbage-collection/implementing-dispose).</span></span>

## <a name="integration-tests-sample"></a><span data-ttu-id="39000-722">Ejemplo de pruebas de integración</span><span class="sxs-lookup"><span data-stu-id="39000-722">Integration tests sample</span></span>

<span data-ttu-id="39000-723">La [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) se compone de dos aplicaciones:</span><span class="sxs-lookup"><span data-stu-id="39000-723">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is composed of two apps:</span></span>

| <span data-ttu-id="39000-724">Aplicación</span><span class="sxs-lookup"><span data-stu-id="39000-724">App</span></span> | <span data-ttu-id="39000-725">Directorio del proyecto</span><span class="sxs-lookup"><span data-stu-id="39000-725">Project directory</span></span> | <span data-ttu-id="39000-726">Descripción</span><span class="sxs-lookup"><span data-stu-id="39000-726">Description</span></span> |
| --- | ---
<span data-ttu-id="39000-727">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="39000-727">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39000-728">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="39000-728">'Blazor'</span></span>
- <span data-ttu-id="39000-729">"Identity"</span><span class="sxs-lookup"><span data-stu-id="39000-729">'Identity'</span></span>
- <span data-ttu-id="39000-730">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="39000-730">'Let's Encrypt'</span></span>
- <span data-ttu-id="39000-731">"Razor"</span><span class="sxs-lookup"><span data-stu-id="39000-731">'Razor'</span></span>
- <span data-ttu-id="39000-732">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="39000-732">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39000-733">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="39000-733">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39000-734">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="39000-734">'Blazor'</span></span>
- <span data-ttu-id="39000-735">"Identity"</span><span class="sxs-lookup"><span data-stu-id="39000-735">'Identity'</span></span>
- <span data-ttu-id="39000-736">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="39000-736">'Let's Encrypt'</span></span>
- <span data-ttu-id="39000-737">"Razor"</span><span class="sxs-lookup"><span data-stu-id="39000-737">'Razor'</span></span>
- <span data-ttu-id="39000-738">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="39000-738">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39000-739">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="39000-739">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39000-740">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="39000-740">'Blazor'</span></span>
- <span data-ttu-id="39000-741">"Identity"</span><span class="sxs-lookup"><span data-stu-id="39000-741">'Identity'</span></span>
- <span data-ttu-id="39000-742">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="39000-742">'Let's Encrypt'</span></span>
- <span data-ttu-id="39000-743">"Razor"</span><span class="sxs-lookup"><span data-stu-id="39000-743">'Razor'</span></span>
- <span data-ttu-id="39000-744">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="39000-744">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39000-745">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="39000-745">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39000-746">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="39000-746">'Blazor'</span></span>
- <span data-ttu-id="39000-747">"Identity"</span><span class="sxs-lookup"><span data-stu-id="39000-747">'Identity'</span></span>
- <span data-ttu-id="39000-748">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="39000-748">'Let's Encrypt'</span></span>
- <span data-ttu-id="39000-749">"Razor"</span><span class="sxs-lookup"><span data-stu-id="39000-749">'Razor'</span></span>
- <span data-ttu-id="39000-750">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="39000-750">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39000-751">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="39000-751">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39000-752">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="39000-752">'Blazor'</span></span>
- <span data-ttu-id="39000-753">"Identity"</span><span class="sxs-lookup"><span data-stu-id="39000-753">'Identity'</span></span>
- <span data-ttu-id="39000-754">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="39000-754">'Let's Encrypt'</span></span>
- <span data-ttu-id="39000-755">"Razor"</span><span class="sxs-lookup"><span data-stu-id="39000-755">'Razor'</span></span>
- <span data-ttu-id="39000-756">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="39000-756">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39000-757">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="39000-757">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39000-758">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="39000-758">'Blazor'</span></span>
- <span data-ttu-id="39000-759">"Identity"</span><span class="sxs-lookup"><span data-stu-id="39000-759">'Identity'</span></span>
- <span data-ttu-id="39000-760">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="39000-760">'Let's Encrypt'</span></span>
- <span data-ttu-id="39000-761">"Razor"</span><span class="sxs-lookup"><span data-stu-id="39000-761">'Razor'</span></span>
- <span data-ttu-id="39000-762">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="39000-762">'SignalR' uid:</span></span> 

<span data-ttu-id="39000-763">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="39000-763">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39000-764">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="39000-764">'Blazor'</span></span>
- <span data-ttu-id="39000-765">"Identity"</span><span class="sxs-lookup"><span data-stu-id="39000-765">'Identity'</span></span>
- <span data-ttu-id="39000-766">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="39000-766">'Let's Encrypt'</span></span>
- <span data-ttu-id="39000-767">"Razor"</span><span class="sxs-lookup"><span data-stu-id="39000-767">'Razor'</span></span>
- <span data-ttu-id="39000-768">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="39000-768">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39000-769">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="39000-769">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39000-770">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="39000-770">'Blazor'</span></span>
- <span data-ttu-id="39000-771">"Identity"</span><span class="sxs-lookup"><span data-stu-id="39000-771">'Identity'</span></span>
- <span data-ttu-id="39000-772">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="39000-772">'Let's Encrypt'</span></span>
- <span data-ttu-id="39000-773">"Razor"</span><span class="sxs-lookup"><span data-stu-id="39000-773">'Razor'</span></span>
- <span data-ttu-id="39000-774">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="39000-774">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39000-775">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="39000-775">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39000-776">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="39000-776">'Blazor'</span></span>
- <span data-ttu-id="39000-777">"Identity"</span><span class="sxs-lookup"><span data-stu-id="39000-777">'Identity'</span></span>
- <span data-ttu-id="39000-778">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="39000-778">'Let's Encrypt'</span></span>
- <span data-ttu-id="39000-779">"Razor"</span><span class="sxs-lookup"><span data-stu-id="39000-779">'Razor'</span></span>
- <span data-ttu-id="39000-780">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="39000-780">'SignalR' uid:</span></span> 

<span data-ttu-id="39000-781">------ | | Aplicación de mensaje (el sistema a prueba) | *src/RazorPagesProject* | Permite a un usuario agregar, analizar mensajes y eliminarlos todos o uno solo.</span><span class="sxs-lookup"><span data-stu-id="39000-781">------ | | Message app (the SUT) | *src/RazorPagesProject* | Allows a user to add, delete one, delete all, and analyze messages.</span></span> <span data-ttu-id="39000-782">| | Aplicación de prueba | *tests/RazorPagesProject.Tests* | Se usa para la prueba de integración del sistema a prueba.</span><span class="sxs-lookup"><span data-stu-id="39000-782">| | Test app | *tests/RazorPagesProject.Tests* | Used to integration test the SUT.</span></span> |

<span data-ttu-id="39000-783">Las pruebas se pueden ejecutar con las características de prueba integradas de un IDE, como [Visual Studio](https://visualstudio.microsoft.com).</span><span class="sxs-lookup"><span data-stu-id="39000-783">The tests can be run using the built-in test features of an IDE, such as [Visual Studio](https://visualstudio.microsoft.com).</span></span> <span data-ttu-id="39000-784">Si usa [Visual Studio Code](https://code.visualstudio.com/) o la línea de comandos, ejecute el siguiente comando en un símbolo del sistema del directorio *tests/RazorPagesProject.Tests*:</span><span class="sxs-lookup"><span data-stu-id="39000-784">If using [Visual Studio Code](https://code.visualstudio.com/) or the command line, execute the following command at a command prompt in the *tests/RazorPagesProject.Tests* directory:</span></span>

```dotnetcli
dotnet test
```

### <a name="message-app-sut-organization"></a><span data-ttu-id="39000-785">Organización de aplicación de mensajes (SUT)</span><span class="sxs-lookup"><span data-stu-id="39000-785">Message app (SUT) organization</span></span>

<span data-ttu-id="39000-786">El sistema a prueba es un sistema de mensajes de Razor Pages con las siguientes características:</span><span class="sxs-lookup"><span data-stu-id="39000-786">The SUT is a Razor Pages message system with the following characteristics:</span></span>

* <span data-ttu-id="39000-787">La página Index de la aplicación (*Pages/Index.cshtml* y *Pages/Index.cshtml.cs*) proporciona una interfaz de usuario y métodos de modelo de página para controlar la adición, la eliminación y el análisis de mensajes (promedio de palabras por mensaje).</span><span class="sxs-lookup"><span data-stu-id="39000-787">The Index page of the app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) provides a UI and page model methods to control the addition, deletion, and analysis of messages (average words per message).</span></span>
* <span data-ttu-id="39000-788">La clase `Message` (*Data/Message.cs*) describe un mensaje con dos propiedades: `Id` (clave) y `Text` (mensaje).</span><span class="sxs-lookup"><span data-stu-id="39000-788">A message is described by the `Message` class (*Data/Message.cs*) with two properties: `Id` (key) and `Text` (message).</span></span> <span data-ttu-id="39000-789">Se necesita la propiedad `Text`, que está limitada a 200 caracteres.</span><span class="sxs-lookup"><span data-stu-id="39000-789">The `Text` property is required and limited to 200 characters.</span></span>
* <span data-ttu-id="39000-790">Los mensajes se almacenan mediante [Base de datos en memoria de Entity Framework](/ef/core/providers/in-memory/)&#8224;.</span><span class="sxs-lookup"><span data-stu-id="39000-790">Messages are stored using [Entity Framework's in-memory database](/ef/core/providers/in-memory/)&#8224;.</span></span>
* <span data-ttu-id="39000-791">La aplicación contiene una capa de acceso a datos (DAL) en su clase de contexto de base de datos, `AppDbContext` (*Data/AppDbContext.cs*).</span><span class="sxs-lookup"><span data-stu-id="39000-791">The app contains a data access layer (DAL) in its database context class, `AppDbContext` (*Data/AppDbContext.cs*).</span></span>
* <span data-ttu-id="39000-792">Si la base de datos está vacía al inicio de una aplicación, el almacén de mensajes se inicializa con tres mensajes.</span><span class="sxs-lookup"><span data-stu-id="39000-792">If the database is empty on app startup, the message store is initialized with three messages.</span></span>
* <span data-ttu-id="39000-793">La aplicación incluye un elemento `/SecurePage` al que solo puede acceder un usuario autenticado.</span><span class="sxs-lookup"><span data-stu-id="39000-793">The app includes a `/SecurePage` that can only be accessed by an authenticated user.</span></span>

<span data-ttu-id="39000-794">&#8224;En el tema de EF, [Pruebas con InMemory](/ef/core/miscellaneous/testing/in-memory), se explica cómo usar una base de datos en memoria para las pruebas con MSTest.</span><span class="sxs-lookup"><span data-stu-id="39000-794">&#8224;The EF topic, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explains how to use an in-memory database for tests with MSTest.</span></span> <span data-ttu-id="39000-795">En este tema se usa el marco de pruebas [xUnit](https://xunit.github.io/).</span><span class="sxs-lookup"><span data-stu-id="39000-795">This topic uses the [xUnit](https://xunit.github.io/) test framework.</span></span> <span data-ttu-id="39000-796">Los conceptos y las implementaciones de prueba de diferentes marcos de pruebas son similares, pero no idénticos.</span><span class="sxs-lookup"><span data-stu-id="39000-796">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span>

<span data-ttu-id="39000-797">Aunque la aplicación no usa el patrón del repositorio y no es un buen ejemplo del [patrón de unidad de trabajo (UoW)](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages admite estos patrones de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="39000-797">Although the app doesn't use the repository pattern and isn't an effective example of the [Unit of Work (UoW) pattern](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages supports these patterns of development.</span></span> <span data-ttu-id="39000-798">Para obtener más información, vea [Diseño del nivel de persistencia de infraestructura](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) y [Lógica del controlador de pruebas](/aspnet/core/mvc/controllers/testing) (el ejemplo implementa el patrón del repositorio).</span><span class="sxs-lookup"><span data-stu-id="39000-798">For more information, see [Designing the infrastructure persistence layer](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) and [Test controller logic](/aspnet/core/mvc/controllers/testing) (the sample implements the repository pattern).</span></span>

### <a name="test-app-organization"></a><span data-ttu-id="39000-799">Organización de la aplicación de prueba</span><span class="sxs-lookup"><span data-stu-id="39000-799">Test app organization</span></span>

<span data-ttu-id="39000-800">La aplicación de prueba es una aplicación de consola dentro del directorio *tests/RazorPagesProject.Tests*.</span><span class="sxs-lookup"><span data-stu-id="39000-800">The test app is a console app inside the *tests/RazorPagesProject.Tests* directory.</span></span>

| <span data-ttu-id="39000-801">Directorio de la aplicación de prueba</span><span class="sxs-lookup"><span data-stu-id="39000-801">Test app directory</span></span> | <span data-ttu-id="39000-802">Descripción</span><span class="sxs-lookup"><span data-stu-id="39000-802">Description</span></span> |
| ---
<span data-ttu-id="39000-803">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="39000-803">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39000-804">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="39000-804">'Blazor'</span></span>
- <span data-ttu-id="39000-805">"Identity"</span><span class="sxs-lookup"><span data-stu-id="39000-805">'Identity'</span></span>
- <span data-ttu-id="39000-806">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="39000-806">'Let's Encrypt'</span></span>
- <span data-ttu-id="39000-807">"Razor"</span><span class="sxs-lookup"><span data-stu-id="39000-807">'Razor'</span></span>
- <span data-ttu-id="39000-808">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="39000-808">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39000-809">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="39000-809">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39000-810">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="39000-810">'Blazor'</span></span>
- <span data-ttu-id="39000-811">"Identity"</span><span class="sxs-lookup"><span data-stu-id="39000-811">'Identity'</span></span>
- <span data-ttu-id="39000-812">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="39000-812">'Let's Encrypt'</span></span>
- <span data-ttu-id="39000-813">"Razor"</span><span class="sxs-lookup"><span data-stu-id="39000-813">'Razor'</span></span>
- <span data-ttu-id="39000-814">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="39000-814">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39000-815">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="39000-815">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39000-816">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="39000-816">'Blazor'</span></span>
- <span data-ttu-id="39000-817">"Identity"</span><span class="sxs-lookup"><span data-stu-id="39000-817">'Identity'</span></span>
- <span data-ttu-id="39000-818">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="39000-818">'Let's Encrypt'</span></span>
- <span data-ttu-id="39000-819">"Razor"</span><span class="sxs-lookup"><span data-stu-id="39000-819">'Razor'</span></span>
- <span data-ttu-id="39000-820">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="39000-820">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39000-821">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="39000-821">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39000-822">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="39000-822">'Blazor'</span></span>
- <span data-ttu-id="39000-823">"Identity"</span><span class="sxs-lookup"><span data-stu-id="39000-823">'Identity'</span></span>
- <span data-ttu-id="39000-824">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="39000-824">'Let's Encrypt'</span></span>
- <span data-ttu-id="39000-825">"Razor"</span><span class="sxs-lookup"><span data-stu-id="39000-825">'Razor'</span></span>
- <span data-ttu-id="39000-826">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="39000-826">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39000-827">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="39000-827">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39000-828">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="39000-828">'Blazor'</span></span>
- <span data-ttu-id="39000-829">"Identity"</span><span class="sxs-lookup"><span data-stu-id="39000-829">'Identity'</span></span>
- <span data-ttu-id="39000-830">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="39000-830">'Let's Encrypt'</span></span>
- <span data-ttu-id="39000-831">"Razor"</span><span class="sxs-lookup"><span data-stu-id="39000-831">'Razor'</span></span>
- <span data-ttu-id="39000-832">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="39000-832">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39000-833">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="39000-833">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39000-834">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="39000-834">'Blazor'</span></span>
- <span data-ttu-id="39000-835">"Identity"</span><span class="sxs-lookup"><span data-stu-id="39000-835">'Identity'</span></span>
- <span data-ttu-id="39000-836">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="39000-836">'Let's Encrypt'</span></span>
- <span data-ttu-id="39000-837">"Razor"</span><span class="sxs-lookup"><span data-stu-id="39000-837">'Razor'</span></span>
- <span data-ttu-id="39000-838">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="39000-838">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39000-839">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="39000-839">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39000-840">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="39000-840">'Blazor'</span></span>
- <span data-ttu-id="39000-841">"Identity"</span><span class="sxs-lookup"><span data-stu-id="39000-841">'Identity'</span></span>
- <span data-ttu-id="39000-842">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="39000-842">'Let's Encrypt'</span></span>
- <span data-ttu-id="39000-843">"Razor"</span><span class="sxs-lookup"><span data-stu-id="39000-843">'Razor'</span></span>
- <span data-ttu-id="39000-844">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="39000-844">'SignalR' uid:</span></span> 

<span data-ttu-id="39000-845">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="39000-845">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39000-846">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="39000-846">'Blazor'</span></span>
- <span data-ttu-id="39000-847">"Identity"</span><span class="sxs-lookup"><span data-stu-id="39000-847">'Identity'</span></span>
- <span data-ttu-id="39000-848">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="39000-848">'Let's Encrypt'</span></span>
- <span data-ttu-id="39000-849">"Razor"</span><span class="sxs-lookup"><span data-stu-id="39000-849">'Razor'</span></span>
- <span data-ttu-id="39000-850">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="39000-850">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39000-851">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="39000-851">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39000-852">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="39000-852">'Blazor'</span></span>
- <span data-ttu-id="39000-853">"Identity"</span><span class="sxs-lookup"><span data-stu-id="39000-853">'Identity'</span></span>
- <span data-ttu-id="39000-854">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="39000-854">'Let's Encrypt'</span></span>
- <span data-ttu-id="39000-855">"Razor"</span><span class="sxs-lookup"><span data-stu-id="39000-855">'Razor'</span></span>
- <span data-ttu-id="39000-856">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="39000-856">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39000-857">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="39000-857">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39000-858">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="39000-858">'Blazor'</span></span>
- <span data-ttu-id="39000-859">"Identity"</span><span class="sxs-lookup"><span data-stu-id="39000-859">'Identity'</span></span>
- <span data-ttu-id="39000-860">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="39000-860">'Let's Encrypt'</span></span>
- <span data-ttu-id="39000-861">"Razor"</span><span class="sxs-lookup"><span data-stu-id="39000-861">'Razor'</span></span>
- <span data-ttu-id="39000-862">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="39000-862">'SignalR' uid:</span></span> 

<span data-ttu-id="39000-863">------ | | *AuthTests* | Contiene métodos de prueba para:</span><span class="sxs-lookup"><span data-stu-id="39000-863">------ | | *AuthTests* | Contains test methods for:</span></span><ul><li><span data-ttu-id="39000-864">Acceder a una página segura mediante un usuario no autenticado.</span><span class="sxs-lookup"><span data-stu-id="39000-864">Accessing a secure page by an unauthenticated user.</span></span></li><li><span data-ttu-id="39000-865">Acceder a una página segura mediante un usuario autenticado con una simulación <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>.</span><span class="sxs-lookup"><span data-stu-id="39000-865">Accessing a secure page by an authenticated user with a mock <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>.</span></span></li><li><span data-ttu-id="39000-866">Obtener un perfil de usuario de GitHub y comprobar el inicio de sesión de usuario del perfil.</span><span class="sxs-lookup"><span data-stu-id="39000-866">Obtaining a GitHub user profile and checking the profile's user login.</span></span></li></ul> <span data-ttu-id="39000-867">| | *BasicTests* | Contiene un método de prueba para el enrutamiento y el tipo de contenido.</span><span class="sxs-lookup"><span data-stu-id="39000-867">| | *BasicTests* | Contains a test method for routing and content type.</span></span> <span data-ttu-id="39000-868">| | *IntegrationTests* | Contiene las pruebas de integración de la página Index que usa la clase `WebApplicationFactory` personalizada.</span><span class="sxs-lookup"><span data-stu-id="39000-868">| | *IntegrationTests* | Contains the integration tests for the Index page using custom `WebApplicationFactory` class.</span></span> <span data-ttu-id="39000-869">| | *Asistentes o utilidades* | </span><span class="sxs-lookup"><span data-stu-id="39000-869">| | *Helpers/Utilities* | </span></span><ul><li><span data-ttu-id="39000-870">*Utilities.cs* contiene el método `InitializeDbForTests` que se usa para propagar la base de datos con datos de prueba.</span><span class="sxs-lookup"><span data-stu-id="39000-870">*Utilities.cs* contains the `InitializeDbForTests` method used to seed the database with test data.</span></span></li><li><span data-ttu-id="39000-871">*HtmlHelpers.cs* proporciona un método para devolver un elemento `IHtmlDocument` de AngleSharp para que lo usen los métodos de prueba.</span><span class="sxs-lookup"><span data-stu-id="39000-871">*HtmlHelpers.cs* provides a method to return an AngleSharp `IHtmlDocument` for use by the test methods.</span></span></li><li><span data-ttu-id="39000-872">*HttpClientExtensions.cs* proporciona sobrecargas para que `SendAsync` envíe solicitudes al SUT.</span><span class="sxs-lookup"><span data-stu-id="39000-872">*HttpClientExtensions.cs* provide overloads for `SendAsync` to submit requests to the SUT.</span></span></li></ul> |

<span data-ttu-id="39000-873">El marco de pruebas es [xUnit](https://xunit.github.io/).</span><span class="sxs-lookup"><span data-stu-id="39000-873">The test framework is [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="39000-874">Las pruebas de integración se llevan a cabo con [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost), que incluye [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span><span class="sxs-lookup"><span data-stu-id="39000-874">Integration tests are conducted using the [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost), which includes the [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span></span> <span data-ttu-id="39000-875">Dado que el paquete [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) se usa para configurar el host de prueba y el servidor de pruebas, los paquetes `TestHost` y `TestServer` no requieren referencias de paquete directas en el archivo de proyecto o la configuración de desarrollador de la aplicación de prueba en la aplicación de prueba.</span><span class="sxs-lookup"><span data-stu-id="39000-875">Because the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package is used to configure the test host and test server, the `TestHost` and `TestServer` packages don't require direct package references in the test app's project file or developer configuration in the test app.</span></span>

<span data-ttu-id="39000-876">**Propagación de la base de datos para pruebas**</span><span class="sxs-lookup"><span data-stu-id="39000-876">**Seeding the database for testing**</span></span>

<span data-ttu-id="39000-877">Las pruebas de integración suelen requerir un pequeño conjunto de datos en la base de datos antes de la ejecución de la prueba.</span><span class="sxs-lookup"><span data-stu-id="39000-877">Integration tests usually require a small dataset in the database prior to the test execution.</span></span> <span data-ttu-id="39000-878">Por ejemplo, una prueba de eliminación consiste en la eliminación de un registro de base de datos, por lo que la base de datos debe tener al menos un registro para que la solicitud de eliminación se realice correctamente.</span><span class="sxs-lookup"><span data-stu-id="39000-878">For example, a delete test calls for a database record deletion, so the database must have at least one record for the delete request to succeed.</span></span>

<span data-ttu-id="39000-879">La aplicación de ejemplo propaga la base de datos con tres mensajes en *Utilities.cs* que las pruebas pueden usar al ejecutarse:</span><span class="sxs-lookup"><span data-stu-id="39000-879">The sample app seeds the database with three messages in *Utilities.cs* that tests can use when they execute:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="39000-880">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="39000-880">Additional resources</span></span>

* [<span data-ttu-id="39000-881">Pruebas unitarias</span><span class="sxs-lookup"><span data-stu-id="39000-881">Unit tests</span></span>](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:test/razor-pages-tests>
* <xref:fundamentals/middleware/index>
* <xref:mvc/controllers/testing>
