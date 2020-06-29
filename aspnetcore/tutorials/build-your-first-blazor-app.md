---
title: Compilación de la primera aplicación Blazor
author: guardrex
description: Cree una aplicación Blazor paso a paso.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-blazor-app
ms.openlocfilehash: 892663a533a207df84b0fce9af259a7dc212bc9b
ms.sourcegitcommit: 5e462c3328c70f95969d02adce9c71592049f54c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/24/2020
ms.locfileid: "85292781"
---
# <a name="build-your-first-blazor-app"></a><span data-ttu-id="25cee-103">Compilación de la primera aplicación Blazor</span><span class="sxs-lookup"><span data-stu-id="25cee-103">Build your first Blazor app</span></span>

<span data-ttu-id="25cee-104">Por [Daniel Roth](https://github.com/danroth27) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="25cee-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="25cee-105">En este tutorial se muestra cómo crear y modificar una aplicación Blazor.</span><span class="sxs-lookup"><span data-stu-id="25cee-105">This tutorial shows you how to build and modify a Blazor app.</span></span> <span data-ttu-id="25cee-106">Aprenderá a:</span><span class="sxs-lookup"><span data-stu-id="25cee-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="25cee-107">Crear un proyecto de aplicación Blazor de lista de tareas</span><span class="sxs-lookup"><span data-stu-id="25cee-107">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="25cee-108">Modificar los componentes de Razor</span><span class="sxs-lookup"><span data-stu-id="25cee-108">Modify Razor components</span></span>
> * <span data-ttu-id="25cee-109">Usar el control de eventos y el enlace de datos en los componentes</span><span class="sxs-lookup"><span data-stu-id="25cee-109">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="25cee-110">Usar la inserción de dependencias (DI) y el enrutamiento en una aplicación Blazor</span><span class="sxs-lookup"><span data-stu-id="25cee-110">Use dependency injection (DI) and routing in a Blazor app</span></span>

<span data-ttu-id="25cee-111">Al final de este tutorial, tendrá una aplicación de lista de tareas funcional.</span><span class="sxs-lookup"><span data-stu-id="25cee-111">At the end of this tutorial, you'll have a working todo list app.</span></span>

## <a name="build-components"></a><span data-ttu-id="25cee-112">Creación de componentes</span><span class="sxs-lookup"><span data-stu-id="25cee-112">Build components</span></span>

1. <span data-ttu-id="25cee-113">Siga las instrucciones del artículo <xref:blazor/get-started> para crear un proyecto de Blazor en este tutorial.</span><span class="sxs-lookup"><span data-stu-id="25cee-113">Follow the guidance in the <xref:blazor/get-started> article to create a Blazor project for this tutorial.</span></span> <span data-ttu-id="25cee-114">Dé un nombre al proyecto `ToDoList`.</span><span class="sxs-lookup"><span data-stu-id="25cee-114">Name the project `ToDoList`.</span></span>

1. <span data-ttu-id="25cee-115">Vaya a cada una de las tres páginas de la aplicación en la carpeta `Pages`: `Home`, `Counter` y `Fetch data`.</span><span class="sxs-lookup"><span data-stu-id="25cee-115">Browse to each of the app's three pages in the `Pages` folder: `Home`, `Counter`, and `Fetch data`.</span></span> <span data-ttu-id="25cee-116">Estas páginas se implementan mediante los archivos de componentes de Razor `Index.razor`, `Counter.razor` y `FetchData.razor`.</span><span class="sxs-lookup"><span data-stu-id="25cee-116">These pages are implemented by the Razor component files `Index.razor`, `Counter.razor`, and `FetchData.razor`.</span></span>

1. <span data-ttu-id="25cee-117">En la página `Counter`, seleccione el botón para aumentar el contador sin una actualización de página.</span><span class="sxs-lookup"><span data-stu-id="25cee-117">On the `Counter` page, select the button to increment the counter without a page refresh.</span></span> <span data-ttu-id="25cee-118">Para aumentar un contador en una página web suele ser necesario escribir JavaScript.</span><span class="sxs-lookup"><span data-stu-id="25cee-118">Incrementing a counter in a webpage normally requires writing JavaScript.</span></span> <span data-ttu-id="25cee-119">Con Blazor, puede escribir C# en su lugar.</span><span class="sxs-lookup"><span data-stu-id="25cee-119">With Blazor, you can write C# instead.</span></span>

1. <span data-ttu-id="25cee-120">Examine la implementación del componente `Counter` en el archivo `Counter.razor`.</span><span class="sxs-lookup"><span data-stu-id="25cee-120">Examine the implementation of the `Counter` component in the `Counter.razor` file.</span></span>

   <span data-ttu-id="25cee-121">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="25cee-121">`Pages/Counter.razor`:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter1.razor)]

   <span data-ttu-id="25cee-122">la interfaz de usuario del componente `Counter` se define mediante HTML.</span><span class="sxs-lookup"><span data-stu-id="25cee-122">The UI of the `Counter` component is defined using HTML.</span></span> <span data-ttu-id="25cee-123">La lógica de representación dinámica (por ejemplo, bucles, condicionales, expresiones) se agrega mediante una sintaxis de C# insertada denominada [Razor](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="25cee-123">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="25cee-124">El marcado HTML y la lógica de representación de C# se convierten en una clase de componente en tiempo de compilación.</span><span class="sxs-lookup"><span data-stu-id="25cee-124">The HTML markup and C# rendering logic are converted into a component class at build time.</span></span> <span data-ttu-id="25cee-125">El nombre de la clase de .NET generada coincide con el nombre del archivo.</span><span class="sxs-lookup"><span data-stu-id="25cee-125">The name of the generated .NET class matches the file name.</span></span>

   <span data-ttu-id="25cee-126">Los miembros de la clase de componente se definen en un bloque `@code`.</span><span class="sxs-lookup"><span data-stu-id="25cee-126">Members of the component class are defined in an `@code` block.</span></span> <span data-ttu-id="25cee-127">En el bloque `@code`, se especifica el estado del componente (propiedades, campos) y los métodos para el tratamiento de eventos o para definir otra lógica del componente.</span><span class="sxs-lookup"><span data-stu-id="25cee-127">In the `@code` block, component state (properties, fields) and methods are specified for event handling or for defining other component logic.</span></span> <span data-ttu-id="25cee-128">Estos miembros se utilizan como parte de la lógica de representación del componente y para el tratamiento de eventos.</span><span class="sxs-lookup"><span data-stu-id="25cee-128">These members are then used as part of the component's rendering logic and for handling events.</span></span>

   <span data-ttu-id="25cee-129">Al seleccionar el botón de incremento del contador:</span><span class="sxs-lookup"><span data-stu-id="25cee-129">When the counter increment button is selected:</span></span>

   * <span data-ttu-id="25cee-130">Se llama al controlador `onclick` registrado del componente `Counter` (el método `IncrementCount`).</span><span class="sxs-lookup"><span data-stu-id="25cee-130">The `Counter` component's registered `onclick` handler is called (the `IncrementCount` method).</span></span>
   * <span data-ttu-id="25cee-131">El componente `Counter` regenera su árbol de representación.</span><span class="sxs-lookup"><span data-stu-id="25cee-131">The `Counter` component regenerates its render tree.</span></span>
   * <span data-ttu-id="25cee-132">El nuevo árbol de representación se compara con el anterior.</span><span class="sxs-lookup"><span data-stu-id="25cee-132">The new render tree is compared to the previous one.</span></span>
   * <span data-ttu-id="25cee-133">Únicamente se aplican modificaciones en Document Object Model (DOM).</span><span class="sxs-lookup"><span data-stu-id="25cee-133">Only modifications to the Document Object Model (DOM) are applied.</span></span> <span data-ttu-id="25cee-134">Se actualiza el recuento mostrado.</span><span class="sxs-lookup"><span data-stu-id="25cee-134">The displayed count is updated.</span></span>

