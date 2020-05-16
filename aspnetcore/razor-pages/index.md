---
title: Introducción a Razor Pages en ASP.NET Core
author: Rick-Anderson
description: Obtenga información sobre cómo Razor Pages en ASP.NET Core facilita la programación en escenarios centrados en páginas y mejora la productividad en comparación con el uso de MVC.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.date: 02/12/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: razor-pages/index
ms.openlocfilehash: 6939d285838a6dd971f530c1d65d73273b5b14e7
ms.sourcegitcommit: 69e1a79a572b0af17d08e81af12c594b7316f2e1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/15/2020
ms.locfileid: "83424564"
---
# <a name="introduction-to-razor-pages-in-aspnet-core"></a><span data-ttu-id="2fb81-103">Introducción a las páginas de Razor en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2fb81-103">Introduction to Razor Pages in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2fb81-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT) y [Ryan Nowak](https://github.com/rynowak)</span><span class="sxs-lookup"><span data-stu-id="2fb81-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Ryan Nowak](https://github.com/rynowak)</span></span>

<span data-ttu-id="2fb81-105">Razor Pages facilita la programación de escenarios centrados en páginas y hace que resulte más productiva que con controladores y vistas.</span><span class="sxs-lookup"><span data-stu-id="2fb81-105">Razor Pages can make coding page-focused scenarios easier and more productive than using controllers and views.</span></span>

<span data-ttu-id="2fb81-106">Si busca un tutorial que use el enfoque Model-View-Controller, consulte [Introducción a ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span><span class="sxs-lookup"><span data-stu-id="2fb81-106">If you're looking for a tutorial that uses the Model-View-Controller approach, see [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<span data-ttu-id="2fb81-107">En este documento se proporciona una introducción a las páginas de Razor.</span><span class="sxs-lookup"><span data-stu-id="2fb81-107">This document provides an introduction to Razor Pages.</span></span> <span data-ttu-id="2fb81-108">No es un tutorial paso a paso.</span><span class="sxs-lookup"><span data-stu-id="2fb81-108">It's not a step by step tutorial.</span></span> <span data-ttu-id="2fb81-109">Si encuentra que alguna sección es demasiado avanzada, consulte [Introducción a las páginas de Razor](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="2fb81-109">If you find some of the sections too advanced, see [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span></span> <span data-ttu-id="2fb81-110">Para obtener información general de ASP.NET Core, vea [Introducción a ASP.NET Core](xref:index).</span><span class="sxs-lookup"><span data-stu-id="2fb81-110">For an overview of ASP.NET Core, see the [Introduction to ASP.NET Core](xref:index).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="2fb81-111">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="2fb81-111">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2fb81-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2fb81-112">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="2fb81-113">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2fb81-113">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2fb81-114">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="2fb81-114">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-razor-pages-project"></a><span data-ttu-id="2fb81-115">Creación de un proyecto de Razor Pages</span><span class="sxs-lookup"><span data-stu-id="2fb81-115">Create a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2fb81-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2fb81-116">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2fb81-117">Vea [Introducción a Razor Pages](xref:tutorials/razor-pages/razor-pages-start) para obtener instrucciones detalladas sobre cómo crear un proyecto Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="2fb81-117">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2fb81-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2fb81-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="2fb81-119">Ejecute `dotnet new webapp` desde la línea de comandos.</span><span class="sxs-lookup"><span data-stu-id="2fb81-119">Run `dotnet new webapp` from the command line.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2fb81-120">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="2fb81-120">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="2fb81-121">Vea [Introducción a Razor Pages](xref:tutorials/razor-pages/razor-pages-start) para obtener instrucciones detalladas sobre cómo crear un proyecto Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="2fb81-121">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

---

## <a name="razor-pages"></a><span data-ttu-id="2fb81-122">Páginas de Razor</span><span class="sxs-lookup"><span data-stu-id="2fb81-122">Razor Pages</span></span>

<span data-ttu-id="2fb81-123">Páginas de Razor está habilitada en *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="2fb81-123">Razor Pages is enabled in *Startup.cs*:</span></span>

[!code-cs[](index/3.0sample/RazorPagesIntro/Startup.cs?name=snippet_Startup&highlight=12,36)]

<span data-ttu-id="2fb81-124">Considere la posibilidad de una página básica: <a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="2fb81-124">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index.cshtml?highlight=1)]

<span data-ttu-id="2fb81-125">El código anterior se parece mucho a un [archivo de vista de Razor](xref:tutorials/first-mvc-app/adding-view) que se utiliza en una aplicación ASP.NET Core con controladores y vistas.</span><span class="sxs-lookup"><span data-stu-id="2fb81-125">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="2fb81-126">La directiva [`@page`](xref:mvc/views/razor#page) lo hace diferente.</span><span class="sxs-lookup"><span data-stu-id="2fb81-126">What makes it different is the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="2fb81-127">`@page` transforma el archivo en una acción de MVC, lo que significa que administra las solicitudes directamente, sin tener que pasar a través de un controlador.</span><span class="sxs-lookup"><span data-stu-id="2fb81-127">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="2fb81-128">`@page` debe ser la primera directiva de Razor de una página.</span><span class="sxs-lookup"><span data-stu-id="2fb81-128">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="2fb81-129">`@page` afecta al comportamiento de otras construcciones de [Razor](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="2fb81-129">`@page` affects the behavior of other [Razor](xref:mvc/views/razor) constructs.</span></span> <span data-ttu-id="2fb81-130">Los nombres de archivo de Razor Pages tienen el sufijo *.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="2fb81-130">Razor Pages file names have a *.cshtml* suffix.</span></span>

<span data-ttu-id="2fb81-131">Una página similar, con una clase `PageModel`, se muestra en los dos archivos siguientes.</span><span class="sxs-lookup"><span data-stu-id="2fb81-131">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="2fb81-132">El archivo *Pages/Index2.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="2fb81-132">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="2fb81-133">Modelo de página *Pages/Index2.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="2fb81-133">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-cs[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="2fb81-134">Por convención, el archivo de clase `PageModel` tiene el mismo nombre que el archivo de páginas de Razor con *.cs* anexado.</span><span class="sxs-lookup"><span data-stu-id="2fb81-134">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="2fb81-135">Por ejemplo, la página de Razor anterior es *Pages/Index2.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="2fb81-135">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="2fb81-136">El archivo que contiene la clase `PageModel` se denomina *Pages/Index2.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="2fb81-136">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="2fb81-137">Las asociaciones de rutas de dirección URL a páginas se determinan según la ubicación de la página en el sistema de archivos.</span><span class="sxs-lookup"><span data-stu-id="2fb81-137">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="2fb81-138">En la tabla siguiente, se muestra una ruta de acceso de página de Razor y la dirección URL correspondiente:</span><span class="sxs-lookup"><span data-stu-id="2fb81-138">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="2fb81-139">Ruta de acceso y nombre de archivo</span><span class="sxs-lookup"><span data-stu-id="2fb81-139">File name and path</span></span>               | <span data-ttu-id="2fb81-140">URL correspondiente</span><span class="sxs-lookup"><span data-stu-id="2fb81-140">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="2fb81-141">*/Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="2fb81-141">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="2fb81-142">`/` o `/Index`</span><span class="sxs-lookup"><span data-stu-id="2fb81-142">`/` or `/Index`</span></span> |
| <span data-ttu-id="2fb81-143">*/Pages/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="2fb81-143">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="2fb81-144">*/Pages/Store/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="2fb81-144">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="2fb81-145">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="2fb81-145">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="2fb81-146">`/Store` o `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="2fb81-146">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="2fb81-147">Notas:</span><span class="sxs-lookup"><span data-stu-id="2fb81-147">Notes:</span></span>

* <span data-ttu-id="2fb81-148">El runtime busca archivos de páginas de Razor en la carpeta *Páginas* de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="2fb81-148">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="2fb81-149">`Index` es la página predeterminada cuando una URL no incluye una página.</span><span class="sxs-lookup"><span data-stu-id="2fb81-149">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="2fb81-150">Escritura de un formulario básico</span><span class="sxs-lookup"><span data-stu-id="2fb81-150">Write a basic form</span></span>

<span data-ttu-id="2fb81-151">Las páginas de Razor están diseñadas para facilitar la implementación de patrones comunes que se usan con exploradores web al compilar una aplicación.</span><span class="sxs-lookup"><span data-stu-id="2fb81-151">Razor Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="2fb81-152">Los [enlaces de modelos](xref:mvc/models/model-binding), los [asistentes de etiquetas](xref:mvc/views/tag-helpers/intro) y los asistentes de HTML *simplemente funcionan* con las propiedades definidas en una clase de página de Razor.</span><span class="sxs-lookup"><span data-stu-id="2fb81-152">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="2fb81-153">Considere la posibilidad de una página que implementa un formulario básico del estilo "Póngase en contacto con nosotros" para el modelo `Contact`:</span><span class="sxs-lookup"><span data-stu-id="2fb81-153">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="2fb81-154">Para los ejemplos de este documento, `DbContext` se inicializa en el archivo [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24).</span><span class="sxs-lookup"><span data-stu-id="2fb81-154">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24) file.</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Startup.cs?name=snippet)]

<span data-ttu-id="2fb81-155">El modelo de datos:</span><span class="sxs-lookup"><span data-stu-id="2fb81-155">The data model:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

<span data-ttu-id="2fb81-156">El contexto de la base de datos:</span><span class="sxs-lookup"><span data-stu-id="2fb81-156">The db context:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Data/CustomerDbContext.cs)]

<span data-ttu-id="2fb81-157">El archivo de vista *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="2fb81-157">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

<span data-ttu-id="2fb81-158">Modelo de página *Pages/Create.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="2fb81-158">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="2fb81-159">Por convención, la clase `PageModel` se denomina `<PageName>Model` y se encuentra en el mismo espacio de nombres que la página.</span><span class="sxs-lookup"><span data-stu-id="2fb81-159">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="2fb81-160">La clase `PageModel` permite la separación de la lógica de una página de su presentación.</span><span class="sxs-lookup"><span data-stu-id="2fb81-160">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="2fb81-161">Define los controladores de página para solicitudes que se envían a la página y los datos que usan para representar la página.</span><span class="sxs-lookup"><span data-stu-id="2fb81-161">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="2fb81-162">Esta separación permite lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="2fb81-162">This separation allows:</span></span>

* <span data-ttu-id="2fb81-163">Administración de dependencias de página mediante la [inserción de dependencias](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="2fb81-163">Managing of page dependencies through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* [<span data-ttu-id="2fb81-164">Pruebas unitarias</span><span class="sxs-lookup"><span data-stu-id="2fb81-164">Unit testing</span></span>](xref:test/razor-pages-tests)

<span data-ttu-id="2fb81-165">La página tiene un *método de controlador* `OnPostAsync`, que se ejecuta en solicitudes `POST` (cuando un usuario envía el formulario).</span><span class="sxs-lookup"><span data-stu-id="2fb81-165">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="2fb81-166">Se pueden agregar métodos de controlador para cualquier verbo HTTP.</span><span class="sxs-lookup"><span data-stu-id="2fb81-166">Handler methods for any HTTP verb can be added.</span></span> <span data-ttu-id="2fb81-167">Los controladores más comunes son:</span><span class="sxs-lookup"><span data-stu-id="2fb81-167">The most common handlers are:</span></span>

* <span data-ttu-id="2fb81-168">`OnGet` para inicializar el estado necesario para la página.</span><span class="sxs-lookup"><span data-stu-id="2fb81-168">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="2fb81-169">En el código anterior, el método `OnGet` muestra la página de Razor *CreateModel.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="2fb81-169">In the preceding code, the `OnGet` method displays the *CreateModel.cshtml* Razor Page.</span></span>
* <span data-ttu-id="2fb81-170">`OnPost` para controlar los envíos del formulario.</span><span class="sxs-lookup"><span data-stu-id="2fb81-170">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="2fb81-171">El sufijo de nombre `Async` es opcional, pero se usa a menudo por convención para funciones asincrónicas.</span><span class="sxs-lookup"><span data-stu-id="2fb81-171">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="2fb81-172">El código anterior es típico de las páginas de Razor.</span><span class="sxs-lookup"><span data-stu-id="2fb81-172">The preceding code is typical for Razor Pages.</span></span>

<span data-ttu-id="2fb81-173">Si está familiarizado con las aplicaciones de ASP.NET con controladores y vistas:</span><span class="sxs-lookup"><span data-stu-id="2fb81-173">If you're familiar with ASP.NET apps using controllers and views:</span></span>

* <span data-ttu-id="2fb81-174">El código `OnPostAsync` del ejemplo anterior es similar al típico código de controlador.</span><span class="sxs-lookup"><span data-stu-id="2fb81-174">The `OnPostAsync` code in the preceding example looks similar to typical controller code.</span></span>
* <span data-ttu-id="2fb81-175">La mayoría de los primitivos de MVC, como el [enlace de modelos](xref:mvc/models/model-binding), la [validación](xref:mvc/models/validation) y los resultados de acciones, funcionan del mismo modo con los controladores y Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="2fb81-175">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), and action results work the same with Controllers and Razor Pages.</span></span> 

<span data-ttu-id="2fb81-176">El método `OnPostAsync` anterior:</span><span class="sxs-lookup"><span data-stu-id="2fb81-176">The previous `OnPostAsync` method:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="2fb81-177">El flujo básico de `OnPostAsync`:</span><span class="sxs-lookup"><span data-stu-id="2fb81-177">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="2fb81-178">Compruebe los errores de validación.</span><span class="sxs-lookup"><span data-stu-id="2fb81-178">Check for validation errors.</span></span>

* <span data-ttu-id="2fb81-179">Si no hay ningún error, guarde los datos y redirija.</span><span class="sxs-lookup"><span data-stu-id="2fb81-179">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="2fb81-180">Si hay errores, muestre la página de nuevo con mensajes de validación.</span><span class="sxs-lookup"><span data-stu-id="2fb81-180">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="2fb81-181">En muchos casos, los errores de validación se detectan en el cliente y nunca se envían al servidor.</span><span class="sxs-lookup"><span data-stu-id="2fb81-181">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="2fb81-182">El archivo de vista *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="2fb81-182">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

<span data-ttu-id="2fb81-183">El código HTML representado de *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="2fb81-183">The rendered HTML from *Pages/Create.cshtml*:</span></span>

[!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.html)]

<span data-ttu-id="2fb81-184">En el código anterior, la publicación del formulario:</span><span class="sxs-lookup"><span data-stu-id="2fb81-184">In the previous code, posting the form:</span></span>

* <span data-ttu-id="2fb81-185">Con datos válidos:</span><span class="sxs-lookup"><span data-stu-id="2fb81-185">With valid data:</span></span>

  * <span data-ttu-id="2fb81-186">El método del controlador `OnPostAsync` llama al método auxiliar <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*>.</span><span class="sxs-lookup"><span data-stu-id="2fb81-186">The `OnPostAsync` handler method calls the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> helper method.</span></span> <span data-ttu-id="2fb81-187">`RedirectToPage` devuelve una instancia de <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult>.</span><span class="sxs-lookup"><span data-stu-id="2fb81-187">`RedirectToPage` returns an instance of <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult>.</span></span> <span data-ttu-id="2fb81-188">`RedirectToPage`:</span><span class="sxs-lookup"><span data-stu-id="2fb81-188">`RedirectToPage`:</span></span>

    * <span data-ttu-id="2fb81-189">Es el resultado de una acción.</span><span class="sxs-lookup"><span data-stu-id="2fb81-189">Is an action result.</span></span>
    * <span data-ttu-id="2fb81-190">Es similar a `RedirectToAction` o `RedirectToRoute` (se usa en controladores y vistas).</span><span class="sxs-lookup"><span data-stu-id="2fb81-190">Is similar to `RedirectToAction` or `RedirectToRoute` (used in controllers and views).</span></span>
    * <span data-ttu-id="2fb81-191">Se ha personalizado para las páginas.</span><span class="sxs-lookup"><span data-stu-id="2fb81-191">Is customized for pages.</span></span> <span data-ttu-id="2fb81-192">En el ejemplo anterior, redirige a la página de índice raíz (`/Index`).</span><span class="sxs-lookup"><span data-stu-id="2fb81-192">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="2fb81-193">`RedirectToPage` se detalla en la sección [Generación de direcciones URL para las páginas](#url_gen).</span><span class="sxs-lookup"><span data-stu-id="2fb81-193">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

* <span data-ttu-id="2fb81-194">Con errores de validación que se pasan al servidor:</span><span class="sxs-lookup"><span data-stu-id="2fb81-194">With validation errors that are passed to the server:</span></span>

  * <span data-ttu-id="2fb81-195">El método del controlador `OnPostAsync` llama al método auxiliar <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*>.</span><span class="sxs-lookup"><span data-stu-id="2fb81-195">The `OnPostAsync` handler method calls the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*> helper method.</span></span> <span data-ttu-id="2fb81-196">`Page` devuelve una instancia de <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>.</span><span class="sxs-lookup"><span data-stu-id="2fb81-196">`Page` returns an instance of <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>.</span></span> <span data-ttu-id="2fb81-197">Devolver `Page` es similar a cómo las acciones en los controladores devuelven `View`.</span><span class="sxs-lookup"><span data-stu-id="2fb81-197">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="2fb81-198">`PageResult` es el tipo de valor devuelto predeterminado para un método de controlador.</span><span class="sxs-lookup"><span data-stu-id="2fb81-198">`PageResult` is the default return type for a handler method.</span></span> <span data-ttu-id="2fb81-199">Un método de controlador que devuelve `void` representa la página.</span><span class="sxs-lookup"><span data-stu-id="2fb81-199">A handler method that returns `void` renders the page.</span></span>
  * <span data-ttu-id="2fb81-200">En el ejemplo anterior, la publicación del formulario sin valores hace que se devuelva [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) como false.</span><span class="sxs-lookup"><span data-stu-id="2fb81-200">In the preceding example, posting the form with no value results in [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) returning false.</span></span> <span data-ttu-id="2fb81-201">En este ejemplo, no se muestra ningún error de validación en el cliente.</span><span class="sxs-lookup"><span data-stu-id="2fb81-201">In this sample, no validation errors are displayed on the client.</span></span> <span data-ttu-id="2fb81-202">La entrega de errores de validación se trata más adelante en este documento.</span><span class="sxs-lookup"><span data-stu-id="2fb81-202">Validation error handing is covered later in this document.</span></span>

  [!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=3-6)]

* <span data-ttu-id="2fb81-203">Con errores de validación detectados mediante la validación del lado cliente:</span><span class="sxs-lookup"><span data-stu-id="2fb81-203">With validation errors detected by client side validation:</span></span>

  * <span data-ttu-id="2fb81-204">Los datos **no** se publican en el servidor.</span><span class="sxs-lookup"><span data-stu-id="2fb81-204">Data is **not** posted to the server.</span></span>
  * <span data-ttu-id="2fb81-205">La validación del lado cliente se explica más adelante en este documento.</span><span class="sxs-lookup"><span data-stu-id="2fb81-205">Client-side validation is explained later in this document.</span></span>

<span data-ttu-id="2fb81-206">La propiedad `Customer` usa el atributo [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) para participar en el enlace de modelos:</span><span class="sxs-lookup"><span data-stu-id="2fb81-206">The `Customer` property uses [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute to opt in to model binding:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=15-16)]

<span data-ttu-id="2fb81-207">`[BindProperty]`**no** debe usarse en modelos que contengan propiedades que el cliente no debe cambiar.</span><span class="sxs-lookup"><span data-stu-id="2fb81-207">`[BindProperty]` should **not** be used on models containing properties that should not be changed by the client.</span></span> <span data-ttu-id="2fb81-208">Para más información, consulte [Publicación excesiva](xref:data/ef-rp/crud#overposting).</span><span class="sxs-lookup"><span data-stu-id="2fb81-208">For more information, see [Overposting](xref:data/ef-rp/crud#overposting).</span></span>

<span data-ttu-id="2fb81-209">De forma predeterminada, Razor Pages enlaza propiedades solo con verbos que no sean `GET`.</span><span class="sxs-lookup"><span data-stu-id="2fb81-209">Razor Pages, by default, bind properties only with non-`GET` verbs.</span></span> <span data-ttu-id="2fb81-210">El enlace a propiedades elimina la necesidad de escribir código para convertir los datos HTTP en el tipo de modelo.</span><span class="sxs-lookup"><span data-stu-id="2fb81-210">Binding to properties removes the need to writing code to convert HTTP data to the model type.</span></span> <span data-ttu-id="2fb81-211">Enlazar reduce el código al usar la misma propiedad para representar los campos de formulario (`<input asp-for="Customer.Name">`) y aceptar la entrada.</span><span class="sxs-lookup"><span data-stu-id="2fb81-211">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="2fb81-212">Revisión del archivo de vista *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="2fb81-212">Reviewing the *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml?highlight=3,9)]

* <span data-ttu-id="2fb81-213">En el código anterior, el [asistente de etiquetas de entrada](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` enlaza el elemento `<input>` HTML con la expresión del modelo `Customer.Name`.</span><span class="sxs-lookup"><span data-stu-id="2fb81-213">In the preceding code, the [input tag helper](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` binds the HTML `<input>` element to the `Customer.Name` model expression.</span></span>
* <span data-ttu-id="2fb81-214">[`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available) hace que los asistentes de etiquetas estén disponibles.</span><span class="sxs-lookup"><span data-stu-id="2fb81-214">[`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available) makes Tag Helpers available.</span></span>

### <a name="the-home-page"></a><span data-ttu-id="2fb81-215">La página principal</span><span class="sxs-lookup"><span data-stu-id="2fb81-215">The home page</span></span>

<span data-ttu-id="2fb81-216">*Index.cshtml* es la página principal:</span><span class="sxs-lookup"><span data-stu-id="2fb81-216">*Index.cshtml* is the home page:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml)]

<span data-ttu-id="2fb81-217">La clase `PageModel` asociada (*Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="2fb81-217">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="2fb81-218">El archivo *Index.cshtml* contiene el siguiente marcado:</span><span class="sxs-lookup"><span data-stu-id="2fb81-218">The *Index.cshtml* file contains the following markup:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=21)]

<span data-ttu-id="2fb81-219">El `<a /a>` [asistente de etiquetas delimitadoras](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) ha usado el atributo `asp-route-{value}` para generar un vínculo a la página de edición.</span><span class="sxs-lookup"><span data-stu-id="2fb81-219">The `<a /a>` [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="2fb81-220">El vínculo contiene datos de ruta con el identificador del contacto.</span><span class="sxs-lookup"><span data-stu-id="2fb81-220">The link contains route data with the contact ID.</span></span> <span data-ttu-id="2fb81-221">Por ejemplo: `https://localhost:5001/Edit/1`.</span><span class="sxs-lookup"><span data-stu-id="2fb81-221">For example, `https://localhost:5001/Edit/1`.</span></span> <span data-ttu-id="2fb81-222">Los [asistentes de etiquetas](xref:mvc/views/tag-helpers/intro) permiten que el código de servidor participe en la creación y la representación de elementos HTML en archivos de Razor.</span><span class="sxs-lookup"><span data-stu-id="2fb81-222">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>

<span data-ttu-id="2fb81-223">El archivo *index.cshtml* contiene marcado para crear un botón de eliminar para cada contacto de cliente:</span><span class="sxs-lookup"><span data-stu-id="2fb81-223">The *Index.cshtml* file contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=22-23)]

<span data-ttu-id="2fb81-224">El código HTML representado:</span><span class="sxs-lookup"><span data-stu-id="2fb81-224">The rendered HTML:</span></span>

```html
<button type="submit" formaction="/Customers?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="2fb81-225">Al representar el botón de eliminar en HTML, el elemento [formaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) incluye parámetros para los siguientes elementos:</span><span class="sxs-lookup"><span data-stu-id="2fb81-225">When the delete button is rendered in HTML, its [formaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) includes parameters for:</span></span>

* <span data-ttu-id="2fb81-226">Id. de contacto de cliente especificado mediante el atributo `asp-route-id`.</span><span class="sxs-lookup"><span data-stu-id="2fb81-226">The customer contact ID, specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="2fb81-227">`handler` especificado mediante el atributo `asp-page-handler`.</span><span class="sxs-lookup"><span data-stu-id="2fb81-227">The `handler`, specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="2fb81-228">Al seleccionar el botón, se envía una solicitud de formulario `POST` al servidor.</span><span class="sxs-lookup"><span data-stu-id="2fb81-228">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="2fb81-229">De forma predeterminada, el nombre del método de control se selecciona de acuerdo con el valor del parámetro `handler` y según el esquema `OnPost[handler]Async`.</span><span class="sxs-lookup"><span data-stu-id="2fb81-229">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="2fb81-230">Como en este ejemplo `handler` es `delete`, el método de control `OnPostDeleteAsync` se usa para procesar la solicitud `POST`.</span><span class="sxs-lookup"><span data-stu-id="2fb81-230">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="2fb81-231">Si `asp-page-handler` se establece en otro valor, como `remove`, se seleccionará un método de controlador llamado `OnPostRemoveAsync`.</span><span class="sxs-lookup"><span data-stu-id="2fb81-231">If the `asp-page-handler` is set to a different value, such as `remove`, a handler method with the name `OnPostRemoveAsync` is selected.</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="2fb81-232">El método `OnPostDeleteAsync` realiza las acciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="2fb81-232">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="2fb81-233">Obtiene el elemento `id` de la cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="2fb81-233">Gets the `id` from the query string.</span></span>
* <span data-ttu-id="2fb81-234">Realiza una consulta a la base de datos del contacto de cliente con `FindAsync`.</span><span class="sxs-lookup"><span data-stu-id="2fb81-234">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="2fb81-235">Si se encuentra el contacto del cliente, este se quita y se actualiza la base de datos.</span><span class="sxs-lookup"><span data-stu-id="2fb81-235">If the customer contact is found, it's removed and the database is updated.</span></span>
* <span data-ttu-id="2fb81-236">Llama a <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> para redirigir la página Index raíz (`/Index`).</span><span class="sxs-lookup"><span data-stu-id="2fb81-236">Calls <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> to redirect to the root Index page (`/Index`).</span></span>

### <a name="the-editcshtml-file"></a><span data-ttu-id="2fb81-237">El archivo Edit.cshtml</span><span class="sxs-lookup"><span data-stu-id="2fb81-237">The Edit.cshtml file</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml?highlight=1)]

<span data-ttu-id="2fb81-238">La primera línea contiene la directiva `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="2fb81-238">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="2fb81-239">La restricción de enrutamiento `"{id:int}"` indica a la página que acepte las solicitudes a la página que contienen datos de ruta `int`.</span><span class="sxs-lookup"><span data-stu-id="2fb81-239">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="2fb81-240">Si una solicitud a la página no contiene datos de ruta que se puedan convertir en `int`, el tiempo de ejecución devuelve un error HTTP 404 (no encontrado).</span><span class="sxs-lookup"><span data-stu-id="2fb81-240">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="2fb81-241">Para que el identificador sea opcional, anexe `?` a la restricción de ruta:</span><span class="sxs-lookup"><span data-stu-id="2fb81-241">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="2fb81-242">El archivo *Edit.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="2fb81-242">The *Edit.cshtml.cs* file:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml.cs?name=snippet)]

