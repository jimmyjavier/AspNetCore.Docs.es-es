---
title: Globalización y localización de Blazor de ASP.NET Core
author: guardrex
description: Aprenda a poner los componentes de Razor a disposición de los usuarios de diferentes referencias culturales e idiomas.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/04/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/globalization-localization
ms.openlocfilehash: 5050d99e5304c7edaf6faa43f05298b69882521d
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/23/2020
ms.locfileid: "85243595"
---
# <a name="aspnet-core-blazor-globalization-and-localization"></a>Globalización y localización de Blazor de ASP.NET Core

Por [Luke Latham](https://github.com/guardrex) y [Daniel Roth](https://github.com/danroth27)

Los componentes de Razor se pueden poner a disposición de los usuarios de varias referencias culturales e idiomas. Estos son los escenarios de globalización y localización de .NET disponibles:

* Sistema de recursos de .NET
* Formato de fecha y número específico de la referencia cultural

Actualmente se admite un conjunto limitado de escenarios de localización de ASP.NET Core:

* <xref:Microsoft.Extensions.Localization.IStringLocalizer> y <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> *se admiten* en aplicaciones de Blazor.
* <xref:Microsoft.AspNetCore.Mvc.Localization.IHtmlLocalizer>, <xref:Microsoft.AspNetCore.Mvc.Localization.IViewLocalizer> y la localización de anotaciones de datos son escenarios de MVC de ASP.NET Core y **no se admiten** en aplicaciones de Blazor.

Para obtener más información, vea <xref:fundamentals/localization>.

## <a name="globalization"></a>Globalización

La funcionalidad [`@bind`](xref:mvc/views/razor#bind) de Blazor realiza formatos y analiza valores para mostrarlos en función de la referencia cultural actual del usuario.

Se puede acceder a la referencia cultural actual desde la propiedad <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName>.

<xref:System.Globalization.CultureInfo.InvariantCulture?displayProperty=nameWithType> se emplea para los siguientes tipos de campo (`<input type="{TYPE}" />`):

* `date`
* `number`

Los tipos de campo anteriores:

* Se muestran según sus reglas de formato basado en explorador correspondientes.
* No pueden contener texto de forma libre.
* Proporcionar características de interacción del usuario en función de la implementación del explorador.

Los siguientes tipos de campo tienen requisitos de formato específicos y no se admiten actualmente en Blazor, ya que no son compatibles con todos los exploradores principales:

* `datetime-local`
* `month`
* `week`

[`@bind`](xref:mvc/views/razor#bind) admite el parámetro `@bind:culture` para proporcionar un elemento <xref:System.Globalization.CultureInfo?displayProperty=fullName> para analizar y dar formato a un valor. No se recomienda especificar una referencia cultural cuando se usen los tipos de campo `date` y `number`. `date` y `number` tienen compatibilidad integrada con Blazor que proporciona la referencia cultural necesaria.

## <a name="localization"></a>Localización

### <a name="blazor-webassembly"></a>Blazor WebAssembly

Las aplicaciones de Blazor WebAssembly establecen la referencia cultural mediante la [preferencia de idioma](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages) del usuario.

Para configurar explícitamente la referencia cultural, establezca <xref:System.Globalization.CultureInfo.DefaultThreadCurrentCulture?displayProperty=nameWithType> y <xref:System.Globalization.CultureInfo.DefaultThreadCurrentUICulture?displayProperty=nameWithType> en `Program.Main`.

De forma predeterminada, la configuración del enlazador de Blazor para aplicaciones WebAssembly de Blazor quita información de internacionalización, excepto para las configuraciones regionales solicitadas de forma explícita. Para obtener más información e instrucciones sobre cómo controlar el comportamiento del enlazador, vea <xref:blazor/host-and-deploy/configure-linker#configure-the-linker-for-internationalization>.

Si bien la referencia cultural que Blazor selecciona de manera predeterminada puede ser suficiente para la mayoría de los usuarios, considere la posibilidad de ofrecer una manera para que los usuarios especifiquen su configuración regional preferida. Para una aplicación de ejemplo WebAssembly de Blazor con un selector de referencia cultural, consulte la aplicación de ejemplo de localización [`LocSample`](https://github.com/pranavkm/LocSample).

### <a name="blazor-server"></a>Servidor de Blazor

Las aplicaciones de servidor Blazor se localizan usando un [middleware de localización](xref:fundamentals/localization#localization-middleware). El middleware selecciona la referencia cultural adecuada según los usuarios que solicitan recursos de la aplicación.

La referencia cultural se puede establecer con uno de los siguientes métodos:

* [Cookies](#cookies)
* [Especificando una interfaz de usuario para elegir la referencia cultural](#provide-ui-to-choose-the-culture)

Para obtener más información y ejemplos, vea <xref:fundamentals/localization>.

#### <a name="cookies"></a>Cookies

Una cookie de referencia cultural de localización puede conservar la referencia cultural del usuario. El middleware de localización lee la cookie en solicitudes posteriores para establecer la referencia cultural del usuario. 

El uso de una cookie garantiza que la conexión WebSocket puede propagar correctamente la referencia cultural. Si los esquemas de localización se basan en la ruta de acceso URL o en la cadena de consulta, puede que el esquema no funcione con WebSockets, por lo que no se podrá conservar la referencia cultural. Por lo tanto, el uso de una cookie de referencia cultural de localización es el método recomendado.

Se puede usar cualquier técnica para asignar una referencia cultural si la referencia cultural se conserva en una cookie de localización. Si la aplicación ya tiene un esquema de localización establecido para ASP.NET Core del lado servidor, siga usando la infraestructura de localización existente de la aplicación y establezca la cookie de cultura de localización en el esquema de la aplicación.

En el siguiente ejemplo se muestra cómo establecer la referencia cultural actual en una cookie que el middleware de localización puede leer. Cree una expresión de Razor en el archivo `Pages/_Host.cshtml`, justo dentro de la etiqueta de apertura `<body>`:

```cshtml
@using System.Globalization
@using Microsoft.AspNetCore.Localization

...

<body>
    @{
        this.HttpContext.Response.Cookies.Append(
            CookieRequestCultureProvider.DefaultCookieName,
            CookieRequestCultureProvider.MakeCookieValue(
                new RequestCulture(
                    CultureInfo.CurrentCulture,
                    CultureInfo.CurrentUICulture)));
    }

    ...
</body>
```

La aplicación controla la localización en la siguiente secuencia de eventos:

1. El explorador envía una solicitud HTTP inicial a la aplicación.
1. El middleware de localización asigna la referencia cultural.
1. La expresión Razor de la página `_Host` (`_Host.cshtml`) conserva la referencia cultural en una cookie como parte de la respuesta.
1. El explorador abre una conexión WebSocket para crear una sesión de servidor Blazor interactiva.
1. El middleware de localización lee la cookie y asigna la referencia cultural.
1. La sesión del servidor Blazor comienza con la referencia cultural correcta.

#### <a name="provide-ui-to-choose-the-culture"></a>Especificación de una interfaz de usuario para elegir la referencia cultural

Para especificar una interfaz de usuario que permita a los usuarios seleccionar una referencia cultural, se recomienda usar un *método basado en el redireccionamiento*. El proceso es similar a lo que ocurre en una aplicación web cuando un usuario intenta acceder a un recurso seguro. El usuario se redirige a una página de inicio de sesión y, a continuación, se redirige de vuelta al recurso original. 

La aplicación conserva la referencia cultural seleccionada por el usuario a través de un redireccionamiento a un controlador. Este controlador establece la referencia cultural seleccionada del usuario en una cookie y redirige al usuario de nuevo al URI original.

Establezca un punto de conexión HTTP en el servidor para establecer la referencia cultural seleccionada del usuario en una cookie y realizar el redireccionamiento al URI original:

```csharp
[Route("[controller]/[action]")]
public class CultureController : Controller
{
    public IActionResult SetCulture(string culture, string redirectUri)
    {
        if (culture != null)
        {
            HttpContext.Response.Cookies.Append(
                CookieRequestCultureProvider.DefaultCookieName,
                CookieRequestCultureProvider.MakeCookieValue(
                    new RequestCulture(culture)));
        }

        return LocalRedirect(redirectUri);
    }
}
```

> [!WARNING]
> Use el resultado de la acción <xref:Microsoft.AspNetCore.Mvc.ControllerBase.LocalRedirect%2A> para evitar ataques de redireccionamiento abierto. Para obtener más información, vea <xref:security/preventing-open-redirects>.

Si la aplicación no está configurada para procesar acciones del controlador:

* Agregue servicios de MVC a la colección de servicios en `Startup.ConfigureServices`:

  ```csharp
  services.AddControllers();
  ```

* Agregue un enrutamiento de punto de conexión del controlador en `Startup.Configure`:

  ```csharp
  app.UseEndpoints(endpoints =>
  {
      endpoints.MapControllers();
      endpoints.MapBlazorHub();
      endpoints.MapFallbackToPage("/_Host");
  });
  ```

El siguiente componente muestra un ejemplo sobre cómo realizar el redireccionamiento inicial cuando el usuario selecciona una referencia cultural:

```razor
@inject NavigationManager NavigationManager

<h3>Select your language</h3>

<select @onchange="OnSelected">
    <option>Select...</option>
    <option value="en-US">English</option>
    <option value="fr-FR">Français</option>
</select>

@code {
    private void OnSelected(ChangeEventArgs e)
    {
        var culture = (string)e.Value;
        var uri = new Uri(NavigationManager.Uri)
            .GetComponents(UriComponents.PathAndQuery, UriFormat.Unescaped);
        var query = $"?culture={Uri.EscapeDataString(culture)}&" +
            $"redirectUri={Uri.EscapeDataString(uri)}";

        NavigationManager.NavigateTo("/Culture/SetCulture" + query, forceLoad: true);
    }
}
```

## <a name="additional-resources"></a>Recursos adicionales

* <xref:fundamentals/localization>
