---
title: Compilación de la primera aplicación Blazor
author: guardrex
description: Cree una aplicación Blazor paso a paso.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 03/20/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-blazor-app
ms.openlocfilehash: 5a5a56ee646cba21a883df2cf686cb1ccb18d7f9
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776609"
---
# <a name="build-your-first-blazor-app"></a>Compilación de la primera aplicación Blazor

Por [Daniel Roth](https://github.com/danroth27) y [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

En este tutorial se muestra cómo crear y modificar una aplicación Blazor.

## <a name="build-components"></a>Creación de componentes

1. Siga las instrucciones del artículo <xref:blazor/get-started> para crear un proyecto de Blazor en este tutorial. Denomine el proyecto *ToDoList*.

1. Vaya a cada una de las tres páginas de la aplicación en la carpeta *Pages*: Home (Inicio), Counter (Contador) y Fetch data (Recuperar datos). Estas páginas se implementan mediante los archivos de componentes de Razor *Index.razor*, *Counter.razor* y *FetchData.razor*.

1. En la página Contador, seleccione el botón **Click me** para aumentar el contador sin una actualización de página. Para aumentar un contador en una página web suele ser necesario escribir JavaScript. Con Blazor, puede escribir C# en su lugar.

1. Examine la implementación del componente `Counter` en el archivo *Counter.razor*.

   *Pages/Counter.razor*:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter1.razor)]

   la interfaz de usuario del componente `Counter` se define mediante HTML. La lógica de representación dinámica (por ejemplo, bucles, condicionales, expresiones) se agrega mediante una sintaxis de C# insertada denominada [Razor](xref:mvc/views/razor). El marcado HTML y la lógica de representación de C# se convierten en una clase de componente en tiempo de compilación. El nombre de la clase de .NET generada coincide con el nombre del archivo.

   Los miembros de la clase de componente se definen en un bloque `@code`. En el bloque `@code`, se especifica el estado del componente (propiedades, campos) y los métodos para el tratamiento de eventos o para definir otra lógica del componente. Estos miembros se utilizan como parte de la lógica de representación del componente y para el tratamiento de eventos.

   Al seleccionarse el botón **Click me**:

   * Se llama al controlador `onclick` registrado del componente `Counter` (el método `IncrementCount`).
   * El componente `Counter` regenera su árbol de representación.
   * El nuevo árbol de representación se compara con el anterior.
   * Únicamente se aplican modificaciones en Document Object Model (DOM). Se actualiza el recuento mostrado.

1. Modifique la lógica de C# del componente `Counter` para hacer que el recuento se incremente en dos en lugar de uno.

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter2.razor?highlight=14)]

1. Recompile y ejecute la aplicación para ver los cambios. Seleccione el botón **Hacer clic aquí**. El contador se incrementa en dos.

## <a name="use-components"></a>Uso de componentes

Incluya un componente en otro componente mediante una sintaxis HTML.

1. Agregue el componente `Counter` al componente `Index` de la aplicación al agregar un elemento `<Counter />` al componente `Index` (*Index.razor*).

   Si va a usar Blazor WebAssembly en esta experiencia, el componente `Index` usa un componente `SurveyPrompt`. Reemplace el elemento `<SurveyPrompt>` por un elemento `<Counter />`. Si va a usar la aplicación Blazor Server en esta experiencia, agregue el elemento `<Counter />` al componente `Index`:

   *Pages/Index.razor*:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index1.razor?highlight=7)]

1. Recompile y ejecute la aplicación. El componente `Index` tiene su propio contador.

## <a name="component-parameters"></a>Parámetros del componente

Los componentes también pueden tener parámetros. Los parámetros de componente se definen mediante propiedades públicas en la clase de componentes con el atributo `[Parameter]`. Use atributos para especificar argumentos para un componente en el marcado.

1. Actualice el código de C# `@code` del componente de la siguiente forma:

   * Agregue una propiedad `IncrementAmount` pública con el atributo `[Parameter]`.
   * Cambie el método `IncrementCount` para usar la propiedad `IncrementAmount` al aumentar el valor de `currentCount`.

   *Pages/Counter.razor*:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter.razor?highlight=13,17)]

   <!-- Add back when supported.
       > [!NOTE]
       > From Visual Studio, you can quickly add a component parameter by using the `para` snippet. Type `para` and press the `Tab` key twice.
   -->

1. Especifique un parámetro `IncrementAmount` en el elemento `<Counter>` del componente `Index` mediante un atributo. Establezca el valor para incrementar el contador en diez.

   *Pages/Index.razor*:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index2.razor?highlight=7)]

1. Recargue el componente `Index`. El contador se incrementa en diez cada vez que se selecciona el botón **Click me**. El contador del componente `Counter` sigue incrementándose en uno.

## <a name="route-to-components"></a>Enrutamiento a los componentes

La directiva `@page` en la parte superior del archivo *Counter.razor* especifica que el componente `Counter` es un punto de conexión de enrutamiento. El componente `Counter` controla las solicitudes enviadas a `/counter`. Sin la directiva `@page`, el componente no controla las solicitudes enrutadas, pero otros componentes aún pueden usar el componente.

## <a name="dependency-injection"></a>Inserción de dependencias

