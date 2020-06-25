---
title: Entornos de Blazor de ASP.NET Core
author: guardrex
description: Obtenga información sobre los entornos en Blazor, incluido cómo configurar el entorno de una aplicación WebAssembly de Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/environments
ms.openlocfilehash: 203f29ce606a313463e416b068177ce02acd6231
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103437"
---
# <a name="aspnet-core-blazor-environments"></a><span data-ttu-id="98581-103">Entornos de Blazor de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="98581-103">ASP.NET Core Blazor environments</span></span>

> [!NOTE]
> <span data-ttu-id="98581-104">Este tema atañe a WebAssembly de Blazor.</span><span class="sxs-lookup"><span data-stu-id="98581-104">This topic applies to Blazor WebAssembly.</span></span> <span data-ttu-id="98581-105">Para obtener instrucciones generales sobre cómo configurar la aplicación ASP.NET Core, vea <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="98581-105">For general guidance on ASP.NET Core app configuration, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="98581-106">Cuando una aplicación se ejecuta de manera local, el entorno se establece de manera predeterminada en Desarrollo.</span><span class="sxs-lookup"><span data-stu-id="98581-106">When running an app locally, the environment defaults to Development.</span></span> <span data-ttu-id="98581-107">Cuando la aplicación se publica, el entorno se establece de manera predeterminada en Producción.</span><span class="sxs-lookup"><span data-stu-id="98581-107">When the app is published, the environment defaults to Production.</span></span>

<span data-ttu-id="98581-108">Una aplicación Blazor WebAssembly hospedada selecciona el entorno desde el servidor a través de un middleware que comunica el entorno con el explorador mediante la adición del encabezado `blazor-environment`.</span><span class="sxs-lookup"><span data-stu-id="98581-108">A hosted Blazor WebAssembly app picks up the environment from the server via a middleware that communicates the environment to the browser by adding the `blazor-environment` header.</span></span> <span data-ttu-id="98581-109">El valor del encabezado es el entorno.</span><span class="sxs-lookup"><span data-stu-id="98581-109">The value of the header is the environment.</span></span> <span data-ttu-id="98581-110">La aplicación Blazor hospedada y la aplicación de servidor comparten el mismo entorno.</span><span class="sxs-lookup"><span data-stu-id="98581-110">The hosted Blazor app and the server app share the same environment.</span></span> <span data-ttu-id="98581-111">Para más información, incluida la manera de configurar el entorno, consulte <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="98581-111">For more information, including how to configure the environment, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="98581-112">En el caso de una aplicación independiente que se ejecuta localmente, el servidor de desarrollo agrega el encabezado `blazor-environment` para especificar el entorno de Desarrollo.</span><span class="sxs-lookup"><span data-stu-id="98581-112">For a standalone app running locally, the development server adds the `blazor-environment` header to specify the Development environment.</span></span> <span data-ttu-id="98581-113">Para especificar el entorno para otros entornos de hospedaje, agregue el encabezado `blazor-environment`.</span><span class="sxs-lookup"><span data-stu-id="98581-113">To specify the environment for other hosting environments, add the `blazor-environment` header.</span></span>

<span data-ttu-id="98581-114">En el ejemplo siguiente de IIS, agregue el encabezado personalizado al archivo *web.config* publicado.</span><span class="sxs-lookup"><span data-stu-id="98581-114">In the following example for IIS, add the custom header to the published *web.config* file.</span></span> <span data-ttu-id="98581-115">El archivo *web.config* se encuentra en la carpeta *bin/Release/{PLATAFORMA DE DESTINO}/publish*:</span><span class="sxs-lookup"><span data-stu-id="98581-115">The *web.config* file is located in the *bin/Release/{TARGET FRAMEWORK}/publish* folder:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>

    ...

    <httpProtocol>
      <customHeaders>
        <add name="blazor-environment" value="Staging" />
      </customHeaders>
    </httpProtocol>
  </system.webServer>
</configuration>
```

> [!NOTE]
> <span data-ttu-id="98581-116">Si quiere usar un archivo *web.config* personalizado para IIS que no se sobrescriba cuando la aplicación se publique en la carpeta *publish*, consulte <xref:blazor/host-and-deploy/webassembly#use-a-custom-webconfig>.</span><span class="sxs-lookup"><span data-stu-id="98581-116">To use a custom *web.config* file for IIS that isn't overwritten when the app is published to the *publish* folder, see <xref:blazor/host-and-deploy/webassembly#use-a-custom-webconfig>.</span></span>

<span data-ttu-id="98581-117">Para obtener el entorno de la aplicación en un componente, inserte <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> y lea la propiedad <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment>:</span><span class="sxs-lookup"><span data-stu-id="98581-117">Obtain the app's environment in a component by injecting <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> and reading the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> property:</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

<span data-ttu-id="98581-118">Durante el inicio, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> expone el <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> a través de la propiedad <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment>, lo que permite que los desarrolladores tengan una lógica específica del entorno en el código:</span><span class="sxs-lookup"><span data-stu-id="98581-118">During startup, the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> exposes the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> through the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> property, which enables developers to have environment-specific logic in their code:</span></span>

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

<span data-ttu-id="98581-119">Los métodos de extensión útiles siguientes permiten comprobar el entorno actual para Desarrollo, Producción, Ensayo y nombres de entornos personalizados:</span><span class="sxs-lookup"><span data-stu-id="98581-119">The following convenience extension methods permit checking the current environment for Development, Production, Staging, and custom environment names:</span></span>

* `IsDevelopment()`
* `IsProduction()`
* `IsStaging()`
* `IsEnvironment("{ENVIRONMENT NAME}")`

```csharp
if (builder.HostEnvironment.IsStaging())
{
    ...
};

if (builder.HostEnvironment.IsEnvironment("Custom"))
{
    ...
};
```

<span data-ttu-id="98581-120">La propiedad <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> se puede usar durante el inicio cuando el servicio <xref:Microsoft.AspNetCore.Components.NavigationManager> no está disponible.</span><span class="sxs-lookup"><span data-stu-id="98581-120">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> property can be used during startup when the <xref:Microsoft.AspNetCore.Components.NavigationManager> service isn't available.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="98581-121">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="98581-121">Additional resources</span></span>

* <xref:fundamentals/environments>