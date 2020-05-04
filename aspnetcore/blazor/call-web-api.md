---
title: Llamada a una API web desde WebAssembly de Blazor de ASP.NET Core
author: guardrex
description: Obtenga información sobre cómo llamar a una API web desde una aplicación de WebAssembly de Blazor mediante asistentes de JSON, incluida la realización de solicitudes de uso compartido de recursos entre orígenes (CORS).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: blazor/call-web-api
ms.openlocfilehash: abc546cc0079a01e3999b2c7c083235d3fff9b06
ms.sourcegitcommit: e94ecfae6a3ef568fa197da791c8bc595917d17a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/24/2020
ms.locfileid: "82122216"
---
# <a name="call-a-web-api-from-aspnet-core-blazor"></a><span data-ttu-id="781cf-103">Llamada a una API web desde Blazor de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="781cf-103">Call a web API from ASP.NET Core Blazor</span></span>

<span data-ttu-id="781cf-104">Por [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27) y [Juan De la Cruz](https://github.com/juandelacruz23)</span><span class="sxs-lookup"><span data-stu-id="781cf-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Juan De la Cruz](https://github.com/juandelacruz23)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="781cf-105">Las aplicaciones [WebAssembly de Blazor](xref:blazor/hosting-models#blazor-webassembly) llaman a las API web a través de un servicio `HttpClient` preconfigurado.</span><span class="sxs-lookup"><span data-stu-id="781cf-105">[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) apps call web APIs using a preconfigured `HttpClient` service.</span></span> <span data-ttu-id="781cf-106">Redacte las solicitudes, que pueden incluir opciones [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) de JavaScript, usando los asistentes de JSON de Blazor o <xref:System.Net.Http.HttpRequestMessage>.</span><span class="sxs-lookup"><span data-stu-id="781cf-106">Compose requests, which can include JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) options, using Blazor JSON helpers or with <xref:System.Net.Http.HttpRequestMessage>.</span></span> <span data-ttu-id="781cf-107">El servicio `HttpClient` en las aplicaciones WebAssembly de Blazor se centra en realizar solicitudes de vuelta al servidor de origen.</span><span class="sxs-lookup"><span data-stu-id="781cf-107">The `HttpClient` service in Blazor WebAssembly apps is focused on making requests back to the server of origin.</span></span> <span data-ttu-id="781cf-108">Las instrucciones de este tema solo son válidas con las aplicaciones WebAssembly de Blazor.</span><span class="sxs-lookup"><span data-stu-id="781cf-108">The guidance in this topic only pertains to Blazor WebAssembly apps.</span></span>

<span data-ttu-id="781cf-109">Las aplicaciones [Blazor Server](xref:blazor/hosting-models#blazor-server) llaman a las API web usando instancias de <xref:System.Net.Http.HttpClient>, creadas normalmente con <xref:System.Net.Http.IHttpClientFactory>.</span><span class="sxs-lookup"><span data-stu-id="781cf-109">[Blazor Server](xref:blazor/hosting-models#blazor-server) apps call web APIs using <xref:System.Net.Http.HttpClient> instances, typically created using <xref:System.Net.Http.IHttpClientFactory>.</span></span> <span data-ttu-id="781cf-110">Las instrucciones de este tema no son válidas en aplicaciones Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="781cf-110">The guidance in this topic doesn't pertain to Blazor Server apps.</span></span> <span data-ttu-id="781cf-111">Al desarrollar aplicaciones Blazor Server, siga las instrucciones de <xref:fundamentals/http-requests>.</span><span class="sxs-lookup"><span data-stu-id="781cf-111">When developing Blazor Server apps, follow the guidance in <xref:fundamentals/http-requests>.</span></span>

<span data-ttu-id="781cf-112">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([cómo descargar](xref:index#how-to-download-a-sample))&ndash; seleccione la aplicación *BlazorWebAssemblySample*.</span><span class="sxs-lookup"><span data-stu-id="781cf-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample)) &ndash; Select the *BlazorWebAssemblySample* app.</span></span>

<span data-ttu-id="781cf-113">Vea los componentes siguientes en la aplicación de ejemplo *BlazorWebAssemblySample*:</span><span class="sxs-lookup"><span data-stu-id="781cf-113">See the following components in the *BlazorWebAssemblySample* sample app:</span></span>

* <span data-ttu-id="781cf-114">Llamada a la API web (*Pages/CallWebAPI.razor*)</span><span class="sxs-lookup"><span data-stu-id="781cf-114">Call Web API (*Pages/CallWebAPI.razor*)</span></span>
* <span data-ttu-id="781cf-115">Evaluador de solicitudes HTTP (*Components/HTTPRequestTester.razor*)</span><span class="sxs-lookup"><span data-stu-id="781cf-115">HTTP Request Tester (*Components/HTTPRequestTester.razor*)</span></span>

## <a name="packages"></a><span data-ttu-id="781cf-116">Paquetes</span><span class="sxs-lookup"><span data-stu-id="781cf-116">Packages</span></span>

<span data-ttu-id="781cf-117">Haga referencia al paquete NuGet [System.Net.Http.Json](https://www.nuget.org/packages/System.Net.Http.Json/) en el archivo del proyecto.</span><span class="sxs-lookup"><span data-stu-id="781cf-117">Reference the [System.Net.Http.Json](https://www.nuget.org/packages/System.Net.Http.Json/) NuGet package in the project file.</span></span>

## <a name="add-the-httpclient-service"></a><span data-ttu-id="781cf-118">Agregar el servicio HttpClient</span><span class="sxs-lookup"><span data-stu-id="781cf-118">Add the HttpClient service</span></span>

<span data-ttu-id="781cf-119">En `Program.Main`, agregue un servicio `HttpClient` si aún no existe:</span><span class="sxs-lookup"><span data-stu-id="781cf-119">In `Program.Main`, add an `HttpClient` service if it doesn't already exist:</span></span>

```csharp
builder.Services.AddSingleton(
    new HttpClient
    {
        BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
    });
```

## <a name="httpclient-and-json-helpers"></a><span data-ttu-id="781cf-120">HttpClient y asistentes de JSON</span><span class="sxs-lookup"><span data-stu-id="781cf-120">HttpClient and JSON helpers</span></span>

<span data-ttu-id="781cf-121">En una aplicación WebAssembly de Blazor, [HttpClient](xref:fundamentals/http-requests) está disponible como un servicio preconfigurado para realizar solicitudes de vuelta al servidor de origen.</span><span class="sxs-lookup"><span data-stu-id="781cf-121">In a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) is available as a preconfigured service for making requests back to the origin server.</span></span>

<span data-ttu-id="781cf-122">Una aplicación Blazor Server no incluye un servicio `HttpClient` de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="781cf-122">A Blazor Server app doesn't include an `HttpClient` service by default.</span></span> <span data-ttu-id="781cf-123">Proporcione un servicio `HttpClient` a la aplicación mediante la [infraestructura de fábrica de HttpClient](xref:fundamentals/http-requests).</span><span class="sxs-lookup"><span data-stu-id="781cf-123">Provide an `HttpClient` to the app using the [HttpClient factory infrastructure](xref:fundamentals/http-requests).</span></span>

<span data-ttu-id="781cf-124">`HttpClient` y los asistentes de JSON también se usan para llamar a puntos de conexión de API web de terceros.</span><span class="sxs-lookup"><span data-stu-id="781cf-124">`HttpClient` and JSON helpers are also used to call third-party web API endpoints.</span></span> <span data-ttu-id="781cf-125">`HttpClient` se implementa mediante [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) del explorador y está sujeto a sus limitaciones, incluido el cumplimiento de la directiva del mismo origen.</span><span class="sxs-lookup"><span data-stu-id="781cf-125">`HttpClient` is implemented using the browser [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) and is subject to its limitations, including enforcement of the same origin policy.</span></span>

<span data-ttu-id="781cf-126">La dirección base del cliente se establece en la dirección del servidor de origen.</span><span class="sxs-lookup"><span data-stu-id="781cf-126">The client's base address is set to the originating server's address.</span></span> <span data-ttu-id="781cf-127">Inserte una instancia de `HttpClient` mediante la directiva `@inject`:</span><span class="sxs-lookup"><span data-stu-id="781cf-127">Inject an `HttpClient` instance using the `@inject` directive:</span></span>

```razor
@using System.Net.Http
@inject HttpClient Http
```

<span data-ttu-id="781cf-128">En los ejemplos siguientes, una API web Todo procesa operaciones de creación, lectura, actualización y eliminación (CRUD).</span><span class="sxs-lookup"><span data-stu-id="781cf-128">In the following examples, a Todo web API processes create, read, update, and delete (CRUD) operations.</span></span> <span data-ttu-id="781cf-129">Los ejemplos se basan en una clase `TodoItem` que almacena lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="781cf-129">The examples are based on a `TodoItem` class that stores the:</span></span>

* <span data-ttu-id="781cf-130">Id. (`Id`, `long`): identificador único del elemento.</span><span class="sxs-lookup"><span data-stu-id="781cf-130">ID (`Id`, `long`) &ndash; Unique ID of the item.</span></span>
* <span data-ttu-id="781cf-131">Nombre (`Name`, `string`): nombre del elemento.</span><span class="sxs-lookup"><span data-stu-id="781cf-131">Name (`Name`, `string`) &ndash; Name of the item.</span></span>
* <span data-ttu-id="781cf-132">Estado (`IsComplete`, `bool`): indicación de si el elemento Todo ha finalizado.</span><span class="sxs-lookup"><span data-stu-id="781cf-132">Status (`IsComplete`, `bool`) &ndash; Indication if the Todo item is finished.</span></span>

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

<span data-ttu-id="781cf-133">Los métodos auxiliares de JSON envían solicitudes a un URI (una API web en los ejemplos siguientes) y procesan la respuesta:</span><span class="sxs-lookup"><span data-stu-id="781cf-133">JSON helper methods send requests to a URI (a web API in the following examples) and process the response:</span></span>

* <span data-ttu-id="781cf-134">`GetFromJsonAsync`: envía una solicitud HTTP GET y analiza el cuerpo de respuesta JSON para crear un objeto.</span><span class="sxs-lookup"><span data-stu-id="781cf-134">`GetFromJsonAsync` &ndash; Sends an HTTP GET request and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="781cf-135">En el código siguiente, el componente muestra el elemento `_todoItems`.</span><span class="sxs-lookup"><span data-stu-id="781cf-135">In the following code, the `_todoItems` are displayed by the component.</span></span> <span data-ttu-id="781cf-136">El método `GetTodoItems` se desencadena cuando finaliza la representación del componente ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)).</span><span class="sxs-lookup"><span data-stu-id="781cf-136">The `GetTodoItems` method is triggered when the component is finished rendering ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)).</span></span> <span data-ttu-id="781cf-137">Vea la aplicación de ejemplo para obtener un ejemplo completo.</span><span class="sxs-lookup"><span data-stu-id="781cf-137">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] _todoItems;

      protected override async Task OnInitializedAsync() => 
          _todoItems = await Http.GetFromJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* <span data-ttu-id="781cf-138">`PostAsJsonAsync`: envía una solicitud HTTP POST, incluido el contenido con codificación JSON, y analiza el cuerpo de respuesta JSON para crear un objeto.</span><span class="sxs-lookup"><span data-stu-id="781cf-138">`PostAsJsonAsync` &ndash; Sends an HTTP POST request, including JSON-encoded content, and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="781cf-139">En el código siguiente, un elemento enlazado del componente proporciona `_newItemName`.</span><span class="sxs-lookup"><span data-stu-id="781cf-139">In the following code, `_newItemName` is provided by a bound element of the component.</span></span> <span data-ttu-id="781cf-140">El método `AddItem` se desencadena al seleccionar un elemento `<button>`.</span><span class="sxs-lookup"><span data-stu-id="781cf-140">The `AddItem` method is triggered by selecting a `<button>` element.</span></span> <span data-ttu-id="781cf-141">Vea la aplicación de ejemplo para obtener un ejemplo completo.</span><span class="sxs-lookup"><span data-stu-id="781cf-141">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  <input @bind="_newItemName" placeholder="New Todo Item" />
  <button @onclick="@AddItem">Add</button>

  @code {
      private string _newItemName;

      private async Task AddItem()
      {
          var addItem = new TodoItem { Name = _newItemName, IsComplete = false };
          await Http.PostAsJsonAsync("api/TodoItems", addItem);
      }
  }
  ```
  
  <span data-ttu-id="781cf-142">Las llamadas a `PostAsJsonAsync` devuelven <xref:System.Net.Http.HttpResponseMessage>.</span><span class="sxs-lookup"><span data-stu-id="781cf-142">Calls to `PostAsJsonAsync` return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="781cf-143">Para deserializar el contenido JSON del mensaje de respuesta, use el método de extensión `ReadFromJsonAsync<T>`:</span><span class="sxs-lookup"><span data-stu-id="781cf-143">To deserialize the JSON content from the response message, use the `ReadFromJsonAsync<T>` extension method:</span></span>
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

* <span data-ttu-id="781cf-144">`PutAsJsonAsync`: envía una solicitud HTTP PUT, incluido el contenido con codificación JSON.</span><span class="sxs-lookup"><span data-stu-id="781cf-144">`PutAsJsonAsync` &ndash; Sends an HTTP PUT request, including JSON-encoded content.</span></span>

  <span data-ttu-id="781cf-145">En el código siguiente, los elementos enlazados del componente proporcionan valores `_editItem` para `Name` e `IsCompleted`.</span><span class="sxs-lookup"><span data-stu-id="781cf-145">In the following code, `_editItem` values for `Name` and `IsCompleted` are provided by bound elements of the component.</span></span> <span data-ttu-id="781cf-146">El valor `Id` del elemento se establece cuando el elemento está seleccionado en otra parte de la interfaz de usuario y se llama a `EditItem`.</span><span class="sxs-lookup"><span data-stu-id="781cf-146">The item's `Id` is set when the item is selected in another part of the UI and `EditItem` is called.</span></span> <span data-ttu-id="781cf-147">El método `SaveItem` se desencadena al seleccionar un objeto `<button>` Guardar.</span><span class="sxs-lookup"><span data-stu-id="781cf-147">The `SaveItem` method is triggered by selecting the Save `<button>` element.</span></span> <span data-ttu-id="781cf-148">Vea la aplicación de ejemplo para obtener un ejemplo completo.</span><span class="sxs-lookup"><span data-stu-id="781cf-148">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  <input type="checkbox" @bind="_editItem.IsComplete" />
  <input @bind="_editItem.Name" />
  <button @onclick="@SaveItem">Save</button>

  @code {
      private TodoItem _editItem = new TodoItem();

      private void EditItem(long id)
      {
          var editItem = _todoItems.Single(i => i.Id == id);
          _editItem = new TodoItem { Id = editItem.Id, Name = editItem.Name, 
              IsComplete = editItem.IsComplete };
      }

      private async Task SaveItem() =>
          await Http.PutAsJsonAsync($"api/TodoItems/{_editItem.Id}, _editItem);
  }
  ```
  
  <span data-ttu-id="781cf-149">Las llamadas a `PutAsJsonAsync` devuelven <xref:System.Net.Http.HttpResponseMessage>.</span><span class="sxs-lookup"><span data-stu-id="781cf-149">Calls to `PutAsJsonAsync` return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="781cf-150">Para deserializar el contenido JSON del mensaje de respuesta, use el método de extensión `ReadFromJsonAsync<T>`:</span><span class="sxs-lookup"><span data-stu-id="781cf-150">To deserialize the JSON content from the response message, use the `ReadFromJsonAsync<T>` extension method:</span></span>
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

