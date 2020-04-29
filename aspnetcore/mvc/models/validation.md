---
title: Validación de modelos en ASP.NET Core MVC
author: rick-anderson
description: Obtenga información sobre la validación de modelos en ASP.NET Core MVC y Razor Pages.
ms.author: riande
ms.custom: mvc
ms.date: 12/15/2019
uid: mvc/models/validation
ms.openlocfilehash: 0e3d4f4705dbfdae00943de2d85c603b6762a2f8
ms.sourcegitcommit: 56861af66bb364a5d60c3c72d133d854b4cf292d
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2020
ms.locfileid: "82205896"
---
# <a name="model-validation-in-aspnet-core-mvc-and-razor-pages"></a><span data-ttu-id="5841c-103">Validación de modelos en ASP.NET Core MVC y Razor Pages</span><span class="sxs-lookup"><span data-stu-id="5841c-103">Model validation in ASP.NET Core MVC and Razor Pages</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5841c-104">De [Kirk Larkin](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="5841c-104">By [Kirk Larkin](https://github.com/serpent5)</span></span>

<span data-ttu-id="5841c-105">En este artículo se explica cómo validar la entrada del usuario en una aplicación ASP.NET Core MVC o Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="5841c-105">This article explains how to validate user input in an ASP.NET Core MVC or Razor Pages app.</span></span>

<span data-ttu-id="5841c-106">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="5841c-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="model-state"></a><span data-ttu-id="5841c-107">Estado del modelo</span><span class="sxs-lookup"><span data-stu-id="5841c-107">Model state</span></span>

<span data-ttu-id="5841c-108">El estado del modelo representa los errores que proceden de dos subsistemas: el enlace de modelos y la validación de modelos.</span><span class="sxs-lookup"><span data-stu-id="5841c-108">Model state represents errors that come from two subsystems: model binding and model validation.</span></span> <span data-ttu-id="5841c-109">Los errores que se originan del [enlace de modelos](model-binding.md) suelen ser errores de conversión de datos.</span><span class="sxs-lookup"><span data-stu-id="5841c-109">Errors that originate from [model binding](model-binding.md) are generally data conversion errors.</span></span> <span data-ttu-id="5841c-110">Por ejemplo, se escribe una "x" en un campo numérico entero.</span><span class="sxs-lookup"><span data-stu-id="5841c-110">For example, an "x" is entered in an integer field.</span></span> <span data-ttu-id="5841c-111">La validación del modelo se produce después del enlace de modelos y notifica los errores en los que los datos no cumplen las reglas de negocio.</span><span class="sxs-lookup"><span data-stu-id="5841c-111">Model validation occurs after model binding and reports errors where data doesn't conform to business rules.</span></span> <span data-ttu-id="5841c-112">Por ejemplo, se especifica un 0 en un campo que espera una clasificación entre 1 y 5.</span><span class="sxs-lookup"><span data-stu-id="5841c-112">For example, a 0 is entered in a field that expects a rating between 1 and 5.</span></span>

<span data-ttu-id="5841c-113">Tanto el enlace como la validación de modelos se producen antes de la ejecución de una acción de controlador o un método de controlador de Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="5841c-113">Both model binding and model validation occur before the execution of a controller action or a Razor Pages handler method.</span></span> <span data-ttu-id="5841c-114">En el caso de las aplicaciones web, la aplicación es responsable de inspeccionar `ModelState.IsValid` y reaccionar de manera apropiada.</span><span class="sxs-lookup"><span data-stu-id="5841c-114">For web apps, it's the app's responsibility to inspect `ModelState.IsValid` and react appropriately.</span></span> <span data-ttu-id="5841c-115">Normalmente, las aplicaciones web vuelven a mostrar la página con un mensaje de error:</span><span class="sxs-lookup"><span data-stu-id="5841c-115">Web apps typically redisplay the page with an error message:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Pages/Movies/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=3-6)]

<span data-ttu-id="5841c-116">Los controladores de Web API no tienen que comprobar `ModelState.IsValid` si tienen el atributo `[ApiController]`.</span><span class="sxs-lookup"><span data-stu-id="5841c-116">Web API controllers don't have to check `ModelState.IsValid` if they have the `[ApiController]` attribute.</span></span> <span data-ttu-id="5841c-117">En ese caso, se devuelve una respuesta HTTP 400 automática que contiene los detalles del error cuando el estado del modelo no es válido.</span><span class="sxs-lookup"><span data-stu-id="5841c-117">In that case, an automatic HTTP 400 response containing error details is returned when model state is invalid.</span></span> <span data-ttu-id="5841c-118">Para obtener más información, consulte [Respuestas HTTP 400 automáticas](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="5841c-118">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

## <a name="rerun-validation"></a><span data-ttu-id="5841c-119">Nueva ejecución de la validación</span><span class="sxs-lookup"><span data-stu-id="5841c-119">Rerun validation</span></span>

<span data-ttu-id="5841c-120">La validación es automática, pero tal vez le interese repetirla manualmente.</span><span class="sxs-lookup"><span data-stu-id="5841c-120">Validation is automatic, but you might want to repeat it manually.</span></span> <span data-ttu-id="5841c-121">Por ejemplo, tal vez haya calculado el valor de una propiedad y quiera volver a ejecutar la validación después de establecer la propiedad en el valor calculado.</span><span class="sxs-lookup"><span data-stu-id="5841c-121">For example, you might compute a value for a property and want to rerun validation after setting the property to the computed value.</span></span> <span data-ttu-id="5841c-122">Para volver a ejecutar la validación, llame al método `TryValidateModel`, como se muestra aquí:</span><span class="sxs-lookup"><span data-stu-id="5841c-122">To rerun validation, call the `TryValidateModel` method, as shown here:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Pages/Movies/Create.cshtml.cs?name=snippet_TryValidate&highlight=3-6)]

## <a name="validation-attributes"></a><span data-ttu-id="5841c-123">Atributos de validación</span><span class="sxs-lookup"><span data-stu-id="5841c-123">Validation attributes</span></span>

<span data-ttu-id="5841c-124">Los atributos de validación permiten especificar reglas de validación para las propiedades del modelo.</span><span class="sxs-lookup"><span data-stu-id="5841c-124">Validation attributes let you specify validation rules for model properties.</span></span> <span data-ttu-id="5841c-125">En el ejemplo siguiente de la aplicación de ejemplo se muestra una clase de modelo anotada con atributos de validación.</span><span class="sxs-lookup"><span data-stu-id="5841c-125">The following example from the sample app shows a model class that is annotated with validation attributes.</span></span> <span data-ttu-id="5841c-126">El atributo `[ClassicMovie]` es un atributo de validación personalizado y los demás están integrados.</span><span class="sxs-lookup"><span data-stu-id="5841c-126">The `[ClassicMovie]` attribute is a custom validation attribute and the others are built-in.</span></span> <span data-ttu-id="5841c-127">`[ClassicMovieWithClientValidator]` no se muestra.</span><span class="sxs-lookup"><span data-stu-id="5841c-127">Not shown is `[ClassicMovieWithClientValidator]`.</span></span> <span data-ttu-id="5841c-128">`[ClassicMovieWithClientValidator]` muestra una manera alternativa de implementar un atributo personalizado.</span><span class="sxs-lookup"><span data-stu-id="5841c-128">`[ClassicMovieWithClientValidator]` shows an alternative way to implement a custom attribute.</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Models/Movie.cs?name=snippet_Class)]

## <a name="built-in-attributes"></a><span data-ttu-id="5841c-129">Atributos integrados</span><span class="sxs-lookup"><span data-stu-id="5841c-129">Built-in attributes</span></span>

<span data-ttu-id="5841c-130">Estos son algunos de los atributos de validación integrados:</span><span class="sxs-lookup"><span data-stu-id="5841c-130">Here are some of the built-in validation attributes:</span></span>

* <span data-ttu-id="5841c-131">`[CreditCard]`: Valida que la propiedad tiene un formato de tarjeta de crédito.</span><span class="sxs-lookup"><span data-stu-id="5841c-131">`[CreditCard]`: Validates that the property has a credit card format.</span></span>
* <span data-ttu-id="5841c-132">`[Compare]`: Valida que dos propiedades de un modelo coincidan.</span><span class="sxs-lookup"><span data-stu-id="5841c-132">`[Compare]`: Validates that two properties in a model match.</span></span>
* <span data-ttu-id="5841c-133">`[EmailAddress]`: Valida que la propiedad tiene un formato de correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="5841c-133">`[EmailAddress]`: Validates that the property has an email format.</span></span>
* <span data-ttu-id="5841c-134">`[Phone]`: Valida que la propiedad tiene un formato de número de teléfono.</span><span class="sxs-lookup"><span data-stu-id="5841c-134">`[Phone]`: Validates that the property has a telephone number format.</span></span>
* <span data-ttu-id="5841c-135">`[Range]`: Valida que el valor de la propiedad se encuentra dentro de un intervalo especificado.</span><span class="sxs-lookup"><span data-stu-id="5841c-135">`[Range]`: Validates that the property value falls within a specified range.</span></span>
* <span data-ttu-id="5841c-136">`[RegularExpression]`: Valida que el valor de propiedad coincide con una expresión regular especificada.</span><span class="sxs-lookup"><span data-stu-id="5841c-136">`[RegularExpression]`: Validates that the property value matches a specified regular expression.</span></span>
* <span data-ttu-id="5841c-137">`[Required]`: Valida que el campo no sea NULL.</span><span class="sxs-lookup"><span data-stu-id="5841c-137">`[Required]`: Validates that the field is not null.</span></span> <span data-ttu-id="5841c-138">Vea [ `[Required]` el atributo](#required-attribute) para obtener más información sobre el comportamiento de este atributo.</span><span class="sxs-lookup"><span data-stu-id="5841c-138">See [`[Required]` attribute](#required-attribute) for details about this attribute's behavior.</span></span>
* <span data-ttu-id="5841c-139">`[StringLength]`: Valida que un valor de propiedad de cadena no supera un límite de longitud especificado.</span><span class="sxs-lookup"><span data-stu-id="5841c-139">`[StringLength]`: Validates that a string property value doesn't exceed a specified length limit.</span></span>
* <span data-ttu-id="5841c-140">`[Url]`: Valida que la propiedad tiene un formato de dirección URL.</span><span class="sxs-lookup"><span data-stu-id="5841c-140">`[Url]`: Validates that the property has a URL format.</span></span>
* <span data-ttu-id="5841c-141">`[Remote]`: Valida la entrada en el cliente mediante una llamada a un método de acción en el servidor.</span><span class="sxs-lookup"><span data-stu-id="5841c-141">`[Remote]`: Validates input on the client by calling an action method on the server.</span></span> <span data-ttu-id="5841c-142">Vea [ `[Remote]` el atributo](#remote-attribute) para obtener más información sobre el comportamiento de este atributo.</span><span class="sxs-lookup"><span data-stu-id="5841c-142">See [`[Remote]` attribute](#remote-attribute) for details about this attribute's behavior.</span></span>

<span data-ttu-id="5841c-143">En el espacio de nombres [System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations) encontrará una lista completa de atributos de validación.</span><span class="sxs-lookup"><span data-stu-id="5841c-143">A complete list of validation attributes can be found in the [System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations) namespace.</span></span>

### <a name="error-messages"></a><span data-ttu-id="5841c-144">Mensajes de error</span><span class="sxs-lookup"><span data-stu-id="5841c-144">Error messages</span></span>

<span data-ttu-id="5841c-145">Los atributos de validación permiten especificar el mensaje de error que se mostrará para una entrada no válida.</span><span class="sxs-lookup"><span data-stu-id="5841c-145">Validation attributes let you specify the error message to be displayed for invalid input.</span></span> <span data-ttu-id="5841c-146">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="5841c-146">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "Name length can't be more than 8.")]
```

<span data-ttu-id="5841c-147">Internamente, los atributos llaman a `String.Format` con un marcador de posición para el nombre de campo y, en ocasiones, marcadores de posición adicionales.</span><span class="sxs-lookup"><span data-stu-id="5841c-147">Internally, the attributes call `String.Format` with a placeholder for the field name and sometimes additional placeholders.</span></span> <span data-ttu-id="5841c-148">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="5841c-148">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "{0} length must be between {2} and {1}.", MinimumLength = 6)]
```

<span data-ttu-id="5841c-149">Cuando se aplica a una propiedad `Name`, el mensaje de error creado por el código anterior sería "La longitud del nombre debe estar entre 6 y 8".</span><span class="sxs-lookup"><span data-stu-id="5841c-149">When applied to a `Name` property, the error message created by the preceding code would be "Name length must be between 6 and 8.".</span></span>