## <a name="validation"></a><span data-ttu-id="2fb81-243">Validación</span><span class="sxs-lookup"><span data-stu-id="2fb81-243">Validation</span></span>

<span data-ttu-id="2fb81-244">Reglas de validación:</span><span class="sxs-lookup"><span data-stu-id="2fb81-244">Validation rules:</span></span>

* <span data-ttu-id="2fb81-245">Se especifican mediante declaración en la clase de modelo.</span><span class="sxs-lookup"><span data-stu-id="2fb81-245">Are declaratively specified in the model class.</span></span>
* <span data-ttu-id="2fb81-246">Se aplican en toda la aplicación.</span><span class="sxs-lookup"><span data-stu-id="2fb81-246">Are enforced everywhere in the app.</span></span>

<span data-ttu-id="2fb81-247">El espacio de nombres <xref:System.ComponentModel.DataAnnotations> proporciona un conjunto de atributos de validación integrados que se aplican mediante declaración a una clase o propiedad.</span><span class="sxs-lookup"><span data-stu-id="2fb81-247">The <xref:System.ComponentModel.DataAnnotations> namespace provides a set of built-in validation attributes that are applied declaratively to a class or property.</span></span> <span data-ttu-id="2fb81-248">DataAnnotations también contiene atributos de formato como [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute), que ayudan a aplicar formato y no proporcionan ninguna validación.</span><span class="sxs-lookup"><span data-stu-id="2fb81-248">DataAnnotations also contains formatting attributes like [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) that help with formatting and don't provide any validation.</span></span>

<span data-ttu-id="2fb81-249">Considere el modelo `Customer`:</span><span class="sxs-lookup"><span data-stu-id="2fb81-249">Consider the `Customer` model:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

<span data-ttu-id="2fb81-250">Con el siguiente archivo de vista *Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="2fb81-250">Using the following *Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=3,8-9,15-99)]

<span data-ttu-id="2fb81-251">El código anterior:</span><span class="sxs-lookup"><span data-stu-id="2fb81-251">The preceding code:</span></span>

* <span data-ttu-id="2fb81-252">Incluye scripts de validación de jQuery y jQuery.</span><span class="sxs-lookup"><span data-stu-id="2fb81-252">Includes jQuery and jQuery validation scripts.</span></span>
* <span data-ttu-id="2fb81-253">Usa los [asistentes de etiquetas](xref:mvc/views/tag-helpers/intro) `<div />` y `<span />` para habilitar lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="2fb81-253">Uses the `<div />` and `<span />` [Tag Helpers](xref:mvc/views/tag-helpers/intro) to enable:</span></span>

  * <span data-ttu-id="2fb81-254">Validación del lado cliente.</span><span class="sxs-lookup"><span data-stu-id="2fb81-254">Client-side validation.</span></span>
  * <span data-ttu-id="2fb81-255">Representación del error de validación.</span><span class="sxs-lookup"><span data-stu-id="2fb81-255">Validation error rendering.</span></span>

* <span data-ttu-id="2fb81-256">Se genera el siguiente código HTML:</span><span class="sxs-lookup"><span data-stu-id="2fb81-256">Generates the following HTML:</span></span>

  [!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create5.html)]