1. <span data-ttu-id="25cee-135">Modifique la lógica de C# del componente `Counter` para hacer que el recuento se incremente en dos en lugar de uno.</span><span class="sxs-lookup"><span data-stu-id="25cee-135">Modify the C# logic of the `Counter` component to make the count increment by two instead of one.</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter2.razor?highlight=14)]

1. <span data-ttu-id="25cee-136">Recompile y ejecute la aplicación para ver los cambios.</span><span class="sxs-lookup"><span data-stu-id="25cee-136">Rebuild and run the app to see the changes.</span></span> <span data-ttu-id="25cee-137">Seleccione el botón.</span><span class="sxs-lookup"><span data-stu-id="25cee-137">Select the button.</span></span> <span data-ttu-id="25cee-138">El contador se incrementa en dos.</span><span class="sxs-lookup"><span data-stu-id="25cee-138">The counter increments by two.</span></span>

## <a name="use-components"></a><span data-ttu-id="25cee-139">Uso de componentes</span><span class="sxs-lookup"><span data-stu-id="25cee-139">Use components</span></span>

<span data-ttu-id="25cee-140">Incluya un componente en otro componente mediante una sintaxis HTML.</span><span class="sxs-lookup"><span data-stu-id="25cee-140">Include a component in another component using an HTML syntax.</span></span>