<span data-ttu-id="5841c-150">Para averiguar qué parámetros se pasan a `String.Format` para el mensaje de error de un atributo determinado, vea el [código fuente de DataAnnotations](https://github.com/dotnet/runtime/tree/master/src/libraries/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).</span><span class="sxs-lookup"><span data-stu-id="5841c-150">To find out which parameters are passed to `String.Format` for a particular attribute's error message, see the [DataAnnotations source code](https://github.com/dotnet/runtime/tree/master/src/libraries/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).</span></span>

## <a name="required-attribute"></a><span data-ttu-id="5841c-151">Atributo [Required]</span><span class="sxs-lookup"><span data-stu-id="5841c-151">[Required] attribute</span></span>

<span data-ttu-id="5841c-152">El sistema de validación de .NET Core 3.0 y versiones posteriores trata las propiedades enlazadas o los parámetros que no aceptan valores NULL como si tuvieran un atributo `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="5841c-152">The validation system in .NET Core 3.0 and later treats non-nullable parameters or bound properties as if they had a `[Required]` attribute.</span></span> <span data-ttu-id="5841c-153">Los [tipos de valor](/dotnet/csharp/language-reference/keywords/value-types) como `decimal` y `int` no aceptan valores NULL.</span><span class="sxs-lookup"><span data-stu-id="5841c-153">[Value types](/dotnet/csharp/language-reference/keywords/value-types) such as `decimal` and `int` are non-nullable.</span></span> <span data-ttu-id="5841c-154">Este comportamiento se puede deshabilitar si se configura <xref:Microsoft.AspNetCore.Mvc.MvcOptions.SuppressImplicitRequiredAttributeForNonNullableReferenceTypes> en `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="5841c-154">This behavior can be disabled by configuring <xref:Microsoft.AspNetCore.Mvc.MvcOptions.SuppressImplicitRequiredAttributeForNonNullableReferenceTypes> in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddControllers(options => options.SuppressImplicitRequiredAttributeForNonNullableReferenceTypes = true);
```

### <a name="required-validation-on-the-server"></a><span data-ttu-id="5841c-155">Validación de [Required] en el servidor</span><span class="sxs-lookup"><span data-stu-id="5841c-155">[Required] validation on the server</span></span>

<span data-ttu-id="5841c-156">En el servidor, si la propiedad es NULL, se considera que falta un valor requerido.</span><span class="sxs-lookup"><span data-stu-id="5841c-156">On the server, a required value is considered missing if the property is null.</span></span> <span data-ttu-id="5841c-157">Un campo que no acepta valores NULL siempre es válido y el mensaje de error del atributo `[Required]` no se muestra nunca.</span><span class="sxs-lookup"><span data-stu-id="5841c-157">A non-nullable field is always valid, and the `[Required]` attribute's error message is never displayed.</span></span>

<span data-ttu-id="5841c-158">Aun así, el enlace de modelos para una propiedad que no acepta valores NULL podría fallar, lo que genera un mensaje de error como `The value '' is invalid`.</span><span class="sxs-lookup"><span data-stu-id="5841c-158">However, model binding for a non-nullable property may fail, resulting in an error message such as `The value '' is invalid`.</span></span> <span data-ttu-id="5841c-159">Para especificar un mensaje de error personalizado para la validación del lado servidor de tipos que no aceptan valores NULL, tiene las siguientes opciones:</span><span class="sxs-lookup"><span data-stu-id="5841c-159">To specify a custom error message for server-side validation of non-nullable types, you have the following options:</span></span>

* <span data-ttu-id="5841c-160">Haga que el campo acepte valores NULL (por ejemplo, `decimal?` en lugar de `decimal`).</span><span class="sxs-lookup"><span data-stu-id="5841c-160">Make the field nullable (for example, `decimal?` instead of `decimal`).</span></span> <span data-ttu-id="5841c-161">Los tipos de valor [Nullable\<T>](/dotnet/csharp/programming-guide/nullable-types/) se tratan como tipos estándar que aceptan valores NULL.</span><span class="sxs-lookup"><span data-stu-id="5841c-161">[Nullable\<T>](/dotnet/csharp/programming-guide/nullable-types/) value types are treated like standard nullable types.</span></span>
* <span data-ttu-id="5841c-162">Especifique el mensaje de error predeterminado que el enlace de modelos va a usar, como se muestra en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="5841c-162">Specify the default error message to be used by model binding, as shown in the following example:</span></span>

  [!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_Configuration&highlight=5-6)]

  <span data-ttu-id="5841c-163">Para obtener más información sobre los errores de enlace de modelos para los que se pueden establecer mensajes predeterminados, vea <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>.</span><span class="sxs-lookup"><span data-stu-id="5841c-163">For more information about model binding errors that you can set default messages for, see <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>.</span></span>

### <a name="required-validation-on-the-client"></a><span data-ttu-id="5841c-164">Validación de [Required] en el cliente</span><span class="sxs-lookup"><span data-stu-id="5841c-164">[Required] validation on the client</span></span>

<span data-ttu-id="5841c-165">Las cadenas y los tipos que no aceptan valores NULL se tratan de forma diferente en el cliente, en comparación con el servidor.</span><span class="sxs-lookup"><span data-stu-id="5841c-165">Non-nullable types and strings are handled differently on the client compared to the server.</span></span> <span data-ttu-id="5841c-166">En el cliente:</span><span class="sxs-lookup"><span data-stu-id="5841c-166">On the client:</span></span>

* <span data-ttu-id="5841c-167">Un valor se considera presente solo si se especifica una entrada para él.</span><span class="sxs-lookup"><span data-stu-id="5841c-167">A value is considered present only if input is entered for it.</span></span> <span data-ttu-id="5841c-168">Por lo tanto, la validación del lado cliente controla los tipos que no aceptan valores NULL del mismo modo que los tipos que aceptan valores NULL.</span><span class="sxs-lookup"><span data-stu-id="5841c-168">Therefore, client-side validation handles non-nullable types the same as nullable types.</span></span>
* <span data-ttu-id="5841c-169">El método [required](https://jqueryvalidation.org/required-method/) de jQuery Validate considera que un espacio en blanco en un campo de cadena es una entrada válida.</span><span class="sxs-lookup"><span data-stu-id="5841c-169">Whitespace in a string field is considered valid input by the jQuery Validation [required](https://jqueryvalidation.org/required-method/) method.</span></span> <span data-ttu-id="5841c-170">La validación del lado servidor considera que un campo de cadena necesario no es válido si solo se especifica un espacio en blanco.</span><span class="sxs-lookup"><span data-stu-id="5841c-170">Server-side validation considers a required string field invalid if only whitespace is entered.</span></span>

<span data-ttu-id="5841c-171">Como se indicó anteriormente, los tipos que no aceptan valores NULL se tratan como si tuvieran un atributo `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="5841c-171">As noted earlier, non-nullable types are treated as though they had a `[Required]` attribute.</span></span> <span data-ttu-id="5841c-172">Esto significa que se obtiene la validación del lado cliente incluso si no se aplica el atributo `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="5841c-172">That means you get client-side validation even if you don't apply the `[Required]` attribute.</span></span> <span data-ttu-id="5841c-173">Si no usa el atributo, aparecerá un mensaje de error predeterminado.</span><span class="sxs-lookup"><span data-stu-id="5841c-173">But if you don't use the attribute, you get a default error message.</span></span> <span data-ttu-id="5841c-174">Para especificar un mensaje de error personalizado, use el atributo.</span><span class="sxs-lookup"><span data-stu-id="5841c-174">To specify a custom error message, use the attribute.</span></span>

## <a name="remote-attribute"></a><span data-ttu-id="5841c-175">Atributo [Remote]</span><span class="sxs-lookup"><span data-stu-id="5841c-175">[Remote] attribute</span></span>

<span data-ttu-id="5841c-176">El atributo `[Remote]` implementa la validación del lado cliente que requiere llamar a un método en el servidor para determinar si la entrada del campo es válida.</span><span class="sxs-lookup"><span data-stu-id="5841c-176">The `[Remote]` attribute implements client-side validation that requires calling a method on the server to determine whether field input is valid.</span></span> <span data-ttu-id="5841c-177">Por ejemplo, la aplicación podría tener que comprobar si un nombre de usuario ya está en uso.</span><span class="sxs-lookup"><span data-stu-id="5841c-177">For example, the app may need to verify whether a user name is already in use.</span></span>

<span data-ttu-id="5841c-178">Para implementar la validación remota:</span><span class="sxs-lookup"><span data-stu-id="5841c-178">To implement remote validation:</span></span>

1. <span data-ttu-id="5841c-179">Cree un método de acción para que lo llame JavaScript.</span><span class="sxs-lookup"><span data-stu-id="5841c-179">Create an action method for JavaScript to call.</span></span>  <span data-ttu-id="5841c-180">El método [remote](https://jqueryvalidation.org/remote-method/) de jQuery Validate espera una respuesta JSON:</span><span class="sxs-lookup"><span data-stu-id="5841c-180">The jQuery Validate [remote](https://jqueryvalidation.org/remote-method/) method expects a JSON response:</span></span>

   * <span data-ttu-id="5841c-181">`true` significa que los datos de entrada son válidos.</span><span class="sxs-lookup"><span data-stu-id="5841c-181">`true` means the input data is valid.</span></span>
   * <span data-ttu-id="5841c-182">`false`, `undefined` o `null` significan que la entrada no es válida.</span><span class="sxs-lookup"><span data-stu-id="5841c-182">`false`, `undefined`, or `null` means the input is invalid.</span></span> <span data-ttu-id="5841c-183">Muestre el mensaje de error predeterminado.</span><span class="sxs-lookup"><span data-stu-id="5841c-183">Display the default error message.</span></span>
   * <span data-ttu-id="5841c-184">Cualquier otra cadena significa que la entrada no es válida.</span><span class="sxs-lookup"><span data-stu-id="5841c-184">Any other string means the input is invalid.</span></span> <span data-ttu-id="5841c-185">Muestre la cadena como un mensaje de error personalizado.</span><span class="sxs-lookup"><span data-stu-id="5841c-185">Display the string as a custom error message.</span></span>

   <span data-ttu-id="5841c-186">A continuación encontrará un ejemplo de un método de acción que devuelve un mensaje de error personalizado:</span><span class="sxs-lookup"><span data-stu-id="5841c-186">Here's an example of an action method that returns a custom error message:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyEmail)]

1. <span data-ttu-id="5841c-187">En la clase de modelo, anote la propiedad con un atributo `[Remote]` que apunte al método de acción de validación, como se muestra en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="5841c-187">In the model class, annotate the property with a `[Remote]` attribute that points to the validation action method, as shown in the following example:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Models/User.cs?name=snippet_Email)]
 
   <span data-ttu-id="5841c-188">El atributo `[Remote]` está en el espacio de nombres `Microsoft.AspNetCore.Mvc`.</span><span class="sxs-lookup"><span data-stu-id="5841c-188">The `[Remote]` attribute is in the `Microsoft.AspNetCore.Mvc` namespace.</span></span>
   
### <a name="additional-fields"></a><span data-ttu-id="5841c-189">Campos adicionales</span><span class="sxs-lookup"><span data-stu-id="5841c-189">Additional fields</span></span>

<span data-ttu-id="5841c-190">La propiedad `AdditionalFields` del atributo `[Remote]` permite validar combinaciones de campos con los datos del servidor.</span><span class="sxs-lookup"><span data-stu-id="5841c-190">The `AdditionalFields` property of the `[Remote]` attribute lets you validate combinations of fields against data on the server.</span></span> <span data-ttu-id="5841c-191">Por ejemplo, si el modelo `User` tuviera las propiedades `FirstName` y `LastName`, podría interesarle comprobar que ningún usuario actual tuviera ya ese par de nombres.</span><span class="sxs-lookup"><span data-stu-id="5841c-191">For example, if the `User` model had `FirstName` and `LastName` properties, you might want to verify that no existing users already have that pair of names.</span></span> <span data-ttu-id="5841c-192">En el siguiente ejemplo se muestra cómo usar `AdditionalFields`:</span><span class="sxs-lookup"><span data-stu-id="5841c-192">The following example shows how to use `AdditionalFields`:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Models/User.cs?name=snippet_Name&highlight=1,5)]

<span data-ttu-id="5841c-193">`AdditionalFields` podría configurarse explícitamente para las cadenas "FirstName" y "LastName", pero, al usar el operador [nameof](/dotnet/csharp/language-reference/keywords/nameof), se simplifica la refactorización posterior.</span><span class="sxs-lookup"><span data-stu-id="5841c-193">`AdditionalFields` could be set explicitly to the strings "FirstName" and "LastName", but using the [nameof](/dotnet/csharp/language-reference/keywords/nameof) operator simplifies later refactoring.</span></span> <span data-ttu-id="5841c-194">El método de acción para esta validación debe aceptar los argumentos `firstName` y `lastName`:</span><span class="sxs-lookup"><span data-stu-id="5841c-194">The action method for this validation must accept both `firstName` and `lastName` arguments:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyName)]

<span data-ttu-id="5841c-195">Cuando el usuario escribe un nombre o un apellido, JavaScript realiza una llamada remota para comprobar si ese par de nombres ya existe.</span><span class="sxs-lookup"><span data-stu-id="5841c-195">When the user enters a first or last name, JavaScript makes a remote call to see if that pair of names has been taken.</span></span>

<span data-ttu-id="5841c-196">Para validar dos o más campos adicionales, proporciónelos como una lista delimitada por comas.</span><span class="sxs-lookup"><span data-stu-id="5841c-196">To validate two or more additional fields, provide them as a comma-delimited list.</span></span> <span data-ttu-id="5841c-197">Por ejemplo, para agregar una propiedad `MiddleName` al modelo, establezca el atributo `[Remote]` tal como se muestra en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="5841c-197">For example, to add a `MiddleName` property to the model, set the `[Remote]` attribute as shown in the following example:</span></span>

```csharp
[Remote(action: "VerifyName", controller: "Users", AdditionalFields = nameof(FirstName) + "," + nameof(LastName))]
public string MiddleName { get; set; }
```

<span data-ttu-id="5841c-198">`AdditionalFields`, al igual que todos los argumentos de atributo, debe ser una expresión constante.</span><span class="sxs-lookup"><span data-stu-id="5841c-198">`AdditionalFields`, like all attribute arguments, must be a constant expression.</span></span> <span data-ttu-id="5841c-199">Por lo tanto, no use una [cadena interpolada](/dotnet/csharp/language-reference/keywords/interpolated-strings) ni llame a <xref:System.String.Join*> para inicializar `AdditionalFields`.</span><span class="sxs-lookup"><span data-stu-id="5841c-199">Therefore, don't use an [interpolated string](/dotnet/csharp/language-reference/keywords/interpolated-strings) or call <xref:System.String.Join*> to initialize `AdditionalFields`.</span></span>

## <a name="alternatives-to-built-in-attributes"></a><span data-ttu-id="5841c-200">Alternativas a los atributos integrados</span><span class="sxs-lookup"><span data-stu-id="5841c-200">Alternatives to built-in attributes</span></span>

<span data-ttu-id="5841c-201">Si necesita una validación que no proporcionan los atributos integrados, puede hacer lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="5841c-201">If you need validation not provided by built-in attributes, you can:</span></span>

* <span data-ttu-id="5841c-202">[Crear atributos personalizados](#custom-attributes)</span><span class="sxs-lookup"><span data-stu-id="5841c-202">[Create custom attributes](#custom-attributes).</span></span>
* <span data-ttu-id="5841c-203">[Implementar IValidatableObject](#ivalidatableobject)</span><span class="sxs-lookup"><span data-stu-id="5841c-203">[Implement IValidatableObject](#ivalidatableobject).</span></span>

## <a name="custom-attributes"></a><span data-ttu-id="5841c-204">Personalización de atributos</span><span class="sxs-lookup"><span data-stu-id="5841c-204">Custom attributes</span></span>

<span data-ttu-id="5841c-205">Para los escenarios que no se controlan mediante los atributos de validación integrados, puede crear atributos de validación personalizados.</span><span class="sxs-lookup"><span data-stu-id="5841c-205">For scenarios that the built-in validation attributes don't handle, you can create custom validation attributes.</span></span> <span data-ttu-id="5841c-206">Cree una clase que herede de <xref:System.ComponentModel.DataAnnotations.ValidationAttribute> y reemplace el método <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*>.</span><span class="sxs-lookup"><span data-stu-id="5841c-206">Create a class that inherits from <xref:System.ComponentModel.DataAnnotations.ValidationAttribute>, and override the <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*> method.</span></span>

<span data-ttu-id="5841c-207">El método `IsValid` acepta un objeto denominado *value*, que es la entrada que se va a validar.</span><span class="sxs-lookup"><span data-stu-id="5841c-207">The `IsValid` method accepts an object named *value*, which is the input to be validated.</span></span> <span data-ttu-id="5841c-208">Una sobrecarga también acepta un objeto `ValidationContext`, que proporciona información adicional, como la instancia del modelo creada por el enlace de modelos.</span><span class="sxs-lookup"><span data-stu-id="5841c-208">An overload also accepts a `ValidationContext` object, which provides additional information, such as the model instance created by model binding.</span></span>

<span data-ttu-id="5841c-209">El siguiente ejemplo valida que la fecha de lanzamiento de una película del género *Classic* no sea posterior a un año especificado.</span><span class="sxs-lookup"><span data-stu-id="5841c-209">The following example validates that the release date for a movie in the *Classic* genre isn't later than a specified year.</span></span> <span data-ttu-id="5841c-210">El atributo `[ClassicMovie]`:</span><span class="sxs-lookup"><span data-stu-id="5841c-210">The `[ClassicMovie]` attribute:</span></span>

* <span data-ttu-id="5841c-211">Solo se ejecuta en el servidor.</span><span class="sxs-lookup"><span data-stu-id="5841c-211">Is only run on the server.</span></span>
* <span data-ttu-id="5841c-212">En el caso de las películas clásicas, valida la fecha de lanzamiento:</span><span class="sxs-lookup"><span data-stu-id="5841c-212">For Classic movies, validates the release date:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Validation/ClassicMovieAttribute.cs?name=snippet_Class)]

<span data-ttu-id="5841c-213">La variable `movie` del ejemplo anterior representa un objeto `Movie` que contiene los datos del envío del formulario.</span><span class="sxs-lookup"><span data-stu-id="5841c-213">The `movie` variable in the preceding example represents a `Movie` object that contains the data from the form submission.</span></span> <span data-ttu-id="5841c-214">Si se produce un error de validación, se devuelve un `ValidationResult` con un mensaje de error.</span><span class="sxs-lookup"><span data-stu-id="5841c-214">When validation fails, a `ValidationResult` with an error message is returned.</span></span>

## <a name="ivalidatableobject"></a><span data-ttu-id="5841c-215">IValidatableObject</span><span class="sxs-lookup"><span data-stu-id="5841c-215">IValidatableObject</span></span>

<span data-ttu-id="5841c-216">El ejemplo anterior solo funciona con tipos `Movie`.</span><span class="sxs-lookup"><span data-stu-id="5841c-216">The preceding example works only with `Movie` types.</span></span> <span data-ttu-id="5841c-217">Otra opción para la validación del nivel de clase consiste en implementar `IValidatableObject` en la clase de modelo, como se muestra en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="5841c-217">Another option for class-level validation is to implement `IValidatableObject` in the model class, as shown in the following example:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Models/ValidatableMovie.cs?name=snippet_Class&highlight=1,26-34)]

## <a name="top-level-node-validation"></a><span data-ttu-id="5841c-218">Validación de nodo de nivel superior</span><span class="sxs-lookup"><span data-stu-id="5841c-218">Top-level node validation</span></span>

<span data-ttu-id="5841c-219">Los nodos de nivel superior incluyen lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="5841c-219">Top-level nodes include:</span></span>

* <span data-ttu-id="5841c-220">Parámetros de acción</span><span class="sxs-lookup"><span data-stu-id="5841c-220">Action parameters</span></span>
* <span data-ttu-id="5841c-221">Propiedades de controlador</span><span class="sxs-lookup"><span data-stu-id="5841c-221">Controller properties</span></span>
* <span data-ttu-id="5841c-222">Parámetros de controlador de página</span><span class="sxs-lookup"><span data-stu-id="5841c-222">Page handler parameters</span></span>
* <span data-ttu-id="5841c-223">Propiedades de modelo de página</span><span class="sxs-lookup"><span data-stu-id="5841c-223">Page model properties</span></span>

<span data-ttu-id="5841c-224">Los nodos de nivel superior enlazados al modelo se validan además de la validación de las propiedades del modelo.</span><span class="sxs-lookup"><span data-stu-id="5841c-224">Model-bound top-level nodes are validated in addition to validating model properties.</span></span> <span data-ttu-id="5841c-225">En el ejemplo siguiente de la aplicación de muestra, el método `VerifyPhone` usa <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> para validar el parámetro de acción `phone`:</span><span class="sxs-lookup"><span data-stu-id="5841c-225">In the following example from the sample app, the `VerifyPhone` method uses the <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> to validate the `phone` action parameter:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyPhone)]

<span data-ttu-id="5841c-226">Los nodos de nivel superior pueden usar <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> con atributos de validación.</span><span class="sxs-lookup"><span data-stu-id="5841c-226">Top-level nodes can use <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> with validation attributes.</span></span> <span data-ttu-id="5841c-227">En el ejemplo siguiente de la aplicación de muestra, el método `CheckAge` especifica que el parámetro `age` debe estar enlazado desde la cadena de consulta al enviar el formulario:</span><span class="sxs-lookup"><span data-stu-id="5841c-227">In the following example from the sample app, the `CheckAge` method specifies that the `age` parameter must be bound from the query string when the form is submitted:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_CheckAgeSignature)]

<span data-ttu-id="5841c-228">En la página Comprobar edad (*CheckAge.cshtml*), hay dos formularios.</span><span class="sxs-lookup"><span data-stu-id="5841c-228">In the Check Age page (*CheckAge.cshtml*), there are two forms.</span></span> <span data-ttu-id="5841c-229">El primer formulario envía un valor `Age` de `99` como una cadena de parámetro de consulta: `https://localhost:5001/Users/CheckAge?Age=99`.</span><span class="sxs-lookup"><span data-stu-id="5841c-229">The first form submits an `Age` value of `99` as a query string parameter: `https://localhost:5001/Users/CheckAge?Age=99`.</span></span>

<span data-ttu-id="5841c-230">Al enviar un parámetro `age` con un formato correcto desde la cadena de consulta, el formulario se valida.</span><span class="sxs-lookup"><span data-stu-id="5841c-230">When a properly formatted `age` parameter from the query string is submitted, the form validates.</span></span>

<span data-ttu-id="5841c-231">El segundo formulario de la página Comprobar edad envía el valor `Age` en el cuerpo de la solicitud, y se produce un error de validación.</span><span class="sxs-lookup"><span data-stu-id="5841c-231">The second form on the Check Age page submits the `Age` value in the body of the request, and validation fails.</span></span> <span data-ttu-id="5841c-232">Se produce un error en el enlace porque el parámetro `age` debe provenir de una cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="5841c-232">Binding fails because the `age` parameter must come from a query string.</span></span>

## <a name="maximum-errors"></a><span data-ttu-id="5841c-233">Número máximo de errores</span><span class="sxs-lookup"><span data-stu-id="5841c-233">Maximum errors</span></span>

<span data-ttu-id="5841c-234">La validación se detiene cuando se alcanza el número máximo de errores (200 de forma predeterminada).</span><span class="sxs-lookup"><span data-stu-id="5841c-234">Validation stops when the maximum number of errors is reached (200 by default).</span></span> <span data-ttu-id="5841c-235">Puede configurar este número con el siguiente código en `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="5841c-235">You can configure this number with the following code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_Configuration&highlight=4)]

## <a name="maximum-recursion"></a><span data-ttu-id="5841c-236">Recursividad máxima</span><span class="sxs-lookup"><span data-stu-id="5841c-236">Maximum recursion</span></span>

<span data-ttu-id="5841c-237"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> recorre el gráfico de objetos del modelo que se está validando.</span><span class="sxs-lookup"><span data-stu-id="5841c-237"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> traverses the object graph of the model being validated.</span></span> <span data-ttu-id="5841c-238">En el caso de los modelos profundos o infinitamente recursivos, la validación podría causar un desbordamiento de pila.</span><span class="sxs-lookup"><span data-stu-id="5841c-238">For models that are deep or are infinitely recursive, validation may result in stack overflow.</span></span> <span data-ttu-id="5841c-239">[MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) proporciona una manera de detener pronto la validación si la recursividad del visitante supera la profundidad configurada.</span><span class="sxs-lookup"><span data-stu-id="5841c-239">[MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) provides a way to stop validation early if the visitor recursion exceeds a configured depth.</span></span> <span data-ttu-id="5841c-240">El valor predeterminado de `MvcOptions.MaxValidationDepth` es 32.</span><span class="sxs-lookup"><span data-stu-id="5841c-240">The default value of `MvcOptions.MaxValidationDepth` is 32.</span></span>

## <a name="automatic-short-circuit"></a><span data-ttu-id="5841c-241">Cortocircuito automático</span><span class="sxs-lookup"><span data-stu-id="5841c-241">Automatic short-circuit</span></span>

<span data-ttu-id="5841c-242">La validación cortocircuita (se omite) automáticamente si el gráfico de modelo no requiere validación.</span><span class="sxs-lookup"><span data-stu-id="5841c-242">Validation is automatically short-circuited (skipped) if the model graph doesn't require validation.</span></span> <span data-ttu-id="5841c-243">Entre los objetos para los que el tiempo de ejecución omite la validación se incluyen las colecciones de elementos primitivos (como `byte[]`, `string[]` y `Dictionary<string, string>`) y gráficos de objeto complejo que no tienen los validadores.</span><span class="sxs-lookup"><span data-stu-id="5841c-243">Objects that the runtime skips validation for include collections of primitives (such as `byte[]`, `string[]`, `Dictionary<string, string>`) and complex object graphs that don't have any validators.</span></span>

## <a name="disable-validation"></a><span data-ttu-id="5841c-244">Deshabilitación de la validación</span><span class="sxs-lookup"><span data-stu-id="5841c-244">Disable validation</span></span>

<span data-ttu-id="5841c-245">Para deshabilitar la validación:</span><span class="sxs-lookup"><span data-stu-id="5841c-245">To disable validation:</span></span>

1. <span data-ttu-id="5841c-246">Cree una implementación de `IObjectModelValidator` que no marque ningún campo como no válido.</span><span class="sxs-lookup"><span data-stu-id="5841c-246">Create an implementation of `IObjectModelValidator` that doesn't mark any fields as invalid.</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/NullObjectModelValidator.cs?name=snippet_Class)]

1. <span data-ttu-id="5841c-247">Agregue el código siguiente a `Startup.ConfigureServices` para reemplazar la implementación predeterminada de `IObjectModelValidator` en el contenedor de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="5841c-247">Add the following code to `Startup.ConfigureServices` to replace the default `IObjectModelValidator` implementation in the dependency injection container.</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_DisableValidation)]

<span data-ttu-id="5841c-248">Es posible que todavía vea errores de estado del modelo originados en el enlace de modelos.</span><span class="sxs-lookup"><span data-stu-id="5841c-248">You might still see model state errors that originate from model binding.</span></span>

## <a name="client-side-validation"></a><span data-ttu-id="5841c-249">Validación del lado cliente</span><span class="sxs-lookup"><span data-stu-id="5841c-249">Client-side validation</span></span>

<span data-ttu-id="5841c-250">La validación del lado cliente impide realizar el envío hasta que el formulario sea válido.</span><span class="sxs-lookup"><span data-stu-id="5841c-250">Client-side validation prevents submission until the form is valid.</span></span> <span data-ttu-id="5841c-251">El botón Enviar ejecuta JavaScript para enviar el formulario o mostrar mensajes de error.</span><span class="sxs-lookup"><span data-stu-id="5841c-251">The Submit button runs JavaScript that either submits the form or displays error messages.</span></span>

<span data-ttu-id="5841c-252">La validación del lado cliente evita un recorrido de ida y vuelta innecesario en el servidor cuando hay errores de entrada en un formulario.</span><span class="sxs-lookup"><span data-stu-id="5841c-252">Client-side validation avoids an unnecessary round trip to the server when there are input errors on a form.</span></span> <span data-ttu-id="5841c-253">Las siguientes referencias de script de *_Layout.cshtml* y *_ValidationScriptsPartial.cshtml* admiten la validación del lado cliente:</span><span class="sxs-lookup"><span data-stu-id="5841c-253">The following script references in *_Layout.cshtml* and *_ValidationScriptsPartial.cshtml* support client-side validation:</span></span>

[!code-cshtml[](validation/samples/3.x/ValidationSample/Views/Shared/_Layout.cshtml?name=snippet_Scripts)]

[!code-cshtml[](validation/samples/3.x/ValidationSample/Views/Shared/_ValidationScriptsPartial.cshtml?name=snippet_Scripts)]

<span data-ttu-id="5841c-254">El script [Validación discreta de jQuery](https://github.com/aspnet/jquery-validation-unobtrusive) es una biblioteca front-end personalizada de Microsoft que se basa en el conocido complemento [Validación de jQuery](https://jqueryvalidation.org/).</span><span class="sxs-lookup"><span data-stu-id="5841c-254">The [jQuery Unobtrusive Validation](https://github.com/aspnet/jquery-validation-unobtrusive) script is a custom Microsoft front-end library that builds on the popular [jQuery Validate](https://jqueryvalidation.org/) plugin.</span></span> <span data-ttu-id="5841c-255">Si no usa Validación discreta de jQuery, deberá codificar la misma lógica de validación dos veces: una vez en los atributos de validación del lado servidor en las propiedades del modelo y luego en los scripts del lado cliente.</span><span class="sxs-lookup"><span data-stu-id="5841c-255">Without jQuery Unobtrusive Validation, you would have to code the same validation logic in two places: once in the server-side validation attributes on model properties, and then again in client-side scripts.</span></span> <span data-ttu-id="5841c-256">En su lugar, los [asistentes de etiquetas](xref:mvc/views/tag-helpers/intro) y los [asistentes de HTML](xref:mvc/views/overview) usan los atributos de validación y escriben metadatos de las propiedades del modelo para representar atributos `data-` HTML 5 para los elementos de formulario que necesitan validación.</span><span class="sxs-lookup"><span data-stu-id="5841c-256">Instead, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use the validation attributes and type metadata from model properties to render HTML 5 `data-` attributes for the form elements that need validation.</span></span> <span data-ttu-id="5841c-257">Validación discreta de jQuery analiza los atributos `data-` y pasa la lógica a jQuery Validate. De este modo, la lógica de validación del lado servidor se "copia" de manera eficaz en el cliente.</span><span class="sxs-lookup"><span data-stu-id="5841c-257">jQuery Unobtrusive Validation parses the `data-` attributes and passes the logic to jQuery Validate, effectively "copying" the server-side validation logic to the client.</span></span> <span data-ttu-id="5841c-258">Puede mostrar errores de validación en el cliente mediante el uso de asistentes de etiquetas, como se muestra aquí:</span><span class="sxs-lookup"><span data-stu-id="5841c-258">You can display validation errors on the client using tag helpers as shown here:</span></span>

[!code-cshtml[](validation/samples/3.x/ValidationSample/Pages/Movies/Create.cshtml?name=snippet_ReleaseDate&highlight=3-4)]

<span data-ttu-id="5841c-259">Los anteriores asistentes de etiquetas representan el siguiente código HTML:</span><span class="sxs-lookup"><span data-stu-id="5841c-259">The preceding tag helpers render the following HTML:</span></span>

```html
<div class="form-group">
    <label class="control-label" for="Movie_ReleaseDate">Release Date</label>
    <input class="form-control" type="date" data-val="true"
        data-val-required="The Release Date field is required."
        id="Movie_ReleaseDate" name="Movie.ReleaseDate" value="">
    <span class="text-danger field-validation-valid"
        data-valmsg-for="Movie.ReleaseDate" data-valmsg-replace="true"></span>
</div>
```

<span data-ttu-id="5841c-260">Tenga en cuenta que los atributos `data-` en los resultados HTML corresponden a los atributos de validación para la propiedad `Movie.ReleaseDate`.</span><span class="sxs-lookup"><span data-stu-id="5841c-260">Notice that the `data-` attributes in the HTML output correspond to the validation attributes for the `Movie.ReleaseDate` property.</span></span> <span data-ttu-id="5841c-261">El atributo `data-val-required` contiene un mensaje de error que se muestra si el usuario no rellena el campo de fecha de estreno.</span><span class="sxs-lookup"><span data-stu-id="5841c-261">The `data-val-required` attribute contains an error message to display if the user doesn't fill in the release date field.</span></span> <span data-ttu-id="5841c-262">La validación discreta de jQuery pasa este valor al método [required()](https://jqueryvalidation.org/required-method/) de la validación de jQuery, que luego muestra ese mensaje en el elemento **\<span>** que lo acompaña.</span><span class="sxs-lookup"><span data-stu-id="5841c-262">jQuery Unobtrusive Validation passes this value to the jQuery Validate [required()](https://jqueryvalidation.org/required-method/) method, which then displays that message in the accompanying **\<span>** element.</span></span>

<span data-ttu-id="5841c-263">La validación del tipo de datos se basa en el tipo .NET de una propiedad, a menos que lo reemplace un atributo `[DataType]`.</span><span class="sxs-lookup"><span data-stu-id="5841c-263">Data type validation is based on the .NET type of a property, unless that is overridden by a `[DataType]` attribute.</span></span> <span data-ttu-id="5841c-264">Los exploradores tienen sus propios mensajes de error de predeterminados, pero el paquete de Validación discreta de jQuery Validate puede invalidar esos mensajes.</span><span class="sxs-lookup"><span data-stu-id="5841c-264">Browsers have their own default error messages, but the jQuery Validation Unobtrusive Validation package can override those messages.</span></span> <span data-ttu-id="5841c-265">Los atributos y las subclases `[DataType]`, como `[EmailAddress]`, permiten especificar el mensaje de error.</span><span class="sxs-lookup"><span data-stu-id="5841c-265">`[DataType]` attributes and subclasses such as `[EmailAddress]` let you specify the error message.</span></span>

## <a name="unobtrusive-validation"></a><span data-ttu-id="5841c-266">Validación discreta</span><span class="sxs-lookup"><span data-stu-id="5841c-266">Unobtrusive validation</span></span>

<span data-ttu-id="5841c-267">Para obtener información sobre la validación discreta, consulte [este problema de GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/1111).</span><span class="sxs-lookup"><span data-stu-id="5841c-267">For information on unobtrusive validation, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/1111).</span></span>

### <a name="add-validation-to-dynamic-forms"></a><span data-ttu-id="5841c-268">Agregar validación a formularios dinámicos</span><span class="sxs-lookup"><span data-stu-id="5841c-268">Add Validation to Dynamic Forms</span></span>

<span data-ttu-id="5841c-269">Validación discreta de jQuery pasa los parámetros y la lógica de validación a jQuery Validate cuando la página se carga por primera vez.</span><span class="sxs-lookup"><span data-stu-id="5841c-269">jQuery Unobtrusive Validation passes validation logic and parameters to jQuery Validate when the page first loads.</span></span> <span data-ttu-id="5841c-270">Por lo tanto, la validación no funciona automáticamente en los formularios generados dinámicamente.</span><span class="sxs-lookup"><span data-stu-id="5841c-270">Therefore, validation doesn't work automatically on dynamically generated forms.</span></span> <span data-ttu-id="5841c-271">Para habilitar la validación, hay que indicarle a Validación discreta de jQuery que analice el formulario dinámico inmediatamente después de su creación.</span><span class="sxs-lookup"><span data-stu-id="5841c-271">To enable validation, tell jQuery Unobtrusive Validation to parse the dynamic form immediately after you create it.</span></span> <span data-ttu-id="5841c-272">Por ejemplo, en el código siguiente se configura la validación del lado cliente en un formulario agregado mediante AJAX.</span><span class="sxs-lookup"><span data-stu-id="5841c-272">For example, the following code sets up client-side validation on a form added via AJAX.</span></span>

```javascript
$.get({
    url: "https://url/that/returns/a/form",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add form. " + errorThrown);
    },
    success: function(newFormHTML) {
        var container = document.getElementById("form-container");
        container.insertAdjacentHTML("beforeend", newFormHTML);
        var forms = container.getElementsByTagName("form");
        var newForm = forms[forms.length - 1];
        $.validator.unobtrusive.parse(newForm);
    }
})
```

<span data-ttu-id="5841c-273">El método `$.validator.unobtrusive.parse()` acepta un selector de jQuery para su único argumento.</span><span class="sxs-lookup"><span data-stu-id="5841c-273">The `$.validator.unobtrusive.parse()` method accepts a jQuery selector for its one argument.</span></span> <span data-ttu-id="5841c-274">Este método indica a Validación discreta de jQuery que analice los atributos `data-` de formularios dentro de ese selector.</span><span class="sxs-lookup"><span data-stu-id="5841c-274">This method tells jQuery Unobtrusive Validation to parse the `data-` attributes of forms within that selector.</span></span> <span data-ttu-id="5841c-275">Después, los valores de estos atributos se pasan al complemento de jQuery Validate.</span><span class="sxs-lookup"><span data-stu-id="5841c-275">The values of those attributes are then passed to the jQuery Validate plugin.</span></span>

### <a name="add-validation-to-dynamic-controls"></a><span data-ttu-id="5841c-276">Agregar validación a controles dinámicos</span><span class="sxs-lookup"><span data-stu-id="5841c-276">Add Validation to Dynamic Controls</span></span>

<span data-ttu-id="5841c-277">El método `$.validator.unobtrusive.parse()` funciona en todo el formulario, no en los controles individuales generados dinámicamente, como `<input>` y `<select/>`.</span><span class="sxs-lookup"><span data-stu-id="5841c-277">The `$.validator.unobtrusive.parse()` method works on an entire form, not on individual dynamically generated controls, such as `<input>` and `<select/>`.</span></span> <span data-ttu-id="5841c-278">Para volver a analizar el formulario, quite los datos de validación que se agregaron cuando el formulario se analizó anteriormente, como se muestra en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="5841c-278">To reparse the form, remove the validation data that was added when the form was parsed earlier, as shown in the following example:</span></span>

```javascript
$.get({
    url: "https://url/that/returns/a/control",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add control. " + errorThrown);
    },
    success: function(newInputHTML) {
        var form = document.getElementById("my-form");
        form.insertAdjacentHTML("beforeend", newInputHTML);
        $(form).removeData("validator")    // Added by jQuery Validate
               .removeData("unobtrusiveValidation");   // Added by jQuery Unobtrusive Validation
        $.validator.unobtrusive.parse(form);
    }
})
```

## <a name="custom-client-side-validation"></a><span data-ttu-id="5841c-279">Validación del lado cliente personalizada</span><span class="sxs-lookup"><span data-stu-id="5841c-279">Custom client-side validation</span></span>

<span data-ttu-id="5841c-280">Para personalizar la validación del lado cliente, es necesario generar atributos HTML `data-` que funcionen con un adaptador personalizado de jQuery Validate.</span><span class="sxs-lookup"><span data-stu-id="5841c-280">Custom client-side validation is done by generating `data-` HTML attributes that work with a custom jQuery Validate adapter.</span></span> <span data-ttu-id="5841c-281">El siguiente ejemplo de código de adaptador se escribió para los atributos `[ClassicMovie]` y `[ClassicMovieWithClientValidator]` que se introdujeron anteriormente en este artículo:</span><span class="sxs-lookup"><span data-stu-id="5841c-281">The following sample adapter code was written for the `[ClassicMovie]` and `[ClassicMovieWithClientValidator]` attributes that were introduced earlier in this article:</span></span>

[!code-javascript[](validation/samples/3.x/ValidationSample/wwwroot/js/classicMovieValidator.js)]

<span data-ttu-id="5841c-282">Para obtener información sobre cómo escribir adaptadores, vea la [documentación de jQuery Validate](https://jqueryvalidation.org/documentation/).</span><span class="sxs-lookup"><span data-stu-id="5841c-282">For information about how to write adapters, see the [jQuery Validate documentation](https://jqueryvalidation.org/documentation/).</span></span>

<span data-ttu-id="5841c-283">El uso de un adaptador para un campo determinado se desencadena mediante atributos `data-` que:</span><span class="sxs-lookup"><span data-stu-id="5841c-283">The use of an adapter for a given field is triggered by `data-` attributes that:</span></span>

* <span data-ttu-id="5841c-284">Marcan que el campo está sujeto a validación (`data-val="true"`).</span><span class="sxs-lookup"><span data-stu-id="5841c-284">Flag the field as being subject to validation (`data-val="true"`).</span></span>
* <span data-ttu-id="5841c-285">Identifican un nombre de regla de validación y un texto de mensaje de error (por ejemplo, `data-val-rulename="Error message."`).</span><span class="sxs-lookup"><span data-stu-id="5841c-285">Identify a validation rule name and error message text (for example, `data-val-rulename="Error message."`).</span></span>
* <span data-ttu-id="5841c-286">Proporcionan los parámetros adicionales que necesite el validador (por ejemplo, `data-val-rulename-param1="value"`).</span><span class="sxs-lookup"><span data-stu-id="5841c-286">Provide any additional parameters the validator needs (for example, `data-val-rulename-param1="value"`).</span></span>

<span data-ttu-id="5841c-287">En el ejemplo siguiente se muestran los atributos `data-` para el atributo `ClassicMovie` de la aplicación de ejemplo:</span><span class="sxs-lookup"><span data-stu-id="5841c-287">The following example shows the `data-` attributes for the sample app's `ClassicMovie` attribute:</span></span>

```html
<input class="form-control" type="date"
    data-val="true"
    data-val-classicmovie="Classic movies must have a release year no later than 1960."
    data-val-classicmovie-year="1960"
    data-val-required="The Release Date field is required."
    id="Movie_ReleaseDate" name="Movie.ReleaseDate" value="">
