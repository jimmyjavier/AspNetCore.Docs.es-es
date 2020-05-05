---
title: JsonPatch en la API web de ASP.NET Core
author: rick-anderson
description: Aprenda a administrar solicitudes JSON Patch en una API web ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/02/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: web-api/jsonpatch
ms.openlocfilehash: 3a78fa268cce8cff10fedf5814d61ce0e5faaf4b
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2020
ms.locfileid: "82766672"
---
# <a name="jsonpatch-in-aspnet-core-web-api"></a><span data-ttu-id="429c3-103">JsonPatch en la API web de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="429c3-103">JsonPatch in ASP.NET Core web API</span></span>

<span data-ttu-id="429c3-104">Por [Tom Dykstra](https://github.com/tdykstra) y [Kirk Larkin](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="429c3-104">By [Tom Dykstra](https://github.com/tdykstra) and [Kirk Larkin](https://github.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="429c3-105">En este artículo se explica cómo administrar solicitudes JSON Patch en una API web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="429c3-105">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="package-installation"></a><span data-ttu-id="429c3-106">Instalación del paquete</span><span class="sxs-lookup"><span data-stu-id="429c3-106">Package installation</span></span>

<span data-ttu-id="429c3-107">Para habilitar la compatibilidad con la revisión de JSON en la aplicación, complete los pasos siguientes:</span><span class="sxs-lookup"><span data-stu-id="429c3-107">To enable JSON Patch support in your app, complete the following steps:</span></span>

1. <span data-ttu-id="429c3-108">Instalar el paquete NuGet [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/).</span><span class="sxs-lookup"><span data-stu-id="429c3-108">Install the [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package.</span></span>
1. <span data-ttu-id="429c3-109">Actualice el método del `Startup.ConfigureServices` proyecto para llamar <xref:Microsoft.Extensions.DependencyInjection.NewtonsoftJsonMvcBuilderExtensions.AddNewtonsoftJson*>a.</span><span class="sxs-lookup"><span data-stu-id="429c3-109">Update the project's `Startup.ConfigureServices` method to call <xref:Microsoft.Extensions.DependencyInjection.NewtonsoftJsonMvcBuilderExtensions.AddNewtonsoftJson*>.</span></span> <span data-ttu-id="429c3-110">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="429c3-110">For example:</span></span>

    ```csharp
    services
        .AddControllersWithViews()
        .AddNewtonsoftJson();
    ```

<span data-ttu-id="429c3-111">`AddNewtonsoftJson` es compatible con los métodos de registro del servicio MVC:</span><span class="sxs-lookup"><span data-stu-id="429c3-111">`AddNewtonsoftJson` is compatible with the MVC service registration methods:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages*>
* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews*>
* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers*>

## <a name="json-patch-addnewtonsoftjson-and-systemtextjson"></a><span data-ttu-id="429c3-112">Revisión de JSON, AddNewtonsoftJson y System. Text. JSON</span><span class="sxs-lookup"><span data-stu-id="429c3-112">JSON Patch, AddNewtonsoftJson, and System.Text.Json</span></span>

<span data-ttu-id="429c3-113">`AddNewtonsoftJson`reemplaza los `System.Text.Json`formateadores de entrada y salida basados en que se usan para dar formato a **todo** el contenido JSON.</span><span class="sxs-lookup"><span data-stu-id="429c3-113">`AddNewtonsoftJson` replaces the `System.Text.Json`-based input and output formatters used for formatting **all** JSON content.</span></span> <span data-ttu-id="429c3-114">Para agregar compatibilidad con la revisión de `Newtonsoft.Json`JSON mediante, sin modificar los otros formateadores, actualice el método del `Startup.ConfigureServices` proyecto como se indica a continuación:</span><span class="sxs-lookup"><span data-stu-id="429c3-114">To add support for JSON Patch using `Newtonsoft.Json`, while leaving the other formatters unchanged, update the project's `Startup.ConfigureServices` method as follows:</span></span>

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet)]

<span data-ttu-id="429c3-115">El código anterior requiere el `Microsoft.AspNetCore.Mvc.NewtonsoftJson` paquete y las siguientes `using` instrucciones:</span><span class="sxs-lookup"><span data-stu-id="429c3-115">The preceding code requires the `Microsoft.AspNetCore.Mvc.NewtonsoftJson` package and the following `using` statements:</span></span>

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet1)]

## <a name="patch-http-request-method"></a><span data-ttu-id="429c3-116">Método de solicitud HTTP PATCH</span><span class="sxs-lookup"><span data-stu-id="429c3-116">PATCH HTTP request method</span></span>