1. <span data-ttu-id="25cee-141">Agregue el componente `Counter` al componente `Index` de la aplicación; para ello, agregue un elemento `<Counter />` al componente `Index` (`Index.razor`).</span><span class="sxs-lookup"><span data-stu-id="25cee-141">Add the `Counter` component to the app's `Index` component by adding a `<Counter />` element to the `Index` component (`Index.razor`).</span></span>

   <span data-ttu-id="25cee-142">Si va a usar Blazor WebAssembly en esta experiencia, el componente `Index` usa un componente `SurveyPrompt`.</span><span class="sxs-lookup"><span data-stu-id="25cee-142">If you're using Blazor WebAssembly for this experience, a `SurveyPrompt` component is used by the `Index` component.</span></span> <span data-ttu-id="25cee-143">Reemplace el elemento `<SurveyPrompt>` por un elemento `<Counter />`.</span><span class="sxs-lookup"><span data-stu-id="25cee-143">Replace the `<SurveyPrompt>` element with a `<Counter />` element.</span></span> <span data-ttu-id="25cee-144">Si va a usar la aplicación Blazor Server en esta experiencia, agregue el elemento `<Counter />` al componente `Index`:</span><span class="sxs-lookup"><span data-stu-id="25cee-144">If you're using a Blazor Server app for this experience, add the `<Counter />` element to the `Index` component:</span></span>

   <span data-ttu-id="25cee-145">`Pages/Index.razor`:</span><span class="sxs-lookup"><span data-stu-id="25cee-145">`Pages/Index.razor`:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index1.razor?highlight=7)]

1. <span data-ttu-id="25cee-146">Recompile y ejecute la aplicación.</span><span class="sxs-lookup"><span data-stu-id="25cee-146">Rebuild and run the app.</span></span> <span data-ttu-id="25cee-147">El componente `Index` tiene su propio contador.</span><span class="sxs-lookup"><span data-stu-id="25cee-147">The `Index` component has its own counter.</span></span>

## <a name="component-parameters"></a><span data-ttu-id="25cee-148">Parámetros del componente</span><span class="sxs-lookup"><span data-stu-id="25cee-148">Component parameters</span></span>

