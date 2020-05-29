---
<span data-ttu-id="dda66-101">title: "Llamada a una API web desde ASP.NET Core Blazor WebAssembly" author: description: "Aprenda a llamar a una API web desde una aplicación Blazor WebAssembly mediante asistentes de JSON, incluida la realización de solicitudes de uso compartido de recursos entre orígenes (CORS)".</span><span class="sxs-lookup"><span data-stu-id="dda66-101">title: 'Call a web API from ASP.NET Core Blazor WebAssembly' author: description: 'Learn how to call a web API from a Blazor WebAssembly app using JSON helpers, including making cross-origin resource sharing (CORS) requests.'</span></span>
<span data-ttu-id="dda66-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dda66-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dda66-103">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="dda66-103">'Blazor'</span></span>
- <span data-ttu-id="dda66-104">"Identity"</span><span class="sxs-lookup"><span data-stu-id="dda66-104">'Identity'</span></span>
- <span data-ttu-id="dda66-105">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="dda66-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="dda66-106">"Razor"</span><span class="sxs-lookup"><span data-stu-id="dda66-106">'Razor'</span></span>
- <span data-ttu-id="dda66-107">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="dda66-107">'SignalR' uid:</span></span> 

---
# <a name="call-a-web-api-from-aspnet-core-blazor"></a><span data-ttu-id="dda66-108">Llamada a una API web desde Blazor de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="dda66-108">Call a web API from ASP.NET Core Blazor</span></span>

