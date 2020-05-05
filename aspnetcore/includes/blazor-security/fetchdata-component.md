<span data-ttu-id="042ad-101">El `FetchData` componente muestra cómo:</span><span class="sxs-lookup"><span data-stu-id="042ad-101">The `FetchData` component shows how to:</span></span>

* <span data-ttu-id="042ad-102">Aprovisione un token de acceso.</span><span class="sxs-lookup"><span data-stu-id="042ad-102">Provision an access token.</span></span>
* <span data-ttu-id="042ad-103">Use el token de acceso para llamar a una API de recursos protegidos en la aplicación de *servidor* .</span><span class="sxs-lookup"><span data-stu-id="042ad-103">Use the access token to call a protected resource API in the *Server* app.</span></span>

<span data-ttu-id="042ad-104">La `@attribute [Authorize]` Directiva indica al sistema de autorización de webassembly increíble que el usuario debe estar autorizado para visitar este componente.</span><span class="sxs-lookup"><span data-stu-id="042ad-104">The `@attribute [Authorize]` directive indicates to the Blazor WebAssembly authorization system that the user must be authorized in order to visit this component.</span></span> <span data-ttu-id="042ad-105">La presencia del atributo en la aplicación *cliente* no impide que se llame a la API del servidor sin credenciales adecuadas.</span><span class="sxs-lookup"><span data-stu-id="042ad-105">The presence of the attribute in the *Client* app doesn't prevent the API on the server from being called without proper credentials.</span></span> <span data-ttu-id="042ad-106">La aplicación de *servidor* también debe `[Authorize]` usar en los puntos de conexión adecuados para protegerlos correctamente.</span><span class="sxs-lookup"><span data-stu-id="042ad-106">The *Server* app also must use `[Authorize]` on the appropriate endpoints to correctly protect them.</span></span>

<span data-ttu-id="042ad-107">`IAccessTokenProvider.RequestAccessToken();`se encarga de solicitar un token de acceso que se puede Agregar a la solicitud para llamar a la API.</span><span class="sxs-lookup"><span data-stu-id="042ad-107">`IAccessTokenProvider.RequestAccessToken();` takes care of requesting an access token that can be added to the request to call the API.</span></span> <span data-ttu-id="042ad-108">Si el token se almacena en caché o el servicio puede aprovisionar un nuevo token de acceso sin la interacción del usuario, la solicitud de token se realiza correctamente.</span><span class="sxs-lookup"><span data-stu-id="042ad-108">If the token is cached or the service is able to provision a new access token without user interaction, the token request succeeds.</span></span> <span data-ttu-id="042ad-109">De lo contrario, se produce un `AccessTokenNotAvailableException` error en la solicitud de token, que `try-catch` se detecta en una instrucción.</span><span class="sxs-lookup"><span data-stu-id="042ad-109">Otherwise, the token request fails with an `AccessTokenNotAvailableException` error, which is caught in a `try-catch` statement.</span></span>

<span data-ttu-id="042ad-110">Para obtener el token real que se va a incluir en la solicitud, la aplicación debe comprobar que la solicitud se ha `tokenResult.TryGetToken(out var token)`realizado correctamente mediante una llamada a.</span><span class="sxs-lookup"><span data-stu-id="042ad-110">In order to obtain the actual token to include in the request, the app must check that the request succeeded by calling `tokenResult.TryGetToken(out var token)`.</span></span> 

<span data-ttu-id="042ad-111">Si la solicitud se realizó correctamente, la variable de token se rellena con el token de acceso.</span><span class="sxs-lookup"><span data-stu-id="042ad-111">If the request was successful, the token variable is populated with the access token.</span></span> <span data-ttu-id="042ad-112">La `Value` propiedad del token expone la cadena literal que se va a incluir en `Authorization` el encabezado de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="042ad-112">The `Value` property of the token exposes the literal string to include in the `Authorization` request header.</span></span>

<span data-ttu-id="042ad-113">Si se produjo un error en la solicitud porque no se pudo aprovisionar el token sin la interacción del usuario, el resultado del token contiene una dirección URL de redireccionamiento.</span><span class="sxs-lookup"><span data-stu-id="042ad-113">If the request failed because the token couldn't be provisioned without user interaction, the token result contains a redirect URL.</span></span> <span data-ttu-id="042ad-114">Al navegar a esta dirección URL, el usuario se dirige a la página de inicio de sesión y vuelve a la página actual después de una autenticación correcta.</span><span class="sxs-lookup"><span data-stu-id="042ad-114">Navigating to this URL takes the user to the login page and back to the current page after a successful authentication.</span></span>

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