<span data-ttu-id="781cf-151"><xref:System.Net.Http> incluye métodos de extensión adicionales para enviar solicitudes HTTP y recibir respuestas HTTP.</span><span class="sxs-lookup"><span data-stu-id="781cf-151"><xref:System.Net.Http> includes additional extension methods for sending HTTP requests and receiving HTTP responses.</span></span> <span data-ttu-id="781cf-152">[HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) se usa para enviar una solicitud HTTP DELETE a una API web.</span><span class="sxs-lookup"><span data-stu-id="781cf-152">[HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) is used to send an HTTP DELETE request to a web API.</span></span>

<span data-ttu-id="781cf-153">En el código siguiente, el elemento `<button>` Delete llama al método `DeleteItem`.</span><span class="sxs-lookup"><span data-stu-id="781cf-153">In the following code, the Delete `<button>` element calls the `DeleteItem` method.</span></span> <span data-ttu-id="781cf-154">El elemento `<input>` enlazado proporciona el valor `id` del elemento que se va a eliminar.</span><span class="sxs-lookup"><span data-stu-id="781cf-154">The bound `<input>` element supplies the `id` of the item to delete.</span></span> <span data-ttu-id="781cf-155">Vea la aplicación de ejemplo para obtener un ejemplo completo.</span><span class="sxs-lookup"><span data-stu-id="781cf-155">See the sample app for a complete example.</span></span>