```

<span data-ttu-id="5841c-288">Como se indicó anteriormente, los [asistentes de etiquetas](xref:mvc/views/tag-helpers/intro) y los [asistentes de HTML](xref:mvc/views/overview) usan información procedente de los atributos de validación para representar atributos `data-`.</span><span class="sxs-lookup"><span data-stu-id="5841c-288">As noted earlier, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use information from validation attributes to render `data-` attributes.</span></span> <span data-ttu-id="5841c-289">Hay dos opciones para escribir código que dé como resultado la creación de atributos HTML `data-` personalizados:</span><span class="sxs-lookup"><span data-stu-id="5841c-289">There are two options for writing code that results in the creation of custom `data-` HTML attributes:</span></span>

* <span data-ttu-id="5841c-290">Puede crear una clase que derive de `AttributeAdapterBase<TAttribute>` y una clase que implemente `IValidationAttributeAdapterProvider` y, después, registrar el atributo y su adaptador en la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="5841c-290">Create a class that derives from `AttributeAdapterBase<TAttribute>` and a class that implements `IValidationAttributeAdapterProvider`, and register your attribute and its adapter in DI.</span></span> <span data-ttu-id="5841c-291">Este método sigue el [principio de responsabilidad única](https://wikipedia.org/wiki/Single_responsibility_principle), ya que el código de validación relacionado con servidor y el relacionado con el cliente se encuentran en clases independientes.</span><span class="sxs-lookup"><span data-stu-id="5841c-291">This method follows the [single responsibility principal](https://wikipedia.org/wiki/Single_responsibility_principle) in that server-related and client-related validation code is in separate classes.</span></span> <span data-ttu-id="5841c-292">El adaptador también cuenta con una ventaja: debido a que está registrado en la inserción de dependencias, tiene a su disposición otros servicios de la inserción de dependencias si es necesario.</span><span class="sxs-lookup"><span data-stu-id="5841c-292">The adapter also has the advantage that since it is registered in DI, other services in DI are available to it if needed.</span></span>
* <span data-ttu-id="5841c-293">Puede implementar `IClientModelValidator` en la clase `ValidationAttribute`.</span><span class="sxs-lookup"><span data-stu-id="5841c-293">Implement `IClientModelValidator` in your `ValidationAttribute` class.</span></span> <span data-ttu-id="5841c-294">Este método es adecuado si el atributo no realiza ninguna validación en el lado servidor y no necesita ningún servicio de la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="5841c-294">This method might be appropriate if the attribute doesn't do any server-side validation and doesn't need any services from DI.</span></span>

### <a name="attributeadapter-for-client-side-validation"></a><span data-ttu-id="5841c-295">AttributeAdapter para la validación del lado cliente</span><span class="sxs-lookup"><span data-stu-id="5841c-295">AttributeAdapter for client-side validation</span></span>

<span data-ttu-id="5841c-296">Este método para representar atributos `data-` en HTML es lo que usa el atributo `ClassicMovie` en la aplicación de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="5841c-296">This method of rendering `data-` attributes in HTML is used by the `ClassicMovie` attribute in the sample app.</span></span> <span data-ttu-id="5841c-297">Para agregar la validación de cliente mediante este método:</span><span class="sxs-lookup"><span data-stu-id="5841c-297">To add client validation by using this method:</span></span>

1. <span data-ttu-id="5841c-298">Cree una clase de adaptador de atributo para el atributo de validación personalizado.</span><span class="sxs-lookup"><span data-stu-id="5841c-298">Create an attribute adapter class for the custom validation attribute.</span></span> <span data-ttu-id="5841c-299">Derive la clase de [AttributeAdapterBase\<T>](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="5841c-299">Derive the class from [AttributeAdapterBase\<T>](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2).</span></span> <span data-ttu-id="5841c-300">Cree un método `AddValidation` que agregue atributos `data-` a la salida representada, tal como se muestra en este ejemplo:</span><span class="sxs-lookup"><span data-stu-id="5841c-300">Create an `AddValidation` method that adds `data-` attributes to the rendered output, as shown in this example:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/ClassicMovieAttributeAdapter.cs?name=snippet_Class)]

1. <span data-ttu-id="5841c-301">Cree una clase de proveedor de adaptador que implemente <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>.</span><span class="sxs-lookup"><span data-stu-id="5841c-301">Create an adapter provider class that implements <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>.</span></span> <span data-ttu-id="5841c-302">En el método `GetAttributeAdapter`, pase el atributo personalizado al constructor del adaptador, como se muestra en este ejemplo:</span><span class="sxs-lookup"><span data-stu-id="5841c-302">In the `GetAttributeAdapter` method pass in the custom attribute to the adapter's constructor, as shown in this example:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/CustomValidationAttributeAdapterProvider.cs?name=snippet_Class)]

1. <span data-ttu-id="5841c-303">Registre el proveedor del adaptador para la inserción de dependencias en `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="5841c-303">Register the adapter provider for DI in `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_Configuration&highlight=9-10)]

### <a name="iclientmodelvalidator-for-client-side-validation"></a><span data-ttu-id="5841c-304">IClientModelValidator para la validación del lado cliente</span><span class="sxs-lookup"><span data-stu-id="5841c-304">IClientModelValidator for client-side validation</span></span>

<span data-ttu-id="5841c-305">Este método para representar atributos `data-` en HTML es lo que usa el atributo `ClassicMovieWithClientValidator` en la aplicación de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="5841c-305">This method of rendering `data-` attributes in HTML is used by the `ClassicMovieWithClientValidator` attribute in the sample app.</span></span> <span data-ttu-id="5841c-306">Para agregar la validación de cliente mediante este método:</span><span class="sxs-lookup"><span data-stu-id="5841c-306">To add client validation by using this method:</span></span>

* <span data-ttu-id="5841c-307">En el atributo de validación personalizado, implemente la interfaz `IClientModelValidator` y cree un método `AddValidation`.</span><span class="sxs-lookup"><span data-stu-id="5841c-307">In the custom validation attribute, implement the `IClientModelValidator` interface and create an `AddValidation` method.</span></span> <span data-ttu-id="5841c-308">En el método `AddValidation`, agregue atributos `data-` para la validación, como se muestra en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="5841c-308">In the `AddValidation` method, add `data-` attributes for validation, as shown in the following example:</span></span>

  [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/ClassicMovieWithClientValidatorAttribute.cs?name=snippet_Class)]

## <a name="disable-client-side-validation"></a><span data-ttu-id="5841c-309">Deshabilitación de la validación del lado cliente</span><span class="sxs-lookup"><span data-stu-id="5841c-309">Disable client-side validation</span></span>

<span data-ttu-id="5841c-310">El código siguiente deshabilita la validación de cliente en Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="5841c-310">The following code disables client validation in Razor Pages:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_DisableClientValidation&highlight=2-5)]

<span data-ttu-id="5841c-311">Otras opciones para deshabilitar la validación del lado cliente:</span><span class="sxs-lookup"><span data-stu-id="5841c-311">Other options to disable client-side validation:</span></span>

* <span data-ttu-id="5841c-312">Convierta en comentario la referencia a `_ValidationScriptsPartial` en todos los archivos *.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="5841c-312">Comment out the reference to `_ValidationScriptsPartial` in all the *.cshtml* files.</span></span>
* <span data-ttu-id="5841c-313">Quite el contenido del archivo *Pages\Shared\_ValidationScriptsPartial.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="5841c-313">Remove the contents of the *Pages\Shared\_ValidationScriptsPartial.cshtml* file.</span></span>

<span data-ttu-id="5841c-314">El enfoque anterior no impedirá la validación del lado cliente de la biblioteca de clases de Razor de identidad de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5841c-314">The preceding approach won't prevent client side validation of ASP.NET Core Identity Razor Class Library.</span></span> <span data-ttu-id="5841c-315">Para obtener más información, vea <xref:security/authentication/scaffold-identity>.</span><span class="sxs-lookup"><span data-stu-id="5841c-315">For more information, see <xref:security/authentication/scaffold-identity>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5841c-316">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="5841c-316">Additional resources</span></span>

* [<span data-ttu-id="5841c-317">Espacio de nombres System.ComponentModel.DataAnnotations</span><span class="sxs-lookup"><span data-stu-id="5841c-317">System.ComponentModel.DataAnnotations namespace</span></span>](xref:System.ComponentModel.DataAnnotations)
* [<span data-ttu-id="5841c-318">Enlace de modelos</span><span class="sxs-lookup"><span data-stu-id="5841c-318">Model Binding</span></span>](model-binding.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5841c-319">En este artículo se explica cómo validar la entrada del usuario en una aplicación ASP.NET Core MVC o Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="5841c-319">This article explains how to validate user input in an ASP.NET Core MVC or Razor Pages app.</span></span>

<span data-ttu-id="5841c-320">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) ([cómo descargarlo](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="5841c-320">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="model-state"></a><span data-ttu-id="5841c-321">Estado del modelo</span><span class="sxs-lookup"><span data-stu-id="5841c-321">Model state</span></span>

<span data-ttu-id="5841c-322">El estado del modelo representa los errores que proceden de dos subsistemas: el enlace de modelos y la validación de modelos.</span><span class="sxs-lookup"><span data-stu-id="5841c-322">Model state represents errors that come from two subsystems: model binding and model validation.</span></span> <span data-ttu-id="5841c-323">Los errores que se originan en el [enlace de modelos](model-binding.md) suelen ser errores de conversión de datos (por ejemplo, se especifica una "x" en un campo que espera un entero).</span><span class="sxs-lookup"><span data-stu-id="5841c-323">Errors that originate from [model binding](model-binding.md) are generally data conversion errors (for example, an "x" is entered in a field that expects an integer).</span></span> <span data-ttu-id="5841c-324">La validación de modelos se produce después de enlace de modelos y notifica errores cuando los datos no se ajustan a las reglas de negocios (por ejemplo, se especifica un 0 en un campo que espera una clasificación entre 1 y 5).</span><span class="sxs-lookup"><span data-stu-id="5841c-324">Model validation occurs after model binding and reports errors where the data doesn't conform to business rules (for example, a 0 is entered in a field that expects a rating between 1 and 5).</span></span>

<span data-ttu-id="5841c-325">Tanto el enlace como la validación de modelos se producen antes de la ejecución de una acción de controlador o un método de controlador de Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="5841c-325">Both model binding and validation occur before the execution of a controller action or a Razor Pages handler method.</span></span> <span data-ttu-id="5841c-326">En el caso de las aplicaciones web, la aplicación es responsable de inspeccionar `ModelState.IsValid` y reaccionar de manera apropiada.</span><span class="sxs-lookup"><span data-stu-id="5841c-326">For web apps, it's the app's responsibility to inspect `ModelState.IsValid` and react appropriately.</span></span> <span data-ttu-id="5841c-327">Normalmente, las aplicaciones web vuelven a mostrar la página con un mensaje de error:</span><span class="sxs-lookup"><span data-stu-id="5841c-327">Web apps typically redisplay the page with an error message:</span></span>

[!code-csharp[](validation/samples_snapshot/2.x/Create.cshtml.cs?name=snippet&highlight=3-6)]

<span data-ttu-id="5841c-328">Los controladores de Web API no tienen que comprobar `ModelState.IsValid` si tienen el atributo `[ApiController]`.</span><span class="sxs-lookup"><span data-stu-id="5841c-328">Web API controllers don't have to check `ModelState.IsValid` if they have the `[ApiController]` attribute.</span></span> <span data-ttu-id="5841c-329">En ese caso, se devuelve una respuesta HTTP 400 automática que contiene los detalles del error cuando el estado del modelo no es válido.</span><span class="sxs-lookup"><span data-stu-id="5841c-329">In that case, an automatic HTTP 400 response containing error details is returned when model state is invalid.</span></span> <span data-ttu-id="5841c-330">Para obtener más información, consulte [Respuestas HTTP 400 automáticas](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="5841c-330">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

## <a name="rerun-validation"></a><span data-ttu-id="5841c-331">Nueva ejecución de la validación</span><span class="sxs-lookup"><span data-stu-id="5841c-331">Rerun validation</span></span>

<span data-ttu-id="5841c-332">La validación es automática, pero tal vez le interese repetirla manualmente.</span><span class="sxs-lookup"><span data-stu-id="5841c-332">Validation is automatic, but you might want to repeat it manually.</span></span> <span data-ttu-id="5841c-333">Por ejemplo, tal vez haya calculado el valor de una propiedad y quiera volver a ejecutar la validación después de establecer la propiedad en el valor calculado.</span><span class="sxs-lookup"><span data-stu-id="5841c-333">For example, you might compute a value for a property and want to rerun validation after setting the property to the computed value.</span></span> <span data-ttu-id="5841c-334">Para volver a ejecutar la validación, llame al método `TryValidateModel`, como se muestra aquí:</span><span class="sxs-lookup"><span data-stu-id="5841c-334">To rerun validation, call the `TryValidateModel` method, as shown here:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/MoviesController.cs?name=snippet_TryValidateModel&highlight=11)]

## <a name="validation-attributes"></a><span data-ttu-id="5841c-335">Atributos de validación</span><span class="sxs-lookup"><span data-stu-id="5841c-335">Validation attributes</span></span>

<span data-ttu-id="5841c-336">Los atributos de validación permiten especificar reglas de validación para las propiedades del modelo.</span><span class="sxs-lookup"><span data-stu-id="5841c-336">Validation attributes let you specify validation rules for model properties.</span></span> <span data-ttu-id="5841c-337">En el ejemplo siguiente de la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) se muestra una clase de modelo anotada con atributos de validación.</span><span class="sxs-lookup"><span data-stu-id="5841c-337">The following example from [the sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) shows a model class that is annotated with validation attributes.</span></span> <span data-ttu-id="5841c-338">El atributo `[ClassicMovie]` es un atributo de validación personalizado y los demás están integrados.</span><span class="sxs-lookup"><span data-stu-id="5841c-338">The `[ClassicMovie]` attribute is a custom validation attribute and the others are built-in.</span></span> <span data-ttu-id="5841c-339">No se muestra `[ClassicMovie2]`, que indica una manera alternativa de implementar un atributo personalizado.</span><span class="sxs-lookup"><span data-stu-id="5841c-339">Not shown is `[ClassicMovie2]`, which shows an alternative way to implement a custom attribute.</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Models/Movie.cs?name=snippet_ModelClass)]

## <a name="built-in-attributes"></a><span data-ttu-id="5841c-340">Atributos integrados</span><span class="sxs-lookup"><span data-stu-id="5841c-340">Built-in attributes</span></span>

<span data-ttu-id="5841c-341">Entre los atributos de validación integrados se incluyen:</span><span class="sxs-lookup"><span data-stu-id="5841c-341">Built-in validation attributes include:</span></span>

* <span data-ttu-id="5841c-342">`[CreditCard]`: Valida que la propiedad tiene un formato de tarjeta de crédito.</span><span class="sxs-lookup"><span data-stu-id="5841c-342">`[CreditCard]`: Validates that the property has a credit card format.</span></span>
* <span data-ttu-id="5841c-343">`[Compare]`: Valida que dos propiedades de un modelo coincidan.</span><span class="sxs-lookup"><span data-stu-id="5841c-343">`[Compare]`: Validates that two properties in a model match.</span></span> <span data-ttu-id="5841c-344">Por ejemplo, el archivo *Register.cshtml.cs* usa `[Compare]` para validar que ambas contraseñas escritas coincidan.</span><span class="sxs-lookup"><span data-stu-id="5841c-344">For example, the *Register.cshtml.cs* file uses `[Compare]` to validate the two entered passwords match.</span></span> <span data-ttu-id="5841c-345">[Identidad de scaffolding](xref:security/authentication/scaffold-identity) para ver el código del Registro.</span><span class="sxs-lookup"><span data-stu-id="5841c-345">[Scaffold Identity](xref:security/authentication/scaffold-identity) to see the Register code.</span></span>
* <span data-ttu-id="5841c-346">`[EmailAddress]`: Valida que la propiedad tiene un formato de correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="5841c-346">`[EmailAddress]`: Validates that the property has an email format.</span></span>
* <span data-ttu-id="5841c-347">`[Phone]`: Valida que la propiedad tiene un formato de número de teléfono.</span><span class="sxs-lookup"><span data-stu-id="5841c-347">`[Phone]`: Validates that the property has a telephone number format.</span></span>
* <span data-ttu-id="5841c-348">`[Range]`: Valida que el valor de la propiedad se encuentra dentro de un intervalo especificado.</span><span class="sxs-lookup"><span data-stu-id="5841c-348">`[Range]`: Validates that the property value falls within a specified range.</span></span>
* <span data-ttu-id="5841c-349">`[RegularExpression]`: Valida que el valor de propiedad coincide con una expresión regular especificada.</span><span class="sxs-lookup"><span data-stu-id="5841c-349">`[RegularExpression]`: Validates that the property value matches a specified regular expression.</span></span>
* <span data-ttu-id="5841c-350">`[Required]`: Valida que el campo no sea NULL.</span><span class="sxs-lookup"><span data-stu-id="5841c-350">`[Required]`: Validates that the field is not null.</span></span> <span data-ttu-id="5841c-351">Vea [ `[Required]` el atributo](#required-attribute) para obtener más información sobre el comportamiento de este atributo.</span><span class="sxs-lookup"><span data-stu-id="5841c-351">See [`[Required]` attribute](#required-attribute) for details about this attribute's behavior.</span></span>
* <span data-ttu-id="5841c-352">`[StringLength]`: Valida que un valor de propiedad de cadena no supera un límite de longitud especificado.</span><span class="sxs-lookup"><span data-stu-id="5841c-352">`[StringLength]`: Validates that a string property value doesn't exceed a specified length limit.</span></span>
* <span data-ttu-id="5841c-353">`[Url]`: Valida que la propiedad tiene un formato de dirección URL.</span><span class="sxs-lookup"><span data-stu-id="5841c-353">`[Url]`: Validates that the property has a URL format.</span></span>
* <span data-ttu-id="5841c-354">`[Remote]`: Valida la entrada en el cliente mediante una llamada a un método de acción en el servidor.</span><span class="sxs-lookup"><span data-stu-id="5841c-354">`[Remote]`: Validates input on the client by calling an action method on the server.</span></span> <span data-ttu-id="5841c-355">Vea [ `[Remote]` el atributo](#remote-attribute) para obtener más información sobre el comportamiento de este atributo.</span><span class="sxs-lookup"><span data-stu-id="5841c-355">See [`[Remote]` attribute](#remote-attribute) for details about this attribute's behavior.</span></span>

<span data-ttu-id="5841c-356">Cuando se usa el atributo `[RegularExpression]` con la validación del lado cliente, la regex se ejecuta en JavaScript en el cliente.</span><span class="sxs-lookup"><span data-stu-id="5841c-356">When using the `[RegularExpression]` attribute with client-side validation, the regex is executed in JavaScript on the client.</span></span> <span data-ttu-id="5841c-357">Esto significa que se usará el comportamiento de coincidencia de [ECMAScript](/dotnet/standard/base-types/regular-expression-options#ecmascript-matching-behavior).</span><span class="sxs-lookup"><span data-stu-id="5841c-357">This means [ECMAScript](/dotnet/standard/base-types/regular-expression-options#ecmascript-matching-behavior) matching behavior will be used.</span></span> <span data-ttu-id="5841c-358">Para más información, consulte [este problema de GitHub](https://github.com/dotnet/corefx/issues/42487).</span><span class="sxs-lookup"><span data-stu-id="5841c-358">For more information, see [this GitHub issue](https://github.com/dotnet/corefx/issues/42487).</span></span>

<span data-ttu-id="5841c-359">En el espacio de nombres [System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations) encontrará una lista completa de atributos de validación.</span><span class="sxs-lookup"><span data-stu-id="5841c-359">A complete list of validation attributes can be found in the [System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations) namespace.</span></span>

### <a name="error-messages"></a><span data-ttu-id="5841c-360">Mensajes de error</span><span class="sxs-lookup"><span data-stu-id="5841c-360">Error messages</span></span>

<span data-ttu-id="5841c-361">Los atributos de validación permiten especificar el mensaje de error que se mostrará para una entrada no válida.</span><span class="sxs-lookup"><span data-stu-id="5841c-361">Validation attributes let you specify the error message to be displayed for invalid input.</span></span> <span data-ttu-id="5841c-362">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="5841c-362">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "Name length can't be more than 8.")]
```