### <a name="blazor-server-experience"></a>Experiencia de Blazor Server

Si va a trabajar con una aplicación Blazor Server, el servicio `WeatherForecastService` se registra como [singleton](xref:fundamentals/dependency-injection#service-lifetimes) en `Startup.ConfigureServices`. Una instancia del servicio está disponible en toda la aplicación a través de la [inserción de dependencias (DI)](xref:fundamentals/dependency-injection):

[!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/Startup.cs?highlight=5)]

La directiva `@inject` se usa para insertar la instancia del servicio `WeatherForecastService` en el componente `FetchData`.

*Pages/FetchData.razor*:

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1.razor?highlight=3)]

El componente `FetchData` usa el servicio insertado, como `ForecastService`, para recuperar una matriz de objetos `WeatherForecast`:

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData2.razor?highlight=6)]

### <a name="blazor-webassembly-experience"></a>Experiencia de Blazor WebAssembly

Si trabaja con una aplicación Blazor WebAssembly, se inserta `HttpClient` para obtener datos de previsión del tiempo del archivo *weather.json* de la carpeta *wwwroot/sample-data*.

*Pages/FetchData.razor*:

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1_client.razor?highlight=7-8)]

Se usa un bucle [`@foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) para representar cada instancia de previsión como una fila en la tabla de datos meteorológicos:

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData3.razor?highlight=11-19)]

## <a name="build-a-todo-list"></a>Creación de una lista de tareas pendientes

Agregue un nuevo componente a la aplicación que implemente una simple lista de tareas pendientes.

1. Agregue un nuevo componente `Todo` de Razor a la aplicación en la carpeta *Pages*. En Visual Studio, haga clic con el botón derecho en la carpeta **Pages** y seleccione **Agregar** > **Nuevo elemento** > **Componente de Razor** . Asigne el nombre *Todo.razor* al archivo del componente. En otros entornos de desarrollo, agregue un archivo en blanco denominado *Todo.razor* a la carpeta **Páginas**.

1. Proporcione el marcado inicial para el componente:

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

1. Agregue el componente `Todo` a la barra de navegación.

   El componente `NavMenu` (*Shared/NavMenu.razor*) se usa en el diseño de la aplicación. Los diseños son componentes que le permiten impedir la duplicación de contenido en la aplicación.

   Agregue un elemento `<NavLink>` al componente `Todo` mediante la incorporación del siguiente marcado de elementos de lista debajo de los elementos de lista existentes en el archivo *Shared/NavMenu.razor*:

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. Recompile y ejecute la aplicación. Visite la nueva página Todo para confirmar que el vínculo al componente `Todo` funcione.

1. Agregue un archivo *TodoItem.cs* a la raíz del proyecto para contener una clase que represente un elemento de la lista de tareas. Use el siguiente código de C# para la clase `TodoItem`:

   [!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. Vuelva al componente `Todo` (*Pages/Todo.razor*):

   * Agregue un campo a los elementos de tareas pendientes en un bloque `@code`. El componente `Todo` utiliza este campo para mantener el estado de la lista de tareas pendientes.
   * Agregue el marcado de la lista no ordenada y un bucle `foreach` para que cada elemento de la lista se represente en un elemento de la lista de tareas pendientes (`<li>`).

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. Para agregar elementos de tareas pendientes a la lista, la aplicación requiere elementos de la interfaz de usuario. Agregue una entrada de texto (`<input>`) y un botón (`<button>`) debajo de la lista no ordenada (`<ul>...</ul>`):

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. Recompile y ejecute la aplicación. Cuando se selecciona el botón **Add todo** (Agregar tarea pendiente), no ocurre nada porque no hay ningún controlador de eventos conectado al botón.

1. Agregue un método `AddTodo` al componente `Todo` y regístrelo para seleccionar los botones mediante el atributo `@onclick`. El método `AddTodo` de C# se llama cuando se selecciona el botón:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

1. Para obtener el título del nuevo elemento de tarea pendiente, agregue un campo de cadena `newTodo` en la parte superior del bloque `@code` y enlácelo al valor de la entrada de texto mediante el atributo `bind` en el elemento `<input>`:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. Actualice el método `AddTodo` para agregar el `TodoItem` con el título especificado a la lista. Borre el valor de la entrada de texto mediante el establecimiento de `newTodo` en una cadena vacía:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. Recompile y ejecute la aplicación. Agregue algunos elementos de tareas pendientes a la lista de tareas pendientes para probar el nuevo código.

1. Se puede hacer que el texto de título de cada elemento de tarea pendiente sea editable y una casilla puede ayudar al usuario a realizar un seguimiento de los elementos completados. Agregue una entrada de casilla a cada elemento de tarea pendiente y enlace su valor a la propiedad `IsDone`. Cambie `@todo.Title` a un elemento `<input>` enlazado a `@todo.Title`:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. Para comprobar que estos valores están enlazados, actualice el encabezado `<h3>` para mostrar un recuento del número de elementos de la lista de tareas pendientes que no se han completado (`IsDone` es `false`).

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. El componente `Todo` completado (*Pages/Todo.razor*):

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. Recompile y ejecute la aplicación. Agregue elementos de tarea pendiente para probar el nuevo código.

> [!div class="nextstepaction"]
> <xref:blazor/components>
