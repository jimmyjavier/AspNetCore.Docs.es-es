---
title: Introducción a Swashbuckle y ASP.NET Core
author: zuckerthoben
description: Obtenga información sobre cómo agregar Swashbuckle a un proyecto de ASP.NET Core Web API para integrar la interfaz de usuario de Swagger.
ms.author: scaddie
ms.custom: mvc
ms.date: 01/17/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/get-started-with-swashbuckle
ms.openlocfilehash: 6e4d80afa1c38344321ad45031ff21fec71ae0a4
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776726"
---
# <a name="get-started-with-swashbuckle-and-aspnet-core"></a><span data-ttu-id="0d312-103">Introducción a Swashbuckle y ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0d312-103">Get started with Swashbuckle and ASP.NET Core</span></span>

<span data-ttu-id="0d312-104">Por [Shayne Boyer](https://twitter.com/spboyer) y [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="0d312-104">By [Shayne Boyer](https://twitter.com/spboyer) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="0d312-105">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0d312-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="0d312-106">Hay tres componentes principales de Swashbuckle:</span><span class="sxs-lookup"><span data-stu-id="0d312-106">There are three main components to Swashbuckle:</span></span>

* <span data-ttu-id="0d312-107">[Swashbuckle.AspNetCore.Swagger](https://www.nuget.org/packages/Swashbuckle.AspNetCore.Swagger/): modelo de objetos de Swagger y middleware para exponer objetos `SwaggerDocument` como puntos de conexión JSON.</span><span class="sxs-lookup"><span data-stu-id="0d312-107">[Swashbuckle.AspNetCore.Swagger](https://www.nuget.org/packages/Swashbuckle.AspNetCore.Swagger/): a Swagger object model and middleware to expose `SwaggerDocument` objects as JSON endpoints.</span></span>

* <span data-ttu-id="0d312-108">[Swashbuckle.AspNetCore.SwaggerGen](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerGen/): generador de Swagger que genera objetos `SwaggerDocument` directamente desde las rutas, controladores y modelos.</span><span class="sxs-lookup"><span data-stu-id="0d312-108">[Swashbuckle.AspNetCore.SwaggerGen](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerGen/): a Swagger generator that builds `SwaggerDocument` objects directly from your routes, controllers, and models.</span></span> <span data-ttu-id="0d312-109">Se suele combinar con el middleware de punto de conexión de Swagger para exponer automáticamente el JSON de Swagger.</span><span class="sxs-lookup"><span data-stu-id="0d312-109">It's typically combined with the Swagger endpoint middleware to automatically expose Swagger JSON.</span></span>

* <span data-ttu-id="0d312-110">[Swashbuckle.AspNetCore.SwaggerUI](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerUI/): versión insertada de la herramienta de interfaz de usuario de Swagger.</span><span class="sxs-lookup"><span data-stu-id="0d312-110">[Swashbuckle.AspNetCore.SwaggerUI](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerUI/): an embedded version of the Swagger UI tool.</span></span> <span data-ttu-id="0d312-111">Interpreta el JSON de Swagger para crear una experiencia enriquecida y personalizable para describir la funcionalidad de la API web.</span><span class="sxs-lookup"><span data-stu-id="0d312-111">It interprets Swagger JSON to build a rich, customizable experience for describing the web API functionality.</span></span> <span data-ttu-id="0d312-112">Incluye herramientas de ejecución de pruebas integradas para los métodos públicos.</span><span class="sxs-lookup"><span data-stu-id="0d312-112">It includes built-in test harnesses for the public methods.</span></span>

## <a name="package-installation"></a><span data-ttu-id="0d312-113">Instalación del paquete</span><span class="sxs-lookup"><span data-stu-id="0d312-113">Package installation</span></span>

<span data-ttu-id="0d312-114">Se puede agregar Swashbuckle con los métodos siguientes:</span><span class="sxs-lookup"><span data-stu-id="0d312-114">Swashbuckle can be added with the following approaches:</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="0d312-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0d312-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0d312-116">En la ventana **Consola del Administrador de paquetes**:</span><span class="sxs-lookup"><span data-stu-id="0d312-116">From the **Package Manager Console** window:</span></span>
  * <span data-ttu-id="0d312-117">Vaya a **Vista** > **Otras ventanas** > **Consola del Administrador de paquetes**.</span><span class="sxs-lookup"><span data-stu-id="0d312-117">Go to **View** > **Other Windows** > **Package Manager Console**</span></span>
  * <span data-ttu-id="0d312-118">Vaya al directorio en el que está *TodoApi.csproj*.</span><span class="sxs-lookup"><span data-stu-id="0d312-118">Navigate to the directory in which the *TodoApi.csproj* file exists</span></span>
  * <span data-ttu-id="0d312-119">Ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="0d312-119">Execute the following command:</span></span>

    ```powershell
    Install-Package Swashbuckle.AspNetCore -Version 5.0.0
    ```

* <span data-ttu-id="0d312-120">En el cuadro de diálogo **Administrar paquetes NuGet**:</span><span class="sxs-lookup"><span data-stu-id="0d312-120">From the **Manage NuGet Packages** dialog:</span></span>
  * <span data-ttu-id="0d312-121">Haga clic con el botón derecho en el proyecto en el **Explorador de soluciones** > **Administrar paquetes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="0d312-121">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
  * <span data-ttu-id="0d312-122">Establezca el **origen del paquete** en "nuget.org".</span><span class="sxs-lookup"><span data-stu-id="0d312-122">Set the **Package source** to "nuget.org"</span></span>
  * <span data-ttu-id="0d312-123">Asegúrese de que la opción "Incluir versión preliminar" está habilitada.</span><span class="sxs-lookup"><span data-stu-id="0d312-123">Ensure the "Include prerelease" option is enabled</span></span>
  * <span data-ttu-id="0d312-124">Escriba "Swashbuckle.AspNetCore" en el cuadro de búsqueda.</span><span class="sxs-lookup"><span data-stu-id="0d312-124">Enter "Swashbuckle.AspNetCore" in the search box</span></span>
  * <span data-ttu-id="0d312-125">Seleccione el paquete "Swashbuckle.AspNetCore" más reciente en la pestaña **Examinar** y haga clic en **Instalar**.</span><span class="sxs-lookup"><span data-stu-id="0d312-125">Select the latest "Swashbuckle.AspNetCore" package from the **Browse** tab and click **Install**</span></span>

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0d312-126">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="0d312-126">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="0d312-127">Haga clic con el botón derecho en la carpeta *Paquetes* en **Panel de solución** > **Agregar paquetes...**</span><span class="sxs-lookup"><span data-stu-id="0d312-127">Right-click the *Packages* folder in **Solution Pad** > **Add Packages...**</span></span>
* <span data-ttu-id="0d312-128">Establezca el menú desplegable **Origen** de la ventana **Agregar paquetes** en "nuget.org".</span><span class="sxs-lookup"><span data-stu-id="0d312-128">Set the **Add Packages** window's **Source** drop-down to "nuget.org"</span></span>
* <span data-ttu-id="0d312-129">Asegúrese de que la opción "Show pre-release packages" (Mostrar paquetes de versión preliminar) está habilitada.</span><span class="sxs-lookup"><span data-stu-id="0d312-129">Ensure the "Show pre-release packages" option is enabled</span></span>
* <span data-ttu-id="0d312-130">Escriba "Swashbuckle.AspNetCore" en el cuadro de búsqueda.</span><span class="sxs-lookup"><span data-stu-id="0d312-130">Enter "Swashbuckle.AspNetCore" in the search box</span></span>
* <span data-ttu-id="0d312-131">Seleccione el paquete "Swashbuckle.AspNetCore" más reciente en el panel de resultados y haga clic en **Agregar paquete**.</span><span class="sxs-lookup"><span data-stu-id="0d312-131">Select the latest "Swashbuckle.AspNetCore" package from the results pane and click **Add Package**</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="0d312-132">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0d312-132">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="0d312-133">Ejecute el siguiente comando en el **terminal integrado**:</span><span class="sxs-lookup"><span data-stu-id="0d312-133">Run the following command from the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet add TodoApi.csproj package Swashbuckle.AspNetCore -v 5.0.0
```

### <a name="net-core-cli"></a>[<span data-ttu-id="0d312-134">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="0d312-134">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="0d312-135">Ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="0d312-135">Run the following command:</span></span>

```dotnetcli
dotnet add TodoApi.csproj package Swashbuckle.AspNetCore -v 5.0.0
```

---

## <a name="add-and-configure-swagger-middleware"></a><span data-ttu-id="0d312-136">Agregar y configurar el middleware de Swagger</span><span class="sxs-lookup"><span data-stu-id="0d312-136">Add and configure Swagger middleware</span></span>

<span data-ttu-id="0d312-137">En la clase `Startup`, importe el siguiente espacio de nombres para que use la clase `OpenApiInfo`:</span><span class="sxs-lookup"><span data-stu-id="0d312-137">In the `Startup` class, import the following namespace to use the `OpenApiInfo` class:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_InfoClassNamespace)]

<span data-ttu-id="0d312-138">Agregue el generador de Swagger a la colección de servicios en el método `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="0d312-138">Add the Swagger generator to the services collection in the `Startup.ConfigureServices` method:</span></span>

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=8-11)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=9-12)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=8-11)]