<span data-ttu-id="dda66-109">Por [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27) y [Juan De la Cruz](https://github.com/juandelacruz23)</span><span class="sxs-lookup"><span data-stu-id="dda66-109">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Juan De la Cruz](https://github.com/juandelacruz23)</span></span>

<span data-ttu-id="dda66-110">Las aplicaciones [WebAssembly de Blazor](xref:blazor/hosting-models#blazor-webassembly) llaman a las API web mediante un servicio `HttpClient` preconfigurado.</span><span class="sxs-lookup"><span data-stu-id="dda66-110">[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) apps call web APIs using a preconfigured `HttpClient` service.</span></span> <span data-ttu-id="dda66-111">Redacte las solicitudes, que pueden incluir opciones [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) de JavaScript, mediante asistentes de JSON de Blazor o con <xref:System.Net.Http.HttpRequestMessage>.</span><span class="sxs-lookup"><span data-stu-id="dda66-111">Compose requests, which can include JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) options, using Blazor JSON helpers or with <xref:System.Net.Http.HttpRequestMessage>.</span></span> <span data-ttu-id="dda66-112">El servicio `HttpClient` en las aplicaciones WebAssembly de Blazor se centra en realizar solicitudes de vuelta al servidor de origen.</span><span class="sxs-lookup"><span data-stu-id="dda66-112">The `HttpClient` service in Blazor WebAssembly apps is focused on making requests back to the server of origin.</span></span> <span data-ttu-id="dda66-113">Las instrucciones de este tema solo se aplican a las aplicaciones WebAssembly de Blazor.</span><span class="sxs-lookup"><span data-stu-id="dda66-113">The guidance in this topic only pertains to Blazor WebAssembly apps.</span></span>

<span data-ttu-id="dda66-114">Las aplicaciones de [servidor Blazor](xref:blazor/hosting-models#blazor-server) llaman a las API web mediante instancias de <xref:System.Net.Http.HttpClient>, creadas normalmente con <xref:System.Net.Http.IHttpClientFactory>.</span><span class="sxs-lookup"><span data-stu-id="dda66-114">[Blazor Server](xref:blazor/hosting-models#blazor-server) apps call web APIs using <xref:System.Net.Http.HttpClient> instances, typically created using <xref:System.Net.Http.IHttpClientFactory>.</span></span> <span data-ttu-id="dda66-115">Las instrucciones de este tema no se aplican a las aplicaciones de servidor Blazor.</span><span class="sxs-lookup"><span data-stu-id="dda66-115">The guidance in this topic doesn't pertain to Blazor Server apps.</span></span> <span data-ttu-id="dda66-116">Al desarrollar aplicaciones de servidor Blazor, siga las instrucciones de <xref:fundamentals/http-requests>.</span><span class="sxs-lookup"><span data-stu-id="dda66-116">When developing Blazor Server apps, follow the guidance in <xref:fundamentals/http-requests>.</span></span>

<span data-ttu-id="dda66-117">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([cómo descargar](xref:index#how-to-download-a-sample))&ndash; seleccione la aplicación *BlazorWebAssemblySample*.</span><span class="sxs-lookup"><span data-stu-id="dda66-117">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample)) &ndash; Select the *BlazorWebAssemblySample* app.</span></span>

<span data-ttu-id="dda66-118">Vea los componentes siguientes en la aplicación de ejemplo *BlazorWebAssemblySample*:</span><span class="sxs-lookup"><span data-stu-id="dda66-118">See the following components in the *BlazorWebAssemblySample* sample app:</span></span>

* <span data-ttu-id="dda66-119">Llamada a la API web (*Pages/CallWebAPI.razor*)</span><span class="sxs-lookup"><span data-stu-id="dda66-119">Call Web API (*Pages/CallWebAPI.razor*)</span></span>
* <span data-ttu-id="dda66-120">Evaluador de solicitudes HTTP (*Components/HTTPRequestTester.razor*)</span><span class="sxs-lookup"><span data-stu-id="dda66-120">HTTP Request Tester (*Components/HTTPRequestTester.razor*)</span></span>

## <a name="packages"></a><span data-ttu-id="dda66-121">Paquetes</span><span class="sxs-lookup"><span data-stu-id="dda66-121">Packages</span></span>

<span data-ttu-id="dda66-122">Haga referencia al paquete NuGet [System.Net.Http.Json](https://www.nuget.org/packages/System.Net.Http.Json/) en el archivo del proyecto.</span><span class="sxs-lookup"><span data-stu-id="dda66-122">Reference the [System.Net.Http.Json](https://www.nuget.org/packages/System.Net.Http.Json/) NuGet package in the project file.</span></span>

## <a name="add-the-httpclient-service"></a><span data-ttu-id="dda66-123">Agregar el servicio HttpClient</span><span class="sxs-lookup"><span data-stu-id="dda66-123">Add the HttpClient service</span></span>

<span data-ttu-id="dda66-124">En `Program.Main`, agregue un servicio `HttpClient` si aún no existe:</span><span class="sxs-lookup"><span data-stu-id="dda66-124">In `Program.Main`, add an `HttpClient` service if it doesn't already exist:</span></span>

```csharp
builder.Services.AddTransient(sp => 
    new HttpClient
    {
        BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
    });
```

## <a name="httpclient-and-json-helpers"></a><span data-ttu-id="dda66-125">HttpClient y asistentes de JSON</span><span class="sxs-lookup"><span data-stu-id="dda66-125">HttpClient and JSON helpers</span></span>

<span data-ttu-id="dda66-126">En una aplicación WebAssembly de Blazor, [HttpClient](xref:fundamentals/http-requests) está disponible como un servicio preconfigurado para realizar solicitudes de vuelta al servidor de origen.</span><span class="sxs-lookup"><span data-stu-id="dda66-126">In a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) is available as a preconfigured service for making requests back to the origin server.</span></span>

<span data-ttu-id="dda66-127">De forma predeterminada, una aplicación de servidor Blazor no incluye un servicio `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="dda66-127">A Blazor Server app doesn't include an `HttpClient` service by default.</span></span> <span data-ttu-id="dda66-128">Proporcione un servicio `HttpClient` a la aplicación mediante la [infraestructura de fábrica de HttpClient](xref:fundamentals/http-requests).</span><span class="sxs-lookup"><span data-stu-id="dda66-128">Provide an `HttpClient` to the app using the [HttpClient factory infrastructure](xref:fundamentals/http-requests).</span></span>

<span data-ttu-id="dda66-129">`HttpClient` y los asistentes de JSON también se usan para llamar a puntos de conexión de API web de terceros.</span><span class="sxs-lookup"><span data-stu-id="dda66-129">`HttpClient` and JSON helpers are also used to call third-party web API endpoints.</span></span> <span data-ttu-id="dda66-130">`HttpClient` se implementa mediante [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) del explorador y está sujeto a sus limitaciones, incluido el cumplimiento de la directiva del mismo origen.</span><span class="sxs-lookup"><span data-stu-id="dda66-130">`HttpClient` is implemented using the browser [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) and is subject to its limitations, including enforcement of the same origin policy.</span></span>

<span data-ttu-id="dda66-131">La dirección base del cliente se establece en la dirección del servidor de origen.</span><span class="sxs-lookup"><span data-stu-id="dda66-131">The client's base address is set to the originating server's address.</span></span> <span data-ttu-id="dda66-132">Inserte una instancia de `HttpClient` mediante la directiva `@inject`:</span><span class="sxs-lookup"><span data-stu-id="dda66-132">Inject an `HttpClient` instance using the `@inject` directive:</span></span>

```razor
@using System.Net.Http
@inject HttpClient Http
```

<span data-ttu-id="dda66-133">En los ejemplos siguientes, una API web Todo procesa operaciones de creación, lectura, actualización y eliminación (CRUD).</span><span class="sxs-lookup"><span data-stu-id="dda66-133">In the following examples, a Todo web API processes create, read, update, and delete (CRUD) operations.</span></span> <span data-ttu-id="dda66-134">Los ejemplos se basan en una clase `TodoItem` que almacena lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="dda66-134">The examples are based on a `TodoItem` class that stores the:</span></span>

* <span data-ttu-id="dda66-135">Id. (`Id`, `long`): identificador único del elemento.</span><span class="sxs-lookup"><span data-stu-id="dda66-135">ID (`Id`, `long`) &ndash; Unique ID of the item.</span></span>
* <span data-ttu-id="dda66-136">Nombre (`Name`, `string`): nombre del elemento.</span><span class="sxs-lookup"><span data-stu-id="dda66-136">Name (`Name`, `string`) &ndash; Name of the item.</span></span>
* <span data-ttu-id="dda66-137">Estado (`IsComplete`, `bool`): indicación de si el elemento Todo ha finalizado.</span><span class="sxs-lookup"><span data-stu-id="dda66-137">Status (`IsComplete`, `bool`) &ndash; Indication if the Todo item is finished.</span></span>

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

<span data-ttu-id="dda66-138">Los métodos auxiliares de JSON envían solicitudes a un URI (una API web en los ejemplos siguientes) y procesan la respuesta:</span><span class="sxs-lookup"><span data-stu-id="dda66-138">JSON helper methods send requests to a URI (a web API in the following examples) and process the response:</span></span>

* <span data-ttu-id="dda66-139">`GetFromJsonAsync`: envía una solicitud HTTP GET y analiza el cuerpo de respuesta JSON para crear un objeto.</span><span class="sxs-lookup"><span data-stu-id="dda66-139">`GetFromJsonAsync` &ndash; Sends an HTTP GET request and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="dda66-140">En el código siguiente, el componente muestra el elemento `todoItems`.</span><span class="sxs-lookup"><span data-stu-id="dda66-140">In the following code, the `todoItems` are displayed by the component.</span></span> <span data-ttu-id="dda66-141">El método `GetTodoItems` se desencadena cuando finaliza la representación del componente ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)).</span><span class="sxs-lookup"><span data-stu-id="dda66-141">The `GetTodoItems` method is triggered when the component is finished rendering ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)).</span></span> <span data-ttu-id="dda66-142">Vea la aplicación de ejemplo para obtener un ejemplo completo.</span><span class="sxs-lookup"><span data-stu-id="dda66-142">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] todoItems;

      protected override async Task OnInitializedAsync() => 
          todoItems = await Http.GetFromJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* <span data-ttu-id="dda66-143">`PostAsJsonAsync`: envía una solicitud HTTP POST, incluido el contenido con codificación JSON, y analiza el cuerpo de respuesta JSON para crear un objeto.</span><span class="sxs-lookup"><span data-stu-id="dda66-143">`PostAsJsonAsync` &ndash; Sends an HTTP POST request, including JSON-encoded content, and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="dda66-144">En el código siguiente, un elemento enlazado del componente proporciona `newItemName`.</span><span class="sxs-lookup"><span data-stu-id="dda66-144">In the following code, `newItemName` is provided by a bound element of the component.</span></span> <span data-ttu-id="dda66-145">El método `AddItem` se desencadena al seleccionar un elemento `<button>`.</span><span class="sxs-lookup"><span data-stu-id="dda66-145">The `AddItem` method is triggered by selecting a `<button>` element.</span></span> <span data-ttu-id="dda66-146">Vea la aplicación de ejemplo para obtener un ejemplo completo.</span><span class="sxs-lookup"><span data-stu-id="dda66-146">See the sample app for a complete example.</span></span>

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
  
  <span data-ttu-id="dda66-147">Las llamadas a `PostAsJsonAsync` devuelven <xref:System.Net.Http.HttpResponseMessage>.</span><span class="sxs-lookup"><span data-stu-id="dda66-147">Calls to `PostAsJsonAsync` return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="dda66-148">Para deserializar el contenido JSON del mensaje de respuesta, use el método de extensión `ReadFromJsonAsync<T>`:</span><span class="sxs-lookup"><span data-stu-id="dda66-148">To deserialize the JSON content from the response message, use the `ReadFromJsonAsync<T>` extension method:</span></span>
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

* <span data-ttu-id="dda66-149">`PutAsJsonAsync`: envía una solicitud HTTP PUT, incluido el contenido con codificación JSON.</span><span class="sxs-lookup"><span data-stu-id="dda66-149">`PutAsJsonAsync` &ndash; Sends an HTTP PUT request, including JSON-encoded content.</span></span>

  <span data-ttu-id="dda66-150">En el código siguiente, los elementos enlazados del componente proporcionan valores `editItem` para `Name` e `IsCompleted`.</span><span class="sxs-lookup"><span data-stu-id="dda66-150">In the following code, `editItem` values for `Name` and `IsCompleted` are provided by bound elements of the component.</span></span> <span data-ttu-id="dda66-151">El valor `Id` del elemento se establece cuando el elemento está seleccionado en otra parte de la interfaz de usuario y se llama a `EditItem`.</span><span class="sxs-lookup"><span data-stu-id="dda66-151">The item's `Id` is set when the item is selected in another part of the UI and `EditItem` is called.</span></span> <span data-ttu-id="dda66-152">El método `SaveItem` se desencadena al seleccionar un objeto `<button>` Guardar.</span><span class="sxs-lookup"><span data-stu-id="dda66-152">The `SaveItem` method is triggered by selecting the Save `<button>` element.</span></span> <span data-ttu-id="dda66-153">Vea la aplicación de ejemplo para obtener un ejemplo completo.</span><span class="sxs-lookup"><span data-stu-id="dda66-153">See the sample app for a complete example.</span></span>

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
          var editItem = todoItems.Single(i => i.Id == id);
          editItem = new TodoItem { Id = editItem.Id, Name = editItem.Name, 
              IsComplete = editItem.IsComplete };
      }

      private async Task SaveItem() =>
          await Http.PutAsJsonAsync($"api/TodoItems/{editItem.Id}, editItem);
  }
  ```
  
  <span data-ttu-id="dda66-154">Las llamadas a `PutAsJsonAsync` devuelven <xref:System.Net.Http.HttpResponseMessage>.</span><span class="sxs-lookup"><span data-stu-id="dda66-154">Calls to `PutAsJsonAsync` return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="dda66-155">Para deserializar el contenido JSON del mensaje de respuesta, use el método de extensión `ReadFromJsonAsync<T>`:</span><span class="sxs-lookup"><span data-stu-id="dda66-155">To deserialize the JSON content from the response message, use the `ReadFromJsonAsync<T>` extension method:</span></span>
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

<span data-ttu-id="dda66-156"><xref:System.Net.Http> incluye métodos de extensión adicionales para enviar solicitudes HTTP y recibir respuestas HTTP.</span><span class="sxs-lookup"><span data-stu-id="dda66-156"><xref:System.Net.Http> includes additional extension methods for sending HTTP requests and receiving HTTP responses.</span></span> <span data-ttu-id="dda66-157">[HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) se usa para enviar una solicitud HTTP DELETE a una API web.</span><span class="sxs-lookup"><span data-stu-id="dda66-157">[HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) is used to send an HTTP DELETE request to a web API.</span></span>

<span data-ttu-id="dda66-158">En el código siguiente, el elemento `<button>` Delete llama al método `DeleteItem`.</span><span class="sxs-lookup"><span data-stu-id="dda66-158">In the following code, the Delete `<button>` element calls the `DeleteItem` method.</span></span> <span data-ttu-id="dda66-159">El elemento `<input>` enlazado proporciona el valor `id` del elemento que se va a eliminar.</span><span class="sxs-lookup"><span data-stu-id="dda66-159">The bound `<input>` element supplies the `id` of the item to delete.</span></span> <span data-ttu-id="dda66-160">Vea la aplicación de ejemplo para obtener un ejemplo completo.</span><span class="sxs-lookup"><span data-stu-id="dda66-160">See the sample app for a complete example.</span></span>

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

## <a name="named-httpclient-with-ihttpclientfactory"></a><span data-ttu-id="dda66-161">HttpClient con nombre con IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="dda66-161">Named HttpClient with IHttpClientFactory</span></span>

<span data-ttu-id="dda66-162">Se admiten servicios <xref:System.Net.Http.IHttpClientFactory> y la configuración de una instancia de <xref:System.Net.Http.HttpClient> con nombre.</span><span class="sxs-lookup"><span data-stu-id="dda66-162"><xref:System.Net.Http.IHttpClientFactory> services and the configuration of a named <xref:System.Net.Http.HttpClient> are supported.</span></span>

<span data-ttu-id="dda66-163">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="dda66-163">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="dda66-164">Componente `FetchData`(*Pages/FetchData.razor*):</span><span class="sxs-lookup"><span data-stu-id="dda66-164">`FetchData` component (*Pages/FetchData.razor*):</span></span>

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

## <a name="typed-httpclient"></a><span data-ttu-id="dda66-165">HttpClient con tipo</span><span class="sxs-lookup"><span data-stu-id="dda66-165">Typed HttpClient</span></span>

<span data-ttu-id="dda66-166">La instancia de <xref:System.Net.Http.HttpClient> con tipo usa una o varias instancias de <xref:System.Net.Http.HttpClient> de la aplicación, ya sean predeterminadas o con nombre, para devolver datos de uno o varios puntos de conexión de la API web.</span><span class="sxs-lookup"><span data-stu-id="dda66-166">Typed <xref:System.Net.Http.HttpClient> uses one or more of the app's <xref:System.Net.Http.HttpClient> instances, default or named, to return data from one or more web API endpoints.</span></span>

<span data-ttu-id="dda66-167">*WeatherForecastClient.cs*:</span><span class="sxs-lookup"><span data-stu-id="dda66-167">*WeatherForecastClient.cs*:</span></span>

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

<span data-ttu-id="dda66-168">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="dda66-168">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="dda66-169">Los componentes insertan la instancia de `HttpClient` con tipo para llamar a la API web.</span><span class="sxs-lookup"><span data-stu-id="dda66-169">Components inject the typed `HttpClient` to call the web API.</span></span>

<span data-ttu-id="dda66-170">Componente `FetchData`(*Pages/FetchData.razor*):</span><span class="sxs-lookup"><span data-stu-id="dda66-170">`FetchData` component (*Pages/FetchData.razor*):</span></span>

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

## <a name="handle-errors"></a><span data-ttu-id="dda66-171">Control de errores</span><span class="sxs-lookup"><span data-stu-id="dda66-171">Handle errors</span></span>

<span data-ttu-id="dda66-172">Cuando se producen errores al interactuar con una API web, pueden controlarse mediante código de desarrollador.</span><span class="sxs-lookup"><span data-stu-id="dda66-172">When errors occur while interacting with a web API, they can be handled by developer code.</span></span> <span data-ttu-id="dda66-173">Por ejemplo, `GetFromJsonAsync` espera una respuesta JSON de la API del servidor con un `Content-Type` de `application/json`.</span><span class="sxs-lookup"><span data-stu-id="dda66-173">For example, `GetFromJsonAsync` expects a JSON response from the server API with a `Content-Type` of `application/json`.</span></span> <span data-ttu-id="dda66-174">Si la respuesta no está en formato JSON, la validación del contenido producirá una excepción <xref:System.NotSupportedException>.</span><span class="sxs-lookup"><span data-stu-id="dda66-174">If the response isn't in JSON format, content validation throws a <xref:System.NotSupportedException>.</span></span>

<span data-ttu-id="dda66-175">En el ejemplo siguiente, el punto de conexión del URI para la solicitud de datos de previsión meteorológica está mal escrito.</span><span class="sxs-lookup"><span data-stu-id="dda66-175">In the following example, the URI endpoint for the weather forecast data request is misspelled.</span></span> <span data-ttu-id="dda66-176">El URI debería ser `WeatherForecast`, pero en la llamada aparece como `WeatherForcast` (falta una "e").</span><span class="sxs-lookup"><span data-stu-id="dda66-176">The URI should be to `WeatherForecast` but appears in the call as `WeatherForcast` (missing "e").</span></span>

<span data-ttu-id="dda66-177">La llamada a `GetFromJsonAsync` espera que se devuelva JSON, pero el servidor devuelve HTML para una excepción no controlada en el servidor con un `Content-Type` de `text/html`.</span><span class="sxs-lookup"><span data-stu-id="dda66-177">The `GetFromJsonAsync` call expects JSON to be returned, but the server returns HTML for an unhandled exception on the server with a `Content-Type` of `text/html`.</span></span> <span data-ttu-id="dda66-178">La excepción no controlada se produce en el servidor, puesto que no se encuentra la ruta de acceso y el middleware no puede proporcionar una página o vista para la solicitud.</span><span class="sxs-lookup"><span data-stu-id="dda66-178">The unhandled exception occurs on the server because the path isn't found and middleware can't serve a page or view for the request.</span></span>

<span data-ttu-id="dda66-179">En `OnInitializedAsync` en el cliente, se produce una excepción <xref:System.NotSupportedException> cuando el contenido de la respuesta se valida como distinto de JSON.</span><span class="sxs-lookup"><span data-stu-id="dda66-179">In `OnInitializedAsync` on the client, <xref:System.NotSupportedException> is thrown when the response content is validated as non-JSON.</span></span> <span data-ttu-id="dda66-180">La excepción se captura en el bloque `catch`, donde la lógica personalizada podría registrar el error o mostrar al usuario un mensaje de error descriptivo:</span><span class="sxs-lookup"><span data-stu-id="dda66-180">The exception is caught in the `catch` block, where custom logic could log the error or present a friendly error message to the user:</span></span>

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
> <span data-ttu-id="dda66-181">El ejemplo anterior sirve de demostración.</span><span class="sxs-lookup"><span data-stu-id="dda66-181">The preceding example is for demonstration purposes.</span></span> <span data-ttu-id="dda66-182">Se puede configurar una aplicación de servidor de API web para que devuelva JSON incluso cuando no existe un punto de conexión o cuando se produce una excepción no controlada en el servidor.</span><span class="sxs-lookup"><span data-stu-id="dda66-182">A web API server app can be configured to return JSON even when an endpoint doesn't exist or an unhandled excpetion on the server occurs.</span></span>

<span data-ttu-id="dda66-183">Para obtener más información, vea <xref:blazor/handle-errors>.</span><span class="sxs-lookup"><span data-stu-id="dda66-183">For more information, see <xref:blazor/handle-errors>.</span></span>

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="dda66-184">Uso compartido de recursos entre orígenes (CORS)</span><span class="sxs-lookup"><span data-stu-id="dda66-184">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="dda66-185">La seguridad del explorador evita que una página web realice solicitudes a un dominio diferente del que ha proporcionado esa página web.</span><span class="sxs-lookup"><span data-stu-id="dda66-185">Browser security prevents a webpage from making requests to a different domain than the one that served the webpage.</span></span> <span data-ttu-id="dda66-186">Esta restricción se denomina *directiva de mismo origen*.</span><span class="sxs-lookup"><span data-stu-id="dda66-186">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="dda66-187">La directiva de mismo origen evita que un sitio malintencionado lea información confidencial de otro sitio.</span><span class="sxs-lookup"><span data-stu-id="dda66-187">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="dda66-188">Para realizar solicitudes desde el explorador a un punto de conexión con un origen diferente, el *punto de conexión* debe habilitar el [uso compartido de recursos entre orígenes (CORS)](https://www.w3.org/TR/cors/).</span><span class="sxs-lookup"><span data-stu-id="dda66-188">To make requests from the browser to an endpoint with a different origin, the *endpoint* must enable [cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/).</span></span>

<span data-ttu-id="dda66-189">En la [aplicación de ejemplo WebAssembly de Blazor (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) se muestra el uso de CORS en el componente Call Web API (*Pages/CallWebAPI.razor*).</span><span class="sxs-lookup"><span data-stu-id="dda66-189">The [Blazor WebAssembly sample app (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) demonstrates the use of CORS in the Call Web API component (*Pages/CallWebAPI.razor*).</span></span>

<span data-ttu-id="dda66-190">Para permitir que otros sitios realicen solicitudes de uso compartido de recursos entre orígenes (CORS) a la aplicación, vea <xref:security/cors>.</span><span class="sxs-lookup"><span data-stu-id="dda66-190">To allow other sites to make cross-origin resource sharing (CORS) requests to your app, see <xref:security/cors>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="dda66-191">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="dda66-191">Additional resources</span></span>

* <span data-ttu-id="dda66-192"><xref:security/blazor/webassembly/additional-scenarios>: incluye cobertura sobre el uso de `HttpClient` para hacer solicitudes seguras de API web.</span><span class="sxs-lookup"><span data-stu-id="dda66-192"><xref:security/blazor/webassembly/additional-scenarios> &ndash; Includes coverage on using `HttpClient` to make secure web API requests.</span></span>
* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [<span data-ttu-id="dda66-193">Configuración de puntos de conexión HTTPS de Kestrel</span><span class="sxs-lookup"><span data-stu-id="dda66-193">Kestrel HTTPS endpoint configuration</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [<span data-ttu-id="dda66-194">Uso compartido de recursos entre orígenes (CORS) en W3C</span><span class="sxs-lookup"><span data-stu-id="dda66-194">Cross Origin Resource Sharing (CORS) at W3C</span></span>](https://www.w3.org/TR/cors/)