<span data-ttu-id="5841c-363">Internamente, los atributos llaman a `String.Format` con un marcador de posición para el nombre de campo y, en ocasiones, marcadores de posición adicionales.</span><span class="sxs-lookup"><span data-stu-id="5841c-363">Internally, the attributes call `String.Format` with a placeholder for the field name and sometimes additional placeholders.</span></span> <span data-ttu-id="5841c-364">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="5841c-364">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "{0} length must be between {2} and {1}.", MinimumLength = 6)]
```

<span data-ttu-id="5841c-365">Cuando se aplica a una propiedad `Name`, el mensaje de error creado por el código anterior sería "La longitud del nombre debe estar entre 6 y 8".</span><span class="sxs-lookup"><span data-stu-id="5841c-365">When applied to a `Name` property, the error message created by the preceding code would be "Name length must be between 6 and 8.".</span></span>

<span data-ttu-id="5841c-366">Para averiguar qué parámetros se pasan a `String.Format` para el mensaje de error de un atributo determinado, vea el [código fuente de DataAnnotations](https://github.com/dotnet/corefx/tree/master/src/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).</span><span class="sxs-lookup"><span data-stu-id="5841c-366">To find out which parameters are passed to `String.Format` for a particular attribute's error message, see the [DataAnnotations source code](https://github.com/dotnet/corefx/tree/master/src/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).</span></span>

## <a name="required-attribute"></a><span data-ttu-id="5841c-367">Atributo [Required]</span><span class="sxs-lookup"><span data-stu-id="5841c-367">[Required] attribute</span></span>

<span data-ttu-id="5841c-368">De forma predeterminada, el sistema de validación trata las propiedades o los parámetros que no aceptan valores NULL como si tuvieran un atributo `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="5841c-368">By default, the validation system treats non-nullable parameters or properties as if they had a `[Required]` attribute.</span></span> <span data-ttu-id="5841c-369">Los [tipos de valor](/dotnet/csharp/language-reference/keywords/value-types) como `decimal` y `int` no aceptan valores NULL.</span><span class="sxs-lookup"><span data-stu-id="5841c-369">[Value types](/dotnet/csharp/language-reference/keywords/value-types) such as `decimal` and `int` are non-nullable.</span></span>