::: moniker-end

<span data-ttu-id="0d312-139">En el método `Startup.Configure`, habilite el middleware para servir el documento JSON generado y la interfaz de usuario de Swagger:</span><span class="sxs-lookup"><span data-stu-id="0d312-139">In the `Startup.Configure` method, enable the middleware for serving the generated JSON document and the Swagger UI:</span></span>

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_Configure&highlight=4,8-11)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_Configure&highlight=4,8-11)]

::: moniker-end

<span data-ttu-id="0d312-140">La llamada de método `UseSwaggerUI` anterior habilita el [middleware de archivos estáticos](xref:fundamentals/static-files).</span><span class="sxs-lookup"><span data-stu-id="0d312-140">The preceding `UseSwaggerUI` method call enables the [Static File Middleware](xref:fundamentals/static-files).</span></span> <span data-ttu-id="0d312-141">Si el destino es .NET Framework o .NET Core 1.x, agregue el paquete NuGet [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) al proyecto.</span><span class="sxs-lookup"><span data-stu-id="0d312-141">If targeting .NET Framework or .NET Core 1.x, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) NuGet package to the project.</span></span>

<span data-ttu-id="0d312-142">Inicie la aplicación y vaya a `http://localhost:<port>/swagger/v1/swagger.json`.</span><span class="sxs-lookup"><span data-stu-id="0d312-142">Launch the app, and navigate to `http://localhost:<port>/swagger/v1/swagger.json`.</span></span> <span data-ttu-id="0d312-143">El documento generado en el que se describen los puntos de conexión aparecerá según se muestra en la [especificación de Swagger (swagger.json)](xref:tutorials/web-api-help-pages-using-swagger#swagger-specification-swaggerjson).</span><span class="sxs-lookup"><span data-stu-id="0d312-143">The generated document describing the endpoints appears as shown in [Swagger specification (swagger.json)](xref:tutorials/web-api-help-pages-using-swagger#swagger-specification-swaggerjson).</span></span>

<span data-ttu-id="0d312-144">La interfaz de usuario de Swagger se encuentra en `http://localhost:<port>/swagger`.</span><span class="sxs-lookup"><span data-stu-id="0d312-144">The Swagger UI can be found at `http://localhost:<port>/swagger`.</span></span> <span data-ttu-id="0d312-145">Explore la API a través de la interfaz de usuario de Swagger e incorpórela a otros programas.</span><span class="sxs-lookup"><span data-stu-id="0d312-145">Explore the API via Swagger UI and incorporate it in other programs.</span></span>

> [!TIP]
> <span data-ttu-id="0d312-146">Para servir la interfaz de usuario de Swagger en la raíz de la aplicación (`http://localhost:<port>/`), establezca la propiedad `RoutePrefix` en una cadena vacía:</span><span class="sxs-lookup"><span data-stu-id="0d312-146">To serve the Swagger UI at the app's root (`http://localhost:<port>/`), set the `RoutePrefix` property to an empty string:</span></span>
>
> [!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup3.cs?name=snippet_UseSwaggerUI&highlight=4)]

