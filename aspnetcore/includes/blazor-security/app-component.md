<span data-ttu-id="20c09-101">El `App` componente (*app. Razor*) es similar al `App` componente que se encuentra en las aplicaciones de servidor increíbles:</span><span class="sxs-lookup"><span data-stu-id="20c09-101">The `App` component (*App.razor*) is similar to the `App` component found in Blazor Server apps:</span></span>

* <span data-ttu-id="20c09-102">El <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> componente administra la exposición del <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> al resto de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="20c09-102">The <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> component manages exposing the <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> to the rest of the app.</span></span>
* <span data-ttu-id="20c09-103">El <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> componente se asegura de que el usuario actual está autorizado para tener acceso a una página determinada o, de lo contrario, representa el `RedirectToLogin` componente.</span><span class="sxs-lookup"><span data-stu-id="20c09-103">The <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> component makes sure that the current user is authorized to access a given page or otherwise renders the `RedirectToLogin` component.</span></span>
* <span data-ttu-id="20c09-104">El `RedirectToLogin` componente administra el redireccionamiento de usuarios no autorizados a la página de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="20c09-104">The `RedirectToLogin` component manages redirecting unauthorized users to the login page.</span></span>

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="@typeof(Program).Assembly">
        <Found Context="routeData">
            <AuthorizeRouteView RouteData="@routeData" 
                DefaultLayout="@typeof(MainLayout)">
                <NotAuthorized>
                    @if (!context.User.Identity.IsAuthenticated)
                    {
                        <RedirectToLogin />
                    }
                    else
                    {
                        <p>
                            You are not authorized to access 
                            this resource.
                        </p>
                    }
                </NotAuthorized>
            </AuthorizeRouteView>
        </Found>
        <NotFound>
            <LayoutView Layout="@typeof(MainLayout)">
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </NotFound>
    </Router>
</CascadingAuthenticationState>
```
