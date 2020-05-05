---
title: Formateadores personalizados en ASP.NET Core Web API
author: rick-anderson
description: Obtenga información sobre cómo crear y utilizar formateadores personalizados para las API web de ASP.NET Core.
ms.author: riande
ms.date: 02/08/2017
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: web-api/advanced/custom-formatters
ms.openlocfilehash: 0836fc288a015adb9a6223c5a2b681b1b03bded4
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777324"
---
# <a name="custom-formatters-in-aspnet-core-web-api"></a>Formateadores personalizados en ASP.NET Core Web API

Por [Tom Dykstra](https://github.com/tdykstra)

ASP.NET Core MVC admite el intercambio de datos en las API Web con formateadores de entrada y salida. El [enlace de modelos](xref:mvc/models/model-binding) usa formateadores de entrada. Los formateadores de salida se usan para [dar formato](xref:web-api/advanced/formatting)a las respuestas.

El marco proporciona formateadores de entrada y salida integrados para JSON y XML. Proporciona un formateador de salida integrado para texto sin formato, pero no proporciona un formateador de entrada para texto sin formato.

En este artículo se muestra cómo agregar compatibilidad con formatos adicionales mediante la creación de formateadores personalizados. Para ver un ejemplo de un formateador de entrada personalizado para texto sin formato, consulte [TextPlainInputFormatter](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.Formatters/TextPlainInputFormatter.cs) en GitHub.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample) ([cómo descargarlo](xref:index#how-to-download-a-sample))

## <a name="when-to-use-custom-formatters"></a>Cuándo usar formateadores personalizados

Utilice un formateador personalizado cuando quiera que el proceso de [negociación de contenido](xref:web-api/advanced/formatting#content-negotiation) admita un tipo de contenido que no es compatible con los formateadores integrados.

Por ejemplo, si algunos de los clientes de la API web pueden controlar el formato [Protobuf](https://github.com/google/protobuf), quizá quiera usar Protobuf con esos clientes porque le resulte más eficaz. O tal vez desee que la API web envíe nombres y direcciones de contacto en formato [vCard](https://wikipedia.org/wiki/VCard), un formato comúnmente utilizado para intercambiar datos de contacto. La aplicación de ejemplo proporcionada con este artículo implementa a un formateador de vCard simple.

## <a name="overview-of-how-to-use-a-custom-formatter"></a>Información general sobre cómo utilizar a un formateador personalizado

Estos son los pasos para crear y usar a un formateador personalizado:

* Cree una clase de formateador de salida si quiere serializar los datos que se van a enviar al cliente.
* Cree una clase de formateador de entrada si quiere deserializar los datos recibidos del cliente.
* Agregue instancias de los formateadores a las colecciones `InputFormatters` y `OutputFormatters` en [MvcOptions](/dotnet/api/microsoft.aspnetcore.mvc.mvcoptions).

Las secciones siguientes proporcionan instrucciones y ejemplos de código para cada uno de estos pasos.

## <a name="how-to-create-a-custom-formatter-class"></a>Cómo crear una clase de formateador personalizado

Para crear un formateador:

* Derive la clase de la clase base adecuada.
* Especifique tipos de medios válidos y codificaciones en el constructor.
* Invalidar métodos `CanReadType`/`CanWriteType`
* Invalidar métodos `ReadRequestBodyAsync`/`WriteResponseBodyAsync`
  
### <a name="derive-from-the-appropriate-base-class"></a>Derivar de la clase base adecuada

Para los tipos de medios de texto (por ejemplo, vCard), se deriva de la clase base [TextInputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textinputformatter) o [TextOutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textoutputformatter).

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=classdef)]

Para obtener un ejemplo del formateador de entrada, consulte la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).

Para los tipos binarios, se deriva de la clase base [InputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.inputformatter) o [OutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformatter).

### <a name="specify-valid-media-types-and-encodings"></a>Especificar las codificaciones y los tipos de medios válidos

En el constructor, especifique tipos de medios y codificaciones válidos. Para ello, agréguelos a las colecciones `SupportedMediaTypes` y `SupportedEncodings`.

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=ctor&highlight=3,5-6)]

Para obtener un ejemplo del formateador de entrada, consulte la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).