<span data-ttu-id="0d312-147">Si usa directorios con IIS o un proxy inverso, establezca el punto de conexión de Swagger en una ruta de acceso relativa mediante el prefijo `./`.</span><span class="sxs-lookup"><span data-stu-id="0d312-147">If using directories with IIS or a reverse proxy, set the Swagger endpoint to a relative path using the `./` prefix.</span></span> <span data-ttu-id="0d312-148">Por ejemplo: `./swagger/v1/swagger.json`.</span><span class="sxs-lookup"><span data-stu-id="0d312-148">For example, `./swagger/v1/swagger.json`.</span></span> <span data-ttu-id="0d312-149">El uso de `/swagger/v1/swagger.json` indica a la aplicación que debe buscar el archivo JSON en la verdadera raíz de la dirección URL (junto con el prefijo de ruta, si se usa).</span><span class="sxs-lookup"><span data-stu-id="0d312-149">Using `/swagger/v1/swagger.json` instructs the app to look for the JSON file at the true root of the URL (plus the route prefix, if used).</span></span> <span data-ttu-id="0d312-150">Por ejemplo, use `http://localhost:<port>/<route_prefix>/swagger/v1/swagger.json` en lugar de `http://localhost:<port>/<virtual_directory>/<route_prefix>/swagger/v1/swagger.json`.</span><span class="sxs-lookup"><span data-stu-id="0d312-150">For example, use `http://localhost:<port>/<route_prefix>/swagger/v1/swagger.json` instead of `http://localhost:<port>/<virtual_directory>/<route_prefix>/swagger/v1/swagger.json`.</span></span>

## <a name="customize-and-extend"></a><span data-ttu-id="0d312-151">Personalizar y ampliar</span><span class="sxs-lookup"><span data-stu-id="0d312-151">Customize and extend</span></span>

<span data-ttu-id="0d312-152">Swagger proporciona opciones para documentar el modelo de objetos y personalizar la interfaz de usuario para que coincida con el tema.</span><span class="sxs-lookup"><span data-stu-id="0d312-152">Swagger provides options for documenting the object model and customizing the UI to match your theme.</span></span>

<span data-ttu-id="0d312-153">En la clase `Startup`, agregue los siguientes espacios de nombres:</span><span class="sxs-lookup"><span data-stu-id="0d312-153">In the `Startup` class, add the following namespaces:</span></span>

```csharp
using System;
using System.Reflection;
using System.IO;
```

### <a name="api-info-and-description"></a><span data-ttu-id="0d312-154">Información y descripción de la API</span><span class="sxs-lookup"><span data-stu-id="0d312-154">API info and description</span></span>

<span data-ttu-id="0d312-155">La acción de configuración que se pasa al método `AddSwaggerGen` agrega información, como el autor, la licencia y la descripción:</span><span class="sxs-lookup"><span data-stu-id="0d312-155">The configuration action passed to the `AddSwaggerGen` method adds information such as the author, license, and description:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup4.cs?name=snippet_AddSwaggerGen)]

<span data-ttu-id="0d312-156">La interfaz de usuario de Swagger muestra información de la versión:</span><span class="sxs-lookup"><span data-stu-id="0d312-156">The Swagger UI displays the version's information:</span></span>