<span data-ttu-id="2fb81-257">Al publicar el formulario de creación sin un valor de nombre, se muestra el mensaje de error "El campo Nombre es obligatorio".</span><span class="sxs-lookup"><span data-stu-id="2fb81-257">Posting the Create form without a name value displays the error message "The Name field is required."</span></span> <span data-ttu-id="2fb81-258">en el formulario.</span><span class="sxs-lookup"><span data-stu-id="2fb81-258">on the form.</span></span> <span data-ttu-id="2fb81-259">Si JavaScript está habilitado en el cliente, el explorador muestra el error sin realizar la publicación en el servidor.</span><span class="sxs-lookup"><span data-stu-id="2fb81-259">If JavaScript is enabled on the client, the browser displays the error without posting to the server.</span></span>

<span data-ttu-id="2fb81-260">El atributo `[StringLength(10)]` genera `data-val-length-max="10"` en el código HTML representado.</span><span class="sxs-lookup"><span data-stu-id="2fb81-260">The `[StringLength(10)]` attribute generates `data-val-length-max="10"` on the rendered HTML.</span></span> <span data-ttu-id="2fb81-261">`data-val-length-max` impide que los exploradores superen la longitud máxima especificada al escribir.</span><span class="sxs-lookup"><span data-stu-id="2fb81-261">`data-val-length-max` prevents browsers from entering more than the maximum length specified.</span></span> <span data-ttu-id="2fb81-262">Si se usa una herramienta como [Fiddler](https://www.telerik.com/fiddler) para editar y reproducir la publicación:</span><span class="sxs-lookup"><span data-stu-id="2fb81-262">If a tool such as [Fiddler](https://www.telerik.com/fiddler) is used to edit and replay the post:</span></span>

* <span data-ttu-id="2fb81-263">Con el nombre de más de 10 caracteres.</span><span class="sxs-lookup"><span data-stu-id="2fb81-263">With the name longer than 10.</span></span>
* <span data-ttu-id="2fb81-264">Se devolverá el mensaje de error "El nombre del campo debe ser una cadena con una longitud máxima de 10 caracteres".</span><span class="sxs-lookup"><span data-stu-id="2fb81-264">The error message "The field Name must be a string with a maximum length of 10."</span></span> <span data-ttu-id="2fb81-265">.</span><span class="sxs-lookup"><span data-stu-id="2fb81-265">is returned.</span></span>

<span data-ttu-id="2fb81-266">Considere el modelo `Movie` siguiente:</span><span class="sxs-lookup"><span data-stu-id="2fb81-266">Consider the following `Movie` model:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

<span data-ttu-id="2fb81-267">Los atributos de validación especifican el comportamiento que se exigirá a las propiedades del modelo al que se aplican:</span><span class="sxs-lookup"><span data-stu-id="2fb81-267">The validation attributes specify behavior to enforce on the model properties they're applied to:</span></span>

* <span data-ttu-id="2fb81-268">Los atributos `Required` y `MinimumLength` indican que una propiedad debe tener un valor, pero nada impide al usuario escribir espacios en blanco para satisfacer esta validación.</span><span class="sxs-lookup"><span data-stu-id="2fb81-268">The `Required` and `MinimumLength` attributes indicate that a property must have a value, but nothing prevents a user from entering white space to satisfy this validation.</span></span>
* <span data-ttu-id="2fb81-269">El atributo `RegularExpression` se usa para limitar los caracteres que se pueden escribir.</span><span class="sxs-lookup"><span data-stu-id="2fb81-269">The `RegularExpression` attribute is used to limit what characters can be input.</span></span> <span data-ttu-id="2fb81-270">En el código anterior, "Género":</span><span class="sxs-lookup"><span data-stu-id="2fb81-270">In the preceding code, "Genre":</span></span>

  * <span data-ttu-id="2fb81-271">Solo debe usar letras.</span><span class="sxs-lookup"><span data-stu-id="2fb81-271">Must only use letters.</span></span>
  * <span data-ttu-id="2fb81-272">La primera letra debe estar en mayúsculas.</span><span class="sxs-lookup"><span data-stu-id="2fb81-272">The first letter is required to be uppercase.</span></span> <span data-ttu-id="2fb81-273">No se permiten espacios en blanco, números ni caracteres especiales.</span><span class="sxs-lookup"><span data-stu-id="2fb81-273">White space, numbers, and special characters are not allowed.</span></span>

* <span data-ttu-id="2fb81-274">La "Clasificación" de `RegularExpression`:</span><span class="sxs-lookup"><span data-stu-id="2fb81-274">The `RegularExpression` "Rating":</span></span>

  * <span data-ttu-id="2fb81-275">Requiere que el primer carácter sea una letra mayúscula.</span><span class="sxs-lookup"><span data-stu-id="2fb81-275">Requires that the first character be an uppercase letter.</span></span>
  * <span data-ttu-id="2fb81-276">Permite caracteres especiales y números en los espacios posteriores.</span><span class="sxs-lookup"><span data-stu-id="2fb81-276">Allows special characters and numbers in subsequent spaces.</span></span> <span data-ttu-id="2fb81-277">"PG-13" es válido para una "Clasificación", pero se produce un error en un "Género".</span><span class="sxs-lookup"><span data-stu-id="2fb81-277">"PG-13" is valid for a rating, but fails for a "Genre".</span></span>

* <span data-ttu-id="2fb81-278">El atributo `Range` restringe un valor a un intervalo determinado.</span><span class="sxs-lookup"><span data-stu-id="2fb81-278">The `Range` attribute constrains a value to within a specified range.</span></span>
* <span data-ttu-id="2fb81-279">El atributo `StringLength` establece la longitud máxima de una propiedad de cadena y, opcionalmente, su longitud mínima.</span><span class="sxs-lookup"><span data-stu-id="2fb81-279">The `StringLength` attribute sets the maximum length of a string property, and optionally its minimum length.</span></span>
* <span data-ttu-id="2fb81-280">Los tipos de valor (como `decimal`, `int`, `float`, `DateTime`) son intrínsecamente necesarios y no necesitan el atributo `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="2fb81-280">Value types (such as `decimal`, `int`, `float`, `DateTime`) are inherently required and don't need the `[Required]` attribute.</span></span>

<span data-ttu-id="2fb81-281">La página de creación del modelo `Movie` muestra errores de visualización con valores no válidos:</span><span class="sxs-lookup"><span data-stu-id="2fb81-281">The Create page for the `Movie` model shows displays errors with invalid values:</span></span>

![Formulario de vista de película con varios errores de validación de cliente de jQuery](~/tutorials/razor-pages/validation/_static/val.png)

<span data-ttu-id="2fb81-283">Para obtener más información, consulte:</span><span class="sxs-lookup"><span data-stu-id="2fb81-283">For more information, see:</span></span>

* [<span data-ttu-id="2fb81-284">Adición de validación a la aplicación Película</span><span class="sxs-lookup"><span data-stu-id="2fb81-284">Add validation to the Movie app</span></span>](xref:tutorials/razor-pages/validation)
* <span data-ttu-id="2fb81-285">[Validación de modelos en ASP.NET Core](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="2fb81-285">[Model validation in ASP.NET Core](xref:mvc/models/validation).</span></span>

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a><span data-ttu-id="2fb81-286">Control de solicitudes HEAD con un controlador OnGet de reserva</span><span class="sxs-lookup"><span data-stu-id="2fb81-286">Handle HEAD requests with an OnGet handler fallback</span></span>

<span data-ttu-id="2fb81-287">Las solicitudes `HEAD` permiten recuperar los encabezados de un recurso específico.</span><span class="sxs-lookup"><span data-stu-id="2fb81-287">`HEAD` requests allow retrieving the headers for a specific resource.</span></span> <span data-ttu-id="2fb81-288">A diferencia de las solicitudes `GET`, las solicitudes `HEAD` no devuelven un cuerpo de respuesta.</span><span class="sxs-lookup"><span data-stu-id="2fb81-288">Unlike `GET` requests, `HEAD` requests don't return a response body.</span></span>

<span data-ttu-id="2fb81-289">Normalmente, se crea un controlador `OnHead` al que se llama para las solicitudes `HEAD`:</span><span class="sxs-lookup"><span data-stu-id="2fb81-289">Ordinarily, an `OnHead` handler is created and called for `HEAD` requests:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="2fb81-290">Razor Pages recurre a una llamada al controlador `OnGet` si no se define ningún controlador `OnHead`.</span><span class="sxs-lookup"><span data-stu-id="2fb81-290">Razor Pages falls back to calling the `OnGet` handler if no `OnHead` handler is defined.</span></span>

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-razor-pages"></a><span data-ttu-id="2fb81-291">XSRF/CSRF y páginas de Razor</span><span class="sxs-lookup"><span data-stu-id="2fb81-291">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="2fb81-292">Razor Pages está protegido mediante [validación antifalsificación](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="2fb81-292">Razor Pages are protected by [Antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="2fb81-293">El elemento [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) inserta tokens antifalsificación en los elementos de formulario HTML.</span><span class="sxs-lookup"><span data-stu-id="2fb81-293">The [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injects antiforgery tokens into HTML form elements.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-razor-pages"></a><span data-ttu-id="2fb81-294">Usar diseños, parciales, plantillas y asistentes de etiquetas con las páginas de Razor</span><span class="sxs-lookup"><span data-stu-id="2fb81-294">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="2fb81-295">Las páginas funcionan con todas las características del motor de vista de Razor.</span><span class="sxs-lookup"><span data-stu-id="2fb81-295">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="2fb81-296">Los diseños, parciales, plantillas, asistentes de etiquetas, *_ViewStart.cshtml* y *_ViewImports.cshtml* funcionan de la misma manera que lo hacen con las vistas de Razor convencionales.</span><span class="sxs-lookup"><span data-stu-id="2fb81-296">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, and *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="2fb81-297">Para simplificar esta página, aprovecharemos algunas de esas características.</span><span class="sxs-lookup"><span data-stu-id="2fb81-297">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

<span data-ttu-id="2fb81-298">Agregue una [página de diseño](xref:mvc/views/layout) a *Pages/Shared/_Layout.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="2fb81-298">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml*:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Shared/_Layout2.cshtml?hightlight=12)]

<span data-ttu-id="2fb81-299">El [diseño](xref:mvc/views/layout):</span><span class="sxs-lookup"><span data-stu-id="2fb81-299">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="2fb81-300">Controla el diseño de cada página (a no ser que la página no tenga diseño).</span><span class="sxs-lookup"><span data-stu-id="2fb81-300">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="2fb81-301">Importa las estructuras HTML como JavaScript y hojas de estilos.</span><span class="sxs-lookup"><span data-stu-id="2fb81-301">Imports HTML structures such as JavaScript and stylesheets.</span></span>
* <span data-ttu-id="2fb81-302">El contenido de la página de Razor se representa donde se llama a `@RenderBody()`.</span><span class="sxs-lookup"><span data-stu-id="2fb81-302">The contents of the Razor page are rendered where `@RenderBody()` is called.</span></span>

<span data-ttu-id="2fb81-303">Para más información, consulte la [página de diseño](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="2fb81-303">For more information, see [layout page](xref:mvc/views/layout).</span></span>

<span data-ttu-id="2fb81-304">La propiedad [Layout](xref:mvc/views/layout#specifying-a-layout) se establece en *Pages/_ViewStart.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="2fb81-304">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="2fb81-305">El diseño está en la carpeta *Pages/Shared*.</span><span class="sxs-lookup"><span data-stu-id="2fb81-305">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="2fb81-306">Las páginas buscan otras vistas (diseños, plantillas, parciales) de forma jerárquica, a partir de la misma carpeta que la página actual.</span><span class="sxs-lookup"><span data-stu-id="2fb81-306">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="2fb81-307">Un diseño en la carpeta *Pages/Shared* se puede usar desde cualquier página de Razor en la carpeta *Pages*.</span><span class="sxs-lookup"><span data-stu-id="2fb81-307">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="2fb81-308">El archivo de diseño debería ir en la carpeta *Pages/Shared*.</span><span class="sxs-lookup"><span data-stu-id="2fb81-308">The layout file should go in the *Pages/Shared* folder.</span></span>

<span data-ttu-id="2fb81-309">Le recomendamos que **no** coloque el archivo de diseño en la carpeta *Views/Shared*.</span><span class="sxs-lookup"><span data-stu-id="2fb81-309">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="2fb81-310">*Views/Shared* es un patrón de vistas de MVC.</span><span class="sxs-lookup"><span data-stu-id="2fb81-310">*Views/Shared* is an MVC views pattern.</span></span> <span data-ttu-id="2fb81-311">Las páginas de Razor están diseñadas para basarse en la jerarquía de carpetas, no en las convenciones de ruta de acceso.</span><span class="sxs-lookup"><span data-stu-id="2fb81-311">Razor Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="2fb81-312">La búsqueda de vistas de una página de Razor incluye la carpeta *Pages*.</span><span class="sxs-lookup"><span data-stu-id="2fb81-312">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="2fb81-313">Los diseños, plantillas y parciales que se usan con los controladores de MVC y las vistas de Razor convencionales *simplemente funcionan*.</span><span class="sxs-lookup"><span data-stu-id="2fb81-313">The layouts, templates, and partials used with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="2fb81-314">Agregue un archivo *Pages/_ViewImports.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="2fb81-314">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="2fb81-315">`@namespace` se explica más adelante en el tutorial.</span><span class="sxs-lookup"><span data-stu-id="2fb81-315">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="2fb81-316">La directiva `@addTagHelper` pone los [asistentes de etiquetas integradas](xref:mvc/views/tag-helpers/builtin-th/Index) en todas las páginas de la carpeta *Pages*.</span><span class="sxs-lookup"><span data-stu-id="2fb81-316">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="2fb81-317">La directiva `@namespace` establecida en una página:</span><span class="sxs-lookup"><span data-stu-id="2fb81-317">The `@namespace` directive set on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="2fb81-318">La directiva `@namespace` establece el espacio de nombres de la página.</span><span class="sxs-lookup"><span data-stu-id="2fb81-318">The `@namespace` directive sets the namespace for the page.</span></span> <span data-ttu-id="2fb81-319">La directiva `@model` no necesita incluir el espacio de nombres.</span><span class="sxs-lookup"><span data-stu-id="2fb81-319">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="2fb81-320">Cuando la directiva `@namespace` se encuentra en *_ViewImports.cshtml*, el espacio de nombres especificado proporciona el prefijo del espacio de nombres generado en la página que importa la directiva `@namespace`.</span><span class="sxs-lookup"><span data-stu-id="2fb81-320">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="2fb81-321">El resto del espacio de nombres generado (la parte del sufijo) es la ruta de acceso relativa separada por puntos entre la carpeta que contiene *_ViewImports.cshtml* y la carpeta que contiene la página.</span><span class="sxs-lookup"><span data-stu-id="2fb81-321">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="2fb81-322">Por ejemplo, la clase `PageModel`*Pages/Customers/Edit.cshtml.cs* establece explícitamente el espacio de nombres:</span><span class="sxs-lookup"><span data-stu-id="2fb81-322">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="2fb81-323">El archivo *Pages/_ViewImports.cshtml* establece el espacio de nombres siguiente:</span><span class="sxs-lookup"><span data-stu-id="2fb81-323">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="2fb81-324">El espacio de nombres generado para la página de Razor *Pages/Customers/Edit.cshtml* es el mismo que la clase `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="2fb81-324">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="2fb81-325">`@namespace` *también funciona con vistas de Razor convencionales.*</span><span class="sxs-lookup"><span data-stu-id="2fb81-325">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="2fb81-326">Considere el archivo de vista *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="2fb81-326">Consider the *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=2-3)]

