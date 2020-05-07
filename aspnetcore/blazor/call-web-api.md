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
# <a name="call-a-web-api-from-aspnet-core-blazor"></a>Llamada a una API web desde Blazor de ASP.NET Core

Por [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27) y [Juan De la Cruz](https://github.com/juandelacruz23)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Las aplicaciones [WebAssembly de Blazor](xref:blazor/hosting-models#blazor-webassembly) llaman a las API web mediante un servicio `HttpClient` preconfigurado. Redacte las solicitudes, que pueden incluir opciones [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) de JavaScript, mediante asistentes de JSON de Blazor o con <xref:System.Net.Http.HttpRequestMessage>. El servicio `HttpClient` en las aplicaciones WebAssembly de Blazor se centra en realizar solicitudes de vuelta al servidor de origen. Las instrucciones de este tema solo se aplican a las aplicaciones WebAssembly de Blazor.

Las aplicaciones de [servidor Blazor](xref:blazor/hosting-models#blazor-server) llaman a las API web mediante instancias de <xref:System.Net.Http.HttpClient>, creadas normalmente con <xref:System.Net.Http.IHttpClientFactory>. Las instrucciones de este tema no se aplican a las aplicaciones de servidor Blazor. Al desarrollar aplicaciones de servidor Blazor, siga las instrucciones de <xref:fundamentals/http-requests>.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([cómo descargar](xref:index#how-to-download-a-sample))&ndash; seleccione la aplicación *BlazorWebAssemblySample*.

Vea los componentes siguientes en la aplicación de ejemplo *BlazorWebAssemblySample*:

* Llamada a la API web (*Pages/CallWebAPI.razor*)
* Evaluador de solicitudes HTTP (*Components/HTTPRequestTester.razor*)

## <a name="packages"></a>Paquetes

Haga referencia al paquete NuGet [System.Net.Http.Json](https://www.nuget.org/packages/System.Net.Http.Json/) en el archivo del proyecto.

## <a name="add-the-httpclient-service"></a>Agregar el servicio HttpClient

En `Program.Main`, agregue un servicio `HttpClient` si aún no existe:

```csharp
builder.Services.AddTransient(sp => 
    new HttpClient
    {
        BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
    });
```

## <a name="httpclient-and-json-helpers"></a>HttpClient y asistentes de JSON

En una aplicación WebAssembly de Blazor, [HttpClient](xref:fundamentals/http-requests) está disponible como un servicio preconfigurado para realizar solicitudes de vuelta al servidor de origen.

De forma predeterminada, una aplicación de servidor Blazor no incluye un servicio `HttpClient`. Proporcione un servicio `HttpClient` a la aplicación mediante la [infraestructura de fábrica de HttpClient](xref:fundamentals/http-requests).

`HttpClient` y los asistentes de JSON también se usan para llamar a puntos de conexión de API web de terceros. `HttpClient` se implementa mediante [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) del explorador y está sujeto a sus limitaciones, incluido el cumplimiento de la directiva del mismo origen.

La dirección base del cliente se establece en la dirección del servidor de origen. Inserte una instancia de `HttpClient` mediante la directiva `@inject`:

```razor
@using System.Net.Http
@inject HttpClient Http
```

En los ejemplos siguientes, una API web Todo procesa operaciones de creación, lectura, actualización y eliminación (CRUD). Los ejemplos se basan en una clase `TodoItem` que almacena lo siguiente:

* Id. (`Id`, `long`): identificador único del elemento.
* Nombre (`Name`, `string`): nombre del elemento.
* Estado (`IsComplete`, `bool`): indicación de si el elemento Todo ha finalizado.

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

Los métodos auxiliares de JSON envían solicitudes a un URI (una API web en los ejemplos siguientes) y procesan la respuesta:

* `GetFromJsonAsync`: envía una solicitud HTTP GET y analiza el cuerpo de respuesta JSON para crear un objeto.

  En el código siguiente, el componente muestra el elemento `_todoItems`. El método `GetTodoItems` se desencadena cuando finaliza la representación del componente ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)). Vea la aplicación de ejemplo para obtener un ejemplo completo.

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] _todoItems;

      protected override async Task OnInitializedAsync() => 
          _todoItems = await Http.GetFromJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* `PostAsJsonAsync`: envía una solicitud HTTP POST, incluido el contenido con codificación JSON, y analiza el cuerpo de respuesta JSON para crear un objeto.

  En el código siguiente, un elemento enlazado del componente proporciona `_newItemName`. El método `AddItem` se desencadena al seleccionar un elemento `<button>`. Vea la aplicación de ejemplo para obtener un ejemplo completo.

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
  
  Las llamadas a `PostAsJsonAsync` devuelven <xref:System.Net.Http.HttpResponseMessage>. Para deserializar el contenido JSON del mensaje de respuesta, use el método de extensión `ReadFromJsonAsync<T>`:
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

* `PutAsJsonAsync`: envía una solicitud HTTP PUT, incluido el contenido con codificación JSON.

  En el código siguiente, los elementos enlazados del componente proporcionan valores `_editItem` para `Name` e `IsCompleted`. El valor `Id` del elemento se establece cuando el elemento está seleccionado en otra parte de la interfaz de usuario y se llama a `EditItem`. El método `SaveItem` se desencadena al seleccionar un objeto `<button>` Guardar. Vea la aplicación de ejemplo para obtener un ejemplo completo.

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
  
  Las llamadas a `PutAsJsonAsync` devuelven <xref:System.Net.Http.HttpResponseMessage>. Para deserializar el contenido JSON del mensaje de respuesta, use el método de extensión `ReadFromJsonAsync<T>`:
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

<xref:System.Net.Http> incluye métodos de extensión adicionales para enviar solicitudes HTTP y recibir respuestas HTTP. [HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) se usa para enviar una solicitud HTTP DELETE a una API web.

En el código siguiente, el elemento `<button>` Delete llama al método `DeleteItem`. El elemento `<input>` enlazado proporciona el valor `id` del elemento que se va a eliminar. Vea la aplicación de ejemplo para obtener un ejemplo completo.

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

## <a name="cross-origin-resource-sharing-cors"></a>Uso compartido de recursos entre orígenes (CORS)

La seguridad del explorador evita que una página web realice solicitudes a un dominio diferente del que ha proporcionado esa página web. Esta restricción se denomina *directiva de mismo origen*. La directiva de mismo origen evita que un sitio malintencionado lea información confidencial de otro sitio. Para realizar solicitudes desde el explorador a un punto de conexión con un origen diferente, el *punto de conexión* debe habilitar el [uso compartido de recursos entre orígenes (CORS)](https://www.w3.org/TR/cors/).

En la [aplicación de ejemplo WebAssembly de Blazor (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) se muestra el uso de CORS en el componente Call Web API (*Pages/CallWebAPI.razor*).

Para permitir que otros sitios realicen solicitudes de uso compartido de recursos entre orígenes (CORS) a la aplicación, vea <xref:security/cors>.

## <a name="additional-resources"></a>Recursos adicionales

* <xref:security/blazor/webassembly/index>
* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [Configuración de puntos de conexión HTTPS de Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [Uso compartido de recursos entre orígenes (CORS) en W3C](https://www.w3.org/TR/cors/)
