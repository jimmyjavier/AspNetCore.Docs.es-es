---
title: 'title: "Llamada a una API web desde ASP.NET Core Blazor WebAssembly" author: guardrex description: "Aprenda a llamar a una API web desde una aplicación Blazor WebAssembly mediante asistentes de JSON, incluida la realización de solicitudes de uso compartido de recursos entre orígenes (CORS)".'
author: guardrex
description: "monikerRange: '>= aspnetcore-3.1' ms.author: riande ms.custom: mvc ms.date: 05/28/2020 no-loc:"
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/28/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/call-web-api
ms.openlocfilehash: 4f3fd1fca075098a94756757474b2cad3cc99775
ms.sourcegitcommit: d8c35c37c90f81bd2cd7ae5e1700f2fede1b4b75
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/29/2020
ms.locfileid: "84214773"
---
# <a name="call-a-web-api-from-aspnet-core-blazor"></a><span data-ttu-id="f0c8a-103">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="f0c8a-103">'Blazor'</span></span>

<span data-ttu-id="f0c8a-104">"Identity"</span><span class="sxs-lookup"><span data-stu-id="f0c8a-104">'Identity'</span></span>

<span data-ttu-id="f0c8a-105">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="f0c8a-105">'Let's Encrypt'</span></span> <span data-ttu-id="f0c8a-106">"Razor"</span><span class="sxs-lookup"><span data-stu-id="f0c8a-106">'Razor'</span></span> <span data-ttu-id="f0c8a-107">"SignalR" uid: blazor/call-web-api</span><span class="sxs-lookup"><span data-stu-id="f0c8a-107">'SignalR' uid: blazor/call-web-api</span></span> <span data-ttu-id="f0c8a-108">Llamada a una API web desde Blazor de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f0c8a-108">Call a web API from ASP.NET Core Blazor</span></span>

<span data-ttu-id="f0c8a-109">Por [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27) y [Juan De la Cruz](https://github.com/juandelacruz23)</span><span class="sxs-lookup"><span data-stu-id="f0c8a-109">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Juan De la Cruz](https://github.com/juandelacruz23)</span></span> <span data-ttu-id="f0c8a-110">Las aplicaciones [WebAssembly de Blazor](xref:blazor/hosting-models#blazor-webassembly) llaman a las API web mediante un servicio <xref:System.Net.Http.HttpClient> preconfigurado.</span><span class="sxs-lookup"><span data-stu-id="f0c8a-110">[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) apps call web APIs using a preconfigured <xref:System.Net.Http.HttpClient> service.</span></span> <span data-ttu-id="f0c8a-111">Redacte las solicitudes, que pueden incluir opciones [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) de JavaScript, mediante asistentes de JSON de Blazor o con <xref:System.Net.Http.HttpRequestMessage>.</span><span class="sxs-lookup"><span data-stu-id="f0c8a-111">Compose requests, which can include JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) options, using Blazor JSON helpers or with <xref:System.Net.Http.HttpRequestMessage>.</span></span>

<span data-ttu-id="f0c8a-112">El servicio <xref:System.Net.Http.HttpClient> en las aplicaciones WebAssembly de Blazor se centra en realizar solicitudes de vuelta al servidor de origen.</span><span class="sxs-lookup"><span data-stu-id="f0c8a-112">The <xref:System.Net.Http.HttpClient> service in Blazor WebAssembly apps is focused on making requests back to the server of origin.</span></span>

<span data-ttu-id="f0c8a-113">Las instrucciones de este tema solo se aplican a las aplicaciones WebAssembly de Blazor.</span><span class="sxs-lookup"><span data-stu-id="f0c8a-113">The guidance in this topic only pertains to Blazor WebAssembly apps.</span></span>

* <span data-ttu-id="f0c8a-114">Las aplicaciones de [servidor Blazor](xref:blazor/hosting-models#blazor-server) llaman a las API web mediante instancias de <xref:System.Net.Http.HttpClient>, creadas normalmente con <xref:System.Net.Http.IHttpClientFactory>.</span><span class="sxs-lookup"><span data-stu-id="f0c8a-114">[Blazor Server](xref:blazor/hosting-models#blazor-server) apps call web APIs using <xref:System.Net.Http.HttpClient> instances, typically created using <xref:System.Net.Http.IHttpClientFactory>.</span></span>
* <span data-ttu-id="f0c8a-115">Las instrucciones de este tema no se aplican a las aplicaciones de servidor Blazor.</span><span class="sxs-lookup"><span data-stu-id="f0c8a-115">The guidance in this topic doesn't pertain to Blazor Server apps.</span></span>

## <a name="packages"></a><span data-ttu-id="f0c8a-116">Al desarrollar aplicaciones de servidor Blazor, siga las instrucciones de <xref:fundamentals/http-requests>.</span><span class="sxs-lookup"><span data-stu-id="f0c8a-116">When developing Blazor Server apps, follow the guidance in <xref:fundamentals/http-requests>.</span></span>

<span data-ttu-id="f0c8a-117">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample)): Seleccione la aplicación *BlazorWebAssemblySample*.</span><span class="sxs-lookup"><span data-stu-id="f0c8a-117">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample)): Select the *BlazorWebAssemblySample* app.</span></span>