![Interfaz de usuario de Swagger con información de la versión: descripción, autor y el vínculo See more (Ver más)](web-api-help-pages-using-swagger/_static/custom-info.png)

### <a name="xml-comments"></a><span data-ttu-id="0d312-158">comentarios XML</span><span class="sxs-lookup"><span data-stu-id="0d312-158">XML comments</span></span>

<span data-ttu-id="0d312-159">Los comentarios XML se pueden habilitar con los métodos siguientes:</span><span class="sxs-lookup"><span data-stu-id="0d312-159">XML comments can be enabled with the following approaches:</span></span>

#### <a name="visual-studio"></a>[<span data-ttu-id="0d312-160">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0d312-160">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="0d312-161">Haga clic con el botón derecho en el **Explorador de soluciones** y seleccione **Editar <nombre_de_proyecto>.csproj**.</span><span class="sxs-lookup"><span data-stu-id="0d312-161">Right-click the project in **Solution Explorer** and select **Edit <project_name>.csproj**.</span></span>
* <span data-ttu-id="0d312-162">Agregue manualmente las líneas resaltadas al archivo *.csproj*:</span><span class="sxs-lookup"><span data-stu-id="0d312-162">Manually add the highlighted lines to the *.csproj* file:</span></span>

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* <span data-ttu-id="0d312-163">Haga clic con el botón derecho en el proyecto en el **Explorador de soluciones** y seleccione **Propiedades**.</span><span class="sxs-lookup"><span data-stu-id="0d312-163">Right-click the project in **Solution Explorer** and select **Properties**.</span></span>
* <span data-ttu-id="0d312-164">Active la casilla **Archivo de documentación XML** en la sección **Salida** de la pestaña **Compilar**.</span><span class="sxs-lookup"><span data-stu-id="0d312-164">Check the **XML documentation file** box under the **Output** section of the **Build** tab.</span></span>

::: moniker-end

#### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0d312-165">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="0d312-165">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="0d312-166">Desde el *Panel de solución*, presione **Control** y haga clic en el nombre del proyecto.</span><span class="sxs-lookup"><span data-stu-id="0d312-166">From the *Solution Pad*, press **control** and click the project name.</span></span> <span data-ttu-id="0d312-167">Vaya a **Herramientas** > **Editar archivo**.</span><span class="sxs-lookup"><span data-stu-id="0d312-167">Navigate to **Tools** > **Edit File**.</span></span>
* <span data-ttu-id="0d312-168">Agregue manualmente las líneas resaltadas al archivo *.csproj*:</span><span class="sxs-lookup"><span data-stu-id="0d312-168">Manually add the highlighted lines to the *.csproj* file:</span></span>

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* <span data-ttu-id="0d312-169">Abra el cuadro de diálogo **Opciones del proyecto** > **Compilar** > **Compilador**.</span><span class="sxs-lookup"><span data-stu-id="0d312-169">Open the **Project Options** dialog > **Build** > **Compiler**</span></span>
* <span data-ttu-id="0d312-170">Active la casilla **Generar documentación XML** en la sección **Opciones generales**.</span><span class="sxs-lookup"><span data-stu-id="0d312-170">Check the **Generate xml documentation** box under the **General Options** section</span></span>

::: moniker-end

#### <a name="visual-studio-code"></a>[<span data-ttu-id="0d312-171">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0d312-171">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="0d312-172">Agregue manualmente las líneas resaltadas al archivo *.csproj*:</span><span class="sxs-lookup"><span data-stu-id="0d312-172">Manually add the highlighted lines to the *.csproj* file:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

#### <a name="net-core-cli"></a>[<span data-ttu-id="0d312-173">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="0d312-173">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="0d312-174">Agregue manualmente las líneas resaltadas al archivo *.csproj*:</span><span class="sxs-lookup"><span data-stu-id="0d312-174">Manually add the highlighted lines to the *.csproj* file:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

---

<span data-ttu-id="0d312-175">Puede habilitar los comentarios XML para proporcionar información de depuración para miembros y tipos públicos sin documentación.</span><span class="sxs-lookup"><span data-stu-id="0d312-175">Enabling XML comments provides debug information for undocumented public types and members.</span></span> <span data-ttu-id="0d312-176">Los miembros y tipos que no estén documentados se indican por medio del mensaje de advertencia.</span><span class="sxs-lookup"><span data-stu-id="0d312-176">Undocumented types and members are indicated by the warning message.</span></span> <span data-ttu-id="0d312-177">Por ejemplo, el siguiente mensaje señala una infracción con el código de advertencia 1591:</span><span class="sxs-lookup"><span data-stu-id="0d312-177">For example, the following message indicates a violation of warning code 1591:</span></span>

```text
warning CS1591: Missing XML comment for publicly visible type or member 'TodoController.GetAll()'
```

