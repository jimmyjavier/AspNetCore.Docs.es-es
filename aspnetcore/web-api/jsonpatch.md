---
<span data-ttu-id="9b7be-101">Título: autor: Descripción: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="9b7be-101">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9b7be-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9b7be-102">'Blazor'</span></span>
- <span data-ttu-id="9b7be-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9b7be-103">'Identity'</span></span>
- <span data-ttu-id="9b7be-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9b7be-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="9b7be-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9b7be-105">'Razor'</span></span>
- <span data-ttu-id="9b7be-106">SignalRUID ' ':</span><span class="sxs-lookup"><span data-stu-id="9b7be-106">'SignalR' uid:</span></span> 

---

# <a name="jsonpatch-in-aspnet-core-web-api"></a><span data-ttu-id="9b7be-107">JsonPatch en la API web de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9b7be-107">JsonPatch in ASP.NET Core web API</span></span>

<span data-ttu-id="9b7be-108">Por [Tom Dykstra](https://github.com/tdykstra) y [Kirk Larkin](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="9b7be-108">By [Tom Dykstra](https://github.com/tdykstra) and [Kirk Larkin](https://github.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9b7be-109">En este artículo se explica cómo administrar solicitudes JSON Patch en una API web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9b7be-109">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="package-installation"></a><span data-ttu-id="9b7be-110">Instalación del paquete</span><span class="sxs-lookup"><span data-stu-id="9b7be-110">Package installation</span></span>

<span data-ttu-id="9b7be-111">Para habilitar la compatibilidad con la revisión de JSON en la aplicación, complete los pasos siguientes:</span><span class="sxs-lookup"><span data-stu-id="9b7be-111">To enable JSON Patch support in your app, complete the following steps:</span></span>

1. <span data-ttu-id="9b7be-112">Instale el [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) paquete NuGet.</span><span class="sxs-lookup"><span data-stu-id="9b7be-112">Install the [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package.</span></span>
1. <span data-ttu-id="9b7be-113">Actualice el método del proyecto `Startup.ConfigureServices` para llamar a <xref:Microsoft.Extensions.DependencyInjection.NewtonsoftJsonMvcBuilderExtensions.AddNewtonsoftJson*> .</span><span class="sxs-lookup"><span data-stu-id="9b7be-113">Update the project's `Startup.ConfigureServices` method to call <xref:Microsoft.Extensions.DependencyInjection.NewtonsoftJsonMvcBuilderExtensions.AddNewtonsoftJson*>.</span></span> <span data-ttu-id="9b7be-114">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="9b7be-114">For example:</span></span>

    ```csharp
    services
        .AddControllersWithViews()
        .AddNewtonsoftJson();
    ```

<span data-ttu-id="9b7be-115">`AddNewtonsoftJson` es compatible con los métodos de registro del servicio MVC:</span><span class="sxs-lookup"><span data-stu-id="9b7be-115">`AddNewtonsoftJson` is compatible with the MVC service registration methods:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages*>
* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews*>
* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers*>

## <a name="json-patch-addnewtonsoftjson-and-systemtextjson"></a><span data-ttu-id="9b7be-116">Revisión de JSON, AddNewtonsoftJson y System. Text. JSON</span><span class="sxs-lookup"><span data-stu-id="9b7be-116">JSON Patch, AddNewtonsoftJson, and System.Text.Json</span></span>

<span data-ttu-id="9b7be-117">`AddNewtonsoftJson`reemplaza los `System.Text.Json` formateadores de entrada y salida basados en que se usan para dar formato a **todo** el contenido JSON.</span><span class="sxs-lookup"><span data-stu-id="9b7be-117">`AddNewtonsoftJson` replaces the `System.Text.Json`-based input and output formatters used for formatting **all** JSON content.</span></span> <span data-ttu-id="9b7be-118">Para agregar compatibilidad con la revisión de JSON mediante `Newtonsoft.Json` , sin modificar los otros formateadores, actualice el método del proyecto `Startup.ConfigureServices` como se indica a continuación:</span><span class="sxs-lookup"><span data-stu-id="9b7be-118">To add support for JSON Patch using `Newtonsoft.Json`, while leaving the other formatters unchanged, update the project's `Startup.ConfigureServices` method as follows:</span></span>

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet)]

<span data-ttu-id="9b7be-119">El código anterior requiere el `Microsoft.AspNetCore.Mvc.NewtonsoftJson` paquete y las siguientes `using` instrucciones:</span><span class="sxs-lookup"><span data-stu-id="9b7be-119">The preceding code requires the `Microsoft.AspNetCore.Mvc.NewtonsoftJson` package and the following `using` statements:</span></span>

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet1)]

## <a name="patch-http-request-method"></a><span data-ttu-id="9b7be-120">Método de solicitud HTTP PATCH</span><span class="sxs-lookup"><span data-stu-id="9b7be-120">PATCH HTTP request method</span></span>

