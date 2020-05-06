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
# <a name="enforce-a-content-security-policy-for-aspnet-core-blazor"></a><span data-ttu-id="256f5-103">Aplicar una directiva de seguridad de contenido para ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="256f5-103">Enforce a Content Security Policy for ASP.NET Core Blazor</span></span>

<span data-ttu-id="256f5-104">Por [Javier Calvarro Nelson](https://github.com/javiercn) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="256f5-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="256f5-105">El [scripting entre sitios (XSS)](xref:security/cross-site-scripting) es una vulnerabilidad de seguridad en la que un atacante coloca uno o varios scripts del lado cliente malintencionados en el contenido representado de una aplicación.</span><span class="sxs-lookup"><span data-stu-id="256f5-105">[Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) is a security vulnerability where an attacker places one or more malicious client-side scripts into an app's rendered content.</span></span> <span data-ttu-id="256f5-106">Una directiva de seguridad de contenido (CSP) ayuda a protegerse contra ataques XSS informando al explorador de válido:</span><span class="sxs-lookup"><span data-stu-id="256f5-106">A Content Security Policy (CSP) helps protect against XSS attacks by informing the browser of valid:</span></span>

* <span data-ttu-id="256f5-107">Orígenes para contenido cargado, incluidos scripts, hojas de estilo e imágenes.</span><span class="sxs-lookup"><span data-stu-id="256f5-107">Sources for loaded content, including scripts, stylesheets, and images.</span></span>
* <span data-ttu-id="256f5-108">Acciones realizadas por una página que especifican los destinos de URL permitidos de los formularios.</span><span class="sxs-lookup"><span data-stu-id="256f5-108">Actions taken by a page, specifying permitted URL targets of forms.</span></span>
* <span data-ttu-id="256f5-109">Complementos que se pueden cargar.</span><span class="sxs-lookup"><span data-stu-id="256f5-109">Plugins that can be loaded.</span></span>

<span data-ttu-id="256f5-110">Para aplicar un CSP a una aplicación, el desarrollador especifica varias *directivas* de seguridad de contenido de CSP en `Content-Security-Policy` uno o varios `<meta>` encabezados o etiquetas.</span><span class="sxs-lookup"><span data-stu-id="256f5-110">To apply a CSP to an app, the developer specifies several CSP content security *directives* in one or more `Content-Security-Policy` headers or `<meta>` tags.</span></span>

<span data-ttu-id="256f5-111">El explorador evalúa las directivas mientras se carga una página.</span><span class="sxs-lookup"><span data-stu-id="256f5-111">Policies are evaluated by the browser while a page is loading.</span></span> <span data-ttu-id="256f5-112">El explorador inspecciona los orígenes de la página y determina si cumplen los requisitos de las directivas de seguridad de contenido.</span><span class="sxs-lookup"><span data-stu-id="256f5-112">The browser inspects the page's sources and determines if they meet the requirements of the content security directives.</span></span> <span data-ttu-id="256f5-113">Cuando no se cumplen las directivas de directiva para un recurso, el explorador no carga el recurso.</span><span class="sxs-lookup"><span data-stu-id="256f5-113">When policy directives aren't met for a resource, the browser doesn't load the resource.</span></span> <span data-ttu-id="256f5-114">Por ejemplo, considere una directiva que no permita scripts de terceros.</span><span class="sxs-lookup"><span data-stu-id="256f5-114">For example, consider a policy that doesn't allow third-party scripts.</span></span> <span data-ttu-id="256f5-115">Cuando una página contiene una `<script>` etiqueta con un origen de terceros en el `src` atributo, el explorador evita que se cargue el script.</span><span class="sxs-lookup"><span data-stu-id="256f5-115">When a page contains a `<script>` tag with a third-party origin in the `src` attribute, the browser prevents the script from loading.</span></span>

<span data-ttu-id="256f5-116">CSP se admite en la mayoría de los exploradores de escritorio y móviles modernos, como Chrome, Edge, Firefox, opera y Safari.</span><span class="sxs-lookup"><span data-stu-id="256f5-116">CSP is supported in most modern desktop and mobile browsers, including Chrome, Edge, Firefox, Opera, and Safari.</span></span> <span data-ttu-id="256f5-117">CSP se recomienda para Blazor las aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="256f5-117">CSP is recommended for Blazor apps.</span></span>

## <a name="policy-directives"></a><span data-ttu-id="256f5-118">Directivas de Directiva</span><span class="sxs-lookup"><span data-stu-id="256f5-118">Policy directives</span></span>

<span data-ttu-id="256f5-119">Como mínimo, especifique las siguientes directivas y orígenes para Blazor las aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="256f5-119">Minimally, specify the following directives and sources for Blazor apps.</span></span> <span data-ttu-id="256f5-120">Agregue directivas y orígenes adicionales según sea necesario.</span><span class="sxs-lookup"><span data-stu-id="256f5-120">Add additional directives and sources as needed.</span></span> <span data-ttu-id="256f5-121">Las directivas siguientes se usan en la sección [aplicar la Directiva](#apply-the-policy) de este artículo, donde se proporcionan las directivas Blazor de seguridad de Blazor ejemplo para webassembly y Server:</span><span class="sxs-lookup"><span data-stu-id="256f5-121">The following directives are used in the [Apply the policy](#apply-the-policy) section of this article, where example security policies for Blazor WebAssembly and Blazor Server are provided:</span></span>

* <span data-ttu-id="256f5-122">[base-URI](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/base-uri) &ndash; restringe las direcciones URL de la etiqueta `<base>` de una página.</span><span class="sxs-lookup"><span data-stu-id="256f5-122">[base-uri](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/base-uri) &ndash; Restricts the URLs for a page's `<base>` tag.</span></span> <span data-ttu-id="256f5-123">Especifique `self` para indicar que el origen de la aplicación, incluido el esquema y el número de puerto, es un origen válido.</span><span class="sxs-lookup"><span data-stu-id="256f5-123">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
* <span data-ttu-id="256f5-124">[Block-All-Mixed-Content](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/block-all-mixed-content) &ndash; evita la carga de contenido http y https mixto.</span><span class="sxs-lookup"><span data-stu-id="256f5-124">[block-all-mixed-content](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/block-all-mixed-content) &ndash; Prevents loading mixed HTTP and HTTPS content.</span></span>
* <span data-ttu-id="256f5-125">[default-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/default-src) &ndash; indica una reserva para las directivas de código fuente que no se especifican explícitamente en la Directiva.</span><span class="sxs-lookup"><span data-stu-id="256f5-125">[default-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/default-src) &ndash; Indicates a fallback for source directives that aren't explicitly specified by the policy.</span></span> <span data-ttu-id="256f5-126">Especifique `self` para indicar que el origen de la aplicación, incluido el esquema y el número de puerto, es un origen válido.</span><span class="sxs-lookup"><span data-stu-id="256f5-126">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
* <span data-ttu-id="256f5-127">[img-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/img-src) &ndash; indica orígenes válidos para las imágenes.</span><span class="sxs-lookup"><span data-stu-id="256f5-127">[img-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/img-src) &ndash; Indicates valid sources for images.</span></span>
  * <span data-ttu-id="256f5-128">Especifique `data:` para permitir la carga de `data:` imágenes de las direcciones URL.</span><span class="sxs-lookup"><span data-stu-id="256f5-128">Specify `data:` to permit loading images from `data:` URLs.</span></span>
  * <span data-ttu-id="256f5-129">Especifique `https:` para permitir la carga de imágenes desde puntos de conexión HTTPS.</span><span class="sxs-lookup"><span data-stu-id="256f5-129">Specify `https:` to permit loading images from HTTPS endpoints.</span></span>
* <span data-ttu-id="256f5-130">[Object-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/object-src) &ndash; indica los orígenes válidos `<object>`para `<embed>`las etiquetas `<applet>` , y.</span><span class="sxs-lookup"><span data-stu-id="256f5-130">[object-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/object-src) &ndash; Indicates valid sources for the `<object>`, `<embed>`, and `<applet>` tags.</span></span> <span data-ttu-id="256f5-131">Especifique `none` para evitar todos los orígenes de direcciones URL.</span><span class="sxs-lookup"><span data-stu-id="256f5-131">Specify `none` to prevent all URL sources.</span></span>
* <span data-ttu-id="256f5-132">[script-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/script-src) &ndash; indica orígenes válidos para scripts.</span><span class="sxs-lookup"><span data-stu-id="256f5-132">[script-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/script-src) &ndash; Indicates valid sources for scripts.</span></span>
  * <span data-ttu-id="256f5-133">Especifique el `https://stackpath.bootstrapcdn.com/` origen de host para los scripts de arranque.</span><span class="sxs-lookup"><span data-stu-id="256f5-133">Specify the `https://stackpath.bootstrapcdn.com/` host source for Bootstrap scripts.</span></span>
  * <span data-ttu-id="256f5-134">Especifique `self` para indicar que el origen de la aplicación, incluido el esquema y el número de puerto, es un origen válido.</span><span class="sxs-lookup"><span data-stu-id="256f5-134">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
  * <span data-ttu-id="256f5-135">En una Blazor aplicación webassembly:</span><span class="sxs-lookup"><span data-stu-id="256f5-135">In a Blazor WebAssembly app:</span></span>
    * <span data-ttu-id="256f5-136">Especifique los siguientes valores hash para permitir que se Blazor carguen los scripts en línea de webassembly necesarios:</span><span class="sxs-lookup"><span data-stu-id="256f5-136">Specify the following hashes to permit the required Blazor WebAssembly inline scripts to load:</span></span>
      * `sha256-v8ZC9OgMhcnEQ/Me77/R9TlJfzOBqrMTW8e1KuqLaqc=`
      * `sha256-If//FtbPc03afjLezvWHnC3Nbu4fDM04IIzkPaf3pH0=`
      * `sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=`
    * <span data-ttu-id="256f5-137">Especifique `unsafe-eval` el uso `eval()` de los métodos y para crear código a partir de cadenas.</span><span class="sxs-lookup"><span data-stu-id="256f5-137">Specify `unsafe-eval` to use `eval()` and methods for creating code from strings.</span></span>
  * <span data-ttu-id="256f5-138">En una Blazor aplicación de servidor, especifique `sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=` el hash del script en línea que realiza la detección de reserva para las hojas de estilos.</span><span class="sxs-lookup"><span data-stu-id="256f5-138">In a Blazor Server app, specify the `sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=` hash for the inline script that performs fallback detection for stylesheets.</span></span>
* <span data-ttu-id="256f5-139">[Style: src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/style-src) &ndash; indica orígenes válidos para las hojas de estilos.</span><span class="sxs-lookup"><span data-stu-id="256f5-139">[style-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/style-src) &ndash; Indicates valid sources for stylesheets.</span></span>
  * <span data-ttu-id="256f5-140">Especifique el `https://stackpath.bootstrapcdn.com/` origen de host para las hojas de estilo bootstrap.</span><span class="sxs-lookup"><span data-stu-id="256f5-140">Specify the `https://stackpath.bootstrapcdn.com/` host source for Bootstrap stylesheets.</span></span>
  * <span data-ttu-id="256f5-141">Especifique `self` para indicar que el origen de la aplicación, incluido el esquema y el número de puerto, es un origen válido.</span><span class="sxs-lookup"><span data-stu-id="256f5-141">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
  * <span data-ttu-id="256f5-142">Especifique `unsafe-inline` para permitir el uso de estilos alineados.</span><span class="sxs-lookup"><span data-stu-id="256f5-142">Specify `unsafe-inline` to allow the use of inline styles.</span></span> <span data-ttu-id="256f5-143">La declaración en línea es necesaria para que la interfaz Blazor de usuario de las aplicaciones de servidor reconecte el cliente y el servidor después de la solicitud inicial.</span><span class="sxs-lookup"><span data-stu-id="256f5-143">The inline declaration is required for the UI in Blazor Server apps for reconnecting the client and server after the initial request.</span></span> <span data-ttu-id="256f5-144">En una versión futura, es posible que se eliminen los estilos `unsafe-inline` en línea para que ya no sea necesario.</span><span class="sxs-lookup"><span data-stu-id="256f5-144">In a future release, inline styling might be removed so that `unsafe-inline` is no longer required.</span></span>
* <span data-ttu-id="256f5-145">[actualización: insecure-requests](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/upgrade-insecure-requests) &ndash; indica que las direcciones URL de contenido de orígenes no seguros (http) se deben adquirir de forma segura a través de HTTPS.</span><span class="sxs-lookup"><span data-stu-id="256f5-145">[upgrade-insecure-requests](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/upgrade-insecure-requests) &ndash; Indicates that content URLs from insecure (HTTP) sources should be acquired securely over HTTPS.</span></span>

<span data-ttu-id="256f5-146">Todos los exploradores, excepto Microsoft Internet Explorer, admiten las directivas anteriores.</span><span class="sxs-lookup"><span data-stu-id="256f5-146">The preceding directives are supported by all browsers except Microsoft Internet Explorer.</span></span>

<span data-ttu-id="256f5-147">Para obtener los algoritmos hash SHA para scripts en línea adicionales:</span><span class="sxs-lookup"><span data-stu-id="256f5-147">To obtain SHA hashes for additional inline scripts:</span></span>

* <span data-ttu-id="256f5-148">Aplique el CSP mostrado en la sección [aplicar la Directiva](#apply-the-policy) .</span><span class="sxs-lookup"><span data-stu-id="256f5-148">Apply the CSP shown in the [Apply the policy](#apply-the-policy) section.</span></span>
* <span data-ttu-id="256f5-149">Acceda a la consola de herramientas de desarrollo del explorador mientras ejecuta la aplicación localmente.</span><span class="sxs-lookup"><span data-stu-id="256f5-149">Access the browser's developer tools console while running the app locally.</span></span> <span data-ttu-id="256f5-150">El explorador calcula y muestra los valores hash para los scripts bloqueados cuando está `meta` presente un encabezado o etiqueta CSP.</span><span class="sxs-lookup"><span data-stu-id="256f5-150">The browser calculates and displays hashes for blocked scripts when a CSP header or `meta` tag is present.</span></span>
* <span data-ttu-id="256f5-151">Copie los valores hash proporcionados por el explorador en `script-src` los orígenes.</span><span class="sxs-lookup"><span data-stu-id="256f5-151">Copy the hashes provided by the browser to the `script-src` sources.</span></span> <span data-ttu-id="256f5-152">Use comillas simples alrededor de cada hash.</span><span class="sxs-lookup"><span data-stu-id="256f5-152">Use single quotes around each hash.</span></span>

<span data-ttu-id="256f5-153">Para obtener una matriz de compatibilidad con exploradores de nivel 2 de directiva de seguridad de contenido, consulte [puedo usar: Directiva de seguridad de contenido nivel 2](https://www.caniuse.com/#feat=contentsecuritypolicy2).</span><span class="sxs-lookup"><span data-stu-id="256f5-153">For a Content Security Policy Level 2 browser support matrix, see [Can I use: Content Security Policy Level 2](https://www.caniuse.com/#feat=contentsecuritypolicy2).</span></span>

## <a name="apply-the-policy"></a><span data-ttu-id="256f5-154">Aplicación de la directiva</span><span class="sxs-lookup"><span data-stu-id="256f5-154">Apply the policy</span></span>

<span data-ttu-id="256f5-155">Use una `<meta>` etiqueta para aplicar la Directiva:</span><span class="sxs-lookup"><span data-stu-id="256f5-155">Use a `<meta>` tag to apply the policy:</span></span>

* <span data-ttu-id="256f5-156">Establezca el valor del `http-equiv` atributo en. `Content-Security-Policy`</span><span class="sxs-lookup"><span data-stu-id="256f5-156">Set the value of the `http-equiv` attribute to `Content-Security-Policy`.</span></span>
* <span data-ttu-id="256f5-157">Coloque las directivas en el `content` valor del atributo.</span><span class="sxs-lookup"><span data-stu-id="256f5-157">Place the directives in the `content` attribute value.</span></span> <span data-ttu-id="256f5-158">Separe las directivas con un punto y`;`coma ().</span><span class="sxs-lookup"><span data-stu-id="256f5-158">Separate directives with a semicolon (`;`).</span></span>
* <span data-ttu-id="256f5-159">Coloque siempre la `meta` etiqueta en el `<head>` contenido.</span><span class="sxs-lookup"><span data-stu-id="256f5-159">Always place the `meta` tag in the `<head>` content.</span></span>

<span data-ttu-id="256f5-160">En las secciones siguientes se muestran las Blazor directivas de ejemplo Blazor para webassembly y Server.</span><span class="sxs-lookup"><span data-stu-id="256f5-160">The following sections show example policies for Blazor WebAssembly and Blazor Server.</span></span> <span data-ttu-id="256f5-161">En estos ejemplos se tiene una versión de este artículo para cada Blazorversión de.</span><span class="sxs-lookup"><span data-stu-id="256f5-161">These examples are versioned with this article for each release of Blazor.</span></span> <span data-ttu-id="256f5-162">Para usar una versión adecuada para su versión, seleccione la versión del documento con el selector desplegable **versión** en esta página web.</span><span class="sxs-lookup"><span data-stu-id="256f5-162">To use a version appropriate for your release, select the document version with the **Version** drop down selector on this webpage.</span></span>

### <a name="blazor-webassembly"></a>Blazor<span data-ttu-id="256f5-163"> WebAssembly</span><span class="sxs-lookup"><span data-stu-id="256f5-163"> WebAssembly</span></span>

<span data-ttu-id="256f5-164">En el `<head>` contenido de la página host de *wwwroot/index.html* , aplique las directivas descritas en la sección [directivas de directiva](#policy-directives) :</span><span class="sxs-lookup"><span data-stu-id="256f5-164">In the `<head>` content of the *wwwroot/index.html* host page, apply the directives described in the [Policy directives](#policy-directives) section:</span></span>

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

### <a name="blazor-server"></a><span data-ttu-id="256f5-165">Servidor de Blazor</span><span class="sxs-lookup"><span data-stu-id="256f5-165">Blazor Server</span></span>

<span data-ttu-id="256f5-166">En el `<head>` contenido de la página de host *pages/_Host. cshtml* , aplique las directivas descritas en la sección [directivas de directiva](#policy-directives) :</span><span class="sxs-lookup"><span data-stu-id="256f5-166">In the `<head>` content of the *Pages/_Host.cshtml* host page, apply the directives described in the [Policy directives](#policy-directives) section:</span></span>

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

## <a name="meta-tag-limitations"></a><span data-ttu-id="256f5-167">Limitaciones de la etiqueta meta</span><span class="sxs-lookup"><span data-stu-id="256f5-167">Meta tag limitations</span></span>

<span data-ttu-id="256f5-168">Una `<meta>` Directiva de etiquetas no admite las siguientes directivas:</span><span class="sxs-lookup"><span data-stu-id="256f5-168">A `<meta>` tag policy doesn't support the following directives:</span></span>

* [<span data-ttu-id="256f5-169">antecesores de marco</span><span class="sxs-lookup"><span data-stu-id="256f5-169">frame-ancestors</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/frame-ancestors)
* [<span data-ttu-id="256f5-170">notificar a</span><span class="sxs-lookup"><span data-stu-id="256f5-170">report-to</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [<span data-ttu-id="256f5-171">URI de informe</span><span class="sxs-lookup"><span data-stu-id="256f5-171">report-uri</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)
* [<span data-ttu-id="256f5-172">aislados</span><span class="sxs-lookup"><span data-stu-id="256f5-172">sandbox</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/sandbox)

<span data-ttu-id="256f5-173">Para admitir las directivas anteriores, use un encabezado denominado `Content-Security-Policy`.</span><span class="sxs-lookup"><span data-stu-id="256f5-173">To support the preceding directives, use a header named `Content-Security-Policy`.</span></span> <span data-ttu-id="256f5-174">La cadena de directiva es el valor del encabezado.</span><span class="sxs-lookup"><span data-stu-id="256f5-174">The directive string is the header's value.</span></span>

## <a name="test-a-policy-and-receive-violation-reports"></a><span data-ttu-id="256f5-175">Probar una directiva y recibir informes de infracción</span><span class="sxs-lookup"><span data-stu-id="256f5-175">Test a policy and receive violation reports</span></span>

<span data-ttu-id="256f5-176">La prueba ayuda a confirmar que los scripts de terceros no se bloquean accidentalmente al crear una directiva inicial.</span><span class="sxs-lookup"><span data-stu-id="256f5-176">Testing helps confirm that third-party scripts aren't inadvertently blocked when building an initial policy.</span></span>

<span data-ttu-id="256f5-177">Para probar una directiva a lo largo de un período de tiempo sin aplicar las directivas de directiva `<meta>` , establezca `http-equiv` el atributo o el nombre de encabezado de la etiqueta de `Content-Security-Policy-Report-Only`una directiva basada en encabezado en.</span><span class="sxs-lookup"><span data-stu-id="256f5-177">To test a policy over a period of time without enforcing the policy directives, set the `<meta>` tag's `http-equiv` attribute or header name of a header-based policy to `Content-Security-Policy-Report-Only`.</span></span> <span data-ttu-id="256f5-178">Los informes de errores se envían como documentos JSON a una dirección URL especificada.</span><span class="sxs-lookup"><span data-stu-id="256f5-178">Failure reports are sent as JSON documents to a specified URL.</span></span> <span data-ttu-id="256f5-179">Para obtener más información, vea [MDN web docs: Content-Security-Policy-Report-Report-Only](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy-Report-Only).</span><span class="sxs-lookup"><span data-stu-id="256f5-179">For more information, see [MDN web docs: Content-Security-Policy-Report-Only](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy-Report-Only).</span></span>

<span data-ttu-id="256f5-180">Para obtener información sobre las infracciones mientras una directiva está activa, consulte los siguientes artículos:</span><span class="sxs-lookup"><span data-stu-id="256f5-180">For reporting on violations while a policy is active, see the following articles:</span></span>

* [<span data-ttu-id="256f5-181">notificar a</span><span class="sxs-lookup"><span data-stu-id="256f5-181">report-to</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [<span data-ttu-id="256f5-182">URI de informe</span><span class="sxs-lookup"><span data-stu-id="256f5-182">report-uri</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)

<span data-ttu-id="256f5-183">Aunque `report-uri` ya no se recomienda para su uso, deben usarse las dos `report-to` directivas hasta que sea compatible con todos los exploradores principales.</span><span class="sxs-lookup"><span data-stu-id="256f5-183">Although `report-uri` is no longer recommended for use, both directives should be used until `report-to` is supported by all of the major browsers.</span></span> <span data-ttu-id="256f5-184">No utilice `report-uri` exclusivamente porque la compatibilidad `report-uri` con está sujeta a la eliminación *en cualquier momento* desde exploradores.</span><span class="sxs-lookup"><span data-stu-id="256f5-184">Don't exclusively use `report-uri` because support for `report-uri` is subject to being dropped *at any time* from browsers.</span></span> <span data-ttu-id="256f5-185">Quite la compatibilidad `report-uri` con en las directivas `report-to` cuando sea totalmente compatible.</span><span class="sxs-lookup"><span data-stu-id="256f5-185">Remove support for `report-uri` in your policies when `report-to` is fully supported.</span></span> <span data-ttu-id="256f5-186">Para realizar un seguimiento `report-to`de la adopción de, consulte [puedo usar: Report-to](https://caniuse.com/#feat=mdn-http_headers_csp_content-security-policy_report-to).</span><span class="sxs-lookup"><span data-stu-id="256f5-186">To track adoption of `report-to`, see [Can I use: report-to](https://caniuse.com/#feat=mdn-http_headers_csp_content-security-policy_report-to).</span></span>

<span data-ttu-id="256f5-187">Probar y actualizar la Directiva de una aplicación en cada versión.</span><span class="sxs-lookup"><span data-stu-id="256f5-187">Test and update an app's policy every release.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="256f5-188">Solución de problemas</span><span class="sxs-lookup"><span data-stu-id="256f5-188">Troubleshoot</span></span>

* <span data-ttu-id="256f5-189">Los errores aparecen en la consola de herramientas de desarrollo del explorador.</span><span class="sxs-lookup"><span data-stu-id="256f5-189">Errors appear in the browser's developer tools console.</span></span> <span data-ttu-id="256f5-190">Los exploradores proporcionan información acerca de:</span><span class="sxs-lookup"><span data-stu-id="256f5-190">Browsers provide information about:</span></span>
  * <span data-ttu-id="256f5-191">Elementos que no cumplen la Directiva.</span><span class="sxs-lookup"><span data-stu-id="256f5-191">Elements that don't comply with the policy.</span></span>
  * <span data-ttu-id="256f5-192">Cómo modificar la Directiva para permitir un elemento bloqueado.</span><span class="sxs-lookup"><span data-stu-id="256f5-192">How to modify the policy to allow for a blocked item.</span></span>
* <span data-ttu-id="256f5-193">Una directiva solo es totalmente efectiva cuando el explorador del cliente admite todas las directivas incluidas.</span><span class="sxs-lookup"><span data-stu-id="256f5-193">A policy is only completely effective when the client's browser supports all of the included directives.</span></span> <span data-ttu-id="256f5-194">Para obtener una matriz de compatibilidad de explorador actual, vea [¿puedo usar: Content-Security-Policy](https://caniuse.com/#search=Content-Security-Policy).</span><span class="sxs-lookup"><span data-stu-id="256f5-194">For a current browser support matrix, see [Can I use: Content-Security-Policy](https://caniuse.com/#search=Content-Security-Policy).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="256f5-195">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="256f5-195">Additional resources</span></span>

* [<span data-ttu-id="256f5-196">Documentos web de MDN: Content-Security-Policy</span><span class="sxs-lookup"><span data-stu-id="256f5-196">MDN web docs: Content-Security-Policy</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy)
* [<span data-ttu-id="256f5-197">Nivel 2 de directiva de seguridad de contenido</span><span class="sxs-lookup"><span data-stu-id="256f5-197">Content Security Policy Level 2</span></span>](https://www.w3.org/TR/CSP2/)
* [<span data-ttu-id="256f5-198">Evaluador de CSP de Google</span><span class="sxs-lookup"><span data-stu-id="256f5-198">Google CSP Evaluator</span></span>](https://csp-evaluator.withgoogle.com/)
