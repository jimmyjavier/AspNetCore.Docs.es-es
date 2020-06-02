---
<span data-ttu-id="da293-101">title: migrar de Microsoft. Extensions. Logging 2,1 a 2,2 o 3,0 Author: pakrym Description: Aprenda a migrar una aplicación non-ASP.NET Core que usa Microsoft. Extensions. Logging from 2,1 a 2,2 o 3,0.</span><span class="sxs-lookup"><span data-stu-id="da293-101">title: Migrate from Microsoft.Extensions.Logging 2.1 to 2.2 or 3.0 author: pakrym description: Learn how to migrate a non-ASP.NET Core application that uses Microsoft.Extensions.Logging from 2.1 to 2.2 or 3.0.</span></span>
<span data-ttu-id="da293-102">MS. Author: pakrym ms. Custom: MVC ms. Date: 01/04/2019 no-LOC:</span><span class="sxs-lookup"><span data-stu-id="da293-102">ms.author: pakrym ms.custom: mvc ms.date: 01/04/2019 no-loc:</span></span>
- <span data-ttu-id="da293-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="da293-103">'Blazor'</span></span>
- <span data-ttu-id="da293-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="da293-104">'Identity'</span></span>
- <span data-ttu-id="da293-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="da293-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="da293-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="da293-106">'Razor'</span></span>
- <span data-ttu-id="da293-107">' SignalR ' UID: Migration/Logging-nonaspnetcore</span><span class="sxs-lookup"><span data-stu-id="da293-107">'SignalR' uid: migration/logging-nonaspnetcore</span></span>

---

# <a name="migrate-from-microsoftextensionslogging-21-to-22-or-30"></a><span data-ttu-id="da293-108">Migre desde Microsoft. Extensions. Logging 2,1 a 2,2 o 3,0</span><span class="sxs-lookup"><span data-stu-id="da293-108">Migrate from Microsoft.Extensions.Logging 2.1 to 2.2 or 3.0</span></span>

<span data-ttu-id="da293-109">En este artículo se describen los pasos comunes para migrar una aplicación de non-ASP.NET Core que usa `Microsoft.Extensions.Logging` de 2,1 a 2,2 o 3,0.</span><span class="sxs-lookup"><span data-stu-id="da293-109">This article outlines the common steps for migrating a non-ASP.NET Core application that uses `Microsoft.Extensions.Logging` from 2.1 to 2.2 or 3.0.</span></span>

## <a name="21-to-22"></a><span data-ttu-id="da293-110">2.1 a 2.2</span><span class="sxs-lookup"><span data-stu-id="da293-110">2.1 to 2.2</span></span>

<span data-ttu-id="da293-111">Crear `ServiceCollection` y llamar manualmente a `AddLogging` .</span><span class="sxs-lookup"><span data-stu-id="da293-111">Manually create `ServiceCollection` and call `AddLogging`.</span></span>

<span data-ttu-id="da293-112">ejemplo 2,1:</span><span class="sxs-lookup"><span data-stu-id="da293-112">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="da293-113">ejemplo 2,2:</span><span class="sxs-lookup"><span data-stu-id="da293-113">2.2 example:</span></span>

```csharp
var serviceCollection = new ServiceCollection();
serviceCollection.AddLogging(builder => builder.AddConsole());

using (var serviceProvider = serviceCollection.BuildServiceProvider())
using (var loggerFactory = serviceProvider.GetService<ILoggerFactory>())
{
    // use loggerFactory
}
```

## <a name="21-to-30"></a><span data-ttu-id="da293-114">de 2,1 a 3,0</span><span class="sxs-lookup"><span data-stu-id="da293-114">2.1 to 3.0</span></span>

<span data-ttu-id="da293-115">En 3,0, use `LoggingFactory.Create` .</span><span class="sxs-lookup"><span data-stu-id="da293-115">In 3.0, use `LoggingFactory.Create`.</span></span>

<span data-ttu-id="da293-116">ejemplo 2,1:</span><span class="sxs-lookup"><span data-stu-id="da293-116">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="da293-117">ejemplo 3,0:</span><span class="sxs-lookup"><span data-stu-id="da293-117">3.0 example:</span></span>

```csharp
using (var loggerFactory = LoggerFactory.Create(builder => builder.AddConsole()))
{
    // use loggerFactory
}
```

## <a name="additional-resources"></a><span data-ttu-id="da293-118">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="da293-118">Additional resources</span></span>

* <span data-ttu-id="da293-119">[Paquete NuGet Microsoft. Extensions. Logging. Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/).</span><span class="sxs-lookup"><span data-stu-id="da293-119">[Microsoft.Extensions.Logging.Console NuGet package](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/).</span></span>
* <xref:fundamentals/logging/index>