<span data-ttu-id="0d312-178">Para eliminar las advertencias a nivel de proyecto, defina una lista delimitada por punto y coma de códigos de advertencia que se deban omitir en el archivo de proyecto.</span><span class="sxs-lookup"><span data-stu-id="0d312-178">To suppress warnings project-wide, define a semicolon-delimited list of warning codes to ignore in the project file.</span></span> <span data-ttu-id="0d312-179">Al anexar los códigos de advertencia a `$(NoWarn);`, se aplican también los [valores predeterminados de C#](https://github.com/dotnet/sdk/blob/2eb6c546931b5bcb92cd3128b93932a980553ea1/src/Tasks/Microsoft.NET.Build.Tasks/targets/Microsoft.NET.Sdk.CSharp.props#L16).</span><span class="sxs-lookup"><span data-stu-id="0d312-179">Appending the warning codes to `$(NoWarn);` applies the [C# default values](https://github.com/dotnet/sdk/blob/2eb6c546931b5bcb92cd3128b93932a980553ea1/src/Tasks/Microsoft.NET.Build.Tasks/targets/Microsoft.NET.Sdk.CSharp.props#L16) too.</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=3)]

::: moniker-end

<span data-ttu-id="0d312-180">Para suprimir las advertencias solo para miembros específicos, incluya el código en directivas de preprocesador de [advertencias #pragma](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-pragma-warning).</span><span class="sxs-lookup"><span data-stu-id="0d312-180">To suppress warnings only for specific members, enclose the code in [#pragma warning](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-pragma-warning) preprocessor directives.</span></span> <span data-ttu-id="0d312-181">Este enfoque es útil para el código que no debe exponerse mediante los documentos de API. En el ejemplo siguiente, se omite el código de advertencia CS1591 para toda la clase `Program`.</span><span class="sxs-lookup"><span data-stu-id="0d312-181">This approach is useful for code that shouldn't be exposed via the API docs. In the following example, warning code CS1591 is ignored for the entire `Program` class.</span></span> <span data-ttu-id="0d312-182">La ejecución del código de advertencia se restaura al cerrar la definición de clase.</span><span class="sxs-lookup"><span data-stu-id="0d312-182">Enforcement of the warning code is restored at the close of the class definition.</span></span> <span data-ttu-id="0d312-183">Especifique varios códigos de advertencia con una lista delimitada por comas.</span><span class="sxs-lookup"><span data-stu-id="0d312-183">Specify multiple warning codes with a comma-delimited list.</span></span>

```csharp
namespace TodoApi
{
#pragma warning disable CS1591
    public class Program
    {
        public static void Main(string[] args) =>
            BuildWebHost(args).Run();

        public static IWebHost BuildWebHost(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .UseStartup<Startup>()
                .Build();
    }
#pragma warning restore CS1591
}
```

<span data-ttu-id="0d312-184">Configure Swagger para usar el archivo XML que se generó con las instrucciones anteriores.</span><span class="sxs-lookup"><span data-stu-id="0d312-184">Configure Swagger to use the XML file that's generated with the preceding instructions.</span></span> <span data-ttu-id="0d312-185">En Linux o sistemas operativos que no sean Windows, las rutas de acceso y los nombres de archivo pueden distinguir entre mayúsculas y minúsculas.</span><span class="sxs-lookup"><span data-stu-id="0d312-185">For Linux or non-Windows operating systems, file names and paths can be case-sensitive.</span></span> <span data-ttu-id="0d312-186">Por ejemplo, un archivo *TodoApi.XML* es válido en Windows, pero no en CentOS.</span><span class="sxs-lookup"><span data-stu-id="0d312-186">For example, a *TodoApi.XML* file is valid on Windows but not CentOS.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup.cs?name=snippet_ConfigureServices&highlight=30-32)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup.cs?name=snippet_ConfigureServices&highlight=31-33)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup.cs?name=snippet_ConfigureServices&highlight=30-32)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup1x.cs?name=snippet_ConfigureServices&highlight=30-32)]

::: moniker-end

<span data-ttu-id="0d312-187">En el código anterior, [Reflection](/dotnet/csharp/programming-guide/concepts/reflection) se usa para crear un nombre de archivo XML que coincida con el proyecto de API web.</span><span class="sxs-lookup"><span data-stu-id="0d312-187">In the preceding code, [Reflection](/dotnet/csharp/programming-guide/concepts/reflection) is used to build an XML file name matching that of the web API project.</span></span> <span data-ttu-id="0d312-188">La propiedad [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory*) sirve para crear una ruta de acceso al archivo XML.</span><span class="sxs-lookup"><span data-stu-id="0d312-188">The [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory*) property is used to construct a path to the XML file.</span></span> <span data-ttu-id="0d312-189">Algunas características de Swagger (por ejemplo, esquemas de parámetros de entrada o métodos HTTP y códigos de respuesta de los atributos respectivos) funcionan sin el uso de un archivo de documentación XML.</span><span class="sxs-lookup"><span data-stu-id="0d312-189">Some Swagger features (for example, schemata of input parameters or HTTP methods and response codes from the respective attributes) work without the use of an XML documentation file.</span></span> <span data-ttu-id="0d312-190">Para la mayoría de las características, es decir, los resúmenes de los métodos y las descripciones de los parámetros y los códigos de respuesta, el uso de un archivo XML es obligatorio.</span><span class="sxs-lookup"><span data-stu-id="0d312-190">For most features, namely method summaries and the descriptions of parameters and response codes, the use of an XML file is mandatory.</span></span>

<span data-ttu-id="0d312-191">Agregar comentarios con la triple barra diagonal a una acción mejora la interfaz de usuario de Swagger en tanto agrega una descripción de la acción al encabezado de la sección.</span><span class="sxs-lookup"><span data-stu-id="0d312-191">Adding triple-slash comments to an action enhances the Swagger UI by adding the description to the section header.</span></span> <span data-ttu-id="0d312-192">Agregue un elemento [\<summary>](/dotnet/csharp/programming-guide/xmldoc/summary) antes de la acción `Delete`:</span><span class="sxs-lookup"><span data-stu-id="0d312-192">Add a [\<summary>](/dotnet/csharp/programming-guide/xmldoc/summary) element above the `Delete` action:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Delete&highlight=1-3)]

