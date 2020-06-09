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
# <a name="configure-portable-object-localization-in-aspnet-core"></a>"Blazor"

"Identity"

"Let's Encrypt"

"Razor" "SignalR" uid: fundamentals/portable-object-localization

Configurar la localización de objetos portátiles en ASP.NET Core

## <a name="what-is-a-po-file"></a>Por [Sébastien Ros](https://github.com/sebastienros) y [Scott Addie](https://twitter.com/Scott_Addie)

En este artículo se describen los pasos para usar archivos de objeto portátil (PO) en una aplicación ASP.NET Core con el marco de trabajo de [Orchard Core](https://github.com/OrchardCMS/OrchardCore). **Nota:** Orchard Core no es un producto de Microsoft.
- Por tanto, Microsoft no ofrece soporte técnico para esta característica.
- [Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/POLocalization) ([cómo descargarlo](xref:index#how-to-download-a-sample)) ¿Qué es un archivo de objeto portátil?
- Los archivos de objeto portátil se distribuyen como archivos de texto que contienen las cadenas traducidas de un idioma determinado.

### <a name="example"></a>Entre las ventajas de usar archivos de objeto portátil en lugar de archivos *.resx* se incluyen las siguientes:

Los archivos de objeto portátil admiten la pluralización, a diferencia de los archivos *.resx*.

Los archivos de objeto portátil no se compilan como archivos *.resx*.

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

Por tanto, no se requieren herramientas ni pasos de compilación especializados.

- Los archivos de objeto portátil funcionan bien con herramientas de colaboración de edición en línea. Ejemplo
- Este es un archivo de objeto portátil de ejemplo que contiene la traducción de dos cadenas en francés, incluida una con su forma en plural:
- *fr.po*

En este ejemplo se usa la sintaxis siguiente:

- `#:`: comentario que indica el contexto de la cadena que se va a traducir.
- La misma cadena se podría traducir de forma diferente según donde se vaya a usar.
- `msgid`: la cadena sin traducir.

`msgstr`: la cadena traducida.

## <a name="configuring-po-file-support-in-aspnet-core"></a>En el caso de compatibilidad con la pluralización, se pueden definir más entradas.

`msgid_plural`: la cadena en plural sin traducir.

### <a name="referencing-the-package"></a>`msgstr[0]`: la cadena traducida para el caso 0.

`msgstr[N]`: la cadena traducida para el caso N. Encontrará [aquí](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html) la especificación del archivo de objeto portátil.

Configuración de la compatibilidad con archivos de objeto portátil en ASP.NET Core

[!code-xml[](localization/sample/2.x/POLocalization/POLocalization.csproj?range=9)]

### <a name="registering-the-service"></a>Este ejemplo se basa en una aplicación ASP.NET Core MVC generada a partir de una plantilla de proyecto de Visual Studio 2017.

Hacer referencia al paquete

[!code-csharp[](localization/sample/2.x/POLocalization/Startup.cs?name=snippet_ConfigureServices&highlight=4-21)]

Agregue una referencia al paquete NuGet `OrchardCore.Localization.Core`.

[!code-csharp[](localization/sample/2.x/POLocalization/Startup.cs?name=snippet_Configure&highlight=15)]

Este paquete se encuentra disponible en [MyGet](https://www.myget.org/), en el siguiente origen de paquete: https://www.myget.org/F/orchardcore-preview/api/v3/index.json. El archivo *.csproj* ahora contiene una línea similar a la siguiente (el número de versión puede variar):

[!code-cshtml[](localization/sample/2.x/POLocalization/Views/Home/About.cshtml)]

Registrar el servicio

### <a name="creating-a-po-file"></a>Agregue los servicios necesarios al método `ConfigureServices` de *Startup.cs*:

Agregue el software intermedio necesario al método `Configure` de *Startup.cs*: Agregue el código siguiente a la vista de Razor que quiera.

[!code-text[](localization/sample/2.x/POLocalization/fr.po)]

En este ejemplo se usa *About.cshtml*. Se inserta una instancia de `IViewLocalizer` que se usa para traducir el texto "Hello world!". Crear un archivo de objeto portátil

### <a name="testing-the-application"></a>Cree un archivo denominado *\<culture code>.po* en la carpeta raíz de la aplicación.

En este ejemplo, el nombre del archivo es *fr.po* porque se usa el idioma francés: En este archivo se almacenan la cadena que se va a traducir y la cadena traducida al francés. Si es necesario, las traducciones se revierten a su referencia cultural principal.

En este ejemplo, el archivo *fr.po* se usa si la referencia cultural solicitada es `fr-FR` o `fr-CA`. Probar la aplicación Ejecute la aplicación y vaya a la URL `/Home/About`.

## <a name="pluralization"></a>El texto **Hello world!**

aparece en pantalla. Vaya a la dirección URL `/Home/About?culture=fr-FR`.

El texto **Bonjour le monde!**

### <a name="creating-pluralization-po-files"></a>aparece en pantalla.

Pluralización

```text
msgid "There is one item."
msgid_plural "There are {0} items."
msgstr[0] "Il y a un élément."
msgstr[1] "Il y a {0} éléments."
```

Los archivos de objeto portátil son compatibles con las formas de pluralización, lo que resulta útil cuando es necesario traducir la misma cadena de forma diferente en función de una cardinalidad.

### <a name="adding-a-language-using-different-pluralization-forms"></a>Esta tarea se ve dificultada por el hecho de que cada idioma define reglas personalizadas para seleccionar qué cadena se va a usar en función de la cardinalidad.

El paquete de localización de Orchard proporciona una API para invocar automáticamente estas diferentes formas plurales. Crear archivos de objeto portátil de pluralización Agregue el contenido siguiente al archivo *fr.po* mencionado anteriormente:

Vea [¿Qué es un archivo de objeto portátil?](#what-is-a-po-file) para saber qué representa cada entrada de este ejemplo. Agregar un idioma con formas de pluralización diferentes

En el ejemplo anterior se han usado cadenas en inglés y francés.

[!code-text[](localization/sample/2.x/POLocalization/cs.po)]

El idioma inglés y francés solo tienen dos formas de pluralización y comparten las mismas reglas de formato, es decir, se asigna una cardinalidad de uno a la primera forma plural.

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

Todas las demás cardinalidades se asignan a la segunda forma plural.

```cshtml
<p>@Localizer.Plural(1, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(2, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(5, "There is one item.", "There are {0} items.")</p>
```

No todos los idiomas comparten las mismas reglas. Esto se muestra con el idioma checo, que tiene tres formas plurales.

Cree el archivo `cs.po` como se indica a continuación y observe que la pluralización requiere tres traducciones diferentes:

Para aceptar localizaciones en checo, agregue `"cs"` a la lista de referencias culturales admitidas en el método `ConfigureServices`:

```html
There is one item.
There are 2 items.
There are 5 items.
```

Edite el archivo *Views/Home/About.cshtml* para representar cadenas localizadas en plural para diversas cardinalidades:

```html
Il y a un élément.
Il y a 2 éléments.
Il y a 5 éléments.
```

**Nota:** En un escenario real, se usaría una variable para representar el número.

```html
Existuje jedna položka.
Existují 2 položky.
Existuje 5 položek.
```

En este caso, repetimos el mismo código con tres valores diferentes para exponer un caso muy específico. Si cambia las referencias culturales, verá lo siguiente:

## <a name="advanced-tasks"></a>Para `/Home/About`:

### <a name="contextualizing-strings"></a>Para `/Home/About?culture=fr`:

Para `/Home/About?culture=cs`: Tenga en cuenta que para la referencia cultural de checo, las tres traducciones son diferentes. Las referencias culturales de inglés y francés comparten la misma construcción para las dos últimas cadenas traducidas. Tareas avanzadas

Contextualización de cadenas Las aplicaciones a menudo contienen las cadenas que se van a traducir en lugares diferentes.

La misma cadena puede tener una traducción diferente en determinadas ubicaciones dentro de una aplicación (vistas de Razor o archivos de clase). Un archivo de objeto portátil admite el concepto de contexto de archivo, que se puede usar para clasificar la cadena que se va a representar.

```text
msgctxt "Views.Home.About"
msgid "Hello world!"
msgstr "Bonjour le monde!"
```

Mediante el uso de un contexto de archivo, una cadena se puede traducir de forma diferente según el contexto de archivo (o según la falta de contexto de archivo). Los servicios de localización de objetos portátiles usan el nombre de la clase completa o la vista que se usa al traducir una cadena.

Esto se logra mediante el establecimiento del valor en la entrada `msgctxt`. Considere la posibilidad de realizar una adición mínima al ejemplo *fr.po* anterior.

[!code-text[](localization/sample/2.x/POLocalization/fr.po)]

### <a name="changing-the-location-of-po-files"></a>Una vista de Razor ubicada en *Views/Home/About.cshtml* se puede definir como el contexto de archivo si se establece el valor de la entrada reservado `msgctxt`:

Después de establecer `msgctxt`, el texto se traduce cuando se va a `/Home/About?culture=fr-FR`.

```csharp
services.AddPortableObjectLocalization(options => options.ResourcesPath = "Localization");
```

La traducción no se llevará a cabo al ir a `/Home/Contact?culture=fr-FR`.

### <a name="implementing-a-custom-logic-for-finding-localization-files"></a>Cuando ninguna entrada específica coincide con un contexto de archivo determinado, el mecanismo de reserva de Orchard Core busca un archivo de objeto portátil adecuado sin contexto.

Suponiendo que no haya ningún contexto de archivo específico definido para *Views/Home/Contact.cshtml*, al ir a `/Home/Contact?culture=fr-FR` se carga un archivo de objeto portátil como: Cambiar la ubicación de los archivos de objeto portátil

### <a name="using-a-different-default-pluralized-language"></a>La ubicación predeterminada de los archivos de objeto portátil se puede cambiar en `ConfigureServices`:

En este ejemplo, los archivos de objeto portátil se cargan desde la carpeta *Localization*. Implementar una lógica personalizada para buscar archivos de localización Cuando se necesita una lógica más compleja para buscar archivos de objeto portátil, es posible implementar y registrar la interfaz `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` como un servicio.

Esto es útil cuando los archivos de objeto portátil se pueden almacenar en ubicaciones diferentes o cuando los archivos deben encontrarse en una jerarquía de carpetas.