<span data-ttu-id="429c3-117">Los métodos PUT y [PATCH](https://tools.ietf.org/html/rfc5789) se usan para actualizar un recurso existente.</span><span class="sxs-lookup"><span data-stu-id="429c3-117">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="429c3-118">La diferencia entre ellos es que PUT reemplaza el recurso entero, mientras que PATCH especifica únicamente los cambios.</span><span class="sxs-lookup"><span data-stu-id="429c3-118">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="429c3-119">JSON Patch</span><span class="sxs-lookup"><span data-stu-id="429c3-119">JSON Patch</span></span>

<span data-ttu-id="429c3-120">[JSON Patch](https://tools.ietf.org/html/rfc6902) es un formato para especificar las actualizaciones que se aplicarán a un recurso.</span><span class="sxs-lookup"><span data-stu-id="429c3-120">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="429c3-121">Un documento JSON Patch tiene una matriz de *operaciones*.</span><span class="sxs-lookup"><span data-stu-id="429c3-121">A JSON Patch document has an array of *operations*.</span></span> <span data-ttu-id="429c3-122">Cada operación identifica un tipo de cambio determinado.</span><span class="sxs-lookup"><span data-stu-id="429c3-122">Each operation identifies a particular type of change.</span></span> <span data-ttu-id="429c3-123">Algunos ejemplos de estos cambios incluyen agregar un elemento de matriz o reemplazar un valor de propiedad.</span><span class="sxs-lookup"><span data-stu-id="429c3-123">Examples of such changes include adding an array element or replacing a property value.</span></span>

<span data-ttu-id="429c3-124">Por ejemplo, los siguientes documentos JSON representan un recurso, un documento de revisión JSON para el recurso y el resultado de aplicar las operaciones patch.</span><span class="sxs-lookup"><span data-stu-id="429c3-124">For example, the following JSON documents represent a resource, a JSON Patch document for the resource, and the result of applying the Patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="429c3-125">Ejemplo de recursos</span><span class="sxs-lookup"><span data-stu-id="429c3-125">Resource example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="429c3-126">Ejemplo de revisión de JSON</span><span class="sxs-lookup"><span data-stu-id="429c3-126">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="429c3-127">En el código JSON anterior:</span><span class="sxs-lookup"><span data-stu-id="429c3-127">In the preceding JSON:</span></span>

* <span data-ttu-id="429c3-128">La propiedad `op` indica el tipo de operación.</span><span class="sxs-lookup"><span data-stu-id="429c3-128">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="429c3-129">La propiedad `path` indica el elemento que se va a actualizar.</span><span class="sxs-lookup"><span data-stu-id="429c3-129">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="429c3-130">La propiedad `value` proporciona el nuevo valor.</span><span class="sxs-lookup"><span data-stu-id="429c3-130">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="429c3-131">Recurso después de la revisión</span><span class="sxs-lookup"><span data-stu-id="429c3-131">Resource after patch</span></span>

<span data-ttu-id="429c3-132">Este es el recurso después de aplicar el documento JSON Patch anterior:</span><span class="sxs-lookup"><span data-stu-id="429c3-132">Here's the resource after applying the preceding JSON Patch document:</span></span>

```json
{
  "customerName": "Barry",
  "orders": [
    {
      "orderName": "Order0",
      "orderType": null
    },
    {
      "orderName": "Order1",
      "orderType": null
    },
    {
      "orderName": "Order2",
      "orderType": null
    }
  ]
}
```

<span data-ttu-id="429c3-133">Los cambios realizados al aplicar un documento de revisión de JSON a un recurso son atómicos.</span><span class="sxs-lookup"><span data-stu-id="429c3-133">The changes made by applying a JSON Patch document to a resource are atomic.</span></span> <span data-ttu-id="429c3-134">Si se produce un error en cualquier operación de la lista, no se aplica ninguna operación en la lista.</span><span class="sxs-lookup"><span data-stu-id="429c3-134">If any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="429c3-135">Sintaxis de path</span><span class="sxs-lookup"><span data-stu-id="429c3-135">Path syntax</span></span>

<span data-ttu-id="429c3-136">La propiedad [path](https://tools.ietf.org/html/rfc6901) de un objeto de operación tiene barras inversas entre niveles.</span><span class="sxs-lookup"><span data-stu-id="429c3-136">The [path](https://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="429c3-137">Por ejemplo: `"/address/zipCode"`.</span><span class="sxs-lookup"><span data-stu-id="429c3-137">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="429c3-138">Para especificar elementos de matriz se usan índices de base cero.</span><span class="sxs-lookup"><span data-stu-id="429c3-138">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="429c3-139">El primer elemento de la matriz `addresses` estaría en `/addresses/0`.</span><span class="sxs-lookup"><span data-stu-id="429c3-139">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="429c3-140">Hasta `add` el final de una matriz, use un guion (`-`) en lugar de un número de índice `/addresses/-`:.</span><span class="sxs-lookup"><span data-stu-id="429c3-140">To `add` to the end of an array, use a hyphen (`-`) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="429c3-141">Operaciones</span><span class="sxs-lookup"><span data-stu-id="429c3-141">Operations</span></span>

<span data-ttu-id="429c3-142">En la siguiente tabla se muestran las operaciones admitidas, como se ha definido en la [especificación de JSON Patch](https://tools.ietf.org/html/rfc6902):</span><span class="sxs-lookup"><span data-stu-id="429c3-142">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="429c3-143">Operación</span><span class="sxs-lookup"><span data-stu-id="429c3-143">Operation</span></span>  | <span data-ttu-id="429c3-144">Notas</span><span class="sxs-lookup"><span data-stu-id="429c3-144">Notes</span></span> |
|-----------|--------------------------------|
| `add`     | <span data-ttu-id="429c3-145">Agrega un elemento de propiedad o matriz.</span><span class="sxs-lookup"><span data-stu-id="429c3-145">Add a property or array element.</span></span> <span data-ttu-id="429c3-146">Para la propiedad existente: establece el valor.</span><span class="sxs-lookup"><span data-stu-id="429c3-146">For existing property: set value.</span></span>|
| `remove`  | <span data-ttu-id="429c3-147">Quita un elemento de propiedad o matriz.</span><span class="sxs-lookup"><span data-stu-id="429c3-147">Remove a property or array element.</span></span> |
| `replace` | <span data-ttu-id="429c3-148">Lo mismo que `remove` seguido de `add` en la misma ubicación.</span><span class="sxs-lookup"><span data-stu-id="429c3-148">Same as `remove` followed by `add` at same location.</span></span> |
| `move`    | <span data-ttu-id="429c3-149">Lo mismo que `remove` desde el origen seguido de `add` al destino mediante el valor del origen.</span><span class="sxs-lookup"><span data-stu-id="429c3-149">Same as `remove` from source followed by `add` to destination using value from source.</span></span> |
| `copy`    | <span data-ttu-id="429c3-150">Lo mismo que `add` al destino mediante el valor del origen.</span><span class="sxs-lookup"><span data-stu-id="429c3-150">Same as `add` to destination using value from source.</span></span> |
| `test`    | <span data-ttu-id="429c3-151">Devuelve el código de estado correcto si el valor en `path` = al `value` proporcionado.</span><span class="sxs-lookup"><span data-stu-id="429c3-151">Return success status code if value at `path` = provided `value`.</span></span>|

## <a name="json-patch-in-aspnet-core"></a><span data-ttu-id="429c3-152">Revisión de JSON en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="429c3-152">JSON Patch in ASP.NET Core</span></span>

<span data-ttu-id="429c3-153">La implementación de ASP.NET Core de JSON Patch se proporciona en el paquete NuGet [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/).</span><span class="sxs-lookup"><span data-stu-id="429c3-153">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="429c3-154">Código del método de acción</span><span class="sxs-lookup"><span data-stu-id="429c3-154">Action method code</span></span>

<span data-ttu-id="429c3-155">En un controlador de API, un método de acción para JSON Patch:</span><span class="sxs-lookup"><span data-stu-id="429c3-155">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="429c3-156">Se anota con el atributo `HttpPatch`.</span><span class="sxs-lookup"><span data-stu-id="429c3-156">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="429c3-157">Acepta `JsonPatchDocument<T>`, normalmente con `[FromBody]`.</span><span class="sxs-lookup"><span data-stu-id="429c3-157">Accepts a `JsonPatchDocument<T>`, typically with `[FromBody]`.</span></span>
* <span data-ttu-id="429c3-158">Llama a `ApplyTo` en el documento de revisión para aplicar los cambios.</span><span class="sxs-lookup"><span data-stu-id="429c3-158">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="429c3-159">Este es un ejemplo:</span><span class="sxs-lookup"><span data-stu-id="429c3-159">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="429c3-160">Este código de la aplicación de ejemplo funciona con el `Customer` siguiente modelo:</span><span class="sxs-lookup"><span data-stu-id="429c3-160">This code from the sample app works with the following `Customer` model:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="429c3-161">El método de acción de ejemplo:</span><span class="sxs-lookup"><span data-stu-id="429c3-161">The sample action method:</span></span>

* <span data-ttu-id="429c3-162">Construye un objeto `Customer`.</span><span class="sxs-lookup"><span data-stu-id="429c3-162">Constructs a `Customer`.</span></span>
* <span data-ttu-id="429c3-163">Aplica la revisión.</span><span class="sxs-lookup"><span data-stu-id="429c3-163">Applies the patch.</span></span>
* <span data-ttu-id="429c3-164">Devuelve el resultado en el cuerpo de la respuesta.</span><span class="sxs-lookup"><span data-stu-id="429c3-164">Returns the result in the body of the response.</span></span>

<span data-ttu-id="429c3-165">En una aplicación real, el código recuperaría los datos de un almacén como una base de datos y actualizaría la base de datos después de aplicar la revisión.</span><span class="sxs-lookup"><span data-stu-id="429c3-165">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="429c3-166">Estado del modelo</span><span class="sxs-lookup"><span data-stu-id="429c3-166">Model state</span></span>

<span data-ttu-id="429c3-167">En el ejemplo anterior del método de acción, se llama a una sobrecarga de `ApplyTo` que toma el estado del modelo como uno de sus parámetros.</span><span class="sxs-lookup"><span data-stu-id="429c3-167">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="429c3-168">Con esta opción, puede obtener mensajes de error en las respuestas.</span><span class="sxs-lookup"><span data-stu-id="429c3-168">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="429c3-169">En el ejemplo siguiente se muestra el cuerpo de una respuesta 400 Solicitud incorrecta de una operación `test`:</span><span class="sxs-lookup"><span data-stu-id="429c3-169">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="429c3-170">Objetos dinámicos</span><span class="sxs-lookup"><span data-stu-id="429c3-170">Dynamic objects</span></span>

<span data-ttu-id="429c3-171">En el ejemplo de método de acción siguiente se muestra cómo aplicar una revisión a un objeto dinámico:</span><span class="sxs-lookup"><span data-stu-id="429c3-171">The following action method example shows how to apply a patch to a dynamic object:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="429c3-172">La operación add</span><span class="sxs-lookup"><span data-stu-id="429c3-172">The add operation</span></span>

* <span data-ttu-id="429c3-173">Si `path` apunta a un elemento de matriz: inserta un nuevo elemento delante del especificado por `path`.</span><span class="sxs-lookup"><span data-stu-id="429c3-173">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="429c3-174">Si `path` apunta a una propiedad: establece el valor de la propiedad.</span><span class="sxs-lookup"><span data-stu-id="429c3-174">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="429c3-175">Si `path` apunta a una ubicación que no existe:</span><span class="sxs-lookup"><span data-stu-id="429c3-175">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="429c3-176">Si el recurso para revisar es un objeto dinámico: agrega una propiedad.</span><span class="sxs-lookup"><span data-stu-id="429c3-176">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="429c3-177">Si el recurso para revisar es un objeto estático: la solicitud produce un error.</span><span class="sxs-lookup"><span data-stu-id="429c3-177">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="429c3-178">El siguiente documento de revisión de ejemplo establece el valor de `CustomerName` y agrega un objeto `Order` al final de la matriz `Orders`.</span><span class="sxs-lookup"><span data-stu-id="429c3-178">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="429c3-179">La operación remove</span><span class="sxs-lookup"><span data-stu-id="429c3-179">The remove operation</span></span>

* <span data-ttu-id="429c3-180">Si `path` apunta a un elemento de matriz: quita el elemento.</span><span class="sxs-lookup"><span data-stu-id="429c3-180">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="429c3-181">Si `path` apunta a una propiedad:</span><span class="sxs-lookup"><span data-stu-id="429c3-181">If `path` points to a property:</span></span>
  * <span data-ttu-id="429c3-182">Si el recurso para revisar es un objeto dinámico: quita la propiedad.</span><span class="sxs-lookup"><span data-stu-id="429c3-182">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="429c3-183">Si el recurso para revisar es un objeto estático:</span><span class="sxs-lookup"><span data-stu-id="429c3-183">If resource to patch is a static object:</span></span>
    * <span data-ttu-id="429c3-184">Si la propiedad acepta valores NULL: la establece en null.</span><span class="sxs-lookup"><span data-stu-id="429c3-184">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="429c3-185">Si la propiedad es distinta de null, la establece en `default<T>`.</span><span class="sxs-lookup"><span data-stu-id="429c3-185">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="429c3-186">En el documento de revisión de `CustomerName` ejemplo siguiente se establece en `Orders[0]`NULL y se elimina:</span><span class="sxs-lookup"><span data-stu-id="429c3-186">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="429c3-187">La operación replace</span><span class="sxs-lookup"><span data-stu-id="429c3-187">The replace operation</span></span>

<span data-ttu-id="429c3-188">Esta operación es funcionalmente igual que `remove` seguida de `add`.</span><span class="sxs-lookup"><span data-stu-id="429c3-188">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="429c3-189">En el siguiente documento de revisión de ejemplo se `CustomerName` establece el `Orders[0]`valor de y `Order` se reemplaza por un nuevo objeto:</span><span class="sxs-lookup"><span data-stu-id="429c3-189">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="429c3-190">La operación move</span><span class="sxs-lookup"><span data-stu-id="429c3-190">The move operation</span></span>

* <span data-ttu-id="429c3-191">Si `path` apunta a un elemento de matriz: copia el elemento `from` en la ubicación del elemento `path` y, luego, ejecuta una operación `remove` en el elemento `from`.</span><span class="sxs-lookup"><span data-stu-id="429c3-191">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="429c3-192">Si `path` apunta a una propiedad: copia el valor de la propiedad `from` en la propiedad `path` y, luego, ejecuta la operación `remove` en la propiedad `from`.</span><span class="sxs-lookup"><span data-stu-id="429c3-192">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="429c3-193">Si `path` apunta a una propiedad que no existe:</span><span class="sxs-lookup"><span data-stu-id="429c3-193">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="429c3-194">Si el recurso para revisar es un objeto estático: la solicitud produce un error.</span><span class="sxs-lookup"><span data-stu-id="429c3-194">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="429c3-195">Si el recurso para revisar es un objeto dinámico: copia la propiedad `from` en la ubicación indicada por `path` y, luego, ejecuta una operación `remove` en la propiedad `from`.</span><span class="sxs-lookup"><span data-stu-id="429c3-195">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="429c3-196">En el siguiente documento de revisión de ejemplo:</span><span class="sxs-lookup"><span data-stu-id="429c3-196">The following sample patch document:</span></span>

* <span data-ttu-id="429c3-197">Se copia el valor de `Orders[0].OrderName` en `CustomerName`.</span><span class="sxs-lookup"><span data-stu-id="429c3-197">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="429c3-198">Se establece `Orders[0].OrderName` en null.</span><span class="sxs-lookup"><span data-stu-id="429c3-198">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="429c3-199">Se mueve `Orders[1]` delante de `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="429c3-199">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="429c3-200">La operación copy</span><span class="sxs-lookup"><span data-stu-id="429c3-200">The copy operation</span></span>

<span data-ttu-id="429c3-201">Esta operación es funcionalmente igual que la operación `move` sin el paso `remove` final.</span><span class="sxs-lookup"><span data-stu-id="429c3-201">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="429c3-202">En el siguiente documento de revisión de ejemplo:</span><span class="sxs-lookup"><span data-stu-id="429c3-202">The following sample patch document:</span></span>

* <span data-ttu-id="429c3-203">Se copia el valor de `Orders[0].OrderName` en `CustomerName`.</span><span class="sxs-lookup"><span data-stu-id="429c3-203">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="429c3-204">Se inserta una copia de `Orders[1]` delante de `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="429c3-204">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="429c3-205">La operación test</span><span class="sxs-lookup"><span data-stu-id="429c3-205">The test operation</span></span>

<span data-ttu-id="429c3-206">Si el valor de la ubicación indicada por `path` es diferente del valor proporcionado en `value`, la solicitud produce un error.</span><span class="sxs-lookup"><span data-stu-id="429c3-206">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="429c3-207">En ese caso, la solicitud PATCH entera produce un error incluso si todas las demás operaciones del documento de revisión se realizan correctamente.</span><span class="sxs-lookup"><span data-stu-id="429c3-207">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="429c3-208">La operación `test` se usa habitualmente para impedir una actualización cuando hay un conflicto de simultaneidad.</span><span class="sxs-lookup"><span data-stu-id="429c3-208">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="429c3-209">El siguiente documento de revisión de ejemplo no tiene ningún efecto si el valor inicial de `CustomerName` es "John", porque la prueba produce un error:</span><span class="sxs-lookup"><span data-stu-id="429c3-209">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="429c3-210">Obtención del código</span><span class="sxs-lookup"><span data-stu-id="429c3-210">Get the code</span></span>

<span data-ttu-id="429c3-211">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples).</span><span class="sxs-lookup"><span data-stu-id="429c3-211">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples).</span></span> <span data-ttu-id="429c3-212">([Cómo descargar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="429c3-212">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="429c3-213">Para probar el ejemplo, ejecute la aplicación y envíe solicitudes HTTP con la configuración siguiente:</span><span class="sxs-lookup"><span data-stu-id="429c3-213">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="429c3-214">Dirección URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="429c3-214">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="429c3-215">Método HTTP: `PATCH`</span><span class="sxs-lookup"><span data-stu-id="429c3-215">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="429c3-216">Encabezado: `Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="429c3-216">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="429c3-217">Cuerpo: Copie y pegue uno de los ejemplos de documentos de revisión de JSON de la carpeta de proyecto *JSON* .</span><span class="sxs-lookup"><span data-stu-id="429c3-217">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="429c3-218">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="429c3-218">Additional resources</span></span>

* [<span data-ttu-id="429c3-219">Especificación del método PATCH de IETF RFC 5789</span><span class="sxs-lookup"><span data-stu-id="429c3-219">IETF RFC 5789 PATCH method specification</span></span>](https://tools.ietf.org/html/rfc5789)
* [<span data-ttu-id="429c3-220">Especificación del método JSON PATCH de IETF RFC 6902</span><span class="sxs-lookup"><span data-stu-id="429c3-220">IETF RFC 6902 JSON Patch specification</span></span>](https://tools.ietf.org/html/rfc6902)
* [<span data-ttu-id="429c3-221">Especificación del formato de ruta de acceso JSON Patch de IETF RFC 6901</span><span class="sxs-lookup"><span data-stu-id="429c3-221">IETF RFC 6901 JSON Patch path format spec</span></span>](https://tools.ietf.org/html/rfc6901)
* <span data-ttu-id="429c3-222">[Documentación de JSON Patch](https://jsonpatch.com/).</span><span class="sxs-lookup"><span data-stu-id="429c3-222">[JSON Patch documentation](https://jsonpatch.com/).</span></span> <span data-ttu-id="429c3-223">Incluye vínculos a recursos para crear documentos JSON Patch.</span><span class="sxs-lookup"><span data-stu-id="429c3-223">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="429c3-224">Código fuente de JSON Patch para ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="429c3-224">ASP.NET Core JSON Patch source code</span></span>](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="429c3-225">En este artículo se explica cómo administrar solicitudes JSON Patch en una API web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="429c3-225">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="patch-http-request-method"></a><span data-ttu-id="429c3-226">Método de solicitud HTTP PATCH</span><span class="sxs-lookup"><span data-stu-id="429c3-226">PATCH HTTP request method</span></span>

<span data-ttu-id="429c3-227">Los métodos PUT y [PATCH](https://tools.ietf.org/html/rfc5789) se usan para actualizar un recurso existente.</span><span class="sxs-lookup"><span data-stu-id="429c3-227">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="429c3-228">La diferencia entre ellos es que PUT reemplaza el recurso entero, mientras que PATCH especifica únicamente los cambios.</span><span class="sxs-lookup"><span data-stu-id="429c3-228">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="429c3-229">JSON Patch</span><span class="sxs-lookup"><span data-stu-id="429c3-229">JSON Patch</span></span>

<span data-ttu-id="429c3-230">[JSON Patch](https://tools.ietf.org/html/rfc6902) es un formato para especificar las actualizaciones que se aplicarán a un recurso.</span><span class="sxs-lookup"><span data-stu-id="429c3-230">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="429c3-231">Un documento JSON Patch tiene una matriz de *operaciones*.</span><span class="sxs-lookup"><span data-stu-id="429c3-231">A JSON Patch document has an array of *operations*.</span></span> <span data-ttu-id="429c3-232">Cada operación identifica un determinado tipo de cambio, como agregar un elemento de matriz o reemplazar un valor de propiedad.</span><span class="sxs-lookup"><span data-stu-id="429c3-232">Each operation identifies a particular type of change, such as add an array element or replace a property value.</span></span>

<span data-ttu-id="429c3-233">Por ejemplo, los documentos JSON siguientes representan un recurso, un documento de revisión JSON para el recurso y el resultado de aplicar las operaciones de revisión.</span><span class="sxs-lookup"><span data-stu-id="429c3-233">For example, the following JSON documents represent a resource, a JSON patch document for the resource, and the result of applying the patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="429c3-234">Ejemplo de recursos</span><span class="sxs-lookup"><span data-stu-id="429c3-234">Resource example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="429c3-235">Ejemplo de revisión de JSON</span><span class="sxs-lookup"><span data-stu-id="429c3-235">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="429c3-236">En el código JSON anterior:</span><span class="sxs-lookup"><span data-stu-id="429c3-236">In the preceding JSON:</span></span>

* <span data-ttu-id="429c3-237">La propiedad `op` indica el tipo de operación.</span><span class="sxs-lookup"><span data-stu-id="429c3-237">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="429c3-238">La propiedad `path` indica el elemento que se va a actualizar.</span><span class="sxs-lookup"><span data-stu-id="429c3-238">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="429c3-239">La propiedad `value` proporciona el nuevo valor.</span><span class="sxs-lookup"><span data-stu-id="429c3-239">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="429c3-240">Recurso después de la revisión</span><span class="sxs-lookup"><span data-stu-id="429c3-240">Resource after patch</span></span>

<span data-ttu-id="429c3-241">Este es el recurso después de aplicar el documento JSON Patch anterior:</span><span class="sxs-lookup"><span data-stu-id="429c3-241">Here's the resource after applying the preceding JSON Patch document:</span></span>

```json
{
  "customerName": "Barry",
  "orders": [
    {
      "orderName": "Order0",
      "orderType": null
    },
    {
      "orderName": "Order1",
      "orderType": null
    },
    {
      "orderName": "Order2",
      "orderType": null
    }
  ]
}
```

<span data-ttu-id="429c3-242">Los cambios realizados mediante la aplicación de un documento JSON Patch a un recurso son atómicos: si alguna operación de la lista produce un error, no se aplica ninguna operación de la lista.</span><span class="sxs-lookup"><span data-stu-id="429c3-242">The changes made by applying a JSON Patch document to a resource are atomic: if any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="429c3-243">Sintaxis de path</span><span class="sxs-lookup"><span data-stu-id="429c3-243">Path syntax</span></span>

<span data-ttu-id="429c3-244">La propiedad [path](https://tools.ietf.org/html/rfc6901) de un objeto de operación tiene barras inversas entre niveles.</span><span class="sxs-lookup"><span data-stu-id="429c3-244">The [path](https://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="429c3-245">Por ejemplo: `"/address/zipCode"`.</span><span class="sxs-lookup"><span data-stu-id="429c3-245">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="429c3-246">Para especificar elementos de matriz se usan índices de base cero.</span><span class="sxs-lookup"><span data-stu-id="429c3-246">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="429c3-247">El primer elemento de la matriz `addresses` estaría en `/addresses/0`.</span><span class="sxs-lookup"><span data-stu-id="429c3-247">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="429c3-248">Para usar `add` al final de una matriz, use un guion (-) en lugar de un número de índice: `/addresses/-`.</span><span class="sxs-lookup"><span data-stu-id="429c3-248">To `add` to the end of an array, use a hyphen (-) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="429c3-249">Operaciones</span><span class="sxs-lookup"><span data-stu-id="429c3-249">Operations</span></span>

<span data-ttu-id="429c3-250">En la siguiente tabla se muestran las operaciones admitidas, como se ha definido en la [especificación de JSON Patch](https://tools.ietf.org/html/rfc6902):</span><span class="sxs-lookup"><span data-stu-id="429c3-250">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="429c3-251">Operación</span><span class="sxs-lookup"><span data-stu-id="429c3-251">Operation</span></span>  | <span data-ttu-id="429c3-252">Notas</span><span class="sxs-lookup"><span data-stu-id="429c3-252">Notes</span></span> |
|-----------|--------------------------------|
| `add`     | <span data-ttu-id="429c3-253">Agrega un elemento de propiedad o matriz.</span><span class="sxs-lookup"><span data-stu-id="429c3-253">Add a property or array element.</span></span> <span data-ttu-id="429c3-254">Para la propiedad existente: establece el valor.</span><span class="sxs-lookup"><span data-stu-id="429c3-254">For existing property: set value.</span></span>|
| `remove`  | <span data-ttu-id="429c3-255">Quita un elemento de propiedad o matriz.</span><span class="sxs-lookup"><span data-stu-id="429c3-255">Remove a property or array element.</span></span> |
| `replace` | <span data-ttu-id="429c3-256">Lo mismo que `remove` seguido de `add` en la misma ubicación.</span><span class="sxs-lookup"><span data-stu-id="429c3-256">Same as `remove` followed by `add` at same location.</span></span> |
| `move`    | <span data-ttu-id="429c3-257">Lo mismo que `remove` desde el origen seguido de `add` al destino mediante el valor del origen.</span><span class="sxs-lookup"><span data-stu-id="429c3-257">Same as `remove` from source followed by `add` to destination using value from source.</span></span> |
| `copy`    | <span data-ttu-id="429c3-258">Lo mismo que `add` al destino mediante el valor del origen.</span><span class="sxs-lookup"><span data-stu-id="429c3-258">Same as `add` to destination using value from source.</span></span> |
| `test`    | <span data-ttu-id="429c3-259">Devuelve el código de estado correcto si el valor en `path` = al `value` proporcionado.</span><span class="sxs-lookup"><span data-stu-id="429c3-259">Return success status code if value at `path` = provided `value`.</span></span>|

## <a name="jsonpatch-in-aspnet-core"></a><span data-ttu-id="429c3-260">JsonPatch en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="429c3-260">JsonPatch in ASP.NET Core</span></span>

<span data-ttu-id="429c3-261">La implementación de ASP.NET Core de JSON Patch se proporciona en el paquete NuGet [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/).</span><span class="sxs-lookup"><span data-stu-id="429c3-261">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span> <span data-ttu-id="429c3-262">El paquete se incluye en el metapaquete [Microsoft.AspnetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="429c3-262">The package is included in the [Microsoft.AspnetCore.App](xref:fundamentals/metapackage-app) metapackage.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="429c3-263">Código del método de acción</span><span class="sxs-lookup"><span data-stu-id="429c3-263">Action method code</span></span>

<span data-ttu-id="429c3-264">En un controlador de API, un método de acción para JSON Patch:</span><span class="sxs-lookup"><span data-stu-id="429c3-264">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="429c3-265">Se anota con el atributo `HttpPatch`.</span><span class="sxs-lookup"><span data-stu-id="429c3-265">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="429c3-266">Acepta `JsonPatchDocument<T>`, normalmente con `[FromBody]`.</span><span class="sxs-lookup"><span data-stu-id="429c3-266">Accepts a `JsonPatchDocument<T>`, typically with `[FromBody]`.</span></span>
* <span data-ttu-id="429c3-267">Llama a `ApplyTo` en el documento de revisión para aplicar los cambios.</span><span class="sxs-lookup"><span data-stu-id="429c3-267">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="429c3-268">Este es un ejemplo:</span><span class="sxs-lookup"><span data-stu-id="429c3-268">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="429c3-269">Este código de la aplicación de ejemplo funciona con el siguiente modelo `Customer`.</span><span class="sxs-lookup"><span data-stu-id="429c3-269">This code from the sample app works with the following `Customer` model.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="429c3-270">El método de acción de ejemplo:</span><span class="sxs-lookup"><span data-stu-id="429c3-270">The sample action method:</span></span>

* <span data-ttu-id="429c3-271">Construye un objeto `Customer`.</span><span class="sxs-lookup"><span data-stu-id="429c3-271">Constructs a `Customer`.</span></span>
* <span data-ttu-id="429c3-272">Aplica la revisión.</span><span class="sxs-lookup"><span data-stu-id="429c3-272">Applies the patch.</span></span>
* <span data-ttu-id="429c3-273">Devuelve el resultado en el cuerpo de la respuesta.</span><span class="sxs-lookup"><span data-stu-id="429c3-273">Returns the result in the body of the response.</span></span>

 <span data-ttu-id="429c3-274">En una aplicación real, el código recuperaría los datos de un almacén como una base de datos y actualizaría la base de datos después de aplicar la revisión.</span><span class="sxs-lookup"><span data-stu-id="429c3-274">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="429c3-275">Estado del modelo</span><span class="sxs-lookup"><span data-stu-id="429c3-275">Model state</span></span>

<span data-ttu-id="429c3-276">En el ejemplo anterior del método de acción, se llama a una sobrecarga de `ApplyTo` que toma el estado del modelo como uno de sus parámetros.</span><span class="sxs-lookup"><span data-stu-id="429c3-276">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="429c3-277">Con esta opción, puede obtener mensajes de error en las respuestas.</span><span class="sxs-lookup"><span data-stu-id="429c3-277">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="429c3-278">En el ejemplo siguiente se muestra el cuerpo de una respuesta 400 Solicitud incorrecta de una operación `test`:</span><span class="sxs-lookup"><span data-stu-id="429c3-278">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="429c3-279">Objetos dinámicos</span><span class="sxs-lookup"><span data-stu-id="429c3-279">Dynamic objects</span></span>

<span data-ttu-id="429c3-280">En el ejemplo siguiente de método de acción se muestra cómo aplicar una revisión a un objeto dinámico.</span><span class="sxs-lookup"><span data-stu-id="429c3-280">The following action method example shows how to apply a patch to a dynamic object.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="429c3-281">La operación add</span><span class="sxs-lookup"><span data-stu-id="429c3-281">The add operation</span></span>

* <span data-ttu-id="429c3-282">Si `path` apunta a un elemento de matriz: inserta un nuevo elemento delante del especificado por `path`.</span><span class="sxs-lookup"><span data-stu-id="429c3-282">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="429c3-283">Si `path` apunta a una propiedad: establece el valor de la propiedad.</span><span class="sxs-lookup"><span data-stu-id="429c3-283">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="429c3-284">Si `path` apunta a una ubicación que no existe:</span><span class="sxs-lookup"><span data-stu-id="429c3-284">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="429c3-285">Si el recurso para revisar es un objeto dinámico: agrega una propiedad.</span><span class="sxs-lookup"><span data-stu-id="429c3-285">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="429c3-286">Si el recurso para revisar es un objeto estático: la solicitud produce un error.</span><span class="sxs-lookup"><span data-stu-id="429c3-286">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="429c3-287">El siguiente documento de revisión de ejemplo establece el valor de `CustomerName` y agrega un objeto `Order` al final de la matriz `Orders`.</span><span class="sxs-lookup"><span data-stu-id="429c3-287">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="429c3-288">La operación remove</span><span class="sxs-lookup"><span data-stu-id="429c3-288">The remove operation</span></span>

* <span data-ttu-id="429c3-289">Si `path` apunta a un elemento de matriz: quita el elemento.</span><span class="sxs-lookup"><span data-stu-id="429c3-289">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="429c3-290">Si `path` apunta a una propiedad:</span><span class="sxs-lookup"><span data-stu-id="429c3-290">If `path` points to a property:</span></span>
  * <span data-ttu-id="429c3-291">Si el recurso para revisar es un objeto dinámico: quita la propiedad.</span><span class="sxs-lookup"><span data-stu-id="429c3-291">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="429c3-292">Si el recurso para revisar es un objeto estático:</span><span class="sxs-lookup"><span data-stu-id="429c3-292">If resource to patch is a static object:</span></span>
    * <span data-ttu-id="429c3-293">Si la propiedad acepta valores NULL: la establece en null.</span><span class="sxs-lookup"><span data-stu-id="429c3-293">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="429c3-294">Si la propiedad es distinta de null, la establece en `default<T>`.</span><span class="sxs-lookup"><span data-stu-id="429c3-294">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="429c3-295">En el siguiente documento de revisión de ejemplo, se establece `CustomerName` en null y se elimina `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="429c3-295">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="429c3-296">La operación replace</span><span class="sxs-lookup"><span data-stu-id="429c3-296">The replace operation</span></span>

<span data-ttu-id="429c3-297">Esta operación es funcionalmente igual que `remove` seguida de `add`.</span><span class="sxs-lookup"><span data-stu-id="429c3-297">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="429c3-298">En el siguiente documento de revisión de ejemplo se establece el valor de `CustomerName` y se reemplaza `Orders[0]` por un nuevo objeto `Order`.</span><span class="sxs-lookup"><span data-stu-id="429c3-298">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="429c3-299">La operación move</span><span class="sxs-lookup"><span data-stu-id="429c3-299">The move operation</span></span>

* <span data-ttu-id="429c3-300">Si `path` apunta a un elemento de matriz: copia el elemento `from` en la ubicación del elemento `path` y, luego, ejecuta una operación `remove` en el elemento `from`.</span><span class="sxs-lookup"><span data-stu-id="429c3-300">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="429c3-301">Si `path` apunta a una propiedad: copia el valor de la propiedad `from` en la propiedad `path` y, luego, ejecuta la operación `remove` en la propiedad `from`.</span><span class="sxs-lookup"><span data-stu-id="429c3-301">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="429c3-302">Si `path` apunta a una propiedad que no existe:</span><span class="sxs-lookup"><span data-stu-id="429c3-302">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="429c3-303">Si el recurso para revisar es un objeto estático: la solicitud produce un error.</span><span class="sxs-lookup"><span data-stu-id="429c3-303">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="429c3-304">Si el recurso para revisar es un objeto dinámico: copia la propiedad `from` en la ubicación indicada por `path` y, luego, ejecuta una operación `remove` en la propiedad `from`.</span><span class="sxs-lookup"><span data-stu-id="429c3-304">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="429c3-305">En el siguiente documento de revisión de ejemplo:</span><span class="sxs-lookup"><span data-stu-id="429c3-305">The following sample patch document:</span></span>

* <span data-ttu-id="429c3-306">Se copia el valor de `Orders[0].OrderName` en `CustomerName`.</span><span class="sxs-lookup"><span data-stu-id="429c3-306">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="429c3-307">Se establece `Orders[0].OrderName` en null.</span><span class="sxs-lookup"><span data-stu-id="429c3-307">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="429c3-308">Se mueve `Orders[1]` delante de `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="429c3-308">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="429c3-309">La operación copy</span><span class="sxs-lookup"><span data-stu-id="429c3-309">The copy operation</span></span>

<span data-ttu-id="429c3-310">Esta operación es funcionalmente igual que la operación `move` sin el paso `remove` final.</span><span class="sxs-lookup"><span data-stu-id="429c3-310">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="429c3-311">En el siguiente documento de revisión de ejemplo:</span><span class="sxs-lookup"><span data-stu-id="429c3-311">The following sample patch document:</span></span>

* <span data-ttu-id="429c3-312">Se copia el valor de `Orders[0].OrderName` en `CustomerName`.</span><span class="sxs-lookup"><span data-stu-id="429c3-312">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="429c3-313">Se inserta una copia de `Orders[1]` delante de `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="429c3-313">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="429c3-314">La operación test</span><span class="sxs-lookup"><span data-stu-id="429c3-314">The test operation</span></span>

<span data-ttu-id="429c3-315">Si el valor de la ubicación indicada por `path` es diferente del valor proporcionado en `value`, la solicitud produce un error.</span><span class="sxs-lookup"><span data-stu-id="429c3-315">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="429c3-316">En ese caso, la solicitud PATCH entera produce un error incluso si todas las demás operaciones del documento de revisión se realizan correctamente.</span><span class="sxs-lookup"><span data-stu-id="429c3-316">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="429c3-317">La operación `test` se usa habitualmente para impedir una actualización cuando hay un conflicto de simultaneidad.</span><span class="sxs-lookup"><span data-stu-id="429c3-317">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="429c3-318">El siguiente documento de revisión de ejemplo no tiene ningún efecto si el valor inicial de `CustomerName` es "John", porque la prueba produce un error:</span><span class="sxs-lookup"><span data-stu-id="429c3-318">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="429c3-319">Obtención del código</span><span class="sxs-lookup"><span data-stu-id="429c3-319">Get the code</span></span>

<span data-ttu-id="429c3-320">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2).</span><span class="sxs-lookup"><span data-stu-id="429c3-320">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2).</span></span> <span data-ttu-id="429c3-321">([Cómo descargar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="429c3-321">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="429c3-322">Para probar el ejemplo, ejecute la aplicación y envíe solicitudes HTTP con la configuración siguiente:</span><span class="sxs-lookup"><span data-stu-id="429c3-322">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="429c3-323">Dirección URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="429c3-323">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="429c3-324">Método HTTP: `PATCH`</span><span class="sxs-lookup"><span data-stu-id="429c3-324">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="429c3-325">Encabezado: `Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="429c3-325">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="429c3-326">Cuerpo: Copie y pegue uno de los ejemplos de documentos de revisión de JSON de la carpeta de proyecto *JSON* .</span><span class="sxs-lookup"><span data-stu-id="429c3-326">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="429c3-327">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="429c3-327">Additional resources</span></span>

* [<span data-ttu-id="429c3-328">Especificación del método PATCH de IETF RFC 5789</span><span class="sxs-lookup"><span data-stu-id="429c3-328">IETF RFC 5789 PATCH method specification</span></span>](https://tools.ietf.org/html/rfc5789)
* [<span data-ttu-id="429c3-329">Especificación del método JSON PATCH de IETF RFC 6902</span><span class="sxs-lookup"><span data-stu-id="429c3-329">IETF RFC 6902 JSON Patch specification</span></span>](https://tools.ietf.org/html/rfc6902)
* [<span data-ttu-id="429c3-330">Especificación del formato de ruta de acceso JSON Patch de IETF RFC 6901</span><span class="sxs-lookup"><span data-stu-id="429c3-330">IETF RFC 6901 JSON Patch path format spec</span></span>](https://tools.ietf.org/html/rfc6901)
* <span data-ttu-id="429c3-331">[Documentación de JSON Patch](https://jsonpatch.com/).</span><span class="sxs-lookup"><span data-stu-id="429c3-331">[JSON Patch documentation](https://jsonpatch.com/).</span></span> <span data-ttu-id="429c3-332">Incluye vínculos a recursos para crear documentos JSON Patch.</span><span class="sxs-lookup"><span data-stu-id="429c3-332">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="429c3-333">Código fuente de JSON Patch para ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="429c3-333">ASP.NET Core JSON Patch source code</span></span>](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end