<span data-ttu-id="0d312-193">La interfaz de usuario de Swagger muestra el texto interno del elemento `<summary>` del código anterior:</span><span class="sxs-lookup"><span data-stu-id="0d312-193">The Swagger UI displays the inner text of the preceding code's `<summary>` element:</span></span>

![Interfaz de usuario de Swagger en la que se muestra el comentario XML "Deletes a specific TodoItem" (Elimina una tarea pendiente específica)](web-api-help-pages-using-swagger/_static/triple-slash-comments.png)

<span data-ttu-id="0d312-196">La interfaz de usuario se controla por medio del esquema JSON generado:</span><span class="sxs-lookup"><span data-stu-id="0d312-196">The UI is driven by the generated JSON schema:</span></span>

```json
"delete": {
    "tags": [
        "Todo"
    ],
    "summary": "Deletes a specific TodoItem.",
    "operationId": "ApiTodoByIdDelete",
    "consumes": [],
    "produces": [],
    "parameters": [
        {
            "name": "id",
            "in": "path",
            "description": "",
            "required": true,
            "type": "integer",
            "format": "int64"
        }
    ],
    "responses": {
        "200": {
            "description": "Success"
        }
    }
}
```

<span data-ttu-id="0d312-197">Agregue un elemento [\<remarks>](/dotnet/csharp/programming-guide/xmldoc/remarks) a la documentación del método de acción `Create`.</span><span class="sxs-lookup"><span data-stu-id="0d312-197">Add a [\<remarks>](/dotnet/csharp/programming-guide/xmldoc/remarks) element to the `Create` action method documentation.</span></span> <span data-ttu-id="0d312-198">Este elemento complementa la información especificada en el elemento `<summary>` y proporciona una interfaz de usuario de Swagger más sólida.</span><span class="sxs-lookup"><span data-stu-id="0d312-198">It supplements information specified in the `<summary>` element and provides a more robust Swagger UI.</span></span> <span data-ttu-id="0d312-199">El contenido del elemento `<remarks>` puede estar formado por texto, JSON o XML.</span><span class="sxs-lookup"><span data-stu-id="0d312-199">The `<remarks>` element content can consist of text, JSON, or XML.</span></span>

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

<span data-ttu-id="0d312-200">Fíjese en las mejoras de la interfaz de usuario con estos comentarios extra:</span><span class="sxs-lookup"><span data-stu-id="0d312-200">Notice the UI enhancements with these additional comments:</span></span>

![Interfaz de usuario de Swagger con comentarios adicionales](web-api-help-pages-using-swagger/_static/xml-comments-extended.png)

### <a name="data-annotations"></a><span data-ttu-id="0d312-202">Anotaciones de datos</span><span class="sxs-lookup"><span data-stu-id="0d312-202">Data annotations</span></span>

<span data-ttu-id="0d312-203">Marque el modelo con atributos que se encuentren en el espacio de nombres [System.ComponentModel.DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) para controlar los componentes de la interfaz de usuario de Swagger.</span><span class="sxs-lookup"><span data-stu-id="0d312-203">Mark the model with attributes, found in the [System.ComponentModel.DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) namespace, to help drive the Swagger UI components.</span></span>

<span data-ttu-id="0d312-204">Agregue el atributo `[Required]` a la propiedad `Name` de la clase `TodoItem`:</span><span class="sxs-lookup"><span data-stu-id="0d312-204">Add the `[Required]` attribute to the `Name` property of the `TodoItem` class:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Models/TodoItem.cs?highlight=10)]

<span data-ttu-id="0d312-205">La presencia de este atributo cambia el comportamiento de la interfaz de usuario y modifica el esquema JSON subyacente:</span><span class="sxs-lookup"><span data-stu-id="0d312-205">The presence of this attribute changes the UI behavior and alters the underlying JSON schema:</span></span>

```json
"definitions": {
    "TodoItem": {
        "required": [
            "name"
        ],
        "type": "object",
        "properties": {
            "id": {
                "format": "int64",
                "type": "integer"
            },
            "name": {
                "type": "string"
            },
            "isComplete": {
                "default": false,
                "type": "boolean"
            }
        }
    }
},
```

