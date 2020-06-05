::: moniker range=">= aspnetcore-3.0"

Ejecute el scaffolding de identidad:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* En **Explorador de soluciones**, haga clic con el botón derecho en el proyecto > **Agregar** > **nuevo elemento con scaffolding**.
* En el panel izquierdo del cuadro de diálogo **Agregar scaffold** , seleccione **Identity** > **Add (agregar**identidad).
* En el cuadro de diálogo **Agregar identidad** , seleccione las opciones que desee.
  * Seleccione la página de diseño existente para que el archivo de diseño no se sobrescriba con un marcado incorrecto. Cuando se selecciona un archivo * \_ layout. cshtml* existente, **no** se sobrescribe. Por ejemplo:
    * `~/Pages/Shared/_Layout.cshtml`para los proyectos de servidor Razor Pages o increíble con la infraestructura de Razor Pages existente
    * `~/Views/Shared/_Layout.cshtml`para proyectos de MVC o proyectos de servidor increíbles con la infraestructura MVC existente
* Para usar el contexto de datos existente, seleccione al menos un archivo para invalidar. Debe seleccionar al menos un archivo para agregar el contexto de datos.
  * Seleccione la clase de contexto de datos.
  * Seleccione **Agregar**.
* Para crear un nuevo contexto de usuario y, posiblemente, crear una clase de usuario personalizada para la identidad:
  * Seleccione el **+** botón para crear una nueva **clase de contexto de datos**. Acepte el valor predeterminado o especifique una clase (por ejemplo, `MyApplication.Data.ApplicationDbContext` ).
  * Seleccione **Agregar**.

Nota: Si va a crear un nuevo contexto de usuario, no tiene que seleccionar un archivo para invalidar.

# <a name="net-core-cli"></a>[CLI de .NET Core](#tab/netcore-cli)

Si no ha instalado previamente el scaffolding de ASP.NET Core, instálelo ahora:

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

Agregue las referencias de paquete de NuGet necesarias al archivo de proyecto (*. csproj*). Ejecute los siguientes comandos en el directorio del proyecto:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

Ejecute el siguiente comando para enumerar las opciones del scaffolding de identidad:

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

[!INCLUDE[](~/includes/scaffoldTFM.md)]

En la carpeta del proyecto, ejecute el scaffolding de identidad con las opciones que desee. Por ejemplo, para configurar la identidad con la interfaz de usuario predeterminada y el número mínimo de archivos, ejecute el siguiente comando. Use el nombre completo correcto para el contexto de base de BD:

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login"
```

PowerShell usa punto y coma como separador de comandos. Al usar PowerShell, use el carácter de escape de punto y coma en la lista de archivos o ponga la lista de archivos entre comillas dobles. Por ejemplo:

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

Si ejecuta el scaffolding de identidad sin especificar la `--files` marca o la `--useDefaultUI` marca, se crearán todas las páginas de la interfaz de usuario de identidad disponibles en el proyecto.

---

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Ejecute el scaffolding de identidad:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* En **Explorador de soluciones**, haga clic con el botón derecho en el proyecto > **Agregar** > **nuevo elemento con scaffolding**.
* En el panel izquierdo del cuadro de diálogo **Agregar scaffold** , seleccione **Identity** > **Add (agregar**identidad).
* En el cuadro de diálogo **Agregar identidad** , seleccione las opciones que desee.
  * Seleccione la página de diseño existente o el archivo de diseño se sobrescribirá con un marcado incorrecto. Cuando se selecciona un archivo * \_ layout. cshtml* existente, **no** se sobrescribe. Por ejemplo:
    * `~/Pages/Shared/_Layout.cshtml`por Razor Pages
    * `~/Views/Shared/_Layout.cshtml`para proyectos de MVC
* Para usar el contexto de datos existente, seleccione al menos un archivo para invalidar. Debe seleccionar al menos un archivo para agregar el contexto de datos.
  * Seleccione la clase de contexto de datos.
  * Seleccione **Agregar**.
* Para crear un nuevo contexto de usuario y, posiblemente, crear una clase de usuario personalizada para la identidad:
  * Seleccione el **+** botón para crear una nueva **clase de contexto de datos**. Acepte el valor predeterminado o especifique una clase (por ejemplo, `MyApplication.Data.ApplicationDbContext` ).
  * Seleccione **Agregar**.

Nota: Si va a crear un nuevo contexto de usuario, no tiene que seleccionar un archivo para invalidar.

# <a name="net-core-cli"></a>[CLI de .NET Core](#tab/netcore-cli)

Si no ha instalado previamente el scaffolding de ASP.NET Core, instálelo ahora:

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

Agregue una referencia de paquete a [Microsoft. VisualStudio. Web. CodeGeneration. Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) al archivo de proyecto (*. csproj*). Ejecute los siguientes comandos en el directorio del proyecto:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

Ejecute el siguiente comando para enumerar las opciones del scaffolding de identidad:

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

En la carpeta del proyecto, ejecute el scaffolding de identidad con las opciones que desee. Por ejemplo, para configurar la identidad con la interfaz de usuario predeterminada y el número mínimo de archivos, ejecute el siguiente comando. Use el nombre completo correcto para el contexto de base de BD:

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login"
```

PowerShell usa punto y coma como separador de comandos. Al usar PowerShell, use el carácter de escape de punto y coma en la lista de archivos o ponga la lista de archivos entre comillas dobles. Por ejemplo:

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

Si ejecuta el scaffolding de identidad sin especificar la `--files` marca o la `--useDefaultUI` marca, se crearán todas las páginas de la interfaz de usuario de identidad disponibles en el proyecto.

---

::: moniker-end
