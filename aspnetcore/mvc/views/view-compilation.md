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
# <a name="razor-file-compilation-in-aspnet-core"></a>Compilación de archivos de Razor en ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Los archivos de Razor con una extensión *.cshtml* se compilan en tiempo de compilación y publicación mediante el [SDK de Razor](xref:razor-pages/sdk). La compilación en tiempo de ejecución se puede habilitar opcionalmente mediante la configuración de la aplicación

## <a name="razor-compilation"></a>Compilación de Razor

La compilación de los archivos de Razor en tiempo de publicación y compilación está habilitada de manera predeterminada en el SDK de Razor. Cuando se habilita la compilación en tiempo de ejecución, complementa a la de tiempo de compilación y permite que se actualicen los archivos de Razor si se modifican.

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

  [!code-csharp[](~/mvc/views/view-compilation/sample/Startup.cs?name=snippet)]

## <a name="additional-resources"></a>Recursos adicionales

* [Propiedades RazorCompileOnBuild y RazorCompileOnPublish.](xref:razor-pages/sdk#properties)
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* Consulte el [ejemplo runtimecompilation en GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) para obtener un ejemplo que muestra cómo hacer que la compilación en tiempo de ejecución funcione entre proyectos.

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
