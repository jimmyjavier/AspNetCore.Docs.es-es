---
title: Compilación de archivos de Razor en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo se compilan los archivos de Razor en una aplicación ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 4/8/2020
uid: mvc/views/view-compilation
ms.openlocfilehash: 0afd39fdb5a6f570e0e78ad54f6c436460bad3a6
ms.sourcegitcommit: 6f1b516e0c899a49afe9a29044a2383ce2ada3c7
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/13/2020
ms.locfileid: "81223964"
---
# <a name="razor-file-compilation-in-aspnet-core"></a><span data-ttu-id="1937b-103">Compilación de archivos de Razor en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1937b-103">Razor file compilation in ASP.NET Core</span></span>

<span data-ttu-id="1937b-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="1937b-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1937b-105">Los archivos de Razor con una extensión *.cshtml* se compilan en tiempo de compilación y publicación mediante el [SDK de Razor](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="1937b-105">Razor files with a *.cshtml* extension are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="1937b-106">La compilación en tiempo de ejecución se puede habilitar opcionalmente mediante la configuración de la aplicación</span><span class="sxs-lookup"><span data-stu-id="1937b-106">Runtime compilation may be optionally enabled by configuring your application.</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="1937b-107">Compilación de Razor</span><span class="sxs-lookup"><span data-stu-id="1937b-107">Razor compilation</span></span>

<span data-ttu-id="1937b-108">La compilación de los archivos de Razor en tiempo de publicación y compilación está habilitada de manera predeterminada en el SDK de Razor.</span><span class="sxs-lookup"><span data-stu-id="1937b-108">Build- and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="1937b-109">Cuando se habilita la compilación en tiempo de ejecución, complementa a la de tiempo de compilación y permite que se actualicen los archivos de Razor si se modifican.</span><span class="sxs-lookup"><span data-stu-id="1937b-109">When enabled, runtime compilation complements build-time compilation, allowing Razor files to be updated if they are edited.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="1937b-110">Compilación en tiempo de ejecución</span><span class="sxs-lookup"><span data-stu-id="1937b-110">Runtime compilation</span></span>

<span data-ttu-id="1937b-111">Para habilitar la compilación en tiempo de ejecución para todos los entornos y modos de configuración:</span><span class="sxs-lookup"><span data-stu-id="1937b-111">To enable runtime compilation for all environments and configuration modes:</span></span>

1. <span data-ttu-id="1937b-112">Instalar el paquete NuGet [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/).</span><span class="sxs-lookup"><span data-stu-id="1937b-112">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>

1. <span data-ttu-id="1937b-113">Actualizar el método `Startup.ConfigureServices` del proyecto para incluir una llamada a <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span><span class="sxs-lookup"><span data-stu-id="1937b-113">Update the project's `Startup.ConfigureServices` method to include a call to <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span></span> <span data-ttu-id="1937b-114">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="1937b-114">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

### <a name="conditionally-enable-runtime-compilation"></a><span data-ttu-id="1937b-115">Habilitación condicional de la compilación en tiempo de ejecución</span><span class="sxs-lookup"><span data-stu-id="1937b-115">Conditionally enable runtime compilation</span></span>

<span data-ttu-id="1937b-116">La compilación en tiempo de ejecución se puede habilitar para que solo esté disponible para el desarrollo local.</span><span class="sxs-lookup"><span data-stu-id="1937b-116">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="1937b-117">Este modo de habilitación condicional garantiza que la salida publicada:</span><span class="sxs-lookup"><span data-stu-id="1937b-117">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="1937b-118">Usa vistas precompiladas.</span><span class="sxs-lookup"><span data-stu-id="1937b-118">Uses compiled views.</span></span>
* <span data-ttu-id="1937b-119">Tiene un tamaño inferior.</span><span class="sxs-lookup"><span data-stu-id="1937b-119">Is smaller in size.</span></span>
* <span data-ttu-id="1937b-120">No habilita monitores de archivos en producción.</span><span class="sxs-lookup"><span data-stu-id="1937b-120">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="1937b-121">Para habilitar la compilación en tiempo de ejecución basada en el modo de configuración y el entorno, deberá:</span><span class="sxs-lookup"><span data-stu-id="1937b-121">To enable runtime compilation based on the environment and configuration mode:</span></span>