## <a name="add-the-httpclient-service"></a><span data-ttu-id="f0c8a-118">Vea los componentes siguientes en la aplicación de ejemplo *BlazorWebAssemblySample*:</span><span class="sxs-lookup"><span data-stu-id="f0c8a-118">See the following components in the *BlazorWebAssemblySample* sample app:</span></span>

<span data-ttu-id="f0c8a-119">Llamada a la API web (*Pages/CallWebAPI.razor*)</span><span class="sxs-lookup"><span data-stu-id="f0c8a-119">Call Web API (*Pages/CallWebAPI.razor*)</span></span>

```csharp
builder.Services.AddTransient(sp => 
    new HttpClient
    {
        BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
    });
```

## <a name="httpclient-and-json-helpers"></a><span data-ttu-id="f0c8a-120">Evaluador de solicitudes HTTP (*Components/HTTPRequestTester.razor*)</span><span class="sxs-lookup"><span data-stu-id="f0c8a-120">HTTP Request Tester (*Components/HTTPRequestTester.razor*)</span></span>

<span data-ttu-id="f0c8a-121">Paquetes</span><span class="sxs-lookup"><span data-stu-id="f0c8a-121">Packages</span></span>

<span data-ttu-id="f0c8a-122">Haga referencia al paquete NuGet [System.Net.Http.Json](https://www.nuget.org/packages/System.Net.Http.Json/) en el archivo del proyecto.</span><span class="sxs-lookup"><span data-stu-id="f0c8a-122">Reference the [System.Net.Http.Json](https://www.nuget.org/packages/System.Net.Http.Json/) NuGet package in the project file.</span></span> <span data-ttu-id="f0c8a-123">Agregar el servicio HttpClient</span><span class="sxs-lookup"><span data-stu-id="f0c8a-123">Add the HttpClient service</span></span>

<span data-ttu-id="f0c8a-124">En `Program.Main`, agregue un servicio <xref:System.Net.Http.HttpClient> si aún no existe:</span><span class="sxs-lookup"><span data-stu-id="f0c8a-124">In `Program.Main`, add an <xref:System.Net.Http.HttpClient> service if it doesn't already exist:</span></span> <span data-ttu-id="f0c8a-125">HttpClient y asistentes de JSON</span><span class="sxs-lookup"><span data-stu-id="f0c8a-125">HttpClient and JSON helpers</span></span>

<span data-ttu-id="f0c8a-126">En una aplicación WebAssembly de Blazor, [HttpClient](xref:fundamentals/http-requests) está disponible como un servicio preconfigurado para realizar solicitudes de vuelta al servidor de origen.</span><span class="sxs-lookup"><span data-stu-id="f0c8a-126">In a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) is available as a preconfigured service for making requests back to the origin server.</span></span> <span data-ttu-id="f0c8a-127">De forma predeterminada, una aplicación de servidor Blazor no incluye un servicio <xref:System.Net.Http.HttpClient>.</span><span class="sxs-lookup"><span data-stu-id="f0c8a-127">A Blazor Server app doesn't include an <xref:System.Net.Http.HttpClient> service by default.</span></span>

```razor
@using System.Net.Http
@inject HttpClient Http
```

