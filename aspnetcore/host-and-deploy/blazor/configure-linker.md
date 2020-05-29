---
<span data-ttu-id="5bdf5-101">title: "Configuración del enlazador para ASP.NET Core Blazor" autor: descripción: "Aprenda a controlar al enlazador de lenguaje intermedio (IL) al compilar una aplicación Blazor".</span><span class="sxs-lookup"><span data-stu-id="5bdf5-101">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="5bdf5-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5bdf5-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5bdf5-103">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5bdf5-103">'Blazor'</span></span>
- <span data-ttu-id="5bdf5-104">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5bdf5-104">'Identity'</span></span>
- <span data-ttu-id="5bdf5-105">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5bdf5-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="5bdf5-106">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5bdf5-106">'Razor'</span></span>
- <span data-ttu-id="5bdf5-107">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5bdf5-107">'SignalR' uid:</span></span> 

---
# <a name="configure-the-linker-for-aspnet-core-blazor"></a><span data-ttu-id="5bdf5-108">Configuración del enlazador de ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="5bdf5-108">Configure the Linker for ASP.NET Core Blazor</span></span>

<span data-ttu-id="5bdf5-109">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="5bdf5-109">By [Luke Latham](https://github.com/guardrex)</span></span>

Blazor<span data-ttu-id="5bdf5-110"> WebAssembly realiza la vinculación de [lenguaje intermedio (IL)](/dotnet/standard/managed-code#intermediate-language--execution) durante una compilación para reducir el IL innecesario de los ensamblados de salida de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="5bdf5-110"> WebAssembly performs [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) linking during a build to trim unnecessary IL from the app's output assemblies.</span></span> <span data-ttu-id="5bdf5-111">El enlazador está deshabilitado durante la compilación en la configuración Debug.</span><span class="sxs-lookup"><span data-stu-id="5bdf5-111">The linker is disabled when building in Debug configuration.</span></span> <span data-ttu-id="5bdf5-112">Las aplicaciones deben compilarse en la configuración Release para habilitar el enlazador.</span><span class="sxs-lookup"><span data-stu-id="5bdf5-112">Apps must build in Release configuration to enable the linker.</span></span> <span data-ttu-id="5bdf5-113">Se recomienda compilar en Release cuando se implementen las aplicaciones Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="5bdf5-113">We recommend building in Release when deploying your Blazor WebAssembly apps.</span></span> 

<span data-ttu-id="5bdf5-114">La vinculación de una aplicación optimiza el tamaño, pero puede tener efectos perjudiciales.</span><span class="sxs-lookup"><span data-stu-id="5bdf5-114">Linking an app optimizes for size but may have detrimental effects.</span></span> <span data-ttu-id="5bdf5-115">Las aplicaciones que usan la reflexión o características dinámicas relacionadas pueden verse interrumpidas cuando se reduzcan porque el enlazador no conoce este comportamiento dinámico y no puede determinar, en general, qué tipos son necesarios para la reflexión en el entorno de ejecución.</span><span class="sxs-lookup"><span data-stu-id="5bdf5-115">Apps that use reflection or related dynamic features may break when trimmed because the linker doesn't know about this dynamic behavior and can't determine in general which types are required for reflection at runtime.</span></span> <span data-ttu-id="5bdf5-116">Para reducir estas aplicaciones, se debe informar al vinculador de los tipos necesarios para la reflexión en el código y en los paquetes o marcos de trabajo de los que depende la aplicación.</span><span class="sxs-lookup"><span data-stu-id="5bdf5-116">To trim such apps, the linker must be informed about any types required by reflection in the code and in packages or frameworks that the app depends on.</span></span> 

<span data-ttu-id="5bdf5-117">Para asegurarse de que la aplicación reducida funciona correctamente una vez implementada, es importante probar las compilaciones de Release de la aplicación con frecuencia durante el desarrollo.</span><span class="sxs-lookup"><span data-stu-id="5bdf5-117">To ensure the trimmed app works correctly once deployed, it's important to test Release builds of the app frequently while developing.</span></span>

<span data-ttu-id="5bdf5-118">La vinculación de las aplicaciones de Blazor se puede configurar con estas características de MSBuild:</span><span class="sxs-lookup"><span data-stu-id="5bdf5-118">Linking for Blazor apps can be configured using these MSBuild features:</span></span>

* <span data-ttu-id="5bdf5-119">Configuración de la vinculación global con una [propiedad de MSBuild](#control-linking-with-an-msbuild-property).</span><span class="sxs-lookup"><span data-stu-id="5bdf5-119">Configure linking globally with a [MSBuild property](#control-linking-with-an-msbuild-property).</span></span>
* <span data-ttu-id="5bdf5-120">Control de la vinculación por cada ensamblado con un [archivo de configuración](#control-linking-with-a-configuration-file).</span><span class="sxs-lookup"><span data-stu-id="5bdf5-120">Control linking on a per-assembly basis with a [configuration file](#control-linking-with-a-configuration-file).</span></span>

## <a name="control-linking-with-an-msbuild-property"></a><span data-ttu-id="5bdf5-121">Control de la vinculación con una propiedad de MSBuild</span><span class="sxs-lookup"><span data-stu-id="5bdf5-121">Control linking with an MSBuild property</span></span>

<span data-ttu-id="5bdf5-122">La vinculación se habilita cuando una aplicación se compila en la configuración `Release`.</span><span class="sxs-lookup"><span data-stu-id="5bdf5-122">Linking is enabled when an app is built in `Release` configuration.</span></span> <span data-ttu-id="5bdf5-123">Para cambiar esto, configure la propiedad `BlazorWebAssemblyEnableLinking` de MSBuild en el archivo del proyecto:</span><span class="sxs-lookup"><span data-stu-id="5bdf5-123">To change this, configure the `BlazorWebAssemblyEnableLinking` MSBuild property in the project file:</span></span>

```xml
<PropertyGroup>
  <BlazorWebAssemblyEnableLinking>false</BlazorWebAssemblyEnableLinking>
</PropertyGroup>
```

## <a name="control-linking-with-a-configuration-file"></a><span data-ttu-id="5bdf5-124">Control de la vinculación con un archivo de configuración</span><span class="sxs-lookup"><span data-stu-id="5bdf5-124">Control linking with a configuration file</span></span>

<span data-ttu-id="5bdf5-125">Control de la vinculación por cada ensamblado al proporcionar un archivo de configuración XML y especificar el archivo como un elemento MSBuild en el archivo de proyecto:</span><span class="sxs-lookup"><span data-stu-id="5bdf5-125">Control linking on a per-assembly basis by providing an XML configuration file and specifying the file as a MSBuild item in the project file:</span></span>

```xml
<ItemGroup>
  <BlazorLinkerDescriptor Include="LinkerConfig.xml" />
</ItemGroup>
```

<span data-ttu-id="5bdf5-126">*LinkerConfig.xml*:</span><span class="sxs-lookup"><span data-stu-id="5bdf5-126">*LinkerConfig.xml*:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!--
  This file specifies which parts of the BCL or Blazor packages must not be
  stripped by the IL Linker even if they aren't referenced by user code.
-->
<linker>
  <assembly fullname="mscorlib">
    <!--
      Preserve the methods in WasmRuntime because its methods are called by 
      JavaScript client-side code to implement timers.
      Fixes: https://github.com/dotnet/blazor/issues/239
    -->
    <type fullname="System.Threading.WasmRuntime" />
  </assembly>
  <assembly fullname="System.Core">
    <!--
      System.Linq.Expressions* is required by Json.NET and any 
      expression.Compile caller. The assembly isn't stripped.
    -->
    <type fullname="System.Linq.Expressions*" />
  </assembly>
  <!--
    In this example, the app's entry point assembly is listed. The assembly
    isn't stripped by the IL Linker.
  -->
  <assembly fullname="MyCoolBlazorApp" />
</linker>
```

<span data-ttu-id="5bdf5-127">Para obtener más información y ejemplos, vea [Formatos de datos (repositorio mono/linker de GitHub)](https://github.com/mono/linker/blob/master/docs/data-formats.md).</span><span class="sxs-lookup"><span data-stu-id="5bdf5-127">For more information and examples, see [Data Formats (mono/linker GitHub repository)](https://github.com/mono/linker/blob/master/docs/data-formats.md).</span></span>

## <a name="add-an-xml-linker-configuration-file-to-a-library"></a><span data-ttu-id="5bdf5-128">Adición de un archivo de configuración del enlazador XML a una biblioteca</span><span class="sxs-lookup"><span data-stu-id="5bdf5-128">Add an XML linker configuration file to a library</span></span>

<span data-ttu-id="5bdf5-129">Para configurar el enlazador para una biblioteca específica, agregue un archivo de configuración de enlazador XML a la biblioteca como un recurso incrustado.</span><span class="sxs-lookup"><span data-stu-id="5bdf5-129">To configure the linker for a specific library, add an XML linker configuration file into the library as an embedded resource.</span></span> <span data-ttu-id="5bdf5-130">El recurso incrustado debe tener el mismo nombre que el ensamblado.</span><span class="sxs-lookup"><span data-stu-id="5bdf5-130">The embedded resource must have the same name as the assembly.</span></span>

<span data-ttu-id="5bdf5-131">En el ejemplo siguiente, el archivo *LinkerConfig.xml* se especifica como un recurso incrustado que tiene el mismo nombre que el ensamblado de la biblioteca:</span><span class="sxs-lookup"><span data-stu-id="5bdf5-131">In the following example, the *LinkerConfig.xml* file is specified as an embedded resource that has the same name as the library's assembly:</span></span>

```xml
<ItemGroup>
  <EmbeddedResource Include="LinkerConfig.xml">
    <LogicalName>$(MSBuildProjectName).xml</LogicalName>
  </EmbeddedResource>
</ItemGroup>
```

### <a name="configure-the-linker-for-internationalization"></a><span data-ttu-id="5bdf5-132">Configuración del enlazador para la internacionalización</span><span class="sxs-lookup"><span data-stu-id="5bdf5-132">Configure the linker for internationalization</span></span>

<span data-ttu-id="5bdf5-133">De forma predeterminada, la configuración del enlazador de Blazor para aplicaciones WebAssembly de Blazor quita información de internacionalización, excepto para las configuraciones regionales solicitadas de forma explícita.</span><span class="sxs-lookup"><span data-stu-id="5bdf5-133">By default, Blazor's linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="5bdf5-134">Al quitar estos ensamblados se minimiza el tamaño de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="5bdf5-134">Removing these assemblies minimizes the app's size.</span></span>

<span data-ttu-id="5bdf5-135">Para controlar qué ensamblados de I18N se conservan, establezca la propiedad `<BlazorWebAssemblyI18NAssemblies>` de MSBuild en el archivo de proyecto:</span><span class="sxs-lookup"><span data-stu-id="5bdf5-135">To control which I18N assemblies are retained, set the `<BlazorWebAssemblyI18NAssemblies>` MSBuild property in the project file:</span></span>

```xml
<PropertyGroup>
  <BlazorWebAssemblyI18NAssemblies>{all|none|REGION1,REGION2,...}</BlazorWebAssemblyI18NAssemblies>
</PropertyGroup>
```

| <span data-ttu-id="5bdf5-136">Valor de región</span><span class="sxs-lookup"><span data-stu-id="5bdf5-136">Region Value</span></span>     | <span data-ttu-id="5bdf5-137">Ensamblado de región de Mono</span><span class="sxs-lookup"><span data-stu-id="5bdf5-137">Mono region assembly</span></span>    |
| ---
<span data-ttu-id="5bdf5-138">title: "Configuración del enlazador para ASP.NET Core Blazor" autor: descripción: "Aprenda a controlar al enlazador de lenguaje intermedio (IL) al compilar una aplicación Blazor".</span><span class="sxs-lookup"><span data-stu-id="5bdf5-138">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="5bdf5-139">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5bdf5-139">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5bdf5-140">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5bdf5-140">'Blazor'</span></span>
- <span data-ttu-id="5bdf5-141">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5bdf5-141">'Identity'</span></span>
- <span data-ttu-id="5bdf5-142">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5bdf5-142">'Let's Encrypt'</span></span>
- <span data-ttu-id="5bdf5-143">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5bdf5-143">'Razor'</span></span>
- <span data-ttu-id="5bdf5-144">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5bdf5-144">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5bdf5-145">title: "Configuración del enlazador para ASP.NET Core Blazor" autor: descripción: "Aprenda a controlar al enlazador de lenguaje intermedio (IL) al compilar una aplicación Blazor".</span><span class="sxs-lookup"><span data-stu-id="5bdf5-145">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="5bdf5-146">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5bdf5-146">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5bdf5-147">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5bdf5-147">'Blazor'</span></span>
- <span data-ttu-id="5bdf5-148">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5bdf5-148">'Identity'</span></span>
- <span data-ttu-id="5bdf5-149">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5bdf5-149">'Let's Encrypt'</span></span>
- <span data-ttu-id="5bdf5-150">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5bdf5-150">'Razor'</span></span>
- <span data-ttu-id="5bdf5-151">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5bdf5-151">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5bdf5-152">title: "Configuración del enlazador para ASP.NET Core Blazor" autor: descripción: "Aprenda a controlar al enlazador de lenguaje intermedio (IL) al compilar una aplicación Blazor".</span><span class="sxs-lookup"><span data-stu-id="5bdf5-152">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="5bdf5-153">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5bdf5-153">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5bdf5-154">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5bdf5-154">'Blazor'</span></span>
- <span data-ttu-id="5bdf5-155">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5bdf5-155">'Identity'</span></span>
- <span data-ttu-id="5bdf5-156">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5bdf5-156">'Let's Encrypt'</span></span>
- <span data-ttu-id="5bdf5-157">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5bdf5-157">'Razor'</span></span>
- <span data-ttu-id="5bdf5-158">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5bdf5-158">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5bdf5-159">title: "Configuración del enlazador para ASP.NET Core Blazor" autor: descripción: "Aprenda a controlar al enlazador de lenguaje intermedio (IL) al compilar una aplicación Blazor".</span><span class="sxs-lookup"><span data-stu-id="5bdf5-159">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="5bdf5-160">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5bdf5-160">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5bdf5-161">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5bdf5-161">'Blazor'</span></span>
- <span data-ttu-id="5bdf5-162">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5bdf5-162">'Identity'</span></span>
- <span data-ttu-id="5bdf5-163">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5bdf5-163">'Let's Encrypt'</span></span>
- <span data-ttu-id="5bdf5-164">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5bdf5-164">'Razor'</span></span>
- <span data-ttu-id="5bdf5-165">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5bdf5-165">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5bdf5-166">title: "Configuración del enlazador para ASP.NET Core Blazor" autor: descripción: "Aprenda a controlar al enlazador de lenguaje intermedio (IL) al compilar una aplicación Blazor".</span><span class="sxs-lookup"><span data-stu-id="5bdf5-166">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="5bdf5-167">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5bdf5-167">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5bdf5-168">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5bdf5-168">'Blazor'</span></span>
- <span data-ttu-id="5bdf5-169">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5bdf5-169">'Identity'</span></span>
- <span data-ttu-id="5bdf5-170">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5bdf5-170">'Let's Encrypt'</span></span>
- <span data-ttu-id="5bdf5-171">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5bdf5-171">'Razor'</span></span>
- <span data-ttu-id="5bdf5-172">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5bdf5-172">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5bdf5-173">title: "Configuración del enlazador para ASP.NET Core Blazor" autor: descripción: "Aprenda a controlar al enlazador de lenguaje intermedio (IL) al compilar una aplicación Blazor".</span><span class="sxs-lookup"><span data-stu-id="5bdf5-173">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="5bdf5-174">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5bdf5-174">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5bdf5-175">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5bdf5-175">'Blazor'</span></span>
- <span data-ttu-id="5bdf5-176">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5bdf5-176">'Identity'</span></span>
- <span data-ttu-id="5bdf5-177">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5bdf5-177">'Let's Encrypt'</span></span>
- <span data-ttu-id="5bdf5-178">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5bdf5-178">'Razor'</span></span>
- <span data-ttu-id="5bdf5-179">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5bdf5-179">'SignalR' uid:</span></span> 

<span data-ttu-id="5bdf5-180">-------- | --- title: "Configuración del enlazador para ASP.NET Core Blazor" autor: descripción: "Aprenda a controlar al enlazador de lenguaje intermedio (IL) al compilar una aplicación Blazor".</span><span class="sxs-lookup"><span data-stu-id="5bdf5-180">-------- | --- title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="5bdf5-181">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5bdf5-181">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5bdf5-182">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5bdf5-182">'Blazor'</span></span>
- <span data-ttu-id="5bdf5-183">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5bdf5-183">'Identity'</span></span>
- <span data-ttu-id="5bdf5-184">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5bdf5-184">'Let's Encrypt'</span></span>
- <span data-ttu-id="5bdf5-185">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5bdf5-185">'Razor'</span></span>
- <span data-ttu-id="5bdf5-186">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5bdf5-186">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5bdf5-187">title: "Configuración del enlazador para ASP.NET Core Blazor" autor: descripción: "Aprenda a controlar al enlazador de lenguaje intermedio (IL) al compilar una aplicación Blazor".</span><span class="sxs-lookup"><span data-stu-id="5bdf5-187">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="5bdf5-188">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5bdf5-188">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5bdf5-189">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5bdf5-189">'Blazor'</span></span>
- <span data-ttu-id="5bdf5-190">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5bdf5-190">'Identity'</span></span>
- <span data-ttu-id="5bdf5-191">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5bdf5-191">'Let's Encrypt'</span></span>
- <span data-ttu-id="5bdf5-192">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5bdf5-192">'Razor'</span></span>
- <span data-ttu-id="5bdf5-193">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5bdf5-193">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5bdf5-194">title: "Configuración del enlazador para ASP.NET Core Blazor" autor: descripción: "Aprenda a controlar al enlazador de lenguaje intermedio (IL) al compilar una aplicación Blazor".</span><span class="sxs-lookup"><span data-stu-id="5bdf5-194">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="5bdf5-195">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5bdf5-195">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5bdf5-196">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5bdf5-196">'Blazor'</span></span>
- <span data-ttu-id="5bdf5-197">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5bdf5-197">'Identity'</span></span>
- <span data-ttu-id="5bdf5-198">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5bdf5-198">'Let's Encrypt'</span></span>
- <span data-ttu-id="5bdf5-199">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5bdf5-199">'Razor'</span></span>
- <span data-ttu-id="5bdf5-200">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5bdf5-200">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5bdf5-201">title: "Configuración del enlazador para ASP.NET Core Blazor" autor: descripción: "Aprenda a controlar al enlazador de lenguaje intermedio (IL) al compilar una aplicación Blazor".</span><span class="sxs-lookup"><span data-stu-id="5bdf5-201">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="5bdf5-202">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5bdf5-202">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5bdf5-203">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5bdf5-203">'Blazor'</span></span>
- <span data-ttu-id="5bdf5-204">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5bdf5-204">'Identity'</span></span>
- <span data-ttu-id="5bdf5-205">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5bdf5-205">'Let's Encrypt'</span></span>
- <span data-ttu-id="5bdf5-206">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5bdf5-206">'Razor'</span></span>
- <span data-ttu-id="5bdf5-207">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5bdf5-207">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5bdf5-208">title: "Configuración del enlazador para ASP.NET Core Blazor" autor: descripción: "Aprenda a controlar al enlazador de lenguaje intermedio (IL) al compilar una aplicación Blazor".</span><span class="sxs-lookup"><span data-stu-id="5bdf5-208">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="5bdf5-209">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5bdf5-209">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5bdf5-210">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5bdf5-210">'Blazor'</span></span>
- <span data-ttu-id="5bdf5-211">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5bdf5-211">'Identity'</span></span>
- <span data-ttu-id="5bdf5-212">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5bdf5-212">'Let's Encrypt'</span></span>
- <span data-ttu-id="5bdf5-213">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5bdf5-213">'Razor'</span></span>
- <span data-ttu-id="5bdf5-214">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5bdf5-214">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5bdf5-215">title: "Configuración del enlazador para ASP.NET Core Blazor" autor: descripción: "Aprenda a controlar al enlazador de lenguaje intermedio (IL) al compilar una aplicación Blazor".</span><span class="sxs-lookup"><span data-stu-id="5bdf5-215">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="5bdf5-216">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5bdf5-216">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5bdf5-217">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5bdf5-217">'Blazor'</span></span>
- <span data-ttu-id="5bdf5-218">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5bdf5-218">'Identity'</span></span>
- <span data-ttu-id="5bdf5-219">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5bdf5-219">'Let's Encrypt'</span></span>
- <span data-ttu-id="5bdf5-220">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5bdf5-220">'Razor'</span></span>
- <span data-ttu-id="5bdf5-221">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5bdf5-221">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5bdf5-222">title: "Configuración del enlazador para ASP.NET Core Blazor" autor: descripción: "Aprenda a controlar al enlazador de lenguaje intermedio (IL) al compilar una aplicación Blazor".</span><span class="sxs-lookup"><span data-stu-id="5bdf5-222">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="5bdf5-223">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5bdf5-223">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5bdf5-224">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5bdf5-224">'Blazor'</span></span>
- <span data-ttu-id="5bdf5-225">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5bdf5-225">'Identity'</span></span>
- <span data-ttu-id="5bdf5-226">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5bdf5-226">'Let's Encrypt'</span></span>
- <span data-ttu-id="5bdf5-227">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5bdf5-227">'Razor'</span></span>
- <span data-ttu-id="5bdf5-228">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5bdf5-228">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5bdf5-229">title: "Configuración del enlazador para ASP.NET Core Blazor" autor: descripción: "Aprenda a controlar al enlazador de lenguaje intermedio (IL) al compilar una aplicación Blazor".</span><span class="sxs-lookup"><span data-stu-id="5bdf5-229">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="5bdf5-230">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5bdf5-230">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5bdf5-231">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5bdf5-231">'Blazor'</span></span>
- <span data-ttu-id="5bdf5-232">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5bdf5-232">'Identity'</span></span>
- <span data-ttu-id="5bdf5-233">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5bdf5-233">'Let's Encrypt'</span></span>
- <span data-ttu-id="5bdf5-234">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5bdf5-234">'Razor'</span></span>
- <span data-ttu-id="5bdf5-235">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5bdf5-235">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5bdf5-236">title: "Configuración del enlazador para ASP.NET Core Blazor" autor: descripción: "Aprenda a controlar al enlazador de lenguaje intermedio (IL) al compilar una aplicación Blazor".</span><span class="sxs-lookup"><span data-stu-id="5bdf5-236">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="5bdf5-237">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5bdf5-237">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5bdf5-238">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5bdf5-238">'Blazor'</span></span>
- <span data-ttu-id="5bdf5-239">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5bdf5-239">'Identity'</span></span>
- <span data-ttu-id="5bdf5-240">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5bdf5-240">'Let's Encrypt'</span></span>
- <span data-ttu-id="5bdf5-241">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5bdf5-241">'Razor'</span></span>
- <span data-ttu-id="5bdf5-242">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5bdf5-242">'SignalR' uid:</span></span> 

<span data-ttu-id="5bdf5-243">------------ | | `all`            | Todos los ensamblados incluidos | | `cjk`            | *I18N.CJK.dll*          | | `mideast`        | *I18N.MidEast.dll*      | | `none` (predeterminado) | Ninguno                    | | `other`          | *I18N.Other.dll*        | | `rare`           | *I18N.Rare.dll*         | | `west`           | *I18N.West.dll*         |</span><span class="sxs-lookup"><span data-stu-id="5bdf5-243">------------ | | `all`            | All assemblies included | | `cjk`            | *I18N.CJK.dll*          | | `mideast`        | *I18N.MidEast.dll*      | | `none` (default) | None                    | | `other`          | *I18N.Other.dll*        | | `rare`           | *I18N.Rare.dll*         | | `west`           | *I18N.West.dll*         |</span></span>

<span data-ttu-id="5bdf5-244">Use una coma para separar varios valores (por ejemplo, `mideast,west`).</span><span class="sxs-lookup"><span data-stu-id="5bdf5-244">Use a comma to separate multiple values (for example, `mideast,west`).</span></span>

<span data-ttu-id="5bdf5-245">Para más información, vea [I18N: biblioteca del marco de internacionalización Pnetlib (repositorio de GitHub mono/mono)](https://github.com/mono/mono/tree/master/mcs/class/I18N).</span><span class="sxs-lookup"><span data-stu-id="5bdf5-245">For more information, see [I18N: Pnetlib Internationalization Framework Library (mono/mono GitHub repository)](https://github.com/mono/mono/tree/master/mcs/class/I18N).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5bdf5-246">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="5bdf5-246">Additional resources</span></span>

* <xref:performance/blazor/webassembly-best-practices#intermediate-language-il-linking>