<span data-ttu-id="25cee-149">Los componentes también pueden tener parámetros.</span><span class="sxs-lookup"><span data-stu-id="25cee-149">Components can also have parameters.</span></span> <span data-ttu-id="25cee-150">Los parámetros de componente se definen mediante propiedades públicas en la clase de componentes con el atributo [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute).</span><span class="sxs-lookup"><span data-stu-id="25cee-150">Component parameters are defined using public properties on the component class with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute.</span></span> <span data-ttu-id="25cee-151">Use atributos para especificar argumentos para un componente en el marcado.</span><span class="sxs-lookup"><span data-stu-id="25cee-151">Use attributes to specify arguments for a component in markup.</span></span>

1. <span data-ttu-id="25cee-152">Actualice el código de C# `@code` del componente de la siguiente forma:</span><span class="sxs-lookup"><span data-stu-id="25cee-152">Update the component's `@code` C# code as follows:</span></span>

   * <span data-ttu-id="25cee-153">Agregue una propiedad `IncrementAmount` pública con el atributo [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute).</span><span class="sxs-lookup"><span data-stu-id="25cee-153">Add a public `IncrementAmount` property with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute.</span></span>
   * <span data-ttu-id="25cee-154">Cambie el método `IncrementCount` para usar la propiedad `IncrementAmount` al aumentar el valor de `currentCount`.</span><span class="sxs-lookup"><span data-stu-id="25cee-154">Change the `IncrementCount` method to use the `IncrementAmount` property when increasing the value of `currentCount`.</span></span>

   <span data-ttu-id="25cee-155">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="25cee-155">`Pages/Counter.razor`:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter.razor?highlight=13,17)]

   <!-- Add back when supported.
       > [!NOTE]
       > From Visual Studio, you can quickly add a component parameter by using the `para` snippet. Type `para` and press the `Tab` key twice.
   -->

1. <span data-ttu-id="25cee-156">Especifique un parámetro `IncrementAmount` en el elemento `<Counter>` del componente `Index` mediante un atributo.</span><span class="sxs-lookup"><span data-stu-id="25cee-156">Specify an `IncrementAmount` parameter in the `Index` component's `<Counter>` element using an attribute.</span></span> <span data-ttu-id="25cee-157">Establezca el valor para incrementar el contador en diez.</span><span class="sxs-lookup"><span data-stu-id="25cee-157">Set the value to increment the counter by ten.</span></span>

   <span data-ttu-id="25cee-158">`Pages/Index.razor`:</span><span class="sxs-lookup"><span data-stu-id="25cee-158">`Pages/Index.razor`:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index2.razor?highlight=7)]

1. <span data-ttu-id="25cee-159">Recargue el componente `Index`.</span><span class="sxs-lookup"><span data-stu-id="25cee-159">Reload the `Index` component.</span></span> <span data-ttu-id="25cee-160">El contador se incrementa en diez cada vez que se selecciona el botón.</span><span class="sxs-lookup"><span data-stu-id="25cee-160">The counter increments by ten each time the button is selected.</span></span> <span data-ttu-id="25cee-161">El contador del componente `Counter` sigue incrementándose en uno.</span><span class="sxs-lookup"><span data-stu-id="25cee-161">The counter in the `Counter` component continues to increment by one.</span></span>

## <a name="route-to-components"></a><span data-ttu-id="25cee-162">Enrutamiento a los componentes</span><span class="sxs-lookup"><span data-stu-id="25cee-162">Route to components</span></span>

<span data-ttu-id="25cee-163">La directiva `@page` en la parte superior del archivo `Counter.razor` especifica que el componente `Counter` es un punto de conexión de enrutamiento.</span><span class="sxs-lookup"><span data-stu-id="25cee-163">The `@page` directive at the top of the `Counter.razor` file specifies that the `Counter` component is a routing endpoint.</span></span> <span data-ttu-id="25cee-164">El componente `Counter` controla las solicitudes enviadas a `/counter`.</span><span class="sxs-lookup"><span data-stu-id="25cee-164">The `Counter` component handles requests sent to `/counter`.</span></span> <span data-ttu-id="25cee-165">Sin la directiva `@page`, el componente no controla las solicitudes enrutadas, pero otros componentes aún pueden usar el componente.</span><span class="sxs-lookup"><span data-stu-id="25cee-165">Without the `@page` directive, a component doesn't handle routed requests, but the component can still be used by other components.</span></span>

