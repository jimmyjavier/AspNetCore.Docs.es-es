El `FetchData` componente muestra cómo:

* Aprovisione un token de acceso.
* Use el token de acceso para llamar a una API de recursos protegidos en la aplicación de *servidor* .

La `@attribute [Authorize]` Directiva indica al sistema de autorización de webassembly increíble que el usuario debe estar autorizado para visitar este componente. La presencia del atributo en la aplicación *cliente* no impide que se llame a la API del servidor sin credenciales adecuadas. La aplicación de *servidor* también debe `[Authorize]` usar en los puntos de conexión adecuados para protegerlos correctamente.

`AuthenticationService.RequestAccessToken();`se encarga de solicitar un token de acceso que se puede Agregar a la solicitud para llamar a la API. Si el token se almacena en caché o el servicio puede aprovisionar un nuevo token de acceso sin la interacción del usuario, la solicitud de token se realiza correctamente. De lo contrario, se produce un error en la solicitud de token.

Para obtener el token real que se va a incluir en la solicitud, la aplicación debe comprobar que la solicitud se ha `tokenResult.TryGetToken(out var token)`realizado correctamente mediante una llamada a. 

Si la solicitud se realizó correctamente, la variable de token se rellena con el token de acceso. La `Value` propiedad del token expone la cadena literal que se va a incluir en `Authorization` el encabezado de la solicitud.

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
