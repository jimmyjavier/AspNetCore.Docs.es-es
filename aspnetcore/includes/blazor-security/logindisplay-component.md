<span data-ttu-id="eb664-101">El `LoginDisplay` componente (*Shared/LoginDisplay. Razor*) se representa en el `MainLayout` componente (*Shared/MainLayout. Razor*) y administra los comportamientos siguientes:</span><span class="sxs-lookup"><span data-stu-id="eb664-101">The `LoginDisplay` component (*Shared/LoginDisplay.razor*) is rendered in the `MainLayout` component (*Shared/MainLayout.razor*) and manages the following behaviors:</span></span>

* <span data-ttu-id="eb664-102">Para usuarios autenticados:</span><span class="sxs-lookup"><span data-stu-id="eb664-102">For authenticated users:</span></span>
  * <span data-ttu-id="eb664-103">Muestra el nombre de usuario actual.</span><span class="sxs-lookup"><span data-stu-id="eb664-103">Displays the current username.</span></span>
  * <span data-ttu-id="eb664-104">Ofrece un botón para cerrar la sesión de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="eb664-104">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="eb664-105">En el caso de los usuarios anónimos, ofrece la opción de iniciar sesión.</span><span class="sxs-lookup"><span data-stu-id="eb664-105">For anonymous users, offers the option to log in.</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        Hello, @context.User.Identity.Name!
        <button class="nav-link btn btn-link" @onclick="BeginLogout">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code {
    private async Task BeginLogout(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```