<span data-ttu-id="2fb81-327">Archivo de vista actualizado *Pages/Create.cshtml* con *_ViewImports.cshtml* y el archivo de distribución anterior:</span><span class="sxs-lookup"><span data-stu-id="2fb81-327">The updated *Pages/Create.cshtml* view file with *_ViewImports.cshtml* and the preceding layout file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.cshtml?highlight=2)]

<span data-ttu-id="2fb81-328">En el código anterior, el elemento *_ViewImports. cshtml* importó el espacio de nombres y los asistentes de etiquetas.</span><span class="sxs-lookup"><span data-stu-id="2fb81-328">In the preceding code, the *_ViewImports.cshtml* imported the namespace and Tag Helpers.</span></span> <span data-ttu-id="2fb81-329">El archivo de distribución importó los archivos JavaScript.</span><span class="sxs-lookup"><span data-stu-id="2fb81-329">The layout file imported the JavaScript files.</span></span>

<span data-ttu-id="2fb81-330">El [proyecto de inicio de las páginas de Razor](#rpvs17) contiene *Pages/_ValidationScriptsPartial.cshtml*, que enlaza la validación del lado cliente.</span><span class="sxs-lookup"><span data-stu-id="2fb81-330">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<span data-ttu-id="2fb81-331">Para más información sobre las vistas parciales, vea <xref:mvc/views/partial>.</span><span class="sxs-lookup"><span data-stu-id="2fb81-331">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="2fb81-332">Generación de direcciones URL para las páginas</span><span class="sxs-lookup"><span data-stu-id="2fb81-332">URL generation for Pages</span></span>

<span data-ttu-id="2fb81-333">La página `Create`, mostrada anteriormente, usa `RedirectToPage`:</span><span class="sxs-lookup"><span data-stu-id="2fb81-333">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=28)]

<span data-ttu-id="2fb81-334">La aplicación tiene la siguiente estructura de archivos o carpetas:</span><span class="sxs-lookup"><span data-stu-id="2fb81-334">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="2fb81-335">*/Pages*</span><span class="sxs-lookup"><span data-stu-id="2fb81-335">*/Pages*</span></span>

  * <span data-ttu-id="2fb81-336">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="2fb81-336">*Index.cshtml*</span></span>
  * <span data-ttu-id="2fb81-337">*Privacy.cshtml*</span><span class="sxs-lookup"><span data-stu-id="2fb81-337">*Privacy.cshtml*</span></span>
  * <span data-ttu-id="2fb81-338">*/Customers*</span><span class="sxs-lookup"><span data-stu-id="2fb81-338">*/Customers*</span></span>

    * <span data-ttu-id="2fb81-339">*Create.cshtml*</span><span class="sxs-lookup"><span data-stu-id="2fb81-339">*Create.cshtml*</span></span>
    * <span data-ttu-id="2fb81-340">*Edit.cshtml*</span><span class="sxs-lookup"><span data-stu-id="2fb81-340">*Edit.cshtml*</span></span>
    * <span data-ttu-id="2fb81-341">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="2fb81-341">*Index.cshtml*</span></span>

<span data-ttu-id="2fb81-342">Las páginas *Pages/Customers/Create.cshtml* y *Pages/Customers/Edit.cshtml* redirigen a *Pages/Customers/Index.cshtml* si la operación se realiza correctamente.</span><span class="sxs-lookup"><span data-stu-id="2fb81-342">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Customers/Index.cshtml* after success.</span></span> <span data-ttu-id="2fb81-343">La cadena `./Index` es un nombre de página relativo que se usa para acceder a la página anterior.</span><span class="sxs-lookup"><span data-stu-id="2fb81-343">The string `./Index` is a relative page name used to access the preceding page.</span></span> <span data-ttu-id="2fb81-344">Se usa para generar direcciones URL a la página *Pages/Customers/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="2fb81-344">It is used to generate URLs to the *Pages/Customers/Index.cshtml* page.</span></span> <span data-ttu-id="2fb81-345">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="2fb81-345">For example:</span></span>

* `Url.Page("./Index", ...)`
* `<a asp-page="./Index">Customers Index Page</a>`
* `RedirectToPage("./Index")`

<span data-ttu-id="2fb81-346">El nombre de página absoluto `/Index` se usa para generar direcciones URL a la página *Pages/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="2fb81-346">The absolute page name `/Index` is used to generate URLs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="2fb81-347">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="2fb81-347">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">Home Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="2fb81-348">El nombre de página es la ruta de acceso a la página de la carpeta raíz */Pages*, incluido un `/` inicial, por ejemplo `/Index`.</span><span class="sxs-lookup"><span data-stu-id="2fb81-348">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="2fb81-349">Los ejemplos anteriores de generación de URL ofrecen opciones mejoradas y capacidades funcionales en comparación con la escritura a mano de estas.</span><span class="sxs-lookup"><span data-stu-id="2fb81-349">The preceding URL generation samples offer enhanced options and functional capabilities over hard-coding a URL.</span></span> <span data-ttu-id="2fb81-350">La generación de direcciones URL usa el [enrutamiento](xref:mvc/controllers/routing) y puede generar y codificar parámetros según cómo se defina la ruta en la ruta de acceso de destino.</span><span class="sxs-lookup"><span data-stu-id="2fb81-350">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="2fb81-351">La generación de direcciones URL para las páginas admite nombres relativos.</span><span class="sxs-lookup"><span data-stu-id="2fb81-351">URL generation for pages supports relative names.</span></span> <span data-ttu-id="2fb81-352">En la siguiente tabla, se muestra qué página de índice está seleccionada con diferentes parámetros `RedirectToPage` en *Pages/Customers/Create.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="2fb81-352">The following table shows which Index page is selected using different `RedirectToPage` parameters in *Pages/Customers/Create.cshtml*.</span></span>

| <span data-ttu-id="2fb81-353">RedirectToPage(x)</span><span class="sxs-lookup"><span data-stu-id="2fb81-353">RedirectToPage(x)</span></span>| <span data-ttu-id="2fb81-354">Página</span><span class="sxs-lookup"><span data-stu-id="2fb81-354">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="2fb81-355">RedirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="2fb81-355">RedirectToPage("/Index")</span></span> | <span data-ttu-id="2fb81-356">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="2fb81-356">*Pages/Index*</span></span> |
| <span data-ttu-id="2fb81-357">RedirectToPage("./Index");</span><span class="sxs-lookup"><span data-stu-id="2fb81-357">RedirectToPage("./Index");</span></span> | <span data-ttu-id="2fb81-358">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="2fb81-358">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="2fb81-359">RedirectToPage("../Index")</span><span class="sxs-lookup"><span data-stu-id="2fb81-359">RedirectToPage("../Index")</span></span> | <span data-ttu-id="2fb81-360">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="2fb81-360">*Pages/Index*</span></span> |
| <span data-ttu-id="2fb81-361">RedirectToPage("Index")</span><span class="sxs-lookup"><span data-stu-id="2fb81-361">RedirectToPage("Index")</span></span>  | <span data-ttu-id="2fb81-362">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="2fb81-362">*Pages/Customers/Index*</span></span> |

<!-- Test via ~/razor-pages/index/3.0sample/RazorPagesContacts/Pages/Customers/Details.cshtml.cs -->

<span data-ttu-id="2fb81-363">`RedirectToPage("Index")`, `RedirectToPage("./Index")` y `RedirectToPage("../Index")` son *nombres relativos*.</span><span class="sxs-lookup"><span data-stu-id="2fb81-363">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")` are *relative names*.</span></span> <span data-ttu-id="2fb81-364">El parámetro `RedirectToPage` se *combina* con la ruta de acceso de la página actual para calcular el nombre de la página de destino.</span><span class="sxs-lookup"><span data-stu-id="2fb81-364">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>

<span data-ttu-id="2fb81-365">Vincular el nombre relativo es útil al crear sitios con una estructura compleja.</span><span class="sxs-lookup"><span data-stu-id="2fb81-365">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="2fb81-366">Cuando se usan nombres relativos para el vínculo entre las páginas de una carpeta:</span><span class="sxs-lookup"><span data-stu-id="2fb81-366">When relative names are used to link between pages in a folder:</span></span>

* <span data-ttu-id="2fb81-367">Al cambiar el nombre de una carpeta, no se rompen los vínculos relativos.</span><span class="sxs-lookup"><span data-stu-id="2fb81-367">Renaming a folder doesn't break the relative links.</span></span>
* <span data-ttu-id="2fb81-368">Los vínculos no se rompen porque no incluyen el nombre de la carpeta.</span><span class="sxs-lookup"><span data-stu-id="2fb81-368">Links are not broken because they don't include the folder name.</span></span>

<span data-ttu-id="2fb81-369">Para redirigir a una página en otra [área](xref:mvc/controllers/areas), especifique el área:</span><span class="sxs-lookup"><span data-stu-id="2fb81-369">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="2fb81-370">Para obtener más información, vea <xref:mvc/controllers/areas> y <xref:razor-pages/razor-pages-conventions>.</span><span class="sxs-lookup"><span data-stu-id="2fb81-370">For more information, see <xref:mvc/controllers/areas> and <xref:razor-pages/razor-pages-conventions>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="2fb81-371">Atributo ViewData</span><span class="sxs-lookup"><span data-stu-id="2fb81-371">ViewData attribute</span></span>

<span data-ttu-id="2fb81-372">Se pueden pasar datos a una página con <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute>.</span><span class="sxs-lookup"><span data-stu-id="2fb81-372">Data can be passed to a page with <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute>.</span></span> <span data-ttu-id="2fb81-373">Las propiedades con el atributo `[ViewData]` tienen sus valores almacenados y cargados desde el elemento <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary>.</span><span class="sxs-lookup"><span data-stu-id="2fb81-373">Properties with the `[ViewData]` attribute have their values stored and loaded from the <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary>.</span></span>

<span data-ttu-id="2fb81-374">En el ejemplo siguiente, el elemento `AboutModel` aplica el atributo `[ViewData]` a la propiedad `Title`:</span><span class="sxs-lookup"><span data-stu-id="2fb81-374">In the following example, the `AboutModel` applies the `[ViewData]` attribute to the `Title` property:</span></span>

```csharp
public class AboutModel : PageModel
{
    [ViewData]
    public string Title { get; } = "About";

    public void OnGet()
    {
    }
}
```

<span data-ttu-id="2fb81-375">En la página Acerca de, acceda a la propiedad `Title` como propiedad de modelo:</span><span class="sxs-lookup"><span data-stu-id="2fb81-375">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="2fb81-376">En el diseño, el título se lee desde el diccionario ViewData:</span><span class="sxs-lookup"><span data-stu-id="2fb81-376">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a><span data-ttu-id="2fb81-377">TempData</span><span class="sxs-lookup"><span data-stu-id="2fb81-377">TempData</span></span>

<span data-ttu-id="2fb81-378">ASP.NET Core expone el elemento <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>.</span><span class="sxs-lookup"><span data-stu-id="2fb81-378">ASP.NET Core exposes the <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>.</span></span> <span data-ttu-id="2fb81-379">Esta propiedad almacena datos hasta que se leen.</span><span class="sxs-lookup"><span data-stu-id="2fb81-379">This property stores data until it's read.</span></span> <span data-ttu-id="2fb81-380">Los métodos <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> y <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> se pueden usar para examinar los datos sin que se eliminen.</span><span class="sxs-lookup"><span data-stu-id="2fb81-380">The <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> and <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="2fb81-381">`TempData` es útil para el redireccionamiento cuando se necesitan los datos de más de una única solicitud.</span><span class="sxs-lookup"><span data-stu-id="2fb81-381">`TempData` is useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="2fb81-382">El siguiente código establece el valor de `Message` mediante `TempData`:</span><span class="sxs-lookup"><span data-stu-id="2fb81-382">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="2fb81-383">El siguiente marcado en el archivo *Pages/Customers/Index.cshtml* muestra el valor de `Message` mediante `TempData`.</span><span class="sxs-lookup"><span data-stu-id="2fb81-383">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="2fb81-384">El modelo de página *Pages/Customers/Index.cshtml.cs* aplica el atributo `[TempData]` a la propiedad `Message`.</span><span class="sxs-lookup"><span data-stu-id="2fb81-384">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```csharp
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="2fb81-385">Para más información, consulte [TempData](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="2fb81-385">For more information, see [TempData](xref:fundamentals/app-state#tempdata).</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="2fb81-386">Varios controladores por página</span><span class="sxs-lookup"><span data-stu-id="2fb81-386">Multiple handlers per page</span></span>

<span data-ttu-id="2fb81-387">En la página siguiente se usa el asistente de etiquetas `asp-page-handler` para generar marcado para dos controladores:</span><span class="sxs-lookup"><span data-stu-id="2fb81-387">The following page generates markup for two handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<span data-ttu-id="2fb81-388">El formulario del ejemplo anterior tiene dos botones de envío, y cada uno de ellos usa `FormActionTagHelper` para enviar a una dirección URL diferente.</span><span class="sxs-lookup"><span data-stu-id="2fb81-388">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="2fb81-389">El atributo `asp-page-handler` es un complemento de `asp-page`.</span><span class="sxs-lookup"><span data-stu-id="2fb81-389">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="2fb81-390">`asp-page-handler` genera direcciones URL que envían a cada uno de los métodos de controlador definidos por una página.</span><span class="sxs-lookup"><span data-stu-id="2fb81-390">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="2fb81-391">`asp-page` no se especifica porque el ejemplo se vincula a la página actual.</span><span class="sxs-lookup"><span data-stu-id="2fb81-391">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="2fb81-392">Modelo de página:</span><span class="sxs-lookup"><span data-stu-id="2fb81-392">The page model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="2fb81-393">El código anterior usa *métodos de controlador con nombre*.</span><span class="sxs-lookup"><span data-stu-id="2fb81-393">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="2fb81-394">Los métodos de controlador con nombre se crean tomando el texto en el nombre después de `On<HTTP Verb>` y antes de `Async` (si existe).</span><span class="sxs-lookup"><span data-stu-id="2fb81-394">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="2fb81-395">En el ejemplo anterior, los métodos de página son OnPost**JoinList**Async y OnPost**JoinListUC**Async.</span><span class="sxs-lookup"><span data-stu-id="2fb81-395">In the preceding example, the page methods are OnPost**JoinList**Async and OnPost**JoinListUC**Async.</span></span> <span data-ttu-id="2fb81-396">Quitando *OnPost* y *Async*, los nombres de controlador son `JoinList` y `JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="2fb81-396">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="2fb81-397">Al usar el código anterior, la ruta de dirección URL que envía a `OnPostJoinListAsync` es `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span><span class="sxs-lookup"><span data-stu-id="2fb81-397">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="2fb81-398">La ruta de dirección URL que envía a `OnPostJoinListUCAsync` es `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="2fb81-398">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="2fb81-399">Rutas personalizadas</span><span class="sxs-lookup"><span data-stu-id="2fb81-399">Custom routes</span></span>

<span data-ttu-id="2fb81-400">Use la directiva `@page` para:</span><span class="sxs-lookup"><span data-stu-id="2fb81-400">Use the `@page` directive to:</span></span>

* <span data-ttu-id="2fb81-401">Especificar una ruta personalizada a una página.</span><span class="sxs-lookup"><span data-stu-id="2fb81-401">Specify a custom route to a page.</span></span> <span data-ttu-id="2fb81-402">Por ejemplo, la ruta a la página Acerca de se puede establecer en `/Some/Other/Path` con `@page "/Some/Other/Path"`.</span><span class="sxs-lookup"><span data-stu-id="2fb81-402">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="2fb81-403">Anexar segmentos a la ruta predeterminada de una página.</span><span class="sxs-lookup"><span data-stu-id="2fb81-403">Append segments to a page's default route.</span></span> <span data-ttu-id="2fb81-404">Por ejemplo, se puede agregar un segmento "item" a la ruta predeterminada de una página con `@page "item"`.</span><span class="sxs-lookup"><span data-stu-id="2fb81-404">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="2fb81-405">Anexar parámetros a la ruta predeterminada de una página.</span><span class="sxs-lookup"><span data-stu-id="2fb81-405">Append parameters to a page's default route.</span></span> <span data-ttu-id="2fb81-406">Por ejemplo, un parámetro de identificador, `id`, puede ser necesario para una página con `@page "{id}"`.</span><span class="sxs-lookup"><span data-stu-id="2fb81-406">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="2fb81-407">Se admite una ruta de acceso relativa raíz designada por una tilde (`~`) al principio de la ruta de acceso.</span><span class="sxs-lookup"><span data-stu-id="2fb81-407">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="2fb81-408">Por ejemplo, `@page "~/Some/Other/Path"` es lo mismo que `@page "/Some/Other/Path"`.</span><span class="sxs-lookup"><span data-stu-id="2fb81-408">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="2fb81-409">Si no le gusta la cadena de consulta `?handler=JoinList` en la dirección URL, puede cambiar la ruta para poner el nombre del controlador en la parte de la ruta de la dirección URL.</span><span class="sxs-lookup"><span data-stu-id="2fb81-409">If you don't like the query string `?handler=JoinList` in the URL, change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="2fb81-410">Para personalizar la ruta, se puede agregar una plantilla de ruta entre comillas dobles después de la directiva `@page`.</span><span class="sxs-lookup"><span data-stu-id="2fb81-410">The route can be customized by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="2fb81-411">Al usar el código anterior, la ruta de dirección URL que envía a `OnPostJoinListAsync` es `https://localhost:5001/Customers/CreateFATH/JoinList`.</span><span class="sxs-lookup"><span data-stu-id="2fb81-411">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="2fb81-412">La ruta de dirección URL que envía a `OnPostJoinListUCAsync` es `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="2fb81-412">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="2fb81-413">El signo `?` que sigue a `handler` significa que el parámetro de ruta es opcional.</span><span class="sxs-lookup"><span data-stu-id="2fb81-413">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="advanced-configuration-and-settings"></a><span data-ttu-id="2fb81-414">Valores de configuración avanzados</span><span class="sxs-lookup"><span data-stu-id="2fb81-414">Advanced configuration and settings</span></span>

<span data-ttu-id="2fb81-415">La mayoría de las aplicaciones no requieren la configuración de las siguientes secciones.</span><span class="sxs-lookup"><span data-stu-id="2fb81-415">The configuration and settings in following sections is not required by most apps.</span></span>

<span data-ttu-id="2fb81-416">Para configurar opciones avanzadas, use el método de extensión <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>:</span><span class="sxs-lookup"><span data-stu-id="2fb81-416">To configure advanced options, use the extension method <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupRPoptions.cs?name=snippet)]