<span data-ttu-id="0d312-206">Agregue el atributo `[Produces("application/json")]` al controlador de API.</span><span class="sxs-lookup"><span data-stu-id="0d312-206">Add the `[Produces("application/json")]` attribute to the API controller.</span></span> <span data-ttu-id="0d312-207">Su propósito consiste en declarar que las acciones del controlador admiten un contenido de respuesta de tipo *application/json*:</span><span class="sxs-lookup"><span data-stu-id="0d312-207">Its purpose is to declare that the controller's actions support a response content type of *application/json*:</span></span>

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

<span data-ttu-id="0d312-208">En el menú desplegable **Response Content Type** (Tipo de contenido de respuesta) se selecciona este tipo de contenido como el valor predeterminado para las acciones GET del controlador:</span><span class="sxs-lookup"><span data-stu-id="0d312-208">The **Response Content Type** drop-down selects this content type as the default for the controller's GET actions:</span></span>

![Interfaz de usuario de Swagger con el tipo de contenido de respuesta predeterminado](web-api-help-pages-using-swagger/_static/json-response-content-type.png)

<span data-ttu-id="0d312-210">A medida que aumenta el uso de anotaciones de datos en la API web, la interfaz de usuario y las páginas de ayuda de la API pasan a ser más descriptivas y útiles.</span><span class="sxs-lookup"><span data-stu-id="0d312-210">As the usage of data annotations in the web API increases, the UI and API help pages become more descriptive and useful.</span></span>

### <a name="describe-response-types"></a><span data-ttu-id="0d312-211">Describir los tipos de respuesta</span><span class="sxs-lookup"><span data-stu-id="0d312-211">Describe response types</span></span>

<span data-ttu-id="0d312-212">Lo que más preocupa a los desarrolladores que consumen una API web es lo que se devuelve; sobre todo, los tipos de respuesta y los códigos de error (si no son los habituales).</span><span class="sxs-lookup"><span data-stu-id="0d312-212">Developers consuming a web API are most concerned with what's returned&mdash;specifically response types and error codes (if not standard).</span></span> <span data-ttu-id="0d312-213">Los tipos de respuesta y los códigos de error se indican en las anotaciones de datos y los comentarios XML.</span><span class="sxs-lookup"><span data-stu-id="0d312-213">The response types and error codes are denoted in the XML comments and data annotations.</span></span>

<span data-ttu-id="0d312-214">La acción `Create` devuelve un código de estado HTTP 201 cuando se ha ejecutado correctamente.</span><span class="sxs-lookup"><span data-stu-id="0d312-214">The `Create` action returns an HTTP 201 status code on success.</span></span> <span data-ttu-id="0d312-215">Cuando el cuerpo de solicitud enviado es null, se devuelve un código de estado HTTP 400.</span><span class="sxs-lookup"><span data-stu-id="0d312-215">An HTTP 400 status code is returned when the posted request body is null.</span></span> <span data-ttu-id="0d312-216">Sin la documentación correcta en la interfaz de usuario de Swagger, el consumidor no dispone de la información necesaria de estos resultados esperados.</span><span class="sxs-lookup"><span data-stu-id="0d312-216">Without proper documentation in the Swagger UI, the consumer lacks knowledge of these expected outcomes.</span></span> <span data-ttu-id="0d312-217">Corrija este problema agregando las líneas resaltadas en el siguiente ejemplo:</span><span class="sxs-lookup"><span data-stu-id="0d312-217">Fix that problem by adding the highlighted lines in the following example:</span></span>

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

<span data-ttu-id="0d312-218">Ahora, la interfaz de usuario de Swagger documenta de forma clara los códigos de respuesta HTTP esperados:</span><span class="sxs-lookup"><span data-stu-id="0d312-218">The Swagger UI now clearly documents the expected HTTP response codes:</span></span>

![Interfaz de usuario de Swagger, donde se muestra la descripción de la clase de respuesta POST "Returns the newly created item" (Devuelve el elemento recién creado) y "400 - If the item is null" (400 - Si el elemento es nulo) para el código de estado y el motivo en Response Messages (Mensajes de respuesta)](web-api-help-pages-using-swagger/_static/data-annotations-response-types.png)

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="0d312-220">En ASP.NET Core 2.2 o versiones posteriores, las convenciones se pueden usar como alternativa a la representación explícita de acciones individuales con `[ProducesResponseType]`.</span><span class="sxs-lookup"><span data-stu-id="0d312-220">In ASP.NET Core 2.2 or later, conventions can be used as an alternative to explicitly decorating individual actions with `[ProducesResponseType]`.</span></span> <span data-ttu-id="0d312-221">Para obtener más información, vea <xref:web-api/advanced/conventions>.</span><span class="sxs-lookup"><span data-stu-id="0d312-221">For more information, see <xref:web-api/advanced/conventions>.</span></span>

::: moniker-end

### <a name="customize-the-ui"></a><span data-ttu-id="0d312-222">Personalizar la interfaz de usuario</span><span class="sxs-lookup"><span data-stu-id="0d312-222">Customize the UI</span></span>

