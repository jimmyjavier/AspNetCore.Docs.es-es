---
title: Globalización y localización en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre la manera en que ASP.NET Core proporciona servicios y software intermedio para la localización de contenido en diferentes idiomas y referencias culturales.
ms.author: riande
ms.date: 11/30/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/localization
ms.openlocfilehash: 1111c0c57baf5bfd52b26b553179338100b9ba79
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776570"
---
# <a name="globalization-and-localization-in-aspnet-core"></a><span data-ttu-id="d8f28-103">Globalización y localización en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d8f28-103">Globalization and localization in ASP.NET Core</span></span>

<span data-ttu-id="d8f28-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/) y [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span><span class="sxs-lookup"><span data-stu-id="d8f28-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="d8f28-105">El hecho de crear un sitio web multilingüe con ASP.NET Core permite que este llegue a un público más amplio.</span><span class="sxs-lookup"><span data-stu-id="d8f28-105">Creating a multilingual website with ASP.NET Core will allow your site to reach a wider audience.</span></span> <span data-ttu-id="d8f28-106">ASP.NET Core proporciona servicios y software intermedio para la localización en diferentes idiomas y referencias culturales.</span><span class="sxs-lookup"><span data-stu-id="d8f28-106">ASP.NET Core provides services and middleware for localizing into different languages and cultures.</span></span>

<span data-ttu-id="d8f28-107">La internacionalización conlleva [globalización](/dotnet/api/system.globalization) y [localización](/dotnet/standard/globalization-localization/localization).</span><span class="sxs-lookup"><span data-stu-id="d8f28-107">Internationalization involves [Globalization](/dotnet/api/system.globalization) and [Localization](/dotnet/standard/globalization-localization/localization).</span></span> <span data-ttu-id="d8f28-108">La globalización es el proceso de diseñar aplicaciones que admiten diferentes referencias culturales.</span><span class="sxs-lookup"><span data-stu-id="d8f28-108">Globalization is the process of designing apps that support different cultures.</span></span> <span data-ttu-id="d8f28-109">La globalización agrega compatibilidad con la entrada, la visualización y la salida de un conjunto definido de scripts de lenguaje relacionados con áreas geográficas específicas.</span><span class="sxs-lookup"><span data-stu-id="d8f28-109">Globalization adds support for input, display, and output of a defined set of language scripts that relate to specific geographic areas.</span></span>

<span data-ttu-id="d8f28-110">La localización es el proceso de adaptar una aplicación globalizada, que ya se ha procesado para la localizabilidad, a una determinada referencia cultural o configuración regional.</span><span class="sxs-lookup"><span data-stu-id="d8f28-110">Localization is the process of adapting a globalized app, which you have already processed for localizability, to a particular culture/locale.</span></span> <span data-ttu-id="d8f28-111">Para más información, vea **Términos relacionados con la globalización y la localización** al final de este documento.</span><span class="sxs-lookup"><span data-stu-id="d8f28-111">For more information see **Globalization and localization terms** near the end of this document.</span></span>

<span data-ttu-id="d8f28-112">La localización de la aplicación implica lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="d8f28-112">App localization involves the following:</span></span>

1. <span data-ttu-id="d8f28-113">Hacer que el contenido de la aplicación sea localizable</span><span class="sxs-lookup"><span data-stu-id="d8f28-113">Make the app's content localizable</span></span>

2. <span data-ttu-id="d8f28-114">Proporcionar recursos localizados para los idiomas y las referencias culturales admitidos</span><span class="sxs-lookup"><span data-stu-id="d8f28-114">Provide localized resources for the languages and cultures you support</span></span>

3. <span data-ttu-id="d8f28-115">Implementar una estrategia para seleccionar el idioma o la referencia cultural de cada solicitud</span><span class="sxs-lookup"><span data-stu-id="d8f28-115">Implement a strategy to select the language/culture for each request</span></span>

<span data-ttu-id="d8f28-116">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d8f28-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="make-the-apps-content-localizable"></a><span data-ttu-id="d8f28-117">Hacer que el contenido de la aplicación sea localizable</span><span class="sxs-lookup"><span data-stu-id="d8f28-117">Make the app's content localizable</span></span>

<span data-ttu-id="d8f28-118">`IStringLocalizer` y `IStringLocalizer<T>`, introducidos en ASP.NET Core, están diseñados para mejorar la productividad al desarrollar aplicaciones localizadas.</span><span class="sxs-lookup"><span data-stu-id="d8f28-118">Introduced in ASP.NET Core, `IStringLocalizer` and `IStringLocalizer<T>` were architected to improve productivity when developing localized apps.</span></span> <span data-ttu-id="d8f28-119">`IStringLocalizer` usa [ResourceManager](/dotnet/api/system.resources.resourcemanager) y [ResourceReader](/dotnet/api/system.resources.resourcereader) para proporcionar recursos específicos de la referencia cultural en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="d8f28-119">`IStringLocalizer` uses the [ResourceManager](/dotnet/api/system.resources.resourcemanager) and [ResourceReader](/dotnet/api/system.resources.resourcereader) to provide culture-specific resources at run time.</span></span> <span data-ttu-id="d8f28-120">La interfaz simple tiene un indizador y un `IEnumerable` para devolver las cadenas localizadas.</span><span class="sxs-lookup"><span data-stu-id="d8f28-120">The simple interface has an indexer and an `IEnumerable` for returning localized strings.</span></span> <span data-ttu-id="d8f28-121">`IStringLocalizer` no necesita que se almacenen las cadenas de idioma predeterminado en un archivo de recursos.</span><span class="sxs-lookup"><span data-stu-id="d8f28-121">`IStringLocalizer` doesn't require you to store the default language strings in a resource file.</span></span> <span data-ttu-id="d8f28-122">Puede desarrollar una aplicación destinada a la localización sin necesidad de crear archivos de recursos al principio de la fase de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="d8f28-122">You can develop an app targeted for localization and not need to create resource files early in development.</span></span> <span data-ttu-id="d8f28-123">En el código siguiente se muestra cómo ajustar la cadena "About Title" para la localización.</span><span class="sxs-lookup"><span data-stu-id="d8f28-123">The code below shows how to wrap the string "About Title" for localization.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/AboutController.cs)]