<span data-ttu-id="2fb81-417">Use el elemento <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> para establecer el directorio raíz de páginas o agregar convenciones de modelo de aplicación para las páginas.</span><span class="sxs-lookup"><span data-stu-id="2fb81-417">Use the <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="2fb81-418">Para obtener más información sobre las convenciones, vea [Convenciones de autorización de Razor Pages](xref:security/authorization/razor-pages-authorization).</span><span class="sxs-lookup"><span data-stu-id="2fb81-418">For more information on conventions, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization).</span></span>

<span data-ttu-id="2fb81-419">Para precompilar vistas, consulte la sección sobre la [compilación de vistas de Razor](xref:mvc/views/view-compilation).</span><span class="sxs-lookup"><span data-stu-id="2fb81-419">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation).</span></span>

### <a name="specify-that-razor-pages-are-at-the-content-root"></a><span data-ttu-id="2fb81-420">Especificar que las páginas de Razor se encuentran en la raíz de contenido</span><span class="sxs-lookup"><span data-stu-id="2fb81-420">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="2fb81-421">De forma predeterminada, la ruta raíz de las páginas de Razor es el directorio */Pages*.</span><span class="sxs-lookup"><span data-stu-id="2fb81-421">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="2fb81-422">Agregue <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> para especificar que sus Razor Pages se encuentran en la [raíz de contenido](xref:fundamentals/index#content-root) (<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>) de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="2fb81-422">Add <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> to specify that your Razor Pages are at the [content root](xref:fundamentals/index#content-root) (<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>) of the app:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesAtContentRoot.cs?name=snippet)]

### <a name="specify-that-razor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="2fb81-423">Especificar que las páginas de Razor se encuentran en un directorio raíz personalizado</span><span class="sxs-lookup"><span data-stu-id="2fb81-423">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="2fb81-424">Agregue <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*> para especificar que Razor Pages se encuentra en un directorio raíz personalizado en la aplicación (proporcione una ruta de acceso relativa):</span><span class="sxs-lookup"><span data-stu-id="2fb81-424">Add <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*> to specify that Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesRoot.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="2fb81-425">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="2fb81-425">Additional resources</span></span>

* <span data-ttu-id="2fb81-426">Consulte [Introducción a Razor Pages](xref:tutorials/razor-pages/razor-pages-start), que se basa en esta introducción.</span><span class="sxs-lookup"><span data-stu-id="2fb81-426">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction</span></span>
* <span data-ttu-id="2fb81-427">[Descargue o vea el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/3.0sample).</span><span class="sxs-lookup"><span data-stu-id="2fb81-427">[Download or view sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/3.0sample)</span></span>
* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>
* <xref:blazor/integrate-components>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2fb81-428">Por [Rick Anderson](https://twitter.com/RickAndMSFT) y [Ryan Nowak](https://github.com/rynowak)</span><span class="sxs-lookup"><span data-stu-id="2fb81-428">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Ryan Nowak](https://github.com/rynowak)</span></span>

<span data-ttu-id="2fb81-429">Las páginas de Razor son una nueva característica de ASP.NET Core MVC que facilita la codificación de escenarios centrados en páginas y hace que sea más productiva.</span><span class="sxs-lookup"><span data-stu-id="2fb81-429">Razor Pages is a new aspect of ASP.NET Core MVC that makes coding page-focused scenarios easier and more productive.</span></span>

