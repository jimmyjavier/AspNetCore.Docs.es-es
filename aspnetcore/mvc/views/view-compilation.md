---
title: Compilación de archivos de Razor en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo se compilan los archivos de Razor en una aplicación ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/13/2020
uid: mvc/views/view-compilation
ms.openlocfilehash: 67bbeb88cd944791b522900b69bd10cff38c9f3a
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277279"
---
# <a name="razor-file-compilation-in-aspnet-core"></a><span data-ttu-id="07513-103">Compilación de archivos de Razor en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="07513-103">Razor file compilation in ASP.NET Core</span></span>

<span data-ttu-id="07513-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="07513-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="07513-105">Los archivos de Razor con una extensión *.cshtml* se compilan en tiempo de compilación y publicación mediante el [SDK de Razor](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="07513-105">Razor files with a *.cshtml* extension are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="07513-106">La compilación en tiempo de ejecución puede habilitarse opcionalmente configurando el proyecto.</span><span class="sxs-lookup"><span data-stu-id="07513-106">Runtime compilation may be optionally enabled by configuring your project.</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="07513-107">Compilación de Razor</span><span class="sxs-lookup"><span data-stu-id="07513-107">Razor compilation</span></span>

<span data-ttu-id="07513-108">El SDK de Razor habilita la compilación en tiempo de compilación y en tiempo de publicación de archivos Razor de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="07513-108">Build-time and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="07513-109">Cuando está habilitada, la compilación en tiempo de ejecución complementa la compilación en tiempo de compilación, lo que permite que los archivos de Razor se actualicen si se editan.</span><span class="sxs-lookup"><span data-stu-id="07513-109">When enabled, runtime compilation complements build-time compilation, allowing Razor files to be updated if they're edited.</span></span>

## <a name="enable-runtime-compilation-at-project-creation"></a><span data-ttu-id="07513-110">Habilitar la compilación en tiempo de ejecución en la creación del proyecto</span><span class="sxs-lookup"><span data-stu-id="07513-110">Enable runtime compilation at project creation</span></span>

<span data-ttu-id="07513-111">Las plantillas de proyecto Razor Pages y MVC incluyen una opción para habilitar la compilación en tiempo de ejecución cuando se crea el proyecto.</span><span class="sxs-lookup"><span data-stu-id="07513-111">The Razor Pages and MVC project templates include an option to enable runtime compilation when the project is created.</span></span> <span data-ttu-id="07513-112">Esta opción se admite en ASP.NET Core 3.1 y versiones posteriores.</span><span class="sxs-lookup"><span data-stu-id="07513-112">This option is supported in ASP.NET Core 3.1 and later.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="07513-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="07513-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="07513-114">En el cuadro de diálogo **Crear una nueva aplicación web ASP.NET Core:**</span><span class="sxs-lookup"><span data-stu-id="07513-114">In the **Create a new ASP.NET Core web application** dialog:</span></span>

1. <span data-ttu-id="07513-115">Seleccione la **aplicación web** o la plantilla de proyecto Aplicación web **(Model-View-Controller).**</span><span class="sxs-lookup"><span data-stu-id="07513-115">Select either the **Web Application** or the **Web Application (Model-View-Controller)** project template.</span></span>
1. <span data-ttu-id="07513-116">Active la casilla Habilitar compilación en tiempo de ejecución de **Razor.**</span><span class="sxs-lookup"><span data-stu-id="07513-116">Select the **Enable Razor runtime compilation** check box.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="07513-117">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="07513-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="07513-118">Utilice `-rrc` la `--razor-runtime-compilation` opción o plantilla.</span><span class="sxs-lookup"><span data-stu-id="07513-118">Use the `-rrc` or `--razor-runtime-compilation` template option.</span></span> <span data-ttu-id="07513-119">Por ejemplo, el siguiente comando crea un nuevo proyecto Razor Pages con la compilación en tiempo de ejecución habilitada:</span><span class="sxs-lookup"><span data-stu-id="07513-119">For example, the following command creates a new Razor Pages project with runtime compilation enabled:</span></span>

```dotnetcli
dotnet new webapp --razor-runtime-compilation
```

---

## <a name="enable-runtime-compilation-in-an-existing-project"></a><span data-ttu-id="07513-120">Habilitar la compilación en tiempo de ejecución en un proyecto existente</span><span class="sxs-lookup"><span data-stu-id="07513-120">Enable runtime compilation in an existing project</span></span>

<span data-ttu-id="07513-121">Para habilitar la compilación en tiempo de ejecución para todos los entornos de un proyecto existente:</span><span class="sxs-lookup"><span data-stu-id="07513-121">To enable runtime compilation for all environments in an existing project:</span></span>

1. <span data-ttu-id="07513-122">Instalar el paquete NuGet [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/).</span><span class="sxs-lookup"><span data-stu-id="07513-122">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>
1. <span data-ttu-id="07513-123">Actualizar el método `Startup.ConfigureServices` del proyecto para incluir una llamada a <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span><span class="sxs-lookup"><span data-stu-id="07513-123">Update the project's `Startup.ConfigureServices` method to include a call to <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span></span> <span data-ttu-id="07513-124">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="07513-124">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

## <a name="conditionally-enable-runtime-compilation-in-an-existing-project"></a><span data-ttu-id="07513-125">Habilitar condicionalmente la compilación en tiempo de ejecución en un proyecto existente</span><span class="sxs-lookup"><span data-stu-id="07513-125">Conditionally enable runtime compilation in an existing project</span></span>

<span data-ttu-id="07513-126">La compilación en tiempo de ejecución se puede habilitar para que solo esté disponible para el desarrollo local.</span><span class="sxs-lookup"><span data-stu-id="07513-126">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="07513-127">Este modo de habilitación condicional garantiza que la salida publicada:</span><span class="sxs-lookup"><span data-stu-id="07513-127">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="07513-128">Usa vistas precompiladas.</span><span class="sxs-lookup"><span data-stu-id="07513-128">Uses compiled views.</span></span>
* <span data-ttu-id="07513-129">No habilita monitores de archivos en producción.</span><span class="sxs-lookup"><span data-stu-id="07513-129">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="07513-130">Para habilitar la compilación en tiempo de ejecución solo en el entorno de desarrollo:</span><span class="sxs-lookup"><span data-stu-id="07513-130">To enable runtime compilation only in the Development environment:</span></span>

1. <span data-ttu-id="07513-131">Instalar el paquete NuGet [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/).</span><span class="sxs-lookup"><span data-stu-id="07513-131">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>
1. <span data-ttu-id="07513-132">Modifique la `environmentVariables` sección de perfil de lanzamiento en *launchSettings.json*:</span><span class="sxs-lookup"><span data-stu-id="07513-132">Modify the launch profile `environmentVariables` section in *launchSettings.json*:</span></span>
    * <span data-ttu-id="07513-133">Verificar `ASPNETCORE_ENVIRONMENT` se `"Development"`establece en .</span><span class="sxs-lookup"><span data-stu-id="07513-133">Verify `ASPNETCORE_ENVIRONMENT` is set to `"Development"`.</span></span>
    * <span data-ttu-id="07513-134">Establezca `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` en `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"`.</span><span class="sxs-lookup"><span data-stu-id="07513-134">Set `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` to `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"`.</span></span>

<span data-ttu-id="07513-135">En el ejemplo siguiente, la compilación en tiempo `IIS Express` `RazorPagesApp` de ejecución está habilitada en el entorno de desarrollo para los perfiles de lanzamiento y el uso de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="07513-135">In the following example, runtime compilation is enabled in the Development environment for the `IIS Express` and `RazorPagesApp` launch profiles:</span></span>

[!code-json[](~/mvc/views/view-compilation/samples/3.1/launchSettings.json?highlight=15-16,24-25)]

<span data-ttu-id="07513-136">No se necesitan cambios de `Startup` código en la clase del proyecto.</span><span class="sxs-lookup"><span data-stu-id="07513-136">No code changes are needed in the project's `Startup` class.</span></span> <span data-ttu-id="07513-137">En tiempo de ejecución, ASP.NET Core busca un [atributo HostingStartup de nivel de ensamblado](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) en `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`.</span><span class="sxs-lookup"><span data-stu-id="07513-137">At runtime, ASP.NET Core searches for an [assembly-level HostingStartup attribute](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) in `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`.</span></span> <span data-ttu-id="07513-138">El `HostingStartup` atributo especifica el código de inicio de la aplicación que se va a ejecutar.</span><span class="sxs-lookup"><span data-stu-id="07513-138">The `HostingStartup` attribute specifies the app startup code to execute.</span></span> <span data-ttu-id="07513-139">Ese código de inicio habilita la compilación en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="07513-139">That startup code enables runtime compilation.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="07513-140">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="07513-140">Additional resources</span></span>

* <span data-ttu-id="07513-141">[Propiedades RazorCompileOnBuild y RazorCompileOnPublish.](xref:razor-pages/sdk#properties)</span><span class="sxs-lookup"><span data-stu-id="07513-141">[RazorCompileOnBuild and RazorCompileOnPublish](xref:razor-pages/sdk#properties) properties.</span></span>
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* <span data-ttu-id="07513-142">Consulte el ejemplo de compilación en tiempo de [ejecución en GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) para obtener un ejemplo que muestra cómo hacer que la compilación en tiempo de ejecución funcione entre proyectos.</span><span class="sxs-lookup"><span data-stu-id="07513-142">See the [runtime compilation sample on GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) for a sample that shows making runtime compilation work across projects.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

<span data-ttu-id="07513-143">Los archivos de Razor con una extensión *.cshtml* se compilan en tiempo de compilación y publicación mediante el [SDK de Razor](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="07513-143">Razor files with a *.cshtml* extension are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="07513-144">La compilación en tiempo de ejecución se puede habilitar opcionalmente mediante la configuración de la aplicación</span><span class="sxs-lookup"><span data-stu-id="07513-144">Runtime compilation may be optionally enabled by configuring your application.</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="07513-145">Compilación de Razor</span><span class="sxs-lookup"><span data-stu-id="07513-145">Razor compilation</span></span>

<span data-ttu-id="07513-146">El SDK de Razor habilita la compilación en tiempo de compilación y en tiempo de publicación de archivos Razor de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="07513-146">Build-time and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="07513-147">Cuando está habilitada, la compilación en tiempo de ejecución complementa la compilación en tiempo de compilación, lo que permite que los archivos de Razor se actualicen si se editan.</span><span class="sxs-lookup"><span data-stu-id="07513-147">When enabled, runtime compilation complements build-time compilation, allowing Razor files to be updated if they're edited.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="07513-148">Compilación en tiempo de ejecución</span><span class="sxs-lookup"><span data-stu-id="07513-148">Runtime compilation</span></span>

<span data-ttu-id="07513-149">Para habilitar la compilación en tiempo de ejecución para todos los entornos y modos de configuración:</span><span class="sxs-lookup"><span data-stu-id="07513-149">To enable runtime compilation for all environments and configuration modes:</span></span>

1. <span data-ttu-id="07513-150">Instalar el paquete NuGet [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/).</span><span class="sxs-lookup"><span data-stu-id="07513-150">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>

1. <span data-ttu-id="07513-151">Actualizar el método `Startup.ConfigureServices` del proyecto para incluir una llamada a <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span><span class="sxs-lookup"><span data-stu-id="07513-151">Update the project's `Startup.ConfigureServices` method to include a call to <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span></span> <span data-ttu-id="07513-152">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="07513-152">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

### <a name="conditionally-enable-runtime-compilation"></a><span data-ttu-id="07513-153">Habilitación condicional de la compilación en tiempo de ejecución</span><span class="sxs-lookup"><span data-stu-id="07513-153">Conditionally enable runtime compilation</span></span>

<span data-ttu-id="07513-154">La compilación en tiempo de ejecución se puede habilitar para que solo esté disponible para el desarrollo local.</span><span class="sxs-lookup"><span data-stu-id="07513-154">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="07513-155">Este modo de habilitación condicional garantiza que la salida publicada:</span><span class="sxs-lookup"><span data-stu-id="07513-155">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="07513-156">Usa vistas precompiladas.</span><span class="sxs-lookup"><span data-stu-id="07513-156">Uses compiled views.</span></span>
* <span data-ttu-id="07513-157">Tiene un tamaño inferior.</span><span class="sxs-lookup"><span data-stu-id="07513-157">Is smaller in size.</span></span>
* <span data-ttu-id="07513-158">No habilita monitores de archivos en producción.</span><span class="sxs-lookup"><span data-stu-id="07513-158">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="07513-159">Para habilitar la compilación en tiempo de ejecución basada en el modo de configuración y el entorno, deberá:</span><span class="sxs-lookup"><span data-stu-id="07513-159">To enable runtime compilation based on the environment and configuration mode:</span></span>

1. <span data-ttu-id="07513-160">Hacer referencia condicional al paquete [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) basado en el valor activo `Configuration`:</span><span class="sxs-lookup"><span data-stu-id="07513-160">Conditionally reference the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) package based on the active `Configuration` value:</span></span>

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation" Version="3.1.0" Condition="'$(Configuration)' == 'Debug'" />
    ```

1. <span data-ttu-id="07513-161">Actualizar el método `Startup.ConfigureServices` del proyecto para incluir una llamada a `AddRazorRuntimeCompilation`.</span><span class="sxs-lookup"><span data-stu-id="07513-161">Update the project's `Startup.ConfigureServices` method to include a call to `AddRazorRuntimeCompilation`.</span></span> <span data-ttu-id="07513-162">Ejecute `AddRazorRuntimeCompilation` de manera condicional de modo que solo se ejecute en modo de depuración cuando la variable `ASPNETCORE_ENVIRONMENT` esté establecida en `Development`:</span><span class="sxs-lookup"><span data-stu-id="07513-162">Conditionally execute `AddRazorRuntimeCompilation` such that it only runs in Debug mode when the `ASPNETCORE_ENVIRONMENT` variable is set to `Development`:</span></span>

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.0/Startup.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="07513-163">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="07513-163">Additional resources</span></span>

* <span data-ttu-id="07513-164">[Propiedades RazorCompileOnBuild y RazorCompileOnPublish.](xref:razor-pages/sdk#properties)</span><span class="sxs-lookup"><span data-stu-id="07513-164">[RazorCompileOnBuild and RazorCompileOnPublish](xref:razor-pages/sdk#properties) properties.</span></span>
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* <span data-ttu-id="07513-165">Consulte el ejemplo de compilación en tiempo de [ejecución en GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) para obtener un ejemplo que muestra cómo hacer que la compilación en tiempo de ejecución funcione entre proyectos.</span><span class="sxs-lookup"><span data-stu-id="07513-165">See the [runtime compilation sample on GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) for a sample that shows making runtime compilation work across projects.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="07513-166">Un archivo de Razor se compila en tiempo de ejecución, cuando se invoca la vista MVC o la página de Razor asociada.</span><span class="sxs-lookup"><span data-stu-id="07513-166">A Razor file is compiled at runtime, when the associated Razor Page or MVC view is invoked.</span></span> <span data-ttu-id="07513-167">Los archivos de Razor se compilan en tiempo de compilación y publicación mediante el [SDK de Razor](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="07513-167">Razor files are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="07513-168">Compilación de Razor</span><span class="sxs-lookup"><span data-stu-id="07513-168">Razor compilation</span></span>

<span data-ttu-id="07513-169">La compilación de los archivos de Razor en tiempo de publicación y compilación está habilitada de manera predeterminada en el SDK de Razor.</span><span class="sxs-lookup"><span data-stu-id="07513-169">Build- and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="07513-170">La edición de los archivos de Razor después de que se actualicen se admite en tiempo de compilación.</span><span class="sxs-lookup"><span data-stu-id="07513-170">Editing Razor files after they're updated is supported at build time.</span></span> <span data-ttu-id="07513-171">De forma predeterminada, solo se implementan con la aplicación los archivos *Views.dll* y los que no son *.cshtml*, o las referencias de los ensamblados necesarios para compilar los archivos de Razor.</span><span class="sxs-lookup"><span data-stu-id="07513-171">By default, only the compiled *Views.dll* and no *.cshtml* files or references assemblies required to compile Razor files are deployed with your app.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="07513-172">La herramienta de precompilación está en desuso y se eliminará en ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="07513-172">The precompilation tool has been deprecated, and will be removed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="07513-173">Se recomienda migrar al [SDK de Razor](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="07513-173">We recommend migrating to [Razor Sdk](xref:razor-pages/sdk).</span></span>
>
> <span data-ttu-id="07513-174">El SDK de Razor solo es efectivo cuando no hay propiedades específicas de la precompilación establecidas en el archivo de proyecto.</span><span class="sxs-lookup"><span data-stu-id="07513-174">The Razor SDK is effective only when no precompilation-specific properties are set in the project file.</span></span> <span data-ttu-id="07513-175">Por ejemplo, establecer la propiedad `MvcRazorCompileOnPublish` del archivo *.csproj* en `true` deshabilita el SDK de Razor.</span><span class="sxs-lookup"><span data-stu-id="07513-175">For instance, setting the *.csproj* file's `MvcRazorCompileOnPublish` property to `true` disables the Razor SDK.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="07513-176">Compilación en tiempo de ejecución</span><span class="sxs-lookup"><span data-stu-id="07513-176">Runtime compilation</span></span>

<span data-ttu-id="07513-177">La compilación en tiempo de compilación se complementa con la compilación en tiempo de ejecución de archivos de Razor.</span><span class="sxs-lookup"><span data-stu-id="07513-177">Build-time compilation is supplemented by runtime compilation of Razor files.</span></span> <span data-ttu-id="07513-178">ASP.NET Core MVC volverá a compilar los archivos de Razor cuando cambie el contenido de un archivo *.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="07513-178">ASP.NET Core MVC will recompile Razor files when the contents of a *.cshtml* file change.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="07513-179">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="07513-179">Additional resources</span></span>

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end