## <a name="dependency-injection"></a><span data-ttu-id="25cee-166">Inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="25cee-166">Dependency injection</span></span>

### <a name="blazor-server-experience"></a><span data-ttu-id="25cee-167">Experiencia de Blazor Server</span><span class="sxs-lookup"><span data-stu-id="25cee-167">Blazor Server experience</span></span>

<span data-ttu-id="25cee-168">Si va a trabajar con una aplicación Blazor Server, el servicio `WeatherForecastService` se registra como [singleton](xref:fundamentals/dependency-injection#service-lifetimes) en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="25cee-168">If working with a Blazor Server app, the `WeatherForecastService` service is registered as a [singleton](xref:fundamentals/dependency-injection#service-lifetimes) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="25cee-169">Una instancia del servicio está disponible en toda la aplicación a través de la [inserción de dependencias (DI)](xref:fundamentals/dependency-injection):</span><span class="sxs-lookup"><span data-stu-id="25cee-169">An instance of the service is available throughout the app via [dependency injection (DI)](xref:fundamentals/dependency-injection):</span></span>

[!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="25cee-170">La directiva [`@inject`](xref:mvc/views/razor#inject) se usa para insertar la instancia del servicio `WeatherForecastService` en el componente `FetchData`.</span><span class="sxs-lookup"><span data-stu-id="25cee-170">The [`@inject`](xref:mvc/views/razor#inject) directive is used to inject the instance of the `WeatherForecastService` service into the `FetchData` component.</span></span>

<span data-ttu-id="25cee-171">`Pages/FetchData.razor`:</span><span class="sxs-lookup"><span data-stu-id="25cee-171">`Pages/FetchData.razor`:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1.razor?highlight=3)]

<span data-ttu-id="25cee-172">El componente `FetchData` usa el servicio insertado, como `ForecastService`, para recuperar una matriz de objetos `WeatherForecast`:</span><span class="sxs-lookup"><span data-stu-id="25cee-172">The `FetchData` component uses the injected service, as `ForecastService`, to retrieve an array of `WeatherForecast` objects:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData2.razor?highlight=6)]

### <a name="blazor-webassembly-experience"></a><span data-ttu-id="25cee-173">Experiencia de Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="25cee-173">Blazor WebAssembly experience</span></span>

<span data-ttu-id="25cee-174">Si trabaja con una aplicación WebAssembly de Blazor, se inserta <xref:System.Net.Http.HttpClient> para obtener datos de previsión del tiempo del archivo `weather.json` de la carpeta `wwwroot/sample-data`.</span><span class="sxs-lookup"><span data-stu-id="25cee-174">If working with a Blazor WebAssembly app, <xref:System.Net.Http.HttpClient> is injected to obtain weather forecast data from the `weather.json` file in the `wwwroot/sample-data` folder.</span></span>

<span data-ttu-id="25cee-175">`Pages/FetchData.razor`:</span><span class="sxs-lookup"><span data-stu-id="25cee-175">`Pages/FetchData.razor`:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1_client.razor?highlight=7-9)]

<span data-ttu-id="25cee-176">Se usa un bucle [`@foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) para representar cada instancia de previsión como una fila en la tabla de datos meteorológicos:</span><span class="sxs-lookup"><span data-stu-id="25cee-176">An [`@foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop is used to render each forecast instance as a row in the table of weather data:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData3.razor?highlight=11-19)]

