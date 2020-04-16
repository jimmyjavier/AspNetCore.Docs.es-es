---
title: Compilación de archivos de Razor en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo se compilan los archivos de Razor en una aplicación ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/14/2020
uid: mvc/views/view-compilation
ms.openlocfilehash: 3d871ab960de28a565280d9e4cb2c597832e2455
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440940"
---
# <a name="razor-file-compilation-in-aspnet-core"></a><span data-ttu-id="6f755-103">Compilación de archivos de Razor en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6f755-103">Razor file compilation in ASP.NET Core</span></span>

<span data-ttu-id="6f755-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="6f755-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="6f755-105">Los archivos de Razor con una extensión *.cshtml* se compilan en tiempo de compilación y publicación mediante el [SDK de Razor](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="6f755-105">Razor files with a *.cshtml* extension are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="6f755-106">La compilación en tiempo de ejecución puede habilitarse opcionalmente configurando el proyecto.</span><span class="sxs-lookup"><span data-stu-id="6f755-106">Runtime compilation may be optionally enabled by configuring your project.</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="6f755-107">Compilación de Razor</span><span class="sxs-lookup"><span data-stu-id="6f755-107">Razor compilation</span></span>

<span data-ttu-id="6f755-108">El SDK de Razor habilita la compilación en tiempo de compilación y en tiempo de publicación de archivos Razor de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="6f755-108">Build-time and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="6f755-109">Cuando está habilitada, la compilación en tiempo de ejecución complementa la compilación en tiempo de compilación, lo que permite que los archivos de Razor se actualicen si se editan.</span><span class="sxs-lookup"><span data-stu-id="6f755-109">When enabled, runtime compilation complements build-time compilation, allowing Razor files to be updated if they're edited.</span></span>

## <a name="enable-runtime-compilation-at-project-creation"></a><span data-ttu-id="6f755-110">Habilitar la compilación en tiempo de ejecución en la creación del proyecto</span><span class="sxs-lookup"><span data-stu-id="6f755-110">Enable runtime compilation at project creation</span></span>

<span data-ttu-id="6f755-111">Las plantillas de proyecto Razor Pages y MVC incluyen una opción para habilitar la compilación en tiempo de ejecución cuando se crea el proyecto.</span><span class="sxs-lookup"><span data-stu-id="6f755-111">The Razor Pages and MVC project templates include an option to enable runtime compilation when the project is created.</span></span> <span data-ttu-id="6f755-112">Esta opción se admite en ASP.NET Core 3.1 y versiones posteriores.</span><span class="sxs-lookup"><span data-stu-id="6f755-112">This option is supported in ASP.NET Core 3.1 and later.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6f755-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6f755-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6f755-114">En el cuadro de diálogo **Crear una nueva aplicación web ASP.NET Core:**</span><span class="sxs-lookup"><span data-stu-id="6f755-114">In the **Create a new ASP.NET Core web application** dialog:</span></span>

1. <span data-ttu-id="6f755-115">Seleccione la **aplicación web** o la plantilla de proyecto Aplicación web **(Model-View-Controller).**</span><span class="sxs-lookup"><span data-stu-id="6f755-115">Select either the **Web Application** or the **Web Application (Model-View-Controller)** project template.</span></span>
1. <span data-ttu-id="6f755-116">Active la casilla Habilitar compilación en tiempo de ejecución de **Razor.**</span><span class="sxs-lookup"><span data-stu-id="6f755-116">Select the **Enable Razor runtime compilation** check box.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="6f755-117">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="6f755-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="6f755-118">Utilice `-rrc` la `--razor-runtime-compilation` opción o plantilla.</span><span class="sxs-lookup"><span data-stu-id="6f755-118">Use the `-rrc` or `--razor-runtime-compilation` template option.</span></span> <span data-ttu-id="6f755-119">Por ejemplo, el siguiente comando crea un nuevo proyecto Razor Pages con la compilación en tiempo de ejecución habilitada:</span><span class="sxs-lookup"><span data-stu-id="6f755-119">For example, the following command creates a new Razor Pages project with runtime compilation enabled:</span></span>

```dotnetcli
dotnet new webapp --razor-runtime-compilation
```

---

## <a name="enable-runtime-compilation-in-an-existing-project"></a><span data-ttu-id="6f755-120">Habilitar la compilación en tiempo de ejecución en un proyecto existente</span><span class="sxs-lookup"><span data-stu-id="6f755-120">Enable runtime compilation in an existing project</span></span>

<span data-ttu-id="6f755-121">Para habilitar la compilación en tiempo de ejecución para todos los entornos de un proyecto existente:</span><span class="sxs-lookup"><span data-stu-id="6f755-121">To enable runtime compilation for all environments in an existing project:</span></span>

1. <span data-ttu-id="6f755-122">Instalar el paquete NuGet [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/).</span><span class="sxs-lookup"><span data-stu-id="6f755-122">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>
1. <span data-ttu-id="6f755-123">Actualizar el método `Startup.ConfigureServices` del proyecto para incluir una llamada a <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span><span class="sxs-lookup"><span data-stu-id="6f755-123">Update the project's `Startup.ConfigureServices` method to include a call to <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span></span> <span data-ttu-id="6f755-124">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="6f755-124">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

## <a name="conditionally-enable-runtime-compilation-in-an-existing-project"></a><span data-ttu-id="6f755-125">Habilitar condicionalmente la compilación en tiempo de ejecución en un proyecto existente</span><span class="sxs-lookup"><span data-stu-id="6f755-125">Conditionally enable runtime compilation in an existing project</span></span>

<span data-ttu-id="6f755-126">La compilación en tiempo de ejecución se puede habilitar para que solo esté disponible para el desarrollo local.</span><span class="sxs-lookup"><span data-stu-id="6f755-126">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="6f755-127">Este modo de habilitación condicional garantiza que la salida publicada:</span><span class="sxs-lookup"><span data-stu-id="6f755-127">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="6f755-128">Usa vistas precompiladas.</span><span class="sxs-lookup"><span data-stu-id="6f755-128">Uses compiled views.</span></span>
* <span data-ttu-id="6f755-129">No habilita monitores de archivos en producción.</span><span class="sxs-lookup"><span data-stu-id="6f755-129">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="6f755-130">Para habilitar la compilación en tiempo de ejecución solo en el entorno de desarrollo:</span><span class="sxs-lookup"><span data-stu-id="6f755-130">To enable runtime compilation only in the Development environment:</span></span>

1. <span data-ttu-id="6f755-131">Instalar el paquete NuGet [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/).</span><span class="sxs-lookup"><span data-stu-id="6f755-131">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>
1. <span data-ttu-id="6f755-132">Modifique la `environmentVariables` sección de perfil de lanzamiento en *launchSettings.json*:</span><span class="sxs-lookup"><span data-stu-id="6f755-132">Modify the launch profile `environmentVariables` section in *launchSettings.json*:</span></span>
    * <span data-ttu-id="6f755-133">Verificar `ASPNETCORE_ENVIRONMENT` se `"Development"`establece en .</span><span class="sxs-lookup"><span data-stu-id="6f755-133">Verify `ASPNETCORE_ENVIRONMENT` is set to `"Development"`.</span></span>
    * <span data-ttu-id="6f755-134">Establezca `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` en `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"`.</span><span class="sxs-lookup"><span data-stu-id="6f755-134">Set `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` to `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"`.</span></span>

<span data-ttu-id="6f755-135">En el ejemplo siguiente, la compilación en tiempo `IIS Express` `RazorPagesApp` de ejecución está habilitada en el entorno de desarrollo para los perfiles de lanzamiento y el uso de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="6f755-135">In the following example, runtime compilation is enabled in the Development environment for the `IIS Express` and `RazorPagesApp` launch profiles:</span></span>

[!code-json[](~/mvc/views/view-compilation/samples/3.1/launchSettings.json?highlight=15-16,24-25)]

<span data-ttu-id="6f755-136">No se necesitan cambios de `Startup` código en la clase del proyecto.</span><span class="sxs-lookup"><span data-stu-id="6f755-136">No code changes are needed in the project's `Startup` class.</span></span> <span data-ttu-id="6f755-137">En tiempo de ejecución, ASP.NET Core busca un [atributo HostingStartup de nivel de ensamblado](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) en `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`.</span><span class="sxs-lookup"><span data-stu-id="6f755-137">At runtime, ASP.NET Core searches for an [assembly-level HostingStartup attribute](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) in `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`.</span></span> <span data-ttu-id="6f755-138">El `HostingStartup` atributo especifica el código de inicio de la aplicación que se va a ejecutar.</span><span class="sxs-lookup"><span data-stu-id="6f755-138">The `HostingStartup` attribute specifies the app startup code to execute.</span></span> <span data-ttu-id="6f755-139">Ese código de inicio habilita la compilación en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="6f755-139">That startup code enables runtime compilation.</span></span>

## <a name="enable-runtime-compilation-for-a-razor-class-library"></a><span data-ttu-id="6f755-140">Habilitar la compilación en tiempo de ejecución para una biblioteca de clases de Razor</span><span class="sxs-lookup"><span data-stu-id="6f755-140">Enable runtime compilation for a Razor Class Library</span></span>

<span data-ttu-id="6f755-141">Considere un escenario en el que un proyecto razor Pages hace referencia a una biblioteca de [clases de Razor (RCL)](xref:razor-pages/ui-class) denominada *MyClassLib*.</span><span class="sxs-lookup"><span data-stu-id="6f755-141">Consider a scenario in which a Razor Pages project references a [Razor Class Library (RCL)](xref:razor-pages/ui-class) named *MyClassLib*.</span></span> <span data-ttu-id="6f755-142">La RCL contiene un archivo *_Layout.cshtml* que consumen todos los proyectos MVC y Razor Pages de su equipo.</span><span class="sxs-lookup"><span data-stu-id="6f755-142">The RCL contains a *_Layout.cshtml* file that all of your team's MVC and Razor Pages projects consume.</span></span> <span data-ttu-id="6f755-143">Desea habilitar la compilación en tiempo de ejecución para el archivo *_Layout.cshtml* en esa RCL.</span><span class="sxs-lookup"><span data-stu-id="6f755-143">You want to enable runtime compilation for the *_Layout.cshtml* file in that RCL.</span></span> <span data-ttu-id="6f755-144">Realice los siguientes cambios en el proyecto Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="6f755-144">Make the following changes in the Razor Pages project:</span></span>

1. <span data-ttu-id="6f755-145">Habilite la compilación en tiempo de ejecución con las instrucciones en [Habilitar condicionalmente la compilación en tiempo de ejecución en un proyecto existente.](#conditionally-enable-runtime-compilation-in-an-existing-project)</span><span class="sxs-lookup"><span data-stu-id="6f755-145">Enable runtime compilation with the instructions at [Conditionally enable runtime compilation in an existing project](#conditionally-enable-runtime-compilation-in-an-existing-project).</span></span>
1. <span data-ttu-id="6f755-146">Configure las opciones `Startup.ConfigureServices`de compilación en tiempo de ejecución en:</span><span class="sxs-lookup"><span data-stu-id="6f755-146">Configure the runtime compilation options in `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.1/Startup.cs?name=snippet_ConfigureServices&highlight=5-10)]

    <span data-ttu-id="6f755-147">En el código anterior, se construye una ruta de acceso absoluta a la *RCL MyClassLib.*</span><span class="sxs-lookup"><span data-stu-id="6f755-147">In the preceding code, an absolute path to the *MyClassLib* RCL is constructed.</span></span> <span data-ttu-id="6f755-148">La [API PhysicalFileProvider](xref:fundamentals/file-providers#physicalfileprovider) se utiliza para buscar directorios y archivos en esa ruta de acceso absoluta.</span><span class="sxs-lookup"><span data-stu-id="6f755-148">The [PhysicalFileProvider API](xref:fundamentals/file-providers#physicalfileprovider) is used to locate directories and files at that absolute path.</span></span> <span data-ttu-id="6f755-149">Por último, la `PhysicalFileProvider` instancia se agrega a una colección de proveedores de archivos, que permite el acceso a los archivos *.cshtml* de la RCL.</span><span class="sxs-lookup"><span data-stu-id="6f755-149">Finally, the `PhysicalFileProvider` instance is added to a file providers collection, which allows access to the RCL's *.cshtml* files.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6f755-150">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="6f755-150">Additional resources</span></span>

* <span data-ttu-id="6f755-151">[Propiedades RazorCompileOnBuild y RazorCompileOnPublish.](xref:razor-pages/sdk#properties)</span><span class="sxs-lookup"><span data-stu-id="6f755-151">[RazorCompileOnBuild and RazorCompileOnPublish](xref:razor-pages/sdk#properties) properties.</span></span>
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

<span data-ttu-id="6f755-152">Los archivos de Razor con una extensión *.cshtml* se compilan en tiempo de compilación y publicación mediante el [SDK de Razor](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="6f755-152">Razor files with a *.cshtml* extension are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="6f755-153">La compilación en tiempo de ejecución se puede habilitar opcionalmente mediante la configuración de la aplicación</span><span class="sxs-lookup"><span data-stu-id="6f755-153">Runtime compilation may be optionally enabled by configuring your application.</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="6f755-154">Compilación de Razor</span><span class="sxs-lookup"><span data-stu-id="6f755-154">Razor compilation</span></span>

<span data-ttu-id="6f755-155">El SDK de Razor habilita la compilación en tiempo de compilación y en tiempo de publicación de archivos Razor de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="6f755-155">Build-time and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="6f755-156">Cuando está habilitada, la compilación en tiempo de ejecución complementa la compilación en tiempo de compilación, lo que permite que los archivos de Razor se actualicen si se editan.</span><span class="sxs-lookup"><span data-stu-id="6f755-156">When enabled, runtime compilation complements build-time compilation, allowing Razor files to be updated if they're edited.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="6f755-157">Compilación en tiempo de ejecución</span><span class="sxs-lookup"><span data-stu-id="6f755-157">Runtime compilation</span></span>

<span data-ttu-id="6f755-158">Para habilitar la compilación en tiempo de ejecución para todos los entornos y modos de configuración:</span><span class="sxs-lookup"><span data-stu-id="6f755-158">To enable runtime compilation for all environments and configuration modes:</span></span>

1. <span data-ttu-id="6f755-159">Instalar el paquete NuGet [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/).</span><span class="sxs-lookup"><span data-stu-id="6f755-159">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>

1. <span data-ttu-id="6f755-160">Actualizar el método `Startup.ConfigureServices` del proyecto para incluir una llamada a <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span><span class="sxs-lookup"><span data-stu-id="6f755-160">Update the project's `Startup.ConfigureServices` method to include a call to <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span></span> <span data-ttu-id="6f755-161">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="6f755-161">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

### <a name="conditionally-enable-runtime-compilation"></a><span data-ttu-id="6f755-162">Habilitación condicional de la compilación en tiempo de ejecución</span><span class="sxs-lookup"><span data-stu-id="6f755-162">Conditionally enable runtime compilation</span></span>

<span data-ttu-id="6f755-163">La compilación en tiempo de ejecución se puede habilitar para que solo esté disponible para el desarrollo local.</span><span class="sxs-lookup"><span data-stu-id="6f755-163">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="6f755-164">Este modo de habilitación condicional garantiza que la salida publicada:</span><span class="sxs-lookup"><span data-stu-id="6f755-164">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="6f755-165">Usa vistas precompiladas.</span><span class="sxs-lookup"><span data-stu-id="6f755-165">Uses compiled views.</span></span>
* <span data-ttu-id="6f755-166">Tiene un tamaño inferior.</span><span class="sxs-lookup"><span data-stu-id="6f755-166">Is smaller in size.</span></span>
* <span data-ttu-id="6f755-167">No habilita monitores de archivos en producción.</span><span class="sxs-lookup"><span data-stu-id="6f755-167">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="6f755-168">Para habilitar la compilación en tiempo de ejecución basada en el modo de configuración y el entorno, deberá:</span><span class="sxs-lookup"><span data-stu-id="6f755-168">To enable runtime compilation based on the environment and configuration mode:</span></span>

1. <span data-ttu-id="6f755-169">Hacer referencia condicional al paquete [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) basado en el valor activo `Configuration`:</span><span class="sxs-lookup"><span data-stu-id="6f755-169">Conditionally reference the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) package based on the active `Configuration` value:</span></span>

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation" Version="3.1.0" Condition="'$(Configuration)' == 'Debug'" />
    ```

1. <span data-ttu-id="6f755-170">Actualizar el método `Startup.ConfigureServices` del proyecto para incluir una llamada a `AddRazorRuntimeCompilation`.</span><span class="sxs-lookup"><span data-stu-id="6f755-170">Update the project's `Startup.ConfigureServices` method to include a call to `AddRazorRuntimeCompilation`.</span></span> <span data-ttu-id="6f755-171">Ejecute `AddRazorRuntimeCompilation` de manera condicional de modo que solo se ejecute en modo de depuración cuando la variable `ASPNETCORE_ENVIRONMENT` esté establecida en `Development`:</span><span class="sxs-lookup"><span data-stu-id="6f755-171">Conditionally execute `AddRazorRuntimeCompilation` such that it only runs in Debug mode when the `ASPNETCORE_ENVIRONMENT` variable is set to `Development`:</span></span>

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.0/Startup.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="6f755-172">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="6f755-172">Additional resources</span></span>

* <span data-ttu-id="6f755-173">[Propiedades RazorCompileOnBuild y RazorCompileOnPublish.](xref:razor-pages/sdk#properties)</span><span class="sxs-lookup"><span data-stu-id="6f755-173">[RazorCompileOnBuild and RazorCompileOnPublish](xref:razor-pages/sdk#properties) properties.</span></span>
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* <span data-ttu-id="6f755-174">Consulte el ejemplo de compilación en tiempo de [ejecución en GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) para obtener un ejemplo que muestra cómo hacer que la compilación en tiempo de ejecución funcione entre proyectos.</span><span class="sxs-lookup"><span data-stu-id="6f755-174">See the [runtime compilation sample on GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) for a sample that shows making runtime compilation work across projects.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="6f755-175">Un archivo de Razor se compila en tiempo de ejecución, cuando se invoca la vista MVC o la página de Razor asociada.</span><span class="sxs-lookup"><span data-stu-id="6f755-175">A Razor file is compiled at runtime, when the associated Razor Page or MVC view is invoked.</span></span> <span data-ttu-id="6f755-176">Los archivos de Razor se compilan en tiempo de compilación y publicación mediante el [SDK de Razor](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="6f755-176">Razor files are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="6f755-177">Compilación de Razor</span><span class="sxs-lookup"><span data-stu-id="6f755-177">Razor compilation</span></span>

<span data-ttu-id="6f755-178">La compilación de los archivos de Razor en tiempo de publicación y compilación está habilitada de manera predeterminada en el SDK de Razor.</span><span class="sxs-lookup"><span data-stu-id="6f755-178">Build- and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="6f755-179">La edición de los archivos de Razor después de que se actualicen se admite en tiempo de compilación.</span><span class="sxs-lookup"><span data-stu-id="6f755-179">Editing Razor files after they're updated is supported at build time.</span></span> <span data-ttu-id="6f755-180">De forma predeterminada, solo se implementan con la aplicación los archivos *Views.dll* y los que no son *.cshtml*, o las referencias de los ensamblados necesarios para compilar los archivos de Razor.</span><span class="sxs-lookup"><span data-stu-id="6f755-180">By default, only the compiled *Views.dll* and no *.cshtml* files or references assemblies required to compile Razor files are deployed with your app.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="6f755-181">La herramienta de precompilación está en desuso y se eliminará en ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="6f755-181">The precompilation tool has been deprecated, and will be removed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="6f755-182">Se recomienda migrar al [SDK de Razor](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="6f755-182">We recommend migrating to [Razor Sdk](xref:razor-pages/sdk).</span></span>
>
> <span data-ttu-id="6f755-183">El SDK de Razor solo es efectivo cuando no hay propiedades específicas de la precompilación establecidas en el archivo de proyecto.</span><span class="sxs-lookup"><span data-stu-id="6f755-183">The Razor SDK is effective only when no precompilation-specific properties are set in the project file.</span></span> <span data-ttu-id="6f755-184">Por ejemplo, establecer la propiedad `MvcRazorCompileOnPublish` del archivo *.csproj* en `true` deshabilita el SDK de Razor.</span><span class="sxs-lookup"><span data-stu-id="6f755-184">For instance, setting the *.csproj* file's `MvcRazorCompileOnPublish` property to `true` disables the Razor SDK.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="6f755-185">Compilación en tiempo de ejecución</span><span class="sxs-lookup"><span data-stu-id="6f755-185">Runtime compilation</span></span>

<span data-ttu-id="6f755-186">La compilación en tiempo de compilación se complementa con la compilación en tiempo de ejecución de archivos de Razor.</span><span class="sxs-lookup"><span data-stu-id="6f755-186">Build-time compilation is supplemented by runtime compilation of Razor files.</span></span> <span data-ttu-id="6f755-187">ASP.NET Core MVC volverá a compilar los archivos de Razor cuando cambie el contenido de un archivo *.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="6f755-187">ASP.NET Core MVC will recompile Razor files when the contents of a *.cshtml* file change.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6f755-188">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="6f755-188">Additional resources</span></span>

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end
