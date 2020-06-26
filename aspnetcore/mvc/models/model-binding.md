---
title: Enlace de modelos en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo funciona el enlace de modelos en ASP.NET Core y cómo personalizar su comportamiento.
ms.assetid: 0be164aa-1d72-4192-bd6b-192c9c301164
ms.author: riande
ms.date: 12/18/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/models/model-binding
ms.openlocfilehash: b3dcb3a80e8d5150d8513ef558531749d0884568
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/26/2020
ms.locfileid: "85400159"
---
# <a name="model-binding-in-aspnet-core"></a><span data-ttu-id="12b27-103">Enlace de modelos en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="12b27-103">Model Binding in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="12b27-104">En este artículo se explica qué es el enlace de modelos, cómo funciona y cómo personalizar su comportamiento.</span><span class="sxs-lookup"><span data-stu-id="12b27-104">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="12b27-105">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="12b27-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="12b27-106">Qué es el enlace de modelos</span><span class="sxs-lookup"><span data-stu-id="12b27-106">What is Model binding</span></span>

<span data-ttu-id="12b27-107">Los controladores y Razor las páginas funcionan con los datos que proceden de las solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="12b27-107">Controllers and Razor pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="12b27-108">Por ejemplo, los datos de ruta pueden proporcionar una clave de registro y los campos de formulario publicados pueden proporcionar valores para las propiedades del modelo.</span><span class="sxs-lookup"><span data-stu-id="12b27-108">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="12b27-109">La escritura de código para recuperar cada uno de estos valores y convertirlos de cadenas a tipos de .NET sería tediosa y propensa a errores.</span><span class="sxs-lookup"><span data-stu-id="12b27-109">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="12b27-110">El enlace de modelos automatiza este proceso.</span><span class="sxs-lookup"><span data-stu-id="12b27-110">Model binding automates this process.</span></span> <span data-ttu-id="12b27-111">El sistema de enlace de modelos:</span><span class="sxs-lookup"><span data-stu-id="12b27-111">The model binding system:</span></span>

* <span data-ttu-id="12b27-112">Recupera datos de diversos orígenes, como datos de ruta, campos de formulario y cadenas de consulta.</span><span class="sxs-lookup"><span data-stu-id="12b27-112">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="12b27-113">Proporciona los datos a los controladores y Razor las páginas de los parámetros de método y las propiedades públicas.</span><span class="sxs-lookup"><span data-stu-id="12b27-113">Provides the data to controllers and Razor pages in method parameters and public properties.</span></span>
* <span data-ttu-id="12b27-114">Convierte datos de cadena en tipos de .NET.</span><span class="sxs-lookup"><span data-stu-id="12b27-114">Converts string data to .NET types.</span></span>
* <span data-ttu-id="12b27-115">Actualiza las propiedades de tipos complejos.</span><span class="sxs-lookup"><span data-stu-id="12b27-115">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="12b27-116">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="12b27-116">Example</span></span>

<span data-ttu-id="12b27-117">Imagine que tiene el siguiente método de acción:</span><span class="sxs-lookup"><span data-stu-id="12b27-117">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="12b27-118">Y que la aplicación recibe una solicitud con esta dirección URL:</span><span class="sxs-lookup"><span data-stu-id="12b27-118">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="12b27-119">El enlace de modelos realiza los pasos siguientes después de que el sistema de enrutamiento selecciona el método de acción:</span><span class="sxs-lookup"><span data-stu-id="12b27-119">Model binding goes through the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="12b27-120">Busca el primer parámetro de `GetByID`, un entero denominado `id`.</span><span class="sxs-lookup"><span data-stu-id="12b27-120">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="12b27-121">Examina los orígenes disponibles en la solicitud HTTP y busca `id` = "2" en los datos de ruta.</span><span class="sxs-lookup"><span data-stu-id="12b27-121">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="12b27-122">Convierte la cadena "2" en el entero 2.</span><span class="sxs-lookup"><span data-stu-id="12b27-122">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="12b27-123">Busca el siguiente parámetro de `GetByID`, un valor booleano denominado `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="12b27-123">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="12b27-124">Examina los orígenes y busca "DogsOnly=true" en la cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="12b27-124">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="12b27-125">La coincidencia de nombres no distingue mayúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="12b27-125">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="12b27-126">Convierte la cadena "true" en un valor booleano `true`.</span><span class="sxs-lookup"><span data-stu-id="12b27-126">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="12b27-127">Después, el marco llama al método `GetById` y pasa 2 para el parámetro `id` y `true` para el parámetro `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="12b27-127">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="12b27-128">En el ejemplo anterior, los destinos de enlace de modelos son parámetros de método que son tipos simples.</span><span class="sxs-lookup"><span data-stu-id="12b27-128">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="12b27-129">Los destinos también pueden ser las propiedades de un tipo complejo.</span><span class="sxs-lookup"><span data-stu-id="12b27-129">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="12b27-130">Después de que cada propiedad se haya enlazado correctamente, se produce la [validación de modelos](xref:mvc/models/validation) para esa propiedad.</span><span class="sxs-lookup"><span data-stu-id="12b27-130">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="12b27-131">El registro de qué datos se han enlazado al modelo y los errores de enlace o validación se almacenan en [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) o [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span><span class="sxs-lookup"><span data-stu-id="12b27-131">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="12b27-132">Para averiguar si este proceso se ha realizado correctamente, la aplicación comprueba la marca [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid).</span><span class="sxs-lookup"><span data-stu-id="12b27-132">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="12b27-133">Destinos</span><span class="sxs-lookup"><span data-stu-id="12b27-133">Targets</span></span>

<span data-ttu-id="12b27-134">El enlace de modelos intenta encontrar valores para los tipos de destinos siguientes:</span><span class="sxs-lookup"><span data-stu-id="12b27-134">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="12b27-135">Parámetros del método de acción de controlador al que se enruta una solicitud.</span><span class="sxs-lookup"><span data-stu-id="12b27-135">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="12b27-136">Parámetros del Razor método de control de páginas al que se enruta una solicitud.</span><span class="sxs-lookup"><span data-stu-id="12b27-136">Parameters of the Razor Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="12b27-137">Propiedades públicas de un controlador o una clase `PageModel`, si se especifican mediante atributos.</span><span class="sxs-lookup"><span data-stu-id="12b27-137">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="12b27-138">Atributo [BindProperty]</span><span class="sxs-lookup"><span data-stu-id="12b27-138">[BindProperty] attribute</span></span>

<span data-ttu-id="12b27-139">Se puede aplicar a una propiedad pública de un controlador o una clase `PageModel` para hacer que el enlace de modelos tenga esa propiedad como destino:</span><span class="sxs-lookup"><span data-stu-id="12b27-139">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindpropertiesattribute"></a><span data-ttu-id="12b27-140">Atributo [BindProperties]</span><span class="sxs-lookup"><span data-stu-id="12b27-140">[BindProperties] attribute</span></span>

<span data-ttu-id="12b27-141">Disponible en ASP.NET 2.1 Core y versiones posteriores.</span><span class="sxs-lookup"><span data-stu-id="12b27-141">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="12b27-142">Se pueden aplicar a un controlador o una clase `PageModel` para indicar al enlace de modelos que seleccione como destino todas las propiedades públicas de la clase:</span><span class="sxs-lookup"><span data-stu-id="12b27-142">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="12b27-143">Enlace de modelos para solicitudes HTTP GET</span><span class="sxs-lookup"><span data-stu-id="12b27-143">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="12b27-144">De forma predeterminada, las propiedades no se enlazan para las solicitudes HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="12b27-144">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="12b27-145">Normalmente, todo lo que necesita para una solicitud GET es un parámetro de id. de registro.</span><span class="sxs-lookup"><span data-stu-id="12b27-145">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="12b27-146">El id. de registro se usa para buscar el elemento en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="12b27-146">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="12b27-147">Por tanto, no es necesario enlazar una propiedad que contiene una instancia del modelo.</span><span class="sxs-lookup"><span data-stu-id="12b27-147">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="12b27-148">En escenarios donde quiera propiedades enlazadas a datos de las solicitudes GET, establezca la propiedad `SupportsGet` en `true`:</span><span class="sxs-lookup"><span data-stu-id="12b27-148">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="12b27-149">Orígenes</span><span class="sxs-lookup"><span data-stu-id="12b27-149">Sources</span></span>

<span data-ttu-id="12b27-150">De forma predeterminada, el enlace de modelos obtiene datos en forma de pares clave-valor de los siguientes orígenes de una solicitud HTTP:</span><span class="sxs-lookup"><span data-stu-id="12b27-150">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="12b27-151">Campos de formulario</span><span class="sxs-lookup"><span data-stu-id="12b27-151">Form fields</span></span>
1. <span data-ttu-id="12b27-152">El cuerpo de la solicitud (para [controladores que tienen el atributo [ApiController]](xref:web-api/index#binding-source-parameter-inference)).</span><span class="sxs-lookup"><span data-stu-id="12b27-152">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="12b27-153">Datos de ruta</span><span class="sxs-lookup"><span data-stu-id="12b27-153">Route data</span></span>
1. <span data-ttu-id="12b27-154">Parámetros de cadena de consulta</span><span class="sxs-lookup"><span data-stu-id="12b27-154">Query string parameters</span></span>
1. <span data-ttu-id="12b27-155">Archivos cargados</span><span class="sxs-lookup"><span data-stu-id="12b27-155">Uploaded files</span></span>

<span data-ttu-id="12b27-156">Para cada parámetro o propiedad de destino, se examinan los orígenes en el orden indicado en la lista anterior.</span><span class="sxs-lookup"><span data-stu-id="12b27-156">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span></span> <span data-ttu-id="12b27-157">Hay algunas excepciones:</span><span class="sxs-lookup"><span data-stu-id="12b27-157">There are a few exceptions:</span></span>

* <span data-ttu-id="12b27-158">Los datos de ruta y los valores de cadena de consulta solo se usan para tipos simples.</span><span class="sxs-lookup"><span data-stu-id="12b27-158">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="12b27-159">Los archivos cargados solo se enlazan a tipos de destino que implementan `IFormFile` o `IEnumerable<IFormFile>`.</span><span class="sxs-lookup"><span data-stu-id="12b27-159">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="12b27-160">Si el origen predeterminado no es correcto, use uno de los atributos siguientes para especificar el origen:</span><span class="sxs-lookup"><span data-stu-id="12b27-160">If the default source is not correct, use one of the following attributes to specify the source:</span></span>

* <span data-ttu-id="12b27-161">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute): Obtiene los valores de la cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="12b27-161">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="12b27-162">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute): Obtiene los valores de los datos de ruta.</span><span class="sxs-lookup"><span data-stu-id="12b27-162">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="12b27-163">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute): Obtiene los valores de los campos de formulario publicados.</span><span class="sxs-lookup"><span data-stu-id="12b27-163">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="12b27-164">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute): Obtiene valores del cuerpo de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="12b27-164">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="12b27-165">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute): Obtiene valores de los encabezados HTTP.</span><span class="sxs-lookup"><span data-stu-id="12b27-165">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="12b27-166">Estos atributos:</span><span class="sxs-lookup"><span data-stu-id="12b27-166">These attributes:</span></span>

* <span data-ttu-id="12b27-167">Se agregan de forma individual a las propiedades del modelo (no a la clase de modelo), como en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="12b27-167">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="12b27-168">Opcionalmente, acepte un valor de nombre de modelo en el constructor.</span><span class="sxs-lookup"><span data-stu-id="12b27-168">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="12b27-169">Esta opción se proporciona en caso de que el nombre de la propiedad no coincida con el valor de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="12b27-169">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="12b27-170">Por ejemplo, es posible que el valor de la solicitud sea un encabezado con un guion en el nombre, como en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="12b27-170">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="12b27-171">Atributo [FromBody]</span><span class="sxs-lookup"><span data-stu-id="12b27-171">[FromBody] attribute</span></span>