<span data-ttu-id="f0c8a-128">Proporcione un servicio <xref:System.Net.Http.HttpClient> a la aplicación mediante la [infraestructura de fábrica de HttpClient](xref:fundamentals/http-requests).</span><span class="sxs-lookup"><span data-stu-id="f0c8a-128">Provide an <xref:System.Net.Http.HttpClient> to the app using the [HttpClient factory infrastructure](xref:fundamentals/http-requests).</span></span> <span data-ttu-id="f0c8a-129"><xref:System.Net.Http.HttpClient> y los asistentes de JSON también se usan para llamar a puntos de conexión de API web de terceros.</span><span class="sxs-lookup"><span data-stu-id="f0c8a-129"><xref:System.Net.Http.HttpClient> and JSON helpers are also used to call third-party web API endpoints.</span></span>

* <span data-ttu-id="f0c8a-130"><xref:System.Net.Http.HttpClient> se implementa mediante [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) del explorador y está sujeto a sus limitaciones, incluido el cumplimiento de la directiva del mismo origen.</span><span class="sxs-lookup"><span data-stu-id="f0c8a-130"><xref:System.Net.Http.HttpClient> is implemented using the browser [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) and is subject to its limitations, including enforcement of the same origin policy.</span></span>
* <span data-ttu-id="f0c8a-131">La dirección base del cliente se establece en la dirección del servidor de origen.</span><span class="sxs-lookup"><span data-stu-id="f0c8a-131">The client's base address is set to the originating server's address.</span></span>
* <span data-ttu-id="f0c8a-132">Inserte una instancia de <xref:System.Net.Http.HttpClient> mediante la directiva [`@inject`](xref:mvc/views/razor#inject):</span><span class="sxs-lookup"><span data-stu-id="f0c8a-132">Inject an <xref:System.Net.Http.HttpClient> instance using the [`@inject`](xref:mvc/views/razor#inject) directive:</span></span>

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

<span data-ttu-id="f0c8a-133">En los ejemplos siguientes, una API web Todo procesa operaciones de creación, lectura, actualización y eliminación (CRUD).</span><span class="sxs-lookup"><span data-stu-id="f0c8a-133">In the following examples, a Todo web API processes create, read, update, and delete (CRUD) operations.</span></span>

* <span data-ttu-id="f0c8a-134">Los ejemplos se basan en una clase `TodoItem` que almacena lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="f0c8a-134">The examples are based on a `TodoItem` class that stores the:</span></span>

  <span data-ttu-id="f0c8a-135">Id. (`Id`, `long`): identificador único del elemento.</span><span class="sxs-lookup"><span data-stu-id="f0c8a-135">ID (`Id`, `long`): Unique ID of the item.</span></span> <span data-ttu-id="f0c8a-136">Nombre (`Name`, `string`): Nombre del elemento.</span><span class="sxs-lookup"><span data-stu-id="f0c8a-136">Name (`Name`, `string`): Name of the item.</span></span> <span data-ttu-id="f0c8a-137">Estado (`IsComplete`, `bool`): indicación de si el elemento Todo ha finalizado.</span><span class="sxs-lookup"><span data-stu-id="f0c8a-137">Status (`IsComplete`, `bool`): Indication if the Todo item is finished.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] todoItems;

      protected override async Task OnInitializedAsync() => 
          todoItems = await Http.GetFromJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* <span data-ttu-id="f0c8a-138">Los métodos auxiliares de JSON envían solicitudes a un URI (una API web en los ejemplos siguientes) y procesan la respuesta:</span><span class="sxs-lookup"><span data-stu-id="f0c8a-138">JSON helper methods send requests to a URI (a web API in the following examples) and process the response:</span></span>

  <span data-ttu-id="f0c8a-139"><xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A>: envía una solicitud HTTP GET y analiza el cuerpo de la respuesta JSON para crear un objeto.</span><span class="sxs-lookup"><span data-stu-id="f0c8a-139"><xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A>: Sends an HTTP GET request and parses the JSON response body to create an object.</span></span> <span data-ttu-id="f0c8a-140">En el código siguiente, el componente muestra el elemento `todoItems`.</span><span class="sxs-lookup"><span data-stu-id="f0c8a-140">In the following code, the `todoItems` are displayed by the component.</span></span> <span data-ttu-id="f0c8a-141">El método `GetTodoItems` se desencadena cuando finaliza la representación del componente ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)).</span><span class="sxs-lookup"><span data-stu-id="f0c8a-141">The `GetTodoItems` method is triggered when the component is finished rendering ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)).</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  <input @bind="newItemName" placeholder="New Todo Item" />
  <button @onclick="@AddItem">Add</button>

  @code {
      private string newItemName;

      private async Task AddItem()
      {
          var addItem = new TodoItem { Name = newItemName, IsComplete = false };
          await Http.PostAsJsonAsync("api/TodoItems", addItem);
      }
  }
  ```
  
  <span data-ttu-id="f0c8a-142">Vea la aplicación de ejemplo para obtener un ejemplo completo.</span><span class="sxs-lookup"><span data-stu-id="f0c8a-142">See the sample app for a complete example.</span></span> <span data-ttu-id="f0c8a-143"><xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A>: envía una solicitud HTTP POST, incluido el contenido con codificación JSON, y analiza el cuerpo de respuesta JSON para crear un objeto.</span><span class="sxs-lookup"><span data-stu-id="f0c8a-143"><xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A>: Sends an HTTP POST request, including JSON-encoded content, and parses the JSON response body to create an object.</span></span>
  
  ```csharp
  var content = response.Content.ReadFromJsonAsync<WeatherForecast>();
  ```

* <span data-ttu-id="f0c8a-144">En el código siguiente, un elemento enlazado del componente proporciona `newItemName`.</span><span class="sxs-lookup"><span data-stu-id="f0c8a-144">In the following code, `newItemName` is provided by a bound element of the component.</span></span>

  <span data-ttu-id="f0c8a-145">El método `AddItem` se desencadena al seleccionar un elemento `<button>`.</span><span class="sxs-lookup"><span data-stu-id="f0c8a-145">The `AddItem` method is triggered by selecting a `<button>` element.</span></span> <span data-ttu-id="f0c8a-146">Vea la aplicación de ejemplo para obtener un ejemplo completo.</span><span class="sxs-lookup"><span data-stu-id="f0c8a-146">See the sample app for a complete example.</span></span> <span data-ttu-id="f0c8a-147">Las llamadas a <xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A> devuelven <xref:System.Net.Http.HttpResponseMessage>.</span><span class="sxs-lookup"><span data-stu-id="f0c8a-147">Calls to <xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A> return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="f0c8a-148">Para deserializar el contenido JSON del mensaje de respuesta, use el método de extensión `ReadFromJsonAsync<T>`:</span><span class="sxs-lookup"><span data-stu-id="f0c8a-148">To deserialize the JSON content from the response message, use the `ReadFromJsonAsync<T>` extension method:</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  <input type="checkbox" @bind="editItem.IsComplete" />
  <input @bind="editItem.Name" />
  <button @onclick="@SaveItem">Save</button>

  @code {
      private TodoItem editItem = new TodoItem();

      private void EditItem(long id)
      {
          editItem = todoItems.Single(i => i.Id == id);
      }

      private async Task SaveItem() =>
          await Http.PutAsJsonAsync($"api/TodoItems/{editItem.Id}, editItem);
  }
  ```
  
  <span data-ttu-id="f0c8a-149"><xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A>: envía una solicitud HTTP PUT, incluido el contenido con codificación JSON.</span><span class="sxs-lookup"><span data-stu-id="f0c8a-149"><xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A>: Sends an HTTP PUT request, including JSON-encoded content.</span></span> <span data-ttu-id="f0c8a-150">En el código siguiente, los elementos enlazados del componente proporcionan valores `editItem` para `Name` e `IsCompleted`.</span><span class="sxs-lookup"><span data-stu-id="f0c8a-150">In the following code, `editItem` values for `Name` and `IsCompleted` are provided by bound elements of the component.</span></span>
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