1. <span data-ttu-id="1937b-122">Hacer referencia condicional al paquete [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) basado en el valor activo `Configuration`:</span><span class="sxs-lookup"><span data-stu-id="1937b-122">Conditionally reference the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) package based on the active `Configuration` value:</span></span>

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation" Version="3.1.0" Condition="'$(Configuration)' == 'Debug'" />
    ```

1. <span data-ttu-id="1937b-123">Actualizar el método `Startup.ConfigureServices` del proyecto para incluir una llamada a `AddRazorRuntimeCompilation`.</span><span class="sxs-lookup"><span data-stu-id="1937b-123">Update the project's `Startup.ConfigureServices` method to include a call to `AddRazorRuntimeCompilation`.</span></span> <span data-ttu-id="1937b-124">Ejecute `AddRazorRuntimeCompilation` de manera condicional de modo que solo se ejecute en modo de depuración cuando la variable `ASPNETCORE_ENVIRONMENT` esté establecida en `Development`:</span><span class="sxs-lookup"><span data-stu-id="1937b-124">Conditionally execute `AddRazorRuntimeCompilation` such that it only runs in Debug mode when the `ASPNETCORE_ENVIRONMENT` variable is set to `Development`:</span></span>

  [!code-csharp[](~/mvc/views/view-compilation/sample/Startup.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="1937b-125">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="1937b-125">Additional resources</span></span>

* <span data-ttu-id="1937b-126">[Propiedades RazorCompileOnBuild y RazorCompileOnPublish.](xref:razor-pages/sdk#properties)</span><span class="sxs-lookup"><span data-stu-id="1937b-126">[RazorCompileOnBuild and RazorCompileOnPublish](xref:razor-pages/sdk#properties) properties.</span></span>
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* <span data-ttu-id="1937b-127">Consulte el [ejemplo runtimecompilation en GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) para obtener un ejemplo que muestra cómo hacer que la compilación en tiempo de ejecución funcione entre proyectos.</span><span class="sxs-lookup"><span data-stu-id="1937b-127">See the [runtimecompilation sample on GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) for a sample that shows making runtime compilation work across projects.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1937b-128">Un archivo de Razor se compila en tiempo de ejecución, cuando se invoca la vista MVC o la página de Razor asociada.</span><span class="sxs-lookup"><span data-stu-id="1937b-128">A Razor file is compiled at runtime, when the associated Razor Page or MVC view is invoked.</span></span> <span data-ttu-id="1937b-129">Los archivos de Razor se compilan en tiempo de compilación y publicación mediante el [SDK de Razor](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="1937b-129">Razor files are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="1937b-130">Compilación de Razor</span><span class="sxs-lookup"><span data-stu-id="1937b-130">Razor compilation</span></span>

<span data-ttu-id="1937b-131">La compilación de los archivos de Razor en tiempo de publicación y compilación está habilitada de manera predeterminada en el SDK de Razor.</span><span class="sxs-lookup"><span data-stu-id="1937b-131">Build- and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="1937b-132">La edición de los archivos de Razor después de que se actualicen se admite en tiempo de compilación.</span><span class="sxs-lookup"><span data-stu-id="1937b-132">Editing Razor files after they're updated is supported at build time.</span></span> <span data-ttu-id="1937b-133">De forma predeterminada, solo se implementan con la aplicación los archivos *Views.dll* y los que no son *.cshtml*, o las referencias de los ensamblados necesarios para compilar los archivos de Razor.</span><span class="sxs-lookup"><span data-stu-id="1937b-133">By default, only the compiled *Views.dll* and no *.cshtml* files or references assemblies required to compile Razor files are deployed with your app.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="1937b-134">La herramienta de precompilación está en desuso y se eliminará en ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="1937b-134">The precompilation tool has been deprecated, and will be removed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="1937b-135">Se recomienda migrar al [SDK de Razor](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="1937b-135">We recommend migrating to [Razor Sdk](xref:razor-pages/sdk).</span></span>
>
> <span data-ttu-id="1937b-136">El SDK de Razor solo es efectivo cuando no hay propiedades específicas de la precompilación establecidas en el archivo de proyecto.</span><span class="sxs-lookup"><span data-stu-id="1937b-136">The Razor SDK is effective only when no precompilation-specific properties are set in the project file.</span></span> <span data-ttu-id="1937b-137">Por ejemplo, establecer la propiedad `MvcRazorCompileOnPublish` del archivo *.csproj* en `true` deshabilita el SDK de Razor.</span><span class="sxs-lookup"><span data-stu-id="1937b-137">For instance, setting the *.csproj* file's `MvcRazorCompileOnPublish` property to `true` disables the Razor SDK.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="1937b-138">Compilación en tiempo de ejecución</span><span class="sxs-lookup"><span data-stu-id="1937b-138">Runtime compilation</span></span>

<span data-ttu-id="1937b-139">La compilación en tiempo de compilación se complementa con la compilación en tiempo de ejecución de archivos de Razor.</span><span class="sxs-lookup"><span data-stu-id="1937b-139">Build-time compilation is supplemented by runtime compilation of Razor files.</span></span> <span data-ttu-id="1937b-140">ASP.NET Core MVC volverá a compilar los archivos de Razor cuando cambie el contenido de un archivo *.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="1937b-140">ASP.NET Core MVC will recompile Razor files when the contents of a *.cshtml* file change.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1937b-141">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="1937b-141">Additional resources</span></span>

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end
