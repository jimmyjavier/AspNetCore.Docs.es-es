---
title: 'title: Configuración de la localización de objetos portátiles en ASP.NET Core author: sebastienros description: En este artículo se presentan los archivos de objeto portátil y se describen los pasos para usarlos en una aplicación ASP.NET Core con el marco de trabajo de Orchard Core.'
author: sebastienros
description: 'ms.author: scaddie ms.date: 09/26/2017 no-loc:'
ms.author: scaddie
ms.date: 09/26/2017
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/portable-object-localization
ms.openlocfilehash: 57498cc2a773e5147b6eda653cc89d303f238b78
ms.sourcegitcommit: 829dca1d5a7dcccbfe90644101c6be1d1c94ac62
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/04/2020
ms.locfileid: "84355089"
---
# <a name="configure-portable-object-localization-in-aspnet-core"></a><span data-ttu-id="07082-103">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="07082-103">'Blazor'</span></span>

<span data-ttu-id="07082-104">"Identity"</span><span class="sxs-lookup"><span data-stu-id="07082-104">'Identity'</span></span>

<span data-ttu-id="07082-105">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="07082-105">'Let's Encrypt'</span></span>

<span data-ttu-id="07082-106">"Razor"</span><span class="sxs-lookup"><span data-stu-id="07082-106">'Razor'</span></span> <span data-ttu-id="07082-107">"SignalR" uid: fundamentals/portable-object-localization</span><span class="sxs-lookup"><span data-stu-id="07082-107">'SignalR' uid: fundamentals/portable-object-localization</span></span>

<span data-ttu-id="07082-108">Configurar la localización de objetos portátiles en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="07082-108">Configure portable object localization in ASP.NET Core</span></span>

## <a name="what-is-a-po-file"></a><span data-ttu-id="07082-109">Por [Sébastien Ros](https://github.com/sebastienros) y [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="07082-109">By [Sébastien Ros](https://github.com/sebastienros) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="07082-110">En este artículo se describen los pasos para usar archivos de objeto portátil (PO) en una aplicación ASP.NET Core con el marco de trabajo de [Orchard Core](https://github.com/OrchardCMS/OrchardCore).</span><span class="sxs-lookup"><span data-stu-id="07082-110">This article walks through the steps for using Portable Object (PO) files in an ASP.NET Core application with the [Orchard Core](https://github.com/OrchardCMS/OrchardCore) framework.</span></span> <span data-ttu-id="07082-111">**Nota:** Orchard Core no es un producto de Microsoft.</span><span class="sxs-lookup"><span data-stu-id="07082-111">**Note:** Orchard Core isn't a Microsoft product.</span></span>
- <span data-ttu-id="07082-112">Por tanto, Microsoft no ofrece soporte técnico para esta característica.</span><span class="sxs-lookup"><span data-stu-id="07082-112">Consequently, Microsoft provides no support for this feature.</span></span>
- <span data-ttu-id="07082-113">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/POLocalization) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="07082-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/POLocalization) ([how to download](xref:index#how-to-download-a-sample))</span></span> <span data-ttu-id="07082-114">¿Qué es un archivo de objeto portátil?</span><span class="sxs-lookup"><span data-stu-id="07082-114">What is a PO file?</span></span>
- <span data-ttu-id="07082-115">Los archivos de objeto portátil se distribuyen como archivos de texto que contienen las cadenas traducidas de un idioma determinado.</span><span class="sxs-lookup"><span data-stu-id="07082-115">PO files are distributed as text files containing the translated strings for a given language.</span></span>

### <a name="example"></a><span data-ttu-id="07082-116">Entre las ventajas de usar archivos de objeto portátil en lugar de archivos *.resx* se incluyen las siguientes:</span><span class="sxs-lookup"><span data-stu-id="07082-116">Some advantages of using PO files instead *.resx* files include:</span></span>

<span data-ttu-id="07082-117">Los archivos de objeto portátil admiten la pluralización, a diferencia de los archivos *.resx*.</span><span class="sxs-lookup"><span data-stu-id="07082-117">PO files support pluralization; *.resx* files don't support pluralization.</span></span>