<span data-ttu-id="f0c8a-151">El valor `Id` del elemento se establece cuando el elemento está seleccionado en otra parte de la interfaz de usuario y se llama a `EditItem`.</span><span class="sxs-lookup"><span data-stu-id="f0c8a-151">The item's `Id` is set when the item is selected in another part of the UI and `EditItem` is called.</span></span> <span data-ttu-id="f0c8a-152">El método `SaveItem` se desencadena al seleccionar un objeto `<button>` Guardar.</span><span class="sxs-lookup"><span data-stu-id="f0c8a-152">The `SaveItem` method is triggered by selecting the Save `<button>` element.</span></span>

<span data-ttu-id="f0c8a-153">Vea la aplicación de ejemplo para obtener un ejemplo completo.</span><span class="sxs-lookup"><span data-stu-id="f0c8a-153">See the sample app for a complete example.</span></span> <span data-ttu-id="f0c8a-154">Las llamadas a <xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A> devuelven <xref:System.Net.Http.HttpResponseMessage>.</span><span class="sxs-lookup"><span data-stu-id="f0c8a-154">Calls to <xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A> return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="f0c8a-155">Para deserializar el contenido JSON del mensaje de respuesta, use el método de extensión <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A>:</span><span class="sxs-lookup"><span data-stu-id="f0c8a-155">To deserialize the JSON content from the response message, use the <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> extension method:</span></span>