## <a name="build-a-todo-list"></a><span data-ttu-id="25cee-177">Creación de una lista de tareas pendientes</span><span class="sxs-lookup"><span data-stu-id="25cee-177">Build a todo list</span></span>

<span data-ttu-id="25cee-178">Agregue un nuevo componente a la aplicación que implemente una simple lista de tareas pendientes.</span><span class="sxs-lookup"><span data-stu-id="25cee-178">Add a new component to the app that implements a simple todo list.</span></span>

1. <span data-ttu-id="25cee-179">Agregue un nuevo componente `Todo` de Razor a la aplicación en la carpeta `Pages`.</span><span class="sxs-lookup"><span data-stu-id="25cee-179">Add a new `Todo` Razor component to the app in the `Pages` folder.</span></span> <span data-ttu-id="25cee-180">Si usa Visual Studio, haga clic con el botón derecho en la carpeta `Pages` y seleccione **Agregar** > **Nuevo elemento** > **Componente de Razor** .</span><span class="sxs-lookup"><span data-stu-id="25cee-180">If you're using Visual Studio, right-click the `Pages` folder and select **Add** > **New Item** > **Razor Component**.</span></span> <span data-ttu-id="25cee-181">Asigne el nombre `Todo.razor` al archivo del componente.</span><span class="sxs-lookup"><span data-stu-id="25cee-181">Name the component's file `Todo.razor`.</span></span> <span data-ttu-id="25cee-182">En otros entornos de desarrollo, agregue un archivo en blanco denominado `Todo.razor` a la carpeta `Pages`.</span><span class="sxs-lookup"><span data-stu-id="25cee-182">In other development environments, add a blank file to the `Pages` folder named `Todo.razor`.</span></span> <span data-ttu-id="25cee-183">Puesto que la primera letra de los nombres de los archivos del componente Razor debe estar en mayúscula, asegúrese de que el nombre de archivo del componente `Todo` comience por una letra `T` mayúscula.</span><span class="sxs-lookup"><span data-stu-id="25cee-183">Razor component file names require a capitalized first letter, so confirm that the `Todo` component file name starts with a capital letter `T`.</span></span>

1. <span data-ttu-id="25cee-184">Proporcione el marcado inicial para el componente:</span><span class="sxs-lookup"><span data-stu-id="25cee-184">Provide the initial markup for the component:</span></span>

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

1. <span data-ttu-id="25cee-185">Agregue el componente `Todo` a la barra de navegación.</span><span class="sxs-lookup"><span data-stu-id="25cee-185">Add the `Todo` component to the navigation bar.</span></span>

   <span data-ttu-id="25cee-186">El componente `NavMenu` (`Shared/NavMenu.razor`) se usa en el diseño de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="25cee-186">The `NavMenu` component (`Shared/NavMenu.razor`) is used in the app's layout.</span></span> <span data-ttu-id="25cee-187">Los diseños son componentes que le permiten impedir la duplicación de contenido en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="25cee-187">Layouts are components that allow you to avoid duplication of content in the app.</span></span>

   <span data-ttu-id="25cee-188">Agregue un elemento `<NavLink>` al componente `Todo`; para ello, incorpore el siguiente marcado de elementos de lista debajo de los elementos de lista existentes en el archivo `Shared/NavMenu.razor`:</span><span class="sxs-lookup"><span data-stu-id="25cee-188">Add a `<NavLink>` element for the `Todo` component by adding the following list item markup below the existing list items in the `Shared/NavMenu.razor` file:</span></span>

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. <span data-ttu-id="25cee-189">Recompile y ejecute la aplicación.</span><span class="sxs-lookup"><span data-stu-id="25cee-189">Rebuild and run the app.</span></span> <span data-ttu-id="25cee-190">Visite la nueva página Todo para confirmar que el vínculo al componente `Todo` funcione.</span><span class="sxs-lookup"><span data-stu-id="25cee-190">Visit the new Todo page to confirm that the link to the `Todo` component works.</span></span>

