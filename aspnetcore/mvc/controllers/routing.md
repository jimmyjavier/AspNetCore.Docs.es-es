---
title: Enrutar a acciones de controlador de ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo ASP.NET Core MVC usa el middleware de enrutamiento para encontrar direcciones URL de las solicitudes entrantes y asignarlas a acciones.
ms.author: riande
ms.date: 3/25/2020
uid: mvc/controllers/routing
ms.openlocfilehash: 9f7a26a482cb115697a0a3d7439c14a062677c92
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277137"
---
# <a name="routing-to-controller-actions-in-aspnet-core"></a>Enrutar a acciones de controlador de ASP.NET Core

Por [Ryan Nowak](https://github.com/rynowak), [Kirk Larkin](https://twitter.com/serpent5) y [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Controladores principales usan el [middleware](xref:fundamentals/middleware/index) de enrutamiento para hacer coincidir las direcciones URL de las solicitudes entrantes y asignarlas a [acciones.](#action)  Plantillas de rutas:

* Se definen en el código de inicio o atributos.
* Describir cómo las rutas de url coinciden con [las acciones.](#action)
* Se utilizan para generar direcciones URL para vínculos. Los vínculos generados normalmente se devuelven en las respuestas.

Las acciones se [enrutan convencionalmente](#cr) o [se enrutan](#ar)a atributos. Colocar una ruta en el controlador o [la acción](#action) hace que se enrute a tribu. Consulte [Enrutamiento mixto](#routing-mixed-ref-label) para obtener más información.

Este documento:

* Explica las interacciones entre MVC y enrutamiento:
  * Cómo las aplicaciones MVC típicas hacen uso de las características de enrutamiento.
  * Cubre ambos:
    * [El enrutamiento convencional](#cr) se utiliza normalmente con controladores y vistas.
    * *Enrutamiento de atributos* utilizado con las API de REST. Si está interesado principalmente en el enrutamiento de las API de REST, vaya a la sección Enrutamiento de atributos [para API de REST.](#ar)
  * Consulte [Enrutamiento](xref:fundamentals/routing) para obtener detalles avanzados de enrutamiento.
* Hace referencia al sistema de enrutamiento predeterminado agregado en ASP.NET Core 3.0, denominado enrutamiento de punto final. Es posible utilizar controladores con la versión anterior de enrutamiento con fines de compatibilidad. Consulte la guía de [migración 2.2-3.0](xref:migration/22-to-30) para obtener instrucciones. Refiera a la [versión 2.2 de este documento](xref:mvc/controllers/routing?view=aspnetcore-2.2) para el material de referencia en el sistema de ruteo heredado.

<a name="cr"></a>

## <a name="set-up-conventional-route"></a>Configurar la ruta convencional

`Startup.Configure`típicamente tiene código similar al siguiente cuando se utiliza [el enrutamiento convencional:](#crd)

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet)]

Dentro de <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>la <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> llamada a , se utiliza para crear una sola ruta. La ruta única `default` se denomina ruta. La mayoría de las aplicaciones con controladores y vistas usan una plantilla de ruta similar a la `default` ruta. Las API de REST deben usar [el enrutamiento de atributos.](#ar)

La plantilla `"{controller=Home}/{action=Index}/{id?}"`de ruta:

* Coincide con una ruta url como`/Products/Details/5`
* Extrae los `{ controller = Products, action = Details, id = 5 }` valores de ruta tokenizando la ruta de acceso. La extracción de valores de ruta da como `ProductsController` resultado `Details` una coincidencia si la aplicación tiene un controlador con nombre y una acción:

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippetA)]

  [!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

* `/Products/Details/5`modelo enlaza el `id = 5` valor de `id` para `5`establecer el parámetro en . Consulte [Enlace de modelos](xref:mvc/models/model-binding) para obtener más detalles.
* `{controller=Home}`define `Home` como `controller`el valor predeterminado .
* `{action=Index}`define `Index` como `action`el valor predeterminado .
*  El `?` carácter en `{id?}` define `id` como opcional.
  * No es necesario que los parámetros de ruta opcionales y predeterminados estén presentes en la ruta de dirección URL para una coincidencia. Consulte [Referencia de plantilla de ruta](xref:fundamentals/routing#route-template-reference) para obtener una descripción detallada de la sintaxis de la plantilla de ruta.
* Coincide con `/`la ruta de acceso url .
* Produce los valores `{ controller = Home, action = Index }`de ruta .

Los valores `controller` `action` para y hacer uso de los valores predeterminados. `id`no produce un valor ya que no hay ningún segmento correspondiente en la ruta de acceso URL. `/`sólo coincide si existe `HomeController` `Index` una y acción:

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

Mediante la definición de controlador `HomeController.Index` anterior y la plantilla de ruta, la acción se ejecuta para las siguientes rutas de URL:

* `/Home/Index/17`
* `/Home/Index`
* `/Home`
* `/`

La ruta `/` de acceso `Home` URL `Index` utiliza los controladores y la acción predeterminados de la plantilla de ruta. La ruta `/Home` de acceso `Index` URL utiliza la acción predeterminada de la plantilla de ruta.

El método de conveniencia <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>:

```csharp
endpoints.MapDefaultControllerRoute();
```

Reemplaza:

```csharp
endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

> [!IMPORTANT]
> El enrutamiento se <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> configura mediante el middleware y. Para utilizar controladores:
>
> * Llame <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> `UseEndpoints` dentro para asignar controladores [enrutados de atributos.](#ar)
> * Llame <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>o , para asignar controladores [enrutados convencionalmente.](#cr)

<a name="routing-conventional-ref-label"></a>
<a name="crd"></a>

## <a name="conventional-routing"></a>Enrutamiento convencional

El enrutamiento convencional se utiliza con controladores y vistas. La ruta `default`:

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet2)]

es un ejemplo de un *enrutamiento convencional*. Se llama *enrutamiento convencional* porque establece una *convención* para las rutas de URL:

* El primer segmento `{controller=Home}`de ruta de acceso, , se asigna al nombre del controlador.
* El segundo `{action=Index}`segmento, , se asigna al nombre de la [acción.](#action)
* El tercer `{id?}` segmento, se `id`utiliza para un archivo . El `?` `{id?}` in lo hace opcional. `id`se utiliza para asignar a una entidad de modelo.

Usando `default` esta ruta, la trayectoria URL:

* `/Products/List`mapas a `ProductsController.List` la acción.
* `/Blog/Article/17`se `BlogController.Article` asigna a y normalmente el modelo enlaza el `id` parámetro a 17.

Esta asignación:

* Se basa **únicamente**en el controlador y los nombres de [acción.](#action)
* No se basa en espacios de nombres, ubicaciones de archivos de origen o parámetros de método.

El uso de enrutamiento convencional con la ruta predeterminada permite crear la aplicación sin tener que crear un nuevo patrón de URL para cada acción. Para una aplicación con acciones de estilo [CRUD,](https://wikipedia.org/wiki/Create,_read,_update_and_delete) con coherencia para las direcciones URL en todos los controladores:

* Ayuda a simplificar el código.
* Hace que la interfaz de usuario sea más predecible.

> [!WARNING]
> El `id` código anterior se define como opcional por la plantilla de ruta. Las acciones se pueden ejecutar sin el identificador opcional proporcionado como parte de la dirección URL. Generalmente,`id` cuando se omite de la dirección URL:
>
> * `id`se establece `0` en el enlace de modelos.
> * No se encuentra ninguna `id == 0`entidad en la coincidencia de base de datos .
>
> [El enrutamiento](#ar) de atributos proporciona un control detallado para que el identificador sea necesario para algunas acciones y no para otras. Por convención, la documentación `id` incluye parámetros opcionales como cuándo es probable que aparezcan en el uso correcto.

La mayoría de las aplicaciones deben elegir un esquema de enrutamiento básico y descriptivo para que las direcciones URL sean legibles y significativas. La ruta convencional predeterminada `{controller=Home}/{action=Index}/{id?}`:

* Admite un esquema de enrutamiento básico y descriptivo.
* Se trata de un punto de partida útil para las aplicaciones basadas en la interfaz de usuario.
* Es la única plantilla de ruta necesaria para muchas aplicaciones de interfaz de usuario web. Para aplicaciones de interfaz de usuario web más grandes, otra ruta que use [Areas](#areas) si con frecuencia todo lo que se necesita.

<xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>y <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> :

* Asigne automáticamente un valor de **pedido** a sus puntos finales en función del orden en que se invocan.

Enrutamiento de punto final en ASP.NET Core 3.0 y versiones posteriores:

* No tiene un concepto de rutas.
* No proporciona garantías de ordenación para la ejecución de la extensibilidad, todos los puntos de conexión se procesan a la vez.

Habilite el [registro](xref:fundamentals/logging/index) para ver de qué forma las implementaciones de enrutamiento integradas, como <xref:Microsoft.AspNetCore.Routing.Route>, coinciden con las solicitudes.

[El ruteo](#ar) de atributos se explica más adelante en este documento.

<a name="mr"></a>

### <a name="multiple-conventional-routes"></a>Múltiples rutas convencionales

Múltiples [rutas convencionales](#cr) se `UseEndpoints` pueden agregar <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> en <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>el interior mediante la adición de más llamadas a y . Esto permite definir varias convenciones, o agregar rutas convencionales dedicadas a una [acción](#action)específica, como:

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<a name="dcr"></a>

La `blog` ruta en el código anterior es una **ruta convencional dedicada.** Se llama una ruta convencional dedicada porque:

* Utiliza [enrutamiento convencional.](#cr)
* Está dedicado a una [acción](#action)específica.

Porque `controller` `action` y no aparecen en `"blog/{*article}"` la plantilla de ruta como parámetros:

* Solo pueden tener los `{ controller = "Blog", action = "Article" }`valores predeterminados.
* Esta ruta siempre se `BlogController.Article`asigna a la acción .

`/Blog`, `/Blog/Article`, `/Blog/{any-string}` y son las únicas rutas de URL que coinciden con la ruta del blog.

El ejemplo anterior:

* `blog`la ruta tiene una prioridad `default` más alta para las coincidencias que la ruta porque se agrega primero.
* Es y ejemplo de enrutamiento de estilo [Slug](https://developer.mozilla.org/docs/Glossary/Slug) donde es típico tener un nombre de artículo como parte de la dirección URL.

> [!WARNING]
> En ASP.NET Core 3.0 y versiones posteriores, el enrutamiento no:
> * Defina un concepto denominado *ruta*. `UseRouting`agrega coincidencia de ruta a la canalización de middleware. El `UseRouting` middleware examina el conjunto de puntos de conexión definidos en la aplicación y selecciona la mejor coincidencia de punto de conexión en función de la solicitud.
> * Proporcionar garantías sobre el orden de <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint>ejecución de extensibilidad como o .
>
>Consulte [Enrutamiento](xref:fundamentals/routing) para obtener material de referencia en el enrutamiento.

<a name="cro"></a>

### <a name="conventional-routing-order"></a>Orden de enrutamiento convencional

El enrutamiento convencional solo coincide con una combinación de acción y controlador definidos por la aplicación. Esto está destinado a simplificar los casos en los que las rutas convencionales se superponen.
Agregar rutas <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>mediante <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> , y asignar automáticamente un valor de pedido a sus puntos finales en función del orden en que se invocan. Las coincidencias de una ruta que aparece anteriormente tienen una prioridad más alta. El enrutamiento convencional depende del orden. En general, las rutas con áreas deben colocarse antes, ya que son más específicas que las rutas sin un área. [Las rutas convencionales dedicadas](#dcr) con `{*article}` captura de todos los parámetros de ruta como puede hacer que una ruta demasiado [codiciosa,](xref:fundamentals/routing#greedy)lo que significa que coincide con las direcciones URL que pretendía que coincidan con otras rutas. Coloque las rutas codiciosas más adelante en la tabla de rutas para evitar coincidencias codiciosas.

<a name="best"></a>

### <a name="resolving-ambiguous-actions"></a>Resolver acciones ambiguas

Cuando dos puntos finales coinciden con el enrutamiento, el enrutamiento debe realizar una de las siguientes acciones:

* Elige el mejor candidato.
* Iniciar una excepción.

Por ejemplo:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet9)]

El controlador anterior define dos acciones que coinciden:

* La ruta URL`/Products33/Edit/17`
* Datos `{ controller = Products33, action = Edit, id = 17 }`de ruta .

Este es un patrón típico para los controladores MVC:

* `Edit(int)`muestra un formulario para editar un producto.
* `Edit(int, Product)`procesa el formulario registrado.

Para resolver la ruta correcta:

* `Edit(int, Product)`se selecciona cuando la `POST`solicitud es HTTP.
* `Edit(int)`se selecciona cuando el [verbo HTTP](#verb) es cualquier otra cosa. `Edit(int)`generalmente se `GET`llama a través de .

El <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute> `[HttpPost]`, , se proporciona al enrutamiento para que pueda elegir en función del método HTTP de la solicitud. El `HttpPostAttribute` `Edit(int, Product)` hace una `Edit(int)`mejor coincidencia que .

Es importante entender el papel de `HttpPostAttribute`atributos como . Se definen atributos similares para otros [verbos HTTP.](#verb) En el [enrutamiento convencional,](#cr)es común que las acciones usen el mismo nombre de acción cuando forman parte de un formulario de inicio, envían el flujo de trabajo del formulario. Por ejemplo, vea [Examinar los dos métodos](xref:tutorials/first-mvc-app/controller-methods-views#get-post)de acción Editar .

Si el enrutamiento no puede elegir <xref:System.Reflection.AmbiguousMatchException> un mejor candidato, se lanza un, que enumera los varios puntos de conexión coincidentes.

<a name="routing-route-name-ref-label"></a>

### <a name="conventional-route-names"></a>Nombres de rutas convencionales

Las `"blog"` cadenas `"default"` y en los siguientes ejemplos son nombres de ruta convencionales:

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

Los nombres de ruta dan un nombre lógico a la ruta. La ruta con nombre se puede utilizar para la generación de URL. El uso de una ruta con nombre simplifica la creación de direcciones URL cuando el orden de las rutas podría complicar la generación de URL. Los nombres de ruta deben ser únicos en toda la aplicación.

Nombres de ruta:

* No tiene ningún impacto en la coincidencia de URL o el control de solicitudes.
* Solo se utilizan para la generación de URL.

El concepto de nombre de ruta se representa en el enrutamiento como [IEndpointNameMetadata](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata). Los términos nombre de **ruta** y **nombre del punto de conexión:**

* Son intercambiables.
* El que se utiliza en la documentación y el código depende de la API que se describe.

<a name="attribute-routing-ref-label"></a>
<a name="ar"></a>

## <a name="attribute-routing-for-rest-apis"></a>Enrutamiento de atributos para API REST

Las API de REST deben usar el enrutamiento de atributos para modelar la funcionalidad de la aplicación como un conjunto de recursos donde las operaciones se representan mediante [verbos HTTP.](#verb)

El enrutamiento mediante atributos utiliza un conjunto de atributos para asignar acciones directamente a las plantillas de ruta. El `StartUp.Configure` código siguiente es típico de una API de REST y se usa en el ejemplo siguiente:

[!code-csharp[](routing/samples/3.x/main/StartupApi.cs?name=snippet)]

En el código <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> anterior, `UseEndpoints` se llama dentro para asignar controladores enrutados de atributos.

En el ejemplo siguiente:

* Se utiliza `Configure` el método anterior.
* `HomeController`coincide con un conjunto de direcciones URL `{controller=Home}/{action=Index}/{id?}` similares a lo que coincide la ruta convencional predeterminada.

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

La `HomeController.Index` acción se ejecuta para `/`cualquiera `/Home` `/Home/Index`de `/Home/Index/3`las rutas de url , , , o .

En este ejemplo se destaca una diferencia de programación clave entre el enrutamiento de atributos y el [enrutamiento convencional.](#cr) El enrutamiento de atributos requiere más entrada para especificar una ruta. La ruta predeterminada convencional maneja las rutas de forma más sucinta. Sin embargo, el enrutamiento de atributos permite y requiere un control preciso de qué plantillas de ruta se aplican a cada [acción.](#action)

Con el enrutamiento de atributos, el nombre del controlador y los nombres de acción no desempeñan **ningún** papel en el que se coincida con la acción. El ejemplo siguiente coincide con las mismas direcciones URL que en el ejemplo anterior:

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

El código siguiente utiliza `action` `controller`el reemplazo de tokens para y :

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet22)]

El código `[Route("[controller]/[action]")]` siguiente se aplica al controlador:

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

En el código anterior, las `Index` plantillas `/` `~/` de método deben anteponer o a las plantillas de ruta. Las plantillas de ruta aplicadas a una acción que comienzan por `/` o `~/` no se combinan con las plantillas de ruta que se aplican al controlador.

Consulte [Prioridad de plantilla](xref:fundamentals/routing#rtp) de ruta para obtener información sobre la selección de plantillas de ruta.

## <a name="reserved-routing-names"></a>Nombres de enrutamientos reservados

Las siguientes palabras clave son nombres de parámetros de ruta reservados cuando se utilizan controladores o páginas de razor:

* `action`
* `area`
* `controller`
* `handler`
* `page`

El `page` uso como parámetro de ruta con enrutamiento de atributos es un error común. Esto da como resultado un comportamiento incoherente y confuso con la generación de direcciones URL.

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo2Controller.cs?name=snippet)]

La generación de direcciones URL utiliza los nombres de parámetro especiales para determinar si una operación de generación de URL hace referencia a una página de Razor o a un controlador.

<a name="verb"></a>

## <a name="http-verb-templates"></a>Plantillas de verbos HTTP

ASP.NET Core tiene las siguientes plantillas de verbos HTTP:

* [[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)
* [[HttpPost]](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)
* [[HttpPut]](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)
* [[HttpDelete]](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)
* [[HttpHead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)
* [[HttpPatch]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)

<a name="rt"></a>

### <a name="route-templates"></a>Plantillas de ruta

ASP.NET Core tiene las siguientes plantillas de ruta:

* Todas las plantillas de [verbos HTTP](#verb) son plantillas de ruta.
* [[Ruta]](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)

<a name="arx"></a>

### <a name="attribute-routing-with-http-verb-attributes"></a>Enrutamiento de atributos con atributos de verbo Http

Considere el siguiente controlador:

[!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet)]

En el código anterior:

* Cada acción `[HttpGet]` contiene el atributo, que restringe la coincidencia solo con las solicitudes HTTP GET.
* La `GetProduct` acción `"{id}"` incluye la `id` plantilla, por `"api/[controller]"` lo tanto, se anexa a la plantilla en el controlador. La plantilla `"api/[controller]/"{id}""`de métodos es . Por lo tanto, esta acción solo `/api/test2/xyz``/api/test2/123`coincide`/api/test2/{any string}`con las solicitudes GET del formulario , , , etc.
  [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet2)]
* La `GetIntProduct` acción `"int/{id:int}")` contiene la plantilla. La `:int` parte de la `id` plantilla restringe los valores de ruta a cadenas que se pueden convertir en un entero. Una solicitud `/api/test2/int/abc`GET a:
  * No coincide con esta acción.
  * Devuelve un error [404 No encontrado.](https://developer.mozilla.org/docs/Web/HTTP/Status/404)
    [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet3)]
* La `GetInt2Product` acción `{id}` contiene en la plantilla, `id` pero no se restringe a los valores que se pueden convertir en un entero. Una solicitud `/api/test2/int2/abc`GET a:
  * Coincide con esta ruta.
  * El enlace de `abc` modelo no puede convertirse en un entero. El `id` parámetro del método es entero.
  * Devuelve una [solicitud incorrecta 400](https://developer.mozilla.org/docs/Web/HTTP/Status/400) porque`abc` el enlace de modelo no se pudo convertir en un entero.
      [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet4)]

El enrutamiento <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> de atributos <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute>puede <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute>utilizar atributos como <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, , y . Todos los atributos del [verbo HTTP](#verb) aceptan una plantilla de ruta. En el ejemplo siguiente se muestran dos acciones que coinciden con la misma plantilla de ruta:

[!code-csharp[](routing/samples/3.x/main/Controllers/MyProductsController.cs?name=snippet1)]

Uso de `/products3`la ruta URL:

* La `MyProductsController.ListProducts` acción se ejecuta `GET`cuando el verbo [HTTP](#verb) es .
* La `MyProductsController.CreateProduct` acción se ejecuta `POST`cuando el verbo [HTTP](#verb) es .

Al crear una API de REST, es raro `[Route(...)]` que necesite usar en un método de acción porque la acción acepta todos los métodos HTTP. Es mejor usar el atributo de [verbo HTTP](#verb) más específico para ser preciso sin lo que admite la API. Se espera que los clientes de API de REST sepan qué rutas y verbos HTTP se asignan a determinadas operaciones lógicas.

Las API de REST deben usar el enrutamiento de atributos para modelar la funcionalidad de la aplicación como un conjunto de recursos donde las operaciones se representan mediante verbos HTTP. Esto significa que muchas operaciones, por ejemplo, GET y POST en el mismo recurso lógico utilizan la misma dirección URL. El enrutamiento mediante atributos proporciona un nivel de control que es necesario para diseñar cuidadosamente un diseño de puntos de conexión públicos de la API.

Puesto que una ruta de atributo se aplica a una acción específica, es fácil crear parámetros necesarios como parte de la definición de plantilla de ruta. En el ejemplo `id` siguiente, es necesario como parte de la ruta de acceso de url:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

La `Products2ApiController.GetProduct(int)` acción:

* Se ejecuta con la ruta de URL como`/products2/3`
* No se ejecuta con `/products2`la ruta de acceso url .

El atributo [[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) permite que una acción limite los tipos de contenido de la solicitud compatibles. Para obtener más información, vea Definir tipos de contenido de [solicitud admitidos con el atributo Consumes](xref:web-api/index#consumes).

 Consulte [Enrutamiento](xref:fundamentals/routing) para obtener una descripción completa de las plantillas de ruta y las opciones relacionadas.

Para obtener `[ApiController]`más información sobre , vea [Atributo ApiController](xref:web-api/index##apicontroller-attribute).

## <a name="route-name"></a>Nombre de ruta

El código siguiente define un nombre de ruta de `Products_List`:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

Los nombres de ruta se pueden utilizar para generar una dirección URL basada en una ruta específica. Nombres de ruta:

* No tenga ningún impacto en el comportamiento de coincidencia de URL del enrutamiento.
* Solo se utilizan para la generación de URL.

Los nombres de ruta deben ser únicos en toda la aplicación.

Contraste el código anterior con la ruta `id` predeterminada convencional,`{id?}`que define el parámetro como opcional ( ). La capacidad de especificar con precisión las `/products` API `/products/5` tiene ventajas, como permitir y enviar a diferentes acciones.

<a name="routing-combining-ref-label"></a>

## <a name="combining-attribute-routes"></a>Combinación de rutas de atributos

Para que el enrutamiento mediante atributos sea menos repetitivo, los atributos de ruta del controlador se combinan con los atributos de ruta de las acciones individuales. Las plantillas de ruta definidas en el controlador se anteponen a las plantillas de ruta de las acciones. La colocación de un atributo de ruta en el controlador hace que **todas** las acciones del controlador usen el enrutamiento mediante atributos.

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet)]

En el ejemplo anterior:

* La ruta `/products` URL puede coincidir`ProductsApi.ListProducts`
* La ruta `/products/5` de `ProductsApi.GetProduct(int)`acceso URL puede coincidir con .

Ambas acciones solo coinciden `GET` con HTTP porque `[HttpGet]` están marcadas con el atributo.

Las plantillas de ruta aplicadas a una acción que comienzan por `/` o `~/` no se combinan con las plantillas de ruta que se aplican al controlador. En el ejemplo siguiente se hace coincidir un conjunto de rutas de URL similares a la ruta predeterminada.

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet)]

En la tabla `[Route]` siguiente se explican los atributos del código anterior:

| Atributo               | Combina con`[Route("Home")]` | Define la plantilla de ruta |
| ----------------- | ------------ | --------- |
| `[Route("")]` | Sí | `"Home"` |
| `[Route("Index")]` | Sí | `"Home/Index"` |
| `[Route("/")]` | **No** | `""` |
| `[Route("About")]` | Sí | `"Home/About"` |

<a name="routing-ordering-ref-label"></a>
<a name="oar"></a>

### <a name="attribute-route-order"></a>Orden de ruta de atributos

El enrutamiento crea un árbol y coincide con todos los puntos finales simultáneamente:

* Las entradas de ruta se comportan como si estuvieran colocadas en un pedido ideal.
* Las rutas más específicas tienen la oportunidad de ejecutarse antes que las rutas más generales.

Por ejemplo, una `blog/search/{topic}` ruta de atributo como `blog/{*article}`es más específica que una ruta de atributo como . La `blog/search/{topic}` ruta tiene mayor prioridad, de forma predeterminada, porque es más específica. Mediante el [enrutamiento convencional,](#cr)el desarrollador es responsable de colocar las rutas en el orden deseado.

Las rutas de atributos <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order> pueden configurar un pedido mediante la propiedad. Todos los [atributos](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) de `Order` ruta proporcionados por el marco de trabajo incluyen . Las rutas se procesan de acuerdo con el orden ascendente de la propiedad `Order`. El orden predeterminado es `0`. Establecer una `Order = -1` ruta mediante corridas antes de las rutas que no establecen un pedido. Establecer una `Order = 1` ruta mediante ejecuciones después del orden de ruta predeterminado.

**Evite** dependiendo `Order`de . Si el espacio URL de una aplicación requiere valores de orden explícitos para enrutar correctamente, es probable que también sea confuso para los clientes. En general, el enrutamiento de atributos selecciona la ruta correcta con coincidencia de URL. Si el orden predeterminado utilizado para la generación de direcciones URL no funciona, `Order` usar un nombre de ruta como invalidación suele ser más sencillo que aplicar la propiedad.

Considere los dos controladores siguientes `/home`que definen la coincidencia de ruta:

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

Al `/home` solicitar con el código anterior se produce una excepción similar a la siguiente:

```text
AmbiguousMatchException: The request matched multiple endpoints. Matches:

 WebMvcRouting.Controllers.HomeController.Index
 WebMvcRouting.Controllers.MyDemoController.MyIndex
```

Agregar `Order` a uno de los atributos de ruta resuelve la ambiguedad:

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo3Controller.cs?name=snippet3& highlight=2)]

Con el código `/home` anterior, `HomeController.Index` ejecuta el punto de conexión. Para llegar `MyDemoController.MyIndex`a `/home/MyIndex`la solicitud , . **Nota**:

* El código anterior es un ejemplo o un diseño de enrutamiento deficiente. Se utilizó para `Order` ilustrar la propiedad.
* La `Order` propiedad solo resuelve la ambiguedad, esa plantilla no se puede hacer coincidir. Sería mejor eliminar la `[Route("Home")]` plantilla.

Consulte Rutas de [Razor Pages y convenciones](xref:razor-pages/razor-pages-conventions#route-order) de aplicaciones: Orden de ruta para obtener información sobre el orden de las rutas con Razor Pages.

En algunos casos, se devuelve un error HTTP 500 con rutas ambiguas. Utilice el [registro](xref:fundamentals/logging/index) para ver `AmbiguousMatchException`qué puntos de conexión causaron el archivo .

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a>Reemplazo de tokens en plantillas de ruta [controlador], [acción], [área]

Para mayor comodidad, las rutas de atributos admiten el reemplazo de tokens para parámetros de ruta reservados mediante la encomienda un token en uno de los siguientes elementos:

* Llaves cuadradas:`[]`
* Llaves rizadas:`{}`

Los `[action]`tokens `[area]`, `[controller]` , y se reemplazan por los valores del nombre de la acción, el nombre del área y el nombre del controlador de la acción donde se define la ruta:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet)]

En el código anterior:

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet10)]

  * Partidos`/Products0/List`

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet11)]

  * Partidos`/Products0/Edit/{id}`

El reemplazo del token se produce como último paso de la creación de las rutas de atributo. El ejemplo anterior se comporta igual que el código siguiente:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet20)]

[!INCLUDE[](~/includes/MTcomments.md)]

Las rutas de atributo también se pueden combinar con la herencia. Esto es potente combinado con el reemplazo de tokens. El reemplazo de token también se aplica a los nombres de ruta definidos por las rutas de atributo.
`[Route("[controller]/[action]", Name="[controller]_[action]")]`genera un nombre de ruta único para cada acción:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet5)]

El reemplazo de token también se aplica a los nombres de ruta definidos por las rutas de atributo.
`[Route("[controller]/[action]", Name="[controller]_[action]")]`
 genera un nombre de ruta único para cada acción.

Para que el delimitador de reemplazo de token `[` o `]` coincida, repita el carácter (`[[` o `]]`) para usarlo como secuencia de escape.

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a>Usar un transformador de parámetro para personalizar el reemplazo de tokens

El reemplazo de tokens se puede personalizarse mediante un transformador de parámetro. Un transformador de parámetro implementa <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> y transforma el valor de parámetros. Por ejemplo, `SlugifyParameterTransformer` un transformador `SubscriptionManagement` de parámetros personalizado cambia el valor de ruta a: `subscription-management`

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet2)]

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention> es una convención de modelo de aplicación que:

* Aplica un transformador de parámetro a todas las rutas de atributo en una aplicación.
* Personaliza los valores de token de ruta de atributo que se reemplazan.

[!code-csharp[](routing/samples/3.x/main/Controllers/SubscriptionManagementController.cs?name=snippet)]

El `ListAll` método anterior `/subscription-management/list-all`coincide con .

`RouteTokenTransformerConvention` está registrado como una opción en `ConfigureServices`.

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet)]

Consulte documentos web de [MDN en Slug](https://developer.mozilla.org/docs/Glossary/Slug) para obtener la definición de Slug.

[!INCLUDE[](~/includes/regex.md)]
<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-attribute-routes"></a>Múltiples rutas de atributos

El enrutamiento mediante atributos permite definir varias rutas que llegan a la misma acción. El uso más común de esto es imitar el comportamiento de la ruta convencional predeterminada tal como se muestra en el ejemplo siguiente:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6x)]

Poner varios atributos de ruta en el controlador significa que cada uno se combina con cada uno de los atributos de ruta en los métodos de acción:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6)]

Todas las restricciones de `IActionConstraint`ruta de verbo [HTTP](#verb) implementan .

Cuando se colocan <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> varios atributos de ruta que se implementan en una acción:

* Cada restricción de acción se combina con la plantilla de ruta aplicada al controlador.

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet7)]

El uso de varias rutas en acciones puede parecer útil y eficaz, es mejor mantener el espacio de URL de la aplicación básico y bien definido. Utilice varias rutas en acciones **solo** cuando sea necesario, por ejemplo, para admitir clientes existentes.

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a>Especificación de parámetros opcionales de ruta de atributo, valores predeterminados y restricciones

Las rutas de atributo admiten la misma sintaxis en línea que las rutas convencionales para especificar parámetros opcionales, valores predeterminados y restricciones.

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet8&highlight=3)]

En el código `[HttpPost("product/{id:int}")]` anterior, aplica una restricción de ruta. La `ProductsController.ShowProduct` acción solo se compara `/product/3`con rutas de URL como . La parte `{id:int}` de la plantilla de ruta restringe ese segmento solo a enteros.

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

Consulte [Referencia de plantilla de ruta](xref:fundamentals/routing#route-template-reference) para obtener una descripción detallada de la sintaxis de la plantilla de ruta.

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a>Atributos de ruta personalizados mediante IRouteTemplateProvider

Todos los [atributos](#rt) de ruta implementan <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider>. El tiempo de ejecución de ASP.NET Core:

* Busca atributos en las clases de controlador y los métodos de acción cuando se inicia la aplicación.
* Utiliza los atributos que se implementan `IRouteTemplateProvider` para crear el conjunto inicial de rutas.

Implementar `IRouteTemplateProvider` para definir atributos de ruta personalizados. Cada `IRouteTemplateProvider` le permite definir una única ruta con una plantilla de ruta, un orden y un nombre personalizados:

[!code-csharp[](routing/samples/3.x/main/Controllers/MyTestApiController.cs?name=snippet&highlight=1-10)]

El `Get` método anterior `Order = 2, Template = api/MyTestApi`devuelve .

<a name="routing-app-model-ref-label"></a>

### <a name="use-application-model-to-customize-attribute-routes"></a>Utilice el modelo de aplicación para personalizar las rutas de atributos

El modelo de aplicación:

* Es un modelo de objetos creado al inicio.
* Contiene todos los metadatos utilizados por ASP.NET Core para enrutar y ejecutar las acciones en una aplicación.

El modelo de aplicación incluye todos los datos recopilados de los atributos de ruta. La `IRouteTemplateProvider` implementación proporciona los datos de los atributos de ruta. Convenciones:

* Se puede escribir para modificar el modelo de aplicación para personalizar el comportamiento del enrutamiento.
* Se leen al inicio de la aplicación.

En esta sección se muestra un ejemplo básico de personalización del enrutamiento mediante el modelo de aplicación. El código siguiente hace que las rutas se alineen aproximadamente con la estructura de carpetas del proyecto.

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet)]

El código siguiente `namespace` impide que la convención se aplique a los controladores que se enrutan por atributos:

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet2)]

Por ejemplo, el siguiente controlador `NamespaceRoutingConvention`no utiliza:

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/ManagersController.cs?name=snippet&highlight=1)]

El método `NamespaceRoutingConvention.Apply` realiza las acciones siguientes:

* No hace nada si el controlador es atributo enrutado.
* Establece la plantilla de `namespace`controladores `namespace` en función de la , con la base eliminada.

El `NamespaceRoutingConvention` se puede `Startup.ConfigureServices`aplicar en:

[!code-csharp[](routing/samples/3.x/nsrc/Startup.cs?name=snippet&highlight=1,14-18)]

Por ejemplo, considere el siguiente controlador:

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/UsersController.cs)]

En el código anterior:

* La `namespace` base `My.Application`es .
* El nombre completo del controlador `My.Application.Admin.Controllers.UsersController`anterior es .
* El `NamespaceRoutingConvention` establece la `Admin/Controllers/Users/[action]/{id?`plantilla de controladores en .

El `NamespaceRoutingConvention` también se puede aplicar como un atributo en un regulador:

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/TestController.cs?name=snippet&highlight=1)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a>Enrutamiento mixto: enrutamiento mediante atributos frente a enrutamiento convencional

ASP.NET aplicaciones principales pueden mezclar el uso de enrutamiento convencional y enrutamiento de atributos. Es habitual usar las rutas convencionales para controladores que sirven páginas HTML para los exploradores, y el enrutamiento mediante atributos para los controladores que sirven las API de REST.

Las acciones se enrutan bien mediante convención o bien mediante atributos. Colocar una ruta en el controlador o la acción hace que se enrute mediante atributos. Las acciones que definen rutas de atributo no se pueden alcanzar a través de las rutas convencionales y viceversa. **Cualquier** atributo de ruta en el controlador realiza **todas las** acciones en el atributo de controlador enrutado.

El enrutamiento de atributos y el enrutamiento convencional utilizan el mismo motor de enrutamiento.

<a name="routing-url-gen-ref-label"></a>
<a name="ambient"></a>

## <a name="url-generation-and-ambient-values"></a>Generación de URL y valores ambientales

Las aplicaciones pueden usar características de generación de URL de enrutamiento para generar vínculos URL a acciones. La generación de direcciones URL elimina las direcciones URL de codificación rígida, lo que hace que el código sea más robusto y mantenible. Esta sección se centra en las características de generación de direcciones URL proporcionadas por MVC y solo cubren los conceptos básicos de cómo funciona la generación de direcciones URL. Consulte [Enrutamiento](xref:fundamentals/routing) para obtener una descripción detallada de la generación de direcciones URL.

La <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> interfaz es el elemento subyacente de la infraestructura entre MVC y enrutamiento para la generación de direcciones URL. Una instancia `IUrlHelper` de está `Url` disponible a través de la propiedad en controladores, vistas y componentes de vista.

En el ejemplo `IUrlHelper` siguiente, la `Controller.Url` interfaz se utiliza a través de la propiedad para generar una dirección URL a otra acción.

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

Si la aplicación utiliza la ruta convencional `url` predeterminada, el valor `/UrlGeneration/Destination`de la variable es la cadena de ruta de acceso URL. Esta ruta de acceso URL se crea mediante el enrutamiento combinando:

* Los valores de ruta de la solicitud actual, que se denominan **valores ambiente.**
* Los valores `Url.Action` pasados y sustituyendo esos valores en la plantilla de ruta:

``` text
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

El valor de cada uno de los parámetros de ruta incluidos en la plantilla de ruta se sustituye por nombres que coincidan con los valores y los valores de ambiente. Un parámetro de ruta que no tiene un valor puede:

* Utilice un valor predeterminado si tiene uno.
* Se omitirá si es opcional. Por ejemplo, `id` la plantilla `{controller}/{action}/{id?}`de ruta .

Se produce un error en la generación de URL si cualquier parámetro de ruta necesario no tiene un valor correspondiente. Si se produce un error en la generación de direcciones URL para una ruta, se prueba con la ruta siguiente hasta que se hayan probado todas las rutas o se encuentra una coincidencia.

El ejemplo anterior `Url.Action` de assumes [conventional routing](#cr). La generación de URL funciona de forma similar con el enrutamiento de [atributos,](#ar)aunque los conceptos son diferentes. Con enrutamiento convencional:

* Los valores de ruta se utilizan para expandir una plantilla.
* Los valores `controller` de `action` ruta para y su ella suelen aparecer en esa plantilla. Esto funciona porque las direcciones URL coincidentes con el enrutamiento se adhieren a una convención.

En el ejemplo siguiente se utiliza el enrutamiento de atributos:

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationAttrController.cs?name=snippet_1)]

La `Source` acción del código `custom/url/to/destination`anterior genera .

<xref:Microsoft.AspNetCore.Routing.LinkGenerator>se añadió en ASP.NET Core 3.0 como alternativa a `IUrlHelper`. `LinkGenerator`ofrece una funcionalidad similar pero más flexible. Cada método `IUrlHelper` en tiene una `LinkGenerator` familia correspondiente de métodos en, así.

### <a name="generating-urls-by-action-name"></a>Generación de direcciones URL por nombre de acción

[Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), [LinkGenerator.GetPathByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*)y todas las sobrecargas relacionadas están diseñadas para generar el punto de conexión de destino especificando un nombre de controlador y un nombre de acción.

Cuando `Url.Action`se utiliza , `controller` los `action` valores de ruta actuales para y son proporcionados por el tiempo de ejecución:

* El valor `controller` `action` de y son parte de los valores y valores [ambientales.](#ambient) El `Url.Action` método siempre utiliza `action` los `controller` valores actuales de y genera una ruta de acceso URL que enruta a la acción actual.

El enrutamiento intenta usar los valores de los valores ambientales para rellenar la información que no se proporcionó al generar una dirección URL. Considere una `{a}/{b}/{c}/{d}` ruta como `{ a = Alice, b = Bob, c = Carol, d = David }`con los valores ambientales:

* El enrutamiento tiene suficiente información para generar una dirección URL sin valores adicionales.
* El enrutamiento tiene suficiente información porque todos los parámetros de ruta tienen un valor.

Si se `{ d = Donovan }` añade el valor:

* El `{ d = David }` valor se omite.
* La ruta de `Alice/Bob/Carol/Donovan`URL generada es .

**Advertencia:** las rutas de URL son jerárquicas. En el ejemplo anterior, `{ c = Cheryl }` si se agrega el valor:

* Se omiten `{ c = Carol, d = David }` ambos valores.
* Ya no hay `d` un valor para y se produce un error en la generación de url.
* Los valores `c` deseados de y `d` deben especificarse para generar una dirección URL.  

Usted puede ser que espere golpear `{controller}/{action}/{id?}`este problema con la ruta predeterminada. Este problema es raro `Url.Action` en la práctica `controller` `action` porque siempre especifica explícitamente a y valor.

Varias sobrecargas de [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) toman un objeto de valores `controller` `action`de ruta para proporcionar valores para parámetros de ruta distintos de y . El objeto de valores de `id`ruta se utiliza con frecuencia con . Por ejemplo, `Url.Action("Buy", "Products", new { id = 17 })`. Objeto de valores de ruta:

* Por convención suele ser un objeto de tipo anónimo.
* Puede ser `IDictionary<>` un poco o un [POCO](https://wikipedia.org/wiki/Plain_old_CLR_object)).

Los valores de ruta adicionales que no coinciden con los parámetros de ruta se colocan en la cadena de consulta.

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet)]

El código anterior `/Products/Buy/17?color=red`genera .

El código siguiente genera una dirección URL absoluta:

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet2)]

Para crear una dirección URL absoluta, utilice una de las siguientes opciones:

* Una sobrecarga que `protocol`acepta un archivo . Por ejemplo, el código anterior.
* [LinkGenerator.GetUriByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*), que genera URI absolutos de forma predeterminada.

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generate-urls-by-route"></a>Generar URL por ruta

El código anterior demostró la generación de una dirección URL pasando el controlador y el nombre de la acción. `IUrlHelper`también proporciona la familia de métodos [Url.RouteUrl.](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*) Estos métodos son similares a [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), pero `action` `controller` no copian los valores actuales de y a los valores de ruta. El uso más `Url.RouteUrl`común de :

* Especifica un nombre de ruta para generar la dirección URL.
* Por lo general, no especifica un controlador o un nombre de acción.

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGeneration2Controller.cs?name=snippet_1)]

