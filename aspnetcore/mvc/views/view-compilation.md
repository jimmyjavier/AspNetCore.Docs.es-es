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
# <a name="razor-file-compilation-in-aspnet-core"></a>Compilación de archivos de Razor en ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.1"

Los archivos de Razor con una extensión *.cshtml* se compilan en tiempo de compilación y publicación mediante el [SDK de Razor](xref:razor-pages/sdk). La compilación en tiempo de ejecución puede habilitarse opcionalmente configurando el proyecto.

## <a name="razor-compilation"></a>Compilación de Razor

El SDK de Razor habilita la compilación en tiempo de compilación y en tiempo de publicación de archivos Razor de forma predeterminada. Cuando está habilitada, la compilación en tiempo de ejecución complementa la compilación en tiempo de compilación, lo que permite que los archivos de Razor se actualicen si se editan.

## <a name="enable-runtime-compilation-at-project-creation"></a>Habilitar la compilación en tiempo de ejecución en la creación del proyecto

Las plantillas de proyecto Razor Pages y MVC incluyen una opción para habilitar la compilación en tiempo de ejecución cuando se crea el proyecto. Esta opción se admite en ASP.NET Core 3.1 y versiones posteriores.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

En el cuadro de diálogo **Crear una nueva aplicación web ASP.NET Core:**

1. Seleccione la **aplicación web** o la plantilla de proyecto Aplicación web **(Model-View-Controller).**
1. Active la casilla Habilitar compilación en tiempo de ejecución de **Razor.**

# <a name="net-core-cli"></a>[CLI de .NET Core](#tab/netcore-cli)

Utilice `-rrc` la `--razor-runtime-compilation` opción o plantilla. Por ejemplo, el siguiente comando crea un nuevo proyecto Razor Pages con la compilación en tiempo de ejecución habilitada:

```dotnetcli
dotnet new webapp --razor-runtime-compilation
```

---

## <a name="enable-runtime-compilation-in-an-existing-project"></a>Habilitar la compilación en tiempo de ejecución en un proyecto existente

Para habilitar la compilación en tiempo de ejecución para todos los entornos de un proyecto existente:

1. Instalar el paquete NuGet [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/).
1. Actualizar el método `Startup.ConfigureServices` del proyecto para incluir una llamada a <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>. Por ejemplo:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

## <a name="conditionally-enable-runtime-compilation-in-an-existing-project"></a>Habilitar condicionalmente la compilación en tiempo de ejecución en un proyecto existente

La compilación en tiempo de ejecución se puede habilitar para que solo esté disponible para el desarrollo local. Este modo de habilitación condicional garantiza que la salida publicada:

* Usa vistas precompiladas.
* No habilita monitores de archivos en producción.

Para habilitar la compilación en tiempo de ejecución solo en el entorno de desarrollo:

1. Instalar el paquete NuGet [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/).
1. Modifique la `environmentVariables` sección de perfil de lanzamiento en *launchSettings.json*:
    * Verificar `ASPNETCORE_ENVIRONMENT` se `"Development"`establece en .
    * Establezca `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` en `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"`.

En el ejemplo siguiente, la compilación en tiempo `IIS Express` `RazorPagesApp` de ejecución está habilitada en el entorno de desarrollo para los perfiles de lanzamiento y el uso de la aplicación:

[!code-json[](~/mvc/views/view-compilation/samples/3.1/launchSettings.json?highlight=15-16,24-25)]

No se necesitan cambios de `Startup` código en la clase del proyecto. En tiempo de ejecución, ASP.NET Core busca un [atributo HostingStartup de nivel de ensamblado](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) en `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`. El `HostingStartup` atributo especifica el código de inicio de la aplicación que se va a ejecutar. Ese código de inicio habilita la compilación en tiempo de ejecución.

## <a name="additional-resources"></a>Recursos adicionales

