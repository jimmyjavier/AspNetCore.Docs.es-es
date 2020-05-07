---
title: Llamada a una API web desde WebAssembly de Blazor de ASP.NET Core
author: guardrex
description: Obtenga información sobre cómo llamar a una API web desde una aplicación de WebAssembly de Blazor mediante asistentes de JSON, incluida la realización de solicitudes de uso compartido de recursos entre orígenes (CORS).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/call-web-api
ms.openlocfilehash: d823db3688e05f6befefacc9f390e0dcdbf329a7
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767153"
---
# <a name="call-a-web-api-from-aspnet-core-blazor"></a><span data-ttu-id="bda69-103">Llamada a una API web desde Blazor de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bda69-103">Call a web API from ASP.NET Core Blazor</span></span>

<span data-ttu-id="bda69-104">Por [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27) y [Juan De la Cruz](https://github.com/juandelacruz23)</span><span class="sxs-lookup"><span data-stu-id="bda69-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Juan De la Cruz](https://github.com/juandelacruz23)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="bda69-105">Las aplicaciones [WebAssembly de Blazor](xref:blazor/hosting-models#blazor-webassembly) llaman a las API web mediante un servicio `HttpClient` preconfigurado.</span><span class="sxs-lookup"><span data-stu-id="bda69-105">[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) apps call web APIs using a preconfigured `HttpClient` service.</span></span> <span data-ttu-id="bda69-106">Redacte las solicitudes, que pueden incluir opciones [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) de JavaScript, mediante asistentes de JSON de Blazor o con <xref:System.Net.Http.HttpRequestMessage>.</span><span class="sxs-lookup"><span data-stu-id="bda69-106">Compose requests, which can include JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) options, using Blazor JSON helpers or with <xref:System.Net.Http.HttpRequestMessage>.</span></span> <span data-ttu-id="bda69-107">El servicio `HttpClient` en las aplicaciones WebAssembly de Blazor se centra en realizar solicitudes de vuelta al servidor de origen.</span><span class="sxs-lookup"><span data-stu-id="bda69-107">The `HttpClient` service in Blazor WebAssembly apps is focused on making requests back to the server of origin.</span></span> <span data-ttu-id="bda69-108">Las instrucciones de este tema solo se aplican a las aplicaciones WebAssembly de Blazor.</span><span class="sxs-lookup"><span data-stu-id="bda69-108">The guidance in this topic only pertains to Blazor WebAssembly apps.</span></span>

<span data-ttu-id="bda69-109">Las aplicaciones de [servidor Blazor](xref:blazor/hosting-models#blazor-server) llaman a las API web mediante instancias de <xref:System.Net.Http.HttpClient>, creadas normalmente con <xref:System.Net.Http.IHttpClientFactory>.</span><span class="sxs-lookup"><span data-stu-id="bda69-109">[Blazor Server](xref:blazor/hosting-models#blazor-server) apps call web APIs using <xref:System.Net.Http.HttpClient> instances, typically created using <xref:System.Net.Http.IHttpClientFactory>.</span></span> <span data-ttu-id="bda69-110">Las instrucciones de este tema no se aplican a las aplicaciones de servidor Blazor.</span><span class="sxs-lookup"><span data-stu-id="bda69-110">The guidance in this topic doesn't pertain to Blazor Server apps.</span></span> <span data-ttu-id="bda69-111">Al desarrollar aplicaciones de servidor Blazor, siga las instrucciones de <xref:fundamentals/http-requests>.</span><span class="sxs-lookup"><span data-stu-id="bda69-111">When developing Blazor Server apps, follow the guidance in <xref:fundamentals/http-requests>.</span></span>

<span data-ttu-id="bda69-112">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([cómo descargar](xref:index#how-to-download-a-sample))&ndash; seleccione la aplicación *BlazorWebAssemblySample*.</span><span class="sxs-lookup"><span data-stu-id="bda69-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample)) &ndash; Select the *BlazorWebAssemblySample* app.</span></span>

<span data-ttu-id="bda69-113">Vea los componentes siguientes en la aplicación de ejemplo *BlazorWebAssemblySample*:</span><span class="sxs-lookup"><span data-stu-id="bda69-113">See the following components in the *BlazorWebAssemblySample* sample app:</span></span>

* <span data-ttu-id="bda69-114">Llamada a la API web (*Pages/CallWebAPI.razor*)</span><span class="sxs-lookup"><span data-stu-id="bda69-114">Call Web API (*Pages/CallWebAPI.razor*)</span></span>
* <span data-ttu-id="bda69-115">Evaluador de solicitudes HTTP (*Components/HTTPRequestTester.razor*)</span><span class="sxs-lookup"><span data-stu-id="bda69-115">HTTP Request Tester (*Components/HTTPRequestTester.razor*)</span></span>

## <a name="packages"></a><span data-ttu-id="bda69-116">Paquetes</span><span class="sxs-lookup"><span data-stu-id="bda69-116">Packages</span></span>

<span data-ttu-id="bda69-117">Haga referencia al paquete NuGet [System.Net.Http.Json](https://www.nuget.org/packages/System.Net.Http.Json/) en el archivo del proyecto.</span><span class="sxs-lookup"><span data-stu-id="bda69-117">Reference the [System.Net.Http.Json](https://www.nuget.org/packages/System.Net.Http.Json/) NuGet package in the project file.</span></span>

## <a name="add-the-httpclient-service"></a><span data-ttu-id="bda69-118">Agregar el servicio HttpClient</span><span class="sxs-lookup"><span data-stu-id="bda69-118">Add the HttpClient service</span></span>

<span data-ttu-id="bda69-119">En `Program.Main`, agregue un servicio `HttpClient` si aún no existe:</span><span class="sxs-lookup"><span data-stu-id="bda69-119">In `Program.Main`, add an `HttpClient` service if it doesn't already exist:</span></span>

```csharp
builder.Services.AddTransient(sp => 
    new HttpClient
    {
        BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
    });
```

## <a name="httpclient-and-json-helpers"></a><span data-ttu-id="bda69-120">HttpClient y asistentes de JSON</span><span class="sxs-lookup"><span data-stu-id="bda69-120">HttpClient and JSON helpers</span></span>

<span data-ttu-id="bda69-121">En una aplicación WebAssembly de Blazor, [HttpClient](xref:fundamentals/http-requests) está disponible como un servicio preconfigurado para realizar solicitudes de vuelta al servidor de origen.</span><span class="sxs-lookup"><span data-stu-id="bda69-121">In a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) is available as a preconfigured service for making requests back to the origin server.</span></span>

<span data-ttu-id="bda69-122">De forma predeterminada, una aplicación de servidor Blazor no incluye un servicio `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="bda69-122">A Blazor Server app doesn't include an `HttpClient` service by default.</span></span> <span data-ttu-id="bda69-123">Proporcione un servicio `HttpClient` a la aplicación mediante la [infraestructura de fábrica de HttpClient](xref:fundamentals/http-requests).</span><span class="sxs-lookup"><span data-stu-id="bda69-123">Provide an `HttpClient` to the app using the [HttpClient factory infrastructure](xref:fundamentals/http-requests).</span></span>

<span data-ttu-id="bda69-124">`HttpClient` y los asistentes de JSON también se usan para llamar a puntos de conexión de API web de terceros.</span><span class="sxs-lookup"><span data-stu-id="bda69-124">`HttpClient` and JSON helpers are also used to call third-party web API endpoints.</span></span> <span data-ttu-id="bda69-125">`HttpClient` se implementa mediante [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) del explorador y está sujeto a sus limitaciones, incluido el cumplimiento de la directiva del mismo origen.</span><span class="sxs-lookup"><span data-stu-id="bda69-125">`HttpClient` is implemented using the browser [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) and is subject to its limitations, including enforcement of the same origin policy.</span></span>

<span data-ttu-id="bda69-126">La dirección base del cliente se establece en la dirección del servidor de origen.</span><span class="sxs-lookup"><span data-stu-id="bda69-126">The client's base address is set to the originating server's address.</span></span> <span data-ttu-id="bda69-127">Inserte una instancia de `HttpClient` mediante la directiva `@inject`:</span><span class="sxs-lookup"><span data-stu-id="bda69-127">Inject an `HttpClient` instance using the `@inject` directive:</span></span>

```razor
@using System.Net.Http
@inject HttpClient Http
```

<span data-ttu-id="bda69-128">En los ejemplos siguientes, una API web Todo procesa operaciones de creación, lectura, actualización y eliminación (CRUD).</span><span class="sxs-lookup"><span data-stu-id="bda69-128">In the following examples, a Todo web API processes create, read, update, and delete (CRUD) operations.</span></span> <span data-ttu-id="bda69-129">Los ejemplos se basan en una clase `TodoItem` que almacena lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="bda69-129">The examples are based on a `TodoItem` class that stores the:</span></span>

* <span data-ttu-id="bda69-130">Id. (`Id`, `long`): identificador único del elemento.</span><span class="sxs-lookup"><span data-stu-id="bda69-130">ID (`Id`, `long`) &ndash; Unique ID of the item.</span></span>
* <span data-ttu-id="bda69-131">Nombre (`Name`, `string`): nombre del elemento.</span><span class="sxs-lookup"><span data-stu-id="bda69-131">Name (`Name`, `string`) &ndash; Name of the item.</span></span>
* <span data-ttu-id="bda69-132">Estado (`IsComplete`, `bool`): indicación de si el elemento Todo ha finalizado.</span><span class="sxs-lookup"><span data-stu-id="bda69-132">Status (`IsComplete`, `bool`) &ndash; Indication if the Todo item is finished.</span></span>

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

<span data-ttu-id="bda69-133">Los métodos auxiliares de JSON envían solicitudes a un URI (una API web en los ejemplos siguientes) y procesan la respuesta:</span><span class="sxs-lookup"><span data-stu-id="bda69-133">JSON helper methods send requests to a URI (a web API in the following examples) and process the response:</span></span>

* <span data-ttu-id="bda69-134">`GetFromJsonAsync`: envía una solicitud HTTP GET y analiza el cuerpo de respuesta JSON para crear un objeto.</span><span class="sxs-lookup"><span data-stu-id="bda69-134">`GetFromJsonAsync` &ndash; Sends an HTTP GET request and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="bda69-135">En el código siguiente, el componente muestra el elemento `_todoItems`.</span><span class="sxs-lookup"><span data-stu-id="bda69-135">In the following code, the `_todoItems` are displayed by the component.</span></span> <span data-ttu-id="bda69-136">El método `GetTodoItems` se desencadena cuando finaliza la representación del componente ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)).</span><span class="sxs-lookup"><span data-stu-id="bda69-136">The `GetTodoItems` method is triggered when the component is finished rendering ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)).</span></span> <span data-ttu-id="bda69-137">Vea la aplicación de ejemplo para obtener un ejemplo completo.</span><span class="sxs-lookup"><span data-stu-id="bda69-137">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] _todoItems;

      protected override async Task OnInitializedAsync() => 
          _todoItems = await Http.GetFromJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* <span data-ttu-id="bda69-138">`PostAsJsonAsync`: envía una solicitud HTTP POST, incluido el contenido con codificación JSON, y analiza el cuerpo de respuesta JSON para crear un objeto.</span><span class="sxs-lookup"><span data-stu-id="bda69-138">`PostAsJsonAsync` &ndash; Sends an HTTP POST request, including JSON-encoded content, and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="bda69-139">En el código siguiente, un elemento enlazado del componente proporciona `_newItemName`.</span><span class="sxs-lookup"><span data-stu-id="bda69-139">In the following code, `_newItemName` is provided by a bound element of the component.</span></span> <span data-ttu-id="bda69-140">El método `AddItem` se desencadena al seleccionar un elemento `<button>`.</span><span class="sxs-lookup"><span data-stu-id="bda69-140">The `AddItem` method is triggered by selecting a `<button>` element.</span></span> <span data-ttu-id="bda69-141">Vea la aplicación de ejemplo para obtener un ejemplo completo.</span><span class="sxs-lookup"><span data-stu-id="bda69-141">See the sample app for a complete example.</span></span>

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
  
  <span data-ttu-id="bda69-142">Las llamadas a `PostAsJsonAsync` devuelven <xref:System.Net.Http.HttpResponseMessage>.</span><span class="sxs-lookup"><span data-stu-id="bda69-142">Calls to `PostAsJsonAsync` return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="bda69-143">Para deserializar el contenido JSON del mensaje de respuesta, use el método de extensión `ReadFromJsonAsync<T>`:</span><span class="sxs-lookup"><span data-stu-id="bda69-143">To deserialize the JSON content from the response message, use the `ReadFromJsonAsync<T>` extension method:</span></span>
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

* <span data-ttu-id="bda69-144">`PutAsJsonAsync`: envía una solicitud HTTP PUT, incluido el contenido con codificación JSON.</span><span class="sxs-lookup"><span data-stu-id="bda69-144">`PutAsJsonAsync` &ndash; Sends an HTTP PUT request, including JSON-encoded content.</span></span>

  <span data-ttu-id="bda69-145">En el código siguiente, los elementos enlazados del componente proporcionan valores `_editItem` para `Name` e `IsCompleted`.</span><span class="sxs-lookup"><span data-stu-id="bda69-145">In the following code, `_editItem` values for `Name` and `IsCompleted` are provided by bound elements of the component.</span></span> <span data-ttu-id="bda69-146">El valor `Id` del elemento se establece cuando el elemento está seleccionado en otra parte de la interfaz de usuario y se llama a `EditItem`.</span><span class="sxs-lookup"><span data-stu-id="bda69-146">The item's `Id` is set when the item is selected in another part of the UI and `EditItem` is called.</span></span> <span data-ttu-id="bda69-147">El método `SaveItem` se desencadena al seleccionar un objeto `<button>` Guardar.</span><span class="sxs-lookup"><span data-stu-id="bda69-147">The `SaveItem` method is triggered by selecting the Save `<button>` element.</span></span> <span data-ttu-id="bda69-148">Vea la aplicación de ejemplo para obtener un ejemplo completo.</span><span class="sxs-lookup"><span data-stu-id="bda69-148">See the sample app for a complete example.</span></span>

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
  
  <span data-ttu-id="bda69-149">Las llamadas a `PutAsJsonAsync` devuelven <xref:System.Net.Http.HttpResponseMessage>.</span><span class="sxs-lookup"><span data-stu-id="bda69-149">Calls to `PutAsJsonAsync` return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="bda69-150">Para deserializar el contenido JSON del mensaje de respuesta, use el método de extensión `ReadFromJsonAsync<T>`:</span><span class="sxs-lookup"><span data-stu-id="bda69-150">To deserialize the JSON content from the response message, use the `ReadFromJsonAsync<T>` extension method:</span></span>
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

<span data-ttu-id="bda69-151"><xref:System.Net.Http> incluye métodos de extensión adicionales para enviar solicitudes HTTP y recibir respuestas HTTP.</span><span class="sxs-lookup"><span data-stu-id="bda69-151"><xref:System.Net.Http> includes additional extension methods for sending HTTP requests and receiving HTTP responses.</span></span> <span data-ttu-id="bda69-152">[HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) se usa para enviar una solicitud HTTP DELETE a una API web.</span><span class="sxs-lookup"><span data-stu-id="bda69-152">[HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) is used to send an HTTP DELETE request to a web API.</span></span>

<span data-ttu-id="bda69-153">En el código siguiente, el elemento `<button>` Delete llama al método `DeleteItem`.</span><span class="sxs-lookup"><span data-stu-id="bda69-153">In the following code, the Delete `<button>` element calls the `DeleteItem` method.</span></span> <span data-ttu-id="bda69-154">El elemento `<input>` enlazado proporciona el valor `id` del elemento que se va a eliminar.</span><span class="sxs-lookup"><span data-stu-id="bda69-154">The bound `<input>` element supplies the `id` of the item to delete.</span></span> <span data-ttu-id="bda69-155">Vea la aplicación de ejemplo para obtener un ejemplo completo.</span><span class="sxs-lookup"><span data-stu-id="bda69-155">See the sample app for a complete example.</span></span>

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

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="bda69-156">Uso compartido de recursos entre orígenes (CORS)</span><span class="sxs-lookup"><span data-stu-id="bda69-156">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="bda69-157">La seguridad del explorador evita que una página web realice solicitudes a un dominio diferente del que ha proporcionado esa página web.</span><span class="sxs-lookup"><span data-stu-id="bda69-157">Browser security prevents a webpage from making requests to a different domain than the one that served the webpage.</span></span> <span data-ttu-id="bda69-158">Esta restricción se denomina *directiva de mismo origen*.</span><span class="sxs-lookup"><span data-stu-id="bda69-158">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="bda69-159">La directiva de mismo origen evita que un sitio malintencionado lea información confidencial de otro sitio.</span><span class="sxs-lookup"><span data-stu-id="bda69-159">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="bda69-160">Para realizar solicitudes desde el explorador a un punto de conexión con un origen diferente, el *punto de conexión* debe habilitar el [uso compartido de recursos entre orígenes (CORS)](https://www.w3.org/TR/cors/).</span><span class="sxs-lookup"><span data-stu-id="bda69-160">To make requests from the browser to an endpoint with a different origin, the *endpoint* must enable [cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/).</span></span>

<span data-ttu-id="bda69-161">En la [aplicación de ejemplo WebAssembly de Blazor (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) se muestra el uso de CORS en el componente Call Web API (*Pages/CallWebAPI.razor*).</span><span class="sxs-lookup"><span data-stu-id="bda69-161">The [Blazor WebAssembly sample app (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) demonstrates the use of CORS in the Call Web API component (*Pages/CallWebAPI.razor*).</span></span>

<span data-ttu-id="bda69-162">Para permitir que otros sitios realicen solicitudes de uso compartido de recursos entre orígenes (CORS) a la aplicación, vea <xref:security/cors>.</span><span class="sxs-lookup"><span data-stu-id="bda69-162">To allow other sites to make cross-origin resource sharing (CORS) requests to your app, see <xref:security/cors>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bda69-163">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="bda69-163">Additional resources</span></span>

* <xref:security/blazor/webassembly/index>
* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [<span data-ttu-id="bda69-164">Configuración de puntos de conexión HTTPS de Kestrel</span><span class="sxs-lookup"><span data-stu-id="bda69-164">Kestrel HTTPS endpoint configuration</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [<span data-ttu-id="bda69-165">Uso compartido de recursos entre orígenes (CORS) en W3C</span><span class="sxs-lookup"><span data-stu-id="bda69-165">Cross Origin Resource Sharing (CORS) at W3C</span></span>](https://www.w3.org/TR/cors/)
