El `FetchData` componente muestra cómo:

* Aprovisione un token de acceso.
* Use el token de acceso para llamar a una API de recursos protegidos en la aplicación de *servidor* .

La [`@attribute [Authorize]`](xref:mvc/views/razor#attribute) Directiva indica al sistema de autorización de Webassembly increíble que el usuario debe estar autorizado para visitar este componente. La presencia del atributo en la aplicación *cliente* no impide que se llame a la API del servidor sin credenciales adecuadas. La aplicación de *servidor* también debe usar `[Authorize]` en los puntos de conexión adecuados para protegerlos correctamente.

<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider.RequestAccessToken%2A?displayProperty=nameWithType>se encarga de solicitar un token de acceso que se puede Agregar a la solicitud para llamar a la API. Si el token se almacena en caché o el servicio puede aprovisionar un nuevo token de acceso sin la interacción del usuario, la solicitud de token se realiza correctamente. De lo contrario, se produce un error en la solicitud de token con un <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> , que se detecta en una instrucción [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) .

Para obtener el token real que se va a incluir en la solicitud, la aplicación debe comprobar que la solicitud se ha realizado correctamente llamando a [tokenResult. TryGetToken (out var token)](xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A).

Si la solicitud se realizó correctamente, la variable de token se rellena con el token de acceso. La <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessToken.Value?displayProperty=nameWithType> propiedad del token expone la cadena literal que se va a incluir en el encabezado de la `Authorization` solicitud.

Si se produjo un error en la solicitud porque no se pudo aprovisionar el token sin la interacción del usuario, el resultado del token contiene una dirección URL de redireccionamiento. Al navegar a esta dirección URL, el usuario se dirige a la página de inicio de sesión y vuelve a la página actual después de una autenticación correcta.

```razor
@page "/fetchdata"
@using Microsoft.AspNetCore.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@using {APP NAMESPACE}.Shared
@attribute [Authorize]
@inject HttpClient Http

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        try
        {
            forecasts = await Http.GetFromJsonAsync<WeatherForecast[]>("WeatherForecast");
        }
        catch (AccessTokenNotAvailableException exception)
        {
            exception.Redirect();
        }
    }
}
```