* [Propiedades RazorCompileOnBuild y RazorCompileOnPublish.](xref:razor-pages/sdk#properties)
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* Consulte el ejemplo de compilación en tiempo de [ejecución en GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) para obtener un ejemplo que muestra cómo hacer que la compilación en tiempo de ejecución funcione entre proyectos.

::: moniker-end

::: moniker range="= aspnetcore-3.0"

Los archivos de Razor con una extensión *.cshtml* se compilan en tiempo de compilación y publicación mediante el [SDK de Razor](xref:razor-pages/sdk). La compilación en tiempo de ejecución se puede habilitar opcionalmente mediante la configuración de la aplicación

## <a name="razor-compilation"></a>Compilación de Razor

El SDK de Razor habilita la compilación en tiempo de compilación y en tiempo de publicación de archivos Razor de forma predeterminada. Cuando está habilitada, la compilación en tiempo de ejecución complementa la compilación en tiempo de compilación, lo que permite que los archivos de Razor se actualicen si se editan.

## <a name="runtime-compilation"></a>Compilación en tiempo de ejecución

Para habilitar la compilación en tiempo de ejecución para todos los entornos y modos de configuración:

1. Instalar el paquete NuGet [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/).

1. Actualizar el método `Startup.ConfigureServices` del proyecto para incluir una llamada a <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>. Por ejemplo:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

### <a name="conditionally-enable-runtime-compilation"></a>Habilitación condicional de la compilación en tiempo de ejecución

La compilación en tiempo de ejecución se puede habilitar para que solo esté disponible para el desarrollo local. Este modo de habilitación condicional garantiza que la salida publicada:

* Usa vistas precompiladas.
* Tiene un tamaño inferior.
* No habilita monitores de archivos en producción.

Para habilitar la compilación en tiempo de ejecución basada en el modo de configuración y el entorno, deberá:

1. Hacer referencia condicional al paquete [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) basado en el valor activo `Configuration`:

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation" Version="3.1.0" Condition="'$(Configuration)' == 'Debug'" />
    ```

1. Actualizar el método `Startup.ConfigureServices` del proyecto para incluir una llamada a `AddRazorRuntimeCompilation`. Ejecute `AddRazorRuntimeCompilation` de manera condicional de modo que solo se ejecute en modo de depuración cuando la variable `ASPNETCORE_ENVIRONMENT` esté establecida en `Development`:

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.0/Startup.cs?name=snippet)]

## <a name="additional-resources"></a>Recursos adicionales

* [Propiedades RazorCompileOnBuild y RazorCompileOnPublish.](xref:razor-pages/sdk#properties)
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* Consulte el ejemplo de compilación en tiempo de [ejecución en GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) para obtener un ejemplo que muestra cómo hacer que la compilación en tiempo de ejecución funcione entre proyectos.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Un archivo de Razor se compila en tiempo de ejecución, cuando se invoca la vista MVC o la página de Razor asociada. Los archivos de Razor se compilan en tiempo de compilación y publicación mediante el [SDK de Razor](xref:razor-pages/sdk).

## <a name="razor-compilation"></a>Compilación de Razor

La compilación de los archivos de Razor en tiempo de publicación y compilación está habilitada de manera predeterminada en el SDK de Razor. La edición de los archivos de Razor después de que se actualicen se admite en tiempo de compilación. De forma predeterminada, solo se implementan con la aplicación los archivos *Views.dll* y los que no son *.cshtml*, o las referencias de los ensamblados necesarios para compilar los archivos de Razor.

> [!IMPORTANT]
> La herramienta de precompilación está en desuso y se eliminará en ASP.NET Core 3.0. Se recomienda migrar al [SDK de Razor](xref:razor-pages/sdk).
>
> El SDK de Razor solo es efectivo cuando no hay propiedades específicas de la precompilación establecidas en el archivo de proyecto. Por ejemplo, establecer la propiedad `MvcRazorCompileOnPublish` del archivo *.csproj* en `true` deshabilita el SDK de Razor.

## <a name="runtime-compilation"></a>Compilación en tiempo de ejecución

La compilación en tiempo de compilación se complementa con la compilación en tiempo de ejecución de archivos de Razor. ASP.NET Core MVC volverá a compilar los archivos de Razor cuando cambie el contenido de un archivo *.cshtml*.

## <a name="additional-resources"></a>Recursos adicionales

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end
