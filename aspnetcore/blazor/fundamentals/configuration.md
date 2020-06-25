---
title: Configuración de Blazor en ASP.NET Core
author: guardrex
description: Obtenga información sobre cómo configurar aplicaciones Blazor, incluidas opciones de configuración de la aplicación, la autenticación y el registro.
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
uid: blazor/fundamentals/configuration
ms.openlocfilehash: b43eae03c71cabbaafa2bc0d704765e89f743279
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103442"
---
# <a name="aspnet-core-blazor-configuration"></a><span data-ttu-id="1b5a2-103">Configuración de Blazor en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1b5a2-103">ASP.NET Core Blazor configuration</span></span>

> [!NOTE]
> <span data-ttu-id="1b5a2-104">Este tema atañe a WebAssembly de Blazor.</span><span class="sxs-lookup"><span data-stu-id="1b5a2-104">This topic applies to Blazor WebAssembly.</span></span> <span data-ttu-id="1b5a2-105">Para obtener instrucciones generales sobre cómo configurar la aplicación ASP.NET Core, vea <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="1b5a2-105">For general guidance on ASP.NET Core app configuration, see <xref:fundamentals/configuration/index>.</span></span>

Blazor<span data-ttu-id="1b5a2-106"> WebAssembly carga la configuración desde:</span><span class="sxs-lookup"><span data-stu-id="1b5a2-106"> WebAssembly loads configuration from:</span></span>

* <span data-ttu-id="1b5a2-107">Archivos de configuración de aplicaciones de forma predeterminada:</span><span class="sxs-lookup"><span data-stu-id="1b5a2-107">App settings files by default:</span></span>
  * <span data-ttu-id="1b5a2-108">*wwwroot/appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="1b5a2-108">*wwwroot/appsettings.json*</span></span>
  * <span data-ttu-id="1b5a2-109">*wwwroot/appsettings.{ENTORNO}.json*</span><span class="sxs-lookup"><span data-stu-id="1b5a2-109">*wwwroot/appsettings.{ENVIRONMENT}.json*</span></span>