<span data-ttu-id="d8f28-124">En el código anterior, la implementación de `IStringLocalizer<T>` procede de la [inserción de dependencias](dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="d8f28-124">In the code above, the `IStringLocalizer<T>` implementation comes from [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="d8f28-125">Si no se encuentra el valor localizado de "About Title, se devuelve la clave de indizador, es decir, la cadena "About Title".</span><span class="sxs-lookup"><span data-stu-id="d8f28-125">If the localized value of "About Title" isn't found, then the indexer key is returned, that is, the string "About Title".</span></span> <span data-ttu-id="d8f28-126">Puede dejar las cadenas literales del idioma predeterminado en la aplicación y ajustarlas en el localizador, de modo que se pueda centrar en el desarrollo de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d8f28-126">You can leave the default language literal strings in the app and wrap them in the localizer, so that you can focus on developing the app.</span></span> <span data-ttu-id="d8f28-127">Desarrolle la aplicación con el idioma predeterminado y prepárela para el proceso de localización sin necesidad de crear primero un archivo de recursos predeterminado.</span><span class="sxs-lookup"><span data-stu-id="d8f28-127">You develop your app with your default language and prepare it for the localization step without first creating a default resource file.</span></span> <span data-ttu-id="d8f28-128">También puede seguir el método tradicional y proporcionar una clave para recuperar la cadena de idioma predeterminado.</span><span class="sxs-lookup"><span data-stu-id="d8f28-128">Alternatively, you can use the traditional approach and provide a key to retrieve the default language string.</span></span> <span data-ttu-id="d8f28-129">El nuevo flujo de trabajo, que carece de archivo *.resx* de idioma predeterminado y simplemente ajusta los literales de cadena, puede ahorrar a muchos desarrolladores la sobrecarga de localizar una aplicación.</span><span class="sxs-lookup"><span data-stu-id="d8f28-129">For many developers the new workflow of not having a default language *.resx* file and simply wrapping the string literals can reduce the overhead of localizing an app.</span></span> <span data-ttu-id="d8f28-130">Otros desarrolladores preferirán el flujo de trabajo tradicional, ya que facilita el trabajo con literales de cadena más largos, así como la actualización de las cadenas localizadas.</span><span class="sxs-lookup"><span data-stu-id="d8f28-130">Other developers will prefer the traditional work flow as it can make it easier to work with longer string literals and make it easier to update localized strings.</span></span>

<span data-ttu-id="d8f28-131">Use la implementación de `IHtmlLocalizer<T>` para los recursos que contienen HTML.</span><span class="sxs-lookup"><span data-stu-id="d8f28-131">Use the `IHtmlLocalizer<T>` implementation for resources that contain HTML.</span></span> <span data-ttu-id="d8f28-132">El HTML de `IHtmlLocalizer` codifica los argumentos a los que se da formato en la cadena de recursos, pero no codifica como HTML la cadena de recursos en sí misma.</span><span class="sxs-lookup"><span data-stu-id="d8f28-132">`IHtmlLocalizer` HTML encodes arguments that are formatted in the resource string, but doesn't HTML encode the resource string itself.</span></span> <span data-ttu-id="d8f28-133">En el ejemplo que se muestra a continuación, solo está codificado en HTML el valor del parámetro `name`.</span><span class="sxs-lookup"><span data-stu-id="d8f28-133">In the sample highlighted below, only the value of `name` parameter is HTML encoded.</span></span>

[!code-csharp[](../fundamentals/localization/sample/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

<span data-ttu-id="d8f28-134">**Nota:** Generalmente solo le interesa localizar texto, no HTML.</span><span class="sxs-lookup"><span data-stu-id="d8f28-134">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="d8f28-135">En el nivel más bajo, puede obtener `IStringLocalizerFactory` de la [inserción de dependencias](dependency-injection.md):</span><span class="sxs-lookup"><span data-stu-id="d8f28-135">At the lowest level, you can get `IStringLocalizerFactory` out of [Dependency Injection](dependency-injection.md):</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

<span data-ttu-id="d8f28-136">En el código anterior se muestran los dos métodos factory.Create.</span><span class="sxs-lookup"><span data-stu-id="d8f28-136">The code above demonstrates each of the two factory create methods.</span></span>

<span data-ttu-id="d8f28-137">Puede dividir las cadenas localizadas por controlador o por área, o bien tener un solo contenedor.</span><span class="sxs-lookup"><span data-stu-id="d8f28-137">You can partition your localized strings by controller, area, or have just one container.</span></span> <span data-ttu-id="d8f28-138">En la aplicación de ejemplo, se usa una clase ficticia denominada `SharedResource` para los recursos compartidos.</span><span class="sxs-lookup"><span data-stu-id="d8f28-138">In the sample app, a dummy class named `SharedResource` is used for shared resources.</span></span>

[!code-csharp[](localization/sample/Localization/Resources/SharedResource.cs)]

<span data-ttu-id="d8f28-139">Algunos programadores usan la clase `Startup` para contener cadenas globales o compartidas.</span><span class="sxs-lookup"><span data-stu-id="d8f28-139">Some developers use the `Startup` class to contain global or shared strings.</span></span> <span data-ttu-id="d8f28-140">En el ejemplo siguiente, se usan los localizadores `InfoController` y `SharedResource`:</span><span class="sxs-lookup"><span data-stu-id="d8f28-140">In the sample below, the `InfoController` and the `SharedResource` localizers are used:</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a><span data-ttu-id="d8f28-141">Localización de vista</span><span class="sxs-lookup"><span data-stu-id="d8f28-141">View localization</span></span>

<span data-ttu-id="d8f28-142">El servicio `IViewLocalizer` proporciona cadenas localizadas para una [vista](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="d8f28-142">The `IViewLocalizer` service provides localized strings for a [view](xref:mvc/views/overview).</span></span> <span data-ttu-id="d8f28-143">La clase `ViewLocalizer` implementa esta interfaz y busca la ubicación del recurso en la ruta de acceso del archivo de vista.</span><span class="sxs-lookup"><span data-stu-id="d8f28-143">The `ViewLocalizer` class implements this interface and finds the resource location from the view file path.</span></span> <span data-ttu-id="d8f28-144">En el código siguiente se muestra cómo usar la implementación predeterminada de `IViewLocalizer`:</span><span class="sxs-lookup"><span data-stu-id="d8f28-144">The following code shows how to use the default implementation of `IViewLocalizer`:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Home/About.cshtml)]

<span data-ttu-id="d8f28-145">La implementación predeterminada de `IViewLocalizer` busca el archivo de recursos según el nombre del archivo de vista.</span><span class="sxs-lookup"><span data-stu-id="d8f28-145">The default implementation of `IViewLocalizer` finds the resource file based on the view's file name.</span></span> <span data-ttu-id="d8f28-146">No se puede usar un archivo de recursos compartidos global.</span><span class="sxs-lookup"><span data-stu-id="d8f28-146">There's no option to use a global shared resource file.</span></span> <span data-ttu-id="d8f28-147">`ViewLocalizer` implementa el localizador mediante `IHtmlLocalizer`, por lo que Razor no codifica como HTML la cadena localizada.</span><span class="sxs-lookup"><span data-stu-id="d8f28-147">`ViewLocalizer` implements the localizer using `IHtmlLocalizer`, so Razor doesn't HTML encode the localized string.</span></span> <span data-ttu-id="d8f28-148">Puede parametrizar las cadenas de recursos y `IViewLocalizer` codificará como HTML los parámetros, pero no la cadena de recursos.</span><span class="sxs-lookup"><span data-stu-id="d8f28-148">You can parameterize resource strings and `IViewLocalizer` will HTML encode the parameters, but not the resource string.</span></span> <span data-ttu-id="d8f28-149">Observe el siguiente marcado de Razor:</span><span class="sxs-lookup"><span data-stu-id="d8f28-149">Consider the following Razor markup:</span></span>

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

<span data-ttu-id="d8f28-150">Un archivo de recursos en francés podría contener lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="d8f28-150">A French resource file could contain the following:</span></span>

| <span data-ttu-id="d8f28-151">Key</span><span class="sxs-lookup"><span data-stu-id="d8f28-151">Key</span></span> | <span data-ttu-id="d8f28-152">Valor</span><span class="sxs-lookup"><span data-stu-id="d8f28-152">Value</span></span> |
| ----- | ------ |
| `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |

<span data-ttu-id="d8f28-153">La vista representada contendría el marcado HTML del archivo de recursos.</span><span class="sxs-lookup"><span data-stu-id="d8f28-153">The rendered view would contain the HTML markup from the resource file.</span></span>

<span data-ttu-id="d8f28-154">**Nota:** Generalmente solo le interesa localizar texto, no HTML.</span><span class="sxs-lookup"><span data-stu-id="d8f28-154">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="d8f28-155">Para usar un archivo de recursos compartido en una vista, inserte `IHtmlLocalizer<T>`:</span><span class="sxs-lookup"><span data-stu-id="d8f28-155">To use a shared resource file in a view, inject `IHtmlLocalizer<T>`:</span></span>

[!code-cshtml[](../fundamentals/localization/sample/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a><span data-ttu-id="d8f28-156">Localización de DataAnnotations</span><span class="sxs-lookup"><span data-stu-id="d8f28-156">DataAnnotations localization</span></span>

<span data-ttu-id="d8f28-157">Los mensajes de error de DataAnnotations se localizan con `IStringLocalizer<T>`.</span><span class="sxs-lookup"><span data-stu-id="d8f28-157">DataAnnotations error messages are localized with `IStringLocalizer<T>`.</span></span> <span data-ttu-id="d8f28-158">Mediante la opción `ResourcesPath = "Resources"`, es posible almacenar los mensajes de error en `RegisterViewModel` en cualquiera de las rutas de acceso siguientes:</span><span class="sxs-lookup"><span data-stu-id="d8f28-158">Using the option `ResourcesPath = "Resources"`, the error messages in `RegisterViewModel` can be stored in either of the following paths:</span></span>

* <span data-ttu-id="d8f28-159">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="d8f28-159">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span></span>
* <span data-ttu-id="d8f28-160">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="d8f28-160">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span></span>

[!code-csharp[](localization/sample/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

<span data-ttu-id="d8f28-161">En ASP.NET Core MVC 1.1.0 y versiones posteriores, los atributos que no son de validación están localizados.</span><span class="sxs-lookup"><span data-stu-id="d8f28-161">In ASP.NET Core MVC 1.1.0 and higher, non-validation attributes are localized.</span></span> <span data-ttu-id="d8f28-162">ASP.NET Core MVC 1.0 **no** busca cadenas localizadas para los atributos que no son de validación.</span><span class="sxs-lookup"><span data-stu-id="d8f28-162">ASP.NET Core MVC 1.0 does **not** look up localized strings for non-validation attributes.</span></span>

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a><span data-ttu-id="d8f28-163">Uso de una cadena de recursos para varias clases</span><span class="sxs-lookup"><span data-stu-id="d8f28-163">Using one resource string for multiple classes</span></span>

<span data-ttu-id="d8f28-164">En el código siguiente se muestra cómo usar una cadena de recursos para atributos de validación con varias clases:</span><span class="sxs-lookup"><span data-stu-id="d8f28-164">The following code shows how to use one resource string for validation attributes with multiple classes:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddDataAnnotationsLocalization(options => {
            options.DataAnnotationLocalizerProvider = (type, factory) =>
                factory.Create(typeof(SharedResource));
        });
}
```

<span data-ttu-id="d8f28-165">En el código anterior, `SharedResource` es la clase correspondiente al archivo resx donde se almacenan los mensajes de validación.</span><span class="sxs-lookup"><span data-stu-id="d8f28-165">In the preceding code, `SharedResource` is the class corresponding to the resx where your validation messages are stored.</span></span> <span data-ttu-id="d8f28-166">Según este método, DataAnnotations solo usará `SharedResource`, en lugar del recurso de cada clase.</span><span class="sxs-lookup"><span data-stu-id="d8f28-166">With this approach, DataAnnotations will only use `SharedResource`, rather than the resource for each class.</span></span>

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a><span data-ttu-id="d8f28-167">Proporcionar recursos localizados para los idiomas y las referencias culturales admitidos</span><span class="sxs-lookup"><span data-stu-id="d8f28-167">Provide localized resources for the languages and cultures you support</span></span>

### <a name="supportedcultures-and-supporteduicultures"></a><span data-ttu-id="d8f28-168">SupportedCultures y SupportedUICultures</span><span class="sxs-lookup"><span data-stu-id="d8f28-168">SupportedCultures and SupportedUICultures</span></span>

<span data-ttu-id="d8f28-169">ASP.NET Core permite especificar dos valores de referencia cultural, `SupportedCultures` y `SupportedUICultures`.</span><span class="sxs-lookup"><span data-stu-id="d8f28-169">ASP.NET Core allows you to specify two culture values, `SupportedCultures` and `SupportedUICultures`.</span></span> <span data-ttu-id="d8f28-170">El objeto [CultureInfo](/dotnet/api/system.globalization.cultureinfo) para `SupportedCultures` determina los resultados de funciones dependientes de la referencia cultural, como el formato de fecha, hora, número y moneda.</span><span class="sxs-lookup"><span data-stu-id="d8f28-170">The [CultureInfo](/dotnet/api/system.globalization.cultureinfo) object for `SupportedCultures` determines the results of culture-dependent functions, such as date, time, number, and currency formatting.</span></span> <span data-ttu-id="d8f28-171">`SupportedCultures` también determina el criterio de ordenación del texto, las convenciones sobre el uso de mayúsculas y minúsculas, y las comparaciones de cadenas.</span><span class="sxs-lookup"><span data-stu-id="d8f28-171">`SupportedCultures` also determines the sorting order of text, casing conventions, and string comparisons.</span></span> <span data-ttu-id="d8f28-172">Vea [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) para obtener más información sobre la manera en que el servidor obtiene la referencia cultural.</span><span class="sxs-lookup"><span data-stu-id="d8f28-172">See [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) for more info on how the server gets the Culture.</span></span> <span data-ttu-id="d8f28-173">`SupportedUICultures` determina qué cadenas traducidas buscará [ResourceManager](/dotnet/api/system.resources.resourcemanager) (en archivos *.resx*).</span><span class="sxs-lookup"><span data-stu-id="d8f28-173">The `SupportedUICultures` determines which translated strings (from *.resx* files) are looked up by the [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span></span> <span data-ttu-id="d8f28-174">`ResourceManager` simplemente busca cadenas específicas de referencias culturales determinadas por `CurrentUICulture`.</span><span class="sxs-lookup"><span data-stu-id="d8f28-174">The `ResourceManager` simply looks up culture-specific strings that's determined by `CurrentUICulture`.</span></span> <span data-ttu-id="d8f28-175">Todos los subprocesos de .NET tienen objetos `CurrentCulture` y `CurrentUICulture`.</span><span class="sxs-lookup"><span data-stu-id="d8f28-175">Every thread in .NET has `CurrentCulture` and `CurrentUICulture` objects.</span></span> <span data-ttu-id="d8f28-176">ASP.NET Core inspecciona estos valores al representar funciones dependientes de la referencia cultural.</span><span class="sxs-lookup"><span data-stu-id="d8f28-176">ASP.NET Core inspects these values when rendering culture-dependent functions.</span></span> <span data-ttu-id="d8f28-177">Por ejemplo, si la referencia cultural del subproceso actual está establecida en "en-US" (inglés (Estados Unidos)), `DateTime.Now.ToLongDateString()` mostrará "Thursday, February 18, 2016". En cambio, si `CurrentCulture` está establecido en "es-ES" (español (España)), la salida será "jueves, 18 de febrero de 2016".</span><span class="sxs-lookup"><span data-stu-id="d8f28-177">For example, if the current thread's culture is set to "en-US" (English, United States), `DateTime.Now.ToLongDateString()` displays "Thursday, February 18, 2016", but if `CurrentCulture` is set to "es-ES" (Spanish, Spain) the output will be "jueves, 18 de febrero de 2016".</span></span>

## <a name="resource-files"></a><span data-ttu-id="d8f28-178">Archivos de recursos</span><span class="sxs-lookup"><span data-stu-id="d8f28-178">Resource files</span></span>

<span data-ttu-id="d8f28-179">Un archivo de recursos es un mecanismo útil para separar del código las cadenas localizables.</span><span class="sxs-lookup"><span data-stu-id="d8f28-179">A resource file is a useful mechanism for separating localizable strings from code.</span></span> <span data-ttu-id="d8f28-180">Las cadenas traducidas para el idioma no predeterminado están aisladas en archivos de recursos *.resx*.</span><span class="sxs-lookup"><span data-stu-id="d8f28-180">Translated strings for the non-default language are isolated in *.resx* resource files.</span></span> <span data-ttu-id="d8f28-181">Pongamos por caso que quiere crear un archivo de recursos de español denominado *Welcome.es.resx* que contenga cadenas traducidas.</span><span class="sxs-lookup"><span data-stu-id="d8f28-181">For example, you might want to create Spanish resource file named *Welcome.es.resx* containing translated strings.</span></span> <span data-ttu-id="d8f28-182">"es" es el código de idioma para español.</span><span class="sxs-lookup"><span data-stu-id="d8f28-182">"es" is the language code for Spanish.</span></span> <span data-ttu-id="d8f28-183">Para crear este archivo de recursos en Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="d8f28-183">To create this resource file in Visual Studio:</span></span>

1. <span data-ttu-id="d8f28-184">En el **Explorador de soluciones**, haga clic con el botón derecho en la carpeta que contendrá el archivo de recursos > **Agregar** > **Nuevo elemento**.</span><span class="sxs-lookup"><span data-stu-id="d8f28-184">In **Solution Explorer**, right click on the folder which will contain the resource file > **Add** > **New Item**.</span></span>

    ![Menú contextual anidado: en el Explorador de soluciones, se abre un menú contextual para los recursos.](localization/_static/newi.png)

2. <span data-ttu-id="d8f28-187">En el cuadro para **buscar plantillas instaladas**, escriba "recurso" y asigne un nombre al archivo.</span><span class="sxs-lookup"><span data-stu-id="d8f28-187">In the **Search installed templates** box, enter "resource" and name the file.</span></span>

    ![Cuadro de diálogo Agregar nuevo elemento](localization/_static/res.png)

3. <span data-ttu-id="d8f28-189">Escriba el valor de clave (cadena nativa) en la columna **Nombre** y la cadena traducida en la columna **Valor**.</span><span class="sxs-lookup"><span data-stu-id="d8f28-189">Enter the key value (native string) in the **Name** column and the translated string in the **Value** column.</span></span>

    ![Archivo Welcome.es.resx (archivo de recursos de bienvenida para el idioma español) con la palabra Hello en la columna Nombre y la palabra Hola en la columna Valor](localization/_static/hola.png)

    <span data-ttu-id="d8f28-191">El archivo *Welcome.es.resx* aparece en Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="d8f28-191">Visual Studio shows the *Welcome.es.resx* file.</span></span>

    ![Archivo de recursos de bienvenida en español (es) en el Explorador de soluciones](localization/_static/se.png)

## <a name="resource-file-naming"></a><span data-ttu-id="d8f28-193">Nomenclatura de los archivos de recursos</span><span class="sxs-lookup"><span data-stu-id="d8f28-193">Resource file naming</span></span>

<span data-ttu-id="d8f28-194">El nombre de los recursos es el nombre del tipo completo de su clase menos el nombre del ensamblado.</span><span class="sxs-lookup"><span data-stu-id="d8f28-194">Resources are named for the full type name of their class minus the assembly name.</span></span> <span data-ttu-id="d8f28-195">Por ejemplo, un recurso francés de un proyecto cuyo ensamblado principal sea `LocalizationWebsite.Web.dll` para la clase `LocalizationWebsite.Web.Startup` se denominará *Startup.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="d8f28-195">For example, a French resource in a project whose main assembly is `LocalizationWebsite.Web.dll` for the class `LocalizationWebsite.Web.Startup` would be named *Startup.fr.resx*.</span></span> <span data-ttu-id="d8f28-196">Un recurso para la clase `LocalizationWebsite.Web.Controllers.HomeController` se denominará *Controllers.HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="d8f28-196">A resource for the class `LocalizationWebsite.Web.Controllers.HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="d8f28-197">Si el espacio de nombres de la clase de destino no es igual que el nombre del ensamblado, necesitará el nombre de tipo completo.</span><span class="sxs-lookup"><span data-stu-id="d8f28-197">If your targeted class's namespace isn't the same as the assembly name you will need the full type name.</span></span> <span data-ttu-id="d8f28-198">Por ejemplo, en el proyecto de ejemplo, un recurso para el tipo `ExtraNamespace.Tools` se denominará *ExtraNamespace.Tools.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="d8f28-198">For example, in the sample project a resource for the type `ExtraNamespace.Tools` would be named *ExtraNamespace.Tools.fr.resx*.</span></span>

<span data-ttu-id="d8f28-199">En el proyecto de ejemplo, el método `ConfigureServices` establece `ResourcesPath` en "Resources", por lo que la ruta de acceso relativa del proyecto para el archivo de recursos en francés del controlador de inicio es *Resources/Controllers.HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="d8f28-199">In the sample project, the `ConfigureServices` method sets the `ResourcesPath` to "Resources", so the project relative path for the home controller's French resource file is *Resources/Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="d8f28-200">También puede usar carpetas para organizar los archivos de recursos.</span><span class="sxs-lookup"><span data-stu-id="d8f28-200">Alternatively, you can use folders to organize resource files.</span></span> <span data-ttu-id="d8f28-201">Para el controlador de inicio, la ruta de acceso sería *Resources/Controllers/HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="d8f28-201">For the home controller, the path would be *Resources/Controllers/HomeController.fr.resx*.</span></span> <span data-ttu-id="d8f28-202">Si no usa la opción `ResourcesPath`, el archivo *.resx* estará en el directorio base del proyecto.</span><span class="sxs-lookup"><span data-stu-id="d8f28-202">If you don't use the `ResourcesPath` option, the *.resx* file would go in the project base directory.</span></span> <span data-ttu-id="d8f28-203">El archivo de recursos para `HomeController` se denominará *Controllers.HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="d8f28-203">The resource file for `HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="d8f28-204">La opción de usar la convención de nomenclatura de punto o ruta de acceso depende de la manera en que quiera organizar los archivos de recursos.</span><span class="sxs-lookup"><span data-stu-id="d8f28-204">The choice of using the dot or path naming convention depends on how you want to organize your resource files.</span></span>

| <span data-ttu-id="d8f28-205">Nombre del recurso</span><span class="sxs-lookup"><span data-stu-id="d8f28-205">Resource name</span></span> | <span data-ttu-id="d8f28-206">Nomenclatura de punto o ruta de acceso</span><span class="sxs-lookup"><span data-stu-id="d8f28-206">Dot or path naming</span></span> |
| ------------   | ------------- |
| <span data-ttu-id="d8f28-207">Resources/Controllers.HomeController.fr.resx</span><span class="sxs-lookup"><span data-stu-id="d8f28-207">Resources/Controllers.HomeController.fr.resx</span></span> | <span data-ttu-id="d8f28-208">Punto</span><span class="sxs-lookup"><span data-stu-id="d8f28-208">Dot</span></span>  |
| <span data-ttu-id="d8f28-209">Resources/Controllers/HomeController.fr.resx</span><span class="sxs-lookup"><span data-stu-id="d8f28-209">Resources/Controllers/HomeController.fr.resx</span></span>  | <span data-ttu-id="d8f28-210">Ruta de acceso</span><span class="sxs-lookup"><span data-stu-id="d8f28-210">Path</span></span> |
|    |     |

<span data-ttu-id="d8f28-211">Los archivos de recursos que usan `@inject IViewLocalizer` en las vistas de Razor siguen un patrón similar.</span><span class="sxs-lookup"><span data-stu-id="d8f28-211">Resource files using `@inject IViewLocalizer` in Razor views follow a similar pattern.</span></span> <span data-ttu-id="d8f28-212">Para asignar un nombre al archivo de recursos de una vista, se puede usar la nomenclatura de punto o de ruta de acceso.</span><span class="sxs-lookup"><span data-stu-id="d8f28-212">The resource file for a view can be named using either dot naming or path naming.</span></span> <span data-ttu-id="d8f28-213">Los archivos de recursos de una vista de Razor imitan la ruta de acceso de su archivo de vista asociado.</span><span class="sxs-lookup"><span data-stu-id="d8f28-213">Razor view resource files mimic the path of their associated view file.</span></span> <span data-ttu-id="d8f28-214">Supongamos que establecemos `ResourcesPath` en "Resources". En ese caso, el archivo de recursos de francés asociado a la vista *Views/Home/About.cshtml* podría ser uno de los siguientes:</span><span class="sxs-lookup"><span data-stu-id="d8f28-214">Assuming we set the `ResourcesPath` to "Resources", the French resource file associated with the *Views/Home/About.cshtml* view could be either of the following:</span></span>

* <span data-ttu-id="d8f28-215">Resources/Views/Home/About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="d8f28-215">Resources/Views/Home/About.fr.resx</span></span>

* <span data-ttu-id="d8f28-216">Resources/Views.Home.About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="d8f28-216">Resources/Views.Home.About.fr.resx</span></span>

<span data-ttu-id="d8f28-217">Si no usa la opción `ResourcesPath`, el archivo *.resx* de una vista se ubicará en la misma carpeta que la vista.</span><span class="sxs-lookup"><span data-stu-id="d8f28-217">If you don't use the `ResourcesPath` option, the *.resx* file for a view would be located in the same folder as the view.</span></span>

### <a name="rootnamespaceattribute"></a><span data-ttu-id="d8f28-218">RootNamespaceAttribute</span><span class="sxs-lookup"><span data-stu-id="d8f28-218">RootNamespaceAttribute</span></span> 

<span data-ttu-id="d8f28-219">El atributo [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) proporciona el espacio de nombres raíz de un ensamblado cuando el espacio de nombres raíz del ensamblado es diferente del nombre de ensamblado.</span><span class="sxs-lookup"><span data-stu-id="d8f28-219">The [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) attribute provides the root namespace of an assembly when the root namespace of an assembly is different than the assembly name.</span></span> 

> [!WARNING]
> <span data-ttu-id="d8f28-220">Esto puede ocurrir cuando el nombre de un proyecto no es un identificador de .NET válido.</span><span class="sxs-lookup"><span data-stu-id="d8f28-220">This can occur when a project's name is not a valid .NET identifier.</span></span> <span data-ttu-id="d8f28-221">Por ejemplo `my-project-name.csproj` usará el espacio de nombres raíz `my_project_name` y el nombre de ensamblado `my-project-name`, lo que lleva a este error.</span><span class="sxs-lookup"><span data-stu-id="d8f28-221">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span> 

<span data-ttu-id="d8f28-222">Si el espacio de nombres raíz de un ensamblado es diferente del nombre de ensamblado:</span><span class="sxs-lookup"><span data-stu-id="d8f28-222">If the root namespace of an assembly is different than the assembly name:</span></span>

* <span data-ttu-id="d8f28-223">La localización no funciona de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="d8f28-223">Localization does not work by default.</span></span>
* <span data-ttu-id="d8f28-224">Se produce un error en la localización debido a la manera en que se buscan los recursos dentro del ensamblado.</span><span class="sxs-lookup"><span data-stu-id="d8f28-224">Localization fails due to the way resources are searched for within the assembly.</span></span> <span data-ttu-id="d8f28-225">`RootNamespace` es un valor en tiempo de compilación que no está disponible para el proceso en ejecución.</span><span class="sxs-lookup"><span data-stu-id="d8f28-225">`RootNamespace` is a build-time value which is not available to the executing process.</span></span> 

<span data-ttu-id="d8f28-226">Si `RootNamespace` es diferente de `AssemblyName`, incluya lo siguiente en *AssemblyInfo.cs* (con los valores de parámetro reemplazados por los valores reales):</span><span class="sxs-lookup"><span data-stu-id="d8f28-226">If the `RootNamespace` is different from the `AssemblyName`, include the following in *AssemblyInfo.cs* (with parameter values replaced with the actual values):</span></span>

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

<span data-ttu-id="d8f28-227">El código anterior permite la resolución correcta de los archivos resx.</span><span class="sxs-lookup"><span data-stu-id="d8f28-227">The preceding code enables the successful resolution of resx files.</span></span>

## <a name="culture-fallback-behavior"></a><span data-ttu-id="d8f28-228">Comportamiento de reserva de la referencia cultural</span><span class="sxs-lookup"><span data-stu-id="d8f28-228">Culture fallback behavior</span></span>

<span data-ttu-id="d8f28-229">Cuando se busca un recurso, la localización entra en un proceso conocido como "reserva de la referencia cultural".</span><span class="sxs-lookup"><span data-stu-id="d8f28-229">When searching for a resource, localization engages in "culture fallback".</span></span> <span data-ttu-id="d8f28-230">Partiendo de la referencia cultural solicitada, si esta no se encuentra, se revierte a su referencia cultural principal correspondiente.</span><span class="sxs-lookup"><span data-stu-id="d8f28-230">Starting from the requested culture, if not found, it reverts to the parent culture of that culture.</span></span> <span data-ttu-id="d8f28-231">Como inciso, decir que la propiedad [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) representa la referencia cultural principal.</span><span class="sxs-lookup"><span data-stu-id="d8f28-231">As an aside, the [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) property represents the parent culture.</span></span> <span data-ttu-id="d8f28-232">Eso suele conllevar (aunque no siempre) la eliminación del significante nacional del código ISO.</span><span class="sxs-lookup"><span data-stu-id="d8f28-232">This usually (but not always) means removing the national signifier from the ISO.</span></span> <span data-ttu-id="d8f28-233">Así, por ejemplo, el dialecto de español hablado en México es "es-MX".</span><span class="sxs-lookup"><span data-stu-id="d8f28-233">For example, the dialect of Spanish spoken in Mexico is "es-MX".</span></span> <span data-ttu-id="d8f28-234">Contiene el elemento principal "es", que corresponde a español no específico de ningún país en concreto.</span><span class="sxs-lookup"><span data-stu-id="d8f28-234">It has the parent "es"&mdash;Spanish non-specific to any country.</span></span>

<span data-ttu-id="d8f28-235">Imagine que su sitio recibe una solicitud sobre un recurso "Welcome" donde se emplea la referencia cultural "fr-CA".</span><span class="sxs-lookup"><span data-stu-id="d8f28-235">Imagine your site receives a request for a "Welcome" resource using culture "fr-CA".</span></span> <span data-ttu-id="d8f28-236">El sistema de localización busca los recursos siguientes (en orden) y selecciona la primera coincidencia:</span><span class="sxs-lookup"><span data-stu-id="d8f28-236">The localization system looks for the following resources, in order, and selects the first match:</span></span>

* <span data-ttu-id="d8f28-237">*Welcome.fr-CA.resx*</span><span class="sxs-lookup"><span data-stu-id="d8f28-237">*Welcome.fr-CA.resx*</span></span>
* <span data-ttu-id="d8f28-238">*Welcome.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="d8f28-238">*Welcome.fr.resx*</span></span>
* <span data-ttu-id="d8f28-239">*Welcome.resx* (si `NeutralResourcesLanguage` es "fr-CA")</span><span class="sxs-lookup"><span data-stu-id="d8f28-239">*Welcome.resx* (if the `NeutralResourcesLanguage` is "fr-CA")</span></span>

<span data-ttu-id="d8f28-240">Consideremos, por ejemplo, que quita el designador de referencia cultural ".fr" y la referencia cultural está establecida en francés. En ese caso, se lee el archivo de recursos predeterminado y se localizan las cadenas.</span><span class="sxs-lookup"><span data-stu-id="d8f28-240">As an example, if you remove the ".fr" culture designator and you have the culture set to French, the default resource file is read and strings are localized.</span></span> <span data-ttu-id="d8f28-241">El Administrador de recursos designa un recurso predeterminado o de reserva para los casos en que nada coincida con la referencia cultural solicitada.</span><span class="sxs-lookup"><span data-stu-id="d8f28-241">The Resource manager designates a default or fallback resource for when nothing meets your requested culture.</span></span> <span data-ttu-id="d8f28-242">Si quiere simplemente devolver la clave cuando falte un recurso para la referencia cultural solicitada, no debe tener un archivo de recursos predeterminado.</span><span class="sxs-lookup"><span data-stu-id="d8f28-242">If you want to just return the key when missing a resource for the requested culture you must not have a default resource file.</span></span>

### <a name="generate-resource-files-with-visual-studio"></a><span data-ttu-id="d8f28-243">Generar archivos de recursos con Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d8f28-243">Generate resource files with Visual Studio</span></span>

<span data-ttu-id="d8f28-244">Si crea un archivo de recursos en Visual Studio sin una referencia cultural en el nombre de archivo (por ejemplo, *Welcome.resx*), Visual Studio creará una clase de C# con una propiedad para cada cadena.</span><span class="sxs-lookup"><span data-stu-id="d8f28-244">If you create a resource file in Visual Studio without a culture in the file name (for example, *Welcome.resx*), Visual Studio will create a C# class with a property for each string.</span></span> <span data-ttu-id="d8f28-245">Normalmente esto no interesa con ASP.NET Core;</span><span class="sxs-lookup"><span data-stu-id="d8f28-245">That's usually not what you want with ASP.NET Core.</span></span> <span data-ttu-id="d8f28-246">por lo general, no tendrá un archivo de recursos *.resx* predeterminado (un archivo *.resx* sin el nombre de la referencia cultural).</span><span class="sxs-lookup"><span data-stu-id="d8f28-246">You typically don't have a default *.resx* resource file (a *.resx* file without the culture name).</span></span> <span data-ttu-id="d8f28-247">Se recomienda que cree el archivo *.resx* con un nombre de referencia cultural (por ejemplo, *Welcome.fr.resx*).</span><span class="sxs-lookup"><span data-stu-id="d8f28-247">We suggest you create the *.resx* file with a culture name (for example *Welcome.fr.resx*).</span></span> <span data-ttu-id="d8f28-248">Cuando cree un archivo *.resx* con un nombre de referencia cultural, Visual Studio no generará el archivo de clase.</span><span class="sxs-lookup"><span data-stu-id="d8f28-248">When you create a *.resx* file with a culture name, Visual Studio won't generate the class file.</span></span>

### <a name="add-other-cultures"></a><span data-ttu-id="d8f28-249">Agregar otras referencias culturales</span><span class="sxs-lookup"><span data-stu-id="d8f28-249">Add other cultures</span></span>

<span data-ttu-id="d8f28-250">Cada combinación de idioma y referencia cultural (que no sea el idioma predeterminado) requiere un archivo de recursos único.</span><span class="sxs-lookup"><span data-stu-id="d8f28-250">Each language and culture combination (other than the default language) requires a unique resource file.</span></span> <span data-ttu-id="d8f28-251">Para crear archivos de recursos para otras referencias culturales y configuraciones regionales, debe crear archivos de recursos en los que los códigos de idioma ISO formen parte del nombre de archivo (por ejemplo, **en-us**, **fr-ca** y  **en-gb**).</span><span class="sxs-lookup"><span data-stu-id="d8f28-251">You create resource files for different cultures and locales by creating new resource files in which the ISO language codes are part of the file name (for example, **en-us**, **fr-ca**, and **en-gb**).</span></span> <span data-ttu-id="d8f28-252">Estos códigos ISO se colocan entre el nombre de archivo y la extensión de archivo *.resx*, como en *Welcome.es-MX.resx* (español [México]).</span><span class="sxs-lookup"><span data-stu-id="d8f28-252">These ISO codes are placed between the file name and the *.resx* file extension, as in *Welcome.es-MX.resx* (Spanish/Mexico).</span></span>

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a><span data-ttu-id="d8f28-253">Implementar una estrategia para seleccionar el idioma o la referencia cultural de cada solicitud</span><span class="sxs-lookup"><span data-stu-id="d8f28-253">Implement a strategy to select the language/culture for each request</span></span>

### <a name="configure-localization"></a><span data-ttu-id="d8f28-254">Configurar la localización</span><span class="sxs-lookup"><span data-stu-id="d8f28-254">Configure localization</span></span>

<span data-ttu-id="d8f28-255">La localización se configura en el método `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="d8f28-255">Localization is configured in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet1)]

* <span data-ttu-id="d8f28-256">`AddLocalization` agrega los servicios de localización al contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="d8f28-256">`AddLocalization` Adds the localization services to the services container.</span></span> <span data-ttu-id="d8f28-257">El código anterior también establece la ruta de acceso a los recursos en "Resources".</span><span class="sxs-lookup"><span data-stu-id="d8f28-257">The code above also sets the resources path to "Resources".</span></span>

* <span data-ttu-id="d8f28-258">`AddViewLocalization` agrega compatibilidad con los archivos de vista localizados.</span><span class="sxs-lookup"><span data-stu-id="d8f28-258">`AddViewLocalization` Adds support for localized view files.</span></span> <span data-ttu-id="d8f28-259">En este ejemplo, la localización de vista se basa en el sufijo del archivo de vista.</span><span class="sxs-lookup"><span data-stu-id="d8f28-259">In this sample view localization is based on the view file suffix.</span></span> <span data-ttu-id="d8f28-260">Por ejemplo, "fr" en el archivo *Index.fr.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="d8f28-260">For example "fr" in the *Index.fr.cshtml* file.</span></span>

* <span data-ttu-id="d8f28-261">`AddDataAnnotationsLocalization` agrega compatibilidad con mensajes de validación de `DataAnnotations` localizados mediante abstracciones `IStringLocalizer`.</span><span class="sxs-lookup"><span data-stu-id="d8f28-261">`AddDataAnnotationsLocalization` Adds support for localized `DataAnnotations` validation messages through `IStringLocalizer` abstractions.</span></span>

### <a name="localization-middleware"></a><span data-ttu-id="d8f28-262">Software intermedio de localización</span><span class="sxs-lookup"><span data-stu-id="d8f28-262">Localization middleware</span></span>

<span data-ttu-id="d8f28-263">La referencia cultural actual de una solicitud se establece en el [software intermedio](xref:fundamentals/middleware/index) de localización.</span><span class="sxs-lookup"><span data-stu-id="d8f28-263">The current culture on a request is set in the localization [Middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="d8f28-264">El software intermedio de localización se habilita en el método `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="d8f28-264">The localization middleware is enabled in the `Startup.Configure` method.</span></span> <span data-ttu-id="d8f28-265">El software intermedio de localización debe configurarse antes que cualquier software intermedio que pueda comprobar la referencia cultural de la solicitud (por ejemplo, `app.UseMvcWithDefaultRoute()`).</span><span class="sxs-lookup"><span data-stu-id="d8f28-265">The localization middleware must be configured before any middleware which might check the request culture (for example, `app.UseMvcWithDefaultRoute()`).</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet2)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="d8f28-266">`UseRequestLocalization` inicializa un objeto `RequestLocalizationOptions`.</span><span class="sxs-lookup"><span data-stu-id="d8f28-266">`UseRequestLocalization` initializes a `RequestLocalizationOptions` object.</span></span> <span data-ttu-id="d8f28-267">En todas las solicitudes, se enumera la lista de `RequestCultureProvider` en `RequestLocalizationOptions` y se usa el primer proveedor que puede determinar correctamente la referencia cultural de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="d8f28-267">On every request the list of `RequestCultureProvider` in the `RequestLocalizationOptions` is enumerated and the first provider that can successfully determine the request culture is used.</span></span> <span data-ttu-id="d8f28-268">Los proveedores predeterminados proceden de la clase `RequestLocalizationOptions`:</span><span class="sxs-lookup"><span data-stu-id="d8f28-268">The default providers come from the `RequestLocalizationOptions` class:</span></span>

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="d8f28-269">La lista predeterminada va de más específico a menos específico.</span><span class="sxs-lookup"><span data-stu-id="d8f28-269">The default list goes from most specific to least specific.</span></span> <span data-ttu-id="d8f28-270">Más adelante en el artículo veremos cómo puede cambiar el orden e incluso agregar un proveedor de referencia cultural personalizado.</span><span class="sxs-lookup"><span data-stu-id="d8f28-270">Later in the article we'll see how you can change the order and even add a custom culture provider.</span></span> <span data-ttu-id="d8f28-271">Si ninguno de los proveedores puede determinar la referencia cultural de la solicitud, se usa `DefaultRequestCulture`.</span><span class="sxs-lookup"><span data-stu-id="d8f28-271">If none of the providers can determine the request culture, the `DefaultRequestCulture` is used.</span></span>

### <a name="querystringrequestcultureprovider"></a><span data-ttu-id="d8f28-272">QueryStringRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="d8f28-272">QueryStringRequestCultureProvider</span></span>

<span data-ttu-id="d8f28-273">Algunas aplicaciones usarán una cadena de consulta para establecer [la referencia cultural y la referencia cultural de la interfaz de usuario](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span><span class="sxs-lookup"><span data-stu-id="d8f28-273">Some apps will use a query string to set the [culture and UI culture](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span></span> <span data-ttu-id="d8f28-274">Para las aplicaciones que usan el método de cookie o de encabezado Accept-Language, es útil agregar una cadena de consulta a la dirección URL para depurar y probar el código.</span><span class="sxs-lookup"><span data-stu-id="d8f28-274">For apps that use the cookie or Accept-Language header approach, adding a query string to the URL is useful for debugging and testing code.</span></span> <span data-ttu-id="d8f28-275">De forma predeterminada, `QueryStringRequestCultureProvider` está registrado como primer proveedor de localización en la lista `RequestCultureProvider`.</span><span class="sxs-lookup"><span data-stu-id="d8f28-275">By default, the `QueryStringRequestCultureProvider` is registered as the first localization provider in the `RequestCultureProvider` list.</span></span> <span data-ttu-id="d8f28-276">Debe pasar los parámetros de cadena de consulta `culture` y `ui-culture`.</span><span class="sxs-lookup"><span data-stu-id="d8f28-276">You pass the query string parameters `culture` and `ui-culture`.</span></span> <span data-ttu-id="d8f28-277">En el ejemplo siguiente, la referencia cultural específica (idioma y región) se establece en español (México):</span><span class="sxs-lookup"><span data-stu-id="d8f28-277">The following example sets the specific culture (language and region) to Spanish/Mexico:</span></span>

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

<span data-ttu-id="d8f28-278">Si solo pasa uno de los dos parámetros (`culture` o `ui-culture`), el proveedor de la cadena de consulta usará el valor que usted ha pasado para establecer ambos valores.</span><span class="sxs-lookup"><span data-stu-id="d8f28-278">If you only pass in one of the two (`culture` or `ui-culture`), the query string provider will set both values using the one you passed in.</span></span> <span data-ttu-id="d8f28-279">Por ejemplo, si solo establece la referencia cultural, se establecerán `Culture` y `UICulture`:</span><span class="sxs-lookup"><span data-stu-id="d8f28-279">For example, setting just the culture will set both the `Culture` and the `UICulture`:</span></span>

   `http://localhost:5000/?culture=es-MX`

### <a name="cookierequestcultureprovider"></a><span data-ttu-id="d8f28-280">CookieRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="d8f28-280">CookieRequestCultureProvider</span></span>

<span data-ttu-id="d8f28-281">Las aplicaciones de producción suelen proporcionar un mecanismo para establecer la referencia cultural con la cookie de la referencia cultural de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d8f28-281">Production apps will often provide a mechanism to set the culture with the ASP.NET Core culture cookie.</span></span> <span data-ttu-id="d8f28-282">Use el método `MakeCookieValue` para crear una cookie.</span><span class="sxs-lookup"><span data-stu-id="d8f28-282">Use the `MakeCookieValue` method to create a cookie.</span></span>

<span data-ttu-id="d8f28-283">`CookieRequestCultureProvider` `DefaultCookieName` devuelve el nombre de cookie predeterminado usado para realizar un seguimiento de la información de la referencia cultural preferida del usuario.</span><span class="sxs-lookup"><span data-stu-id="d8f28-283">The `CookieRequestCultureProvider` `DefaultCookieName` returns the default cookie name used to track the user's preferred culture information.</span></span> <span data-ttu-id="d8f28-284">El nombre de cookie predeterminado es `.AspNetCore.Culture`.</span><span class="sxs-lookup"><span data-stu-id="d8f28-284">The default cookie name is `.AspNetCore.Culture`.</span></span>

<span data-ttu-id="d8f28-285">El formato de la cookie es `c=%LANGCODE%|uic=%LANGCODE%`, donde `c` es `Culture` y `uic` es `UICulture`, por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="d8f28-285">The cookie format is `c=%LANGCODE%|uic=%LANGCODE%`, where `c` is `Culture` and `uic` is `UICulture`, for example:</span></span>

    c=en-UK|uic=en-US

<span data-ttu-id="d8f28-286">Si solo especifica uno de los dos valores, ya sea la información de la referencia cultural o la referencia cultural de la interfaz de usuario, la referencia cultural especificada se usará tanto para la información de la referencia cultural como para la referencia cultural de la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="d8f28-286">If you only specify one of culture info and UI culture, the specified culture will be used for both culture info and UI culture.</span></span>

### <a name="the-accept-language-http-header"></a><span data-ttu-id="d8f28-287">Encabezado HTTP Accept-Language</span><span class="sxs-lookup"><span data-stu-id="d8f28-287">The Accept-Language HTTP header</span></span>

<span data-ttu-id="d8f28-288">El [encabezado Accept-Language](https://www.w3.org/International/questions/qa-accept-lang-locales) se puede establecer en la mayoría de los exploradores y está diseñado inicialmente para especificar el idioma del usuario.</span><span class="sxs-lookup"><span data-stu-id="d8f28-288">The [Accept-Language header](https://www.w3.org/International/questions/qa-accept-lang-locales) is settable in most browsers and was originally intended to specify the user's language.</span></span> <span data-ttu-id="d8f28-289">Este valor indica qué debe enviar el explorador o qué ha heredado del sistema operativo subyacente.</span><span class="sxs-lookup"><span data-stu-id="d8f28-289">This setting indicates what the browser has been set to send or has inherited from the underlying operating system.</span></span> <span data-ttu-id="d8f28-290">El encabezado HTTP Accept-Language de una solicitud del explorador no es un método infalible para detectar el idioma preferido del usuario (vea [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php) [Establecer las preferencias de idioma en un explorador]).</span><span class="sxs-lookup"><span data-stu-id="d8f28-290">The Accept-Language HTTP header from a browser request isn't an infallible way to detect the user's preferred language (see [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span></span> <span data-ttu-id="d8f28-291">Una aplicación de producción debe ofrecer al usuario una manera de personalizar su opción de referencia cultural.</span><span class="sxs-lookup"><span data-stu-id="d8f28-291">A production app should include a way for a user to customize their choice of culture.</span></span>

### <a name="set-the-accept-language-http-header-in-ie"></a><span data-ttu-id="d8f28-292">Establecer el encabezado HTTP Accept-Language en Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="d8f28-292">Set the Accept-Language HTTP header in IE</span></span>

1. <span data-ttu-id="d8f28-293">En el icono de engranaje, pulse **Opciones de Internet**.</span><span class="sxs-lookup"><span data-stu-id="d8f28-293">From the gear icon, tap **Internet Options**.</span></span>

2. <span data-ttu-id="d8f28-294">Haga clic en **Lenguajes**.</span><span class="sxs-lookup"><span data-stu-id="d8f28-294">Tap **Languages**.</span></span>

    ![Opciones de Internet](localization/_static/lang.png)

3. <span data-ttu-id="d8f28-296">Haga clic en **Establecer preferencias de idioma**.</span><span class="sxs-lookup"><span data-stu-id="d8f28-296">Tap **Set Language Preferences**.</span></span>

4. <span data-ttu-id="d8f28-297">Haga clic en **Agregar un idioma**.</span><span class="sxs-lookup"><span data-stu-id="d8f28-297">Tap **Add a language**.</span></span>

5. <span data-ttu-id="d8f28-298">Agregue el idioma.</span><span class="sxs-lookup"><span data-stu-id="d8f28-298">Add the language.</span></span>

6. <span data-ttu-id="d8f28-299">Haga clic en el idioma y, después, en **Subir**.</span><span class="sxs-lookup"><span data-stu-id="d8f28-299">Tap the language, then tap **Move Up**.</span></span>

::: moniker range="> aspnetcore-3.1"
### <a name="the-content-language-http-header"></a><span data-ttu-id="d8f28-300">El encabezado HTTP Content-Language</span><span class="sxs-lookup"><span data-stu-id="d8f28-300">The Content-Language HTTP header</span></span>

<span data-ttu-id="d8f28-301">El encabezado de entidad [Content-Language](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Language):</span><span class="sxs-lookup"><span data-stu-id="d8f28-301">The [Content-Language](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Language) entity header:</span></span>

 - <span data-ttu-id="d8f28-302">Se usa para describir los lenguajes destinados a la audiencia.</span><span class="sxs-lookup"><span data-stu-id="d8f28-302">Is used to describe the language(s) intended for the audience.</span></span>
 - <span data-ttu-id="d8f28-303">Permite a un usuario diferenciar según el propio idioma preferido de los usuarios.</span><span class="sxs-lookup"><span data-stu-id="d8f28-303">Allows a user to differentiate according to the users' own preferred language.</span></span>

<span data-ttu-id="d8f28-304">Los encabezados de entidad se usan en las solicitudes y respuestas HTTP.</span><span class="sxs-lookup"><span data-stu-id="d8f28-304">Entity headers are used in both HTTP requests and responses.</span></span>

<span data-ttu-id="d8f28-305">Se puede agregar el encabezado `Content-Language` mediante el establecimiento de la propiedad `ApplyCurrentCultureToResponseHeaders`.</span><span class="sxs-lookup"><span data-stu-id="d8f28-305">The `Content-Language` header can be added by setting the property `ApplyCurrentCultureToResponseHeaders`.</span></span>

<span data-ttu-id="d8f28-306">La adición del encabezado `Content-Language`:</span><span class="sxs-lookup"><span data-stu-id="d8f28-306">Adding the `Content-Language` header:</span></span>

 - <span data-ttu-id="d8f28-307">Permite que RequestLocalizationMiddleware establezca el encabezado `Content-Language` con `CurrentUICulture`.</span><span class="sxs-lookup"><span data-stu-id="d8f28-307">Allows the RequestLocalizationMiddleware to set the `Content-Language` header with the `CurrentUICulture`.</span></span>
 - <span data-ttu-id="d8f28-308">Elimina la necesidad de establecer el encabezado de respuesta `Content-Language` explícitamente.</span><span class="sxs-lookup"><span data-stu-id="d8f28-308">Eliminates the need to set the response header `Content-Language` explicitly.</span></span>

```csharp
app.UseRequestLocalization(new RequestLocalizationOptions
{
    ApplyCurrentCultureToResponseHeaders = true
});
```
::: moniker-end

### <a name="use-a-custom-provider"></a><span data-ttu-id="d8f28-309">Usar un proveedor personalizado</span><span class="sxs-lookup"><span data-stu-id="d8f28-309">Use a custom provider</span></span>

<span data-ttu-id="d8f28-310">Imagine que quiere permitir que los clientes almacenen su idioma y su referencia cultural en las bases de datos.</span><span class="sxs-lookup"><span data-stu-id="d8f28-310">Suppose you want to let your customers store their language and culture in your databases.</span></span> <span data-ttu-id="d8f28-311">Puede escribir un proveedor para que busque estos valores para el usuario.</span><span class="sxs-lookup"><span data-stu-id="d8f28-311">You could write a provider to look up these values for the user.</span></span> <span data-ttu-id="d8f28-312">En el código siguiente se muestra cómo agregar un proveedor personalizado:</span><span class="sxs-lookup"><span data-stu-id="d8f28-312">The following code shows how to add a custom provider:</span></span>

::: moniker range="< aspnetcore-3.0"
```csharp
private const string enUSCulture = "en-US";

services.Configure<RequestLocalizationOptions>(options =>
{
    var supportedCultures = new[]
    {
        new CultureInfo(enUSCulture),
        new CultureInfo("fr")
    };

    options.DefaultRequestCulture = new RequestCulture(culture: enUSCulture, uiCulture: enUSCulture);
    options.SupportedCultures = supportedCultures;
    options.SupportedUICultures = supportedCultures;

    options.RequestCultureProviders.Insert(0, new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
});
```
::: moniker-end

::: moniker range=">= aspnetcore-3.0"
```csharp
private const string enUSCulture = "en-US";

services.Configure<RequestLocalizationOptions>(options =>
{
    var supportedCultures = new[]
    {
        new CultureInfo(enUSCulture),
        new CultureInfo("fr")
    };

    options.DefaultRequestCulture = new RequestCulture(culture: enUSCulture, uiCulture: enUSCulture);
    options.SupportedCultures = supportedCultures;
    options.SupportedUICultures = supportedCultures;

    options.AddInitialRequestCultureProvider(new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
});
```
::: moniker-end

<span data-ttu-id="d8f28-313">Use `RequestLocalizationOptions` para agregar o quitar proveedores de localización.</span><span class="sxs-lookup"><span data-stu-id="d8f28-313">Use `RequestLocalizationOptions` to add or remove localization providers.</span></span>

### <a name="set-the-culture-programmatically"></a><span data-ttu-id="d8f28-314">Establecer la referencia cultural mediante programación</span><span class="sxs-lookup"><span data-stu-id="d8f28-314">Set the culture programmatically</span></span>

<span data-ttu-id="d8f28-315">Este proyecto **Localization.StarterWeb** de ejemplo de [GitHub](https://github.com/aspnet/entropy) contiene una interfaz de usuario para establecer el valor `Culture`.</span><span class="sxs-lookup"><span data-stu-id="d8f28-315">This sample **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) contains UI to set the `Culture`.</span></span> <span data-ttu-id="d8f28-316">El archivo *Views/Shared/_SelectLanguagePartial.cshtml* le permite seleccionar la referencia cultural de la lista de referencias culturales admitidas:</span><span class="sxs-lookup"><span data-stu-id="d8f28-316">The *Views/Shared/_SelectLanguagePartial.cshtml* file allows you to select the culture from the list of supported cultures:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

<span data-ttu-id="d8f28-317">El archivo *Views/Shared/_SelectLanguagePartial.cshtml* se agrega a la sección `footer` del archivo de diseño para que esté disponible para todas las vistas:</span><span class="sxs-lookup"><span data-stu-id="d8f28-317">The *Views/Shared/_SelectLanguagePartial.cshtml* file is added to the `footer` section of the layout file so it will be available to all views:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

<span data-ttu-id="d8f28-318">El método `SetLanguage` establece la cookie de la referencia cultural.</span><span class="sxs-lookup"><span data-stu-id="d8f28-318">The `SetLanguage` method sets the culture cookie.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/HomeController.cs?range=57-67)]

<span data-ttu-id="d8f28-319">No se puede conectar el archivo *_SelectLanguagePartial.cshtml* con código de ejemplo para este proyecto.</span><span class="sxs-lookup"><span data-stu-id="d8f28-319">You can't plug in the *_SelectLanguagePartial.cshtml* to sample code for this project.</span></span> <span data-ttu-id="d8f28-320">El proyecto **Localization.StarterWeb** de [GitHub](https://github.com/aspnet/entropy) tiene código para enviar `RequestLocalizationOptions` a una vista parcial de Razor a través del contenedor de [inserción de dependencias](dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="d8f28-320">The **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) has code to flow the `RequestLocalizationOptions` to a Razor partial through the [Dependency Injection](dependency-injection.md) container.</span></span>

## <a name="model-binding-route-data-and-query-strings"></a><span data-ttu-id="d8f28-321">Datos de ruta y cadenas de consulta de enlace de modelos</span><span class="sxs-lookup"><span data-stu-id="d8f28-321">Model binding route data and query strings</span></span>

<span data-ttu-id="d8f28-322">Vea [Comportamiento de globalización de los datos de ruta de enlace de modelos y las cadenas de consulta](xref:mvc/models/model-binding#glob).</span><span class="sxs-lookup"><span data-stu-id="d8f28-322">See [Globalization behavior of model binding route data and query strings](xref:mvc/models/model-binding#glob).</span></span>

## <a name="globalization-and-localization-terms"></a><span data-ttu-id="d8f28-323">Términos relacionados con la globalización y la localización</span><span class="sxs-lookup"><span data-stu-id="d8f28-323">Globalization and localization terms</span></span>

<span data-ttu-id="d8f28-324">Para localizar una aplicación también es necesario contar con unos conocimientos básicos sobre los juegos de caracteres pertinentes que se usan en el desarrollo de software moderno y sobre los problemas asociados.</span><span class="sxs-lookup"><span data-stu-id="d8f28-324">The process of localizing your app also requires a basic understanding of relevant character sets commonly used in modern software development and an understanding of the issues associated with them.</span></span> <span data-ttu-id="d8f28-325">Aunque todos los equipos almacenan texto como números (códigos), cada sistema almacena el mismo texto con números diferentes.</span><span class="sxs-lookup"><span data-stu-id="d8f28-325">Although all computers store text as numbers (codes), different systems store the same text using different numbers.</span></span> <span data-ttu-id="d8f28-326">El proceso de localización consiste en traducir la interfaz de usuario (IU) de la aplicación a una referencia cultural o configuración regional específica.</span><span class="sxs-lookup"><span data-stu-id="d8f28-326">The localization process refers to translating the app user interface (UI) for a specific culture/locale.</span></span>

<span data-ttu-id="d8f28-327">La [localizabilidad](/dotnet/standard/globalization-localization/localizability-review) es un proceso intermedio para comprobar que una aplicación globalizada está preparada para la localización.</span><span class="sxs-lookup"><span data-stu-id="d8f28-327">[Localizability](/dotnet/standard/globalization-localization/localizability-review) is an intermediate process for verifying that a globalized app is ready for localization.</span></span>

<span data-ttu-id="d8f28-328">El formato [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) para el nombre de la referencia cultural es `<languagecode2>-<country/regioncode2>`, donde `<languagecode2>` es el código de idioma y `<country/regioncode2>` es el código de la referencia cultural secundaria.</span><span class="sxs-lookup"><span data-stu-id="d8f28-328">The [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) format for the culture name is `<languagecode2>-<country/regioncode2>`, where `<languagecode2>` is the language code and `<country/regioncode2>` is the subculture code.</span></span> <span data-ttu-id="d8f28-329">Por ejemplo, `es-CL` para español (Chile), `en-US` para inglés (Estados Unidos) y `en-AU` para inglés (Australia).</span><span class="sxs-lookup"><span data-stu-id="d8f28-329">For example, `es-CL` for Spanish (Chile), `en-US` for English (United States), and `en-AU` for English (Australia).</span></span> <span data-ttu-id="d8f28-330">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) es una combinación de un código de referencia cultural ISO 639 de dos letras en minúsculas asociado con un idioma y un código de referencia cultural secundaria ISO 3166 de dos letras en mayúsculas asociado con un país o región.</span><span class="sxs-lookup"><span data-stu-id="d8f28-330">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) is a combination of an ISO 639 two-letter lowercase culture code associated with a language and an ISO 3166 two-letter uppercase subculture code associated with a country or region.</span></span> <span data-ttu-id="d8f28-331">Vea [Language Culture Name](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx) (Nombre de la referencia cultural del idioma).</span><span class="sxs-lookup"><span data-stu-id="d8f28-331">See [Language Culture Name](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span></span>

<span data-ttu-id="d8f28-332">"Internacionalización" suele abreviarse como "I18N".</span><span class="sxs-lookup"><span data-stu-id="d8f28-332">Internationalization is often abbreviated to "I18N".</span></span> <span data-ttu-id="d8f28-333">La abreviatura toma la primera y la última letra de la palabra, y el número de letras que hay entre ellas, es decir, el 18 representa el número de letras que hay entre la "I" inicial y la "N" final.</span><span class="sxs-lookup"><span data-stu-id="d8f28-333">The abbreviation takes the first and last letters and the number of letters between them, so 18 stands for the number of letters between the first "I" and the last "N".</span></span> <span data-ttu-id="d8f28-334">Lo mismo se puede decir de "globalización" (G11N) y "localización" (L10N).</span><span class="sxs-lookup"><span data-stu-id="d8f28-334">The same applies to Globalization (G11N), and Localization (L10N).</span></span>

<span data-ttu-id="d8f28-335">Términos:</span><span class="sxs-lookup"><span data-stu-id="d8f28-335">Terms:</span></span>

* <span data-ttu-id="d8f28-336">Globalización (G11N): es el proceso de hacer que una aplicación admita diferentes idiomas y regiones.</span><span class="sxs-lookup"><span data-stu-id="d8f28-336">Globalization (G11N): The process of making an app support different languages and regions.</span></span>
* <span data-ttu-id="d8f28-337">Localización (L10N): es el proceso de personalizar una aplicación para un idioma y región determinados.</span><span class="sxs-lookup"><span data-stu-id="d8f28-337">Localization (L10N): The process of customizing an app for a given language and region.</span></span>
* <span data-ttu-id="d8f28-338">Internacionalización (I18N): hace referencia a la globalización y la localización.</span><span class="sxs-lookup"><span data-stu-id="d8f28-338">Internationalization (I18N): Describes both globalization and localization.</span></span>
* <span data-ttu-id="d8f28-339">Referencia cultural: es un idioma y, opcionalmente, una región.</span><span class="sxs-lookup"><span data-stu-id="d8f28-339">Culture: It's a language and, optionally, a region.</span></span>
* <span data-ttu-id="d8f28-340">Referencia cultural neutra: se trata de una referencia cultural que tiene un idioma especificado, pero no una región.</span><span class="sxs-lookup"><span data-stu-id="d8f28-340">Neutral culture: A culture that has a specified language, but not a region.</span></span> <span data-ttu-id="d8f28-341">(por ejemplo, "en" y "es").</span><span class="sxs-lookup"><span data-stu-id="d8f28-341">(for example "en", "es")</span></span>
* <span data-ttu-id="d8f28-342">Referencia cultural específica: es una referencia cultural que tiene un idioma y una región especificados</span><span class="sxs-lookup"><span data-stu-id="d8f28-342">Specific culture: A culture that has a specified language and region.</span></span> <span data-ttu-id="d8f28-343">(por ejemplo, "en-US", "en-GB" y "es-CL").</span><span class="sxs-lookup"><span data-stu-id="d8f28-343">(for example "en-US", "en-GB", "es-CL")</span></span>
* <span data-ttu-id="d8f28-344">Referencia cultural principal: se trata de la referencia cultural neutra que contiene una referencia cultural específica.</span><span class="sxs-lookup"><span data-stu-id="d8f28-344">Parent culture: The neutral culture that contains a specific culture.</span></span> <span data-ttu-id="d8f28-345">(por ejemplo, "en" es la referencia cultural principal de "en-US" y "en-GB").</span><span class="sxs-lookup"><span data-stu-id="d8f28-345">(for example, "en" is the parent culture of "en-US" and "en-GB")</span></span>
* <span data-ttu-id="d8f28-346">Configuración regional: la configuración regional es lo mismo que la referencia cultural.</span><span class="sxs-lookup"><span data-stu-id="d8f28-346">Locale: A locale is the same as a culture.</span></span>

[!INCLUDE[](~/includes/localization/currency.md)]

::: moniker range=">= aspnetcore-3.0"
[!INCLUDE[](~/includes/localization/unsupported-culture-log-level.md)]
::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="d8f28-347">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="d8f28-347">Additional resources</span></span>

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* <span data-ttu-id="d8f28-348">[Proyecto Localization.StarterWeb](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) usado en el artículo</span><span class="sxs-lookup"><span data-stu-id="d8f28-348">[Localization.StarterWeb project](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) used in the article.</span></span>
* [<span data-ttu-id="d8f28-349">Globalización y localización de aplicaciones .NET</span><span class="sxs-lookup"><span data-stu-id="d8f28-349">Globalizing and localizing .NET applications</span></span>](/dotnet/standard/globalization-localization/index)
* [<span data-ttu-id="d8f28-350">Recursos en archivos .resx</span><span class="sxs-lookup"><span data-stu-id="d8f28-350">Resources in .resx Files</span></span>](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [<span data-ttu-id="d8f28-351">Kit de herramientas de aplicaciones multilingüe de Microsoft</span><span class="sxs-lookup"><span data-stu-id="d8f28-351">Microsoft Multilingual App Toolkit</span></span>](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [<span data-ttu-id="d8f28-352">Localización y genéricos</span><span class="sxs-lookup"><span data-stu-id="d8f28-352">Localization & Generics</span></span>](http://hishambinateya.com/localization-and-generics)