### <a name="required-validation-on-the-server"></a><span data-ttu-id="5841c-370">Validación de [Required] en el servidor</span><span class="sxs-lookup"><span data-stu-id="5841c-370">[Required] validation on the server</span></span>

<span data-ttu-id="5841c-371">En el servidor, si la propiedad es NULL, se considera que falta un valor requerido.</span><span class="sxs-lookup"><span data-stu-id="5841c-371">On the server, a required value is considered missing if the property is null.</span></span> <span data-ttu-id="5841c-372">Un campo que no acepta valores NULL siempre es válido, y el mensaje de error del atributo [Required] no se muestra nunca.</span><span class="sxs-lookup"><span data-stu-id="5841c-372">A non-nullable field is always valid, and the [Required] attribute's error message is never displayed.</span></span>

<span data-ttu-id="5841c-373">Aun así, el enlace de modelos para una propiedad que no acepta valores NULL podría fallar, lo que genera un mensaje de error como `The value '' is invalid`.</span><span class="sxs-lookup"><span data-stu-id="5841c-373">However, model binding for a non-nullable property may fail, resulting in an error message such as `The value '' is invalid`.</span></span> <span data-ttu-id="5841c-374">Para especificar un mensaje de error personalizado para la validación del lado servidor de tipos que no aceptan valores NULL, tiene las siguientes opciones:</span><span class="sxs-lookup"><span data-stu-id="5841c-374">To specify a custom error message for server-side validation of non-nullable types, you have the following options:</span></span>

* <span data-ttu-id="5841c-375">Haga que el campo acepte valores NULL (por ejemplo, `decimal?` en lugar de `decimal`).</span><span class="sxs-lookup"><span data-stu-id="5841c-375">Make the field nullable (for example, `decimal?` instead of `decimal`).</span></span> <span data-ttu-id="5841c-376">Los tipos de valor [Nullable\<T>](/dotnet/csharp/programming-guide/nullable-types/) se tratan como tipos estándar que aceptan valores NULL.</span><span class="sxs-lookup"><span data-stu-id="5841c-376">[Nullable\<T>](/dotnet/csharp/programming-guide/nullable-types/) value types are treated like standard nullable types.</span></span>
* <span data-ttu-id="5841c-377">Especifique el mensaje de error predeterminado que el enlace de modelos va a usar, como se muestra en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="5841c-377">Specify the default error message to be used by model binding, as shown in the following example:</span></span>

  [!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=4-5)]

  <span data-ttu-id="5841c-378">Para obtener más información sobre los errores de enlace de modelos para los que se pueden establecer mensajes predeterminados, vea <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>.</span><span class="sxs-lookup"><span data-stu-id="5841c-378">For more information about model binding errors that you can set default messages for, see <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>.</span></span>