El siguiente archivo Razor genera `Destination_Route`un enlace HTML a:

[!code-cshtml[](routing/samples/3.x/main/Views/Shared/MyLink.cshtml)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generate-urls-in-html-and-razor"></a>Generar URLs en HTML y Razor

<xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper>proporciona <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper> los métodos [Html.BeginForm](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) y `<form>` [Html.ActionLink](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) para generar y `<a>` elementos respectivamente. Estos métodos usan el [método Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) para generar una dirección URL y aceptan argumentos similares. Los métodos `Url.RouteUrl` complementarios de `HtmlHelper` son `Html.BeginRouteForm` y `Html.RouteLink`, cuya funcionalidad es similar.

Las TagHelper generan direcciones URL a través de la TagHelper `form` y la TagHelper `<a>`. Ambos usan `IUrlHelper` para su implementación. Consulte [Aplicaciones auxiliares](xref:mvc/views/working-with-forms) de etiquetas en formularios para obtener más información.

Dentro de las vistas, `IUrlHelper` está disponible a través de la propiedad `Url` para una generación de direcciones URL ad hoc no cubierta por los pasos anteriores.

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="url-generation-in-action-results"></a>Generación de URL en los resultados de la acción

Los ejemplos anteriores `IUrlHelper` mostraban el uso en un controlador. El uso más común en un controlador es generar una dirección URL como parte de un resultado de acción.

Las clases base <xref:Microsoft.AspNetCore.Mvc.ControllerBase> y <xref:Microsoft.AspNetCore.Mvc.Controller> proporcionan métodos de conveniencia para los resultados de acción que hacen referencia a otra acción. Un uso típico es redirigir después de aceptar la entrada del usuario:

[!code-csharp[](routing/samples/3.x/main/Controllers/CustomerController.cs?name=snippet)]

La acción da como <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction*> <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> y sigue un patrón `IUrlHelper`similar a los métodos de .

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a>Caso especial para rutas convencionales dedicadas

[El enrutamiento convencional](#cr) puede utilizar un tipo especial de definición de ruta denominada [ruta convencional dedicada.](#dcr) En el ejemplo siguiente, `blog` la ruta denominada es una ruta convencional dedicada:

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

Utilizando las definiciones `Url.Action("Index", "Home")` de ruta `/` anteriores, genera la ruta URL mediante la `default` ruta, pero ¿por qué? Se puede suponer que los valores de ruta `{ controller = Home, action = Index }` son suficientes para generar una dirección URL utilizando `blog`, con el resultado `/blog?action=Index&controller=Home`.

[Las rutas convencionales dedicadas](#dcr) se basan en un comportamiento especial de valores predeterminados que no tienen un parámetro de ruta correspondiente que impide que la ruta sea demasiado [expansiva](xref:fundamentals/routing#greedy) con la generación de URL. En este caso, los valores predeterminados son `{ controller = Blog, action = Article }`, y ni `controller` ni `action` aparecen como un parámetro de ruta. Cuando el enrutamiento realiza la generación de direcciones URL, los valores proporcionados deben coincidir con los valores predeterminados. Se produce `blog` un error `{ controller = Home, action = Index }` en la `{ controller = Blog, action = Article }`generación de URL mediante porque los valores no coinciden con . Después, el enrutamiento vuelve para probar `default`, operación que se realiza correctamente.

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a>Áreas

[Las áreas](xref:mvc/controllers/areas) son una característica MVC utilizada para organizar la funcionalidad relacionada en un grupo como un independiente:

* Espacio de nombres de enrutamiento para acciones de controlador.
* Estructura de carpetas para vistas.

El uso de áreas permite que una aplicación tenga varios controladores con el mismo nombre, siempre y cuando tengan áreas diferentes. El uso de áreas crea una jerarquía para el enrutamiento mediante la adición de otro parámetro de ruta, `area`, a `controller` y `action`. En esta sección se describe cómo interactúa el enrutamiento con las áreas. Consulte [Areas](xref:mvc/controllers/areas) para obtener más información sobre cómo se utilizan las áreas con las vistas.

En el ejemplo siguiente se configura MVC `area` para utilizar `area` `Blog`la ruta convencional predeterminada y una ruta para un nombre:

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

En el código <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> anterior, se `"blog_route"`llama para crear el archivo . El segundo `"Blog"`parámetro, , es el nombre del área.

Al hacer coincidir `/Manage/Users/AddUser`una `"blog_route"` ruta URL como `{ area = Blog, controller = Users, action = AddUser }`, la ruta genera los valores de ruta. El `area` valor de ruta se produce `area`mediante un valor predeterminado para . La ruta `MapAreaControllerRoute` creada por es equivalente a la siguiente:

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup2.cs?name=snippet2)]

`MapAreaControllerRoute` utiliza el nombre de área proporcionado, que en este caso es `Blog`, para crear una ruta con un valor predeterminado y una restricción para `area`. El valor predeterminado garantiza que la ruta siempre produce `{ area = Blog, ... }`; la restricción requiere el valor `{ area = Blog, ... }` para la generación de la dirección URL.

El enrutamiento convencional depende del orden. En general, las rutas con áreas deben colocarse antes, ya que son más específicas que las rutas sin un área.

Utilizando el ejemplo anterior, `{ area = Blog, controller = Users, action = AddUser }` los valores de ruta coinciden con la acción siguiente:

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

El atributo [[Area]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) es lo que denota un controlador como parte de un área. Este controlador está `Blog` en el área. Los reguladores sin un `[Area]` atributo no son miembros de ninguna área, y **no** hacen juego cuando el valor de ruta es proporcionado por el `area` ruteo. En el ejemplo siguiente, solo el primer controlador enumerado puede coincidir con los valores de ruta `{ area = Blog, controller = Users, action = AddUser }`.

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

El espacio de nombres de cada controlador se muestra aquí para su integridad. Si los controladores anteriores usan el mismo espacio de nombres, se generaría un error del compilador. Los espacios de nombres de clase no tienen ningún efecto en el enrutamiento de MVC.

Los dos primeros controladores son miembros de las áreas y solo coinciden cuando el valor de ruta `area` proporciona su respectivo nombre de área. El tercer controlador no es miembro de ningún área y solo puede coincidir cuando el enrutamiento no proporciona ningún valor para `area`.

<a name="aa"></a>

En términos de búsqueda de coincidencias de *ningún valor*, la ausencia del valor `area` es igual que si el valor de `area` fuese null o una cadena vacía.

Al ejecutar una acción dentro de un `area` área, el valor de ruta para está disponible como [un valor ambiente](#ambient) para el enrutamiento que se usará para la generación de direcciones URL. Esto significa que, de forma predeterminada, las áreas actúan de forma *adhesiva* para la generación de direcciones URL, tal como se muestra en el ejemplo siguiente.

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup3.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

El código siguiente genera `/Zebra/Users/AddUser`una dirección URL a:

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/HomeController.cs?name=snippet)]

