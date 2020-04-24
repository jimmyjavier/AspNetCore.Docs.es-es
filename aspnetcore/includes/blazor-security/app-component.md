El `App` componente (*app. Razor*) es similar al componente `App` que se encuentra en las aplicaciones de servidor increíbles:

* El `CascadingAuthenticationState` componente administra la exposición del `AuthenticationState` al resto de la aplicación.
* El `AuthorizeRouteView` componente se asegura de que el usuario actual está autorizado para tener acceso a una página determinada o, `RedirectToLogin` de lo contrario, representa el componente.
* El `RedirectToLogin` componente administra el redireccionamiento de usuarios no autorizados a la página de inicio de sesión.

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