### <a name="required-validation-on-the-client"></a><span data-ttu-id="5841c-379">Validación de [Required] en el cliente</span><span class="sxs-lookup"><span data-stu-id="5841c-379">[Required] validation on the client</span></span>

<span data-ttu-id="5841c-380">Las cadenas y los tipos que no aceptan valores NULL se tratan de forma diferente en el cliente, en comparación con el servidor.</span><span class="sxs-lookup"><span data-stu-id="5841c-380">Non-nullable types and strings are handled differently on the client compared to the server.</span></span> <span data-ttu-id="5841c-381">En el cliente:</span><span class="sxs-lookup"><span data-stu-id="5841c-381">On the client:</span></span>

* <span data-ttu-id="5841c-382">Un valor se considera presente solo si se especifica una entrada para él.</span><span class="sxs-lookup"><span data-stu-id="5841c-382">A value is considered present only if input is entered for it.</span></span> <span data-ttu-id="5841c-383">Por lo tanto, la validación del lado cliente controla los tipos que no aceptan valores NULL del mismo modo que los tipos que aceptan valores NULL.</span><span class="sxs-lookup"><span data-stu-id="5841c-383">Therefore, client-side validation handles non-nullable types the same as nullable types.</span></span>
* <span data-ttu-id="5841c-384">El método [required](https://jqueryvalidation.org/required-method/) de jQuery Validate considera que un espacio en blanco en un campo de cadena es una entrada válida.</span><span class="sxs-lookup"><span data-stu-id="5841c-384">Whitespace in a string field is considered valid input by the jQuery Validation [required](https://jqueryvalidation.org/required-method/) method.</span></span> <span data-ttu-id="5841c-385">La validación del lado servidor considera que un campo de cadena necesario no es válido si solo se especifica un espacio en blanco.</span><span class="sxs-lookup"><span data-stu-id="5841c-385">Server-side validation considers a required string field invalid if only whitespace is entered.</span></span>

<span data-ttu-id="5841c-386">Como se indicó anteriormente, los tipos que no aceptan valores NULL se tratan como si tuvieran un atributo `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="5841c-386">As noted earlier, non-nullable types are treated as though they had a `[Required]` attribute.</span></span> <span data-ttu-id="5841c-387">Esto significa que se obtiene la validación del lado cliente incluso si no se aplica el atributo `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="5841c-387">That means you get client-side validation even if you don't apply the `[Required]` attribute.</span></span> <span data-ttu-id="5841c-388">Si no usa el atributo, aparecerá un mensaje de error predeterminado.</span><span class="sxs-lookup"><span data-stu-id="5841c-388">But if you don't use the attribute, you get a default error message.</span></span> <span data-ttu-id="5841c-389">Para especificar un mensaje de error personalizado, use el atributo.</span><span class="sxs-lookup"><span data-stu-id="5841c-389">To specify a custom error message, use the attribute.</span></span>

## <a name="remote-attribute"></a><span data-ttu-id="5841c-390">Atributo [Remote]</span><span class="sxs-lookup"><span data-stu-id="5841c-390">[Remote] attribute</span></span>

<span data-ttu-id="5841c-391">El atributo `[Remote]` implementa la validación del lado cliente que requiere llamar a un método en el servidor para determinar si la entrada del campo es válida.</span><span class="sxs-lookup"><span data-stu-id="5841c-391">The `[Remote]` attribute implements client-side validation that requires calling a method on the server to determine whether field input is valid.</span></span> <span data-ttu-id="5841c-392">Por ejemplo, la aplicación podría tener que comprobar si un nombre de usuario ya está en uso.</span><span class="sxs-lookup"><span data-stu-id="5841c-392">For example, the app may need to verify whether a user name is already in use.</span></span>

<span data-ttu-id="5841c-393">Para implementar la validación remota:</span><span class="sxs-lookup"><span data-stu-id="5841c-393">To implement remote validation:</span></span>

1. <span data-ttu-id="5841c-394">Cree un método de acción para que lo llame JavaScript.</span><span class="sxs-lookup"><span data-stu-id="5841c-394">Create an action method for JavaScript to call.</span></span>  <span data-ttu-id="5841c-395">El método [remote](https://jqueryvalidation.org/remote-method/) de jQuery Validate espera una respuesta JSON:</span><span class="sxs-lookup"><span data-stu-id="5841c-395">The jQuery Validate [remote](https://jqueryvalidation.org/remote-method/) method expects a JSON response:</span></span>

   * <span data-ttu-id="5841c-396">`"true"` significa que los datos de entrada son válidos.</span><span class="sxs-lookup"><span data-stu-id="5841c-396">`"true"` means the input data is valid.</span></span>
   * <span data-ttu-id="5841c-397">`"false"`, `undefined` o `null` significan que la entrada no es válida.</span><span class="sxs-lookup"><span data-stu-id="5841c-397">`"false"`, `undefined`, or `null` means the input is invalid.</span></span>  <span data-ttu-id="5841c-398">Muestre el mensaje de error predeterminado.</span><span class="sxs-lookup"><span data-stu-id="5841c-398">Display the default error message.</span></span>
   * <span data-ttu-id="5841c-399">Cualquier otra cadena significa que la entrada no es válida.</span><span class="sxs-lookup"><span data-stu-id="5841c-399">Any other string means the input is invalid.</span></span> <span data-ttu-id="5841c-400">Muestre la cadena como un mensaje de error personalizado.</span><span class="sxs-lookup"><span data-stu-id="5841c-400">Display the string as a custom error message.</span></span>

   <span data-ttu-id="5841c-401">A continuación encontrará un ejemplo de un método de acción que devuelve un mensaje de error personalizado:</span><span class="sxs-lookup"><span data-stu-id="5841c-401">Here's an example of an action method that returns a custom error message:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyEmail)]

1. <span data-ttu-id="5841c-402">En la clase de modelo, anote la propiedad con un atributo `[Remote]` que apunte al método de acción de validación, como se muestra en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="5841c-402">In the model class, annotate the property with a `[Remote]` attribute that points to the validation action method, as shown in the following example:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Models/User.cs?name=snippet_UserEmailProperty)]
 
   <span data-ttu-id="5841c-403">El atributo `[Remote]` está en el espacio de nombres `Microsoft.AspNetCore.Mvc`.</span><span class="sxs-lookup"><span data-stu-id="5841c-403">The `[Remote]` attribute is in the `Microsoft.AspNetCore.Mvc` namespace.</span></span> <span data-ttu-id="5841c-404">Instale el paquete de NuGet [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures) si no usa `Microsoft.AspNetCore.App` o el metapaquete `Microsoft.AspNetCore.All`.</span><span class="sxs-lookup"><span data-stu-id="5841c-404">Install the [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures) NuGet package if you're not using the `Microsoft.AspNetCore.App` or `Microsoft.AspNetCore.All` metapackage.</span></span>
   
### <a name="additional-fields"></a><span data-ttu-id="5841c-405">Campos adicionales</span><span class="sxs-lookup"><span data-stu-id="5841c-405">Additional fields</span></span>

<span data-ttu-id="5841c-406">La propiedad `AdditionalFields` del atributo `[Remote]` permite validar combinaciones de campos con los datos del servidor.</span><span class="sxs-lookup"><span data-stu-id="5841c-406">The `AdditionalFields` property of the `[Remote]` attribute lets you validate combinations of fields against data on the server.</span></span> <span data-ttu-id="5841c-407">Por ejemplo, si el modelo `User` tuviera las propiedades `FirstName` y `LastName`, podría interesarle comprobar que ningún usuario actual tuviera ya ese par de nombres.</span><span class="sxs-lookup"><span data-stu-id="5841c-407">For example, if the `User` model had `FirstName` and `LastName` properties, you might want to verify that no existing users already have that pair of names.</span></span> <span data-ttu-id="5841c-408">En el siguiente ejemplo se muestra cómo usar `AdditionalFields`:</span><span class="sxs-lookup"><span data-stu-id="5841c-408">The following example shows how to use `AdditionalFields`:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Models/User.cs?name=snippet_UserNameProperties)]

<span data-ttu-id="5841c-409">`AdditionalFields` podría configurarse explícitamente para las cadenas `"FirstName"` y `"LastName"`, pero, al usar el operador [nameof](/dotnet/csharp/language-reference/keywords/nameof) se simplifica la refactorización posterior.</span><span class="sxs-lookup"><span data-stu-id="5841c-409">`AdditionalFields` could be set explicitly to the strings `"FirstName"` and `"LastName"`, but using the [nameof](/dotnet/csharp/language-reference/keywords/nameof) operator simplifies later refactoring.</span></span> <span data-ttu-id="5841c-410">El método de acción para esta validación debe aceptar los argumentos de nombre y apellido:</span><span class="sxs-lookup"><span data-stu-id="5841c-410">The action method for this validation must accept both first name and last name arguments:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyName)]

<span data-ttu-id="5841c-411">Cuando el usuario escribe un nombre o un apellido, JavaScript realiza una llamada remota para comprobar si ese par de nombres ya existe.</span><span class="sxs-lookup"><span data-stu-id="5841c-411">When the user enters a first or last name, JavaScript makes a remote call to see if that pair of names has been taken.</span></span>

<span data-ttu-id="5841c-412">Para validar dos o más campos adicionales, proporciónelos como una lista delimitada por comas.</span><span class="sxs-lookup"><span data-stu-id="5841c-412">To validate two or more additional fields, provide them as a comma-delimited list.</span></span> <span data-ttu-id="5841c-413">Por ejemplo, para agregar una propiedad `MiddleName` al modelo, establezca el atributo `[Remote]` tal como se muestra en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="5841c-413">For example, to add a `MiddleName` property to the model, set the `[Remote]` attribute as shown in the following example:</span></span>

```csharp
[Remote(action: "VerifyName", controller: "Users", AdditionalFields = nameof(FirstName) + "," + nameof(LastName))]
public string MiddleName { get; set; }
```

<span data-ttu-id="5841c-414">`AdditionalFields`, al igual que todos los argumentos de atributo, debe ser una expresión constante.</span><span class="sxs-lookup"><span data-stu-id="5841c-414">`AdditionalFields`, like all attribute arguments, must be a constant expression.</span></span> <span data-ttu-id="5841c-415">Por lo tanto, no use una [cadena interpolada](/dotnet/csharp/language-reference/keywords/interpolated-strings) ni llame a <xref:System.String.Join*> para inicializar `AdditionalFields`.</span><span class="sxs-lookup"><span data-stu-id="5841c-415">Therefore, don't use an [interpolated string](/dotnet/csharp/language-reference/keywords/interpolated-strings) or call <xref:System.String.Join*> to initialize `AdditionalFields`.</span></span>

## <a name="alternatives-to-built-in-attributes"></a><span data-ttu-id="5841c-416">Alternativas a los atributos integrados</span><span class="sxs-lookup"><span data-stu-id="5841c-416">Alternatives to built-in attributes</span></span>

<span data-ttu-id="5841c-417">Si necesita una validación que no proporcionan los atributos integrados, puede hacer lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="5841c-417">If you need validation not provided by built-in attributes, you can:</span></span>

* <span data-ttu-id="5841c-418">[Crear atributos personalizados](#custom-attributes)</span><span class="sxs-lookup"><span data-stu-id="5841c-418">[Create custom attributes](#custom-attributes).</span></span>
* <span data-ttu-id="5841c-419">[Implementar IValidatableObject](#ivalidatableobject)</span><span class="sxs-lookup"><span data-stu-id="5841c-419">[Implement IValidatableObject](#ivalidatableobject).</span></span>

## <a name="custom-attributes"></a><span data-ttu-id="5841c-420">Personalización de atributos</span><span class="sxs-lookup"><span data-stu-id="5841c-420">Custom attributes</span></span>

<span data-ttu-id="5841c-421">Para los escenarios que no se controlan mediante los atributos de validación integrados, puede crear atributos de validación personalizados.</span><span class="sxs-lookup"><span data-stu-id="5841c-421">For scenarios that the built-in validation attributes don't handle, you can create custom validation attributes.</span></span> <span data-ttu-id="5841c-422">Cree una clase que herede de <xref:System.ComponentModel.DataAnnotations.ValidationAttribute> y reemplace el método <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*>.</span><span class="sxs-lookup"><span data-stu-id="5841c-422">Create a class that inherits from <xref:System.ComponentModel.DataAnnotations.ValidationAttribute>, and override the <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*> method.</span></span>

<span data-ttu-id="5841c-423">El método `IsValid` acepta un objeto denominado *value*, que es la entrada que se va a validar.</span><span class="sxs-lookup"><span data-stu-id="5841c-423">The `IsValid` method accepts an object named *value*, which is the input to be validated.</span></span> <span data-ttu-id="5841c-424">Una sobrecarga también acepta un objeto `ValidationContext`, que proporciona información adicional, como la instancia del modelo creada por el enlace de modelos.</span><span class="sxs-lookup"><span data-stu-id="5841c-424">An overload also accepts a `ValidationContext` object, which provides additional information, such as the model instance created by model binding.</span></span>

<span data-ttu-id="5841c-425">El siguiente ejemplo valida que la fecha de lanzamiento de una película del género *Classic* no sea posterior a un año especificado.</span><span class="sxs-lookup"><span data-stu-id="5841c-425">The following example validates that the release date for a movie in the *Classic* genre isn't later than a specified year.</span></span> <span data-ttu-id="5841c-426">El atributo `[ClassicMovie2]` comprueba primero el género y continúa únicamente si es *Classic*.</span><span class="sxs-lookup"><span data-stu-id="5841c-426">The `[ClassicMovie2]` attribute checks the genre first and continues only if it's *Classic*.</span></span> <span data-ttu-id="5841c-427">Para las películas que se identifican como clásicos, comprueba la fecha de lanzamiento a fin de asegurarse de que no sea posterior al límite que se ha pasado al constructor de atributo.</span><span class="sxs-lookup"><span data-stu-id="5841c-427">For movies identified as classics, it checks the release date to make sure it's not later than the limit passed to the attribute constructor.)</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/ClassicMovieAttribute.cs?name=snippet_ClassicMovieAttribute)]

<span data-ttu-id="5841c-428">La variable `movie` del ejemplo anterior representa un objeto `Movie` que contiene los datos del envío del formulario.</span><span class="sxs-lookup"><span data-stu-id="5841c-428">The `movie` variable in the preceding example represents a `Movie` object that contains the data from the form submission.</span></span> <span data-ttu-id="5841c-429">El método `IsValid` comprueba la fecha y el género.</span><span class="sxs-lookup"><span data-stu-id="5841c-429">The `IsValid` method checks the date and genre.</span></span> <span data-ttu-id="5841c-430">Si la validación es correcta, `IsValid` devuelve un código `ValidationResult.Success`.</span><span class="sxs-lookup"><span data-stu-id="5841c-430">Upon successful validation, `IsValid` returns a `ValidationResult.Success` code.</span></span> <span data-ttu-id="5841c-431">Si se produce un error de validación, se devuelve un `ValidationResult` con un mensaje de error.</span><span class="sxs-lookup"><span data-stu-id="5841c-431">When validation fails, a `ValidationResult` with an error message is returned.</span></span>

## <a name="ivalidatableobject"></a><span data-ttu-id="5841c-432">IValidatableObject</span><span class="sxs-lookup"><span data-stu-id="5841c-432">IValidatableObject</span></span>

<span data-ttu-id="5841c-433">El ejemplo anterior solo funciona con tipos `Movie`.</span><span class="sxs-lookup"><span data-stu-id="5841c-433">The preceding example works only with `Movie` types.</span></span> <span data-ttu-id="5841c-434">Otra opción para la validación del nivel de clase consiste en implementar `IValidatableObject` en la clase de modelo, como se muestra en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="5841c-434">Another option for class-level validation is to implement `IValidatableObject` in the model class, as shown in the following example:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Models/MovieIValidatable.cs?name=snippet&highlight=1,26-34)]