```razor
@using System.Net.Http
@inject HttpClient Http

<input @bind="id" />
<button @onclick="@DeleteItem">Delete</button>

@code {
    private long id;

    private async Task DeleteItem() =>
        await Http.DeleteAsync($"api/TodoItems/{id}");
}
```

## <a name="named-httpclient-with-ihttpclientfactory"></a><span data-ttu-id="f0c8a-156"><xref:System.Net.Http> incluye métodos de extensión adicionales para enviar solicitudes HTTP y recibir respuestas HTTP.</span><span class="sxs-lookup"><span data-stu-id="f0c8a-156"><xref:System.Net.Http> includes additional extension methods for sending HTTP requests and receiving HTTP responses.</span></span>

<span data-ttu-id="f0c8a-157"><xref:System.Net.Http.HttpClient.DeleteAsync%2A?displayProperty=nameWithType> se usa para enviar una solicitud HTTP DELETE a una API web.</span><span class="sxs-lookup"><span data-stu-id="f0c8a-157"><xref:System.Net.Http.HttpClient.DeleteAsync%2A?displayProperty=nameWithType> is used to send an HTTP DELETE request to a web API.</span></span>

<span data-ttu-id="f0c8a-158">En el código siguiente, el elemento `<button>` Delete llama al método `DeleteItem`.</span><span class="sxs-lookup"><span data-stu-id="f0c8a-158">In the following code, the Delete `<button>` element calls the `DeleteItem` method.</span></span>

<span data-ttu-id="f0c8a-159">El elemento `<input>` enlazado proporciona el valor `id` del elemento que se va a eliminar.</span><span class="sxs-lookup"><span data-stu-id="f0c8a-159">The bound `<input>` element supplies the `id` of the item to delete.</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="f0c8a-160">Vea la aplicación de ejemplo para obtener un ejemplo completo.</span><span class="sxs-lookup"><span data-stu-id="f0c8a-160">See the sample app for a complete example.</span></span>

```razor
@inject IHttpClientFactory ClientFactory

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        var client = ClientFactory.CreateClient("ServerAPI");

        forecasts = await client.GetFromJsonAsync<WeatherForecast[]>(
            "WeatherForecast");
    }
}
```

## <a name="typed-httpclient"></a><span data-ttu-id="f0c8a-161">HttpClient con nombre con IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="f0c8a-161">Named HttpClient with IHttpClientFactory</span></span>

<span data-ttu-id="f0c8a-162">Se admiten servicios <xref:System.Net.Http.IHttpClientFactory> y la configuración de una instancia de <xref:System.Net.Http.HttpClient> con nombre.</span><span class="sxs-lookup"><span data-stu-id="f0c8a-162"><xref:System.Net.Http.IHttpClientFactory> services and the configuration of a named <xref:System.Net.Http.HttpClient> are supported.</span></span>