<span data-ttu-id="07082-118">Los archivos de objeto portátil no se compilan como archivos *.resx*.</span><span class="sxs-lookup"><span data-stu-id="07082-118">PO files aren't compiled like *.resx* files.</span></span>

```text
#: Services/EmailService.cs:29
msgid "Enter a comma separated list of email addresses."
msgstr "Entrez une liste d'emails séparés par une virgule."

#: Views/Email.cshtml:112
msgid "The email address is \"{0}\"."
msgid_plural "The email addresses are \"{0}\"."
msgstr[0] "L'adresse email est \"{0}\"."
msgstr[1] "Les adresses email sont \"{0}\""
```

<span data-ttu-id="07082-119">Por tanto, no se requieren herramientas ni pasos de compilación especializados.</span><span class="sxs-lookup"><span data-stu-id="07082-119">As such, specialized tooling and build steps aren't required.</span></span>

- <span data-ttu-id="07082-120">Los archivos de objeto portátil funcionan bien con herramientas de colaboración de edición en línea.</span><span class="sxs-lookup"><span data-stu-id="07082-120">PO files work well with collaborative online editing tools.</span></span> <span data-ttu-id="07082-121">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="07082-121">Example</span></span>
- <span data-ttu-id="07082-122">Este es un archivo de objeto portátil de ejemplo que contiene la traducción de dos cadenas en francés, incluida una con su forma en plural:</span><span class="sxs-lookup"><span data-stu-id="07082-122">Here is a sample PO file containing the translation for two strings in French, including one with its plural form:</span></span>
- <span data-ttu-id="07082-123">*fr.po*</span><span class="sxs-lookup"><span data-stu-id="07082-123">*fr.po*</span></span>

<span data-ttu-id="07082-124">En este ejemplo se usa la sintaxis siguiente:</span><span class="sxs-lookup"><span data-stu-id="07082-124">This example uses the following syntax:</span></span>

- <span data-ttu-id="07082-125">`#:`: comentario que indica el contexto de la cadena que se va a traducir.</span><span class="sxs-lookup"><span data-stu-id="07082-125">`#:`: A comment indicating the context of the string to be translated.</span></span>
- <span data-ttu-id="07082-126">La misma cadena se podría traducir de forma diferente según donde se vaya a usar.</span><span class="sxs-lookup"><span data-stu-id="07082-126">The same string might be translated differently depending on where it's being used.</span></span>
- <span data-ttu-id="07082-127">`msgid`: la cadena sin traducir.</span><span class="sxs-lookup"><span data-stu-id="07082-127">`msgid`: The untranslated string.</span></span>

<span data-ttu-id="07082-128">`msgstr`: la cadena traducida.</span><span class="sxs-lookup"><span data-stu-id="07082-128">`msgstr`: The translated string.</span></span>

## <a name="configuring-po-file-support-in-aspnet-core"></a><span data-ttu-id="07082-129">En el caso de compatibilidad con la pluralización, se pueden definir más entradas.</span><span class="sxs-lookup"><span data-stu-id="07082-129">In the case of pluralization support, more entries can be defined.</span></span>

<span data-ttu-id="07082-130">`msgid_plural`: la cadena en plural sin traducir.</span><span class="sxs-lookup"><span data-stu-id="07082-130">`msgid_plural`: The untranslated plural string.</span></span>

### <a name="referencing-the-package"></a><span data-ttu-id="07082-131">`msgstr[0]`: la cadena traducida para el caso 0.</span><span class="sxs-lookup"><span data-stu-id="07082-131">`msgstr[0]`: The translated string for the case 0.</span></span>

<span data-ttu-id="07082-132">`msgstr[N]`: la cadena traducida para el caso N.</span><span class="sxs-lookup"><span data-stu-id="07082-132">`msgstr[N]`: The translated string for the case N.</span></span> <span data-ttu-id="07082-133">Encontrará [aquí](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html) la especificación del archivo de objeto portátil.</span><span class="sxs-lookup"><span data-stu-id="07082-133">The PO file specification can be found [here](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html).</span></span>