## <a name="top-level-node-validation"></a><span data-ttu-id="5841c-435">Validación de nodo de nivel superior</span><span class="sxs-lookup"><span data-stu-id="5841c-435">Top-level node validation</span></span>

<span data-ttu-id="5841c-436">Los nodos de nivel superior incluyen lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="5841c-436">Top-level nodes include:</span></span>

* <span data-ttu-id="5841c-437">Parámetros de acción</span><span class="sxs-lookup"><span data-stu-id="5841c-437">Action parameters</span></span>
* <span data-ttu-id="5841c-438">Propiedades de controlador</span><span class="sxs-lookup"><span data-stu-id="5841c-438">Controller properties</span></span>
* <span data-ttu-id="5841c-439">Parámetros de controlador de página</span><span class="sxs-lookup"><span data-stu-id="5841c-439">Page handler parameters</span></span>
* <span data-ttu-id="5841c-440">Propiedades de modelo de página</span><span class="sxs-lookup"><span data-stu-id="5841c-440">Page model properties</span></span>

<span data-ttu-id="5841c-441">Los nodos de nivel superior enlazados al modelo se validan además de la validación de las propiedades del modelo.</span><span class="sxs-lookup"><span data-stu-id="5841c-441">Model-bound top-level nodes are validated in addition to validating model properties.</span></span> <span data-ttu-id="5841c-442">En el ejemplo siguiente de la aplicación de muestra, el método `VerifyPhone` usa <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> para validar el parámetro de acción `phone`:</span><span class="sxs-lookup"><span data-stu-id="5841c-442">In the following example from the sample app, the `VerifyPhone` method uses the <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> to validate the `phone` action parameter:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyPhone)]

<span data-ttu-id="5841c-443">Los nodos de nivel superior pueden usar <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> con atributos de validación.</span><span class="sxs-lookup"><span data-stu-id="5841c-443">Top-level nodes can use <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> with validation attributes.</span></span> <span data-ttu-id="5841c-444">En el ejemplo siguiente de la aplicación de muestra, el método `CheckAge` especifica que el parámetro `age` debe estar enlazado desde la cadena de consulta al enviar el formulario:</span><span class="sxs-lookup"><span data-stu-id="5841c-444">In the following example from the sample app, the `CheckAge` method specifies that the `age` parameter must be bound from the query string when the form is submitted:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_CheckAge)]

<span data-ttu-id="5841c-445">En la página Comprobar edad (*CheckAge.cshtml*), hay dos formularios.</span><span class="sxs-lookup"><span data-stu-id="5841c-445">In the Check Age page (*CheckAge.cshtml*), there are two forms.</span></span> <span data-ttu-id="5841c-446">El primer formulario envía un valor `Age` de `99` como una cadena de consulta: `https://localhost:5001/Users/CheckAge?Age=99`.</span><span class="sxs-lookup"><span data-stu-id="5841c-446">The first form submits an `Age` value of `99` as a query string: `https://localhost:5001/Users/CheckAge?Age=99`.</span></span>

<span data-ttu-id="5841c-447">Al enviar un parámetro `age` con un formato correcto desde la cadena de consulta, el formulario se valida.</span><span class="sxs-lookup"><span data-stu-id="5841c-447">When a properly formatted `age` parameter from the query string is submitted, the form validates.</span></span>

<span data-ttu-id="5841c-448">El segundo formulario de la página Comprobar edad envía el valor `Age` en el cuerpo de la solicitud, y se produce un error de validación.</span><span class="sxs-lookup"><span data-stu-id="5841c-448">The second form on the Check Age page submits the `Age` value in the body of the request, and validation fails.</span></span> <span data-ttu-id="5841c-449">Se produce un error en el enlace porque el parámetro `age` debe provenir de una cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="5841c-449">Binding fails because the `age` parameter must come from a query string.</span></span>

<span data-ttu-id="5841c-450">Cuando se ejecuta con `CompatibilityVersion.Version_2_1` o versiones posteriores, la validación de nodo de nivel superior está habilitada de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="5841c-450">When running with `CompatibilityVersion.Version_2_1` or later, top-level node validation is enabled by default.</span></span> <span data-ttu-id="5841c-451">En caso contrario, la validación del nodo de nivel superior está deshabilitada.</span><span class="sxs-lookup"><span data-stu-id="5841c-451">Otherwise, top-level node validation is disabled.</span></span> <span data-ttu-id="5841c-452">La opción predeterminada se puede invalidar si se establece la propiedad <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowValidatingTopLevelNodes*> en (`Startup.ConfigureServices`), como se muestra aquí:</span><span class="sxs-lookup"><span data-stu-id="5841c-452">The default option can be overridden by setting the <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowValidatingTopLevelNodes*> property in (`Startup.ConfigureServices`), as shown here:</span></span>

[!code-csharp[](validation/samples_snapshot/2.x/Startup.cs?name=snippet_AddMvc&highlight=4)]

## <a name="maximum-errors"></a><span data-ttu-id="5841c-453">Número máximo de errores</span><span class="sxs-lookup"><span data-stu-id="5841c-453">Maximum errors</span></span>

<span data-ttu-id="5841c-454">La validación se detiene cuando se alcanza el número máximo de errores (200 de forma predeterminada).</span><span class="sxs-lookup"><span data-stu-id="5841c-454">Validation stops when the maximum number of errors is reached (200 by default).</span></span> <span data-ttu-id="5841c-455">Puede configurar este número con el siguiente código en `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="5841c-455">You can configure this number with the following code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=3)]

## <a name="maximum-recursion"></a><span data-ttu-id="5841c-456">Recursividad máxima</span><span class="sxs-lookup"><span data-stu-id="5841c-456">Maximum recursion</span></span>

<span data-ttu-id="5841c-457"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> recorre el gráfico de objetos del modelo que se está validando.</span><span class="sxs-lookup"><span data-stu-id="5841c-457"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> traverses the object graph of the model being validated.</span></span> <span data-ttu-id="5841c-458">En el caso de los modelos muy profundos o infinitamente recursivos, la validación podría causar un desbordamiento de pila.</span><span class="sxs-lookup"><span data-stu-id="5841c-458">For models that are very deep or are infinitely recursive, validation may result in stack overflow.</span></span> <span data-ttu-id="5841c-459">[MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) proporciona una manera de detener pronto la validación si la recursividad del visitante supera la profundidad configurada.</span><span class="sxs-lookup"><span data-stu-id="5841c-459">[MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) provides a way to stop validation early if the visitor recursion exceeds a configured depth.</span></span> <span data-ttu-id="5841c-460">El valor predeterminado de `MvcOptions.MaxValidationDepth` es 32 cuando se ejecuta con `CompatibilityVersion.Version_2_2` o una versión posterior.</span><span class="sxs-lookup"><span data-stu-id="5841c-460">The default value of `MvcOptions.MaxValidationDepth` is 32 when running with `CompatibilityVersion.Version_2_2` or later.</span></span> <span data-ttu-id="5841c-461">Para las versiones anteriores, el valor es NULL, lo que significa que no hay restricción de profundidad.</span><span class="sxs-lookup"><span data-stu-id="5841c-461">For earlier versions, the value is null, which means no depth constraint.</span></span>

## <a name="automatic-short-circuit"></a><span data-ttu-id="5841c-462">Cortocircuito automático</span><span class="sxs-lookup"><span data-stu-id="5841c-462">Automatic short-circuit</span></span>

<span data-ttu-id="5841c-463">La validación cortocircuita (se omite) automáticamente si el gráfico de modelo no requiere validación.</span><span class="sxs-lookup"><span data-stu-id="5841c-463">Validation is automatically short-circuited (skipped) if the model graph doesn't require validation.</span></span> <span data-ttu-id="5841c-464">Entre los objetos para los que el tiempo de ejecución omite la validación se incluyen las colecciones de elementos primitivos (como `byte[]`, `string[]` y `Dictionary<string, string>`) y gráficos de objeto complejo que no tienen los validadores.</span><span class="sxs-lookup"><span data-stu-id="5841c-464">Objects that the runtime skips validation for include collections of primitives (such as `byte[]`, `string[]`, `Dictionary<string, string>`) and complex object graphs that don't have any validators.</span></span>

## <a name="disable-validation"></a><span data-ttu-id="5841c-465">Deshabilitación de la validación</span><span class="sxs-lookup"><span data-stu-id="5841c-465">Disable validation</span></span>

<span data-ttu-id="5841c-466">Para deshabilitar la validación:</span><span class="sxs-lookup"><span data-stu-id="5841c-466">To disable validation:</span></span>

1. <span data-ttu-id="5841c-467">Cree una implementación de `IObjectModelValidator` que no marque ningún campo como no válido.</span><span class="sxs-lookup"><span data-stu-id="5841c-467">Create an implementation of `IObjectModelValidator` that doesn't mark any fields as invalid.</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/NullObjectModelValidator.cs?name=snippet_DisableValidation)]

1. <span data-ttu-id="5841c-468">Agregue el código siguiente a `Startup.ConfigureServices` para reemplazar la implementación predeterminada de `IObjectModelValidator` en el contenedor de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="5841c-468">Add the following code to `Startup.ConfigureServices` to replace the default `IObjectModelValidator` implementation in the dependency injection container.</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_DisableValidation)]

<span data-ttu-id="5841c-469">Es posible que todavía vea errores de estado del modelo originados en el enlace de modelos.</span><span class="sxs-lookup"><span data-stu-id="5841c-469">You might still see model state errors that originate from model binding.</span></span>

## <a name="client-side-validation"></a><span data-ttu-id="5841c-470">Validación del lado cliente</span><span class="sxs-lookup"><span data-stu-id="5841c-470">Client-side validation</span></span>

<span data-ttu-id="5841c-471">La validación del lado cliente impide realizar el envío hasta que el formulario sea válido.</span><span class="sxs-lookup"><span data-stu-id="5841c-471">Client-side validation prevents submission until the form is valid.</span></span> <span data-ttu-id="5841c-472">El botón Enviar ejecuta JavaScript para enviar el formulario o mostrar mensajes de error.</span><span class="sxs-lookup"><span data-stu-id="5841c-472">The Submit button runs JavaScript that either submits the form or displays error messages.</span></span>

<span data-ttu-id="5841c-473">La validación del lado cliente evita un recorrido de ida y vuelta innecesario en el servidor cuando hay errores de entrada en un formulario.</span><span class="sxs-lookup"><span data-stu-id="5841c-473">Client-side validation avoids an unnecessary round trip to the server when there are input errors on a form.</span></span> <span data-ttu-id="5841c-474">Las siguientes referencias de script de *_Layout.cshtml* y *_ValidationScriptsPartial.cshtml* admiten la validación del lado cliente:</span><span class="sxs-lookup"><span data-stu-id="5841c-474">The following script references in *_Layout.cshtml* and *_ValidationScriptsPartial.cshtml* support client-side validation:</span></span>

[!code-cshtml[](validation/samples/2.x/ValidationSample/Views/Shared/_Layout.cshtml?name=snippet_ScriptTag)]

[!code-cshtml[](validation/samples/2.x/ValidationSample/Views/Shared/_ValidationScriptsPartial.cshtml?name=snippet_ScriptTags)]

<span data-ttu-id="5841c-475">El script [Validación discreta de jQuery](https://github.com/aspnet/jquery-validation-unobtrusive) es una biblioteca front-end personalizada de Microsoft que se basa en el conocido complemento [Validación de jQuery](https://jqueryvalidation.org/).</span><span class="sxs-lookup"><span data-stu-id="5841c-475">The [jQuery Unobtrusive Validation](https://github.com/aspnet/jquery-validation-unobtrusive) script is a custom Microsoft front-end library that builds on the popular [jQuery Validate](https://jqueryvalidation.org/) plugin.</span></span> <span data-ttu-id="5841c-476">Si no usa Validación discreta de jQuery, deberá codificar la misma lógica de validación dos veces: una vez en los atributos de validación del lado servidor en las propiedades del modelo y luego en los scripts del lado cliente.</span><span class="sxs-lookup"><span data-stu-id="5841c-476">Without jQuery Unobtrusive Validation, you would have to code the same validation logic in two places: once in the server-side validation attributes on model properties, and then again in client-side scripts.</span></span> <span data-ttu-id="5841c-477">En su lugar, los [asistentes de etiquetas](xref:mvc/views/tag-helpers/intro) y los [asistentes de HTML](xref:mvc/views/overview) usan los atributos de validación y escriben metadatos de las propiedades del modelo para representar atributos `data-` HTML 5 para los elementos de formulario que necesitan validación.</span><span class="sxs-lookup"><span data-stu-id="5841c-477">Instead, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use the validation attributes and type metadata from model properties to render HTML 5 `data-` attributes for the form elements that need validation.</span></span> <span data-ttu-id="5841c-478">Validación discreta de jQuery analiza los atributos `data-` y pasa la lógica a jQuery Validate. De este modo, la lógica de validación del lado servidor se "copia" de manera eficaz en el cliente.</span><span class="sxs-lookup"><span data-stu-id="5841c-478">jQuery Unobtrusive Validation parses the `data-` attributes and passes the logic to jQuery Validate, effectively "copying" the server-side validation logic to the client.</span></span> <span data-ttu-id="5841c-479">Puede mostrar errores de validación en el cliente mediante el uso de asistentes de etiquetas, como se muestra aquí:</span><span class="sxs-lookup"><span data-stu-id="5841c-479">You can display validation errors on the client using tag helpers as shown here:</span></span>

[!code-cshtml[](validation/samples/2.x/ValidationSample/Views/Movies/Create.cshtml?name=snippet_ReleaseDate&highlight=4-5)]

<span data-ttu-id="5841c-480">Los anteriores asistentes de etiquetas representan el siguiente código HTML.</span><span class="sxs-lookup"><span data-stu-id="5841c-480">The preceding tag helpers render the following HTML.</span></span>

```html
<form action="/Movies/Create" method="post">
    <div class="form-horizontal">
        <h4>Movie</h4>
        <div class="text-danger"></div>
        <div class="form-group">
            <label class="col-md-2 control-label" for="ReleaseDate">ReleaseDate</label>
            <div class="col-md-10">
                <input class="form-control" type="datetime"
                data-val="true" data-val-required="The ReleaseDate field is required."
                id="ReleaseDate" name="ReleaseDate" value="">
                <span class="text-danger field-validation-valid"
                data-valmsg-for="ReleaseDate" data-valmsg-replace="true"></span>
            </div>
        </div>
    </div>