<span data-ttu-id="f0c8a-163">Haga referencia al paquete [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) de NuGet en el archivo del proyecto.</span><span class="sxs-lookup"><span data-stu-id="f0c8a-163">Reference the [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet package in the project file.</span></span>

```csharp
using System.Net.Http;
using System.Net.Http.Json;
using System.Threading.Tasks;

public class WeatherForecastClient
{
    private readonly HttpClient client;

    public WeatherForecastClient(HttpClient client)
    {
        this.client = client;
    }

    public async Task<WeatherForecast[]> GetForecastAsync()
    {
        var forecasts = new WeatherForecast[0];
    
        try
        {
            forecasts = await client.GetFromJsonAsync<WeatherForecast[]>(
                "WeatherForecast");
        }
        catch
        {
            ...
        }
    
        return forecasts;
    }
}
```

<span data-ttu-id="f0c8a-164">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="f0c8a-164">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="f0c8a-165">Componente `FetchData`(*Pages/FetchData.razor*):</span><span class="sxs-lookup"><span data-stu-id="f0c8a-165">`FetchData` component (*Pages/FetchData.razor*):</span></span>

<span data-ttu-id="f0c8a-166">HttpClient con tipo</span><span class="sxs-lookup"><span data-stu-id="f0c8a-166">Typed HttpClient</span></span>

```razor
@inject WeatherForecastClient Client

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        forecasts = await Client.GetForecastAsync();
    }
}
```

## <a name="handle-errors"></a><span data-ttu-id="f0c8a-167">La instancia de <xref:System.Net.Http.HttpClient> con tipo usa una o varias instancias de <xref:System.Net.Http.HttpClient> de la aplicación, ya sean predeterminadas o con nombre, para devolver datos de uno o varios puntos de conexión de la API web.</span><span class="sxs-lookup"><span data-stu-id="f0c8a-167">Typed <xref:System.Net.Http.HttpClient> uses one or more of the app's <xref:System.Net.Http.HttpClient> instances, default or named, to return data from one or more web API endpoints.</span></span>

<span data-ttu-id="f0c8a-168">*WeatherForecastClient.cs*:</span><span class="sxs-lookup"><span data-stu-id="f0c8a-168">*WeatherForecastClient.cs*:</span></span> <span data-ttu-id="f0c8a-169">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="f0c8a-169">`Program.Main` (*Program.cs*):</span></span> <span data-ttu-id="f0c8a-170">Los componentes insertan la instancia de <xref:System.Net.Http.HttpClient> con tipo para llamar a la API web.</span><span class="sxs-lookup"><span data-stu-id="f0c8a-170">Components inject the typed <xref:System.Net.Http.HttpClient> to call the web API.</span></span>

<span data-ttu-id="f0c8a-171">Componente `FetchData`(*Pages/FetchData.razor*):</span><span class="sxs-lookup"><span data-stu-id="f0c8a-171">`FetchData` component (*Pages/FetchData.razor*):</span></span> <span data-ttu-id="f0c8a-172">Control de errores</span><span class="sxs-lookup"><span data-stu-id="f0c8a-172">Handle errors</span></span>

<span data-ttu-id="f0c8a-173">Cuando se producen errores al interactuar con una API web, pueden controlarse mediante código de desarrollador.</span><span class="sxs-lookup"><span data-stu-id="f0c8a-173">When errors occur while interacting with a web API, they can be handled by developer code.</span></span> <span data-ttu-id="f0c8a-174">Por ejemplo, <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> espera una respuesta JSON de la API del servidor con un `Content-Type` de `application/json`.</span><span class="sxs-lookup"><span data-stu-id="f0c8a-174">For example, <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> expects a JSON response from the server API with a `Content-Type` of `application/json`.</span></span>

<span data-ttu-id="f0c8a-175">Si la respuesta no está en formato JSON, la validación del contenido producirá una excepción <xref:System.NotSupportedException>.</span><span class="sxs-lookup"><span data-stu-id="f0c8a-175">If the response isn't in JSON format, content validation throws a <xref:System.NotSupportedException>.</span></span> <span data-ttu-id="f0c8a-176">En el ejemplo siguiente, el punto de conexión del URI para la solicitud de datos de previsión meteorológica está mal escrito.</span><span class="sxs-lookup"><span data-stu-id="f0c8a-176">In the following example, the URI endpoint for the weather forecast data request is misspelled.</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    try
    {
        forecasts = await Http.GetFromJsonAsync<WeatherForecast[]>(
            "WeatherForcast");
    }
    catch (NotSupportedException exception)
    {
        ...
    }
}
```

> [!NOTE]
> <span data-ttu-id="f0c8a-177">El URI debería ser `WeatherForecast`, pero en la llamada aparece como `WeatherForcast` (falta una "e").</span><span class="sxs-lookup"><span data-stu-id="f0c8a-177">The URI should be to `WeatherForecast` but appears in the call as `WeatherForcast` (missing "e").</span></span> <span data-ttu-id="f0c8a-178">La llamada a <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> espera que se devuelva JSON, pero el servidor devuelve HTML para una excepción no controlada en el servidor con un `Content-Type` de `text/html`.</span><span class="sxs-lookup"><span data-stu-id="f0c8a-178">The <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> call expects JSON to be returned, but the server returns HTML for an unhandled exception on the server with a `Content-Type` of `text/html`.</span></span>

<span data-ttu-id="f0c8a-179">La excepción no controlada se produce en el servidor, puesto que no se encuentra la ruta de acceso y el middleware no puede proporcionar una página o vista para la solicitud.</span><span class="sxs-lookup"><span data-stu-id="f0c8a-179">The unhandled exception occurs on the server because the path isn't found and middleware can't serve a page or view for the request.</span></span>

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="f0c8a-180">En <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> en el cliente, se produce una excepción <xref:System.NotSupportedException> cuando el contenido de la respuesta se valida como distinto de JSON.</span><span class="sxs-lookup"><span data-stu-id="f0c8a-180">In <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> on the client, <xref:System.NotSupportedException> is thrown when the response content is validated as non-JSON.</span></span>

<span data-ttu-id="f0c8a-181">La excepción se captura en el bloque `catch`, donde la lógica personalizada podría registrar el error o mostrar al usuario un mensaje de error descriptivo:</span><span class="sxs-lookup"><span data-stu-id="f0c8a-181">The exception is caught in the `catch` block, where custom logic could log the error or present a friendly error message to the user:</span></span> <span data-ttu-id="f0c8a-182">El ejemplo anterior sirve de demostración.</span><span class="sxs-lookup"><span data-stu-id="f0c8a-182">The preceding example is for demonstration purposes.</span></span> <span data-ttu-id="f0c8a-183">Se puede configurar una aplicación de servidor de API web para que devuelva JSON incluso cuando no existe un punto de conexión o cuando se produce una excepción no controlada en el servidor.</span><span class="sxs-lookup"><span data-stu-id="f0c8a-183">A web API server app can be configured to return JSON even when an endpoint doesn't exist or an unhandled excpetion on the server occurs.</span></span> <span data-ttu-id="f0c8a-184">Para obtener más información, vea <xref:blazor/handle-errors>.</span><span class="sxs-lookup"><span data-stu-id="f0c8a-184">For more information, see <xref:blazor/handle-errors>.</span></span>

<span data-ttu-id="f0c8a-185">Uso compartido de recursos entre orígenes (CORS)</span><span class="sxs-lookup"><span data-stu-id="f0c8a-185">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="f0c8a-186">La seguridad del explorador evita que una página web realice solicitudes a un dominio diferente del que ha proporcionado esa página web.</span><span class="sxs-lookup"><span data-stu-id="f0c8a-186">Browser security prevents a webpage from making requests to a different domain than the one that served the webpage.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f0c8a-187">Esta restricción se denomina *directiva de mismo origen*.</span><span class="sxs-lookup"><span data-stu-id="f0c8a-187">This restriction is called the *same-origin policy*.</span></span>

* <span data-ttu-id="f0c8a-188">La directiva de mismo origen evita que un sitio malintencionado lea información confidencial de otro sitio.</span><span class="sxs-lookup"><span data-stu-id="f0c8a-188">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span>
* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* <span data-ttu-id="f0c8a-189">Para realizar solicitudes desde el explorador a un punto de conexión con un origen diferente, el *punto de conexión* debe habilitar el [uso compartido de recursos entre orígenes (CORS)](https://www.w3.org/TR/cors/).</span><span class="sxs-lookup"><span data-stu-id="f0c8a-189">To make requests from the browser to an endpoint with a different origin, the *endpoint* must enable [cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/).</span></span>
* <span data-ttu-id="f0c8a-190">En la [aplicación de ejemplo WebAssembly de Blazor (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) se muestra el uso de CORS en el componente Call Web API (*Pages/CallWebAPI.razor*).</span><span class="sxs-lookup"><span data-stu-id="f0c8a-190">The [Blazor WebAssembly sample app (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) demonstrates the use of CORS in the Call Web API component (*Pages/CallWebAPI.razor*).</span></span>