<span data-ttu-id="12b27-172">Aplique el atributo `[FromBody]` a un parámetro para rellenar sus propiedades desde el cuerpo de una solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="12b27-172">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span></span> <span data-ttu-id="12b27-173">El runtime de ASP.NET Core delega la responsabilidad de leer el cuerpo al formateador de entrada.</span><span class="sxs-lookup"><span data-stu-id="12b27-173">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span></span> <span data-ttu-id="12b27-174">Los formateadores de entrada se explican [más adelante en este artículo](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="12b27-174">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="12b27-175">Cuando se aplica `[FromBody]` a un parámetro de tipo complejo, se omiten los atributos de origen de enlace aplicados a sus propiedades.</span><span class="sxs-lookup"><span data-stu-id="12b27-175">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span></span> <span data-ttu-id="12b27-176">Por ejemplo, la acción `Create` siguiente especifica que su parámetro `pet` se rellena a partir del cuerpo:</span><span class="sxs-lookup"><span data-stu-id="12b27-176">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span></span>

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

<span data-ttu-id="12b27-177">La clase `Pet` especifica que su propiedad `Breed` se rellena a partir de un parámetro de cadena de consulta:</span><span class="sxs-lookup"><span data-stu-id="12b27-177">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

<span data-ttu-id="12b27-178">En el ejemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="12b27-178">In the preceding example:</span></span>

* <span data-ttu-id="12b27-179">El atributo `[FromQuery]` se ignora.</span><span class="sxs-lookup"><span data-stu-id="12b27-179">The `[FromQuery]` attribute is ignored.</span></span>
* <span data-ttu-id="12b27-180">La propiedad `Breed` no se rellena desde un parámetro de cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="12b27-180">The `Breed` property is not populated from a query string parameter.</span></span> 

<span data-ttu-id="12b27-181">Los formateadores de entrada solo leen el cuerpo y no entienden los atributos de origen de enlace.</span><span class="sxs-lookup"><span data-stu-id="12b27-181">Input formatters read only the body and don't understand binding source attributes.</span></span> <span data-ttu-id="12b27-182">Si se encuentra un valor adecuado en el cuerpo, ese valor se usa para rellenar la propiedad `Breed`.</span><span class="sxs-lookup"><span data-stu-id="12b27-182">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span></span>

<span data-ttu-id="12b27-183">No aplique `[FromBody]` a más de un parámetro por método de acción.</span><span class="sxs-lookup"><span data-stu-id="12b27-183">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="12b27-184">Una vez que un formateador de entrada ha leído la secuencia de solicitudes, deja de estar disponible para una nueva lectura con el fin de enlazar otros parámetros `[FromBody]`.</span><span class="sxs-lookup"><span data-stu-id="12b27-184">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="12b27-185">Orígenes adicionales</span><span class="sxs-lookup"><span data-stu-id="12b27-185">Additional sources</span></span>

<span data-ttu-id="12b27-186">Los *proveedores de valores* proporcionan datos de origen al sistema de enlace de modelos.</span><span class="sxs-lookup"><span data-stu-id="12b27-186">Source data is provided to the model binding system by *value providers*.</span></span> <span data-ttu-id="12b27-187">Puede escribir y registrar proveedores de valores personalizados que obtienen datos de otros orígenes para el enlace de modelos.</span><span class="sxs-lookup"><span data-stu-id="12b27-187">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="12b27-188">Por ejemplo, es posible que le interesen datos de cookies o del estado de sesión.</span><span class="sxs-lookup"><span data-stu-id="12b27-188">For example, you might want data from cookies or session state.</span></span> <span data-ttu-id="12b27-189">Para obtener datos desde un origen nuevo:</span><span class="sxs-lookup"><span data-stu-id="12b27-189">To get data from a new source:</span></span>

* <span data-ttu-id="12b27-190">Cree una clase que implemente `IValueProvider`.</span><span class="sxs-lookup"><span data-stu-id="12b27-190">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="12b27-191">Cree una clase que implemente `IValueProviderFactory`.</span><span class="sxs-lookup"><span data-stu-id="12b27-191">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="12b27-192">Registre la clase de generador en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="12b27-192">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="12b27-193">En la aplicación de ejemplo se incluye un [proveedor de valores](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProvider.cs) y un [generador](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProviderFactory.cs) que obtiene valores de cookies.</span><span class="sxs-lookup"><span data-stu-id="12b27-193">The sample app includes a [value provider](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProvider.cs) and [factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProviderFactory.cs) example that gets values from cookies.</span></span> <span data-ttu-id="12b27-194">Este es el código de registro de `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="12b27-194">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4)]

<span data-ttu-id="12b27-195">En el código mostrado, el proveedor de valor personalizado se coloca después de todos los proveedores de valor integrados.</span><span class="sxs-lookup"><span data-stu-id="12b27-195">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="12b27-196">Para que sea el primero en la lista, llame a `Insert(0, new CookieValueProviderFactory())` en lugar de a `Add`.</span><span class="sxs-lookup"><span data-stu-id="12b27-196">To make it the first in the list, call `Insert(0, new CookieValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="12b27-197">No hay origen para una propiedad de modelo</span><span class="sxs-lookup"><span data-stu-id="12b27-197">No source for a model property</span></span>

<span data-ttu-id="12b27-198">De forma predeterminada, si no se encuentra ningún valor para una propiedad de modelo no se crea un error de estado del modelo.</span><span class="sxs-lookup"><span data-stu-id="12b27-198">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="12b27-199">La propiedad se establece en NULL o en un valor predeterminado:</span><span class="sxs-lookup"><span data-stu-id="12b27-199">The property is set to null or a default value:</span></span>

* <span data-ttu-id="12b27-200">Los tipos simples que aceptan valores NULL se establecen en `null`.</span><span class="sxs-lookup"><span data-stu-id="12b27-200">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="12b27-201">Los tipos de valor que no aceptan valores NULL se establecen en `default(T)`.</span><span class="sxs-lookup"><span data-stu-id="12b27-201">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="12b27-202">Por ejemplo, un parámetro `int id` se establece en 0.</span><span class="sxs-lookup"><span data-stu-id="12b27-202">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="12b27-203">Para los tipos complejos, el enlace de modelos crea una instancia mediante el constructor predeterminado, sin establecer propiedades.</span><span class="sxs-lookup"><span data-stu-id="12b27-203">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="12b27-204">Las matrices se establecen en `Array.Empty<T>()`, salvo las matrices `byte[]`, que se establecen en `null`.</span><span class="sxs-lookup"><span data-stu-id="12b27-204">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="12b27-205">Si el estado del modelo se debe invalidar Cuando no se encuentra nada en los campos de formulario de una propiedad de modelo, utilice el [`[BindRequired]`](#bindrequired-attribute) atributo.</span><span class="sxs-lookup"><span data-stu-id="12b27-205">If model state should be invalidated when nothing is found in form fields for a model property, use the [`[BindRequired]`](#bindrequired-attribute) attribute.</span></span>

<span data-ttu-id="12b27-206">Tenga en cuenta que este comportamiento de `[BindRequired]` se aplica al enlace de modelos desde datos de formulario publicados, no a los datos JSON o XML del cuerpo de una solicitud.</span><span class="sxs-lookup"><span data-stu-id="12b27-206">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="12b27-207">Los datos del cuerpo de la solicitud se controlan mediante [formateadores de entrada](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="12b27-207">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="12b27-208">Errores de la conversión de tipos</span><span class="sxs-lookup"><span data-stu-id="12b27-208">Type conversion errors</span></span>

<span data-ttu-id="12b27-209">Si se encuentra un origen pero no se puede convertir al tipo de destino, el estado del modelo se marca como no válido.</span><span class="sxs-lookup"><span data-stu-id="12b27-209">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="12b27-210">El parámetro o la propiedad de destino se establece en NULL o en un valor predeterminado, como se ha indicado en la sección anterior.</span><span class="sxs-lookup"><span data-stu-id="12b27-210">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="12b27-211">En un controlador de API que tenga el atributo `[ApiController]`, el estado de modelo no válido genera una respuesta HTTP 400 automática.</span><span class="sxs-lookup"><span data-stu-id="12b27-211">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="12b27-212">En una Razor página, vuelva a mostrar la página con un mensaje de error:</span><span class="sxs-lookup"><span data-stu-id="12b27-212">In a Razor page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="12b27-213">La validación del lado cliente detecta la mayoría de los datos incorrectos que de otro modo se enviarían a un Razor formulario de páginas.</span><span class="sxs-lookup"><span data-stu-id="12b27-213">Client-side validation catches most bad data that would otherwise be submitted to a Razor Pages form.</span></span> <span data-ttu-id="12b27-214">Esta validación dificulta que se pueda desencadenar el código resaltado anterior.</span><span class="sxs-lookup"><span data-stu-id="12b27-214">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="12b27-215">En la aplicación de ejemplo se incluye un botón **Submit with Invalid Date** (Enviar con fecha no válida) que agrega datos incorrectos al campo **Hire Date** (Fecha de contratación) y envía el formulario.</span><span class="sxs-lookup"><span data-stu-id="12b27-215">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="12b27-216">Este botón muestra cómo funciona el código para volver a mostrar la página cuando se producen errores de conversión de datos.</span><span class="sxs-lookup"><span data-stu-id="12b27-216">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="12b27-217">Cuando el código anterior vuelve a mostrar la página, no se muestra la entrada no válida en el campo de formulario.</span><span class="sxs-lookup"><span data-stu-id="12b27-217">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="12b27-218">El motivo es que la propiedad de modelo se ha establecido en NULL o en un valor predeterminado.</span><span class="sxs-lookup"><span data-stu-id="12b27-218">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="12b27-219">La entrada no válida sí aparece en un mensaje de error.</span><span class="sxs-lookup"><span data-stu-id="12b27-219">The invalid input does appear in an error message.</span></span> <span data-ttu-id="12b27-220">Pero si quiere volver a mostrar los datos incorrectos en el campo de formulario, considere la posibilidad de convertir la propiedad de modelo en una cadena y realizar la conversión de datos de forma manual.</span><span class="sxs-lookup"><span data-stu-id="12b27-220">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="12b27-221">Se recomienda la misma estrategia si no quiere que los errores de conversión de tipo generen errores de estado de modelo.</span><span class="sxs-lookup"><span data-stu-id="12b27-221">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="12b27-222">En ese caso, convierta la propiedad de modelo en una cadena.</span><span class="sxs-lookup"><span data-stu-id="12b27-222">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="12b27-223">Tipos simples</span><span class="sxs-lookup"><span data-stu-id="12b27-223">Simple types</span></span>

<span data-ttu-id="12b27-224">Los tipos simples a los que el enlazador de modelos puede convertir las cadenas de origen incluyen los siguientes:</span><span class="sxs-lookup"><span data-stu-id="12b27-224">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="12b27-225">Boolean</span><span class="sxs-lookup"><span data-stu-id="12b27-225">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="12b27-226">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="12b27-226">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="12b27-227">Char</span><span class="sxs-lookup"><span data-stu-id="12b27-227">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="12b27-228">DateTime</span><span class="sxs-lookup"><span data-stu-id="12b27-228">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="12b27-229">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="12b27-229">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="12b27-230">Decimal</span><span class="sxs-lookup"><span data-stu-id="12b27-230">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="12b27-231">Double</span><span class="sxs-lookup"><span data-stu-id="12b27-231">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="12b27-232">Enumeración</span><span class="sxs-lookup"><span data-stu-id="12b27-232">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="12b27-233">Volumen</span><span class="sxs-lookup"><span data-stu-id="12b27-233">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="12b27-234">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="12b27-234">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="12b27-235">Single</span><span class="sxs-lookup"><span data-stu-id="12b27-235">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="12b27-236">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="12b27-236">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="12b27-237">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="12b27-237">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="12b27-238">Uri</span><span class="sxs-lookup"><span data-stu-id="12b27-238">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="12b27-239">Versión</span><span class="sxs-lookup"><span data-stu-id="12b27-239">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="12b27-240">Tipos complejos</span><span class="sxs-lookup"><span data-stu-id="12b27-240">Complex types</span></span>

<span data-ttu-id="12b27-241">Un tipo complejo debe tener un constructor público predeterminado y propiedades grabables públicas para enlazar.</span><span class="sxs-lookup"><span data-stu-id="12b27-241">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="12b27-242">Cuando se produce el enlace de modelos, se crea una instancia de la clase con el constructor predeterminado público.</span><span class="sxs-lookup"><span data-stu-id="12b27-242">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="12b27-243">Para cada propiedad del tipo complejo, el enlace de modelos busca entre los orígenes el patrón de nombre *prefijo.nombre_de_propiedad*.</span><span class="sxs-lookup"><span data-stu-id="12b27-243">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name*.</span></span> <span data-ttu-id="12b27-244">Si no se encuentra nada, solo busca *nombre_de_propiedad* sin el prefijo.</span><span class="sxs-lookup"><span data-stu-id="12b27-244">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="12b27-245">Para el enlace a un parámetro, el prefijo es el nombre del parámetro.</span><span class="sxs-lookup"><span data-stu-id="12b27-245">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="12b27-246">Para el enlace a una propiedad pública `PageModel`, el prefijo es el nombre de la propiedad pública.</span><span class="sxs-lookup"><span data-stu-id="12b27-246">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="12b27-247">Algunos atributos tienen una propiedad `Prefix` que permite invalidar el uso predeterminado del nombre de parámetro o propiedad.</span><span class="sxs-lookup"><span data-stu-id="12b27-247">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="12b27-248">Por ejemplo, imagine que el tipo complejo es la clase `Instructor` siguiente:</span><span class="sxs-lookup"><span data-stu-id="12b27-248">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="12b27-249">Prefijo = nombre del parámetro</span><span class="sxs-lookup"><span data-stu-id="12b27-249">Prefix = parameter name</span></span>

<span data-ttu-id="12b27-250">Si el modelo que se va a enlazar es un parámetro denominado `instructorToUpdate`:</span><span class="sxs-lookup"><span data-stu-id="12b27-250">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="12b27-251">El enlace de modelos se inicia mediante el examen de los orígenes para la clave `instructorToUpdate.ID`.</span><span class="sxs-lookup"><span data-stu-id="12b27-251">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="12b27-252">Si no se encuentra, busca `ID` sin un prefijo.</span><span class="sxs-lookup"><span data-stu-id="12b27-252">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="12b27-253">Prefijo = nombre de propiedad</span><span class="sxs-lookup"><span data-stu-id="12b27-253">Prefix = property name</span></span>

<span data-ttu-id="12b27-254">Si el modelo que se va a enlazar es una propiedad denominada `Instructor` del controlador o la clase `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="12b27-254">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="12b27-255">El enlace de modelos se inicia mediante el examen de los orígenes para la clave `Instructor.ID`.</span><span class="sxs-lookup"><span data-stu-id="12b27-255">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="12b27-256">Si no se encuentra, busca `ID` sin un prefijo.</span><span class="sxs-lookup"><span data-stu-id="12b27-256">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="12b27-257">Prefijo personalizado</span><span class="sxs-lookup"><span data-stu-id="12b27-257">Custom prefix</span></span>

<span data-ttu-id="12b27-258">Si el modelo que se va a enlazar es un parámetro denominado `instructorToUpdate` y un atributo `Bind`, especifica `Instructor` como el prefijo:</span><span class="sxs-lookup"><span data-stu-id="12b27-258">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="12b27-259">El enlace de modelos se inicia mediante el examen de los orígenes para la clave `Instructor.ID`.</span><span class="sxs-lookup"><span data-stu-id="12b27-259">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="12b27-260">Si no se encuentra, busca `ID` sin un prefijo.</span><span class="sxs-lookup"><span data-stu-id="12b27-260">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="12b27-261">Atributos para destinos de tipo complejo</span><span class="sxs-lookup"><span data-stu-id="12b27-261">Attributes for complex type targets</span></span>

<span data-ttu-id="12b27-262">Existen varios atributos integrados para controlar el enlace de modelos de tipos complejos:</span><span class="sxs-lookup"><span data-stu-id="12b27-262">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[BindRequired]`
* `[BindNever]`
* `[Bind]`

> [!NOTE]
> <span data-ttu-id="12b27-263">Estos atributos afectan al enlace de modelos cuando el origen de los valores son datos de formulario publicados.</span><span class="sxs-lookup"><span data-stu-id="12b27-263">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="12b27-264">No afectan a los formateadores de entrada, que procesan los cuerpos de la solicitud JSON y XML publicados.</span><span class="sxs-lookup"><span data-stu-id="12b27-264">They do not affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="12b27-265">Los formateadores de entrada se explican [más adelante en este artículo](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="12b27-265">Input formatters are explained [later in this article](#input-formatters).</span></span>
>
> <span data-ttu-id="12b27-266">Vea también la explicación del atributo `[Required]` en [Validación de modelos](xref:mvc/models/validation#required-attribute).</span><span class="sxs-lookup"><span data-stu-id="12b27-266">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindrequired-attribute"></a><span data-ttu-id="12b27-267">Atributo [BindRequired]</span><span class="sxs-lookup"><span data-stu-id="12b27-267">[BindRequired] attribute</span></span>

<span data-ttu-id="12b27-268">Solo se puede aplicar a propiedades del modelo, no a parámetros de método.</span><span class="sxs-lookup"><span data-stu-id="12b27-268">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="12b27-269">Hace que el enlace de modelos agregue un error de estado de modelo si no se puede realizar el enlace para la propiedad de un modelo.</span><span class="sxs-lookup"><span data-stu-id="12b27-269">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="12b27-270">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="12b27-270">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

### <a name="bindnever-attribute"></a><span data-ttu-id="12b27-271">Atributo [BindNever]</span><span class="sxs-lookup"><span data-stu-id="12b27-271">[BindNever] attribute</span></span>

<span data-ttu-id="12b27-272">Solo se puede aplicar a propiedades del modelo, no a parámetros de método.</span><span class="sxs-lookup"><span data-stu-id="12b27-272">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="12b27-273">Impide que el enlace de modelos establezca la propiedad de un modelo.</span><span class="sxs-lookup"><span data-stu-id="12b27-273">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="12b27-274">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="12b27-274">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

### <a name="bind-attribute"></a><span data-ttu-id="12b27-275">Atributo [Bind]</span><span class="sxs-lookup"><span data-stu-id="12b27-275">[Bind] attribute</span></span>

<span data-ttu-id="12b27-276">Se puede aplicar a una clase o un parámetro de método.</span><span class="sxs-lookup"><span data-stu-id="12b27-276">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="12b27-277">Especifica qué propiedades de un modelo se deben incluir en el enlace de modelos.</span><span class="sxs-lookup"><span data-stu-id="12b27-277">Specifies which properties of a model should be included in model binding.</span></span>

<span data-ttu-id="12b27-278">En el ejemplo siguiente, solo se enlazan las propiedades especificadas del modelo `Instructor` cuando se llama a cualquier método de acción o controlador:</span><span class="sxs-lookup"><span data-stu-id="12b27-278">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="12b27-279">En el ejemplo siguiente, solo se enlazan las propiedades especificadas del modelo `Instructor` cuando se llama al método `OnPost`:</span><span class="sxs-lookup"><span data-stu-id="12b27-279">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="12b27-280">El atributo `[Bind]` se puede usar para protegerse de la publicación excesiva en escenarios de *creación*.</span><span class="sxs-lookup"><span data-stu-id="12b27-280">The `[Bind]` attribute can be used to protect against overposting in *create* scenarios.</span></span> <span data-ttu-id="12b27-281">No funciona bien en escenarios de edición porque las propiedades excluidas se establecen en NULL o en un valor predeterminado en lugar de mantenerse sin cambios.</span><span class="sxs-lookup"><span data-stu-id="12b27-281">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="12b27-282">Para defenderse de la publicación excesiva, se recomiendan modelos de vista en lugar del atributo `[Bind]`.</span><span class="sxs-lookup"><span data-stu-id="12b27-282">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="12b27-283">Para más información, vea [Nota de seguridad sobre la publicación excesiva](xref:data/ef-mvc/crud#security-note-about-overposting).</span><span class="sxs-lookup"><span data-stu-id="12b27-283">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

## <a name="collections"></a><span data-ttu-id="12b27-284">Colecciones</span><span class="sxs-lookup"><span data-stu-id="12b27-284">Collections</span></span>

<span data-ttu-id="12b27-285">Para los destinos que son colecciones de tipos simples, el enlace de modelos busca coincidencias con *nombre_de_parámetro* o *nombre_de_propiedad*.</span><span class="sxs-lookup"><span data-stu-id="12b27-285">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="12b27-286">Si no se encuentra ninguna coincidencia, busca uno de los formatos admitidos sin el prefijo.</span><span class="sxs-lookup"><span data-stu-id="12b27-286">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="12b27-287">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="12b27-287">For example:</span></span>

* <span data-ttu-id="12b27-288">Imagine que el parámetro que se va a enlazar es una matriz llamada `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="12b27-288">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="12b27-289">Los datos de cadena de consulta o formulario pueden estar en uno de los formatos siguientes:</span><span class="sxs-lookup"><span data-stu-id="12b27-289">Form or query string data can be in one of the following formats:</span></span>
   
  ```
  selectedCourses=1050&selectedCourses=2000 
  ```

  ```
  selectedCourses[0]=1050&selectedCourses[1]=2000
  ```

  ```
  [0]=1050&[1]=2000
  ```

  ```
  selectedCourses[a]=1050&selectedCourses[b]=2000&selectedCourses.index=a&selectedCourses.index=b
  ```

  ```
  [a]=1050&[b]=2000&index=a&index=b
  ```

* <span data-ttu-id="12b27-290">El formato siguiente solo se admite en datos de formulario:</span><span class="sxs-lookup"><span data-stu-id="12b27-290">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="12b27-291">Para todos los formatos de ejemplo anteriores, el enlace de modelos pasa una matriz de dos elementos al parámetro `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="12b27-291">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="12b27-292">selectedCourses[0]=1050</span><span class="sxs-lookup"><span data-stu-id="12b27-292">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="12b27-293">selectedCourses[1]=2000</span><span class="sxs-lookup"><span data-stu-id="12b27-293">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="12b27-294">Los formatos de datos que usan números de subíndice (... [0] ... [1] ...) deben asegurarse de que se numeran de forma secuencial a partir de cero.</span><span class="sxs-lookup"><span data-stu-id="12b27-294">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="12b27-295">Si hay algún hueco en la numeración de los subíndices, se omiten todos los elementos que aparecen después del hueco.</span><span class="sxs-lookup"><span data-stu-id="12b27-295">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="12b27-296">Por ejemplo, si los subíndices son 0 y 2 en lugar de 0 y 1, se omite el segundo elemento.</span><span class="sxs-lookup"><span data-stu-id="12b27-296">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="12b27-297">Diccionarios</span><span class="sxs-lookup"><span data-stu-id="12b27-297">Dictionaries</span></span>

<span data-ttu-id="12b27-298">Para los destinos `Dictionary`, el enlace de modelos busca coincidencias con *nombre_de_parámetro* o *nombre_de_propiedad*.</span><span class="sxs-lookup"><span data-stu-id="12b27-298">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="12b27-299">Si no se encuentra ninguna coincidencia, busca uno de los formatos admitidos sin el prefijo.</span><span class="sxs-lookup"><span data-stu-id="12b27-299">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="12b27-300">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="12b27-300">For example:</span></span>

* <span data-ttu-id="12b27-301">Imagine que el parámetro de destino es un elemento `Dictionary<int, string>` denominado `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="12b27-301">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="12b27-302">Los datos de cadena de consulta o de formulario publicados pueden ser similares a uno de los ejemplos siguientes:</span><span class="sxs-lookup"><span data-stu-id="12b27-302">The posted form or query string data can look like one of the following examples:</span></span>

  ```
  selectedCourses[1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  [1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  selectedCourses[0].Key=1050&selectedCourses[0].Value=Chemistry&
  selectedCourses[1].Key=2000&selectedCourses[1].Value=Economics
  ```

  ```
  [0].Key=1050&[0].Value=Chemistry&[1].Key=2000&[1].Value=Economics
  ```

* <span data-ttu-id="12b27-303">Para todos los formatos de ejemplo anteriores, el enlace de modelos pasa un diccionario de dos elementos al parámetro `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="12b27-303">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="12b27-304">selectedCourses["1050"]="Chemistry"</span><span class="sxs-lookup"><span data-stu-id="12b27-304">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="12b27-305">selectedCourses["2000"]="Economics"</span><span class="sxs-lookup"><span data-stu-id="12b27-305">selectedCourses["2000"]="Economics"</span></span>

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a><span data-ttu-id="12b27-306">Comportamiento de globalización del enlace de modelos datos de ruta y cadenas de consulta</span><span class="sxs-lookup"><span data-stu-id="12b27-306">Globalization behavior of model binding route data and query strings</span></span>

<span data-ttu-id="12b27-307">El proveedor de valores de ruta de ASP.NET Core y el proveedor de valores de cadena de consulta:</span><span class="sxs-lookup"><span data-stu-id="12b27-307">The ASP.NET Core route value provider and query string value provider:</span></span>

* <span data-ttu-id="12b27-308">Tratan los valores como referencia cultural de todos los idiomas.</span><span class="sxs-lookup"><span data-stu-id="12b27-308">Treat values as invariant culture.</span></span>
* <span data-ttu-id="12b27-309">Esperan que las direcciones URL sean independientes de la referencia cultural.</span><span class="sxs-lookup"><span data-stu-id="12b27-309">Expect that URLs are culture-invariant.</span></span>

<span data-ttu-id="12b27-310">Por el contrario, los valores procedentes de datos de formulario se someten a una conversión que tiene en cuenta la referencia cultural.</span><span class="sxs-lookup"><span data-stu-id="12b27-310">In contrast, values coming from form data undergo a culture-sensitive conversion.</span></span> <span data-ttu-id="12b27-311">Esto es así por diseño, para que las direcciones URL se puedan compartir entre configuraciones regionales.</span><span class="sxs-lookup"><span data-stu-id="12b27-311">This is by design so that URLs are shareable across locales.</span></span>

<span data-ttu-id="12b27-312">Para que el proveedor de valores de ruta de ASP.NET Core y el proveedor de valores de cadena de consulta se sometan a una conversión dependiente de la referencia cultural:</span><span class="sxs-lookup"><span data-stu-id="12b27-312">To make the ASP.NET Core route value provider and query string value provider undergo a culture-sensitive conversion:</span></span>

* <span data-ttu-id="12b27-313">Heredan de <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory>.</span><span class="sxs-lookup"><span data-stu-id="12b27-313">Inherit from <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span></span>
* <span data-ttu-id="12b27-314">Copie el código de [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) o [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span><span class="sxs-lookup"><span data-stu-id="12b27-314">Copy the code from [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) or [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span></span>
* <span data-ttu-id="12b27-315">Reemplace el [valor de referencia cultural](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) pasado al constructor de proveedor de valores con [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span><span class="sxs-lookup"><span data-stu-id="12b27-315">Replace the [culture value](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passed to the value provider constructor with [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span></span>
* <span data-ttu-id="12b27-316">Reemplace el generador de proveedores de valor predeterminado en las opciones de MVC por el nuevo:</span><span class="sxs-lookup"><span data-stu-id="12b27-316">Replace the default value provider factory in MVC options with your new one:</span></span>

[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a><span data-ttu-id="12b27-317">Tipos de datos especiales</span><span class="sxs-lookup"><span data-stu-id="12b27-317">Special data types</span></span>

<span data-ttu-id="12b27-318">Hay algunos tipos de datos especiales que el enlace de modelos puede controlar.</span><span class="sxs-lookup"><span data-stu-id="12b27-318">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="12b27-319">IFormFile e IFormFileCollection</span><span class="sxs-lookup"><span data-stu-id="12b27-319">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="12b27-320">Un archivo cargado incluido en la solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="12b27-320">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="12b27-321">También se admite `IEnumerable<IFormFile>` para varios archivos.</span><span class="sxs-lookup"><span data-stu-id="12b27-321">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="12b27-322">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="12b27-322">CancellationToken</span></span>

<span data-ttu-id="12b27-323">se usa para cancelar la actividad en controladores asincrónicos.</span><span class="sxs-lookup"><span data-stu-id="12b27-323">Used to cancel activity in asynchronous controllers.</span></span>

### <a name="formcollection"></a><span data-ttu-id="12b27-324">FormCollection</span><span class="sxs-lookup"><span data-stu-id="12b27-324">FormCollection</span></span>

<span data-ttu-id="12b27-325">Se usa para recuperar todos los valores de los datos de formulario publicados.</span><span class="sxs-lookup"><span data-stu-id="12b27-325">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="12b27-326">Formateadores de entrada</span><span class="sxs-lookup"><span data-stu-id="12b27-326">Input formatters</span></span>

<span data-ttu-id="12b27-327">Los datos del cuerpo de la solicitud pueden estar en XML, JSON u otro formato.</span><span class="sxs-lookup"><span data-stu-id="12b27-327">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="12b27-328">Para analizar estos datos, el enlace de modelos usa un *formateador de entrada* configurado para controlar un tipo de contenido determinado.</span><span class="sxs-lookup"><span data-stu-id="12b27-328">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="12b27-329">De forma predeterminada, en ASP.NET Core se incluyen formateadores de entrada basados en JSON para el control de los datos JSON.</span><span class="sxs-lookup"><span data-stu-id="12b27-329">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="12b27-330">Puede agregar otros formateadores para otros tipos de contenido.</span><span class="sxs-lookup"><span data-stu-id="12b27-330">You can add other formatters for other content types.</span></span>

<span data-ttu-id="12b27-331">ASP.NET Core selecciona los formateadores de entrada en función del atributo [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute).</span><span class="sxs-lookup"><span data-stu-id="12b27-331">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="12b27-332">Si no hay ningún atributo, usa el [encabezado Content-Type](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span><span class="sxs-lookup"><span data-stu-id="12b27-332">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="12b27-333">Para usar los formateadores de entrada XML integrados:</span><span class="sxs-lookup"><span data-stu-id="12b27-333">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="12b27-334">Instale el paquete NuGet `Microsoft.AspNetCore.Mvc.Formatters.Xml`.</span><span class="sxs-lookup"><span data-stu-id="12b27-334">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="12b27-335">En `Startup.ConfigureServices`, llame a <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> o a <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span><span class="sxs-lookup"><span data-stu-id="12b27-335">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=10)]

* <span data-ttu-id="12b27-336">Aplique el atributo `Consumes` a las clases de controlador o los métodos de acción que deben esperar XML en el cuerpo de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="12b27-336">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="12b27-337">Para más información, vea [Introducción de la serialización XML](/dotnet/standard/serialization/introducing-xml-serialization).</span><span class="sxs-lookup"><span data-stu-id="12b27-337">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

### <a name="customize-model-binding-with-input-formatters"></a><span data-ttu-id="12b27-338">Personalización del enlace de modelos con formateadores de entrada</span><span class="sxs-lookup"><span data-stu-id="12b27-338">Customize model binding with input formatters</span></span>

<span data-ttu-id="12b27-339">Un formateador de entrada asume toda la responsabilidad de leer datos del cuerpo de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="12b27-339">An input formatter takes full responsibility for reading data from the request body.</span></span> <span data-ttu-id="12b27-340">Para personalizar este proceso, configure las API que usa el formateador de entrada.</span><span class="sxs-lookup"><span data-stu-id="12b27-340">To customize this process, configure the APIs used by the input formatter.</span></span> <span data-ttu-id="12b27-341">En esta sección se describe cómo personalizar el formateador de entrada basado en `System.Text.Json` para entender un tipo personalizado denominado `ObjectId`.</span><span class="sxs-lookup"><span data-stu-id="12b27-341">This section describes how to customize the `System.Text.Json`-based input formatter to understand a custom type named `ObjectId`.</span></span> 

<span data-ttu-id="12b27-342">Considere el modelo siguiente, que contiene una propiedad `ObjectId` denominada `Id`:</span><span class="sxs-lookup"><span data-stu-id="12b27-342">Consider the following model, which contains a custom `ObjectId` property named `Id`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ModelWithObjectId.cs?name=snippet_Class&highlight=3)]

<span data-ttu-id="12b27-343">Para personalizar el proceso de enlace de modelos al usar `System.Text.Json`, cree una clase derivada de <xref:System.Text.Json.Serialization.JsonConverter%601>:</span><span class="sxs-lookup"><span data-stu-id="12b27-343">To customize the model binding process when using `System.Text.Json`, create a class derived from <xref:System.Text.Json.Serialization.JsonConverter%601>:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/JsonConverters/ObjectIdConverter.cs?name=snippet_Class)]

<span data-ttu-id="12b27-344">Para usar un convertidor personalizado, aplique el atributo <xref:System.Text.Json.Serialization.JsonConverterAttribute> al tipo.</span><span class="sxs-lookup"><span data-stu-id="12b27-344">To use a custom converter, apply the <xref:System.Text.Json.Serialization.JsonConverterAttribute> attribute to the type.</span></span> <span data-ttu-id="12b27-345">En el ejemplo siguiente, el tipo `ObjectId` se configura con `ObjectIdConverter` como su convertidor personalizado:</span><span class="sxs-lookup"><span data-stu-id="12b27-345">In the following example, the `ObjectId` type is configured with `ObjectIdConverter` as its custom converter:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ObjectId.cs?name=snippet_Class&highlight=1)]

<span data-ttu-id="12b27-346">Para más información, consulte [Procedimientos para escribir convertidores personalizados](/dotnet/standard/serialization/system-text-json-converters-how-to).</span><span class="sxs-lookup"><span data-stu-id="12b27-346">For more information, see [How to write custom converters](/dotnet/standard/serialization/system-text-json-converters-how-to).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="12b27-347">Exclusión de tipos especificados del enlace de modelos</span><span class="sxs-lookup"><span data-stu-id="12b27-347">Exclude specified types from model binding</span></span>

<span data-ttu-id="12b27-348">El comportamiento del enlace de modelos y los sistemas de validación se controla mediante [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span><span class="sxs-lookup"><span data-stu-id="12b27-348">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="12b27-349">Puede personalizar `ModelMetadata` mediante la adición de un proveedor de detalles a [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span><span class="sxs-lookup"><span data-stu-id="12b27-349">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="12b27-350">Los proveedores de detalles integrados están disponibles para deshabilitar el enlace de modelos o la validación para tipos especificados.</span><span class="sxs-lookup"><span data-stu-id="12b27-350">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="12b27-351">Para deshabilitar el enlace de modelos en todos los modelos de un tipo especificado, agregue una instancia de <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="12b27-351">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="12b27-352">Por ejemplo, para deshabilitar el enlace de modelos en todos los modelos del tipo `System.Version`:</span><span class="sxs-lookup"><span data-stu-id="12b27-352">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=5-6)]

<span data-ttu-id="12b27-353">Para deshabilitar la validación en propiedades de un tipo especificado, agregue una instancia de <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="12b27-353">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="12b27-354">Por ejemplo, para deshabilitar la validación en las propiedades de tipo `System.Guid`:</span><span class="sxs-lookup"><span data-stu-id="12b27-354">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=7-8)]

## <a name="custom-model-binders"></a><span data-ttu-id="12b27-355">Enlazadores de modelos personalizados</span><span class="sxs-lookup"><span data-stu-id="12b27-355">Custom model binders</span></span>

<span data-ttu-id="12b27-356">Puede ampliar el enlace de modelos si escribe un enlazador de modelos personalizado y usa el atributo `[ModelBinder]` para seleccionarlo para un destino concreto.</span><span class="sxs-lookup"><span data-stu-id="12b27-356">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="12b27-357">Más información sobre el [enlace de modelos personalizados](xref:mvc/advanced/custom-model-binding).</span><span class="sxs-lookup"><span data-stu-id="12b27-357">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="12b27-358">Enlace de modelos manual</span><span class="sxs-lookup"><span data-stu-id="12b27-358">Manual model binding</span></span> 

<span data-ttu-id="12b27-359">El enlace de modelos se puede invocar de forma manual mediante el método <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>.</span><span class="sxs-lookup"><span data-stu-id="12b27-359">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="12b27-360">El método se define en las clases `ControllerBase` y `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="12b27-360">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="12b27-361">Las sobrecargas de método permiten especificar el prefijo y el proveedor de valores que se van a usar.</span><span class="sxs-lookup"><span data-stu-id="12b27-361">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="12b27-362">El método devuelve `false` si se produce un error en el enlace de modelos.</span><span class="sxs-lookup"><span data-stu-id="12b27-362">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="12b27-363">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="12b27-363">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

<span data-ttu-id="12b27-364"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> usa proveedores de valor para obtener datos del cuerpo del formulario, la cadena de consulta y los datos de ruta.</span><span class="sxs-lookup"><span data-stu-id="12b27-364"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>  uses value providers to get data from the form body, query string, and route data.</span></span> <span data-ttu-id="12b27-365">`TryUpdateModelAsync` suele ser:</span><span class="sxs-lookup"><span data-stu-id="12b27-365">`TryUpdateModelAsync` is typically:</span></span> 

* <span data-ttu-id="12b27-366">Se usa con Razor las páginas y las aplicaciones MVC con controladores y vistas para evitar la publicación excesiva.</span><span class="sxs-lookup"><span data-stu-id="12b27-366">Used with Razor Pages and MVC apps using controllers and views to prevent over-posting.</span></span>
* <span data-ttu-id="12b27-367">No se usa con una API web a menos que se consuma a partir de datos de formulario, cadenas de consulta y datos de ruta.</span><span class="sxs-lookup"><span data-stu-id="12b27-367">Not used with a web API unless consumed from form data, query strings, and route data.</span></span> <span data-ttu-id="12b27-368">Los puntos de conexión de la API web que consumen JSON usan [formateadores de entrada](#input-formatters) para deserializar el cuerpo de la solicitud en un objeto.</span><span class="sxs-lookup"><span data-stu-id="12b27-368">Web API endpoints that consume JSON use [Input formatters](#input-formatters) to deserialize the request body into an object.</span></span>

<span data-ttu-id="12b27-369">Para más información, consulte [TryUpdateModelAsync](xref:data/ef-rp/crud#TryUpdateModelAsync).</span><span class="sxs-lookup"><span data-stu-id="12b27-369">For more information, see [TryUpdateModelAsync](xref:data/ef-rp/crud#TryUpdateModelAsync).</span></span>

## <a name="fromservices-attribute"></a><span data-ttu-id="12b27-370">Atributo [FromServices]</span><span class="sxs-lookup"><span data-stu-id="12b27-370">[FromServices] attribute</span></span>

<span data-ttu-id="12b27-371">El nombre de este atributo sigue el patrón de los atributos de enlace de modelos que especifican un origen de datos.</span><span class="sxs-lookup"><span data-stu-id="12b27-371">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="12b27-372">Pero no se trata de enlazar datos desde un proveedor de valores.</span><span class="sxs-lookup"><span data-stu-id="12b27-372">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="12b27-373">Obtiene una instancia de un tipo desde el contenedor de [inserción de dependencias](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="12b27-373">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="12b27-374">Su objetivo es proporcionar una alternativa a la inserción de constructores cuando se necesita un servicio solo si se llama a un método concreto.</span><span class="sxs-lookup"><span data-stu-id="12b27-374">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="12b27-375">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="12b27-375">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="12b27-376">En este artículo se explica qué es el enlace de modelos, cómo funciona y cómo personalizar su comportamiento.</span><span class="sxs-lookup"><span data-stu-id="12b27-376">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="12b27-377">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="12b27-377">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="12b27-378">Qué es el enlace de modelos</span><span class="sxs-lookup"><span data-stu-id="12b27-378">What is Model binding</span></span>

<span data-ttu-id="12b27-379">Los controladores y Razor las páginas funcionan con los datos que proceden de las solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="12b27-379">Controllers and Razor pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="12b27-380">Por ejemplo, los datos de ruta pueden proporcionar una clave de registro y los campos de formulario publicados pueden proporcionar valores para las propiedades del modelo.</span><span class="sxs-lookup"><span data-stu-id="12b27-380">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="12b27-381">La escritura de código para recuperar cada uno de estos valores y convertirlos de cadenas a tipos de .NET sería tediosa y propensa a errores.</span><span class="sxs-lookup"><span data-stu-id="12b27-381">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="12b27-382">El enlace de modelos automatiza este proceso.</span><span class="sxs-lookup"><span data-stu-id="12b27-382">Model binding automates this process.</span></span> <span data-ttu-id="12b27-383">El sistema de enlace de modelos:</span><span class="sxs-lookup"><span data-stu-id="12b27-383">The model binding system:</span></span>

* <span data-ttu-id="12b27-384">Recupera datos de diversos orígenes, como datos de ruta, campos de formulario y cadenas de consulta.</span><span class="sxs-lookup"><span data-stu-id="12b27-384">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="12b27-385">Proporciona los datos a los controladores y Razor las páginas de los parámetros de método y las propiedades públicas.</span><span class="sxs-lookup"><span data-stu-id="12b27-385">Provides the data to controllers and Razor pages in method parameters and public properties.</span></span>
* <span data-ttu-id="12b27-386">Convierte datos de cadena en tipos de .NET.</span><span class="sxs-lookup"><span data-stu-id="12b27-386">Converts string data to .NET types.</span></span>
* <span data-ttu-id="12b27-387">Actualiza las propiedades de tipos complejos.</span><span class="sxs-lookup"><span data-stu-id="12b27-387">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="12b27-388">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="12b27-388">Example</span></span>

<span data-ttu-id="12b27-389">Imagine que tiene el siguiente método de acción:</span><span class="sxs-lookup"><span data-stu-id="12b27-389">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="12b27-390">Y que la aplicación recibe una solicitud con esta dirección URL:</span><span class="sxs-lookup"><span data-stu-id="12b27-390">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="12b27-391">El enlace de modelos realiza los pasos siguientes después de que el sistema de enrutamiento selecciona el método de acción:</span><span class="sxs-lookup"><span data-stu-id="12b27-391">Model binding goes through the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="12b27-392">Busca el primer parámetro de `GetByID`, un entero denominado `id`.</span><span class="sxs-lookup"><span data-stu-id="12b27-392">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="12b27-393">Examina los orígenes disponibles en la solicitud HTTP y busca `id` = "2" en los datos de ruta.</span><span class="sxs-lookup"><span data-stu-id="12b27-393">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="12b27-394">Convierte la cadena "2" en el entero 2.</span><span class="sxs-lookup"><span data-stu-id="12b27-394">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="12b27-395">Busca el siguiente parámetro de `GetByID`, un valor booleano denominado `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="12b27-395">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="12b27-396">Examina los orígenes y busca "DogsOnly=true" en la cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="12b27-396">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="12b27-397">La coincidencia de nombres no distingue mayúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="12b27-397">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="12b27-398">Convierte la cadena "true" en un valor booleano `true`.</span><span class="sxs-lookup"><span data-stu-id="12b27-398">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="12b27-399">Después, el marco llama al método `GetById` y pasa 2 para el parámetro `id` y `true` para el parámetro `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="12b27-399">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="12b27-400">En el ejemplo anterior, los destinos de enlace de modelos son parámetros de método que son tipos simples.</span><span class="sxs-lookup"><span data-stu-id="12b27-400">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="12b27-401">Los destinos también pueden ser las propiedades de un tipo complejo.</span><span class="sxs-lookup"><span data-stu-id="12b27-401">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="12b27-402">Después de que cada propiedad se haya enlazado correctamente, se produce la [validación de modelos](xref:mvc/models/validation) para esa propiedad.</span><span class="sxs-lookup"><span data-stu-id="12b27-402">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="12b27-403">El registro de qué datos se han enlazado al modelo y los errores de enlace o validación se almacenan en [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) o [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span><span class="sxs-lookup"><span data-stu-id="12b27-403">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="12b27-404">Para averiguar si este proceso se ha realizado correctamente, la aplicación comprueba la marca [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid).</span><span class="sxs-lookup"><span data-stu-id="12b27-404">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="12b27-405">Destinos</span><span class="sxs-lookup"><span data-stu-id="12b27-405">Targets</span></span>

<span data-ttu-id="12b27-406">El enlace de modelos intenta encontrar valores para los tipos de destinos siguientes:</span><span class="sxs-lookup"><span data-stu-id="12b27-406">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="12b27-407">Parámetros del método de acción de controlador al que se enruta una solicitud.</span><span class="sxs-lookup"><span data-stu-id="12b27-407">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="12b27-408">Parámetros del Razor método de control de páginas al que se enruta una solicitud.</span><span class="sxs-lookup"><span data-stu-id="12b27-408">Parameters of the Razor Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="12b27-409">Propiedades públicas de un controlador o una clase `PageModel`, si se especifican mediante atributos.</span><span class="sxs-lookup"><span data-stu-id="12b27-409">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="12b27-410">Atributo [BindProperty]</span><span class="sxs-lookup"><span data-stu-id="12b27-410">[BindProperty] attribute</span></span>

<span data-ttu-id="12b27-411">Se puede aplicar a una propiedad pública de un controlador o una clase `PageModel` para hacer que el enlace de modelos tenga esa propiedad como destino:</span><span class="sxs-lookup"><span data-stu-id="12b27-411">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindpropertiesattribute"></a><span data-ttu-id="12b27-412">Atributo [BindProperties]</span><span class="sxs-lookup"><span data-stu-id="12b27-412">[BindProperties] attribute</span></span>

<span data-ttu-id="12b27-413">Disponible en ASP.NET 2.1 Core y versiones posteriores.</span><span class="sxs-lookup"><span data-stu-id="12b27-413">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="12b27-414">Se pueden aplicar a un controlador o una clase `PageModel` para indicar al enlace de modelos que seleccione como destino todas las propiedades públicas de la clase:</span><span class="sxs-lookup"><span data-stu-id="12b27-414">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="12b27-415">Enlace de modelos para solicitudes HTTP GET</span><span class="sxs-lookup"><span data-stu-id="12b27-415">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="12b27-416">De forma predeterminada, las propiedades no se enlazan para las solicitudes HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="12b27-416">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="12b27-417">Normalmente, todo lo que necesita para una solicitud GET es un parámetro de id. de registro.</span><span class="sxs-lookup"><span data-stu-id="12b27-417">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="12b27-418">El id. de registro se usa para buscar el elemento en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="12b27-418">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="12b27-419">Por tanto, no es necesario enlazar una propiedad que contiene una instancia del modelo.</span><span class="sxs-lookup"><span data-stu-id="12b27-419">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="12b27-420">En escenarios donde quiera propiedades enlazadas a datos de las solicitudes GET, establezca la propiedad `SupportsGet` en `true`:</span><span class="sxs-lookup"><span data-stu-id="12b27-420">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="12b27-421">Orígenes</span><span class="sxs-lookup"><span data-stu-id="12b27-421">Sources</span></span>

<span data-ttu-id="12b27-422">De forma predeterminada, el enlace de modelos obtiene datos en forma de pares clave-valor de los siguientes orígenes de una solicitud HTTP:</span><span class="sxs-lookup"><span data-stu-id="12b27-422">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="12b27-423">Campos de formulario</span><span class="sxs-lookup"><span data-stu-id="12b27-423">Form fields</span></span>
1. <span data-ttu-id="12b27-424">El cuerpo de la solicitud (para [controladores que tienen el atributo [ApiController]](xref:web-api/index#binding-source-parameter-inference)).</span><span class="sxs-lookup"><span data-stu-id="12b27-424">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="12b27-425">Datos de ruta</span><span class="sxs-lookup"><span data-stu-id="12b27-425">Route data</span></span>
1. <span data-ttu-id="12b27-426">Parámetros de cadena de consulta</span><span class="sxs-lookup"><span data-stu-id="12b27-426">Query string parameters</span></span>
1. <span data-ttu-id="12b27-427">Archivos cargados</span><span class="sxs-lookup"><span data-stu-id="12b27-427">Uploaded files</span></span>

<span data-ttu-id="12b27-428">Para cada parámetro o propiedad de destino, se examinan los orígenes en el orden indicado en la lista anterior.</span><span class="sxs-lookup"><span data-stu-id="12b27-428">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span></span> <span data-ttu-id="12b27-429">Hay algunas excepciones:</span><span class="sxs-lookup"><span data-stu-id="12b27-429">There are a few exceptions:</span></span>

* <span data-ttu-id="12b27-430">Los datos de ruta y los valores de cadena de consulta solo se usan para tipos simples.</span><span class="sxs-lookup"><span data-stu-id="12b27-430">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="12b27-431">Los archivos cargados solo se enlazan a tipos de destino que implementan `IFormFile` o `IEnumerable<IFormFile>`.</span><span class="sxs-lookup"><span data-stu-id="12b27-431">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="12b27-432">Si el origen predeterminado no es correcto, use uno de los atributos siguientes para especificar el origen:</span><span class="sxs-lookup"><span data-stu-id="12b27-432">If the default source is not correct, use one of the following attributes to specify the source:</span></span>

* <span data-ttu-id="12b27-433">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute): Obtiene los valores de la cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="12b27-433">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="12b27-434">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute): Obtiene los valores de los datos de ruta.</span><span class="sxs-lookup"><span data-stu-id="12b27-434">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="12b27-435">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute): Obtiene los valores de los campos de formulario publicados.</span><span class="sxs-lookup"><span data-stu-id="12b27-435">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="12b27-436">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute): Obtiene valores del cuerpo de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="12b27-436">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="12b27-437">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute): Obtiene valores de los encabezados HTTP.</span><span class="sxs-lookup"><span data-stu-id="12b27-437">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="12b27-438">Estos atributos:</span><span class="sxs-lookup"><span data-stu-id="12b27-438">These attributes:</span></span>

* <span data-ttu-id="12b27-439">Se agregan de forma individual a las propiedades del modelo (no a la clase de modelo), como en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="12b27-439">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="12b27-440">Opcionalmente, acepte un valor de nombre de modelo en el constructor.</span><span class="sxs-lookup"><span data-stu-id="12b27-440">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="12b27-441">Esta opción se proporciona en caso de que el nombre de la propiedad no coincida con el valor de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="12b27-441">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="12b27-442">Por ejemplo, es posible que el valor de la solicitud sea un encabezado con un guion en el nombre, como en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="12b27-442">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="12b27-443">Atributo [FromBody]</span><span class="sxs-lookup"><span data-stu-id="12b27-443">[FromBody] attribute</span></span>

<span data-ttu-id="12b27-444">Aplique el atributo `[FromBody]` a un parámetro para rellenar sus propiedades desde el cuerpo de una solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="12b27-444">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span></span> <span data-ttu-id="12b27-445">El runtime de ASP.NET Core delega la responsabilidad de leer el cuerpo al formateador de entrada.</span><span class="sxs-lookup"><span data-stu-id="12b27-445">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span></span> <span data-ttu-id="12b27-446">Los formateadores de entrada se explican [más adelante en este artículo](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="12b27-446">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="12b27-447">Cuando se aplica `[FromBody]` a un parámetro de tipo complejo, se omiten los atributos de origen de enlace aplicados a sus propiedades.</span><span class="sxs-lookup"><span data-stu-id="12b27-447">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span></span> <span data-ttu-id="12b27-448">Por ejemplo, la acción `Create` siguiente especifica que su parámetro `pet` se rellena a partir del cuerpo:</span><span class="sxs-lookup"><span data-stu-id="12b27-448">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span></span>

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

<span data-ttu-id="12b27-449">La clase `Pet` especifica que su propiedad `Breed` se rellena a partir de un parámetro de cadena de consulta:</span><span class="sxs-lookup"><span data-stu-id="12b27-449">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

<span data-ttu-id="12b27-450">En el ejemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="12b27-450">In the preceding example:</span></span>

* <span data-ttu-id="12b27-451">El atributo `[FromQuery]` se ignora.</span><span class="sxs-lookup"><span data-stu-id="12b27-451">The `[FromQuery]` attribute is ignored.</span></span>
* <span data-ttu-id="12b27-452">La propiedad `Breed` no se rellena desde un parámetro de cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="12b27-452">The `Breed` property is not populated from a query string parameter.</span></span> 

<span data-ttu-id="12b27-453">Los formateadores de entrada solo leen el cuerpo y no entienden los atributos de origen de enlace.</span><span class="sxs-lookup"><span data-stu-id="12b27-453">Input formatters read only the body and don't understand binding source attributes.</span></span> <span data-ttu-id="12b27-454">Si se encuentra un valor adecuado en el cuerpo, ese valor se usa para rellenar la propiedad `Breed`.</span><span class="sxs-lookup"><span data-stu-id="12b27-454">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span></span>

<span data-ttu-id="12b27-455">No aplique `[FromBody]` a más de un parámetro por método de acción.</span><span class="sxs-lookup"><span data-stu-id="12b27-455">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="12b27-456">Una vez que un formateador de entrada ha leído la secuencia de solicitudes, deja de estar disponible para una nueva lectura con el fin de enlazar otros parámetros `[FromBody]`.</span><span class="sxs-lookup"><span data-stu-id="12b27-456">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="12b27-457">Orígenes adicionales</span><span class="sxs-lookup"><span data-stu-id="12b27-457">Additional sources</span></span>

<span data-ttu-id="12b27-458">Los *proveedores de valores* proporcionan datos de origen al sistema de enlace de modelos.</span><span class="sxs-lookup"><span data-stu-id="12b27-458">Source data is provided to the model binding system by *value providers*.</span></span> <span data-ttu-id="12b27-459">Puede escribir y registrar proveedores de valores personalizados que obtienen datos de otros orígenes para el enlace de modelos.</span><span class="sxs-lookup"><span data-stu-id="12b27-459">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="12b27-460">Por ejemplo, es posible que le interesen datos de cookies o del estado de sesión.</span><span class="sxs-lookup"><span data-stu-id="12b27-460">For example, you might want data from cookies or session state.</span></span> <span data-ttu-id="12b27-461">Para obtener datos desde un origen nuevo:</span><span class="sxs-lookup"><span data-stu-id="12b27-461">To get data from a new source:</span></span>

* <span data-ttu-id="12b27-462">Cree una clase que implemente `IValueProvider`.</span><span class="sxs-lookup"><span data-stu-id="12b27-462">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="12b27-463">Cree una clase que implemente `IValueProviderFactory`.</span><span class="sxs-lookup"><span data-stu-id="12b27-463">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="12b27-464">Registre la clase de generador en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="12b27-464">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="12b27-465">En la aplicación de ejemplo se incluye un [proveedor de valores](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProvider.cs) y un [generador](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProviderFactory.cs) que obtiene valores de cookies.</span><span class="sxs-lookup"><span data-stu-id="12b27-465">The sample app includes a [value provider](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProvider.cs) and [factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProviderFactory.cs) example that gets values from cookies.</span></span> <span data-ttu-id="12b27-466">Este es el código de registro de `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="12b27-466">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=3)]

<span data-ttu-id="12b27-467">En el código mostrado, el proveedor de valor personalizado se coloca después de todos los proveedores de valor integrados.</span><span class="sxs-lookup"><span data-stu-id="12b27-467">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="12b27-468">Para que sea el primero en la lista, llame a `Insert(0, new CookieValueProviderFactory())` en lugar de a `Add`.</span><span class="sxs-lookup"><span data-stu-id="12b27-468">To make it the first in the list, call `Insert(0, new CookieValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="12b27-469">No hay origen para una propiedad de modelo</span><span class="sxs-lookup"><span data-stu-id="12b27-469">No source for a model property</span></span>

<span data-ttu-id="12b27-470">De forma predeterminada, si no se encuentra ningún valor para una propiedad de modelo no se crea un error de estado del modelo.</span><span class="sxs-lookup"><span data-stu-id="12b27-470">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="12b27-471">La propiedad se establece en NULL o en un valor predeterminado:</span><span class="sxs-lookup"><span data-stu-id="12b27-471">The property is set to null or a default value:</span></span>

* <span data-ttu-id="12b27-472">Los tipos simples que aceptan valores NULL se establecen en `null`.</span><span class="sxs-lookup"><span data-stu-id="12b27-472">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="12b27-473">Los tipos de valor que no aceptan valores NULL se establecen en `default(T)`.</span><span class="sxs-lookup"><span data-stu-id="12b27-473">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="12b27-474">Por ejemplo, un parámetro `int id` se establece en 0.</span><span class="sxs-lookup"><span data-stu-id="12b27-474">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="12b27-475">Para los tipos complejos, el enlace de modelos crea una instancia mediante el constructor predeterminado, sin establecer propiedades.</span><span class="sxs-lookup"><span data-stu-id="12b27-475">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="12b27-476">Las matrices se establecen en `Array.Empty<T>()`, salvo las matrices `byte[]`, que se establecen en `null`.</span><span class="sxs-lookup"><span data-stu-id="12b27-476">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="12b27-477">Si el estado del modelo se debe invalidar Cuando no se encuentra nada en los campos de formulario de una propiedad de modelo, utilice el [`[BindRequired]`](#bindrequired-attribute) atributo.</span><span class="sxs-lookup"><span data-stu-id="12b27-477">If model state should be invalidated when nothing is found in form fields for a model property, use the [`[BindRequired]`](#bindrequired-attribute) attribute.</span></span>

<span data-ttu-id="12b27-478">Tenga en cuenta que este comportamiento de `[BindRequired]` se aplica al enlace de modelos desde datos de formulario publicados, no a los datos JSON o XML del cuerpo de una solicitud.</span><span class="sxs-lookup"><span data-stu-id="12b27-478">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="12b27-479">Los datos del cuerpo de la solicitud se controlan mediante [formateadores de entrada](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="12b27-479">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="12b27-480">Errores de la conversión de tipos</span><span class="sxs-lookup"><span data-stu-id="12b27-480">Type conversion errors</span></span>

<span data-ttu-id="12b27-481">Si se encuentra un origen pero no se puede convertir al tipo de destino, el estado del modelo se marca como no válido.</span><span class="sxs-lookup"><span data-stu-id="12b27-481">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="12b27-482">El parámetro o la propiedad de destino se establece en NULL o en un valor predeterminado, como se ha indicado en la sección anterior.</span><span class="sxs-lookup"><span data-stu-id="12b27-482">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="12b27-483">En un controlador de API que tenga el atributo `[ApiController]`, el estado de modelo no válido genera una respuesta HTTP 400 automática.</span><span class="sxs-lookup"><span data-stu-id="12b27-483">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="12b27-484">En una Razor página, vuelva a mostrar la página con un mensaje de error:</span><span class="sxs-lookup"><span data-stu-id="12b27-484">In a Razor page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="12b27-485">La validación del lado cliente detecta la mayoría de los datos incorrectos que de otro modo se enviarían a un Razor formulario de páginas.</span><span class="sxs-lookup"><span data-stu-id="12b27-485">Client-side validation catches most bad data that would otherwise be submitted to a Razor Pages form.</span></span> <span data-ttu-id="12b27-486">Esta validación dificulta que se pueda desencadenar el código resaltado anterior.</span><span class="sxs-lookup"><span data-stu-id="12b27-486">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="12b27-487">En la aplicación de ejemplo se incluye un botón **Submit with Invalid Date** (Enviar con fecha no válida) que agrega datos incorrectos al campo **Hire Date** (Fecha de contratación) y envía el formulario.</span><span class="sxs-lookup"><span data-stu-id="12b27-487">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="12b27-488">Este botón muestra cómo funciona el código para volver a mostrar la página cuando se producen errores de conversión de datos.</span><span class="sxs-lookup"><span data-stu-id="12b27-488">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="12b27-489">Cuando el código anterior vuelve a mostrar la página, no se muestra la entrada no válida en el campo de formulario.</span><span class="sxs-lookup"><span data-stu-id="12b27-489">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="12b27-490">El motivo es que la propiedad de modelo se ha establecido en NULL o en un valor predeterminado.</span><span class="sxs-lookup"><span data-stu-id="12b27-490">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="12b27-491">La entrada no válida sí aparece en un mensaje de error.</span><span class="sxs-lookup"><span data-stu-id="12b27-491">The invalid input does appear in an error message.</span></span> <span data-ttu-id="12b27-492">Pero si quiere volver a mostrar los datos incorrectos en el campo de formulario, considere la posibilidad de convertir la propiedad de modelo en una cadena y realizar la conversión de datos de forma manual.</span><span class="sxs-lookup"><span data-stu-id="12b27-492">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="12b27-493">Se recomienda la misma estrategia si no quiere que los errores de conversión de tipo generen errores de estado de modelo.</span><span class="sxs-lookup"><span data-stu-id="12b27-493">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="12b27-494">En ese caso, convierta la propiedad de modelo en una cadena.</span><span class="sxs-lookup"><span data-stu-id="12b27-494">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="12b27-495">Tipos simples</span><span class="sxs-lookup"><span data-stu-id="12b27-495">Simple types</span></span>

<span data-ttu-id="12b27-496">Los tipos simples a los que el enlazador de modelos puede convertir las cadenas de origen incluyen los siguientes:</span><span class="sxs-lookup"><span data-stu-id="12b27-496">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="12b27-497">Boolean</span><span class="sxs-lookup"><span data-stu-id="12b27-497">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="12b27-498">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="12b27-498">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="12b27-499">Char</span><span class="sxs-lookup"><span data-stu-id="12b27-499">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="12b27-500">DateTime</span><span class="sxs-lookup"><span data-stu-id="12b27-500">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="12b27-501">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="12b27-501">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="12b27-502">Decimal</span><span class="sxs-lookup"><span data-stu-id="12b27-502">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="12b27-503">Double</span><span class="sxs-lookup"><span data-stu-id="12b27-503">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="12b27-504">Enumeración</span><span class="sxs-lookup"><span data-stu-id="12b27-504">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="12b27-505">Volumen</span><span class="sxs-lookup"><span data-stu-id="12b27-505">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="12b27-506">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="12b27-506">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="12b27-507">Single</span><span class="sxs-lookup"><span data-stu-id="12b27-507">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="12b27-508">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="12b27-508">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="12b27-509">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="12b27-509">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="12b27-510">Uri</span><span class="sxs-lookup"><span data-stu-id="12b27-510">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="12b27-511">Versión</span><span class="sxs-lookup"><span data-stu-id="12b27-511">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="12b27-512">Tipos complejos</span><span class="sxs-lookup"><span data-stu-id="12b27-512">Complex types</span></span>

<span data-ttu-id="12b27-513">Un tipo complejo debe tener un constructor público predeterminado y propiedades grabables públicas para enlazar.</span><span class="sxs-lookup"><span data-stu-id="12b27-513">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="12b27-514">Cuando se produce el enlace de modelos, se crea una instancia de la clase con el constructor predeterminado público.</span><span class="sxs-lookup"><span data-stu-id="12b27-514">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="12b27-515">Para cada propiedad del tipo complejo, el enlace de modelos busca entre los orígenes el patrón de nombre *prefijo.nombre_de_propiedad*.</span><span class="sxs-lookup"><span data-stu-id="12b27-515">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name*.</span></span> <span data-ttu-id="12b27-516">Si no se encuentra nada, solo busca *nombre_de_propiedad* sin el prefijo.</span><span class="sxs-lookup"><span data-stu-id="12b27-516">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="12b27-517">Para el enlace a un parámetro, el prefijo es el nombre del parámetro.</span><span class="sxs-lookup"><span data-stu-id="12b27-517">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="12b27-518">Para el enlace a una propiedad pública `PageModel`, el prefijo es el nombre de la propiedad pública.</span><span class="sxs-lookup"><span data-stu-id="12b27-518">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="12b27-519">Algunos atributos tienen una propiedad `Prefix` que permite invalidar el uso predeterminado del nombre de parámetro o propiedad.</span><span class="sxs-lookup"><span data-stu-id="12b27-519">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="12b27-520">Por ejemplo, imagine que el tipo complejo es la clase `Instructor` siguiente:</span><span class="sxs-lookup"><span data-stu-id="12b27-520">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="12b27-521">Prefijo = nombre del parámetro</span><span class="sxs-lookup"><span data-stu-id="12b27-521">Prefix = parameter name</span></span>

<span data-ttu-id="12b27-522">Si el modelo que se va a enlazar es un parámetro denominado `instructorToUpdate`:</span><span class="sxs-lookup"><span data-stu-id="12b27-522">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="12b27-523">El enlace de modelos se inicia mediante el examen de los orígenes para la clave `instructorToUpdate.ID`.</span><span class="sxs-lookup"><span data-stu-id="12b27-523">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="12b27-524">Si no se encuentra, busca `ID` sin un prefijo.</span><span class="sxs-lookup"><span data-stu-id="12b27-524">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="12b27-525">Prefijo = nombre de propiedad</span><span class="sxs-lookup"><span data-stu-id="12b27-525">Prefix = property name</span></span>

<span data-ttu-id="12b27-526">Si el modelo que se va a enlazar es una propiedad denominada `Instructor` del controlador o la clase `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="12b27-526">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="12b27-527">El enlace de modelos se inicia mediante el examen de los orígenes para la clave `Instructor.ID`.</span><span class="sxs-lookup"><span data-stu-id="12b27-527">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="12b27-528">Si no se encuentra, busca `ID` sin un prefijo.</span><span class="sxs-lookup"><span data-stu-id="12b27-528">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="12b27-529">Prefijo personalizado</span><span class="sxs-lookup"><span data-stu-id="12b27-529">Custom prefix</span></span>

<span data-ttu-id="12b27-530">Si el modelo que se va a enlazar es un parámetro denominado `instructorToUpdate` y un atributo `Bind`, especifica `Instructor` como el prefijo:</span><span class="sxs-lookup"><span data-stu-id="12b27-530">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="12b27-531">El enlace de modelos se inicia mediante el examen de los orígenes para la clave `Instructor.ID`.</span><span class="sxs-lookup"><span data-stu-id="12b27-531">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="12b27-532">Si no se encuentra, busca `ID` sin un prefijo.</span><span class="sxs-lookup"><span data-stu-id="12b27-532">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="12b27-533">Atributos para destinos de tipo complejo</span><span class="sxs-lookup"><span data-stu-id="12b27-533">Attributes for complex type targets</span></span>

<span data-ttu-id="12b27-534">Existen varios atributos integrados para controlar el enlace de modelos de tipos complejos:</span><span class="sxs-lookup"><span data-stu-id="12b27-534">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[BindRequired]`
* `[BindNever]`
* `[Bind]`

> [!NOTE]
> <span data-ttu-id="12b27-535">Estos atributos afectan al enlace de modelos cuando el origen de los valores son datos de formulario publicados.</span><span class="sxs-lookup"><span data-stu-id="12b27-535">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="12b27-536">No afectan a los formateadores de entrada, que procesan los cuerpos de la solicitud JSON y XML publicados.</span><span class="sxs-lookup"><span data-stu-id="12b27-536">They do not affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="12b27-537">Los formateadores de entrada se explican [más adelante en este artículo](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="12b27-537">Input formatters are explained [later in this article](#input-formatters).</span></span>
>
> <span data-ttu-id="12b27-538">Vea también la explicación del atributo `[Required]` en [Validación de modelos](xref:mvc/models/validation#required-attribute).</span><span class="sxs-lookup"><span data-stu-id="12b27-538">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindrequired-attribute"></a><span data-ttu-id="12b27-539">Atributo [BindRequired]</span><span class="sxs-lookup"><span data-stu-id="12b27-539">[BindRequired] attribute</span></span>

<span data-ttu-id="12b27-540">Solo se puede aplicar a propiedades del modelo, no a parámetros de método.</span><span class="sxs-lookup"><span data-stu-id="12b27-540">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="12b27-541">Hace que el enlace de modelos agregue un error de estado de modelo si no se puede realizar el enlace para la propiedad de un modelo.</span><span class="sxs-lookup"><span data-stu-id="12b27-541">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="12b27-542">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="12b27-542">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

### <a name="bindnever-attribute"></a><span data-ttu-id="12b27-543">Atributo [BindNever]</span><span class="sxs-lookup"><span data-stu-id="12b27-543">[BindNever] attribute</span></span>

<span data-ttu-id="12b27-544">Solo se puede aplicar a propiedades del modelo, no a parámetros de método.</span><span class="sxs-lookup"><span data-stu-id="12b27-544">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="12b27-545">Impide que el enlace de modelos establezca la propiedad de un modelo.</span><span class="sxs-lookup"><span data-stu-id="12b27-545">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="12b27-546">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="12b27-546">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

### <a name="bind-attribute"></a><span data-ttu-id="12b27-547">Atributo [Bind]</span><span class="sxs-lookup"><span data-stu-id="12b27-547">[Bind] attribute</span></span>

<span data-ttu-id="12b27-548">Se puede aplicar a una clase o un parámetro de método.</span><span class="sxs-lookup"><span data-stu-id="12b27-548">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="12b27-549">Especifica qué propiedades de un modelo se deben incluir en el enlace de modelos.</span><span class="sxs-lookup"><span data-stu-id="12b27-549">Specifies which properties of a model should be included in model binding.</span></span>

<span data-ttu-id="12b27-550">En el ejemplo siguiente, solo se enlazan las propiedades especificadas del modelo `Instructor` cuando se llama a cualquier método de acción o controlador:</span><span class="sxs-lookup"><span data-stu-id="12b27-550">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="12b27-551">En el ejemplo siguiente, solo se enlazan las propiedades especificadas del modelo `Instructor` cuando se llama al método `OnPost`:</span><span class="sxs-lookup"><span data-stu-id="12b27-551">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="12b27-552">El atributo `[Bind]` se puede usar para protegerse de la publicación excesiva en escenarios de *creación*.</span><span class="sxs-lookup"><span data-stu-id="12b27-552">The `[Bind]` attribute can be used to protect against overposting in *create* scenarios.</span></span> <span data-ttu-id="12b27-553">No funciona bien en escenarios de edición porque las propiedades excluidas se establecen en NULL o en un valor predeterminado en lugar de mantenerse sin cambios.</span><span class="sxs-lookup"><span data-stu-id="12b27-553">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="12b27-554">Para defenderse de la publicación excesiva, se recomiendan modelos de vista en lugar del atributo `[Bind]`.</span><span class="sxs-lookup"><span data-stu-id="12b27-554">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="12b27-555">Para más información, vea [Nota de seguridad sobre la publicación excesiva](xref:data/ef-mvc/crud#security-note-about-overposting).</span><span class="sxs-lookup"><span data-stu-id="12b27-555">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

## <a name="collections"></a><span data-ttu-id="12b27-556">Colecciones</span><span class="sxs-lookup"><span data-stu-id="12b27-556">Collections</span></span>

<span data-ttu-id="12b27-557">Para los destinos que son colecciones de tipos simples, el enlace de modelos busca coincidencias con *nombre_de_parámetro* o *nombre_de_propiedad*.</span><span class="sxs-lookup"><span data-stu-id="12b27-557">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="12b27-558">Si no se encuentra ninguna coincidencia, busca uno de los formatos admitidos sin el prefijo.</span><span class="sxs-lookup"><span data-stu-id="12b27-558">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="12b27-559">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="12b27-559">For example:</span></span>

* <span data-ttu-id="12b27-560">Imagine que el parámetro que se va a enlazar es una matriz llamada `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="12b27-560">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="12b27-561">Los datos de cadena de consulta o formulario pueden estar en uno de los formatos siguientes:</span><span class="sxs-lookup"><span data-stu-id="12b27-561">Form or query string data can be in one of the following formats:</span></span>
   
  ```
  selectedCourses=1050&selectedCourses=2000 
  ```

  ```
  selectedCourses[0]=1050&selectedCourses[1]=2000
  ```

  ```
  [0]=1050&[1]=2000
  ```

  ```
  selectedCourses[a]=1050&selectedCourses[b]=2000&selectedCourses.index=a&selectedCourses.index=b
  ```

  ```
  [a]=1050&[b]=2000&index=a&index=b
  ```

* <span data-ttu-id="12b27-562">El formato siguiente solo se admite en datos de formulario:</span><span class="sxs-lookup"><span data-stu-id="12b27-562">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="12b27-563">Para todos los formatos de ejemplo anteriores, el enlace de modelos pasa una matriz de dos elementos al parámetro `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="12b27-563">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="12b27-564">selectedCourses[0]=1050</span><span class="sxs-lookup"><span data-stu-id="12b27-564">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="12b27-565">selectedCourses[1]=2000</span><span class="sxs-lookup"><span data-stu-id="12b27-565">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="12b27-566">Los formatos de datos que usan números de subíndice (... [0] ... [1] ...) deben asegurarse de que se numeran de forma secuencial a partir de cero.</span><span class="sxs-lookup"><span data-stu-id="12b27-566">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="12b27-567">Si hay algún hueco en la numeración de los subíndices, se omiten todos los elementos que aparecen después del hueco.</span><span class="sxs-lookup"><span data-stu-id="12b27-567">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="12b27-568">Por ejemplo, si los subíndices son 0 y 2 en lugar de 0 y 1, se omite el segundo elemento.</span><span class="sxs-lookup"><span data-stu-id="12b27-568">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="12b27-569">Diccionarios</span><span class="sxs-lookup"><span data-stu-id="12b27-569">Dictionaries</span></span>

<span data-ttu-id="12b27-570">Para los destinos `Dictionary`, el enlace de modelos busca coincidencias con *nombre_de_parámetro* o *nombre_de_propiedad*.</span><span class="sxs-lookup"><span data-stu-id="12b27-570">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="12b27-571">Si no se encuentra ninguna coincidencia, busca uno de los formatos admitidos sin el prefijo.</span><span class="sxs-lookup"><span data-stu-id="12b27-571">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="12b27-572">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="12b27-572">For example:</span></span>

* <span data-ttu-id="12b27-573">Imagine que el parámetro de destino es un elemento `Dictionary<int, string>` denominado `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="12b27-573">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="12b27-574">Los datos de cadena de consulta o de formulario publicados pueden ser similares a uno de los ejemplos siguientes:</span><span class="sxs-lookup"><span data-stu-id="12b27-574">The posted form or query string data can look like one of the following examples:</span></span>

  ```
  selectedCourses[1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  [1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  selectedCourses[0].Key=1050&selectedCourses[0].Value=Chemistry&
  selectedCourses[1].Key=2000&selectedCourses[1].Value=Economics
  ```

  ```
  [0].Key=1050&[0].Value=Chemistry&[1].Key=2000&[1].Value=Economics
  ```

* <span data-ttu-id="12b27-575">Para todos los formatos de ejemplo anteriores, el enlace de modelos pasa un diccionario de dos elementos al parámetro `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="12b27-575">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="12b27-576">selectedCourses["1050"]="Chemistry"</span><span class="sxs-lookup"><span data-stu-id="12b27-576">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="12b27-577">selectedCourses["2000"]="Economics"</span><span class="sxs-lookup"><span data-stu-id="12b27-577">selectedCourses["2000"]="Economics"</span></span>

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a><span data-ttu-id="12b27-578">Comportamiento de globalización del enlace de modelos datos de ruta y cadenas de consulta</span><span class="sxs-lookup"><span data-stu-id="12b27-578">Globalization behavior of model binding route data and query strings</span></span>

<span data-ttu-id="12b27-579">El proveedor de valores de ruta de ASP.NET Core y el proveedor de valores de cadena de consulta:</span><span class="sxs-lookup"><span data-stu-id="12b27-579">The ASP.NET Core route value provider and query string value provider:</span></span>

* <span data-ttu-id="12b27-580">Tratan los valores como referencia cultural de todos los idiomas.</span><span class="sxs-lookup"><span data-stu-id="12b27-580">Treat values as invariant culture.</span></span>
* <span data-ttu-id="12b27-581">Esperan que las direcciones URL sean independientes de la referencia cultural.</span><span class="sxs-lookup"><span data-stu-id="12b27-581">Expect that URLs are culture-invariant.</span></span>

<span data-ttu-id="12b27-582">Por el contrario, los valores procedentes de datos de formulario se someten a una conversión que tiene en cuenta la referencia cultural.</span><span class="sxs-lookup"><span data-stu-id="12b27-582">In contrast, values coming from form data undergo a culture-sensitive conversion.</span></span> <span data-ttu-id="12b27-583">Esto es así por diseño, para que las direcciones URL se puedan compartir entre configuraciones regionales.</span><span class="sxs-lookup"><span data-stu-id="12b27-583">This is by design so that URLs are shareable across locales.</span></span>

<span data-ttu-id="12b27-584">Para que el proveedor de valores de ruta de ASP.NET Core y el proveedor de valores de cadena de consulta se sometan a una conversión dependiente de la referencia cultural:</span><span class="sxs-lookup"><span data-stu-id="12b27-584">To make the ASP.NET Core route value provider and query string value provider undergo a culture-sensitive conversion:</span></span>

* <span data-ttu-id="12b27-585">Heredan de <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory>.</span><span class="sxs-lookup"><span data-stu-id="12b27-585">Inherit from <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span></span>
* <span data-ttu-id="12b27-586">Copie el código de [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) o [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span><span class="sxs-lookup"><span data-stu-id="12b27-586">Copy the code from [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) or [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span></span>
* <span data-ttu-id="12b27-587">Reemplace el [valor de referencia cultural](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) pasado al constructor de proveedor de valores con [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span><span class="sxs-lookup"><span data-stu-id="12b27-587">Replace the [culture value](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passed to the value provider constructor with [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span></span>
* <span data-ttu-id="12b27-588">Reemplace el generador de proveedores de valor predeterminado en las opciones de MVC por el nuevo:</span><span class="sxs-lookup"><span data-stu-id="12b27-588">Replace the default value provider factory in MVC options with your new one:</span></span>

[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a><span data-ttu-id="12b27-589">Tipos de datos especiales</span><span class="sxs-lookup"><span data-stu-id="12b27-589">Special data types</span></span>

<span data-ttu-id="12b27-590">Hay algunos tipos de datos especiales que el enlace de modelos puede controlar.</span><span class="sxs-lookup"><span data-stu-id="12b27-590">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="12b27-591">IFormFile e IFormFileCollection</span><span class="sxs-lookup"><span data-stu-id="12b27-591">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="12b27-592">Un archivo cargado incluido en la solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="12b27-592">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="12b27-593">También se admite `IEnumerable<IFormFile>` para varios archivos.</span><span class="sxs-lookup"><span data-stu-id="12b27-593">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="12b27-594">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="12b27-594">CancellationToken</span></span>

<span data-ttu-id="12b27-595">se usa para cancelar la actividad en controladores asincrónicos.</span><span class="sxs-lookup"><span data-stu-id="12b27-595">Used to cancel activity in asynchronous controllers.</span></span>

### <a name="formcollection"></a><span data-ttu-id="12b27-596">FormCollection</span><span class="sxs-lookup"><span data-stu-id="12b27-596">FormCollection</span></span>

<span data-ttu-id="12b27-597">Se usa para recuperar todos los valores de los datos de formulario publicados.</span><span class="sxs-lookup"><span data-stu-id="12b27-597">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="12b27-598">Formateadores de entrada</span><span class="sxs-lookup"><span data-stu-id="12b27-598">Input formatters</span></span>

<span data-ttu-id="12b27-599">Los datos del cuerpo de la solicitud pueden estar en XML, JSON u otro formato.</span><span class="sxs-lookup"><span data-stu-id="12b27-599">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="12b27-600">Para analizar estos datos, el enlace de modelos usa un *formateador de entrada* configurado para controlar un tipo de contenido determinado.</span><span class="sxs-lookup"><span data-stu-id="12b27-600">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="12b27-601">De forma predeterminada, en ASP.NET Core se incluyen formateadores de entrada basados en JSON para el control de los datos JSON.</span><span class="sxs-lookup"><span data-stu-id="12b27-601">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="12b27-602">Puede agregar otros formateadores para otros tipos de contenido.</span><span class="sxs-lookup"><span data-stu-id="12b27-602">You can add other formatters for other content types.</span></span>

<span data-ttu-id="12b27-603">ASP.NET Core selecciona los formateadores de entrada en función del atributo [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute).</span><span class="sxs-lookup"><span data-stu-id="12b27-603">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="12b27-604">Si no hay ningún atributo, usa el [encabezado Content-Type](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span><span class="sxs-lookup"><span data-stu-id="12b27-604">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="12b27-605">Para usar los formateadores de entrada XML integrados:</span><span class="sxs-lookup"><span data-stu-id="12b27-605">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="12b27-606">Instale el paquete NuGet `Microsoft.AspNetCore.Mvc.Formatters.Xml`.</span><span class="sxs-lookup"><span data-stu-id="12b27-606">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="12b27-607">En `Startup.ConfigureServices`, llame a <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> o a <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span><span class="sxs-lookup"><span data-stu-id="12b27-607">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=9)]

* <span data-ttu-id="12b27-608">Aplique el atributo `Consumes` a las clases de controlador o los métodos de acción que deben esperar XML en el cuerpo de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="12b27-608">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="12b27-609">Para más información, vea [Introducción de la serialización XML](/dotnet/standard/serialization/introducing-xml-serialization).</span><span class="sxs-lookup"><span data-stu-id="12b27-609">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="12b27-610">Exclusión de tipos especificados del enlace de modelos</span><span class="sxs-lookup"><span data-stu-id="12b27-610">Exclude specified types from model binding</span></span>

<span data-ttu-id="12b27-611">El comportamiento del enlace de modelos y los sistemas de validación se controla mediante [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span><span class="sxs-lookup"><span data-stu-id="12b27-611">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="12b27-612">Puede personalizar `ModelMetadata` mediante la adición de un proveedor de detalles a [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span><span class="sxs-lookup"><span data-stu-id="12b27-612">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="12b27-613">Los proveedores de detalles integrados están disponibles para deshabilitar el enlace de modelos o la validación para tipos especificados.</span><span class="sxs-lookup"><span data-stu-id="12b27-613">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="12b27-614">Para deshabilitar el enlace de modelos en todos los modelos de un tipo especificado, agregue una instancia de <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="12b27-614">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="12b27-615">Por ejemplo, para deshabilitar el enlace de modelos en todos los modelos del tipo `System.Version`:</span><span class="sxs-lookup"><span data-stu-id="12b27-615">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4-5)]

<span data-ttu-id="12b27-616">Para deshabilitar la validación en propiedades de un tipo especificado, agregue una instancia de <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="12b27-616">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="12b27-617">Por ejemplo, para deshabilitar la validación en las propiedades de tipo `System.Guid`:</span><span class="sxs-lookup"><span data-stu-id="12b27-617">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=6-7)]

## <a name="custom-model-binders"></a><span data-ttu-id="12b27-618">Enlazadores de modelos personalizados</span><span class="sxs-lookup"><span data-stu-id="12b27-618">Custom model binders</span></span>

<span data-ttu-id="12b27-619">Puede ampliar el enlace de modelos si escribe un enlazador de modelos personalizado y usa el atributo `[ModelBinder]` para seleccionarlo para un destino concreto.</span><span class="sxs-lookup"><span data-stu-id="12b27-619">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="12b27-620">Más información sobre el [enlace de modelos personalizados](xref:mvc/advanced/custom-model-binding).</span><span class="sxs-lookup"><span data-stu-id="12b27-620">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="12b27-621">Enlace de modelos manual</span><span class="sxs-lookup"><span data-stu-id="12b27-621">Manual model binding</span></span>

<span data-ttu-id="12b27-622">El enlace de modelos se puede invocar de forma manual mediante el método <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>.</span><span class="sxs-lookup"><span data-stu-id="12b27-622">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="12b27-623">El método se define en las clases `ControllerBase` y `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="12b27-623">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="12b27-624">Las sobrecargas de método permiten especificar el prefijo y el proveedor de valores que se van a usar.</span><span class="sxs-lookup"><span data-stu-id="12b27-624">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="12b27-625">El método devuelve `false` si se produce un error en el enlace de modelos.</span><span class="sxs-lookup"><span data-stu-id="12b27-625">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="12b27-626">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="12b27-626">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

## <a name="fromservices-attribute"></a><span data-ttu-id="12b27-627">Atributo [FromServices]</span><span class="sxs-lookup"><span data-stu-id="12b27-627">[FromServices] attribute</span></span>

<span data-ttu-id="12b27-628">El nombre de este atributo sigue el patrón de los atributos de enlace de modelos que especifican un origen de datos.</span><span class="sxs-lookup"><span data-stu-id="12b27-628">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="12b27-629">Pero no se trata de enlazar datos desde un proveedor de valores.</span><span class="sxs-lookup"><span data-stu-id="12b27-629">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="12b27-630">Obtiene una instancia de un tipo desde el contenedor de [inserción de dependencias](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="12b27-630">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="12b27-631">Su objetivo es proporcionar una alternativa a la inserción de constructores cuando se necesita un servicio solo si se llama a un método concreto.</span><span class="sxs-lookup"><span data-stu-id="12b27-631">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="12b27-632">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="12b27-632">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end