* <span data-ttu-id="1b5a2-110">Otros [proveedores de configuración](xref:fundamentals/configuration/index) registrados por la aplicación</span><span class="sxs-lookup"><span data-stu-id="1b5a2-110">Other [configuration providers](xref:fundamentals/configuration/index) registered by the app.</span></span> <span data-ttu-id="1b5a2-111">No todos los proveedores son adecuados para las aplicaciones Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="1b5a2-111">Not all providers are appropriate for Blazor WebAssembly apps.</span></span> <span data-ttu-id="1b5a2-112">En [Explicación de los proveedores de configuración de Blazor WASM (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134), se explican los proveedores que se admiten en Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="1b5a2-112">Clarification on which providers are supported for Blazor WebAssembly is tracked by [Clarify configuration providers for Blazor WASM (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span></span>

> [!WARNING]
> <span data-ttu-id="1b5a2-113">La configuración de una aplicación Blazor WebAssembly es visible para los demás usuarios.</span><span class="sxs-lookup"><span data-stu-id="1b5a2-113">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="1b5a2-114">**No almacene credenciales ni secretos de aplicación en la configuración.**</span><span class="sxs-lookup"><span data-stu-id="1b5a2-114">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="1b5a2-115">Para más información sobre los proveedores de configuración, vea <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="1b5a2-115">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="app-settings-configuration"></a><span data-ttu-id="1b5a2-116">Configuración de aplicaciones</span><span class="sxs-lookup"><span data-stu-id="1b5a2-116">App settings configuration</span></span>

<span data-ttu-id="1b5a2-117">*wwwroot/appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="1b5a2-117">*wwwroot/appsettings.json*:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="1b5a2-118">Inserte una instancia <xref:Microsoft.Extensions.Configuration.IConfiguration> en un componente para acceder a los datos de configuración:</span><span class="sxs-lookup"><span data-stu-id="1b5a2-118">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

## <a name="provider-configuration"></a><span data-ttu-id="1b5a2-119">Configuración de proveedor</span><span class="sxs-lookup"><span data-stu-id="1b5a2-119">Provider configuration</span></span>

<span data-ttu-id="1b5a2-120">En el ejemplo siguiente se usa <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> para proporcionar configuración adicional:</span><span class="sxs-lookup"><span data-stu-id="1b5a2-120">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> to supply additional configuration:</span></span>

<span data-ttu-id="1b5a2-121">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="1b5a2-121">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Configuration.Memory;

...

var vehicleData = new Dictionary<string, string>()
{
    { "color", "blue" },
    { "type", "car" },
    { "wheels:count", "3" },
    { "wheels:brand", "Blazin" },
    { "wheels:brand:type", "rally" },
    { "wheels:year", "2008" },
};

var memoryConfig = new MemoryConfigurationSource { InitialData = vehicleData };

...

builder.Configuration.Add(memoryConfig);
```

<span data-ttu-id="1b5a2-122">Inserte una instancia <xref:Microsoft.Extensions.Configuration.IConfiguration> en un componente para acceder a los datos de configuración:</span><span class="sxs-lookup"><span data-stu-id="1b5a2-122">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<h2>Wheels</h2>

<ul>
    <li>Count: @Configuration["wheels:count"]</li>
    <li>Brand: @Configuration["wheels:brand"]</li>
    <li>Type: @Configuration["wheels:brand:type"]</li>
    <li>Year: @Configuration["wheels:year"]</li>
</ul>

@code {
    var wheelsSection = Configuration.GetSection("wheels");
    
    ...
}
```

<span data-ttu-id="1b5a2-123">Para leer otros archivos de configuración de la carpeta *wwwroot* en la configuración, use <xref:System.Net.Http.HttpClient> para obtener el contenido del archivo.</span><span class="sxs-lookup"><span data-stu-id="1b5a2-123">To read other configuration files from the *wwwroot* folder into configuration, use an <xref:System.Net.Http.HttpClient> to obtain the file's content.</span></span> <span data-ttu-id="1b5a2-124">Al usar este método, el registro del servicio <xref:System.Net.Http.HttpClient> existente puede usar el cliente local creado para leer el archivo, como se muestra en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="1b5a2-124">When using this approach, the existing <xref:System.Net.Http.HttpClient> service registration can use the local client created to read the file, as the following example shows:</span></span>

<span data-ttu-id="1b5a2-125">*wwwroot/cars.json*:</span><span class="sxs-lookup"><span data-stu-id="1b5a2-125">*wwwroot/cars.json*:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="1b5a2-126">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="1b5a2-126">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Configuration;

...

var client = new HttpClient()
{
    BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
};

builder.Services.AddTransient(sp => client);

using var response = await client.GetAsync("cars.json");
using var stream = await response.Content.ReadAsStreamAsync();

builder.Configuration.AddJsonStream(stream);
```

## <a name="authentication-configuration"></a><span data-ttu-id="1b5a2-127">Configuración de autenticación</span><span class="sxs-lookup"><span data-stu-id="1b5a2-127">Authentication configuration</span></span>

<span data-ttu-id="1b5a2-128">*wwwroot/appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="1b5a2-128">*wwwroot/appsettings.json*:</span></span>

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="1b5a2-129">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="1b5a2-129">`Program.Main`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

## <a name="logging-configuration"></a><span data-ttu-id="1b5a2-130">Configuración del registro</span><span class="sxs-lookup"><span data-stu-id="1b5a2-130">Logging configuration</span></span>

<span data-ttu-id="1b5a2-131">Agregue una referencia de paquete para [Microsoft.Extensions.Logging.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/):</span><span class="sxs-lookup"><span data-stu-id="1b5a2-131">Add a package reference for [Microsoft.Extensions.Logging.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/):</span></span>

```xml
<PackageReference Include="Microsoft.Extensions.Logging.Configuration" Version="{VERSION}" />
```

<span data-ttu-id="1b5a2-132">*wwwroot/appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="1b5a2-132">*wwwroot/appsettings.json*:</span></span>

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  }
}
```

<span data-ttu-id="1b5a2-133">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="1b5a2-133">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Logging;

...

builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

## <a name="host-builder-configuration"></a><span data-ttu-id="1b5a2-134">Configuración del generador de host</span><span class="sxs-lookup"><span data-stu-id="1b5a2-134">Host builder configuration</span></span>

<span data-ttu-id="1b5a2-135">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="1b5a2-135">`Program.Main`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

## <a name="cached-configuration"></a><span data-ttu-id="1b5a2-136">Configuración almacenada en caché</span><span class="sxs-lookup"><span data-stu-id="1b5a2-136">Cached configuration</span></span>

<span data-ttu-id="1b5a2-137">Los archivos de configuración se almacenan en caché para usarlos sin conexión.</span><span class="sxs-lookup"><span data-stu-id="1b5a2-137">Configuration files are cached for offline use.</span></span> <span data-ttu-id="1b5a2-138">Con [Aplicaciones web progresivas (PWA)](xref:blazor/progressive-web-app), solo puede actualizar los archivos de configuración al crear una implementación nueva.</span><span class="sxs-lookup"><span data-stu-id="1b5a2-138">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="1b5a2-139">Editar los archivos de configuración entre las implementaciones no tiene ningún efecto, debido a lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="1b5a2-139">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="1b5a2-140">Los usuarios tienen versiones en caché de los archivos que siguen usando.</span><span class="sxs-lookup"><span data-stu-id="1b5a2-140">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="1b5a2-141">Los archivos *service-worker.js* y *service-worker-assets.js* de PWA se deben recompilar en la compilación, lo que indica a la aplicación en la próxima visita en línea del usuario que la aplicación se volvió a implementar.</span><span class="sxs-lookup"><span data-stu-id="1b5a2-141">The PWA's *service-worker.js* and *service-worker-assets.js* files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="1b5a2-142">Para más información sobre cómo PWA administra las actualizaciones en segundo plano, consulte <xref:blazor/progressive-web-app#background-updates>.</span><span class="sxs-lookup"><span data-stu-id="1b5a2-142">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>
