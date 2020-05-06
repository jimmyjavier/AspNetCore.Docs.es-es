---
title: Aplicar una directiva de seguridad de contenido para ASP.NET CoreBlazor
author: guardrex
description: Aprenda a usar una directiva de seguridad de contenido (CSP) con Blazor ASP.net Core aplicaciones para ayudar a protegerse frente a ataques de scripting entre sitios (XSS).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/02/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/content-security-policy
ms.openlocfilehash: 8c5e1c5dd2d41efade91a612bea2855569a61fee
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775588"
---
# <a name="enforce-a-content-security-policy-for-aspnet-core-blazor"></a>Aplicar una directiva de seguridad de contenido para ASP.NET CoreBlazor

Por [Javier Calvarro Nelson](https://github.com/javiercn) y [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

El [scripting entre sitios (XSS)](xref:security/cross-site-scripting) es una vulnerabilidad de seguridad en la que un atacante coloca uno o varios scripts del lado cliente malintencionados en el contenido representado de una aplicación. Una directiva de seguridad de contenido (CSP) ayuda a protegerse contra ataques XSS informando al explorador de válido:

* Orígenes para contenido cargado, incluidos scripts, hojas de estilo e imágenes.
* Acciones realizadas por una página que especifican los destinos de URL permitidos de los formularios.
* Complementos que se pueden cargar.

Para aplicar un CSP a una aplicación, el desarrollador especifica varias *directivas* de seguridad de contenido de CSP en `Content-Security-Policy` uno o varios `<meta>` encabezados o etiquetas.

El explorador evalúa las directivas mientras se carga una página. El explorador inspecciona los orígenes de la página y determina si cumplen los requisitos de las directivas de seguridad de contenido. Cuando no se cumplen las directivas de directiva para un recurso, el explorador no carga el recurso. Por ejemplo, considere una directiva que no permita scripts de terceros. Cuando una página contiene una `<script>` etiqueta con un origen de terceros en el `src` atributo, el explorador evita que se cargue el script.

CSP se admite en la mayoría de los exploradores de escritorio y móviles modernos, como Chrome, Edge, Firefox, opera y Safari. CSP se recomienda para Blazor las aplicaciones.

## <a name="policy-directives"></a>Directivas de Directiva

Como mínimo, especifique las siguientes directivas y orígenes para Blazor las aplicaciones. Agregue directivas y orígenes adicionales según sea necesario. Las directivas siguientes se usan en la sección [aplicar la Directiva](#apply-the-policy) de este artículo, donde se proporcionan las directivas Blazor de seguridad de Blazor ejemplo para webassembly y Server:

* [base-URI](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/base-uri) &ndash; restringe las direcciones URL de la etiqueta `<base>` de una página. Especifique `self` para indicar que el origen de la aplicación, incluido el esquema y el número de puerto, es un origen válido.
* [Block-All-Mixed-Content](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/block-all-mixed-content) &ndash; evita la carga de contenido http y https mixto.
* [default-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/default-src) &ndash; indica una reserva para las directivas de código fuente que no se especifican explícitamente en la Directiva. Especifique `self` para indicar que el origen de la aplicación, incluido el esquema y el número de puerto, es un origen válido.
* [img-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/img-src) &ndash; indica orígenes válidos para las imágenes.
  * Especifique `data:` para permitir la carga de `data:` imágenes de las direcciones URL.
  * Especifique `https:` para permitir la carga de imágenes desde puntos de conexión HTTPS.
* [Object-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/object-src) &ndash; indica los orígenes válidos `<object>`para `<embed>`las etiquetas `<applet>` , y. Especifique `none` para evitar todos los orígenes de direcciones URL.
* [script-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/script-src) &ndash; indica orígenes válidos para scripts.
  * Especifique el `https://stackpath.bootstrapcdn.com/` origen de host para los scripts de arranque.
  * Especifique `self` para indicar que el origen de la aplicación, incluido el esquema y el número de puerto, es un origen válido.
  * En una Blazor aplicación webassembly:
    * Especifique los siguientes valores hash para permitir que se Blazor carguen los scripts en línea de webassembly necesarios:
      * `sha256-v8ZC9OgMhcnEQ/Me77/R9TlJfzOBqrMTW8e1KuqLaqc=`
      * `sha256-If//FtbPc03afjLezvWHnC3Nbu4fDM04IIzkPaf3pH0=`
      * `sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=`
    * Especifique `unsafe-eval` el uso `eval()` de los métodos y para crear código a partir de cadenas.
  * En una Blazor aplicación de servidor, especifique `sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=` el hash del script en línea que realiza la detección de reserva para las hojas de estilos.
* [Style: src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/style-src) &ndash; indica orígenes válidos para las hojas de estilos.
  * Especifique el `https://stackpath.bootstrapcdn.com/` origen de host para las hojas de estilo bootstrap.
  * Especifique `self` para indicar que el origen de la aplicación, incluido el esquema y el número de puerto, es un origen válido.
  * Especifique `unsafe-inline` para permitir el uso de estilos alineados. La declaración en línea es necesaria para que la interfaz Blazor de usuario de las aplicaciones de servidor reconecte el cliente y el servidor después de la solicitud inicial. En una versión futura, es posible que se eliminen los estilos `unsafe-inline` en línea para que ya no sea necesario.
* [actualización: insecure-requests](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/upgrade-insecure-requests) &ndash; indica que las direcciones URL de contenido de orígenes no seguros (http) se deben adquirir de forma segura a través de HTTPS.

Todos los exploradores, excepto Microsoft Internet Explorer, admiten las directivas anteriores.

Para obtener los algoritmos hash SHA para scripts en línea adicionales:

* Aplique el CSP mostrado en la sección [aplicar la Directiva](#apply-the-policy) .
* Acceda a la consola de herramientas de desarrollo del explorador mientras ejecuta la aplicación localmente. El explorador calcula y muestra los valores hash para los scripts bloqueados cuando está `meta` presente un encabezado o etiqueta CSP.
* Copie los valores hash proporcionados por el explorador en `script-src` los orígenes. Use comillas simples alrededor de cada hash.

Para obtener una matriz de compatibilidad con exploradores de nivel 2 de directiva de seguridad de contenido, consulte [puedo usar: Directiva de seguridad de contenido nivel 2](https://www.caniuse.com/#feat=contentsecuritypolicy2).

## <a name="apply-the-policy"></a>Aplicación de la directiva

Use una `<meta>` etiqueta para aplicar la Directiva:

* Establezca el valor del `http-equiv` atributo en. `Content-Security-Policy`
* Coloque las directivas en el `content` valor del atributo. Separe las directivas con un punto y`;`coma ().
* Coloque siempre la `meta` etiqueta en el `<head>` contenido.

En las secciones siguientes se muestran las Blazor directivas de ejemplo Blazor para webassembly y Server. En estos ejemplos se tiene una versión de este artículo para cada Blazorversión de. Para usar una versión adecuada para su versión, seleccione la versión del documento con el selector desplegable **versión** en esta página web.

### <a name="blazor-webassembly"></a>Blazor WebAssembly

En el `<head>` contenido de la página host de *wwwroot/index.html* , aplique las directivas descritas en la sección [directivas de directiva](#policy-directives) :

```html
<meta http-equiv="Content-Security-Policy" 
      content="base-uri 'self';
               block-all-mixed-content;
               default-src 'self';
               img-src data: https:;
               object-src 'none';
               script-src https://stackpath.bootstrapcdn.com/ 
                          'self' 
                          'sha256-v8ZC9OgMhcnEQ/Me77/R9TlJfzOBqrMTW8e1KuqLaqc=' 
                          'sha256-If//FtbPc03afjLezvWHnC3Nbu4fDM04IIzkPaf3pH0=' 
                          'sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=' 
                          'unsafe-eval';
               style-src https://stackpath.bootstrapcdn.com/
                         'self'
                         'unsafe-inline';
               upgrade-insecure-requests;">
```

### <a name="blazor-server"></a>Servidor de Blazor

En el `<head>` contenido de la página de host *pages/_Host. cshtml* , aplique las directivas descritas en la sección [directivas de directiva](#policy-directives) :

```cshtml
<meta http-equiv="Content-Security-Policy" 
      content="base-uri 'self';
               block-all-mixed-content;
               default-src 'self';
               img-src data: https:;
               object-src 'none';
               script-src https://stackpath.bootstrapcdn.com/ 
                          'self' 
                          'sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=';
               style-src https://stackpath.bootstrapcdn.com/
                         'self' 
                         'unsafe-inline';
               upgrade-insecure-requests;">
```

## <a name="meta-tag-limitations"></a>Limitaciones de la etiqueta meta

Una `<meta>` Directiva de etiquetas no admite las siguientes directivas:

* [antecesores de marco](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/frame-ancestors)
* [notificar a](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [URI de informe](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)
* [aislados](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/sandbox)

Para admitir las directivas anteriores, use un encabezado denominado `Content-Security-Policy`. La cadena de directiva es el valor del encabezado.

## <a name="test-a-policy-and-receive-violation-reports"></a>Probar una directiva y recibir informes de infracción

La prueba ayuda a confirmar que los scripts de terceros no se bloquean accidentalmente al crear una directiva inicial.

Para probar una directiva a lo largo de un período de tiempo sin aplicar las directivas de directiva `<meta>` , establezca `http-equiv` el atributo o el nombre de encabezado de la etiqueta de `Content-Security-Policy-Report-Only`una directiva basada en encabezado en. Los informes de errores se envían como documentos JSON a una dirección URL especificada. Para obtener más información, vea [MDN web docs: Content-Security-Policy-Report-Report-Only](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy-Report-Only).

Para obtener información sobre las infracciones mientras una directiva está activa, consulte los siguientes artículos:

* [notificar a](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [URI de informe](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)

Aunque `report-uri` ya no se recomienda para su uso, deben usarse las dos `report-to` directivas hasta que sea compatible con todos los exploradores principales. No utilice `report-uri` exclusivamente porque la compatibilidad `report-uri` con está sujeta a la eliminación *en cualquier momento* desde exploradores. Quite la compatibilidad `report-uri` con en las directivas `report-to` cuando sea totalmente compatible. Para realizar un seguimiento `report-to`de la adopción de, consulte [puedo usar: Report-to](https://caniuse.com/#feat=mdn-http_headers_csp_content-security-policy_report-to).

Probar y actualizar la Directiva de una aplicación en cada versión.

## <a name="troubleshoot"></a>Solución de problemas

* Los errores aparecen en la consola de herramientas de desarrollo del explorador. Los exploradores proporcionan información acerca de:
  * Elementos que no cumplen la Directiva.
  * Cómo modificar la Directiva para permitir un elemento bloqueado.
* Una directiva solo es totalmente efectiva cuando el explorador del cliente admite todas las directivas incluidas. Para obtener una matriz de compatibilidad de explorador actual, vea [¿puedo usar: Content-Security-Policy](https://caniuse.com/#search=Content-Security-Policy).

## <a name="additional-resources"></a>Recursos adicionales

* [Documentos web de MDN: Content-Security-Policy](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy)
* [Nivel 2 de directiva de seguridad de contenido](https://www.w3.org/TR/CSP2/)
* [Evaluador de CSP de Google](https://csp-evaluator.withgoogle.com/)
