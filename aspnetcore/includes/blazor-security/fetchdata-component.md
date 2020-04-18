El `FetchData` componente muestra cómo:

* Aprovisione un token de acceso.
* Use el token de acceso para llamar a una API de recursos protegidos en la aplicación *Servidor.*

La `@attribute [Authorize]` directiva indica al sistema de autorización Blazor WebAssembly que el usuario debe estar autorizado para visitar este componente. La presencia del atributo en la aplicación *cliente* no impide que se llame a la API en el servidor sin las credenciales adecuadas. La aplicación *Servidor* `[Authorize]` también debe usarse en los puntos de conexión adecuados para protegerlos correctamente.

`AuthenticationService.RequestAccessToken();`se encarga de solicitar un token de acceso que se pueda agregar a la solicitud para llamar a la API. Si el token se almacena en caché o el servicio puede aprovisionar un nuevo token de acceso sin interacción del usuario, la solicitud de token se realiza correctamente. De lo contrario, se produce un error en la solicitud de token.

Para obtener el token real que se incluirá en la solicitud, la `tokenResult.TryGetToken(out var token)`aplicación debe comprobar que la solicitud se realizó correctamente llamando a . 

Si la solicitud se realizó correctamente, la variable de token se rellena con el token de acceso. La `Value` propiedad del token expone la cadena `Authorization` literal que se va a incluir en el encabezado de la solicitud.

Si se produjo un error en la solicitud porque el token no se pudo aprovisionar sin la interacción del usuario, el resultado del token contiene una dirección URL de redirección. Navegar a esta dirección URL lleva al usuario a la página de inicio de sesión y de vuelta a la página actual después de una autenticación correcta.

```razor
@page "/fetchdata"
@using Microsoft.AspNetCore.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject IAccessTokenProvider AuthenticationService
@inject NavigationManager Navigation
@using {APPLICATION NAMESPACE}.Shared
@attribute [Authorize]

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        var httpClient = new HttpClient();
        httpClient.BaseAddress = new Uri(Navigation.BaseUri);

        var tokenResult = await AuthenticationService.RequestAccessToken();

        if (tokenResult.TryGetToken(out var token))
        {
            httpClient.DefaultRequestHeaders.Add("Authorization", 
                $"Bearer {token.Value}");
            forecasts = await httpClient.GetJsonAsync<WeatherForecast[]>(
                "WeatherForecast");
        }
        else
        {
            Navigation.NavigateTo(tokenResult.RedirectUrl);
        }

    }
}
```

Para obtener más información, consulte Guardar el estado de la aplicación antes de [una operación](xref:security/blazor/webassembly/additional-scenarios#save-app-state-before-an-authentication-operation)de autenticación.