> [!NOTE]
> No puede realizar la inserción de dependencias de constructor en una clase de formateador. Por ejemplo, no se puede obtener un registrador mediante la adición de un parámetro de registrador al constructor. Para obtener acceso a los servicios, tendrá que usar el objeto de contexto que se pasa a sus métodos. [A continuación](#read-write) se incluye un código de ejemplo que muestra cómo hacerlo.

### <a name="override-canreadtypecanwritetype"></a>Invalidar CanReadType/CanWriteType

Invalide los métodos `CanReadType` o `CanWriteType` para especificar el tipo en el que se puede deserializar o desde el que se puede serializar. Por ejemplo, quizá solo pueda crear texto de vCard desde un tipo `Contact` y viceversa.

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=canwritetype)]

Para obtener un ejemplo del formateador de entrada, consulte la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).

#### <a name="the-canwriteresult-method"></a>Método CanWriteResult

En algunos casos tendrá que invalidar `CanWriteResult` en lugar de `CanWriteType`. Use `CanWriteResult` si las condiciones siguientes son verdaderas:

* El método de acción devuelve una clase de modelo.
* Hay clases derivadas que pueden obtenerse en tiempo de ejecución.
* Debe conocer en tiempo de ejecución qué clase derivada fue devuelta por la acción.

Por ejemplo, suponga que la firma del método de acción devuelve un tipo `Person`, pero puede devolver un tipo `Student` o `Instructor` que deriva de `Person`. Si quiere que el formateador únicamente controle objetos `Student`, compruebe el tipo del [objeto](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformattercanwritecontext.object#Microsoft_AspNetCore_Mvc_Formatters_OutputFormatterCanWriteContext_Object) en el objeto de contexto proporcionado para el método `CanWriteResult`. Tenga en cuenta que no es necesario utilizar `CanWriteResult` cuando se devuelve el método de acción `IActionResult`; en ese caso, el método `CanWriteType` recibe el tipo en tiempo de ejecución.

<a id="read-write"></a>

### <a name="override-readrequestbodyasyncwriteresponsebodyasync"></a>Invalidar ReadRequestBodyAsync/WriteResponseBodyAsync

Usted se encarga de hacer el trabajo real de deserializar o serializar en `ReadRequestBodyAsync` o `WriteResponseBodyAsync`. Las líneas resaltadas en el ejemplo siguiente muestran cómo obtener los servicios desde el contenedor de inserción de dependencias (no es posible obtenerlos desde a partir de los parámetros del constructor).

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=writeresponse&highlight=3-4)]

Para obtener un ejemplo del formateador de entrada, consulte la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).

## <a name="how-to-configure-mvc-to-use-a-custom-formatter"></a>Cómo configurar MVC para usar a un formateador personalizado

Para utilizar un formateador personalizado, debe agregar una instancia de la clase de formateador a la colección `InputFormatters` o `OutputFormatters`.

[!code-csharp[](custom-formatters/sample/Startup.cs?name=mvcoptions&highlight=3-4)]

Los formateadores se evalúan en el orden en que se insertaron. El primero de ellos tiene prioridad.

## <a name="next-steps"></a>Pasos siguientes

* [Aplicación de ejemplo de este documento](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample), que implementa formateadores de entrada y salida de vCard. La aplicación lee y escribe tarjetas vCard que tienen un aspecto similar al siguiente:

```
BEGIN:VCARD
VERSION:2.1
N:Davolio;Nancy
FN:Nancy Davolio
no-loc: [Blazor, "Identity", "Let's Encrypt", Razor, SignalR]
uid:20293482-9240-4d68-b475-325df4a83728
END:VCARD
```

Para ver la salida de vCard, ejecute la aplicación y envíe una solicitud Get con encabezado Accept "texto/vcard" a `http://localhost:63313/api/contacts/` (cuando se ejecuta desde Visual Studio) o `http://localhost:5000/api/contacts/` (cuando se ejecuta desde la línea de comandos).

Para agregar una tarjeta vCard a la colección en memoria de contactos, envíe una solicitud Post a la misma dirección URL, con el encabezado contenido-tipo "texto/vcard" y con texto de vCard en el cuerpo. El formato sería similar al ejemplo anterior.