<span data-ttu-id="07082-134">Configuración de la compatibilidad con archivos de objeto portátil en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="07082-134">Configuring PO file support in ASP.NET Core</span></span>

[!code-xml[](localization/sample/2.x/POLocalization/POLocalization.csproj?range=9)]

### <a name="registering-the-service"></a><span data-ttu-id="07082-135">Este ejemplo se basa en una aplicación ASP.NET Core MVC generada a partir de una plantilla de proyecto de Visual Studio 2017.</span><span class="sxs-lookup"><span data-stu-id="07082-135">This example is based on an ASP.NET Core MVC application generated from a Visual Studio 2017 project template.</span></span>

<span data-ttu-id="07082-136">Hacer referencia al paquete</span><span class="sxs-lookup"><span data-stu-id="07082-136">Referencing the package</span></span>

[!code-csharp[](localization/sample/2.x/POLocalization/Startup.cs?name=snippet_ConfigureServices&highlight=4-21)]

<span data-ttu-id="07082-137">Agregue una referencia al paquete NuGet `OrchardCore.Localization.Core`.</span><span class="sxs-lookup"><span data-stu-id="07082-137">Add a reference to the `OrchardCore.Localization.Core` NuGet package.</span></span>

[!code-csharp[](localization/sample/2.x/POLocalization/Startup.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="07082-138">Este paquete se encuentra disponible en [MyGet](https://www.myget.org/), en el siguiente origen de paquete: https://www.myget.org/F/orchardcore-preview/api/v3/index.json.</span><span class="sxs-lookup"><span data-stu-id="07082-138">It's available on [MyGet](https://www.myget.org/) at the following package source: https://www.myget.org/F/orchardcore-preview/api/v3/index.json</span></span> <span data-ttu-id="07082-139">El archivo *.csproj* ahora contiene una línea similar a la siguiente (el número de versión puede variar):</span><span class="sxs-lookup"><span data-stu-id="07082-139">The *.csproj* file now contains a line similar to the following (version number may vary):</span></span>

[!code-cshtml[](localization/sample/2.x/POLocalization/Views/Home/About.cshtml)]

<span data-ttu-id="07082-140">Registrar el servicio</span><span class="sxs-lookup"><span data-stu-id="07082-140">Registering the service</span></span>

### <a name="creating-a-po-file"></a><span data-ttu-id="07082-141">Agregue los servicios necesarios al método `ConfigureServices` de *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="07082-141">Add the required services to the `ConfigureServices` method of *Startup.cs*:</span></span>

<span data-ttu-id="07082-142">Agregue el software intermedio necesario al método `Configure` de *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="07082-142">Add the required middleware to the `Configure` method of *Startup.cs*:</span></span> <span data-ttu-id="07082-143">Agregue el código siguiente a la vista de Razor que quiera.</span><span class="sxs-lookup"><span data-stu-id="07082-143">Add the following code to your Razor view of choice.</span></span>

[!code-text[](localization/sample/2.x/POLocalization/fr.po)]

<span data-ttu-id="07082-144">En este ejemplo se usa *About.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="07082-144">*About.cshtml* is used in this example.</span></span> <span data-ttu-id="07082-145">Se inserta una instancia de `IViewLocalizer` que se usa para traducir el texto "Hello world!".</span><span class="sxs-lookup"><span data-stu-id="07082-145">An `IViewLocalizer` instance is injected and used to translate the text "Hello world!".</span></span> <span data-ttu-id="07082-146">Crear un archivo de objeto portátil</span><span class="sxs-lookup"><span data-stu-id="07082-146">Creating a PO file</span></span>

### <a name="testing-the-application"></a><span data-ttu-id="07082-147">Cree un archivo denominado *\<culture code>.po* en la carpeta raíz de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="07082-147">Create a file named *\<culture code>.po* in your application root folder.</span></span>

<span data-ttu-id="07082-148">En este ejemplo, el nombre del archivo es *fr.po* porque se usa el idioma francés:</span><span class="sxs-lookup"><span data-stu-id="07082-148">In this example, the file name is *fr.po* because the French language is used:</span></span> <span data-ttu-id="07082-149">En este archivo se almacenan la cadena que se va a traducir y la cadena traducida al francés.</span><span class="sxs-lookup"><span data-stu-id="07082-149">This file stores both the string to translate and the French-translated string.</span></span> <span data-ttu-id="07082-150">Si es necesario, las traducciones se revierten a su referencia cultural principal.</span><span class="sxs-lookup"><span data-stu-id="07082-150">Translations revert to their parent culture, if necessary.</span></span>

<span data-ttu-id="07082-151">En este ejemplo, el archivo *fr.po* se usa si la referencia cultural solicitada es `fr-FR` o `fr-CA`.</span><span class="sxs-lookup"><span data-stu-id="07082-151">In this example, the *fr.po* file is used if the requested culture is `fr-FR` or `fr-CA`.</span></span> <span data-ttu-id="07082-152">Probar la aplicación</span><span class="sxs-lookup"><span data-stu-id="07082-152">Testing the application</span></span> <span data-ttu-id="07082-153">Ejecute la aplicación y vaya a la URL `/Home/About`.</span><span class="sxs-lookup"><span data-stu-id="07082-153">Run your application, and navigate to the URL `/Home/About`.</span></span>

## <a name="pluralization"></a><span data-ttu-id="07082-154">El texto **Hello world!**</span><span class="sxs-lookup"><span data-stu-id="07082-154">The text **Hello world!**</span></span>

<span data-ttu-id="07082-155">aparece en pantalla.</span><span class="sxs-lookup"><span data-stu-id="07082-155">is displayed.</span></span> <span data-ttu-id="07082-156">Vaya a la dirección URL `/Home/About?culture=fr-FR`.</span><span class="sxs-lookup"><span data-stu-id="07082-156">Navigate to the URL `/Home/About?culture=fr-FR`.</span></span>

<span data-ttu-id="07082-157">El texto **Bonjour le monde!**</span><span class="sxs-lookup"><span data-stu-id="07082-157">The text **Bonjour le monde!**</span></span>

### <a name="creating-pluralization-po-files"></a><span data-ttu-id="07082-158">aparece en pantalla.</span><span class="sxs-lookup"><span data-stu-id="07082-158">is displayed.</span></span>

<span data-ttu-id="07082-159">Pluralización</span><span class="sxs-lookup"><span data-stu-id="07082-159">Pluralization</span></span>

```text
msgid "There is one item."
msgid_plural "There are {0} items."
msgstr[0] "Il y a un élément."
msgstr[1] "Il y a {0} éléments."
```

<span data-ttu-id="07082-160">Los archivos de objeto portátil son compatibles con las formas de pluralización, lo que resulta útil cuando es necesario traducir la misma cadena de forma diferente en función de una cardinalidad.</span><span class="sxs-lookup"><span data-stu-id="07082-160">PO files support pluralization forms, which is useful when the same string needs to be translated differently based on a cardinality.</span></span>

### <a name="adding-a-language-using-different-pluralization-forms"></a><span data-ttu-id="07082-161">Esta tarea se ve dificultada por el hecho de que cada idioma define reglas personalizadas para seleccionar qué cadena se va a usar en función de la cardinalidad.</span><span class="sxs-lookup"><span data-stu-id="07082-161">This task is made complicated by the fact that each language defines custom rules to select which string to use based on the cardinality.</span></span>

<span data-ttu-id="07082-162">El paquete de localización de Orchard proporciona una API para invocar automáticamente estas diferentes formas plurales.</span><span class="sxs-lookup"><span data-stu-id="07082-162">The Orchard Localization package provides an API to invoke these different plural forms automatically.</span></span> <span data-ttu-id="07082-163">Crear archivos de objeto portátil de pluralización</span><span class="sxs-lookup"><span data-stu-id="07082-163">Creating pluralization PO files</span></span> <span data-ttu-id="07082-164">Agregue el contenido siguiente al archivo *fr.po* mencionado anteriormente:</span><span class="sxs-lookup"><span data-stu-id="07082-164">Add the following content to the previously mentioned *fr.po* file:</span></span>

<span data-ttu-id="07082-165">Vea [¿Qué es un archivo de objeto portátil?](#what-is-a-po-file) para saber qué representa cada entrada de este ejemplo.</span><span class="sxs-lookup"><span data-stu-id="07082-165">See [What is a PO file?](#what-is-a-po-file) for an explanation of what each entry in this example represents.</span></span> <span data-ttu-id="07082-166">Agregar un idioma con formas de pluralización diferentes</span><span class="sxs-lookup"><span data-stu-id="07082-166">Adding a language using different pluralization forms</span></span>

<span data-ttu-id="07082-167">En el ejemplo anterior se han usado cadenas en inglés y francés.</span><span class="sxs-lookup"><span data-stu-id="07082-167">English and French strings were used in the previous example.</span></span>

[!code-text[](localization/sample/2.x/POLocalization/cs.po)]

<span data-ttu-id="07082-168">El idioma inglés y francés solo tienen dos formas de pluralización y comparten las mismas reglas de formato, es decir, se asigna una cardinalidad de uno a la primera forma plural.</span><span class="sxs-lookup"><span data-stu-id="07082-168">English and French have only two pluralization forms and share the same form rules, which is that a cardinality of one is mapped to the first plural form.</span></span>

```csharp
var supportedCultures = new List<CultureInfo>
{
    new CultureInfo("en-US"),
    new CultureInfo("en"),
    new CultureInfo("fr-FR"),
    new CultureInfo("fr"),
    new CultureInfo("cs")
};
```

<span data-ttu-id="07082-169">Todas las demás cardinalidades se asignan a la segunda forma plural.</span><span class="sxs-lookup"><span data-stu-id="07082-169">Any other cardinality is mapped to the second plural form.</span></span>

```cshtml
<p>@Localizer.Plural(1, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(2, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(5, "There is one item.", "There are {0} items.")</p>
```

<span data-ttu-id="07082-170">No todos los idiomas comparten las mismas reglas.</span><span class="sxs-lookup"><span data-stu-id="07082-170">Not all languages share the same rules.</span></span> <span data-ttu-id="07082-171">Esto se muestra con el idioma checo, que tiene tres formas plurales.</span><span class="sxs-lookup"><span data-stu-id="07082-171">This is illustrated with the Czech language, which has three plural forms.</span></span>

<span data-ttu-id="07082-172">Cree el archivo `cs.po` como se indica a continuación y observe que la pluralización requiere tres traducciones diferentes:</span><span class="sxs-lookup"><span data-stu-id="07082-172">Create the `cs.po` file as follows, and note how the pluralization needs three different translations:</span></span>

<span data-ttu-id="07082-173">Para aceptar localizaciones en checo, agregue `"cs"` a la lista de referencias culturales admitidas en el método `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="07082-173">To accept Czech localizations, add `"cs"` to the list of supported cultures in the `ConfigureServices` method:</span></span>

```html
There is one item.
There are 2 items.
There are 5 items.
```

<span data-ttu-id="07082-174">Edite el archivo *Views/Home/About.cshtml* para representar cadenas localizadas en plural para diversas cardinalidades:</span><span class="sxs-lookup"><span data-stu-id="07082-174">Edit the *Views/Home/About.cshtml* file to render localized, plural strings for several cardinalities:</span></span>

```html
Il y a un élément.
Il y a 2 éléments.
Il y a 5 éléments.
```

<span data-ttu-id="07082-175">**Nota:** En un escenario real, se usaría una variable para representar el número.</span><span class="sxs-lookup"><span data-stu-id="07082-175">**Note:** In a real world scenario, a variable would be used to represent the count.</span></span>

```html
Existuje jedna položka.
Existují 2 položky.
Existuje 5 položek.
```

<span data-ttu-id="07082-176">En este caso, repetimos el mismo código con tres valores diferentes para exponer un caso muy específico.</span><span class="sxs-lookup"><span data-stu-id="07082-176">Here, we repeat the same code with three different values to expose a very specific case.</span></span> <span data-ttu-id="07082-177">Si cambia las referencias culturales, verá lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="07082-177">Upon switching cultures, you see the following:</span></span>

## <a name="advanced-tasks"></a><span data-ttu-id="07082-178">Para `/Home/About`:</span><span class="sxs-lookup"><span data-stu-id="07082-178">For `/Home/About`:</span></span>

### <a name="contextualizing-strings"></a><span data-ttu-id="07082-179">Para `/Home/About?culture=fr`:</span><span class="sxs-lookup"><span data-stu-id="07082-179">For `/Home/About?culture=fr`:</span></span>

<span data-ttu-id="07082-180">Para `/Home/About?culture=cs`:</span><span class="sxs-lookup"><span data-stu-id="07082-180">For `/Home/About?culture=cs`:</span></span> <span data-ttu-id="07082-181">Tenga en cuenta que para la referencia cultural de checo, las tres traducciones son diferentes.</span><span class="sxs-lookup"><span data-stu-id="07082-181">Note that for the Czech culture, the three translations are different.</span></span> <span data-ttu-id="07082-182">Las referencias culturales de inglés y francés comparten la misma construcción para las dos últimas cadenas traducidas.</span><span class="sxs-lookup"><span data-stu-id="07082-182">The French and English cultures share the same construction for the two last translated strings.</span></span> <span data-ttu-id="07082-183">Tareas avanzadas</span><span class="sxs-lookup"><span data-stu-id="07082-183">Advanced tasks</span></span>

<span data-ttu-id="07082-184">Contextualización de cadenas</span><span class="sxs-lookup"><span data-stu-id="07082-184">Contextualizing strings</span></span> <span data-ttu-id="07082-185">Las aplicaciones a menudo contienen las cadenas que se van a traducir en lugares diferentes.</span><span class="sxs-lookup"><span data-stu-id="07082-185">Applications often contain the strings to be translated in several places.</span></span>

<span data-ttu-id="07082-186">La misma cadena puede tener una traducción diferente en determinadas ubicaciones dentro de una aplicación (vistas de Razor o archivos de clase).</span><span class="sxs-lookup"><span data-stu-id="07082-186">The same string may have a different translation in certain locations within an app (Razor views or class files).</span></span> <span data-ttu-id="07082-187">Un archivo de objeto portátil admite el concepto de contexto de archivo, que se puede usar para clasificar la cadena que se va a representar.</span><span class="sxs-lookup"><span data-stu-id="07082-187">A PO file supports the notion of a file context, which can be used to categorize the string being represented.</span></span>

```text
msgctxt "Views.Home.About"
msgid "Hello world!"
msgstr "Bonjour le monde!"
```

<span data-ttu-id="07082-188">Mediante el uso de un contexto de archivo, una cadena se puede traducir de forma diferente según el contexto de archivo (o según la falta de contexto de archivo).</span><span class="sxs-lookup"><span data-stu-id="07082-188">Using a file context, a string can be translated differently, depending on the file context (or lack of a file context).</span></span> <span data-ttu-id="07082-189">Los servicios de localización de objetos portátiles usan el nombre de la clase completa o la vista que se usa al traducir una cadena.</span><span class="sxs-lookup"><span data-stu-id="07082-189">The PO localization services use the name of the full class or the view that's used when translating a string.</span></span>

<span data-ttu-id="07082-190">Esto se logra mediante el establecimiento del valor en la entrada `msgctxt`.</span><span class="sxs-lookup"><span data-stu-id="07082-190">This is accomplished by setting the value on the `msgctxt` entry.</span></span> <span data-ttu-id="07082-191">Considere la posibilidad de realizar una adición mínima al ejemplo *fr.po* anterior.</span><span class="sxs-lookup"><span data-stu-id="07082-191">Consider a minor addition to the previous *fr.po* example.</span></span>

[!code-text[](localization/sample/2.x/POLocalization/fr.po)]

### <a name="changing-the-location-of-po-files"></a><span data-ttu-id="07082-192">Una vista de Razor ubicada en *Views/Home/About.cshtml* se puede definir como el contexto de archivo si se establece el valor de la entrada reservado `msgctxt`:</span><span class="sxs-lookup"><span data-stu-id="07082-192">A Razor view located at *Views/Home/About.cshtml* can be defined as the file context by setting the reserved `msgctxt` entry's value:</span></span>

<span data-ttu-id="07082-193">Después de establecer `msgctxt`, el texto se traduce cuando se va a `/Home/About?culture=fr-FR`.</span><span class="sxs-lookup"><span data-stu-id="07082-193">With the `msgctxt` set as such, text translation occurs when navigating to `/Home/About?culture=fr-FR`.</span></span>

```csharp
services.AddPortableObjectLocalization(options => options.ResourcesPath = "Localization");
```

<span data-ttu-id="07082-194">La traducción no se llevará a cabo al ir a `/Home/Contact?culture=fr-FR`.</span><span class="sxs-lookup"><span data-stu-id="07082-194">The translation won't occur when navigating to `/Home/Contact?culture=fr-FR`.</span></span>

### <a name="implementing-a-custom-logic-for-finding-localization-files"></a><span data-ttu-id="07082-195">Cuando ninguna entrada específica coincide con un contexto de archivo determinado, el mecanismo de reserva de Orchard Core busca un archivo de objeto portátil adecuado sin contexto.</span><span class="sxs-lookup"><span data-stu-id="07082-195">When no specific entry is matched with a given file context, Orchard Core's fallback mechanism looks for an appropriate PO file without a context.</span></span>

<span data-ttu-id="07082-196">Suponiendo que no haya ningún contexto de archivo específico definido para *Views/Home/Contact.cshtml*, al ir a `/Home/Contact?culture=fr-FR` se carga un archivo de objeto portátil como:</span><span class="sxs-lookup"><span data-stu-id="07082-196">Assuming there's no specific file context defined for *Views/Home/Contact.cshtml*, navigating to `/Home/Contact?culture=fr-FR` loads a PO file such as:</span></span> <span data-ttu-id="07082-197">Cambiar la ubicación de los archivos de objeto portátil</span><span class="sxs-lookup"><span data-stu-id="07082-197">Changing the location of PO files</span></span>

### <a name="using-a-different-default-pluralized-language"></a><span data-ttu-id="07082-198">La ubicación predeterminada de los archivos de objeto portátil se puede cambiar en `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="07082-198">The default location of PO files can be changed in `ConfigureServices`:</span></span>

<span data-ttu-id="07082-199">En este ejemplo, los archivos de objeto portátil se cargan desde la carpeta *Localization*.</span><span class="sxs-lookup"><span data-stu-id="07082-199">In this example, the PO files are loaded from the *Localization* folder.</span></span> <span data-ttu-id="07082-200">Implementar una lógica personalizada para buscar archivos de localización</span><span class="sxs-lookup"><span data-stu-id="07082-200">Implementing a custom logic for finding localization files</span></span> <span data-ttu-id="07082-201">Cuando se necesita una lógica más compleja para buscar archivos de objeto portátil, es posible implementar y registrar la interfaz `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` como un servicio.</span><span class="sxs-lookup"><span data-stu-id="07082-201">When more complex logic is needed to locate PO files, the `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` interface can be implemented and registered as a service.</span></span>

<span data-ttu-id="07082-202">Esto es útil cuando los archivos de objeto portátil se pueden almacenar en ubicaciones diferentes o cuando los archivos deben encontrarse en una jerarquía de carpetas.</span><span class="sxs-lookup"><span data-stu-id="07082-202">This is useful when PO files can be stored in varying locations or when the files have to be found within a hierarchy of folders.</span></span>