</form>
```

<span data-ttu-id="5841c-481">Tenga en cuenta que los atributos `data-` en los resultados HTML corresponden a los atributos de validación para la propiedad `ReleaseDate`.</span><span class="sxs-lookup"><span data-stu-id="5841c-481">Notice that the `data-` attributes in the HTML output correspond to the validation attributes for the `ReleaseDate` property.</span></span> <span data-ttu-id="5841c-482">El atributo `data-val-required` contiene un mensaje de error que se muestra si el usuario no rellena el campo de fecha de estreno.</span><span class="sxs-lookup"><span data-stu-id="5841c-482">The `data-val-required` attribute contains an error message to display if the user doesn't fill in the release date field.</span></span> <span data-ttu-id="5841c-483">La validación discreta de jQuery pasa este valor al método [required()](https://jqueryvalidation.org/required-method/) de la validación de jQuery, que luego muestra ese mensaje en el elemento **\<span>** que lo acompaña.</span><span class="sxs-lookup"><span data-stu-id="5841c-483">jQuery Unobtrusive Validation passes this value to the jQuery Validate [required()](https://jqueryvalidation.org/required-method/) method, which then displays that message in the accompanying **\<span>** element.</span></span>

<span data-ttu-id="5841c-484">La validación del tipo de datos se basa en el tipo .NET de una propiedad, a menos que lo reemplace un atributo `[DataType]`.</span><span class="sxs-lookup"><span data-stu-id="5841c-484">Data type validation is based on the .NET type of a property, unless that is overridden by a `[DataType]` attribute.</span></span> <span data-ttu-id="5841c-485">Los exploradores tienen sus propios mensajes de error de predeterminados, pero el paquete de Validación discreta de jQuery Validate puede invalidar esos mensajes.</span><span class="sxs-lookup"><span data-stu-id="5841c-485">Browsers have their own default error messages, but the jQuery Validation Unobtrusive Validation package can override those messages.</span></span> <span data-ttu-id="5841c-486">Los atributos y las subclases `[DataType]`, como `[EmailAddress]`, permiten especificar el mensaje de error.</span><span class="sxs-lookup"><span data-stu-id="5841c-486">`[DataType]` attributes and subclasses such as `[EmailAddress]` let you specify the error message.</span></span>

### <a name="add-validation-to-dynamic-forms"></a><span data-ttu-id="5841c-487">Agregar validación a formularios dinámicos</span><span class="sxs-lookup"><span data-stu-id="5841c-487">Add Validation to Dynamic Forms</span></span>

<span data-ttu-id="5841c-488">Validación discreta de jQuery pasa los parámetros y la lógica de validación a jQuery Validate cuando la página se carga por primera vez.</span><span class="sxs-lookup"><span data-stu-id="5841c-488">jQuery Unobtrusive Validation passes validation logic and parameters to jQuery Validate when the page first loads.</span></span> <span data-ttu-id="5841c-489">Por lo tanto, la validación no funciona automáticamente en los formularios generados dinámicamente.</span><span class="sxs-lookup"><span data-stu-id="5841c-489">Therefore, validation doesn't work automatically on dynamically generated forms.</span></span> <span data-ttu-id="5841c-490">Para habilitar la validación, hay que indicarle a Validación discreta de jQuery que analice el formulario dinámico inmediatamente después de su creación.</span><span class="sxs-lookup"><span data-stu-id="5841c-490">To enable validation, tell jQuery Unobtrusive Validation to parse the dynamic form immediately after you create it.</span></span> <span data-ttu-id="5841c-491">Por ejemplo, en el código siguiente se configura la validación del lado cliente en un formulario agregado mediante AJAX.</span><span class="sxs-lookup"><span data-stu-id="5841c-491">For example, the following code sets up client-side validation on a form added via AJAX.</span></span>

```javascript
$.get({
    url: "https://url/that/returns/a/form",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add form. " + errorThrown);
    },
    success: function(newFormHTML) {
        var container = document.getElementById("form-container");
        container.insertAdjacentHTML("beforeend", newFormHTML);
        var forms = container.getElementsByTagName("form");
        var newForm = forms[forms.length - 1];
        $.validator.unobtrusive.parse(newForm);
    }
})
```

<span data-ttu-id="5841c-492">El método `$.validator.unobtrusive.parse()` acepta un selector de jQuery para su único argumento.</span><span class="sxs-lookup"><span data-stu-id="5841c-492">The `$.validator.unobtrusive.parse()` method accepts a jQuery selector for its one argument.</span></span> <span data-ttu-id="5841c-493">Este método indica a Validación discreta de jQuery que analice los atributos `data-` de formularios dentro de ese selector.</span><span class="sxs-lookup"><span data-stu-id="5841c-493">This method tells jQuery Unobtrusive Validation to parse the `data-` attributes of forms within that selector.</span></span> <span data-ttu-id="5841c-494">Después, los valores de estos atributos se pasan al complemento de jQuery Validate.</span><span class="sxs-lookup"><span data-stu-id="5841c-494">The values of those attributes are then passed to the jQuery Validate plugin.</span></span>

### <a name="add-validation-to-dynamic-controls"></a><span data-ttu-id="5841c-495">Agregar validación a controles dinámicos</span><span class="sxs-lookup"><span data-stu-id="5841c-495">Add Validation to Dynamic Controls</span></span>

<span data-ttu-id="5841c-496">El método `$.validator.unobtrusive.parse()` funciona en todo el formulario, no en los controles individuales generados dinámicamente, como `<input>` y `<select/>`.</span><span class="sxs-lookup"><span data-stu-id="5841c-496">The `$.validator.unobtrusive.parse()` method works on an entire form, not on individual dynamically generated controls, such as `<input>` and `<select/>`.</span></span> <span data-ttu-id="5841c-497">Para volver a analizar el formulario, quite los datos de validación que se agregaron cuando el formulario se analizó anteriormente, como se muestra en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="5841c-497">To reparse the form, remove the validation data that was added when the form was parsed earlier, as shown in the following example:</span></span>

```javascript
$.get({
    url: "https://url/that/returns/a/control",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add control. " + errorThrown);
    },
    success: function(newInputHTML) {
        var form = document.getElementById("my-form");
        form.insertAdjacentHTML("beforeend", newInputHTML);
        $(form).removeData("validator")    // Added by jQuery Validate
               .removeData("unobtrusiveValidation");   // Added by jQuery Unobtrusive Validation
        $.validator.unobtrusive.parse(form);
    }
})
```

## <a name="custom-client-side-validation"></a><span data-ttu-id="5841c-498">Validación del lado cliente personalizada</span><span class="sxs-lookup"><span data-stu-id="5841c-498">Custom client-side validation</span></span>

<span data-ttu-id="5841c-499">Para personalizar la validación del lado cliente, es necesario generar atributos HTML `data-` que funcionen con un adaptador personalizado de jQuery Validate.</span><span class="sxs-lookup"><span data-stu-id="5841c-499">Custom client-side validation is done by generating `data-` HTML attributes that work with a custom jQuery Validate adapter.</span></span> <span data-ttu-id="5841c-500">El siguiente ejemplo de código de adaptador se escribió para los atributos `ClassicMovie` y `ClassicMovie2` que se introdujeron anteriormente en este artículo:</span><span class="sxs-lookup"><span data-stu-id="5841c-500">The following sample adapter code was written for the `ClassicMovie` and `ClassicMovie2` attributes that were introduced earlier in this article:</span></span>

[!code-javascript[](validation/samples/2.x/ValidationSample/wwwroot/js/classicMovieValidator.js?name=snippet_UnobtrusiveValidation)]

<span data-ttu-id="5841c-501">Para obtener información sobre cómo escribir adaptadores, vea la [documentación de jQuery Validate](https://jqueryvalidation.org/documentation/).</span><span class="sxs-lookup"><span data-stu-id="5841c-501">For information about how to write adapters, see the [jQuery Validate documentation](https://jqueryvalidation.org/documentation/).</span></span>

<span data-ttu-id="5841c-502">El uso de un adaptador para un campo determinado se desencadena mediante atributos `data-` que:</span><span class="sxs-lookup"><span data-stu-id="5841c-502">The use of an adapter for a given field is triggered by `data-` attributes that:</span></span>

* <span data-ttu-id="5841c-503">Marcan que el campo está sujeto a validación (`data-val="true"`).</span><span class="sxs-lookup"><span data-stu-id="5841c-503">Flag the field as being subject to validation (`data-val="true"`).</span></span>
* <span data-ttu-id="5841c-504">Identifican un nombre de regla de validación y un texto de mensaje de error (por ejemplo, `data-val-rulename="Error message."`).</span><span class="sxs-lookup"><span data-stu-id="5841c-504">Identify a validation rule name and error message text (for example, `data-val-rulename="Error message."`).</span></span>
* <span data-ttu-id="5841c-505">Proporcionan los parámetros adicionales que necesite el validador (por ejemplo, `data-val-rulename-parm1="value"`).</span><span class="sxs-lookup"><span data-stu-id="5841c-505">Provide any additional parameters the validator needs (for example, `data-val-rulename-parm1="value"`).</span></span>

<span data-ttu-id="5841c-506">En el ejemplo siguiente se muestran los atributos `data-` para el atributo `ClassicMovie` de la aplicación de ejemplo:</span><span class="sxs-lookup"><span data-stu-id="5841c-506">The following example shows the `data-` attributes for the sample app's `ClassicMovie` attribute:</span></span>

```html
<input class="form-control" type="datetime"
    data-val="true"
    data-val-classicmovie1="Classic movies must have a release year earlier than 1960."
    data-val-classicmovie1-year="1960"
    data-val-required="The ReleaseDate field is required."
    id="ReleaseDate" name="ReleaseDate" value="">
```

<span data-ttu-id="5841c-507">Como se indicó anteriormente, los [asistentes de etiquetas](xref:mvc/views/tag-helpers/intro) y los [asistentes de HTML](xref:mvc/views/overview) usan información procedente de los atributos de validación para representar atributos `data-`.</span><span class="sxs-lookup"><span data-stu-id="5841c-507">As noted earlier, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use information from validation attributes to render `data-` attributes.</span></span> <span data-ttu-id="5841c-508">Hay dos opciones para escribir código que dé como resultado la creación de atributos HTML `data-` personalizados:</span><span class="sxs-lookup"><span data-stu-id="5841c-508">There are two options for writing code that results in the creation of custom `data-` HTML attributes:</span></span>

* <span data-ttu-id="5841c-509">Puede crear una clase que derive de `AttributeAdapterBase<TAttribute>` y una clase que implemente `IValidationAttributeAdapterProvider` y, después, registrar el atributo y su adaptador en la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="5841c-509">Create a class that derives from `AttributeAdapterBase<TAttribute>` and a class that implements `IValidationAttributeAdapterProvider`, and register your attribute and its adapter in DI.</span></span> <span data-ttu-id="5841c-510">Este método sigue el [principio de responsabilidad única](https://wikipedia.org/wiki/Single_responsibility_principle), ya que el código de validación relacionado con servidor y el relacionado con el cliente se encuentran en clases independientes.</span><span class="sxs-lookup"><span data-stu-id="5841c-510">This method follows the [single responsibility principal](https://wikipedia.org/wiki/Single_responsibility_principle) in that server-related and client-related validation code is in separate classes.</span></span> <span data-ttu-id="5841c-511">El adaptador también cuenta con una ventaja: debido a que está registrado en la inserción de dependencias, tiene a su disposición otros servicios de la inserción de dependencias si es necesario.</span><span class="sxs-lookup"><span data-stu-id="5841c-511">The adapter also has the advantage that since it is registered in DI, other services in DI are available to it if needed.</span></span>
* <span data-ttu-id="5841c-512">Puede implementar `IClientModelValidator` en la clase `ValidationAttribute`.</span><span class="sxs-lookup"><span data-stu-id="5841c-512">Implement `IClientModelValidator` in your `ValidationAttribute` class.</span></span> <span data-ttu-id="5841c-513">Este método es adecuado si el atributo no realiza ninguna validación en el lado servidor y no necesita ningún servicio de la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="5841c-513">This method might be appropriate if the attribute doesn't do any server-side validation and doesn't need any services from DI.</span></span>

### <a name="attributeadapter-for-client-side-validation"></a><span data-ttu-id="5841c-514">AttributeAdapter para la validación del lado cliente</span><span class="sxs-lookup"><span data-stu-id="5841c-514">AttributeAdapter for client-side validation</span></span>

<span data-ttu-id="5841c-515">Este método para representar atributos `data-` en HTML es lo que usa el atributo `ClassicMovie` en la aplicación de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="5841c-515">This method of rendering `data-` attributes in HTML is used by the `ClassicMovie` attribute in the sample app.</span></span> <span data-ttu-id="5841c-516">Para agregar la validación de cliente mediante este método:</span><span class="sxs-lookup"><span data-stu-id="5841c-516">To add client validation by using this method:</span></span>

1. <span data-ttu-id="5841c-517">Cree una clase de adaptador de atributo para el atributo de validación personalizado.</span><span class="sxs-lookup"><span data-stu-id="5841c-517">Create an attribute adapter class for the custom validation attribute.</span></span> <span data-ttu-id="5841c-518">Derive la clase de [AttributeAdapterBase\<T>](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="5841c-518">Derive the class from [AttributeAdapterBase\<T>](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2).</span></span> <span data-ttu-id="5841c-519">Cree un método `AddValidation` que agregue atributos `data-` a la salida representada, tal como se muestra en este ejemplo:</span><span class="sxs-lookup"><span data-stu-id="5841c-519">Create an `AddValidation` method that adds `data-` attributes to the rendered output, as shown in this example:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/ClassicMovieAttributeAdapter.cs?name=snippet_ClassicMovieAttributeAdapter)]

1. <span data-ttu-id="5841c-520">Cree una clase de proveedor de adaptador que implemente <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>.</span><span class="sxs-lookup"><span data-stu-id="5841c-520">Create an adapter provider class that implements <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>.</span></span> <span data-ttu-id="5841c-521">En el método `GetAttributeAdapter`, pase el atributo personalizado al constructor del adaptador, como se muestra en este ejemplo:</span><span class="sxs-lookup"><span data-stu-id="5841c-521">In the `GetAttributeAdapter` method pass in the custom attribute to the adapter's constructor, as shown in this example:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/CustomValidationAttributeAdapterProvider.cs?name=snippet_CustomValidationAttributeAdapterProvider)]

1. <span data-ttu-id="5841c-522">Registre el proveedor del adaptador para la inserción de dependencias en `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="5841c-522">Register the adapter provider for DI in `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=8-10)]

### <a name="iclientmodelvalidator-for-client-side-validation"></a><span data-ttu-id="5841c-523">IClientModelValidator para la validación del lado cliente</span><span class="sxs-lookup"><span data-stu-id="5841c-523">IClientModelValidator for client-side validation</span></span>

<span data-ttu-id="5841c-524">Este método para representar atributos `data-` en HTML es lo que usa el atributo `ClassicMovie2` en la aplicación de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="5841c-524">This method of rendering `data-` attributes in HTML is used by the `ClassicMovie2` attribute in the sample app.</span></span> <span data-ttu-id="5841c-525">Para agregar la validación de cliente mediante este método:</span><span class="sxs-lookup"><span data-stu-id="5841c-525">To add client validation by using this method:</span></span>

* <span data-ttu-id="5841c-526">En el atributo de validación personalizado, implemente la interfaz `IClientModelValidator` y cree un método `AddValidation`.</span><span class="sxs-lookup"><span data-stu-id="5841c-526">In the custom validation attribute, implement the `IClientModelValidator` interface and create an `AddValidation` method.</span></span> <span data-ttu-id="5841c-527">En el método `AddValidation`, agregue atributos `data-` para la validación, como se muestra en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="5841c-527">In the `AddValidation` method, add `data-` attributes for validation, as shown in the following example:</span></span>

  [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/ClassicMovie2Attribute.cs?name=snippet_ClassicMovie2Attribute)]

## <a name="disable-client-side-validation"></a><span data-ttu-id="5841c-528">Deshabilitación de la validación del lado cliente</span><span class="sxs-lookup"><span data-stu-id="5841c-528">Disable client-side validation</span></span>

<span data-ttu-id="5841c-529">El código siguiente deshabilita la validación de cliente en las vistas de MVC:</span><span class="sxs-lookup"><span data-stu-id="5841c-529">The following code disables client validation in MVC views:</span></span>

[!code-csharp[](validation/samples_snapshot/2.x/Startup2.cs?name=snippet_DisableClientValidation)]

<span data-ttu-id="5841c-530">Y en Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="5841c-530">And in Razor Pages:</span></span>

[!code-csharp[](validation/samples_snapshot/2.x/Startup3.cs?name=snippet_DisableClientValidation)]

<span data-ttu-id="5841c-531">Otra opción para deshabilitar la validación de cliente consiste en marcar como comentario la referencia a `_ValidationScriptsPartial` en el archivo *.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="5841c-531">Another option for disabling client validation is to comment out the reference to `_ValidationScriptsPartial` in your *.cshtml* file.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5841c-532">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="5841c-532">Additional resources</span></span>

* [<span data-ttu-id="5841c-533">Espacio de nombres System.ComponentModel.DataAnnotations</span><span class="sxs-lookup"><span data-stu-id="5841c-533">System.ComponentModel.DataAnnotations namespace</span></span>](xref:System.ComponentModel.DataAnnotations)
* [<span data-ttu-id="5841c-534">Enlace de modelos</span><span class="sxs-lookup"><span data-stu-id="5841c-534">Model Binding</span></span>](model-binding.md)

::: moniker-end