1. <span data-ttu-id="25cee-191">Agregue un archivo `TodoItem.cs` a la raíz del proyecto para contener una clase que represente un elemento de la lista de tareas.</span><span class="sxs-lookup"><span data-stu-id="25cee-191">Add a `TodoItem.cs` file to the root of the project to hold a class that represents a todo item.</span></span> <span data-ttu-id="25cee-192">Use el siguiente código de C# para la clase `TodoItem`:</span><span class="sxs-lookup"><span data-stu-id="25cee-192">Use the following C# code for the `TodoItem` class:</span></span>

   [!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. <span data-ttu-id="25cee-193">Vuelva al componente `Todo` (`Pages/Todo.razor`):</span><span class="sxs-lookup"><span data-stu-id="25cee-193">Return to the `Todo` component (`Pages/Todo.razor`):</span></span>

   * <span data-ttu-id="25cee-194">Agregue un campo a los elementos de tareas pendientes en un bloque `@code`.</span><span class="sxs-lookup"><span data-stu-id="25cee-194">Add a field for the todo items in an `@code` block.</span></span> <span data-ttu-id="25cee-195">El componente `Todo` utiliza este campo para mantener el estado de la lista de tareas pendientes.</span><span class="sxs-lookup"><span data-stu-id="25cee-195">The `Todo` component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="25cee-196">Agregue el marcado de la lista no ordenada y un bucle `foreach` para que cada elemento de la lista se represente en un elemento de la lista de tareas pendientes (`<li>`).</span><span class="sxs-lookup"><span data-stu-id="25cee-196">Add unordered list markup and a `foreach` loop to render each todo item as a list item (`<li>`).</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. <span data-ttu-id="25cee-197">Para agregar elementos de tareas pendientes a la lista, la aplicación requiere elementos de la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="25cee-197">The app requires UI elements for adding todo items to the list.</span></span> <span data-ttu-id="25cee-198">Agregue una entrada de texto (`<input>`) y un botón (`<button>`) debajo de la lista no ordenada (`<ul>...</ul>`):</span><span class="sxs-lookup"><span data-stu-id="25cee-198">Add a text input (`<input>`) and a button (`<button>`) below the unordered list (`<ul>...</ul>`):</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. <span data-ttu-id="25cee-199">Recompile y ejecute la aplicación.</span><span class="sxs-lookup"><span data-stu-id="25cee-199">Rebuild and run the app.</span></span> <span data-ttu-id="25cee-200">Al seleccionar el botón **`Add todo`** , no ocurre nada porque no hay ningún controlador de eventos conectado al botón.</span><span class="sxs-lookup"><span data-stu-id="25cee-200">When the **`Add todo`** button is selected, nothing happens because an event handler isn't wired up to the button.</span></span>

1. <span data-ttu-id="25cee-201">Agregue un método `AddTodo` al componente `Todo` y regístrelo para seleccionar los botones mediante el atributo `@onclick`.</span><span class="sxs-lookup"><span data-stu-id="25cee-201">Add an `AddTodo` method to the `Todo` component and register it for button selections using the `@onclick` attribute.</span></span> <span data-ttu-id="25cee-202">El método `AddTodo` de C# se llama cuando se selecciona el botón:</span><span class="sxs-lookup"><span data-stu-id="25cee-202">The `AddTodo` C# method is called when the button is selected:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

1. <span data-ttu-id="25cee-203">Para obtener el título del nuevo elemento de tarea pendiente, agregue un campo de cadena `newTodo` en la parte superior del bloque `@code` y enlácelo al valor de la entrada de texto mediante el atributo `bind` en el elemento `<input>`:</span><span class="sxs-lookup"><span data-stu-id="25cee-203">To get the title of the new todo item, add a `newTodo` string field at the top of the `@code` block and bind it to the value of the text input using the `bind` attribute in the `<input>` element:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. <span data-ttu-id="25cee-204">Actualice el método `AddTodo` para agregar el `TodoItem` con el título especificado a la lista.</span><span class="sxs-lookup"><span data-stu-id="25cee-204">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="25cee-205">Borre el valor de la entrada de texto mediante el establecimiento de `newTodo` en una cadena vacía:</span><span class="sxs-lookup"><span data-stu-id="25cee-205">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. <span data-ttu-id="25cee-206">Recompile y ejecute la aplicación.</span><span class="sxs-lookup"><span data-stu-id="25cee-206">Rebuild and run the app.</span></span> <span data-ttu-id="25cee-207">Agregue algunos elementos de tareas pendientes a la lista de tareas pendientes para probar el nuevo código.</span><span class="sxs-lookup"><span data-stu-id="25cee-207">Add some todo items to the todo list to test the new code.</span></span>

1. <span data-ttu-id="25cee-208">Se puede hacer que el texto de título de cada elemento de tarea pendiente sea editable y una casilla puede ayudar al usuario a realizar un seguimiento de los elementos completados.</span><span class="sxs-lookup"><span data-stu-id="25cee-208">The title text for each todo item can be made editable, and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="25cee-209">Agregue una entrada de casilla a cada elemento de tarea pendiente y enlace su valor a la propiedad `IsDone`.</span><span class="sxs-lookup"><span data-stu-id="25cee-209">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="25cee-210">Cambie `@todo.Title` a un elemento `<input>` enlazado a `@todo.Title`:</span><span class="sxs-lookup"><span data-stu-id="25cee-210">Change `@todo.Title` to an `<input>` element bound to `@todo.Title`:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. <span data-ttu-id="25cee-211">Para comprobar que estos valores están enlazados, actualice el encabezado `<h3>` para mostrar un recuento del número de elementos de la lista de tareas pendientes que no se han completado (`IsDone` es `false`).</span><span class="sxs-lookup"><span data-stu-id="25cee-211">To verify that these values are bound, update the `<h3>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. <span data-ttu-id="25cee-212">El componente `Todo` completado (`Pages/Todo.razor`):</span><span class="sxs-lookup"><span data-stu-id="25cee-212">The completed `Todo` component (`Pages/Todo.razor`):</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. <span data-ttu-id="25cee-213">Recompile y ejecute la aplicación.</span><span class="sxs-lookup"><span data-stu-id="25cee-213">Rebuild and run the app.</span></span> <span data-ttu-id="25cee-214">Agregue elementos de tarea pendiente para probar el nuevo código.</span><span class="sxs-lookup"><span data-stu-id="25cee-214">Add todo items to test the new code.</span></span>

## <a name="next-steps"></a><span data-ttu-id="25cee-215">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="25cee-215">Next steps</span></span>

<span data-ttu-id="25cee-216">En este tutorial ha aprendido a:</span><span class="sxs-lookup"><span data-stu-id="25cee-216">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="25cee-217">Crear un proyecto de aplicación Blazor de lista de tareas</span><span class="sxs-lookup"><span data-stu-id="25cee-217">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="25cee-218">Modificar los componentes de Razor</span><span class="sxs-lookup"><span data-stu-id="25cee-218">Modify Razor components</span></span>
> * <span data-ttu-id="25cee-219">Usar el control de eventos y el enlace de datos en los componentes</span><span class="sxs-lookup"><span data-stu-id="25cee-219">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="25cee-220">Usar la inserción de dependencias (DI) y el enrutamiento en una aplicación Blazor</span><span class="sxs-lookup"><span data-stu-id="25cee-220">Use dependency injection (DI) and routing in a Blazor app</span></span>

<span data-ttu-id="25cee-221">Aprenda a compilar y usar componentes:</span><span class="sxs-lookup"><span data-stu-id="25cee-221">Learn how to build and use components:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/components/index>
