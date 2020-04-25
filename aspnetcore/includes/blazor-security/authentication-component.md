<span data-ttu-id="02d38-101">La página generada por `Authentication` el componente (*pages/Authentication. Razor*) define las rutas necesarias para controlar diferentes fases de autenticación.</span><span class="sxs-lookup"><span data-stu-id="02d38-101">The page produced by the `Authentication` component (*Pages/Authentication.razor*) defines the routes required for handling different authentication stages.</span></span>

<span data-ttu-id="02d38-102">El `RemoteAuthenticatorView` componente:</span><span class="sxs-lookup"><span data-stu-id="02d38-102">The `RemoteAuthenticatorView` component:</span></span>

* <span data-ttu-id="02d38-103">Lo proporciona el paquete [Microsoft. AspNetCore. Components. Webassembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) .</span><span class="sxs-lookup"><span data-stu-id="02d38-103">Is provided by the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package.</span></span>
* <span data-ttu-id="02d38-104">Administra la realización de las acciones adecuadas en cada fase de autenticación.</span><span class="sxs-lookup"><span data-stu-id="02d38-104">Manages performing the appropriate actions at each stage of authentication.</span></span>

```razor
@page "/authentication/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code {
    [Parameter]
    public string Action { get; set; }
}
```