<span data-ttu-id="2fb81-430">Si busca un tutorial que use el enfoque Model-View-Controller, consulte [Introducción a ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span><span class="sxs-lookup"><span data-stu-id="2fb81-430">If you're looking for a tutorial that uses the Model-View-Controller approach, see [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<span data-ttu-id="2fb81-431">En este documento se proporciona una introducción a las páginas de Razor.</span><span class="sxs-lookup"><span data-stu-id="2fb81-431">This document provides an introduction to Razor Pages.</span></span> <span data-ttu-id="2fb81-432">No es un tutorial paso a paso.</span><span class="sxs-lookup"><span data-stu-id="2fb81-432">It's not a step by step tutorial.</span></span> <span data-ttu-id="2fb81-433">Si encuentra que alguna sección es demasiado avanzada, consulte [Introducción a las páginas de Razor](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="2fb81-433">If you find some of the sections too advanced, see [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span></span> <span data-ttu-id="2fb81-434">Para obtener información general de ASP.NET Core, vea [Introducción a ASP.NET Core](xref:index).</span><span class="sxs-lookup"><span data-stu-id="2fb81-434">For an overview of ASP.NET Core, see the [Introduction to ASP.NET Core](xref:index).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="2fb81-435">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="2fb81-435">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2fb81-436">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2fb81-436">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="2fb81-437">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2fb81-437">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2fb81-438">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="2fb81-438">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-razor-pages-project"></a><span data-ttu-id="2fb81-439">Creación de un proyecto de Razor Pages</span><span class="sxs-lookup"><span data-stu-id="2fb81-439">Create a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2fb81-440">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2fb81-440">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2fb81-441">Vea [Introducción a Razor Pages](xref:tutorials/razor-pages/razor-pages-start) para obtener instrucciones detalladas sobre cómo crear un proyecto Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="2fb81-441">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2fb81-442">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="2fb81-442">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="2fb81-443">Ejecute `dotnet new webapp` desde la línea de comandos.</span><span class="sxs-lookup"><span data-stu-id="2fb81-443">Run `dotnet new webapp` from the command line.</span></span>

<span data-ttu-id="2fb81-444">Abra el archivo *.csproj* generado desde Visual Studio para Mac.</span><span class="sxs-lookup"><span data-stu-id="2fb81-444">Open the generated *.csproj* file from Visual Studio for Mac.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2fb81-445">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2fb81-445">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="2fb81-446">Ejecute `dotnet new webapp` desde la línea de comandos.</span><span class="sxs-lookup"><span data-stu-id="2fb81-446">Run `dotnet new webapp` from the command line.</span></span>

---

## <a name="razor-pages"></a><span data-ttu-id="2fb81-447">Páginas de Razor</span><span class="sxs-lookup"><span data-stu-id="2fb81-447">Razor Pages</span></span>

<span data-ttu-id="2fb81-448">Páginas de Razor está habilitada en *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="2fb81-448">Razor Pages is enabled in *Startup.cs*:</span></span>

[!code-cs[](index/sample/RazorPagesIntro/Startup.cs?name=snippet_Startup)]

<span data-ttu-id="2fb81-449">Considere la posibilidad de una página básica: <a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="2fb81-449">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index.cshtml)]

<span data-ttu-id="2fb81-450">El código anterior se parece mucho a un [archivo de vista de Razor](xref:tutorials/first-mvc-app/adding-view) que se utiliza en una aplicación ASP.NET Core con controladores y vistas.</span><span class="sxs-lookup"><span data-stu-id="2fb81-450">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="2fb81-451">La directiva `@page` lo hace diferente.</span><span class="sxs-lookup"><span data-stu-id="2fb81-451">What makes it different is the `@page` directive.</span></span> <span data-ttu-id="2fb81-452">`@page` transforma el archivo en una acción de MVC, lo que significa que administra las solicitudes directamente, sin tener que pasar a través de un controlador.</span><span class="sxs-lookup"><span data-stu-id="2fb81-452">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="2fb81-453">`@page` debe ser la primera directiva de Razor de una página.</span><span class="sxs-lookup"><span data-stu-id="2fb81-453">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="2fb81-454">`@page` afecta al comportamiento de otras construcciones de Razor.</span><span class="sxs-lookup"><span data-stu-id="2fb81-454">`@page` affects the behavior of other Razor constructs.</span></span>

<span data-ttu-id="2fb81-455">Una página similar, con una clase `PageModel`, se muestra en los dos archivos siguientes.</span><span class="sxs-lookup"><span data-stu-id="2fb81-455">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="2fb81-456">El archivo *Pages/Index2.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="2fb81-456">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="2fb81-457">Modelo de página *Pages/Index2.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="2fb81-457">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-cs[](index/sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="2fb81-458">Por convención, el archivo de clase `PageModel` tiene el mismo nombre que el archivo de páginas de Razor con *.cs* anexado.</span><span class="sxs-lookup"><span data-stu-id="2fb81-458">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="2fb81-459">Por ejemplo, la página de Razor anterior es *Pages/Index2.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="2fb81-459">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="2fb81-460">El archivo que contiene la clase `PageModel` se denomina *Pages/Index2.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="2fb81-460">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="2fb81-461">Las asociaciones de rutas de dirección URL a páginas se determinan según la ubicación de la página en el sistema de archivos.</span><span class="sxs-lookup"><span data-stu-id="2fb81-461">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="2fb81-462">En la tabla siguiente, se muestra una ruta de acceso de página de Razor y la dirección URL correspondiente:</span><span class="sxs-lookup"><span data-stu-id="2fb81-462">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="2fb81-463">Ruta de acceso y nombre de archivo</span><span class="sxs-lookup"><span data-stu-id="2fb81-463">File name and path</span></span>               | <span data-ttu-id="2fb81-464">URL correspondiente</span><span class="sxs-lookup"><span data-stu-id="2fb81-464">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="2fb81-465">*/Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="2fb81-465">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="2fb81-466">`/` o `/Index`</span><span class="sxs-lookup"><span data-stu-id="2fb81-466">`/` or `/Index`</span></span> |
| <span data-ttu-id="2fb81-467">*/Pages/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="2fb81-467">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="2fb81-468">*/Pages/Store/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="2fb81-468">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="2fb81-469">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="2fb81-469">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="2fb81-470">`/Store` o `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="2fb81-470">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="2fb81-471">Notas:</span><span class="sxs-lookup"><span data-stu-id="2fb81-471">Notes:</span></span>

* <span data-ttu-id="2fb81-472">El runtime busca archivos de páginas de Razor en la carpeta *Páginas* de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="2fb81-472">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="2fb81-473">`Index` es la página predeterminada cuando una URL no incluye una página.</span><span class="sxs-lookup"><span data-stu-id="2fb81-473">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="2fb81-474">Escritura de un formulario básico</span><span class="sxs-lookup"><span data-stu-id="2fb81-474">Write a basic form</span></span>

<span data-ttu-id="2fb81-475">Las páginas de Razor están diseñadas para facilitar la implementación de patrones comunes que se usan con exploradores web al compilar una aplicación.</span><span class="sxs-lookup"><span data-stu-id="2fb81-475">Razor Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="2fb81-476">Los [enlaces de modelos](xref:mvc/models/model-binding), los [asistentes de etiquetas](xref:mvc/views/tag-helpers/intro) y los asistentes de HTML *simplemente funcionan* con las propiedades definidas en una clase de página de Razor.</span><span class="sxs-lookup"><span data-stu-id="2fb81-476">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="2fb81-477">Considere la posibilidad de una página que implementa un formulario básico del estilo "Póngase en contacto con nosotros" para el modelo `Contact`:</span><span class="sxs-lookup"><span data-stu-id="2fb81-477">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="2fb81-478">Para los ejemplos de este documento, `DbContext` se inicializa en el archivo [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16).</span><span class="sxs-lookup"><span data-stu-id="2fb81-478">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) file.</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Startup.cs?highlight=15-16)]

<span data-ttu-id="2fb81-479">El modelo de datos:</span><span class="sxs-lookup"><span data-stu-id="2fb81-479">The data model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Data/Customer.cs)]

<span data-ttu-id="2fb81-480">El contexto de la base de datos:</span><span class="sxs-lookup"><span data-stu-id="2fb81-480">The db context:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Data/AppDbContext.cs)]

<span data-ttu-id="2fb81-481">El archivo de vista *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="2fb81-481">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml)]

<span data-ttu-id="2fb81-482">Modelo de página *Pages/Create.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="2fb81-482">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="2fb81-483">Por convención, la clase `PageModel` se denomina `<PageName>Model` y se encuentra en el mismo espacio de nombres que la página.</span><span class="sxs-lookup"><span data-stu-id="2fb81-483">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="2fb81-484">La clase `PageModel` permite la separación de la lógica de una página de su presentación.</span><span class="sxs-lookup"><span data-stu-id="2fb81-484">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="2fb81-485">Define los controladores de página para solicitudes que se envían a la página y los datos que usan para representar la página.</span><span class="sxs-lookup"><span data-stu-id="2fb81-485">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="2fb81-486">Esta separación permite lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="2fb81-486">This separation allows:</span></span>

* <span data-ttu-id="2fb81-487">Administración de dependencias de página mediante la [inserción de dependencias](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="2fb81-487">Managing of page dependencies through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="2fb81-488">[Pruebas](xref:test/razor-pages-tests) unitarias de las páginas.</span><span class="sxs-lookup"><span data-stu-id="2fb81-488">[Unit testing](xref:test/razor-pages-tests) the pages.</span></span>

<span data-ttu-id="2fb81-489">La página tiene un *método de controlador* `OnPostAsync`, que se ejecuta en solicitudes `POST` (cuando un usuario envía el formulario).</span><span class="sxs-lookup"><span data-stu-id="2fb81-489">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="2fb81-490">Puede agregar métodos de controlador para cualquier verbo HTTP.</span><span class="sxs-lookup"><span data-stu-id="2fb81-490">You can add handler methods for any HTTP verb.</span></span> <span data-ttu-id="2fb81-491">Los controladores más comunes son:</span><span class="sxs-lookup"><span data-stu-id="2fb81-491">The most common handlers are:</span></span>

* <span data-ttu-id="2fb81-492">`OnGet` para inicializar el estado necesario para la página.</span><span class="sxs-lookup"><span data-stu-id="2fb81-492">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="2fb81-493">Ejemplo [OnGet](#OnGet).</span><span class="sxs-lookup"><span data-stu-id="2fb81-493">[OnGet](#OnGet) sample.</span></span>
* <span data-ttu-id="2fb81-494">`OnPost` para controlar los envíos del formulario.</span><span class="sxs-lookup"><span data-stu-id="2fb81-494">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="2fb81-495">El sufijo de nombre `Async` es opcional, pero se usa a menudo por convención para funciones asincrónicas.</span><span class="sxs-lookup"><span data-stu-id="2fb81-495">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="2fb81-496">El código anterior es típico de las páginas de Razor.</span><span class="sxs-lookup"><span data-stu-id="2fb81-496">The preceding code is typical for Razor Pages.</span></span>

<span data-ttu-id="2fb81-497">Si está familiarizado con las aplicaciones de ASP.NET con controladores y vistas:</span><span class="sxs-lookup"><span data-stu-id="2fb81-497">If you're familiar with ASP.NET apps using controllers and views:</span></span>

* <span data-ttu-id="2fb81-498">El código `OnPostAsync` del ejemplo anterior es similar al típico código de controlador.</span><span class="sxs-lookup"><span data-stu-id="2fb81-498">The `OnPostAsync` code in the preceding example looks similar to typical controller code.</span></span>
* <span data-ttu-id="2fb81-499">La mayoría de los primitivos MVC como el [enlace de modelos](xref:mvc/models/model-binding), la [validación](xref:mvc/models/validation), la [validación](xref:mvc/models/validation) y los resultados de acciones se comparten.</span><span class="sxs-lookup"><span data-stu-id="2fb81-499">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), [Validation](xref:mvc/models/validation),  and action results are shared.</span></span>

<span data-ttu-id="2fb81-500">El método `OnPostAsync` anterior:</span><span class="sxs-lookup"><span data-stu-id="2fb81-500">The previous `OnPostAsync` method:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="2fb81-501">El flujo básico de `OnPostAsync`:</span><span class="sxs-lookup"><span data-stu-id="2fb81-501">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="2fb81-502">Compruebe los errores de validación.</span><span class="sxs-lookup"><span data-stu-id="2fb81-502">Check for validation errors.</span></span>

* <span data-ttu-id="2fb81-503">Si no hay ningún error, guarde los datos y redirija.</span><span class="sxs-lookup"><span data-stu-id="2fb81-503">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="2fb81-504">Si hay errores, muestre la página de nuevo con mensajes de validación.</span><span class="sxs-lookup"><span data-stu-id="2fb81-504">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="2fb81-505">La validación del lado cliente es idéntica a las aplicaciones de ASP.NET Core MVC tradicionales.</span><span class="sxs-lookup"><span data-stu-id="2fb81-505">Client-side validation is identical to traditional ASP.NET Core MVC applications.</span></span> <span data-ttu-id="2fb81-506">En muchos casos, los errores de validación se detectan en el cliente y nunca se envían al servidor.</span><span class="sxs-lookup"><span data-stu-id="2fb81-506">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="2fb81-507">Cuando los datos se escriben correctamente, el método del controlador `OnPostAsync` llama al método del asistente `RedirectToPage` para devolver una instancia de `RedirectToPageResult`.</span><span class="sxs-lookup"><span data-stu-id="2fb81-507">When the data is entered successfully, the `OnPostAsync` handler method calls the `RedirectToPage` helper method to return an instance of `RedirectToPageResult`.</span></span> <span data-ttu-id="2fb81-508">`RedirectToPage` es un resultado de acción nueva, similar a `RedirectToAction` o `RedirectToRoute`, pero personalizada para las páginas.</span><span class="sxs-lookup"><span data-stu-id="2fb81-508">`RedirectToPage` is a new action result, similar to `RedirectToAction` or `RedirectToRoute`, but customized for pages.</span></span> <span data-ttu-id="2fb81-509">En el ejemplo anterior, redirige a la página de índice raíz (`/Index`).</span><span class="sxs-lookup"><span data-stu-id="2fb81-509">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="2fb81-510">`RedirectToPage` se detalla en la sección [Generación de direcciones URL para las páginas](#url_gen).</span><span class="sxs-lookup"><span data-stu-id="2fb81-510">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

<span data-ttu-id="2fb81-511">Cuando el formulario enviado tiene errores de validación (que se pasan al servidor), el método del controlador `OnPostAsync` llama al método del asistente `Page`.</span><span class="sxs-lookup"><span data-stu-id="2fb81-511">When the submitted form has validation errors (that are passed to the server), the`OnPostAsync` handler method calls the `Page` helper method.</span></span> <span data-ttu-id="2fb81-512">`Page` devuelve una instancia de `PageResult`.</span><span class="sxs-lookup"><span data-stu-id="2fb81-512">`Page` returns an instance of `PageResult`.</span></span> <span data-ttu-id="2fb81-513">Devolver `Page` es similar a cómo las acciones en los controladores devuelven `View`.</span><span class="sxs-lookup"><span data-stu-id="2fb81-513">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="2fb81-514">`PageResult` es el tipo de valor devuelto predeterminado para un método de controlador.</span><span class="sxs-lookup"><span data-stu-id="2fb81-514">`PageResult` is the default return type for a handler method.</span></span> <span data-ttu-id="2fb81-515">Un método de controlador que devuelve `void` representa la página.</span><span class="sxs-lookup"><span data-stu-id="2fb81-515">A handler method that returns `void` renders the page.</span></span>

<span data-ttu-id="2fb81-516">La propiedad `Customer` usa el atributo `[BindProperty]` para participar en el enlace de modelos.</span><span class="sxs-lookup"><span data-stu-id="2fb81-516">The `Customer` property uses `[BindProperty]` attribute to opt in to model binding.</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_PageModel&highlight=10-11)]

<span data-ttu-id="2fb81-517">De forma predeterminada, Razor Pages enlaza propiedades solo con verbos que no sean `GET`.</span><span class="sxs-lookup"><span data-stu-id="2fb81-517">Razor Pages, by default, bind properties only with non-`GET` verbs.</span></span> <span data-ttu-id="2fb81-518">Enlazar a propiedades puede reducir la cantidad de código que se debe escribir.</span><span class="sxs-lookup"><span data-stu-id="2fb81-518">Binding to properties can reduce the amount of code you have to write.</span></span> <span data-ttu-id="2fb81-519">Enlazar reduce el código al usar la misma propiedad para representar los campos de formulario (`<input asp-for="Customer.Name">`) y aceptar la entrada.</span><span class="sxs-lookup"><span data-stu-id="2fb81-519">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="2fb81-520">La página principal (*Index.cshtml*):</span><span class="sxs-lookup"><span data-stu-id="2fb81-520">The home page (*Index.cshtml*):</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml)]

<span data-ttu-id="2fb81-521">La clase `PageModel` asociada (*Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="2fb81-521">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs)]

<span data-ttu-id="2fb81-522">El archivo *Index.cshtml* contiene el siguiente marcado para crear un vínculo de edición para cada contacto:</span><span class="sxs-lookup"><span data-stu-id="2fb81-522">The *Index.cshtml* file contains the following markup to create an edit link for each contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=21)]

<span data-ttu-id="2fb81-523">El `<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` [asistente de etiquetas delimitadoras](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) ha usado el atributo `asp-route-{value}` para generar un vínculo a la página de edición.</span><span class="sxs-lookup"><span data-stu-id="2fb81-523">The `<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="2fb81-524">El vínculo contiene datos de ruta con el identificador del contacto.</span><span class="sxs-lookup"><span data-stu-id="2fb81-524">The link contains route data with the contact ID.</span></span> <span data-ttu-id="2fb81-525">Por ejemplo: `https://localhost:5001/Edit/1`.</span><span class="sxs-lookup"><span data-stu-id="2fb81-525">For example, `https://localhost:5001/Edit/1`.</span></span> <span data-ttu-id="2fb81-526">Los [asistentes de etiquetas](xref:mvc/views/tag-helpers/intro) permiten que el código de servidor participe en la creación y la representación de elementos HTML en archivos de Razor.</span><span class="sxs-lookup"><span data-stu-id="2fb81-526">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="2fb81-527">Los asistentes de etiquetas se habilitan mediante `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span><span class="sxs-lookup"><span data-stu-id="2fb81-527">Tag Helpers are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="2fb81-528">El archivo *Pages/Edit.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="2fb81-528">The *Pages/Edit.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Edit.cshtml?highlight=1)]

<span data-ttu-id="2fb81-529">La primera línea contiene la directiva `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="2fb81-529">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="2fb81-530">La restricción de enrutamiento `"{id:int}"` indica a la página que acepte las solicitudes a la página que contienen datos de ruta `int`.</span><span class="sxs-lookup"><span data-stu-id="2fb81-530">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="2fb81-531">Si una solicitud a la página no contiene datos de ruta que se puedan convertir en `int`, el tiempo de ejecución devuelve un error HTTP 404 (no encontrado).</span><span class="sxs-lookup"><span data-stu-id="2fb81-531">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="2fb81-532">Para que el identificador sea opcional, anexe `?` a la restricción de ruta:</span><span class="sxs-lookup"><span data-stu-id="2fb81-532">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="2fb81-533">El archivo *Pages/Edit.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="2fb81-533">The *Pages/Edit.cshtml.cs* file:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Edit.cshtml.cs)]

<span data-ttu-id="2fb81-534">El archivo *index.cshtml* también contiene una marca para crear un botón de eliminar para cada contacto de cliente:</span><span class="sxs-lookup"><span data-stu-id="2fb81-534">The *Index.cshtml* file also contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=22-23)]

<span data-ttu-id="2fb81-535">Al representar dicho botón de eliminar en HTML, `formaction` incluye parámetros para:</span><span class="sxs-lookup"><span data-stu-id="2fb81-535">When the delete button is rendered in HTML, its `formaction` includes parameters for:</span></span>

* <span data-ttu-id="2fb81-536">Id. de contacto de cliente especificado mediante el atributo `asp-route-id`.</span><span class="sxs-lookup"><span data-stu-id="2fb81-536">The customer contact ID specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="2fb81-537">`handler` especificado mediante el atributo `asp-page-handler`.</span><span class="sxs-lookup"><span data-stu-id="2fb81-537">The `handler` specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="2fb81-538">Aquí tiene un ejemplo de un botón de eliminar representado con un id. de contacto de cliente de `1`:</span><span class="sxs-lookup"><span data-stu-id="2fb81-538">Here is an example of a rendered delete button with a customer contact ID of `1`:</span></span>

```html
<button type="submit" formaction="/?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="2fb81-539">Al seleccionar el botón, se envía una solicitud de formulario `POST` al servidor.</span><span class="sxs-lookup"><span data-stu-id="2fb81-539">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="2fb81-540">De forma predeterminada, el nombre del método de control se selecciona de acuerdo con el valor del parámetro `handler` y según el esquema `OnPost[handler]Async`.</span><span class="sxs-lookup"><span data-stu-id="2fb81-540">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="2fb81-541">Como en este ejemplo `handler` es `delete`, el método de control `OnPostDeleteAsync` se usa para procesar la solicitud `POST`.</span><span class="sxs-lookup"><span data-stu-id="2fb81-541">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="2fb81-542">Si `asp-page-handler` se establece en otro valor, como `remove`, se seleccionará un método de controlador llamado `OnPostRemoveAsync`.</span><span class="sxs-lookup"><span data-stu-id="2fb81-542">If the `asp-page-handler` is set to a different value, such as `remove`, a handler method with the name `OnPostRemoveAsync` is selected.</span></span> <span data-ttu-id="2fb81-543">En el código siguiente se muestra el controlador `OnPostDeleteAsync`:</span><span class="sxs-lookup"><span data-stu-id="2fb81-543">The following code shows the `OnPostDeleteAsync` handler:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs?range=26-37)]

<span data-ttu-id="2fb81-544">El método `OnPostDeleteAsync` realiza las acciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="2fb81-544">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="2fb81-545">Acepta el elemento `id` de la cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="2fb81-545">Accepts the `id` from the query string.</span></span> <span data-ttu-id="2fb81-546">Si la directiva de página *Index.cshtml* contuviera la restricción de enrutamiento `"{id:int?}"`, `id` provendría de los datos de la ruta.</span><span class="sxs-lookup"><span data-stu-id="2fb81-546">If the *Index.cshtml* page directive contained routing constraint `"{id:int?}"`, `id` would come from route data.</span></span> <span data-ttu-id="2fb81-547">Los datos de la ruta de `id` se especifican en el URI. Por ejemplo, `https://localhost:5001/Customers/2`.</span><span class="sxs-lookup"><span data-stu-id="2fb81-547">The route data for `id` is specified in the URI such as `https://localhost:5001/Customers/2`.</span></span>
* <span data-ttu-id="2fb81-548">Realiza una consulta a la base de datos del contacto de cliente con `FindAsync`.</span><span class="sxs-lookup"><span data-stu-id="2fb81-548">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="2fb81-549">Si encuentra dicho contacto de cliente, se quita de la lista correspondiente.</span><span class="sxs-lookup"><span data-stu-id="2fb81-549">If the customer contact is found, they're removed from the list of customer contacts.</span></span> <span data-ttu-id="2fb81-550">Luego, se actualiza la base de datos.</span><span class="sxs-lookup"><span data-stu-id="2fb81-550">The database is updated.</span></span>
* <span data-ttu-id="2fb81-551">Llama a `RedirectToPage` para redirigir la página Index raíz (`/Index`).</span><span class="sxs-lookup"><span data-stu-id="2fb81-551">Calls `RedirectToPage` to redirect to the root Index page (`/Index`).</span></span>

## <a name="mark-page-properties-as-required"></a><span data-ttu-id="2fb81-552">Marcado de las propiedades de página según sea necesario</span><span class="sxs-lookup"><span data-stu-id="2fb81-552">Mark page properties as required</span></span>