<span data-ttu-id="0d312-223">La interfaz de usuario es funcional y tiene un aspecto adecuado.</span><span class="sxs-lookup"><span data-stu-id="0d312-223">The stock UI is both functional and presentable.</span></span> <span data-ttu-id="0d312-224">Pero las páginas de documentación de la API deben ostentar su marca o tema.</span><span class="sxs-lookup"><span data-stu-id="0d312-224">However, API documentation pages should represent your brand or theme.</span></span> <span data-ttu-id="0d312-225">Para incluir la personalización de marca en los componentes de Swashbuckle, se deben agregar los recursos para servir archivos estáticos y generar la estructura de carpetas que hospedará estos archivos.</span><span class="sxs-lookup"><span data-stu-id="0d312-225">Branding the Swashbuckle components requires adding the resources to serve static files and building the folder structure to host those files.</span></span>

<span data-ttu-id="0d312-226">Si el destino es .NET Framework o .NET Core 1.x, agregue el paquete NuGet [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles) al proyecto:</span><span class="sxs-lookup"><span data-stu-id="0d312-226">If targeting .NET Framework or .NET Core 1.x, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles) NuGet package to the project:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.StaticFiles" Version="2.0.0" />
```

<span data-ttu-id="0d312-227">El paquete NuGet anterior ya estará instalado si el destino es .NET Core 2.x y se usa el [metapaquete](xref:fundamentals/metapackage).</span><span class="sxs-lookup"><span data-stu-id="0d312-227">The preceding NuGet package is already installed if targeting .NET Core 2.x and using the [metapackage](xref:fundamentals/metapackage).</span></span>

<span data-ttu-id="0d312-228">Habilitar middleware de archivos estáticos:</span><span class="sxs-lookup"><span data-stu-id="0d312-228">Enable Static File Middleware:</span></span>

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup.cs?name=snippet_Configure&highlight=3)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup.cs?name=snippet_Configure&highlight=3)]

::: moniker-end

<span data-ttu-id="0d312-229">Obtenga el contenido de la carpeta *dist* en el [repositorio de GitHub de la interfaz de usuario de Swagger](https://github.com/swagger-api/swagger-ui/tree/master/dist).</span><span class="sxs-lookup"><span data-stu-id="0d312-229">Acquire the contents of the *dist* folder from the [Swagger UI GitHub repository](https://github.com/swagger-api/swagger-ui/tree/master/dist).</span></span> <span data-ttu-id="0d312-230">Esta carpeta contiene los recursos necesarios para la página de interfaz de usuario de Swagger.</span><span class="sxs-lookup"><span data-stu-id="0d312-230">This folder contains the necessary assets for the Swagger UI page.</span></span>

<span data-ttu-id="0d312-231">Cree una carpeta *wwwroot/swagger/ui* y copie en ella el contenido de la carpeta *dist*.</span><span class="sxs-lookup"><span data-stu-id="0d312-231">Create a *wwwroot/swagger/ui* folder, and copy into it the contents of the *dist* folder.</span></span>

<span data-ttu-id="0d312-232">Cree un archivo *custom.css* en *wwwroot/swagger/ui* con el siguiente código CSS para personalizar el encabezado de página:</span><span class="sxs-lookup"><span data-stu-id="0d312-232">Create a *custom.css* file, in *wwwroot/swagger/ui*, with the following CSS to customize the page header:</span></span>

[!code-css[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/wwwroot/swagger/ui/custom.css)]

<span data-ttu-id="0d312-233">Cree una referencia a *custom.css* en el archivo *index.html* dentro de la carpeta ui, después de otros archivos CSS:</span><span class="sxs-lookup"><span data-stu-id="0d312-233">Reference *custom.css* in the *index.html* file inside ui folder, after any other CSS files:</span></span>

[!code-html[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/wwwroot/swagger/ui/index.html?name=snippet_SwaggerUiCss&highlight=3)]

<span data-ttu-id="0d312-234">Vaya a la página *index.html* en `http://localhost:<port>/swagger/ui/index.html`.</span><span class="sxs-lookup"><span data-stu-id="0d312-234">Browse to the *index.html* page at `http://localhost:<port>/swagger/ui/index.html`.</span></span> <span data-ttu-id="0d312-235">Escriba `https://localhost:<port>/swagger/v1/swagger.json` en el cuadro de texto del encabezado y haga clic en el botón **Explorar**.</span><span class="sxs-lookup"><span data-stu-id="0d312-235">Enter `https://localhost:<port>/swagger/v1/swagger.json` in the header's textbox, and click the **Explore** button.</span></span> <span data-ttu-id="0d312-236">La página resultante tiene el siguiente aspecto:</span><span class="sxs-lookup"><span data-stu-id="0d312-236">The resulting page looks as follows:</span></span>

![Interfaz de usuario de Swagger con el título de encabezado personalizado](web-api-help-pages-using-swagger/_static/custom-header.png)

<span data-ttu-id="0d312-238">Puede hacer muchas más cosas con la página.</span><span class="sxs-lookup"><span data-stu-id="0d312-238">There's much more you can do with the page.</span></span> <span data-ttu-id="0d312-239">Vea todas las capacidades de los recursos de la interfaz de usuario en el [repositorio de GitHub de la interfaz de usuario de Swagger](https://github.com/swagger-api/swagger-ui).</span><span class="sxs-lookup"><span data-stu-id="0d312-239">See the full capabilities for the UI resources at the [Swagger UI GitHub repository](https://github.com/swagger-api/swagger-ui).</span></span>