<a name="action"></a>

## <a name="action-definition"></a>Definición de acción

Los métodos públicos en un controlador, excepto aquellos con el [nonAction](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) atributo, son acciones.

## <a name="sample-code"></a>Código de ejemplo

 * El [método MyDisplayRouteInfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) se incluye en la descarga de [ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) y se usa para mostrar información de enrutamiento.
* [Ver o descargar código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) ( cómo[descargar](xref:index#how-to-download-a-sample))

[!INCLUDE[](~/includes/dbg-route.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core MVC utiliza el [middleware](xref:fundamentals/middleware/index) de enrutamiento para buscar las direcciones URL de las solicitudes entrantes y asignarlas a acciones. Las rutas se definen en el código de inicio o los atributos. Las rutas describen cómo se deben asociar las rutas de dirección URL a las acciones. Las rutas también se usan para generar direcciones URL (para vínculos) enviadas en las respuestas.

Las acciones se enrutan bien mediante convención o bien mediante atributos. Colocar una ruta en el controlador o la acción hace que se enrute mediante atributos. Consulte [Enrutamiento mixto](#routing-mixed-ref-label) para obtener más información.

Este documento explica las interacciones entre MVC y el enrutamiento, así como el uso que las aplicaciones MVC suelen hacer de las características de enrutamiento. Consulte [Enrutamiento](xref:fundamentals/routing) para obtener más información sobre enrutamiento avanzado.

## <a name="setting-up-routing-middleware"></a>Configurar el middleware de enrutamiento

En su método *Configure* puede ver código similar al siguiente:

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

Dentro de la llamada a `UseMvc`, `MapRoute` se utiliza para crear una ruta única, a la que nos referiremos como la ruta `default`. La mayoría de las aplicaciones MVC usarán una ruta con una plantilla similar a la ruta `default`.

La plantilla de ruta `"{controller=Home}/{action=Index}/{id?}"` puede coincidir con una ruta de dirección URL como `/Products/Details/5` y extraerá los valores de ruta `{ controller = Products, action = Details, id = 5 }` mediante la conversión de la ruta en tokens. MVC intentará encontrar un controlador denominado `ProductsController` y ejecutar la acción `Details`:

```csharp
public class ProductsController : Controller
{
   public IActionResult Details(int id) { ... }
}
```

Tenga en cuenta que, en este ejemplo, el enlace de modelos usaría el valor de `id = 5` para establecer el parámetro `id` en `5` al invocar esta acción. Consulte [Enlace de modelos](../models/model-binding.md) para obtener más detalles.

Utilizando la ruta `default`:

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

La plantilla de ruta:

* `{controller=Home}` define `Home` como el valor `controller` predeterminado

* `{action=Index}` define `Index` como el valor `action` predeterminado

* `{id?}` define `id` como opcional

No es necesario que los parámetros de ruta opcionales y predeterminados estén presentes en la ruta de dirección URL para una coincidencia. Consulte [Referencia de plantilla de ruta](xref:fundamentals/routing#route-template-reference) para obtener una descripción detallada de la sintaxis de la plantilla de ruta.

`"{controller=Home}/{action=Index}/{id?}"` puede coincidir con la ruta de dirección URL `/` y generará los valores de ruta `{ controller = Home, action = Index }`. Los valores de `controller` y `action` utilizan los valores predeterminados, `id` no genera un valor porque no hay ningún segmento correspondiente en la ruta de dirección URL. MVC utilizaría estos valores de ruta para seleccionar `HomeController` y la acción `Index`:

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

Usando esta definición de controlador y la plantilla de ruta, la acción `HomeController.Index` se ejecutaría para cualquiera de las rutas de dirección URL siguientes:

* `/Home/Index/17`

* `/Home/Index`

* `/Home`

* `/`

El método de conveniencia `UseMvcWithDefaultRoute`:

```csharp
app.UseMvcWithDefaultRoute();
```

Se puede usar para reemplazar:

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

`UseMvc` y `UseMvcWithDefaultRoute` agregan una instancia de `RouterMiddleware` a la canalización de middleware. MVC no interactúa directamente con middleware y usa el enrutamiento para controlar las solicitudes. MVC está conectado a las rutas a través de una instancia de `MvcRouteHandler`. El código dentro de `UseMvc` es similar al siguiente:

```csharp
var routes = new RouteBuilder(app);

// Add connection to MVC, will be hooked up by calls to MapRoute.
routes.DefaultHandler = new MvcRouteHandler(...);

// Execute callback to register routes.
// routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");

// Create route collection and add the middleware.
app.UseRouter(routes.Build());
```

`UseMvc` no define directamente ninguna ruta, sino que agrega un marcador de posición a la colección de rutas para la ruta `attribute`. La sobrecarga `UseMvc(Action<IRouteBuilder>)` le permite agregar sus propias rutas y también admite el enrutamiento mediante atributos.  `UseMvc` y todas sus variaciones agregan un marcador de posición para la ruta de atributo (el enrutamiento mediante atributos siempre está disponible, independientemente de cómo configure `UseMvc`). `UseMvcWithDefaultRoute` define una ruta predeterminada y admite el enrutamiento mediante atributos. La sección [Enrutamiento mediante atributos](#attribute-routing-ref-label) incluye más detalles sobre este tipo de enrutamiento.

<a name="routing-conventional-ref-label"></a>

## <a name="conventional-routing"></a>Enrutamiento convencional

La ruta `default`:

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

El código anterior es un ejemplo de un enrutamiento convencional. Este estilo se denomina enrutamiento convencional porque establece una *convención* para las rutas de URL:

* El primer segmento de ruta se asigna al nombre del controlador.
* El segundo se asigna al nombre de la acción.
* El tercer segmento se `id`utiliza para un archivo . `id`se asigna a una entidad de modelo.

Mediante esta ruta `default`, la ruta de dirección URL `/Products/List` se asigna a la acción `ProductsController.List`, y `/Blog/Article/17` se asigna a `BlogController.Article`. Esta asignación **solo** se basa en los nombres de acción y controlador, y no en espacios de nombres, ubicaciones de archivos de origen ni parámetros de método.

> [!TIP]
> Usar el enrutamiento convencional con la ruta predeterminada permite generar rápidamente la aplicación sin necesidad de elaborar un nuevo patrón de dirección URL para cada acción que se define. Para una aplicación con acciones de estilo CRUD, la coherencia en las direcciones URL en todos los controladores puede ayudar a simplificar el código y hacer que la interfaz de usuario sea más predecible.

> [!WARNING]
> `id` se define como opcional mediante la plantilla de ruta, lo que significa que las acciones se pueden ejecutar sin el identificador proporcionado como parte de la dirección URL. Normalmente lo que ocurrirá si `id` se omite de la dirección URL es que el enlace de modelos establecerá en `0` su valor y, como resultado, no se encontrará ninguna entidad en la base de datos que coincida con `id == 0`. El enrutamiento mediante atributos proporciona un mayor control que permite requerir el identificador para algunas acciones y para otras no. Por convención, la documentación incluirá parámetros opcionales como `id` cuando sea más probable que su uso sea correcto.

## <a name="multiple-routes"></a>Varias rutas

Para agregar varias rutas dentro de `UseMvc`, agregue más llamadas a `MapRoute`. Hacerlo le permite definir varias convenciones o agregar rutas convencionales que se dedican a una acción específica, como en el ejemplo siguiente:

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
            defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

Aquí, la ruta `blog` es una *ruta convencional dedicada*, lo que significa que utiliza el sistema de enrutamiento convencional, pero que está dedicada a una acción específica. Puesto que `controller` y `action` no aparecen en la plantilla de ruta como parámetros, solo pueden tener los valores predeterminados y, por tanto, esta ruta siempre se asignará a la acción `BlogController.Article`.

Las rutas de la colección de rutas están ordenadas y se procesan en el orden en que se hayan agregado. Por tanto, la ruta `blog` de este ejemplo se intentará antes que la ruta `default`.

> [!NOTE]
> *Las rutas convencionales dedicadas* a menudo `{*article}` utilizan parámetros de ruta **catch-all** como para capturar la parte restante de la ruta URL. Esto puede hacer que la ruta sea "demasiado expansiva", lo que significa que coincidirá con direcciones URL que se pretendía que coincidieran con otras rutas. Coloque las rutas "expansivas" más adelante en la tabla de rutas para resolver este problema.

### <a name="fallback"></a>Reserva

Como parte del procesamiento de la solicitud, MVC comprobará que los valores de ruta se pueden usar para buscar un controlador y la acción en la aplicación. Si los valores de ruta no coinciden con una acción, entonces la ruta no se considera una coincidencia y se intentará la ruta siguiente. Esto se denomina *reserva*, y se ha diseñado para simplificar los casos donde se superponen rutas convencionales.

### <a name="disambiguating-actions"></a>Eliminar la ambigüedad de acciones

Cuando dos acciones coinciden en el enrutamiento, MVC debe eliminar la ambigüedad para elegir el candidato "recomendado", o de lo contrario se produce una excepción. Por ejemplo:

```csharp
public class ProductsController : Controller
{
   public IActionResult Edit(int id) { ... }

   [HttpPost]
   public IActionResult Edit(int id, Product product) { ... }
}
```

Este controlador define dos acciones que coincidirían con la ruta de dirección URL `/Products/Edit/17` y los datos de ruta `{ controller = Products, action = Edit, id = 17 }`. Se trata de un patrón típico para controladores MVC donde `Edit(int)` muestra un formulario para editar un producto, y `Edit(int, Product)` procesa el formulario expuesto. Para hacer esto posible, MVC necesita seleccionar `Edit(int, Product)` cuando la solicitud es HTTP `POST` y `Edit(int)` cuando el verbo HTTP es cualquier otra cosa.

`HttpPostAttribute` ( `[HttpPost]` ) es una implementación de `IActionConstraint` que solo permitirá que la acción se seleccione cuando el verbo HTTP sea `POST`. La presencia de `IActionConstraint` hace que `Edit(int, Product)` sea una mejor coincidencia que `Edit(int)`, por lo que `Edit(int, Product)` se intentará en primer lugar.

Solo necesitará escribir implementaciones de `IActionConstraint` personalizadas en escenarios especializados, pero es importante comprender el rol de atributos como `HttpPostAttribute` (para otros verbos HTTP se definen atributos similares). En el enrutamiento convencional es común que las acciones utilicen el mismo nombre de acción cuando son parte de un flujo de trabajo `show form -> submit form`. La comodidad de este patrón será más evidente después de revisar la sección [Descripción de IActionConstraint](#understanding-iactionconstraint).

Si coinciden varias rutas y MVC no encuentra una ruta "recomendada", se produce una excepción `AmbiguousActionException`.

<a name="routing-route-name-ref-label"></a>

### <a name="route-names"></a>Nombres de ruta

Las cadenas `"blog"` y `"default"` de los ejemplos siguientes son nombres de ruta:

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
               defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

Los nombres de ruta proporcionan un nombre lógico a la ruta para que la ruta con nombre pueda utilizarse en la generación de direcciones URL. Esto simplifica en gran medida la creación de direcciones URL en los casos en que el orden de las rutas podría complicar la generación de direcciones URL. Los nombres de ruta deben ser únicos en toda la aplicación.

Los nombres de ruta no tienen ningún impacto en la coincidencia de direcciones URL ni en el control de las solicitudes; se utilizan únicamente para la generación de direcciones URL. En [Enrutamiento](xref:fundamentals/routing) se incluye información más detallada sobre la generación de direcciones URL, como la generación de direcciones URL en asistentes específicos de MVC.

<a name="attribute-routing-ref-label"></a>

## <a name="attribute-routing"></a>Enrutamiento mediante atributos

El enrutamiento mediante atributos utiliza un conjunto de atributos para asignar acciones directamente a las plantillas de ruta. En el ejemplo siguiente, `app.UseMvc();` se utiliza en el método `Configure` y no se pasa ninguna ruta. `HomeController` coincidirá con un conjunto de direcciones URL similares a las que coincidirían con la ruta predeterminada `{controller=Home}/{action=Index}/{id?}`:

```csharp
public class HomeController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult Index()
   {
      return View();
   }
   [Route("Home/About")]
   public IActionResult About()
   {
      return View();
   }
   [Route("Home/Contact")]
   public IActionResult Contact()
   {
      return View();
   }
}
```

La acción `HomeController.Index()` se ejecutará para cualquiera de las rutas de dirección URL `/`, `/Home` o `/Home/Index`.

> [!NOTE]
> Este ejemplo resalta una diferencia clave de programación entre el enrutamiento mediante atributos y el enrutamiento convencional. El enrutamiento mediante atributos requiere más entradas para especificar una ruta; la ruta predeterminada convencional controla las rutas de manera más sucinta. Pero el enrutamiento mediante atributos permite (y requiere) un control preciso de las plantillas de ruta que se aplicarán a cada acción.

Con el enrutamiento mediante atributos, el nombre del controlador y los nombres de acción **no** juegan ningún papel en la selección de las acciones. Este ejemplo coincidirá con las mismas direcciones URL que el ejemplo anterior.

```csharp
public class MyDemoController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult MyIndex()
   {
      return View("Index");
   }
   [Route("Home/About")]
   public IActionResult MyAbout()
   {
      return View("About");
   }
   [Route("Home/Contact")]
   public IActionResult MyContact()
   {
      return View("Contact");
   }
}
```

> [!NOTE]
> Las plantillas de ruta anteriores no definen parámetros de ruta para `action`, `area` y `controller`. De hecho, estos parámetros de ruta no se permiten en rutas de atributo. Puesto que la plantilla de ruta ya está asociada a una acción, no tendría sentido analizar el nombre de acción de la dirección URL.

## <a name="attribute-routing-with-httpverb-attributes"></a>Enrutamiento mediante atributos con atributos Http[Verb]

El enrutamiento mediante atributos también puede hacer uso de los atributos `Http[Verb]` como `HttpPostAttribute`. Todos estos atributos pueden aceptar una plantilla de ruta. Este ejemplo muestra dos acciones que coinciden con la misma plantilla de ruta:

```csharp
[HttpGet("/products")]
public IActionResult ListProducts()
{
   // ...
}

[HttpPost("/products")]
public IActionResult CreateProduct(...)
{
   // ...
}
```

Para una ruta de dirección URL como `/products`, la acción `ProductsApi.ListProducts` se ejecutará cuando el verbo HTTP sea `GET` y `ProductsApi.CreateProduct` se ejecutará cuando el verbo HTTP sea `POST`. El enrutamiento mediante atributos primero busca una coincidencia de la dirección URL en el conjunto de plantillas de ruta que se definen mediante atributos de ruta. Una vez que se encuentra una plantilla de ruta que coincide, las restricciones `IActionConstraint` se aplican para determinar qué acciones se pueden ejecutar.

> [!TIP]
> Cuando se compila una API de REST, es poco frecuente que se quiera usar `[Route(...)]` en un método de acción, ya que la acción aceptará todos los métodos HTTP. Es mejor usar `Http*Verb*Attributes` más específicos para precisar lo que es compatible con la API. Se espera que los clientes de API de REST sepan qué rutas y verbos HTTP se asignan a determinadas operaciones lógicas.

Puesto que una ruta de atributo se aplica a una acción específica, es fácil crear parámetros necesarios como parte de la definición de plantilla de ruta. En este ejemplo, se requiere `id` como parte de la ruta de dirección URL.

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

La acción `ProductsApi.GetProduct(int)` se ejecutará para una ruta de dirección URL como `/products/3`, pero no para una ruta de dirección URL como `/products`. Consulte [Enrutamiento](xref:fundamentals/routing) para obtener una descripción completa de las plantillas de ruta y las opciones relacionadas.

## <a name="route-name"></a>Nombre de ruta

El código siguiente define un *nombre de ruta* de `Products_List`:

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

Los nombres de ruta se pueden utilizar para generar una dirección URL basada en una ruta específica. Los nombres de ruta no tienen ningún impacto en el comportamiento de coincidencia de direcciones URL del enrutamiento, y solo se usan para la generación de direcciones URL. Los nombres de ruta deben ser únicos en toda la aplicación.

> [!NOTE]
> Compare esto con la *ruta predeterminada* convencional, que define el parámetro `id` como opcional (`{id?}`). Esta capacidad de especificar con precisión las API tiene sus ventajas, como permitir que `/products` y `/products/5` se envíen a acciones diferentes.

<a name="routing-combining-ref-label"></a>

### <a name="combining-routes"></a>Combinación de rutas

Para que el enrutamiento mediante atributos sea menos repetitivo, los atributos de ruta del controlador se combinan con los atributos de ruta de las acciones individuales. Las plantillas de ruta definidas en el controlador se anteponen a las plantillas de ruta de las acciones. La colocación de un atributo de ruta en el controlador hace que **todas** las acciones del controlador usen el enrutamiento mediante atributos.

```csharp
[Route("products")]
public class ProductsApiController : Controller
{
   [HttpGet]
   public IActionResult ListProducts() { ... }

   [HttpGet("{id}")]
   public ActionResult GetProduct(int id) { ... }
}
```

En este ejemplo, la ruta de dirección URL `/products` puede coincidir con `ProductsApi.ListProducts` y la ruta de dirección URL `/products/5` puede coincidir con `ProductsApi.GetProduct(int)`. Ambas acciones solo coinciden con el método HTTP `GET` porque están marcadas con `HttpGetAttribute`.

Las plantillas de ruta aplicadas a una acción que comienzan por `/` o `~/` no se combinan con las plantillas de ruta que se aplican al controlador. En este ejemplo coinciden un conjunto de rutas de dirección URL similares a la *ruta predeterminada*.

```csharp
[Route("Home")]
public class HomeController : Controller
{
    [Route("")]      // Combines to define the route template "Home"
    [Route("Index")] // Combines to define the route template "Home/Index"
    [Route("/")]     // Doesn't combine, defines the route template ""
    public IActionResult Index()
    {
        ViewData["Message"] = "Home index";
        var url = Url.Action("Index", "Home");
        ViewData["Message"] = "Home index" + "var url = Url.Action; =  " + url;
        return View();
    }

    [Route("About")] // Combines to define the route template "Home/About"
    public IActionResult About()
    {
        return View();
    }   
}
```

<a name="routing-ordering-ref-label"></a>

### <a name="ordering-attribute-routes"></a>Ordenación de rutas de atributo

A diferencia de las rutas convencionales, que se ejecutan en un orden definido, el enrutamiento de atributos crea un árbol y coincide con todas las rutas simultáneamente. Es como si las entradas de ruta se colocasen en un orden ideal; las rutas más específicas tienen la oportunidad de ejecutarse antes que las rutas más generales.

Por ejemplo, una ruta como `blog/search/{topic}` es más específica que una ruta como `blog/{*article}`. Desde un punto de vista lógico, la ruta `blog/search/{topic}` se ejecuta en primer lugar de forma predeterminada, ya que ese es el único orden significativo. En el enrutamiento convencional, el desarrollador es responsable de colocar las rutas en el orden deseado.

Las rutas de atributo pueden configurar un orden mediante la propiedad `Order` de todos los atributos de ruta proporcionados por el marco. Las rutas se procesan de acuerdo con el orden ascendente de la propiedad `Order`. El orden predeterminado es `0`. Si una ruta se configura con `Order = -1`, se ejecutará antes que las rutas que establecen un orden. Si una ruta se configura con `Order = 1`, se ejecutará después del orden de rutas predeterminado.

> [!TIP]
> Evite depender de `Order`. Si su espacio de direcciones URL requiere unos valores de orden explícitos para un enrutamiento correcto, es probable que también sea confuso para los clientes. Por lo general, el enrutamiento mediante atributos seleccionará la ruta correcta con la coincidencia de dirección URL. Si el orden predeterminado que se usa para la generación de direcciones URL no funciona, normalmente es más sencillo utilizar el nombre de ruta como una invalidación que aplicar la propiedad `Order`.

El enrutamiento del controlador de MVC y el enrutamiento de Razor Pages comparten una implementación. La información sobre el orden de la ruta de los temas de Razor Pages se encuentra disponible en [Convenciones de aplicación y de ruta de Razor Pages: Orden de la ruta](xref:razor-pages/razor-pages-conventions#route-order).

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a>Reemplazo de tokens en plantillas de ruta ([controller], [action], [area])

Para mayor comodidad, las rutas de atributos admiten el`[` `]`reemplazo de *tokens* encerrando un token en corchetes ( , ). Los tokens `[action]`, `[area]` y `[controller]` se reemplazan con los valores del nombre de la acción, el nombre del área y el nombre del controlador de la acción donde se define la ruta. En este ejemplo, las acciones coinciden con las rutas de dirección URL, tal como se describe en los comentarios:

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController.cs?range=7-11,13-17,20-22)]

El reemplazo del token se produce como último paso de la creación de las rutas de atributo. El ejemplo anterior se comportará igual que el código siguiente:

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController2.cs?range=7-11,13-17,20-22)]

Las rutas de atributo también se pueden combinar con la herencia. Esto es especialmente eficaz si se combina con el reemplazo de token.

```csharp
[Route("api/[controller]")]
public abstract class MyBaseController : Controller { ... }

public class ProductsController : MyBaseController
{
   [HttpGet] // Matches '/api/Products'
   public IActionResult List() { ... }

   [HttpPut("{id}")] // Matches '/api/Products/{id}'
   public IActionResult Edit(int id) { ... }
}
```

El reemplazo de token también se aplica a los nombres de ruta definidos por las rutas de atributo. `[Route("[controller]/[action]", Name="[controller]_[action]")]` genera un nombre de ruta único para cada acción.

Para que el delimitador de reemplazo de token `[` o `]` coincida, repita el carácter (`[[` o `]]`) para usarlo como secuencia de escape.

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a>Usar un transformador de parámetro para personalizar el reemplazo de tokens

El reemplazo de tokens se puede personalizarse mediante un transformador de parámetro. Un transformador de parámetro implementa `IOutboundParameterTransformer` y transforma el valor de parámetros. Por ejemplo, un transformador de parámetros `SlugifyParameterTransformer` personalizado cambia el valor de ruta `SubscriptionManagement` a `subscription-management`.

`RouteTokenTransformerConvention` es una convención de modelo de aplicación que:

* Aplica un transformador de parámetro a todas las rutas de atributo en una aplicación.
* Personaliza los valores de token de ruta de atributo que se reemplazan.

```csharp
public class SubscriptionManagementController : Controller
{
    [HttpGet("[controller]/[action]")] // Matches '/subscription-management/list-all'
    public IActionResult ListAll() { ... }
}
```

`RouteTokenTransformerConvention` está registrado como una opción en `ConfigureServices`.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc(options =>
    {
        options.Conventions.Add(new RouteTokenTransformerConvention(
                                     new SlugifyParameterTransformer()));
    });
}

public class SlugifyParameterTransformer : IOutboundParameterTransformer
{
    public string TransformOutbound(object value)
    {
        if (value == null) { return null; }

        // Slugify value
        return Regex.Replace(value.ToString(), "([a-z])([A-Z])", "$1-$2").ToLower();
    }
}
```

::: moniker-end


::: moniker range="< aspnetcore-3.0"
<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-routes"></a>Varias rutas

El enrutamiento mediante atributos permite definir varias rutas que llegan a la misma acción. El uso más común de esto es imitar el comportamiento de la *ruta convencional predeterminada* tal como se muestra en el ejemplo siguiente:

```csharp
[Route("[controller]")]
public class ProductsController : Controller
{
   [Route("")]     // Matches 'Products'
   [Route("Index")] // Matches 'Products/Index'
   public IActionResult Index()
}
```

La colocación de varios atributos de ruta en el controlador significa que cada uno de ellos se combinará con cada uno de los atributos de ruta en los métodos de acción.

```csharp
[Route("Store")]
[Route("[controller]")]
public class ProductsController : Controller
{
   [HttpPost("Buy")]     // Matches 'Products/Buy' and 'Store/Buy'
   [HttpPost("Checkout")] // Matches 'Products/Checkout' and 'Store/Checkout'
   public IActionResult Buy()
}
```

Cuando en una acción se colocan varios atributos de ruta (que implementan `IActionConstraint`), cada restricción de acción se combina con la plantilla de ruta del atributo que lo define.

```csharp
[Route("api/[controller]")]
public class ProductsController : Controller
{
   [HttpPut("Buy")]      // Matches PUT 'api/Products/Buy'
   [HttpPost("Checkout")] // Matches POST 'api/Products/Checkout'
   public IActionResult Buy()
}
```

> [!TIP]
> Si bien el uso de varias rutas en acciones puede parecer eficaz, es mejor que el espacio de direcciones URL de la aplicación sea sencillo y esté bien definido. Utilice varias rutas en acciones solo cuando sea necesario, por ejemplo, para admitir a clientes existentes.

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a>Especificación de parámetros opcionales de ruta de atributo, valores predeterminados y restricciones

Las rutas de atributo admiten la misma sintaxis en línea que las rutas convencionales para especificar parámetros opcionales, valores predeterminados y restricciones.

```csharp
[HttpPost("product/{id:int}")]
public IActionResult ShowProduct(int id)
{
   // ...
}
```

Consulte [Referencia de plantilla de ruta](xref:fundamentals/routing#route-template-reference) para obtener una descripción detallada de la sintaxis de la plantilla de ruta.

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a>Atributos de ruta personalizados con `IRouteTemplateProvider`

Todos los atributos de ruta proporcionados en el marco (`[Route(...)]`, `[HttpGet(...)]`, etc.) implementan la interfaz `IRouteTemplateProvider`. Al iniciarse la aplicación, MVC busca atributos en las clases de controlador y los métodos de acción, y usa los que implementan `IRouteTemplateProvider` para generar el conjunto inicial de rutas.

Implemente `IRouteTemplateProvider` para definir sus propios atributos de ruta. Cada `IRouteTemplateProvider` le permite definir una única ruta con una plantilla de ruta, un orden y un nombre personalizados:

```csharp
public class MyApiControllerAttribute : Attribute, IRouteTemplateProvider
{
   public string Template => "api/[controller]";

   public int? Order { get; set; }

   public string Name { get; set; }
}
```

El atributo del ejemplo anterior establece automáticamente `Template` en `"api/[controller]"` cuando se aplica `[MyApiController]`.

<a name="routing-app-model-ref-label"></a>

### <a name="using-application-model-to-customize-attribute-routes"></a>Uso del modelo de aplicación para personalizar las rutas de atributo

El *modelo de aplicación* es un modelo de objetos creado durante el inicio con todos los metadatos utilizados por MVC para enrutar y ejecutar las acciones. El *modelo de aplicación* incluye todos los datos recopilados de los atributos de ruta (a través de `IRouteTemplateProvider`). Puede escribir *convenciones* para modificar el modelo de aplicación en el momento del inicio y personalizar el comportamiento del enrutamiento. En esta sección se muestra un ejemplo sencillo de personalización del enrutamiento mediante el modelo de aplicación.

[!code-csharp[](routing/samples/2.x/main/NamespaceRoutingConvention.cs)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a>Enrutamiento mixto: enrutamiento mediante atributos frente a enrutamiento convencional

Las aplicaciones MVC pueden combinar el uso de enrutamiento convencional y enrutamiento mediante atributos. Es habitual usar las rutas convencionales para controladores que sirven páginas HTML para los exploradores, y el enrutamiento mediante atributos para los controladores que sirven las API de REST.

Las acciones se enrutan bien mediante convención o bien mediante atributos. Colocar una ruta en el controlador o la acción hace que se enrute mediante atributos. Las acciones que definen rutas de atributo no se pueden alcanzar a través de las rutas convencionales y viceversa. **Cualquier** atributo de ruta en el controlador hace que todas las acciones del controlador se enruten mediante atributos.

> [!NOTE]
> Lo que distingue los dos tipos de sistemas de enrutamiento es el proceso que se aplica después de que una dirección URL coincida con una plantilla de ruta. En el enrutamiento convencional, los valores de ruta de la coincidencia se usan para elegir la acción y el controlador en una tabla de búsqueda de todas las acciones enrutadas convencionales. En el enrutamiento mediante atributos, cada plantilla ya está asociada a una acción y no es necesaria ninguna otra búsqueda.

## <a name="complex-segments"></a>Segmentos complejos

Los segmentos complejos (por ejemplo, `[Route("/dog{token}cat")]`), se procesan buscando coincidencias de literales de derecha a izquierda de un modo no expansivo. Para ver una descripción, eche un vistazo al [código fuente](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296). Para más información, vea [este problema](https://github.com/dotnet/AspNetCore.Docs/issues/8197).

<a name="routing-url-gen-ref-label"></a>

## <a name="url-generation"></a>Generación de direcciones URL

Las aplicaciones MVC pueden usar características de generación de direcciones URL de enrutamiento para generar vínculos URL a las acciones. La generación de direcciones URL elimina las direcciones URL codificadas de forma rígida, por lo que el código es más compacto y fácil de mantener. Esta sección se centra en las características de generación de direcciones URL proporcionadas por MVC y solo aborda los conceptos básicos de su funcionamiento. Consulte [Enrutamiento](xref:fundamentals/routing) para obtener una descripción detallada de la generación de direcciones URL.

La interfaz `IUrlHelper` es la pieza subyacente de la infraestructura entre MVC y el enrutamiento para la generación de direcciones URL. Encontrará una instancia de `IUrlHelper` disponible a través de la propiedad `Url` en los controladores, las vistas y los componentes de vista.

En este ejemplo, la interfaz `IUrlHelper` se usa a través de la propiedad `Controller.Url` para generar una dirección URL a otra acción.

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

Si la aplicación está usando la ruta convencional predeterminada, el valor de la variable `url` será la cadena de ruta de dirección URL `/UrlGeneration/Destination`. Esta ruta de dirección URL se crea por enrutamiento mediante la combinación de los valores de ruta de la solicitud actual (valores de ambiente) con los valores pasados a `Url.Action`, y sustituyendo esos valores en la plantilla de ruta:

```
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

El valor de cada uno de los parámetros de ruta incluidos en la plantilla de ruta se sustituye por nombres que coincidan con los valores y los valores de ambiente. Si un parámetro de ruta no tiene un valor, puede utilizar un valor predeterminado en caso de tenerlo, o se puede omitir si es opcional (como en el caso de `id` en este ejemplo). La generación de direcciones URL producirá un error si cualquiera de los parámetros de ruta necesarios no tiene un valor correspondiente. Si se produce un error en la generación de direcciones URL para una ruta, se prueba con la ruta siguiente hasta que se hayan probado todas las rutas o se encuentra una coincidencia.

En el ejemplo anterior de `Url.Action` se supone que el enrutamiento es convencional, pero la generación de direcciones URL funciona del mismo modo con el enrutamiento mediante atributos, si bien los conceptos son diferentes. En el enrutamiento convencional, los valores de ruta se usan para expandir una plantilla; los valores de ruta de `controller` y `action` suelen aparecer en esa plantilla. Esto es válido porque las direcciones URL que coinciden con el enrutamiento se adhieren a una *convención*. En el enrutamiento mediante atributos, no está permitido que los valores de ruta de `controller` y `action` aparezcan en la plantilla. En vez de eso, se utilizan para buscar la plantilla que se va a usar.

Este ejemplo utiliza la enrutamiento mediante atributos:

[!code-csharp[](routing/samples/2.x/main/StartupUseMvc.cs?name=snippet_1)]

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerAttr.cs?name=snippet_1)]

MVC genera una tabla de búsqueda de todas las acciones enrutadas mediante atributos y hará coincidir los valores `controller` y `action` para seleccionar la plantilla de ruta que se usará para la generación de direcciones URL. En el ejemplo anterior se genera `custom/url/to/destination`.

### <a name="generating-urls-by-action-name"></a>Generación de direcciones URL por nombre de acción

`Url.Action` (`IUrlHelper` . `Action`) y todas las sobrecargas relacionadas se basan en la idea de especificar el destino del vínculo mediante un nombre de controlador y un nombre de acción.

> [!NOTE]
> Cuando se usa `Url.Action`, se especifican los valores actuales de la ruta para `controller` y `action`. Los valores de `controller` y `action` forman parte tanto de los *valores de ambiente* **y como de los ** *valores*. El método `Url.Action` siempre utiliza los valores actuales de `action` y `controller` y genera una ruta de dirección URL que dirige a la acción actual.

Intentos de enrutamiento para utilizar los valores en los valores de ambiente para rellenar la información que no se proporcionó al generar una dirección URL. Al utilizar una ruta como `{a}/{b}/{c}/{d}` y los valores de ambiente `{ a = Alice, b = Bob, c = Carol, d = David }`, el enrutamiento tiene suficiente información para generar una dirección URL sin ningún valor adicional, puesto que todos los parámetros de ruta tienen un valor. Si se agregó el valor `{ d = Donovan }`, el valor `{ d = David }` se ignorará y la ruta de dirección URL generada será `Alice/Bob/Carol/Donovan`.

> [!WARNING]
> Las rutas de dirección URL son jerárquicas. En el ejemplo anterior, si se agrega el valor `{ c = Cheryl }`, ambos valores `{ c = Carol, d = David }` se ignorarán. En este caso ya no tenemos un valor para `d` y se producirá un error en la generación de direcciones URL. Debe especificar el valor deseado de `c` y `d`.  Es probable que este problema se produzca con la ruta predeterminada (`{controller}/{action}/{id?}`), pero raramente encontrará este comportamiento en la práctica, dado que `Url.Action` especificará siempre de manera explícita un valor `controller` y `action`.

Las sobrecargas más largas de `Url.Action` también toman un objeto de *valores de ruta* adicional para proporcionar valores para parámetros de ruta distintos de `controller` y `action`. Normalmente verá esto utilizado con `id`, como `Url.Action("Buy", "Products", new { id = 17 })`. Por convención, el objeto de *valores de ruta* normalmente es un objeto de tipo anónimo, pero también puede ser `IDictionary<>` o un *objeto .NET estándar*. Los valores de ruta adicionales que no coinciden con los parámetros de ruta se colocan en la cadena de consulta.

[!code-csharp[](routing/samples/2.x/main/Controllers/TestController.cs)]

> [!TIP]
> Para crear una dirección URL absoluta, use una sobrecarga que acepte `protocol`: `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generating-urls-by-route"></a>Generación de direcciones URL por ruta

En el código anterior se pasó el nombre de acción y de controlador para generar una dirección URL. `IUrlHelper` también proporciona la familia de métodos `Url.RouteUrl`. Estos métodos son similares a `Url.Action`, pero no copian los valores actuales de `action` y `controller` en los valores de ruta. Lo más común es especificar un nombre de ruta para utilizar una ruta específica y generar la dirección URL, por lo general *sin* especificar un nombre de acción o controlador.

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerRouting.cs?name=snippet_1)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generating-urls-in-html"></a>Generación de direcciones URL en HTML

`IHtmlHelper` proporciona los métodos de `HtmlHelper``Html.BeginForm` y `Html.ActionLink` para generar elementos `<form>` y `<a>`, respectivamente. Estos métodos utilizan el método `Url.Action` para generar una dirección URL y aceptan argumentos similares. Los métodos `Url.RouteUrl` complementarios de `HtmlHelper` son `Html.BeginRouteForm` y `Html.RouteLink`, cuya funcionalidad es similar.

Las TagHelper generan direcciones URL a través de la TagHelper `form` y la TagHelper `<a>`. Ambos usan `IUrlHelper` para su implementación. Consulte [Trabajar con formularios](../views/working-with-forms.md) para obtener más información.

Dentro de las vistas, `IUrlHelper` está disponible a través de la propiedad `Url` para una generación de direcciones URL ad hoc no cubierta por los pasos anteriores.

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="generating-urls-in-action-results"></a>Generar direcciones URL en los resultados de acción

Los ejemplos anteriores han demostrado el uso de `IUrlHelper` en un controlador, aunque el uso más común de un controlador consiste en generar una dirección URL como parte de un resultado de acción.

Las clases base `ControllerBase` y `Controller` proporcionan métodos de conveniencia para los resultados de acción que hacen referencia a otra acción. Un uso típico es redirigir después de aceptar la entrada del usuario.

```csharp
public IActionResult Edit(int id, Customer customer)
{
    if (ModelState.IsValid)
    {
        // Update DB with new details.
        return RedirectToAction("Index");
    }
    return View(customer);
}
```

Los patrones de diseño Factory Method de resultados de acción siguen un patrón similar a los métodos en `IUrlHelper`.

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a>Caso especial para rutas convencionales dedicadas

El enrutamiento convencional puede usar un tipo especial de definición de ruta denominada *ruta convencional dedicada*. En el ejemplo siguiente, la ruta llamada `blog` es una ruta convencional dedicada.

```csharp
app.UseMvc(routes =>
{
    routes.MapRoute("blog", "blog/{*article}",
        defaults: new { controller = "Blog", action = "Article" });
    routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

Con estas definiciones de ruta, `Url.Action("Index", "Home")` generará la ruta de dirección URL `/` con la ruta `default`. Pero, ¿por qué? Se puede suponer que los valores de ruta `{ controller = Home, action = Index }` son suficientes para generar una dirección URL utilizando `blog`, con el resultado `/blog?action=Index&controller=Home`.

Las rutas convencionales dedicadas se basan en un comportamiento especial de los valores predeterminados que no tienen un parámetro de ruta correspondiente que impida que la ruta sea "demasiado expansiva" con la generación de direcciones URL. En este caso, los valores predeterminados son `{ controller = Blog, action = Article }`, y ni `controller` ni `action` aparecen como un parámetro de ruta. Cuando el enrutamiento realiza la generación de direcciones URL, los valores proporcionados deben coincidir con los valores predeterminados. La generación de direcciones URL con `blog` producirá un error porque los valores `{ controller = Home, action = Index }` no coinciden con `{ controller = Blog, action = Article }`. Después, el enrutamiento vuelve para probar `default`, operación que se realiza correctamente.

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a>Áreas

Las [áreas](areas.md) son una característica de MVC que se usa para organizar funciones relacionadas en un grupo como un espacio de nombres de enrutamiento independiente (para acciones de controlador) y una estructura de carpetas (para las vistas). La utilización de áreas permite que una aplicación tenga varios controladores con el mismo nombre, siempre y cuando tengan *áreas* diferentes. El uso de áreas crea una jerarquía para el enrutamiento mediante la adición de otro parámetro de ruta, `area`, a `controller` y `action`. En esta sección veremos la interacción entre el enrutamiento y las áreas. Consulte [Áreas](areas.md) para obtener más información sobre cómo se utilizan las áreas con las vistas.

En el ejemplo siguiente se configura MVC para usar la ruta predeterminada convencional y una *ruta de área* para un área denominada `Blog`:

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

Cuando coincide con una ruta de dirección URL como `/Manage/Users/AddUser`, la primera ruta generará los valores de ruta `{ area = Blog, controller = Users, action = AddUser }`. El valor de ruta `area` se genera con un valor predeterminado para `area`. De hecho, la ruta creada por `MapAreaRoute` es equivalente a la siguiente:

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet2)]

`MapAreaRoute` utiliza el nombre de área proporcionado, que en este caso es `Blog`, para crear una ruta con un valor predeterminado y una restricción para `area`. El valor predeterminado garantiza que la ruta siempre produce `{ area = Blog, ... }`; la restricción requiere el valor `{ area = Blog, ... }` para la generación de la dirección URL.

> [!TIP]
> El enrutamiento convencional depende del orden. En general, las rutas con áreas deben colocarse antes en la tabla de rutas, ya que son más específicas que las rutas sin un área.

En el ejemplo anterior, los valores de ruta coincidirían con la acción siguiente:

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

`AreaAttribute` es lo que denota un controlador como parte de un área. Se dice que este controlador está en el área `Blog`. Los controladores sin un atributo `[Area]` no son miembros de ningún área y **no** coincidirán cuando el enrutamiento proporcione el valor de ruta `area`. En el ejemplo siguiente, solo el primer controlador enumerado puede coincidir con los valores de ruta `{ area = Blog, controller = Users, action = AddUser }`.

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

> [!NOTE]
> En aras de la exhaustividad, aquí se muestra el espacio de nombres de cada controlador; en caso contrario, los controladores tendrían un conflicto de nomenclatura y generarían un error del compilador. Los espacios de nombres de clase no tienen ningún efecto en el enrutamiento de MVC.

Los dos primeros controladores son miembros de las áreas y solo coinciden cuando el valor de ruta `area` proporciona su respectivo nombre de área. El tercer controlador no es miembro de ningún área y solo puede coincidir cuando el enrutamiento no proporciona ningún valor para `area`.

> [!NOTE]
> En términos de búsqueda de coincidencias de *ningún valor*, la ausencia del valor `area` es igual que si el valor de `area` fuese null o una cadena vacía.

Al ejecutar una acción en un área, el valor de ruta para `area` estará disponible como un *valor de ambiente* para que el enrutamiento pueda usarlo en la generación de direcciones URL. Esto significa que, de forma predeterminada, las áreas actúan de forma *adhesiva* para la generación de direcciones URL, tal como se muestra en el ejemplo siguiente.
[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<a name="iactionconstraint-ref-label"></a>

## <a name="understanding-iactionconstraint"></a>Descripción de IActionConstraint

> [!NOTE]
> En esta sección se analiza con mayor profundidad el funcionamiento interno del marco y la elección por parte de MVC de la acción que se va a ejecutar. Una aplicación típica no necesitará un `IActionConstraint` personalizado.

Es probable que ya haya usado `IActionConstraint` incluso si no está familiarizado con la interfaz. El atributo `[HttpGet]` y los atributos `[Http-VERB]` similares implementan `IActionConstraint` con el fin de limitar la ejecución de un método de acción.

```csharp
public class ProductsController : Controller
{
    [HttpGet]
    public IActionResult Edit() { }

    public IActionResult Edit(...) { }
}
```

Adoptando la ruta convencional predeterminada, la ruta de dirección URL `/Products/Edit` generaría los valores `{ controller = Products, action = Edit }`, que coincidirían con **ambas** acciones que se muestran aquí. En terminología de `IActionConstraint`, diríamos que ambas acciones se consideran candidatas, puesto que las dos coinciden con los datos de ruta.

Cuando `HttpGetAttribute` se ejecute, dirá que *Edit()* es una coincidencia para *GET*, pero no para cualquier otro verbo HTTP. La acción `Edit(...)` no tiene ninguna restricción definida, por lo que coincidirá con cualquier verbo HTTP. Con `POST`, solamente `Edit(...)` coincide. Pero con `GET` ambas acciones pueden coincidir. No obstante, una acción con `IActionConstraint` siempre se considera *mejor* que una acción sin dicha restricción. Por tanto, como `Edit()` tiene `[HttpGet]`, se considera más específica y se seleccionará si ambas acciones pueden coincidir.

Conceptualmente, `IActionConstraint` es una forma de *sobrecarga*, pero en lugar de sobrecargar métodos con el mismo nombre, la sobrecarga se produce entre acciones que coinciden con la misma dirección URL. El enrutamiento mediante atributos también utiliza `IActionConstraint` y puede dar lugar a que acciones de diferentes controladores se consideren candidatas.

<a name="iactionconstraint-impl-ref-label"></a>

### <a name="implementing-iactionconstraint"></a>Implementación de IActionConstraint

La manera más sencilla de implementar `IActionConstraint` consiste en crear una clase derivada de `System.Attribute` y colocarla en las acciones y los controladores. MVC detectará automáticamente cualquier `IActionConstraint` que se aplique como atributo. El modelo de aplicaciones es quizá el enfoque más flexible para la aplicación de restricciones, puesto que permite metaprogramar cómo se aplican.

En el ejemplo siguiente, una restricción elige una acción basada en un código de *país* de los datos de ruta. El [ejemplo completo se encuentra en GitHub](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).

```csharp
public class CountrySpecificAttribute : Attribute, IActionConstraint
{
    private readonly string _countryCode;

    public CountrySpecificAttribute(string countryCode)
    {
        _countryCode = countryCode;
    }

    public int Order
    {
        get
        {
            return 0;
        }
    }

    public bool Accept(ActionConstraintContext context)
    {
        return string.Equals(
            context.RouteContext.RouteData.Values["country"].ToString(),
            _countryCode,
            StringComparison.OrdinalIgnoreCase);
    }
}
```

Usted es responsable de implementar el método `Accept` y elegir un valor para "Order" para que la restricción se ejecute. En este caso, el método `Accept` devuelve `true` para denotar que la acción es una coincidencia cuando el valor de ruta `country` coincide. Esto difiere de `RouteValueAttribute` en que permite volver a una acción sin atributos. El ejemplo muestra que si se define una acción `en-US`, entonces un código de país como `fr-FR` recurriría a un controlador más genérico que no tenga `[CountrySpecific(...)]` aplicado.

La propiedad `Order` decide de qué *fase* forma parte la restricción. Restricciones de acciones que se ejecutan en grupos basados en la `Order`. Por ejemplo, todos los atributos del método HTTP proporcionados por el marco utilizan el mismo valor `Order` para que se ejecuten en la misma fase. Puede tener las fases que sean necesarias para implementar las directivas deseadas.

> [!TIP]
> Para decidir el valor de `Order`, piense si la restricción se debería o no aplicar antes que los métodos HTTP. Los números más bajos se ejecutan primero.

::: moniker-end