<span data-ttu-id="2fb81-553">Las propiedades de un valor `PageModel` se pueden marcar con el atributo [Required](/dotnet/api/system.componentmodel.dataannotations.requiredattribute):</span><span class="sxs-lookup"><span data-stu-id="2fb81-553">Properties on a `PageModel` can be marked with the [Required](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) attribute:</span></span>

[!code-cs[](index/sample/Create.cshtml.cs?highlight=3,15-16)]

<span data-ttu-id="2fb81-554">Para obtener más información, vea [Validación de modelos](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="2fb81-554">For more information, see [Model validation](xref:mvc/models/validation).</span></span>

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a><span data-ttu-id="2fb81-555">Control de solicitudes HEAD con un controlador OnGet de reserva</span><span class="sxs-lookup"><span data-stu-id="2fb81-555">Handle HEAD requests with an OnGet handler fallback</span></span>

<span data-ttu-id="2fb81-556">Las solicitudes `HEAD` permiten recuperar los encabezados de un recurso específico.</span><span class="sxs-lookup"><span data-stu-id="2fb81-556">`HEAD` requests allow you to retrieve the headers for a specific resource.</span></span> <span data-ttu-id="2fb81-557">A diferencia de las solicitudes `GET`, las solicitudes `HEAD` no devuelven un cuerpo de respuesta.</span><span class="sxs-lookup"><span data-stu-id="2fb81-557">Unlike `GET` requests, `HEAD` requests don't return a response body.</span></span>

<span data-ttu-id="2fb81-558">Normalmente, se crea un controlador `OnHead` al que se llama para las solicitudes `HEAD`:</span><span class="sxs-lookup"><span data-stu-id="2fb81-558">Ordinarily, an `OnHead` handler is created and called for `HEAD` requests:</span></span> 

```csharp
public void OnHead()
{
    HttpContext.Response.Headers.Add("HandledBy", "Handled by OnHead!");
}
```

<span data-ttu-id="2fb81-559">En ASP.NET Core 2.1 o versiones posteriores, Razor Pages recurre a una llamada al controlador `OnGet` si no se define ningún controlador `OnHead`.</span><span class="sxs-lookup"><span data-stu-id="2fb81-559">In ASP.NET Core 2.1 or later, Razor Pages falls back to calling the `OnGet` handler if no `OnHead` handler is defined.</span></span> <span data-ttu-id="2fb81-560">Este comportamiento se habilita mediante la llamada a [SetCompatibilityVersion](xref:mvc/compatibility-version) en `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="2fb81-560">This behavior is enabled by the call to [SetCompatibilityVersion](xref:mvc/compatibility-version) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

<span data-ttu-id="2fb81-561">Las plantillas predeterminadas generan la llamada `SetCompatibilityVersion` en ASP.NET Core 2.1 y 2.2.</span><span class="sxs-lookup"><span data-stu-id="2fb81-561">The default templates generate the `SetCompatibilityVersion` call in ASP.NET Core 2.1 and 2.2.</span></span> <span data-ttu-id="2fb81-562">`SetCompatibilityVersion` define con eficacia la opción de las páginas de Razor `AllowMappingHeadRequestsToGetHandler` como `true`.</span><span class="sxs-lookup"><span data-stu-id="2fb81-562">`SetCompatibilityVersion` effectively sets the Razor Pages option `AllowMappingHeadRequestsToGetHandler` to `true`.</span></span>

<span data-ttu-id="2fb81-563">En lugar de aceptar todos los comportamientos con `SetCompatibilityVersion`, puede aceptar explícitamente comportamientos *específicos*.</span><span class="sxs-lookup"><span data-stu-id="2fb81-563">Rather than opting in to all behaviors with `SetCompatibilityVersion`, you can explicitly opt in to *specific* behaviors.</span></span> <span data-ttu-id="2fb81-564">El código siguiente permite que las solicitudes `HEAD` se asignen al controlador `OnGet`:</span><span class="sxs-lookup"><span data-stu-id="2fb81-564">The following code opts in to allowing `HEAD` requests to be mapped to the `OnGet` handler:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.AllowMappingHeadRequestsToGetHandler = true;
    });
```

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-razor-pages"></a><span data-ttu-id="2fb81-565">XSRF/CSRF y páginas de Razor</span><span class="sxs-lookup"><span data-stu-id="2fb81-565">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="2fb81-566">No tiene que escribir ningún código para la [validación antifalsificación](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="2fb81-566">You don't have to write any code for [antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="2fb81-567">La validación y generación de tokens antifalsificación se incluyen automáticamente en las páginas de Razor.</span><span class="sxs-lookup"><span data-stu-id="2fb81-567">Antiforgery token generation and validation are automatically included in Razor Pages.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-razor-pages"></a><span data-ttu-id="2fb81-568">Usar diseños, parciales, plantillas y asistentes de etiquetas con las páginas de Razor</span><span class="sxs-lookup"><span data-stu-id="2fb81-568">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="2fb81-569">Las páginas funcionan con todas las características del motor de vista de Razor.</span><span class="sxs-lookup"><span data-stu-id="2fb81-569">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="2fb81-570">Los diseños, parciales, plantillas, asistentes de etiquetas, *_ViewStart.cshtml*, *_ViewImports.cshtml* funcionan de la misma manera que lo hacen con las vistas de Razor convencionales.</span><span class="sxs-lookup"><span data-stu-id="2fb81-570">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="2fb81-571">Para simplificar esta página, aprovecharemos algunas de esas características.</span><span class="sxs-lookup"><span data-stu-id="2fb81-571">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

<span data-ttu-id="2fb81-572">Agregue una [página de diseño](xref:mvc/views/layout) a *Pages/Shared/_Layout.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="2fb81-572">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_LayoutSimple.cshtml)]

<span data-ttu-id="2fb81-573">El [diseño](xref:mvc/views/layout):</span><span class="sxs-lookup"><span data-stu-id="2fb81-573">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="2fb81-574">Controla el diseño de cada página (a no ser que la página no tenga diseño).</span><span class="sxs-lookup"><span data-stu-id="2fb81-574">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="2fb81-575">Importa las estructuras HTML como JavaScript y hojas de estilos.</span><span class="sxs-lookup"><span data-stu-id="2fb81-575">Imports HTML structures such as JavaScript and stylesheets.</span></span>

<span data-ttu-id="2fb81-576">Vea [Layout page](xref:mvc/views/layout) (Página de diseño) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="2fb81-576">See [layout page](xref:mvc/views/layout) for more information.</span></span>

<span data-ttu-id="2fb81-577">La propiedad [Layout](xref:mvc/views/layout#specifying-a-layout) se establece en *Pages/_ViewStart.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="2fb81-577">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="2fb81-578">El diseño está en la carpeta *Pages/Shared*.</span><span class="sxs-lookup"><span data-stu-id="2fb81-578">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="2fb81-579">Las páginas buscan otras vistas (diseños, plantillas, parciales) de forma jerárquica, a partir de la misma carpeta que la página actual.</span><span class="sxs-lookup"><span data-stu-id="2fb81-579">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="2fb81-580">Un diseño en la carpeta *Pages/Shared* se puede usar desde cualquier página de Razor en la carpeta *Pages*.</span><span class="sxs-lookup"><span data-stu-id="2fb81-580">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="2fb81-581">El archivo de diseño debería ir en la carpeta *Pages/Shared*.</span><span class="sxs-lookup"><span data-stu-id="2fb81-581">The layout file should go in the *Pages/Shared* folder.</span></span>

<span data-ttu-id="2fb81-582">Le recomendamos que **no** coloque el archivo de diseño en la carpeta *Views/Shared*.</span><span class="sxs-lookup"><span data-stu-id="2fb81-582">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="2fb81-583">*Views/Shared* es un patrón de vistas de MVC.</span><span class="sxs-lookup"><span data-stu-id="2fb81-583">*Views/Shared* is an MVC views pattern.</span></span> <span data-ttu-id="2fb81-584">Las páginas de Razor están diseñadas para basarse en la jerarquía de carpetas, no en las convenciones de ruta de acceso.</span><span class="sxs-lookup"><span data-stu-id="2fb81-584">Razor Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="2fb81-585">La búsqueda de vistas de una página de Razor incluye la carpeta *Pages*.</span><span class="sxs-lookup"><span data-stu-id="2fb81-585">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="2fb81-586">Los diseños, plantillas y parciales que usa con los controladores de MVC y las vistas de Razor convencionales *simplemente funcionan*.</span><span class="sxs-lookup"><span data-stu-id="2fb81-586">The layouts, templates, and partials you're using with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="2fb81-587">Agregue un archivo *Pages/_ViewImports.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="2fb81-587">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="2fb81-588">`@namespace` se explica más adelante en el tutorial.</span><span class="sxs-lookup"><span data-stu-id="2fb81-588">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="2fb81-589">La directiva `@addTagHelper` pone los [asistentes de etiquetas integradas](xref:mvc/views/tag-helpers/builtin-th/Index) en todas las páginas de la carpeta *Pages*.</span><span class="sxs-lookup"><span data-stu-id="2fb81-589">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="2fb81-590">Cuando la directiva `@namespace` se usa explícitamente en una página:</span><span class="sxs-lookup"><span data-stu-id="2fb81-590">When the `@namespace` directive is used explicitly on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="2fb81-591">La directiva establece el espacio de nombres de la página.</span><span class="sxs-lookup"><span data-stu-id="2fb81-591">The directive sets the namespace for the page.</span></span> <span data-ttu-id="2fb81-592">La directiva `@model` no necesita incluir el espacio de nombres.</span><span class="sxs-lookup"><span data-stu-id="2fb81-592">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="2fb81-593">Cuando la directiva `@namespace` se encuentra en *_ViewImports.cshtml*, el espacio de nombres especificado proporciona el prefijo del espacio de nombres generado en la página que importa la directiva `@namespace`.</span><span class="sxs-lookup"><span data-stu-id="2fb81-593">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="2fb81-594">El resto del espacio de nombres generado (la parte del sufijo) es la ruta de acceso relativa separada por puntos entre la carpeta que contiene *_ViewImports.cshtml* y la carpeta que contiene la página.</span><span class="sxs-lookup"><span data-stu-id="2fb81-594">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="2fb81-595">Por ejemplo, la clase `PageModel`*Pages/Customers/Edit.cshtml.cs* establece explícitamente el espacio de nombres:</span><span class="sxs-lookup"><span data-stu-id="2fb81-595">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="2fb81-596">El archivo *Pages/_ViewImports.cshtml* establece el espacio de nombres siguiente:</span><span class="sxs-lookup"><span data-stu-id="2fb81-596">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="2fb81-597">El espacio de nombres generado para la página de Razor *Pages/Customers/Edit.cshtml* es el mismo que la clase `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="2fb81-597">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="2fb81-598">`@namespace` *también funciona con vistas de Razor convencionales.*</span><span class="sxs-lookup"><span data-stu-id="2fb81-598">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="2fb81-599">El archivo de vista *Pages/Create.cshtml* original:</span><span class="sxs-lookup"><span data-stu-id="2fb81-599">The original *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml?highlight=2)]

<span data-ttu-id="2fb81-600">El archivo de vista *Pages/Create.cshtml* actualizado:</span><span class="sxs-lookup"><span data-stu-id="2fb81-600">The updated *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/Create.cshtml?highlight=2)]

<span data-ttu-id="2fb81-601">El [proyecto de inicio de las páginas de Razor](#rpvs17) contiene *Pages/_ValidationScriptsPartial.cshtml*, que enlaza la validación del lado cliente.</span><span class="sxs-lookup"><span data-stu-id="2fb81-601">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<span data-ttu-id="2fb81-602">Para más información sobre las vistas parciales, vea <xref:mvc/views/partial>.</span><span class="sxs-lookup"><span data-stu-id="2fb81-602">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="2fb81-603">Generación de direcciones URL para las páginas</span><span class="sxs-lookup"><span data-stu-id="2fb81-603">URL generation for Pages</span></span>

<span data-ttu-id="2fb81-604">La página `Create`, mostrada anteriormente, usa `RedirectToPage`:</span><span class="sxs-lookup"><span data-stu-id="2fb81-604">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=10)]

<span data-ttu-id="2fb81-605">La aplicación tiene la siguiente estructura de archivos o carpetas:</span><span class="sxs-lookup"><span data-stu-id="2fb81-605">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="2fb81-606">*/Pages*</span><span class="sxs-lookup"><span data-stu-id="2fb81-606">*/Pages*</span></span>

  * <span data-ttu-id="2fb81-607">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="2fb81-607">*Index.cshtml*</span></span>
  * <span data-ttu-id="2fb81-608">*/Customers*</span><span class="sxs-lookup"><span data-stu-id="2fb81-608">*/Customers*</span></span>

    * <span data-ttu-id="2fb81-609">*Create.cshtml*</span><span class="sxs-lookup"><span data-stu-id="2fb81-609">*Create.cshtml*</span></span>
    * <span data-ttu-id="2fb81-610">*Edit.cshtml*</span><span class="sxs-lookup"><span data-stu-id="2fb81-610">*Edit.cshtml*</span></span>
    * <span data-ttu-id="2fb81-611">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="2fb81-611">*Index.cshtml*</span></span>

<span data-ttu-id="2fb81-612">Las páginas *Pages/Customers/Create.cshtml* y *Pages/Customers/Edit.cshtml* redirigen a *Pages/Index.cshtml* si se realiza correctamente.</span><span class="sxs-lookup"><span data-stu-id="2fb81-612">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Index.cshtml* after success.</span></span> <span data-ttu-id="2fb81-613">La cadena `/Index` forma parte del URI para tener acceso a la página anterior.</span><span class="sxs-lookup"><span data-stu-id="2fb81-613">The string `/Index` is part of the URI to access the preceding page.</span></span> <span data-ttu-id="2fb81-614">La cadena `/Index` puede usarse para generar los URI para la página *Pages/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="2fb81-614">The string `/Index` can be used to generate URIs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="2fb81-615">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="2fb81-615">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">My Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="2fb81-616">El nombre de página es la ruta de acceso a la página de la carpeta raíz */Pages*, incluido un `/` inicial, por ejemplo `/Index`.</span><span class="sxs-lookup"><span data-stu-id="2fb81-616">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="2fb81-617">Los ejemplos anteriores de generación de URL ofrecen opciones mejoradas y capacidades funcionales en comparación con la escritura a mano de estas.</span><span class="sxs-lookup"><span data-stu-id="2fb81-617">The preceding URL generation samples offer enhanced options and functional capabilities over hardcoding a URL.</span></span> <span data-ttu-id="2fb81-618">La generación de direcciones URL usa el [enrutamiento](xref:mvc/controllers/routing) y puede generar y codificar parámetros según cómo se defina la ruta en la ruta de acceso de destino.</span><span class="sxs-lookup"><span data-stu-id="2fb81-618">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="2fb81-619">La generación de direcciones URL para las páginas admite nombres relativos.</span><span class="sxs-lookup"><span data-stu-id="2fb81-619">URL generation for pages supports relative names.</span></span> <span data-ttu-id="2fb81-620">En la siguiente tabla, se muestra qué página de índice está seleccionada con diferentes parámetros `RedirectToPage` de *Pages/Customers/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="2fb81-620">The following table shows which Index page is selected with different `RedirectToPage` parameters from *Pages/Customers/Create.cshtml*:</span></span>

