---
title: Hospedaje e implementación de ASP.NET Core Blazor
author: guardrex
description: Descubra cómo hospedar e implementar aplicaciones de Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/host-and-deploy/index
ms.openlocfilehash: 3a3c5ab5365e5b4312dd3fd516f4906155911cc9
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103154"
---
# <a name="host-and-deploy-aspnet-core-blazor"></a><span data-ttu-id="829fc-103">Hospedaje e implementación de ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="829fc-103">Host and deploy ASP.NET Core Blazor</span></span>

<span data-ttu-id="829fc-104">Por [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) y [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="829fc-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="publish-the-app"></a><span data-ttu-id="829fc-105">Publicar la aplicación</span><span class="sxs-lookup"><span data-stu-id="829fc-105">Publish the app</span></span>

<span data-ttu-id="829fc-106">Las aplicaciones se publican para implementación en la configuración de versión.</span><span class="sxs-lookup"><span data-stu-id="829fc-106">Apps are published for deployment in Release configuration.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="829fc-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="829fc-107">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="829fc-108">Seleccione **Compilar** > **Publicar {aplicación}** en la barra de navegación.</span><span class="sxs-lookup"><span data-stu-id="829fc-108">Select **Build** > **Publish {APPLICATION}** from the navigation bar.</span></span>
1. <span data-ttu-id="829fc-109">Seleccione el *destino de publicación*.</span><span class="sxs-lookup"><span data-stu-id="829fc-109">Select the *publish target*.</span></span> <span data-ttu-id="829fc-110">Para publicar localmente, seleccione **Carpeta**.</span><span class="sxs-lookup"><span data-stu-id="829fc-110">To publish locally, select **Folder**.</span></span>
1. <span data-ttu-id="829fc-111">Acepte la ubicación predeterminada del campo **Elegir una carpeta** o especifique una ubicación diferente.</span><span class="sxs-lookup"><span data-stu-id="829fc-111">Accept the default location in the **Choose a folder** field or specify a different location.</span></span> <span data-ttu-id="829fc-112">Seleccione el botón **Publicar**.</span><span class="sxs-lookup"><span data-stu-id="829fc-112">Select the **Publish** button.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="829fc-113">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="829fc-113">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="829fc-114">Seleccione **Compilar** > **Publicar en carpeta**.</span><span class="sxs-lookup"><span data-stu-id="829fc-114">Select **Build** > **Publish to Folder**.</span></span>
1. <span data-ttu-id="829fc-115">Confirme la carpeta para recibir los recursos publicados y seleccione **Publicar**.</span><span class="sxs-lookup"><span data-stu-id="829fc-115">Confirm the folder to receive the published assets and select **Publish**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="829fc-116">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="829fc-116">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="829fc-117">Use el comando [dotnet publish](/dotnet/core/tools/dotnet-publish) para publicar la aplicación con una configuración de versión:</span><span class="sxs-lookup"><span data-stu-id="829fc-117">Use the [dotnet publish](/dotnet/core/tools/dotnet-publish) command to publish the app with a Release configuration:</span></span>

```dotnetcli
dotnet publish -c Release
```

---

<span data-ttu-id="829fc-118">Al publicar la aplicación se desencadena una [restauración](/dotnet/core/tools/dotnet-restore) de las dependencias del proyecto y se [compila](/dotnet/core/tools/dotnet-build) el proyecto antes de crear los recursos para la implementación.</span><span class="sxs-lookup"><span data-stu-id="829fc-118">Publishing the app triggers a [restore](/dotnet/core/tools/dotnet-restore) of the project's dependencies and [builds](/dotnet/core/tools/dotnet-build) the project before creating the assets for deployment.</span></span> <span data-ttu-id="829fc-119">Como parte del proceso de compilación, se quitan los ensamblados y métodos que no se usan para reducir los tiempos de carga y el tamaño de descarga de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="829fc-119">As part of the build process, unused methods and assemblies are removed to reduce app download size and load times.</span></span>

<span data-ttu-id="829fc-120">Ubicaciones de publicación:</span><span class="sxs-lookup"><span data-stu-id="829fc-120">Publish locations:</span></span>

* Blazor<span data-ttu-id="829fc-121"> WebAssembly</span><span class="sxs-lookup"><span data-stu-id="829fc-121"> WebAssembly</span></span>
  * <span data-ttu-id="829fc-122">Independiente: la aplicación se publica en la carpeta */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="829fc-122">Standalone: The app is published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder.</span></span> <span data-ttu-id="829fc-123">Para implementar la aplicación como un sitio estático, copie el contenido de la carpeta *wwwroot* en el host del sitio estático.</span><span class="sxs-lookup"><span data-stu-id="829fc-123">To deploy the app as a static site, copy the contents of the *wwwroot* folder to the static site host.</span></span>
  * <span data-ttu-id="829fc-124">Hospedada: La aplicación WebAssembly del cliente Blazor se publica en la carpeta */bin/Release/{MARCO DE DESTINO}/publish/wwwroot* de la aplicación de servidor, junto con cualquier otro recurso web estático de la aplicación de servidor.</span><span class="sxs-lookup"><span data-stu-id="829fc-124">Hosted: The client Blazor WebAssembly app is published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="829fc-125">Implemente el contenido de la carpeta *publish* en el host.</span><span class="sxs-lookup"><span data-stu-id="829fc-125">Deploy the contents of the *publish* folder to the host.</span></span>
* Blazor<span data-ttu-id="829fc-126"> Server: la aplicación se publica en la carpeta */bin/Release/{TARGET FRAMEWORK}/publish*.</span><span class="sxs-lookup"><span data-stu-id="829fc-126"> Server: The app is published into the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span> <span data-ttu-id="829fc-127">Implemente el contenido de la carpeta *publish* en el host.</span><span class="sxs-lookup"><span data-stu-id="829fc-127">Deploy the contents of the *publish* folder to the host.</span></span>

<span data-ttu-id="829fc-128">Los recursos de la carpeta se implementan en el servidor web.</span><span class="sxs-lookup"><span data-stu-id="829fc-128">The assets in the folder are deployed to the web server.</span></span> <span data-ttu-id="829fc-129">La implementación puede ser un proceso manual o automatizado, en función de las herramientas de desarrollo que se usen.</span><span class="sxs-lookup"><span data-stu-id="829fc-129">Deployment might be a manual or automated process depending on the development tools in use.</span></span>

## <a name="app-base-path"></a><span data-ttu-id="829fc-130">Ruta de acceso base de la aplicación</span><span class="sxs-lookup"><span data-stu-id="829fc-130">App base path</span></span>

<span data-ttu-id="829fc-131">La *ruta de acceso base de la aplicación* es la de la dirección URL raíz de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="829fc-131">The *app base path* is the app's root URL path.</span></span> <span data-ttu-id="829fc-132">Tenga en cuenta la siguiente aplicación de ASP.NET Core y la subaplicación de Blazor:</span><span class="sxs-lookup"><span data-stu-id="829fc-132">Consider the following ASP.NET Core app and Blazor sub-app:</span></span>

* <span data-ttu-id="829fc-133">La aplicación de ASP.NET Core se denomina `MyApp`:</span><span class="sxs-lookup"><span data-stu-id="829fc-133">The ASP.NET Core app is named `MyApp`:</span></span>
  * <span data-ttu-id="829fc-134">La aplicación reside físicamente en *d:/MyApp*.</span><span class="sxs-lookup"><span data-stu-id="829fc-134">The app physically resides at *d:/MyApp*.</span></span>
  * <span data-ttu-id="829fc-135">Las solicitudes se reciben en `https://www.contoso.com/{MYAPP RESOURCE}`.</span><span class="sxs-lookup"><span data-stu-id="829fc-135">Requests are received at `https://www.contoso.com/{MYAPP RESOURCE}`.</span></span>
* <span data-ttu-id="829fc-136">Una aplicación de Blazor denominada `CoolApp` es una subaplicación de `MyApp`:</span><span class="sxs-lookup"><span data-stu-id="829fc-136">A Blazor app named `CoolApp` is a sub-app of `MyApp`:</span></span>
  * <span data-ttu-id="829fc-137">La subaplicación reside físicamente en *d:/MyApp/CoolApp*.</span><span class="sxs-lookup"><span data-stu-id="829fc-137">The sub-app physically resides at *d:/MyApp/CoolApp*.</span></span>
  * <span data-ttu-id="829fc-138">Las solicitudes se reciben en `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}`.</span><span class="sxs-lookup"><span data-stu-id="829fc-138">Requests are received at `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}`.</span></span>

<span data-ttu-id="829fc-139">Sin especificar una configuración adicional para `CoolApp`, la subaplicación de este escenario desconoce dónde reside en el servidor.</span><span class="sxs-lookup"><span data-stu-id="829fc-139">Without specifying additional configuration for `CoolApp`, the sub-app in this scenario has no knowledge of where it resides on the server.</span></span> <span data-ttu-id="829fc-140">Por ejemplo, la aplicación no puede construir URL relativas correctas para sus recursos sin saber que reside en la ruta de acceso URL relativa `/CoolApp/`.</span><span class="sxs-lookup"><span data-stu-id="829fc-140">For example, the app can't construct correct relative URLs to its resources without knowing that it resides at the relative URL path `/CoolApp/`.</span></span>

<span data-ttu-id="829fc-141">Para proporcionar la configuración de la ruta de acceso base de la aplicación de Blazor de `https://www.contoso.com/CoolApp/`, el atributo `href` de la etiqueta `<base>` se establece en la ruta de acceso raíz relativa del archivo *Pages/_Host.cshtml* (servidor de Blazor) o el archivo *wwwroot/index.html* (WebAssembly de Blazor):</span><span class="sxs-lookup"><span data-stu-id="829fc-141">To provide configuration for the Blazor app's base path of `https://www.contoso.com/CoolApp/`, the `<base>` tag's `href` attribute is set to the relative root path in the *Pages/_Host.cshtml* file (Blazor Server) or *wwwroot/index.html* file (Blazor WebAssembly):</span></span>

```html
<base href="/CoolApp/">
```

<span data-ttu-id="829fc-142">Las aplicaciones de servidor de Blazor establecen además la ruta de acceso base del servidor mediante una llamada a <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> en la canalización de solicitudes de la aplicación de `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="829fc-142">Blazor Server apps additionally set the server-side base path by calling <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> in the app's request pipeline of `Startup.Configure`:</span></span>

```csharp
app.UsePathBase("/CoolApp");
```

<span data-ttu-id="829fc-143">Al proporcionar la ruta de acceso URL relativa, un componente que no se encuentre en el directorio raíz puede construir direcciones URL relativas a la ruta de acceso raíz de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="829fc-143">By providing the relative URL path, a component that isn't in the root directory can construct URLs relative to the app's root path.</span></span> <span data-ttu-id="829fc-144">Los componentes que se encuentran en diferentes niveles de la estructura de directorios pueden compilar vínculos a otros recursos en ubicaciones de toda la aplicación.</span><span class="sxs-lookup"><span data-stu-id="829fc-144">Components at different levels of the directory structure can build links to other resources at locations throughout the app.</span></span> <span data-ttu-id="829fc-145">La ruta de acceso base de la aplicación también se usa para interceptar hipervínculos seleccionados en los que el destino `href` del vínculo está dentro del espacio de URI de la ruta de acceso base de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="829fc-145">The app base path is also used to intercept selected hyperlinks where the `href` target of the link is within the app base path URI space.</span></span> <span data-ttu-id="829fc-146">El enrutador de Blazor controla la navegación interna.</span><span class="sxs-lookup"><span data-stu-id="829fc-146">The Blazor router handles the internal navigation.</span></span>

<span data-ttu-id="829fc-147">En muchos escenarios de hospedaje, la ruta de acceso URL relativa a la aplicación es la raíz de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="829fc-147">In many hosting scenarios, the relative URL path to the app is the root of the app.</span></span> <span data-ttu-id="829fc-148">En estos casos, la ruta de acceso base URL relativa de la aplicación es una barra diagonal (`<base href="/" />`), que es la configuración predeterminada para una aplicación de Blazor.</span><span class="sxs-lookup"><span data-stu-id="829fc-148">In these cases, the app's relative URL base path is a forward slash (`<base href="/" />`), which is the default configuration for a Blazor app.</span></span> <span data-ttu-id="829fc-149">En otros escenarios de hospedaje, como las subaplicaciones de IIS y GitHub Pages, la ruta de acceso base de la aplicación debe establecerse en la ruta de acceso URL relativa del servidor de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="829fc-149">In other hosting scenarios, such as GitHub Pages and IIS sub-apps, the app base path must be set to the server's relative URL path of the app.</span></span>

<span data-ttu-id="829fc-150">Para establecer la ruta de acceso base de la aplicación, actualice la etiqueta `<base>` dentro de los elementos de etiqueta `<head>` del archivo *Pages/_Host.cshtml* (servidor de Blazor) o el archivo *wwwroot/index.html* (WebAssembly de Blazor).</span><span class="sxs-lookup"><span data-stu-id="829fc-150">To set the app's base path, update the `<base>` tag within the `<head>` tag elements of the *Pages/_Host.cshtml* file (Blazor Server) or *wwwroot/index.html* file (Blazor WebAssembly).</span></span> <span data-ttu-id="829fc-151">Establezca el valor del atributo `href` en `/{RELATIVE URL PATH}/` (la barra diagonal final es necesaria), donde `{RELATIVE URL PATH}` es la ruta de acceso URL relativa completa de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="829fc-151">Set the `href` attribute value to `/{RELATIVE URL PATH}/` (the trailing slash is required), where `{RELATIVE URL PATH}` is the app's full relative URL path.</span></span>

<span data-ttu-id="829fc-152">En el caso de una aplicación WebAssembly de Blazor con una ruta de acceso URL relativa que no sea raíz (por ejemplo, `<base href="/CoolApp/">`), la aplicación no encuentra sus recursos *si se ejecuta de forma local*.</span><span class="sxs-lookup"><span data-stu-id="829fc-152">For an Blazor WebAssembly app with a non-root relative URL path (for example, `<base href="/CoolApp/">`), the app fails to find its resources *when run locally*.</span></span> <span data-ttu-id="829fc-153">Para solucionar este problema durante la fase de desarrollo y pruebas local, puede proporcionar un argumento de *ruta de acceso base* que coincida con el valor `href` de la etiqueta `<base>` en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="829fc-153">To overcome this problem during local development and testing, you can supply a *path base* argument that matches the `href` value of the `<base>` tag at runtime.</span></span> <span data-ttu-id="829fc-154">No incluya una barra diagonal final.</span><span class="sxs-lookup"><span data-stu-id="829fc-154">Don't include a trailing slash.</span></span> <span data-ttu-id="829fc-155">Para pasar el argumento de ruta de acceso base al ejecutar la aplicación de forma local, ejecute el comando `dotnet run` desde el directorio de la aplicación con la opción `--pathbase`:</span><span class="sxs-lookup"><span data-stu-id="829fc-155">To pass the path base argument when running the app locally, execute the `dotnet run` command from the app's directory with the `--pathbase` option:</span></span>

```dotnetcli
dotnet run --pathbase=/{RELATIVE URL PATH (no trailing slash)}
```

<span data-ttu-id="829fc-156">En el caso de una aplicación WebAssembly de Blazor con una ruta de acceso URL relativa de `/CoolApp/` (`<base href="/CoolApp/">`), el comando es:</span><span class="sxs-lookup"><span data-stu-id="829fc-156">For a Blazor WebAssembly app with a relative URL path of `/CoolApp/` (`<base href="/CoolApp/">`), the command is:</span></span>

```dotnetcli
dotnet run --pathbase=/CoolApp
```

<span data-ttu-id="829fc-157">La aplicación WebAssembly de Blazor responde de forma local en `http://localhost:port/CoolApp`.</span><span class="sxs-lookup"><span data-stu-id="829fc-157">The Blazor WebAssembly app responds locally at `http://localhost:port/CoolApp`.</span></span>

## <a name="deployment"></a><span data-ttu-id="829fc-158">Implementación</span><span class="sxs-lookup"><span data-stu-id="829fc-158">Deployment</span></span>

<span data-ttu-id="829fc-159">Para una guía sobre la implementación, consulte los temas siguientes:</span><span class="sxs-lookup"><span data-stu-id="829fc-159">For deployment guidance, see the following topics:</span></span>

* <xref:blazor/host-and-deploy/webassembly>
* <xref:blazor/host-and-deploy/server>