```razor
@using System.Net.Http
@inject HttpClient Http

<input @bind="_id" />
<button @onclick="@DeleteItem">Delete</button>

@code {
    private long _id;

    private async Task DeleteItem() =>
        await Http.DeleteAsync($"api/TodoItems/{_id}");
}
```

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="781cf-156">Uso compartido de recursos entre orígenes (CORS)</span><span class="sxs-lookup"><span data-stu-id="781cf-156">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="781cf-157">La seguridad del explorador evita que una página web realice solicitudes a un dominio diferente del que ha proporcionado esa página web.</span><span class="sxs-lookup"><span data-stu-id="781cf-157">Browser security prevents a webpage from making requests to a different domain than the one that served the webpage.</span></span> <span data-ttu-id="781cf-158">Esta restricción se denomina *directiva de mismo origen*.</span><span class="sxs-lookup"><span data-stu-id="781cf-158">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="781cf-159">La directiva de mismo origen evita que un sitio malintencionado lea información confidencial de otro sitio.</span><span class="sxs-lookup"><span data-stu-id="781cf-159">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="781cf-160">Para realizar solicitudes desde el explorador a un punto de conexión con un origen diferente, el *punto de conexión* debe habilitar el [uso compartido de recursos entre orígenes (CORS)](https://www.w3.org/TR/cors/).</span><span class="sxs-lookup"><span data-stu-id="781cf-160">To make requests from the browser to an endpoint with a different origin, the *endpoint* must enable [cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/).</span></span>

<span data-ttu-id="781cf-161">En la [aplicación de ejemplo de WebAssembly de Blazor (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) se muestra el uso de CORS en el componente Call Web API (*Pages/CallWebAPI.razor*).</span><span class="sxs-lookup"><span data-stu-id="781cf-161">The [Blazor WebAssembly sample app (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) demonstrates the use of CORS in the Call Web API component (*Pages/CallWebAPI.razor*).</span></span>

<span data-ttu-id="781cf-162">Para permitir que otros sitios realicen solicitudes de uso compartido de recursos entre orígenes (CORS) a la aplicación, vea <xref:security/cors>.</span><span class="sxs-lookup"><span data-stu-id="781cf-162">To allow other sites to make cross-origin resource sharing (CORS) requests to your app, see <xref:security/cors>.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="781cf-163">HttpClient y HttpRequestMessage con las opciones de solicitud de Fetch API</span><span class="sxs-lookup"><span data-stu-id="781cf-163">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="781cf-164">Al ejecutar WebAssembly en una aplicación WebAssembly de Blazor, use [HttpClient](xref:fundamentals/http-requests) y <xref:System.Net.Http.HttpRequestMessage> para personalizar las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="781cf-164">When running on WebAssembly in a Blazor WebAssembly app, use [HttpClient](xref:fundamentals/http-requests) and <xref:System.Net.Http.HttpRequestMessage> to customize requests.</span></span> <span data-ttu-id="781cf-165">Por ejemplo, puede especificar el URI de la solicitud, el método HTTP y cualquier encabezado de solicitud deseado.</span><span class="sxs-lookup"><span data-stu-id="781cf-165">For example, you can specify the request URI, HTTP method, and any desired request headers.</span></span>

```razor
@using System.Net.Http
@using System.Net.Http.Headers
@using System.Net.Http.Json
@inject HttpClient Http

@code {
    private async Task PostRequest()
    {
        var requestMessage = new HttpRequestMessage()
        {
            Method = new HttpMethod("POST"),
            RequestUri = new Uri("https://localhost:10000/api/TodoItems"),
            Content = 
                JsonContent.Create(new TodoItem
                { 
                    Name: "A New Todo Item",
                    IsComplete: false
                })
        };
        
        requestMessage.Headers.Authorization = 
           new AuthenticationHeaderValue("Bearer", "{OAUTH TOKEN}");

        requestMessage.Content.Headers.TryAddWithoutValidation(
            "x-custom-header", "value");

        var response = await Http.SendAsync(requestMessage);
        var responseStatusCode = response.StatusCode;
        var responseBody = await response.Content.ReadAsStringAsync();
    }
}
```

<span data-ttu-id="781cf-166">En la implementación de `HttpClient` de WebAssembly de .NET se usa [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span><span class="sxs-lookup"><span data-stu-id="781cf-166">.NET WebAssembly's implementation of `HttpClient` uses [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span></span> <span data-ttu-id="781cf-167">Fetch permite configurar varias [opciones específicas de la solicitud](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="781cf-167">Fetch allows configuring several [request-specific options](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span> 

<span data-ttu-id="781cf-168">Se pueden configurar opciones de solicitud de captura HTTP con los métodos de extensión `HttpRequestMessage` que se muestran en la siguiente tabla.</span><span class="sxs-lookup"><span data-stu-id="781cf-168">HTTP fetch request options can be configured with `HttpRequestMessage` extension methods shown in the following table.</span></span>

| <span data-ttu-id="781cf-169">Método de extensión `HttpRequestMessage`</span><span class="sxs-lookup"><span data-stu-id="781cf-169">`HttpRequestMessage` extension method</span></span> | <span data-ttu-id="781cf-170">Propiedad de solicitud de captura</span><span class="sxs-lookup"><span data-stu-id="781cf-170">Fetch request property</span></span> |
| ------------------------------------- | ---------------------- |
| `SetBrowserRequestCredentials`        | [<span data-ttu-id="781cf-171">credentials</span><span class="sxs-lookup"><span data-stu-id="781cf-171">credentials</span></span>](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| `SetBrowserRequestCache`              | [<span data-ttu-id="781cf-172">cache</span><span class="sxs-lookup"><span data-stu-id="781cf-172">cache</span></span>](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| `SetBrowserRequestMode`               | [<span data-ttu-id="781cf-173">mode</span><span class="sxs-lookup"><span data-stu-id="781cf-173">mode</span></span>](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| `SetBrowserRequestIntegrity`          | [<span data-ttu-id="781cf-174">integrity</span><span class="sxs-lookup"><span data-stu-id="781cf-174">integrity</span></span>](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

<span data-ttu-id="781cf-175">Se pueden establecer más opciones usando el método de extensión `SetBrowserRequestOption`, que es más genérico.</span><span class="sxs-lookup"><span data-stu-id="781cf-175">You can set additional options using the more generic `SetBrowserRequestOption` extension method.</span></span>
 
<span data-ttu-id="781cf-176">Normalmente, la respuesta HTTP se almacena en búfer en una aplicación WebAssembly de Blazor para habilitar la compatibilidad con las lecturas de sincronización en el contenido de la respuesta.</span><span class="sxs-lookup"><span data-stu-id="781cf-176">The HTTP response is typically buffered in a Blazor WebAssembly app to enable support for sync reads on the response content.</span></span> <span data-ttu-id="781cf-177">Para habilitar la compatibilidad con la transmisión de respuestas, use el método de extensión `SetBrowserResponseStreamingEnabled` en la solicitud.</span><span class="sxs-lookup"><span data-stu-id="781cf-177">To enable support for response streaming, use the `SetBrowserResponseStreamingEnabled` extension method on the request.</span></span>

<span data-ttu-id="781cf-178">Para incluir credenciales en una solicitud entre orígenes, use el método de extensión `SetBrowserRequestCredentials`:</span><span class="sxs-lookup"><span data-stu-id="781cf-178">To include credentials in a cross-origin request, use the `SetBrowserRequestCredentials` extension method:</span></span>

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

<span data-ttu-id="781cf-179">Para obtener más información sobre las opciones de Fetch API, vea [Documentación web de MDN: WindowOrWorkerGlobalScope.fetch(): parámetros](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="781cf-179">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

<span data-ttu-id="781cf-180">Al enviar credenciales (cookies de autorización o encabezados) en solicitudes de CORS, la directiva de CORS debe permitir el encabezado `Authorization`.</span><span class="sxs-lookup"><span data-stu-id="781cf-180">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="781cf-181">En la directiva siguiente se incluye la configuración de:</span><span class="sxs-lookup"><span data-stu-id="781cf-181">The following policy includes configuration for:</span></span>

* <span data-ttu-id="781cf-182">Orígenes de la solicitud (`http://localhost:5000`, `https://localhost:5001`).</span><span class="sxs-lookup"><span data-stu-id="781cf-182">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="781cf-183">Cualquier método (verbo).</span><span class="sxs-lookup"><span data-stu-id="781cf-183">Any method (verb).</span></span>
* <span data-ttu-id="781cf-184">Los encabezados `Content-Type` y `Authorization`.</span><span class="sxs-lookup"><span data-stu-id="781cf-184">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="781cf-185">Para permitir un encabezado personalizado (por ejemplo, `x-custom-header`), enumere el encabezado al llamar a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span><span class="sxs-lookup"><span data-stu-id="781cf-185">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="781cf-186">Las credenciales establecidas por el código JavaScript del lado cliente (la propiedad `credentials` establecida en `include`).</span><span class="sxs-lookup"><span data-stu-id="781cf-186">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="781cf-187">Para obtener más información, vea <xref:security/cors> y el componente del evaluador de solicitudes HTTP de la aplicación de ejemplo (*Components/HTTPRequestTester.razor*).</span><span class="sxs-lookup"><span data-stu-id="781cf-187">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (*Components/HTTPRequestTester.razor*).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="781cf-188">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="781cf-188">Additional resources</span></span>

* [<span data-ttu-id="781cf-189">Solicitar tokens de acceso adicionales</span><span class="sxs-lookup"><span data-stu-id="781cf-189">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="781cf-190">Adjuntar tokens a solicitudes salientes</span><span class="sxs-lookup"><span data-stu-id="781cf-190">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)
* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [<span data-ttu-id="781cf-191">Configuración de puntos de conexión HTTPS de Kestrel</span><span class="sxs-lookup"><span data-stu-id="781cf-191">Kestrel HTTPS endpoint configuration</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [<span data-ttu-id="781cf-192">Uso compartido de recursos entre orígenes (CORS) en W3C</span><span class="sxs-lookup"><span data-stu-id="781cf-192">Cross Origin Resource Sharing (CORS) at W3C</span></span>](https://www.w3.org/TR/cors/)