| <span data-ttu-id="2fb81-621">RedirectToPage(x)</span><span class="sxs-lookup"><span data-stu-id="2fb81-621">RedirectToPage(x)</span></span>| <span data-ttu-id="2fb81-622">Página</span><span class="sxs-lookup"><span data-stu-id="2fb81-622">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="2fb81-623">RedirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="2fb81-623">RedirectToPage("/Index")</span></span> | <span data-ttu-id="2fb81-624">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="2fb81-624">*Pages/Index*</span></span> |
| <span data-ttu-id="2fb81-625">RedirectToPage("./Index");</span><span class="sxs-lookup"><span data-stu-id="2fb81-625">RedirectToPage("./Index");</span></span> | <span data-ttu-id="2fb81-626">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="2fb81-626">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="2fb81-627">RedirectToPage("../Index")</span><span class="sxs-lookup"><span data-stu-id="2fb81-627">RedirectToPage("../Index")</span></span> | <span data-ttu-id="2fb81-628">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="2fb81-628">*Pages/Index*</span></span> |
| <span data-ttu-id="2fb81-629">RedirectToPage("Index")</span><span class="sxs-lookup"><span data-stu-id="2fb81-629">RedirectToPage("Index")</span></span>  | <span data-ttu-id="2fb81-630">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="2fb81-630">*Pages/Customers/Index*</span></span> |

<span data-ttu-id="2fb81-631">`RedirectToPage("Index")`, `RedirectToPage("./Index")` y `RedirectToPage("../Index")` son *nombres relativos*.</span><span class="sxs-lookup"><span data-stu-id="2fb81-631">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")`  are *relative names*.</span></span> <span data-ttu-id="2fb81-632">El parámetro `RedirectToPage` se *combina* con la ruta de acceso de la página actual para calcular el nombre de la página de destino.</span><span class="sxs-lookup"><span data-stu-id="2fb81-632">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>  <!-- Review: Original had The provided string is combined with the page name of the current page to compute the name of the destination page.  page name, not page path -->

<span data-ttu-id="2fb81-633">Vincular el nombre relativo es útil al crear sitios con una estructura compleja.</span><span class="sxs-lookup"><span data-stu-id="2fb81-633">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="2fb81-634">Si usa nombres relativos para vincular entre páginas en una carpeta, puede cambiar el nombre de esa carpeta.</span><span class="sxs-lookup"><span data-stu-id="2fb81-634">If you use relative names to link between pages in a folder, you can rename that folder.</span></span> <span data-ttu-id="2fb81-635">Todos los vínculos seguirán funcionando (porque no incluyen el nombre de carpeta).</span><span class="sxs-lookup"><span data-stu-id="2fb81-635">All the links still work (because they didn't include the folder name).</span></span>

<span data-ttu-id="2fb81-636">Para redirigir a una página en otra [área](xref:mvc/controllers/areas), especifique el área:</span><span class="sxs-lookup"><span data-stu-id="2fb81-636">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="2fb81-637">Para obtener más información, vea <xref:mvc/controllers/areas>.</span><span class="sxs-lookup"><span data-stu-id="2fb81-637">For more information, see <xref:mvc/controllers/areas>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="2fb81-638">Atributo ViewData</span><span class="sxs-lookup"><span data-stu-id="2fb81-638">ViewData attribute</span></span>

<span data-ttu-id="2fb81-639">Se pueden pasar datos a una página con [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span><span class="sxs-lookup"><span data-stu-id="2fb81-639">Data can be passed to a page with [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span></span> <span data-ttu-id="2fb81-640">Los valores de las propiedades de los controladores o los modelos de Razor Pages con el atributo `[ViewData]` se almacenan y cargan desde [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).</span><span class="sxs-lookup"><span data-stu-id="2fb81-640">Properties on controllers or Razor Page models with the `[ViewData]` attribute have their values stored and loaded from the [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).</span></span>

<span data-ttu-id="2fb81-641">En el ejemplo siguiente, el valor `AboutModel` contiene una propiedad `Title` marcada con `[ViewData]`.</span><span class="sxs-lookup"><span data-stu-id="2fb81-641">In the following example, the `AboutModel` contains a `Title` property marked with `[ViewData]`.</span></span> <span data-ttu-id="2fb81-642">La propiedad `Title` se establece en el título de la página Acerca de:</span><span class="sxs-lookup"><span data-stu-id="2fb81-642">The `Title` property is set to the title of the About page:</span></span>

```csharp
public class AboutModel : PageModel
{
    [ViewData]
    public string Title { get; } = "About";

    public void OnGet()
    {
    }
}
```

<span data-ttu-id="2fb81-643">En la página Acerca de, acceda a la propiedad `Title` como propiedad de modelo:</span><span class="sxs-lookup"><span data-stu-id="2fb81-643">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="2fb81-644">En el diseño, el título se lee desde el diccionario ViewData:</span><span class="sxs-lookup"><span data-stu-id="2fb81-644">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a><span data-ttu-id="2fb81-645">TempData</span><span class="sxs-lookup"><span data-stu-id="2fb81-645">TempData</span></span>

<span data-ttu-id="2fb81-646">ASP.NET Core expone la propiedad [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) en un [controlador](/dotnet/api/microsoft.aspnetcore.mvc.controller).</span><span class="sxs-lookup"><span data-stu-id="2fb81-646">ASP.NET Core exposes the [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) property on a [controller](/dotnet/api/microsoft.aspnetcore.mvc.controller).</span></span> <span data-ttu-id="2fb81-647">Esta propiedad almacena datos hasta que se leen.</span><span class="sxs-lookup"><span data-stu-id="2fb81-647">This property stores data until it's read.</span></span> <span data-ttu-id="2fb81-648">Los métodos `Keep` y `Peek` se pueden usar para examinar los datos sin que se eliminen.</span><span class="sxs-lookup"><span data-stu-id="2fb81-648">The `Keep` and `Peek` methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="2fb81-649">`TempData` es útil para el redireccionamiento cuando se necesitan los datos de más de una única solicitud.</span><span class="sxs-lookup"><span data-stu-id="2fb81-649">`TempData` is  useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="2fb81-650">El siguiente código establece el valor de `Message` mediante `TempData`:</span><span class="sxs-lookup"><span data-stu-id="2fb81-650">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="2fb81-651">El siguiente marcado en el archivo *Pages/Customers/Index.cshtml* muestra el valor de `Message` mediante `TempData`.</span><span class="sxs-lookup"><span data-stu-id="2fb81-651">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="2fb81-652">El modelo de página *Pages/Customers/Index.cshtml.cs* aplica el atributo `[TempData]` a la propiedad `Message`.</span><span class="sxs-lookup"><span data-stu-id="2fb81-652">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```csharp
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="2fb81-653">Para obtener más información, vea [TempData](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="2fb81-653">For more information, see [TempData](xref:fundamentals/app-state#tempdata) .</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="2fb81-654">Varios controladores por página</span><span class="sxs-lookup"><span data-stu-id="2fb81-654">Multiple handlers per page</span></span>

<span data-ttu-id="2fb81-655">En la página siguiente se usa el asistente de etiquetas `asp-page-handler` para generar marcado para dos controladores:</span><span class="sxs-lookup"><span data-stu-id="2fb81-655">The following page generates markup for two handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<!-- Review: the FormActionTagHelper applies to all <form /> elements on a Razor page, even when there's no `asp-` attribute   -->

<span data-ttu-id="2fb81-656">El formulario del ejemplo anterior tiene dos botones de envío, y cada uno de ellos usa `FormActionTagHelper` para enviar a una dirección URL diferente.</span><span class="sxs-lookup"><span data-stu-id="2fb81-656">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="2fb81-657">El atributo `asp-page-handler` es un complemento de `asp-page`.</span><span class="sxs-lookup"><span data-stu-id="2fb81-657">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="2fb81-658">`asp-page-handler` genera direcciones URL que envían a cada uno de los métodos de controlador definidos por una página.</span><span class="sxs-lookup"><span data-stu-id="2fb81-658">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="2fb81-659">`asp-page` no se especifica porque el ejemplo se vincula a la página actual.</span><span class="sxs-lookup"><span data-stu-id="2fb81-659">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="2fb81-660">Modelo de página:</span><span class="sxs-lookup"><span data-stu-id="2fb81-660">The page model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="2fb81-661">El código anterior usa *métodos de controlador con nombre*.</span><span class="sxs-lookup"><span data-stu-id="2fb81-661">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="2fb81-662">Los métodos de controlador con nombre se crean tomando el texto en el nombre después de `On<HTTP Verb>` y antes de `Async` (si existe).</span><span class="sxs-lookup"><span data-stu-id="2fb81-662">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="2fb81-663">En el ejemplo anterior, los métodos de página son OnPost**JoinList**Async y OnPost**JoinListUC**Async.</span><span class="sxs-lookup"><span data-stu-id="2fb81-663">In the preceding example, the page methods are OnPost**JoinList**Async and OnPost**JoinListUC**Async.</span></span> <span data-ttu-id="2fb81-664">Quitando *OnPost* y *Async*, los nombres de controlador son `JoinList` y `JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="2fb81-664">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="2fb81-665">Al usar el código anterior, la ruta de dirección URL que envía a `OnPostJoinListAsync` es `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span><span class="sxs-lookup"><span data-stu-id="2fb81-665">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="2fb81-666">La ruta de dirección URL que envía a `OnPostJoinListUCAsync` es `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="2fb81-666">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="2fb81-667">Rutas personalizadas</span><span class="sxs-lookup"><span data-stu-id="2fb81-667">Custom routes</span></span>

<span data-ttu-id="2fb81-668">Use la directiva `@page` para:</span><span class="sxs-lookup"><span data-stu-id="2fb81-668">Use the `@page` directive to:</span></span>

* <span data-ttu-id="2fb81-669">Especificar una ruta personalizada a una página.</span><span class="sxs-lookup"><span data-stu-id="2fb81-669">Specify a custom route to a page.</span></span> <span data-ttu-id="2fb81-670">Por ejemplo, la ruta a la página Acerca de se puede establecer en `/Some/Other/Path` con `@page "/Some/Other/Path"`.</span><span class="sxs-lookup"><span data-stu-id="2fb81-670">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="2fb81-671">Anexar segmentos a la ruta predeterminada de una página.</span><span class="sxs-lookup"><span data-stu-id="2fb81-671">Append segments to a page's default route.</span></span> <span data-ttu-id="2fb81-672">Por ejemplo, se puede agregar un segmento "item" a la ruta predeterminada de una página con `@page "item"`.</span><span class="sxs-lookup"><span data-stu-id="2fb81-672">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="2fb81-673">Anexar parámetros a la ruta predeterminada de una página.</span><span class="sxs-lookup"><span data-stu-id="2fb81-673">Append parameters to a page's default route.</span></span> <span data-ttu-id="2fb81-674">Por ejemplo, un parámetro de identificador, `id`, puede ser necesario para una página con `@page "{id}"`.</span><span class="sxs-lookup"><span data-stu-id="2fb81-674">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="2fb81-675">Se admite una ruta de acceso relativa raíz designada por una tilde (`~`) al principio de la ruta de acceso.</span><span class="sxs-lookup"><span data-stu-id="2fb81-675">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="2fb81-676">Por ejemplo, `@page "~/Some/Other/Path"` es lo mismo que `@page "/Some/Other/Path"`.</span><span class="sxs-lookup"><span data-stu-id="2fb81-676">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="2fb81-677">Si no le gusta la cadena de consulta `?handler=JoinList` en la dirección URL, puede cambiar la ruta para poner el nombre del controlador en la parte de la ruta de la dirección URL.</span><span class="sxs-lookup"><span data-stu-id="2fb81-677">If you don't like the query string `?handler=JoinList` in the URL, change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="2fb81-678">Para personalizar la ruta, se puede agregar una plantilla de ruta entre comillas dobles después de la directiva `@page`.</span><span class="sxs-lookup"><span data-stu-id="2fb81-678">The route can be customized by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="2fb81-679">Al usar el código anterior, la ruta de dirección URL que envía a `OnPostJoinListAsync` es `https://localhost:5001/Customers/CreateFATH/JoinList`.</span><span class="sxs-lookup"><span data-stu-id="2fb81-679">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="2fb81-680">La ruta de dirección URL que envía a `OnPostJoinListUCAsync` es `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="2fb81-680">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="2fb81-681">El signo `?` que sigue a `handler` significa que el parámetro de ruta es opcional.</span><span class="sxs-lookup"><span data-stu-id="2fb81-681">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="configuration-and-settings"></a><span data-ttu-id="2fb81-682">Valores de configuración</span><span class="sxs-lookup"><span data-stu-id="2fb81-682">Configuration and settings</span></span>

<span data-ttu-id="2fb81-683">Para configurar opciones avanzadas, use el método de extensión `AddRazorPagesOptions` en el generador de MVC:</span><span class="sxs-lookup"><span data-stu-id="2fb81-683">To configure advanced options, use the extension method `AddRazorPagesOptions` on the MVC builder:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/StartupAdvanced.cs?name=snippet_1)]

<span data-ttu-id="2fb81-684">Actualmente, puede usar `RazorPagesOptions` para establecer el directorio raíz de páginas, o agregar convenciones de modelo de aplicación a las páginas.</span><span class="sxs-lookup"><span data-stu-id="2fb81-684">Currently you can use the `RazorPagesOptions` to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="2fb81-685">En el futuro habilitaremos más extensibilidad de este modo.</span><span class="sxs-lookup"><span data-stu-id="2fb81-685">We'll enable more extensibility this way in the future.</span></span>

<span data-ttu-id="2fb81-686">Para precompilar vistas, consulte la sección sobre la [compilación de vistas de Razor](xref:mvc/views/view-compilation).</span><span class="sxs-lookup"><span data-stu-id="2fb81-686">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation) .</span></span>

<span data-ttu-id="2fb81-687">[Descargue o vea el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span><span class="sxs-lookup"><span data-stu-id="2fb81-687">[Download or view sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span></span>

<span data-ttu-id="2fb81-688">Consulte [Introducción a Razor Pages](xref:tutorials/razor-pages/razor-pages-start), que se basa en esta introducción.</span><span class="sxs-lookup"><span data-stu-id="2fb81-688">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction.</span></span>

### <a name="specify-that-razor-pages-are-at-the-content-root"></a><span data-ttu-id="2fb81-689">Especificación de Razor Pages en la raíz del contenido</span><span class="sxs-lookup"><span data-stu-id="2fb81-689">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="2fb81-690">De forma predeterminada, Razor Pages se encuentra en la raíz del directorio */Pages*.</span><span class="sxs-lookup"><span data-stu-id="2fb81-690">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="2fb81-691">Agregue [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) a [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) para especificar que Razor Pages se encuentra en la [raíz del contenido](xref:fundamentals/index#content-root) ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="2fb81-691">Add [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at the [content root](xref:fundamentals/index#content-root) ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) of the app:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesAtContentRoot();
```

### <a name="specify-that-razor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="2fb81-692">Especificación de Razor Pages en un directorio raíz personalizado</span><span class="sxs-lookup"><span data-stu-id="2fb81-692">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="2fb81-693">Agregue [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) a [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) para especificar que Razor Pages se encuentra en un directorio raíz personalizado de la aplicación (proporcione la ruta de acceso relativa):</span><span class="sxs-lookup"><span data-stu-id="2fb81-693">Add [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesRoot("/path/to/razor/pages");
```

## <a name="additional-resources"></a><span data-ttu-id="2fb81-694">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="2fb81-694">Additional resources</span></span>

* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>

::: moniker-end