<span data-ttu-id="9b7be-121">Los métodos PUT y [PATCH](https://tools.ietf.org/html/rfc5789) se usan para actualizar un recurso existente.</span><span class="sxs-lookup"><span data-stu-id="9b7be-121">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="9b7be-122">La diferencia entre ellos es que PUT reemplaza el recurso entero, mientras que PATCH especifica únicamente los cambios.</span><span class="sxs-lookup"><span data-stu-id="9b7be-122">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="9b7be-123">JSON Patch</span><span class="sxs-lookup"><span data-stu-id="9b7be-123">JSON Patch</span></span>

<span data-ttu-id="9b7be-124">[JSON Patch](https://tools.ietf.org/html/rfc6902) es un formato para especificar las actualizaciones que se aplicarán a un recurso.</span><span class="sxs-lookup"><span data-stu-id="9b7be-124">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="9b7be-125">Un documento JSON Patch tiene una matriz de *operaciones*.</span><span class="sxs-lookup"><span data-stu-id="9b7be-125">A JSON Patch document has an array of *operations*.</span></span> <span data-ttu-id="9b7be-126">Cada operación identifica un tipo de cambio determinado.</span><span class="sxs-lookup"><span data-stu-id="9b7be-126">Each operation identifies a particular type of change.</span></span> <span data-ttu-id="9b7be-127">Algunos ejemplos de estos cambios incluyen agregar un elemento de matriz o reemplazar un valor de propiedad.</span><span class="sxs-lookup"><span data-stu-id="9b7be-127">Examples of such changes include adding an array element or replacing a property value.</span></span>

<span data-ttu-id="9b7be-128">Por ejemplo, los siguientes documentos JSON representan un recurso, un documento de revisión JSON para el recurso y el resultado de aplicar las operaciones patch.</span><span class="sxs-lookup"><span data-stu-id="9b7be-128">For example, the following JSON documents represent a resource, a JSON Patch document for the resource, and the result of applying the Patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="9b7be-129">Ejemplo de recursos</span><span class="sxs-lookup"><span data-stu-id="9b7be-129">Resource example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="9b7be-130">Ejemplo de revisión de JSON</span><span class="sxs-lookup"><span data-stu-id="9b7be-130">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="9b7be-131">En el código JSON anterior:</span><span class="sxs-lookup"><span data-stu-id="9b7be-131">In the preceding JSON:</span></span>

* <span data-ttu-id="9b7be-132">La propiedad `op` indica el tipo de operación.</span><span class="sxs-lookup"><span data-stu-id="9b7be-132">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="9b7be-133">La propiedad `path` indica el elemento que se va a actualizar.</span><span class="sxs-lookup"><span data-stu-id="9b7be-133">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="9b7be-134">La propiedad `value` proporciona el nuevo valor.</span><span class="sxs-lookup"><span data-stu-id="9b7be-134">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="9b7be-135">Recurso después de la revisión</span><span class="sxs-lookup"><span data-stu-id="9b7be-135">Resource after patch</span></span>

<span data-ttu-id="9b7be-136">Este es el recurso después de aplicar el documento JSON Patch anterior:</span><span class="sxs-lookup"><span data-stu-id="9b7be-136">Here's the resource after applying the preceding JSON Patch document:</span></span>

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

<span data-ttu-id="9b7be-137">Los cambios realizados al aplicar un documento de revisión de JSON a un recurso son atómicos.</span><span class="sxs-lookup"><span data-stu-id="9b7be-137">The changes made by applying a JSON Patch document to a resource are atomic.</span></span> <span data-ttu-id="9b7be-138">Si se produce un error en cualquier operación de la lista, no se aplica ninguna operación en la lista.</span><span class="sxs-lookup"><span data-stu-id="9b7be-138">If any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="9b7be-139">Sintaxis de path</span><span class="sxs-lookup"><span data-stu-id="9b7be-139">Path syntax</span></span>

<span data-ttu-id="9b7be-140">La propiedad [path](https://tools.ietf.org/html/rfc6901) de un objeto de operación tiene barras inversas entre niveles.</span><span class="sxs-lookup"><span data-stu-id="9b7be-140">The [path](https://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="9b7be-141">Por ejemplo, `"/address/zipCode"`.</span><span class="sxs-lookup"><span data-stu-id="9b7be-141">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="9b7be-142">Para especificar elementos de matriz se usan índices de base cero.</span><span class="sxs-lookup"><span data-stu-id="9b7be-142">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="9b7be-143">El primer elemento de la matriz `addresses` estaría en `/addresses/0`.</span><span class="sxs-lookup"><span data-stu-id="9b7be-143">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="9b7be-144">Hasta `add` el final de una matriz, use un guion ( `-` ) en lugar de un número de índice: `/addresses/-` .</span><span class="sxs-lookup"><span data-stu-id="9b7be-144">To `add` to the end of an array, use a hyphen (`-`) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="9b7be-145">Operaciones</span><span class="sxs-lookup"><span data-stu-id="9b7be-145">Operations</span></span>

<span data-ttu-id="9b7be-146">En la siguiente tabla se muestran las operaciones admitidas, como se ha definido en la [especificación de JSON Patch](https://tools.ietf.org/html/rfc6902):</span><span class="sxs-lookup"><span data-stu-id="9b7be-146">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="9b7be-147">Operación</span><span class="sxs-lookup"><span data-stu-id="9b7be-147">Operation</span></span>  | <span data-ttu-id="9b7be-148">Notas</span><span class="sxs-lookup"><span data-stu-id="9b7be-148">Notes</span></span> |
|---
<span data-ttu-id="9b7be-149">Título: autor: Descripción: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="9b7be-149">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9b7be-150">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9b7be-150">'Blazor'</span></span>
- <span data-ttu-id="9b7be-151">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9b7be-151">'Identity'</span></span>
- <span data-ttu-id="9b7be-152">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9b7be-152">'Let's Encrypt'</span></span>
- <span data-ttu-id="9b7be-153">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9b7be-153">'Razor'</span></span>
- <span data-ttu-id="9b7be-154">SignalRUID ' ':</span><span class="sxs-lookup"><span data-stu-id="9b7be-154">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9b7be-155">Título: autor: Descripción: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="9b7be-155">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9b7be-156">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9b7be-156">'Blazor'</span></span>
- <span data-ttu-id="9b7be-157">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9b7be-157">'Identity'</span></span>
- <span data-ttu-id="9b7be-158">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9b7be-158">'Let's Encrypt'</span></span>
- <span data-ttu-id="9b7be-159">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9b7be-159">'Razor'</span></span>
- <span data-ttu-id="9b7be-160">SignalRUID ' ':</span><span class="sxs-lookup"><span data-stu-id="9b7be-160">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9b7be-161">Título: autor: Descripción: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="9b7be-161">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9b7be-162">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9b7be-162">'Blazor'</span></span>
- <span data-ttu-id="9b7be-163">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9b7be-163">'Identity'</span></span>
- <span data-ttu-id="9b7be-164">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9b7be-164">'Let's Encrypt'</span></span>
- <span data-ttu-id="9b7be-165">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9b7be-165">'Razor'</span></span>
- <span data-ttu-id="9b7be-166">SignalRUID ' ':</span><span class="sxs-lookup"><span data-stu-id="9b7be-166">'SignalR' uid:</span></span> 

<span data-ttu-id="9b7be-167">------|---
Título: autor: Descripción: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="9b7be-167">------|---
title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9b7be-168">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9b7be-168">'Blazor'</span></span>
- <span data-ttu-id="9b7be-169">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9b7be-169">'Identity'</span></span>
- <span data-ttu-id="9b7be-170">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9b7be-170">'Let's Encrypt'</span></span>
- <span data-ttu-id="9b7be-171">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9b7be-171">'Razor'</span></span>
- <span data-ttu-id="9b7be-172">SignalRUID ' ':</span><span class="sxs-lookup"><span data-stu-id="9b7be-172">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9b7be-173">Título: autor: Descripción: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="9b7be-173">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9b7be-174">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9b7be-174">'Blazor'</span></span>
- <span data-ttu-id="9b7be-175">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9b7be-175">'Identity'</span></span>
- <span data-ttu-id="9b7be-176">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9b7be-176">'Let's Encrypt'</span></span>
- <span data-ttu-id="9b7be-177">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9b7be-177">'Razor'</span></span>
- <span data-ttu-id="9b7be-178">SignalRUID ' ':</span><span class="sxs-lookup"><span data-stu-id="9b7be-178">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9b7be-179">Título: autor: Descripción: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="9b7be-179">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9b7be-180">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9b7be-180">'Blazor'</span></span>
- <span data-ttu-id="9b7be-181">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9b7be-181">'Identity'</span></span>
- <span data-ttu-id="9b7be-182">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9b7be-182">'Let's Encrypt'</span></span>
- <span data-ttu-id="9b7be-183">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9b7be-183">'Razor'</span></span>
- <span data-ttu-id="9b7be-184">SignalRUID ' ':</span><span class="sxs-lookup"><span data-stu-id="9b7be-184">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9b7be-185">Título: autor: Descripción: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="9b7be-185">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9b7be-186">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9b7be-186">'Blazor'</span></span>
- <span data-ttu-id="9b7be-187">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9b7be-187">'Identity'</span></span>
- <span data-ttu-id="9b7be-188">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9b7be-188">'Let's Encrypt'</span></span>
- <span data-ttu-id="9b7be-189">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9b7be-189">'Razor'</span></span>
- <span data-ttu-id="9b7be-190">SignalRUID ' ':</span><span class="sxs-lookup"><span data-stu-id="9b7be-190">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9b7be-191">Título: autor: Descripción: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="9b7be-191">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9b7be-192">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9b7be-192">'Blazor'</span></span>
- <span data-ttu-id="9b7be-193">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9b7be-193">'Identity'</span></span>
- <span data-ttu-id="9b7be-194">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9b7be-194">'Let's Encrypt'</span></span>
- <span data-ttu-id="9b7be-195">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9b7be-195">'Razor'</span></span>
- <span data-ttu-id="9b7be-196">SignalRUID ' ':</span><span class="sxs-lookup"><span data-stu-id="9b7be-196">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9b7be-197">Título: autor: Descripción: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="9b7be-197">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9b7be-198">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9b7be-198">'Blazor'</span></span>
- <span data-ttu-id="9b7be-199">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9b7be-199">'Identity'</span></span>
- <span data-ttu-id="9b7be-200">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9b7be-200">'Let's Encrypt'</span></span>
- <span data-ttu-id="9b7be-201">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9b7be-201">'Razor'</span></span>
- <span data-ttu-id="9b7be-202">SignalRUID ' ':</span><span class="sxs-lookup"><span data-stu-id="9b7be-202">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9b7be-203">Título: autor: Descripción: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="9b7be-203">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9b7be-204">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9b7be-204">'Blazor'</span></span>
- <span data-ttu-id="9b7be-205">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9b7be-205">'Identity'</span></span>
- <span data-ttu-id="9b7be-206">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9b7be-206">'Let's Encrypt'</span></span>
- <span data-ttu-id="9b7be-207">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9b7be-207">'Razor'</span></span>
- <span data-ttu-id="9b7be-208">SignalRUID ' ':</span><span class="sxs-lookup"><span data-stu-id="9b7be-208">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9b7be-209">Título: autor: Descripción: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="9b7be-209">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9b7be-210">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9b7be-210">'Blazor'</span></span>
- <span data-ttu-id="9b7be-211">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9b7be-211">'Identity'</span></span>
- <span data-ttu-id="9b7be-212">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9b7be-212">'Let's Encrypt'</span></span>
- <span data-ttu-id="9b7be-213">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9b7be-213">'Razor'</span></span>
- <span data-ttu-id="9b7be-214">SignalRUID ' ':</span><span class="sxs-lookup"><span data-stu-id="9b7be-214">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9b7be-215">Título: autor: Descripción: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="9b7be-215">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9b7be-216">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9b7be-216">'Blazor'</span></span>
- <span data-ttu-id="9b7be-217">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9b7be-217">'Identity'</span></span>
- <span data-ttu-id="9b7be-218">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9b7be-218">'Let's Encrypt'</span></span>
- <span data-ttu-id="9b7be-219">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9b7be-219">'Razor'</span></span>
- <span data-ttu-id="9b7be-220">SignalRUID ' ':</span><span class="sxs-lookup"><span data-stu-id="9b7be-220">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9b7be-221">Título: autor: Descripción: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="9b7be-221">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9b7be-222">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9b7be-222">'Blazor'</span></span>
- <span data-ttu-id="9b7be-223">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9b7be-223">'Identity'</span></span>
- <span data-ttu-id="9b7be-224">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9b7be-224">'Let's Encrypt'</span></span>
- <span data-ttu-id="9b7be-225">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9b7be-225">'Razor'</span></span>
- <span data-ttu-id="9b7be-226">SignalRUID ' ':</span><span class="sxs-lookup"><span data-stu-id="9b7be-226">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9b7be-227">Título: autor: Descripción: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="9b7be-227">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9b7be-228">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9b7be-228">'Blazor'</span></span>
- <span data-ttu-id="9b7be-229">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9b7be-229">'Identity'</span></span>
- <span data-ttu-id="9b7be-230">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9b7be-230">'Let's Encrypt'</span></span>
- <span data-ttu-id="9b7be-231">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9b7be-231">'Razor'</span></span>
- <span data-ttu-id="9b7be-232">SignalRUID ' ':</span><span class="sxs-lookup"><span data-stu-id="9b7be-232">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9b7be-233">Título: autor: Descripción: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="9b7be-233">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9b7be-234">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9b7be-234">'Blazor'</span></span>
- <span data-ttu-id="9b7be-235">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9b7be-235">'Identity'</span></span>
- <span data-ttu-id="9b7be-236">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9b7be-236">'Let's Encrypt'</span></span>
- <span data-ttu-id="9b7be-237">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9b7be-237">'Razor'</span></span>
- <span data-ttu-id="9b7be-238">SignalRUID ' ':</span><span class="sxs-lookup"><span data-stu-id="9b7be-238">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9b7be-239">Título: autor: Descripción: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="9b7be-239">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9b7be-240">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9b7be-240">'Blazor'</span></span>
- <span data-ttu-id="9b7be-241">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9b7be-241">'Identity'</span></span>
- <span data-ttu-id="9b7be-242">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9b7be-242">'Let's Encrypt'</span></span>
- <span data-ttu-id="9b7be-243">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9b7be-243">'Razor'</span></span>
- <span data-ttu-id="9b7be-244">SignalRUID ' ':</span><span class="sxs-lookup"><span data-stu-id="9b7be-244">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9b7be-245">Título: autor: Descripción: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="9b7be-245">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9b7be-246">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9b7be-246">'Blazor'</span></span>
- <span data-ttu-id="9b7be-247">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9b7be-247">'Identity'</span></span>
- <span data-ttu-id="9b7be-248">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9b7be-248">'Let's Encrypt'</span></span>
- <span data-ttu-id="9b7be-249">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9b7be-249">'Razor'</span></span>
- <span data-ttu-id="9b7be-250">SignalRUID ' ':</span><span class="sxs-lookup"><span data-stu-id="9b7be-250">'SignalR' uid:</span></span> 

<span data-ttu-id="9b7be-251">----------------| | `add`     | Agregue una propiedad o un elemento de matriz.</span><span class="sxs-lookup"><span data-stu-id="9b7be-251">----------------| | `add`     | Add a property or array element.</span></span> <span data-ttu-id="9b7be-252">Para la propiedad existente: establecer valor. | | `remove`  | Quite una propiedad o un elemento de matriz.</span><span class="sxs-lookup"><span data-stu-id="9b7be-252">For existing property: set value.| | `remove`  | Remove a property or array element.</span></span> <span data-ttu-id="9b7be-253">| | `replace` | Igual `remove` a seguido de `add` en la misma ubicación.</span><span class="sxs-lookup"><span data-stu-id="9b7be-253">| | `replace` | Same as `remove` followed by `add` at same location.</span></span> <span data-ttu-id="9b7be-254">| | `move`    | Igual que `remove` desde el origen seguido de `add` hasta el destino usando el valor del origen.</span><span class="sxs-lookup"><span data-stu-id="9b7be-254">| | `move`    | Same as `remove` from source followed by `add` to destination using value from source.</span></span> <span data-ttu-id="9b7be-255">| | `copy`    | Igual que el `add` destino mediante el valor del origen.</span><span class="sxs-lookup"><span data-stu-id="9b7be-255">| | `copy`    | Same as `add` to destination using value from source.</span></span> <span data-ttu-id="9b7be-256">| | `test`    | Devuelve el código de estado correcto si el valor se `path` proporciona en = `value` . |</span><span class="sxs-lookup"><span data-stu-id="9b7be-256">| | `test`    | Return success status code if value at `path` = provided `value`.|</span></span>

## <a name="json-patch-in-aspnet-core"></a><span data-ttu-id="9b7be-257">Revisión de JSON en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9b7be-257">JSON Patch in ASP.NET Core</span></span>

<span data-ttu-id="9b7be-258">La implementación de ASP.NET Core de JSON Patch se proporciona en el paquete NuGet [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/).</span><span class="sxs-lookup"><span data-stu-id="9b7be-258">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="9b7be-259">Código del método de acción</span><span class="sxs-lookup"><span data-stu-id="9b7be-259">Action method code</span></span>

<span data-ttu-id="9b7be-260">En un controlador de API, un método de acción para JSON Patch:</span><span class="sxs-lookup"><span data-stu-id="9b7be-260">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="9b7be-261">Se anota con el atributo `HttpPatch`.</span><span class="sxs-lookup"><span data-stu-id="9b7be-261">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="9b7be-262">Acepta `JsonPatchDocument<T>`, normalmente con `[FromBody]`.</span><span class="sxs-lookup"><span data-stu-id="9b7be-262">Accepts a `JsonPatchDocument<T>`, typically with `[FromBody]`.</span></span>
* <span data-ttu-id="9b7be-263">Llama a `ApplyTo` en el documento de revisión para aplicar los cambios.</span><span class="sxs-lookup"><span data-stu-id="9b7be-263">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="9b7be-264">Este es un ejemplo:</span><span class="sxs-lookup"><span data-stu-id="9b7be-264">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="9b7be-265">Este código de la aplicación de ejemplo funciona con el siguiente `Customer` modelo:</span><span class="sxs-lookup"><span data-stu-id="9b7be-265">This code from the sample app works with the following `Customer` model:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="9b7be-266">El método de acción de ejemplo:</span><span class="sxs-lookup"><span data-stu-id="9b7be-266">The sample action method:</span></span>

* <span data-ttu-id="9b7be-267">Construye un objeto `Customer`.</span><span class="sxs-lookup"><span data-stu-id="9b7be-267">Constructs a `Customer`.</span></span>
* <span data-ttu-id="9b7be-268">Aplica la revisión.</span><span class="sxs-lookup"><span data-stu-id="9b7be-268">Applies the patch.</span></span>
* <span data-ttu-id="9b7be-269">Devuelve el resultado en el cuerpo de la respuesta.</span><span class="sxs-lookup"><span data-stu-id="9b7be-269">Returns the result in the body of the response.</span></span>

<span data-ttu-id="9b7be-270">En una aplicación real, el código recuperaría los datos de un almacén como una base de datos y actualizaría la base de datos después de aplicar la revisión.</span><span class="sxs-lookup"><span data-stu-id="9b7be-270">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="9b7be-271">Estado del modelo</span><span class="sxs-lookup"><span data-stu-id="9b7be-271">Model state</span></span>

<span data-ttu-id="9b7be-272">En el ejemplo anterior del método de acción, se llama a una sobrecarga de `ApplyTo` que toma el estado del modelo como uno de sus parámetros.</span><span class="sxs-lookup"><span data-stu-id="9b7be-272">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="9b7be-273">Con esta opción, puede obtener mensajes de error en las respuestas.</span><span class="sxs-lookup"><span data-stu-id="9b7be-273">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="9b7be-274">En el ejemplo siguiente se muestra el cuerpo de una respuesta 400 Solicitud incorrecta de una operación `test`:</span><span class="sxs-lookup"><span data-stu-id="9b7be-274">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="9b7be-275">Objetos dinámicos</span><span class="sxs-lookup"><span data-stu-id="9b7be-275">Dynamic objects</span></span>

<span data-ttu-id="9b7be-276">En el ejemplo de método de acción siguiente se muestra cómo aplicar una revisión a un objeto dinámico:</span><span class="sxs-lookup"><span data-stu-id="9b7be-276">The following action method example shows how to apply a patch to a dynamic object:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="9b7be-277">La operación add</span><span class="sxs-lookup"><span data-stu-id="9b7be-277">The add operation</span></span>

* <span data-ttu-id="9b7be-278">Si `path` apunta a un elemento de matriz: inserta un nuevo elemento delante del especificado por `path`.</span><span class="sxs-lookup"><span data-stu-id="9b7be-278">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="9b7be-279">Si `path` apunta a una propiedad: establece el valor de la propiedad.</span><span class="sxs-lookup"><span data-stu-id="9b7be-279">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="9b7be-280">Si `path` apunta a una ubicación que no existe:</span><span class="sxs-lookup"><span data-stu-id="9b7be-280">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="9b7be-281">Si el recurso para revisar es un objeto dinámico: agrega una propiedad.</span><span class="sxs-lookup"><span data-stu-id="9b7be-281">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="9b7be-282">Si el recurso para revisar es un objeto estático: la solicitud produce un error.</span><span class="sxs-lookup"><span data-stu-id="9b7be-282">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="9b7be-283">El siguiente documento de revisión de ejemplo establece el valor de `CustomerName` y agrega un objeto `Order` al final de la matriz `Orders`.</span><span class="sxs-lookup"><span data-stu-id="9b7be-283">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="9b7be-284">La operación remove</span><span class="sxs-lookup"><span data-stu-id="9b7be-284">The remove operation</span></span>

* <span data-ttu-id="9b7be-285">Si `path` apunta a un elemento de matriz: quita el elemento.</span><span class="sxs-lookup"><span data-stu-id="9b7be-285">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="9b7be-286">Si `path` apunta a una propiedad:</span><span class="sxs-lookup"><span data-stu-id="9b7be-286">If `path` points to a property:</span></span>
  * <span data-ttu-id="9b7be-287">Si el recurso para revisar es un objeto dinámico: quita la propiedad.</span><span class="sxs-lookup"><span data-stu-id="9b7be-287">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="9b7be-288">Si el recurso para revisar es un objeto estático:</span><span class="sxs-lookup"><span data-stu-id="9b7be-288">If resource to patch is a static object:</span></span>
    * <span data-ttu-id="9b7be-289">Si la propiedad acepta valores NULL: la establece en null.</span><span class="sxs-lookup"><span data-stu-id="9b7be-289">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="9b7be-290">Si la propiedad es distinta de null, la establece en `default<T>`.</span><span class="sxs-lookup"><span data-stu-id="9b7be-290">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="9b7be-291">En el documento de revisión de ejemplo siguiente `CustomerName` se establece en NULL y se elimina `Orders[0]` :</span><span class="sxs-lookup"><span data-stu-id="9b7be-291">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="9b7be-292">La operación replace</span><span class="sxs-lookup"><span data-stu-id="9b7be-292">The replace operation</span></span>

<span data-ttu-id="9b7be-293">Esta operación es funcionalmente igual que `remove` seguida de `add`.</span><span class="sxs-lookup"><span data-stu-id="9b7be-293">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="9b7be-294">En el siguiente documento de revisión de ejemplo se establece el valor de `CustomerName` y se reemplaza `Orders[0]` por un nuevo `Order` objeto:</span><span class="sxs-lookup"><span data-stu-id="9b7be-294">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="9b7be-295">La operación move</span><span class="sxs-lookup"><span data-stu-id="9b7be-295">The move operation</span></span>

* <span data-ttu-id="9b7be-296">Si `path` apunta a un elemento de matriz: copia el elemento `from` en la ubicación del elemento `path` y, luego, ejecuta una operación `remove` en el elemento `from`.</span><span class="sxs-lookup"><span data-stu-id="9b7be-296">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="9b7be-297">Si `path` apunta a una propiedad: copia el valor de la propiedad `from` en la propiedad `path` y, luego, ejecuta la operación `remove` en la propiedad `from`.</span><span class="sxs-lookup"><span data-stu-id="9b7be-297">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="9b7be-298">Si `path` apunta a una propiedad que no existe:</span><span class="sxs-lookup"><span data-stu-id="9b7be-298">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="9b7be-299">Si el recurso para revisar es un objeto estático: la solicitud produce un error.</span><span class="sxs-lookup"><span data-stu-id="9b7be-299">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="9b7be-300">Si el recurso para revisar es un objeto dinámico: copia la propiedad `from` en la ubicación indicada por `path` y, luego, ejecuta una operación `remove` en la propiedad `from`.</span><span class="sxs-lookup"><span data-stu-id="9b7be-300">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="9b7be-301">En el siguiente documento de revisión de ejemplo:</span><span class="sxs-lookup"><span data-stu-id="9b7be-301">The following sample patch document:</span></span>

* <span data-ttu-id="9b7be-302">Se copia el valor de `Orders[0].OrderName` en `CustomerName`.</span><span class="sxs-lookup"><span data-stu-id="9b7be-302">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="9b7be-303">Se establece `Orders[0].OrderName` en null.</span><span class="sxs-lookup"><span data-stu-id="9b7be-303">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="9b7be-304">Se mueve `Orders[1]` delante de `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="9b7be-304">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="9b7be-305">La operación copy</span><span class="sxs-lookup"><span data-stu-id="9b7be-305">The copy operation</span></span>

<span data-ttu-id="9b7be-306">Esta operación es funcionalmente igual que la operación `move` sin el paso `remove` final.</span><span class="sxs-lookup"><span data-stu-id="9b7be-306">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="9b7be-307">En el siguiente documento de revisión de ejemplo:</span><span class="sxs-lookup"><span data-stu-id="9b7be-307">The following sample patch document:</span></span>

* <span data-ttu-id="9b7be-308">Se copia el valor de `Orders[0].OrderName` en `CustomerName`.</span><span class="sxs-lookup"><span data-stu-id="9b7be-308">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="9b7be-309">Se inserta una copia de `Orders[1]` delante de `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="9b7be-309">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="9b7be-310">La operación test</span><span class="sxs-lookup"><span data-stu-id="9b7be-310">The test operation</span></span>

<span data-ttu-id="9b7be-311">Si el valor de la ubicación indicada por `path` es diferente del valor proporcionado en `value`, la solicitud produce un error.</span><span class="sxs-lookup"><span data-stu-id="9b7be-311">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="9b7be-312">En ese caso, la solicitud PATCH entera produce un error incluso si todas las demás operaciones del documento de revisión se realizan correctamente.</span><span class="sxs-lookup"><span data-stu-id="9b7be-312">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="9b7be-313">La operación `test` se usa habitualmente para impedir una actualización cuando hay un conflicto de simultaneidad.</span><span class="sxs-lookup"><span data-stu-id="9b7be-313">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="9b7be-314">El siguiente documento de revisión de ejemplo no tiene ningún efecto si el valor inicial de `CustomerName` es "John", porque la prueba produce un error:</span><span class="sxs-lookup"><span data-stu-id="9b7be-314">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="9b7be-315">Obtener el código</span><span class="sxs-lookup"><span data-stu-id="9b7be-315">Get the code</span></span>

<span data-ttu-id="9b7be-316">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples).</span><span class="sxs-lookup"><span data-stu-id="9b7be-316">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples).</span></span> <span data-ttu-id="9b7be-317">([Método de descarga](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="9b7be-317">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="9b7be-318">Para probar el ejemplo, ejecute la aplicación y envíe solicitudes HTTP con la configuración siguiente:</span><span class="sxs-lookup"><span data-stu-id="9b7be-318">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="9b7be-319">Dirección URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="9b7be-319">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="9b7be-320">Método HTTP: `PATCH`</span><span class="sxs-lookup"><span data-stu-id="9b7be-320">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="9b7be-321">Encabezado: `Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="9b7be-321">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="9b7be-322">Cuerpo: Copie y pegue uno de los ejemplos de documentos de revisión de JSON de la carpeta de proyecto *JSON* .</span><span class="sxs-lookup"><span data-stu-id="9b7be-322">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9b7be-323">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="9b7be-323">Additional resources</span></span>

* [<span data-ttu-id="9b7be-324">Especificación del método PATCH de IETF RFC 5789</span><span class="sxs-lookup"><span data-stu-id="9b7be-324">IETF RFC 5789 PATCH method specification</span></span>](https://tools.ietf.org/html/rfc5789)
* [<span data-ttu-id="9b7be-325">Especificación del método JSON PATCH de IETF RFC 6902</span><span class="sxs-lookup"><span data-stu-id="9b7be-325">IETF RFC 6902 JSON Patch specification</span></span>](https://tools.ietf.org/html/rfc6902)
* [<span data-ttu-id="9b7be-326">Especificación del formato de ruta de acceso JSON Patch de IETF RFC 6901</span><span class="sxs-lookup"><span data-stu-id="9b7be-326">IETF RFC 6901 JSON Patch path format spec</span></span>](https://tools.ietf.org/html/rfc6901)
* <span data-ttu-id="9b7be-327">[Documentación de JSON Patch](https://jsonpatch.com/).</span><span class="sxs-lookup"><span data-stu-id="9b7be-327">[JSON Patch documentation](https://jsonpatch.com/).</span></span> <span data-ttu-id="9b7be-328">Incluye vínculos a recursos para crear documentos JSON Patch.</span><span class="sxs-lookup"><span data-stu-id="9b7be-328">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="9b7be-329">Código fuente de JSON Patch para ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9b7be-329">ASP.NET Core JSON Patch source code</span></span>](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9b7be-330">En este artículo se explica cómo administrar solicitudes JSON Patch en una API web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9b7be-330">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="patch-http-request-method"></a><span data-ttu-id="9b7be-331">Método de solicitud HTTP PATCH</span><span class="sxs-lookup"><span data-stu-id="9b7be-331">PATCH HTTP request method</span></span>

<span data-ttu-id="9b7be-332">Los métodos PUT y [PATCH](https://tools.ietf.org/html/rfc5789) se usan para actualizar un recurso existente.</span><span class="sxs-lookup"><span data-stu-id="9b7be-332">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="9b7be-333">La diferencia entre ellos es que PUT reemplaza el recurso entero, mientras que PATCH especifica únicamente los cambios.</span><span class="sxs-lookup"><span data-stu-id="9b7be-333">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="9b7be-334">JSON Patch</span><span class="sxs-lookup"><span data-stu-id="9b7be-334">JSON Patch</span></span>

<span data-ttu-id="9b7be-335">[JSON Patch](https://tools.ietf.org/html/rfc6902) es un formato para especificar las actualizaciones que se aplicarán a un recurso.</span><span class="sxs-lookup"><span data-stu-id="9b7be-335">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="9b7be-336">Un documento JSON Patch tiene una matriz de *operaciones*.</span><span class="sxs-lookup"><span data-stu-id="9b7be-336">A JSON Patch document has an array of *operations*.</span></span> <span data-ttu-id="9b7be-337">Cada operación identifica un determinado tipo de cambio, como agregar un elemento de matriz o reemplazar un valor de propiedad.</span><span class="sxs-lookup"><span data-stu-id="9b7be-337">Each operation identifies a particular type of change, such as add an array element or replace a property value.</span></span>

<span data-ttu-id="9b7be-338">Por ejemplo, los documentos JSON siguientes representan un recurso, un documento de revisión JSON para el recurso y el resultado de aplicar las operaciones de revisión.</span><span class="sxs-lookup"><span data-stu-id="9b7be-338">For example, the following JSON documents represent a resource, a JSON patch document for the resource, and the result of applying the patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="9b7be-339">Ejemplo de recursos</span><span class="sxs-lookup"><span data-stu-id="9b7be-339">Resource example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="9b7be-340">Ejemplo de revisión de JSON</span><span class="sxs-lookup"><span data-stu-id="9b7be-340">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="9b7be-341">En el código JSON anterior:</span><span class="sxs-lookup"><span data-stu-id="9b7be-341">In the preceding JSON:</span></span>

* <span data-ttu-id="9b7be-342">La propiedad `op` indica el tipo de operación.</span><span class="sxs-lookup"><span data-stu-id="9b7be-342">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="9b7be-343">La propiedad `path` indica el elemento que se va a actualizar.</span><span class="sxs-lookup"><span data-stu-id="9b7be-343">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="9b7be-344">La propiedad `value` proporciona el nuevo valor.</span><span class="sxs-lookup"><span data-stu-id="9b7be-344">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="9b7be-345">Recurso después de la revisión</span><span class="sxs-lookup"><span data-stu-id="9b7be-345">Resource after patch</span></span>

<span data-ttu-id="9b7be-346">Este es el recurso después de aplicar el documento JSON Patch anterior:</span><span class="sxs-lookup"><span data-stu-id="9b7be-346">Here's the resource after applying the preceding JSON Patch document:</span></span>

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

<span data-ttu-id="9b7be-347">Los cambios realizados mediante la aplicación de un documento JSON Patch a un recurso son atómicos: si alguna operación de la lista produce un error, no se aplica ninguna operación de la lista.</span><span class="sxs-lookup"><span data-stu-id="9b7be-347">The changes made by applying a JSON Patch document to a resource are atomic: if any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="9b7be-348">Sintaxis de path</span><span class="sxs-lookup"><span data-stu-id="9b7be-348">Path syntax</span></span>

<span data-ttu-id="9b7be-349">La propiedad [path](https://tools.ietf.org/html/rfc6901) de un objeto de operación tiene barras inversas entre niveles.</span><span class="sxs-lookup"><span data-stu-id="9b7be-349">The [path](https://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="9b7be-350">Por ejemplo, `"/address/zipCode"`.</span><span class="sxs-lookup"><span data-stu-id="9b7be-350">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="9b7be-351">Para especificar elementos de matriz se usan índices de base cero.</span><span class="sxs-lookup"><span data-stu-id="9b7be-351">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="9b7be-352">El primer elemento de la matriz `addresses` estaría en `/addresses/0`.</span><span class="sxs-lookup"><span data-stu-id="9b7be-352">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="9b7be-353">Para usar `add` al final de una matriz, use un guion (-) en lugar de un número de índice: `/addresses/-`.</span><span class="sxs-lookup"><span data-stu-id="9b7be-353">To `add` to the end of an array, use a hyphen (-) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="9b7be-354">Operaciones</span><span class="sxs-lookup"><span data-stu-id="9b7be-354">Operations</span></span>

<span data-ttu-id="9b7be-355">En la siguiente tabla se muestran las operaciones admitidas, como se ha definido en la [especificación de JSON Patch](https://tools.ietf.org/html/rfc6902):</span><span class="sxs-lookup"><span data-stu-id="9b7be-355">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="9b7be-356">Operación</span><span class="sxs-lookup"><span data-stu-id="9b7be-356">Operation</span></span>  | <span data-ttu-id="9b7be-357">Notas</span><span class="sxs-lookup"><span data-stu-id="9b7be-357">Notes</span></span> |
|---
<span data-ttu-id="9b7be-358">Título: autor: Descripción: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="9b7be-358">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9b7be-359">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9b7be-359">'Blazor'</span></span>
- <span data-ttu-id="9b7be-360">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9b7be-360">'Identity'</span></span>
- <span data-ttu-id="9b7be-361">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9b7be-361">'Let's Encrypt'</span></span>
- <span data-ttu-id="9b7be-362">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9b7be-362">'Razor'</span></span>
- <span data-ttu-id="9b7be-363">SignalRUID ' ':</span><span class="sxs-lookup"><span data-stu-id="9b7be-363">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9b7be-364">Título: autor: Descripción: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="9b7be-364">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9b7be-365">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9b7be-365">'Blazor'</span></span>
- <span data-ttu-id="9b7be-366">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9b7be-366">'Identity'</span></span>
- <span data-ttu-id="9b7be-367">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9b7be-367">'Let's Encrypt'</span></span>
- <span data-ttu-id="9b7be-368">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9b7be-368">'Razor'</span></span>
- <span data-ttu-id="9b7be-369">SignalRUID ' ':</span><span class="sxs-lookup"><span data-stu-id="9b7be-369">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9b7be-370">Título: autor: Descripción: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="9b7be-370">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9b7be-371">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9b7be-371">'Blazor'</span></span>
- <span data-ttu-id="9b7be-372">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9b7be-372">'Identity'</span></span>
- <span data-ttu-id="9b7be-373">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9b7be-373">'Let's Encrypt'</span></span>
- <span data-ttu-id="9b7be-374">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9b7be-374">'Razor'</span></span>
- <span data-ttu-id="9b7be-375">SignalRUID ' ':</span><span class="sxs-lookup"><span data-stu-id="9b7be-375">'SignalR' uid:</span></span> 

<span data-ttu-id="9b7be-376">------|---
Título: autor: Descripción: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="9b7be-376">------|---
title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9b7be-377">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9b7be-377">'Blazor'</span></span>
- <span data-ttu-id="9b7be-378">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9b7be-378">'Identity'</span></span>
- <span data-ttu-id="9b7be-379">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9b7be-379">'Let's Encrypt'</span></span>
- <span data-ttu-id="9b7be-380">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9b7be-380">'Razor'</span></span>
- <span data-ttu-id="9b7be-381">SignalRUID ' ':</span><span class="sxs-lookup"><span data-stu-id="9b7be-381">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9b7be-382">Título: autor: Descripción: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="9b7be-382">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9b7be-383">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9b7be-383">'Blazor'</span></span>
- <span data-ttu-id="9b7be-384">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9b7be-384">'Identity'</span></span>
- <span data-ttu-id="9b7be-385">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9b7be-385">'Let's Encrypt'</span></span>
- <span data-ttu-id="9b7be-386">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9b7be-386">'Razor'</span></span>
- <span data-ttu-id="9b7be-387">SignalRUID ' ':</span><span class="sxs-lookup"><span data-stu-id="9b7be-387">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9b7be-388">Título: autor: Descripción: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="9b7be-388">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9b7be-389">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9b7be-389">'Blazor'</span></span>
- <span data-ttu-id="9b7be-390">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9b7be-390">'Identity'</span></span>
- <span data-ttu-id="9b7be-391">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9b7be-391">'Let's Encrypt'</span></span>
- <span data-ttu-id="9b7be-392">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9b7be-392">'Razor'</span></span>
- <span data-ttu-id="9b7be-393">SignalRUID ' ':</span><span class="sxs-lookup"><span data-stu-id="9b7be-393">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9b7be-394">Título: autor: Descripción: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="9b7be-394">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9b7be-395">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9b7be-395">'Blazor'</span></span>
- <span data-ttu-id="9b7be-396">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9b7be-396">'Identity'</span></span>
- <span data-ttu-id="9b7be-397">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9b7be-397">'Let's Encrypt'</span></span>
- <span data-ttu-id="9b7be-398">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9b7be-398">'Razor'</span></span>
- <span data-ttu-id="9b7be-399">SignalRUID ' ':</span><span class="sxs-lookup"><span data-stu-id="9b7be-399">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9b7be-400">Título: autor: Descripción: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="9b7be-400">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9b7be-401">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9b7be-401">'Blazor'</span></span>
- <span data-ttu-id="9b7be-402">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9b7be-402">'Identity'</span></span>
- <span data-ttu-id="9b7be-403">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9b7be-403">'Let's Encrypt'</span></span>
- <span data-ttu-id="9b7be-404">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9b7be-404">'Razor'</span></span>
- <span data-ttu-id="9b7be-405">SignalRUID ' ':</span><span class="sxs-lookup"><span data-stu-id="9b7be-405">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9b7be-406">Título: autor: Descripción: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="9b7be-406">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9b7be-407">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9b7be-407">'Blazor'</span></span>
- <span data-ttu-id="9b7be-408">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9b7be-408">'Identity'</span></span>
- <span data-ttu-id="9b7be-409">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9b7be-409">'Let's Encrypt'</span></span>
- <span data-ttu-id="9b7be-410">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9b7be-410">'Razor'</span></span>
- <span data-ttu-id="9b7be-411">SignalRUID ' ':</span><span class="sxs-lookup"><span data-stu-id="9b7be-411">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9b7be-412">Título: autor: Descripción: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="9b7be-412">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9b7be-413">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9b7be-413">'Blazor'</span></span>
- <span data-ttu-id="9b7be-414">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9b7be-414">'Identity'</span></span>
- <span data-ttu-id="9b7be-415">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9b7be-415">'Let's Encrypt'</span></span>
- <span data-ttu-id="9b7be-416">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9b7be-416">'Razor'</span></span>
- <span data-ttu-id="9b7be-417">SignalRUID ' ':</span><span class="sxs-lookup"><span data-stu-id="9b7be-417">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9b7be-418">Título: autor: Descripción: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="9b7be-418">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9b7be-419">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9b7be-419">'Blazor'</span></span>
- <span data-ttu-id="9b7be-420">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9b7be-420">'Identity'</span></span>
- <span data-ttu-id="9b7be-421">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9b7be-421">'Let's Encrypt'</span></span>
- <span data-ttu-id="9b7be-422">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9b7be-422">'Razor'</span></span>
- <span data-ttu-id="9b7be-423">SignalRUID ' ':</span><span class="sxs-lookup"><span data-stu-id="9b7be-423">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9b7be-424">Título: autor: Descripción: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="9b7be-424">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9b7be-425">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9b7be-425">'Blazor'</span></span>
- <span data-ttu-id="9b7be-426">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9b7be-426">'Identity'</span></span>
- <span data-ttu-id="9b7be-427">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9b7be-427">'Let's Encrypt'</span></span>
- <span data-ttu-id="9b7be-428">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9b7be-428">'Razor'</span></span>
- <span data-ttu-id="9b7be-429">SignalRUID ' ':</span><span class="sxs-lookup"><span data-stu-id="9b7be-429">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9b7be-430">Título: autor: Descripción: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="9b7be-430">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9b7be-431">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9b7be-431">'Blazor'</span></span>
- <span data-ttu-id="9b7be-432">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9b7be-432">'Identity'</span></span>
- <span data-ttu-id="9b7be-433">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9b7be-433">'Let's Encrypt'</span></span>
- <span data-ttu-id="9b7be-434">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9b7be-434">'Razor'</span></span>
- <span data-ttu-id="9b7be-435">SignalRUID ' ':</span><span class="sxs-lookup"><span data-stu-id="9b7be-435">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9b7be-436">Título: autor: Descripción: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="9b7be-436">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9b7be-437">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9b7be-437">'Blazor'</span></span>
- <span data-ttu-id="9b7be-438">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9b7be-438">'Identity'</span></span>
- <span data-ttu-id="9b7be-439">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9b7be-439">'Let's Encrypt'</span></span>
- <span data-ttu-id="9b7be-440">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9b7be-440">'Razor'</span></span>
- <span data-ttu-id="9b7be-441">SignalRUID ' ':</span><span class="sxs-lookup"><span data-stu-id="9b7be-441">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9b7be-442">Título: autor: Descripción: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="9b7be-442">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9b7be-443">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9b7be-443">'Blazor'</span></span>
- <span data-ttu-id="9b7be-444">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9b7be-444">'Identity'</span></span>
- <span data-ttu-id="9b7be-445">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9b7be-445">'Let's Encrypt'</span></span>
- <span data-ttu-id="9b7be-446">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9b7be-446">'Razor'</span></span>
- <span data-ttu-id="9b7be-447">SignalRUID ' ':</span><span class="sxs-lookup"><span data-stu-id="9b7be-447">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9b7be-448">Título: autor: Descripción: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="9b7be-448">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9b7be-449">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9b7be-449">'Blazor'</span></span>
- <span data-ttu-id="9b7be-450">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9b7be-450">'Identity'</span></span>
- <span data-ttu-id="9b7be-451">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9b7be-451">'Let's Encrypt'</span></span>
- <span data-ttu-id="9b7be-452">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9b7be-452">'Razor'</span></span>
- <span data-ttu-id="9b7be-453">SignalRUID ' ':</span><span class="sxs-lookup"><span data-stu-id="9b7be-453">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9b7be-454">Título: autor: Descripción: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="9b7be-454">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9b7be-455">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9b7be-455">'Blazor'</span></span>
- <span data-ttu-id="9b7be-456">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9b7be-456">'Identity'</span></span>
- <span data-ttu-id="9b7be-457">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9b7be-457">'Let's Encrypt'</span></span>
- <span data-ttu-id="9b7be-458">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9b7be-458">'Razor'</span></span>
- <span data-ttu-id="9b7be-459">SignalRUID ' ':</span><span class="sxs-lookup"><span data-stu-id="9b7be-459">'SignalR' uid:</span></span> 

<span data-ttu-id="9b7be-460">----------------| | `add`     | Agregue una propiedad o un elemento de matriz.</span><span class="sxs-lookup"><span data-stu-id="9b7be-460">----------------| | `add`     | Add a property or array element.</span></span> <span data-ttu-id="9b7be-461">Para la propiedad existente: establecer valor. | | `remove`  | Quite una propiedad o un elemento de matriz.</span><span class="sxs-lookup"><span data-stu-id="9b7be-461">For existing property: set value.| | `remove`  | Remove a property or array element.</span></span> <span data-ttu-id="9b7be-462">| | `replace` | Igual `remove` a seguido de `add` en la misma ubicación.</span><span class="sxs-lookup"><span data-stu-id="9b7be-462">| | `replace` | Same as `remove` followed by `add` at same location.</span></span> <span data-ttu-id="9b7be-463">| | `move`    | Igual que `remove` desde el origen seguido de `add` hasta el destino usando el valor del origen.</span><span class="sxs-lookup"><span data-stu-id="9b7be-463">| | `move`    | Same as `remove` from source followed by `add` to destination using value from source.</span></span> <span data-ttu-id="9b7be-464">| | `copy`    | Igual que el `add` destino mediante el valor del origen.</span><span class="sxs-lookup"><span data-stu-id="9b7be-464">| | `copy`    | Same as `add` to destination using value from source.</span></span> <span data-ttu-id="9b7be-465">| | `test`    | Devuelve el código de estado correcto si el valor se `path` proporciona en = `value` . |</span><span class="sxs-lookup"><span data-stu-id="9b7be-465">| | `test`    | Return success status code if value at `path` = provided `value`.|</span></span>

## <a name="jsonpatch-in-aspnet-core"></a><span data-ttu-id="9b7be-466">JsonPatch en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9b7be-466">JsonPatch in ASP.NET Core</span></span>

<span data-ttu-id="9b7be-467">La implementación de ASP.NET Core de JSON Patch se proporciona en el paquete NuGet [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/).</span><span class="sxs-lookup"><span data-stu-id="9b7be-467">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span> <span data-ttu-id="9b7be-468">El paquete se incluye en el metapaquete [Microsoft.AspnetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="9b7be-468">The package is included in the [Microsoft.AspnetCore.App](xref:fundamentals/metapackage-app) metapackage.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="9b7be-469">Código del método de acción</span><span class="sxs-lookup"><span data-stu-id="9b7be-469">Action method code</span></span>

<span data-ttu-id="9b7be-470">En un controlador de API, un método de acción para JSON Patch:</span><span class="sxs-lookup"><span data-stu-id="9b7be-470">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="9b7be-471">Se anota con el atributo `HttpPatch`.</span><span class="sxs-lookup"><span data-stu-id="9b7be-471">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="9b7be-472">Acepta `JsonPatchDocument<T>`, normalmente con `[FromBody]`.</span><span class="sxs-lookup"><span data-stu-id="9b7be-472">Accepts a `JsonPatchDocument<T>`, typically with `[FromBody]`.</span></span>
* <span data-ttu-id="9b7be-473">Llama a `ApplyTo` en el documento de revisión para aplicar los cambios.</span><span class="sxs-lookup"><span data-stu-id="9b7be-473">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="9b7be-474">Este es un ejemplo:</span><span class="sxs-lookup"><span data-stu-id="9b7be-474">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="9b7be-475">Este código de la aplicación de ejemplo funciona con el siguiente modelo `Customer`.</span><span class="sxs-lookup"><span data-stu-id="9b7be-475">This code from the sample app works with the following `Customer` model.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="9b7be-476">El método de acción de ejemplo:</span><span class="sxs-lookup"><span data-stu-id="9b7be-476">The sample action method:</span></span>

* <span data-ttu-id="9b7be-477">Construye un objeto `Customer`.</span><span class="sxs-lookup"><span data-stu-id="9b7be-477">Constructs a `Customer`.</span></span>
* <span data-ttu-id="9b7be-478">Aplica la revisión.</span><span class="sxs-lookup"><span data-stu-id="9b7be-478">Applies the patch.</span></span>
* <span data-ttu-id="9b7be-479">Devuelve el resultado en el cuerpo de la respuesta.</span><span class="sxs-lookup"><span data-stu-id="9b7be-479">Returns the result in the body of the response.</span></span>

 <span data-ttu-id="9b7be-480">En una aplicación real, el código recuperaría los datos de un almacén como una base de datos y actualizaría la base de datos después de aplicar la revisión.</span><span class="sxs-lookup"><span data-stu-id="9b7be-480">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="9b7be-481">Estado del modelo</span><span class="sxs-lookup"><span data-stu-id="9b7be-481">Model state</span></span>

<span data-ttu-id="9b7be-482">En el ejemplo anterior del método de acción, se llama a una sobrecarga de `ApplyTo` que toma el estado del modelo como uno de sus parámetros.</span><span class="sxs-lookup"><span data-stu-id="9b7be-482">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="9b7be-483">Con esta opción, puede obtener mensajes de error en las respuestas.</span><span class="sxs-lookup"><span data-stu-id="9b7be-483">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="9b7be-484">En el ejemplo siguiente se muestra el cuerpo de una respuesta 400 Solicitud incorrecta de una operación `test`:</span><span class="sxs-lookup"><span data-stu-id="9b7be-484">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="9b7be-485">Objetos dinámicos</span><span class="sxs-lookup"><span data-stu-id="9b7be-485">Dynamic objects</span></span>

<span data-ttu-id="9b7be-486">En el ejemplo siguiente de método de acción se muestra cómo aplicar una revisión a un objeto dinámico.</span><span class="sxs-lookup"><span data-stu-id="9b7be-486">The following action method example shows how to apply a patch to a dynamic object.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="9b7be-487">La operación add</span><span class="sxs-lookup"><span data-stu-id="9b7be-487">The add operation</span></span>

* <span data-ttu-id="9b7be-488">Si `path` apunta a un elemento de matriz: inserta un nuevo elemento delante del especificado por `path`.</span><span class="sxs-lookup"><span data-stu-id="9b7be-488">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="9b7be-489">Si `path` apunta a una propiedad: establece el valor de la propiedad.</span><span class="sxs-lookup"><span data-stu-id="9b7be-489">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="9b7be-490">Si `path` apunta a una ubicación que no existe:</span><span class="sxs-lookup"><span data-stu-id="9b7be-490">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="9b7be-491">Si el recurso para revisar es un objeto dinámico: agrega una propiedad.</span><span class="sxs-lookup"><span data-stu-id="9b7be-491">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="9b7be-492">Si el recurso para revisar es un objeto estático: la solicitud produce un error.</span><span class="sxs-lookup"><span data-stu-id="9b7be-492">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="9b7be-493">El siguiente documento de revisión de ejemplo establece el valor de `CustomerName` y agrega un objeto `Order` al final de la matriz `Orders`.</span><span class="sxs-lookup"><span data-stu-id="9b7be-493">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="9b7be-494">La operación remove</span><span class="sxs-lookup"><span data-stu-id="9b7be-494">The remove operation</span></span>

* <span data-ttu-id="9b7be-495">Si `path` apunta a un elemento de matriz: quita el elemento.</span><span class="sxs-lookup"><span data-stu-id="9b7be-495">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="9b7be-496">Si `path` apunta a una propiedad:</span><span class="sxs-lookup"><span data-stu-id="9b7be-496">If `path` points to a property:</span></span>
  * <span data-ttu-id="9b7be-497">Si el recurso para revisar es un objeto dinámico: quita la propiedad.</span><span class="sxs-lookup"><span data-stu-id="9b7be-497">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="9b7be-498">Si el recurso para revisar es un objeto estático:</span><span class="sxs-lookup"><span data-stu-id="9b7be-498">If resource to patch is a static object:</span></span>
    * <span data-ttu-id="9b7be-499">Si la propiedad acepta valores NULL: la establece en null.</span><span class="sxs-lookup"><span data-stu-id="9b7be-499">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="9b7be-500">Si la propiedad es distinta de null, la establece en `default<T>`.</span><span class="sxs-lookup"><span data-stu-id="9b7be-500">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="9b7be-501">En el siguiente documento de revisión de ejemplo, se establece `CustomerName` en null y se elimina `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="9b7be-501">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="9b7be-502">La operación replace</span><span class="sxs-lookup"><span data-stu-id="9b7be-502">The replace operation</span></span>

<span data-ttu-id="9b7be-503">Esta operación es funcionalmente igual que `remove` seguida de `add`.</span><span class="sxs-lookup"><span data-stu-id="9b7be-503">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="9b7be-504">En el siguiente documento de revisión de ejemplo se establece el valor de `CustomerName` y se reemplaza `Orders[0]` por un nuevo objeto `Order`.</span><span class="sxs-lookup"><span data-stu-id="9b7be-504">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="9b7be-505">La operación move</span><span class="sxs-lookup"><span data-stu-id="9b7be-505">The move operation</span></span>

* <span data-ttu-id="9b7be-506">Si `path` apunta a un elemento de matriz: copia el elemento `from` en la ubicación del elemento `path` y, luego, ejecuta una operación `remove` en el elemento `from`.</span><span class="sxs-lookup"><span data-stu-id="9b7be-506">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="9b7be-507">Si `path` apunta a una propiedad: copia el valor de la propiedad `from` en la propiedad `path` y, luego, ejecuta la operación `remove` en la propiedad `from`.</span><span class="sxs-lookup"><span data-stu-id="9b7be-507">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="9b7be-508">Si `path` apunta a una propiedad que no existe:</span><span class="sxs-lookup"><span data-stu-id="9b7be-508">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="9b7be-509">Si el recurso para revisar es un objeto estático: la solicitud produce un error.</span><span class="sxs-lookup"><span data-stu-id="9b7be-509">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="9b7be-510">Si el recurso para revisar es un objeto dinámico: copia la propiedad `from` en la ubicación indicada por `path` y, luego, ejecuta una operación `remove` en la propiedad `from`.</span><span class="sxs-lookup"><span data-stu-id="9b7be-510">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="9b7be-511">En el siguiente documento de revisión de ejemplo:</span><span class="sxs-lookup"><span data-stu-id="9b7be-511">The following sample patch document:</span></span>

* <span data-ttu-id="9b7be-512">Se copia el valor de `Orders[0].OrderName` en `CustomerName`.</span><span class="sxs-lookup"><span data-stu-id="9b7be-512">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="9b7be-513">Se establece `Orders[0].OrderName` en null.</span><span class="sxs-lookup"><span data-stu-id="9b7be-513">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="9b7be-514">Se mueve `Orders[1]` delante de `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="9b7be-514">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="9b7be-515">La operación copy</span><span class="sxs-lookup"><span data-stu-id="9b7be-515">The copy operation</span></span>

<span data-ttu-id="9b7be-516">Esta operación es funcionalmente igual que la operación `move` sin el paso `remove` final.</span><span class="sxs-lookup"><span data-stu-id="9b7be-516">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="9b7be-517">En el siguiente documento de revisión de ejemplo:</span><span class="sxs-lookup"><span data-stu-id="9b7be-517">The following sample patch document:</span></span>

* <span data-ttu-id="9b7be-518">Se copia el valor de `Orders[0].OrderName` en `CustomerName`.</span><span class="sxs-lookup"><span data-stu-id="9b7be-518">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="9b7be-519">Se inserta una copia de `Orders[1]` delante de `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="9b7be-519">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="9b7be-520">La operación test</span><span class="sxs-lookup"><span data-stu-id="9b7be-520">The test operation</span></span>

<span data-ttu-id="9b7be-521">Si el valor de la ubicación indicada por `path` es diferente del valor proporcionado en `value`, la solicitud produce un error.</span><span class="sxs-lookup"><span data-stu-id="9b7be-521">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="9b7be-522">En ese caso, la solicitud PATCH entera produce un error incluso si todas las demás operaciones del documento de revisión se realizan correctamente.</span><span class="sxs-lookup"><span data-stu-id="9b7be-522">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="9b7be-523">La operación `test` se usa habitualmente para impedir una actualización cuando hay un conflicto de simultaneidad.</span><span class="sxs-lookup"><span data-stu-id="9b7be-523">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="9b7be-524">El siguiente documento de revisión de ejemplo no tiene ningún efecto si el valor inicial de `CustomerName` es "John", porque la prueba produce un error:</span><span class="sxs-lookup"><span data-stu-id="9b7be-524">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="9b7be-525">Obtener el código</span><span class="sxs-lookup"><span data-stu-id="9b7be-525">Get the code</span></span>

<span data-ttu-id="9b7be-526">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2).</span><span class="sxs-lookup"><span data-stu-id="9b7be-526">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2).</span></span> <span data-ttu-id="9b7be-527">([Método de descarga](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="9b7be-527">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="9b7be-528">Para probar el ejemplo, ejecute la aplicación y envíe solicitudes HTTP con la configuración siguiente:</span><span class="sxs-lookup"><span data-stu-id="9b7be-528">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="9b7be-529">Dirección URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="9b7be-529">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="9b7be-530">Método HTTP: `PATCH`</span><span class="sxs-lookup"><span data-stu-id="9b7be-530">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="9b7be-531">Encabezado: `Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="9b7be-531">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="9b7be-532">Cuerpo: Copie y pegue uno de los ejemplos de documentos de revisión de JSON de la carpeta de proyecto *JSON* .</span><span class="sxs-lookup"><span data-stu-id="9b7be-532">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9b7be-533">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="9b7be-533">Additional resources</span></span>

* [<span data-ttu-id="9b7be-534">Especificación del método PATCH de IETF RFC 5789</span><span class="sxs-lookup"><span data-stu-id="9b7be-534">IETF RFC 5789 PATCH method specification</span></span>](https://tools.ietf.org/html/rfc5789)
* [<span data-ttu-id="9b7be-535">Especificación del método JSON PATCH de IETF RFC 6902</span><span class="sxs-lookup"><span data-stu-id="9b7be-535">IETF RFC 6902 JSON Patch specification</span></span>](https://tools.ietf.org/html/rfc6902)
* [<span data-ttu-id="9b7be-536">Especificación del formato de ruta de acceso JSON Patch de IETF RFC 6901</span><span class="sxs-lookup"><span data-stu-id="9b7be-536">IETF RFC 6901 JSON Patch path format spec</span></span>](https://tools.ietf.org/html/rfc6901)
* <span data-ttu-id="9b7be-537">[Documentación de JSON Patch](https://jsonpatch.com/).</span><span class="sxs-lookup"><span data-stu-id="9b7be-537">[JSON Patch documentation](https://jsonpatch.com/).</span></span> <span data-ttu-id="9b7be-538">Incluye vínculos a recursos para crear documentos JSON Patch.</span><span class="sxs-lookup"><span data-stu-id="9b7be-538">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="9b7be-539">Código fuente de JSON Patch para ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9b7be-539">ASP.NET Core JSON Patch source code</span></span>](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end
