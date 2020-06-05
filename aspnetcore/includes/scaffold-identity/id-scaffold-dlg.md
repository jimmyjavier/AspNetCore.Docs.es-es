Ejecute el scaffolding de identidad:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* En **Explorador de soluciones**, haga clic con el botón derecho en el proyecto > **Agregar**  >  **nuevo elemento con scaffolding**.
* En el panel izquierdo del cuadro de diálogo **Agregar nuevo elemento con scaffolding** , seleccione **identidad**  >  **Agregar**.
* En el cuadro de diálogo **Agregar identidad** , seleccione las opciones que desee.
  * Seleccione la página de diseño existente o el archivo de diseño se sobrescribirá con un marcado incorrecto:
    * `~/Pages/Shared/_Layout.cshtml`por Razor Pages
    * `~/Views/Shared/_Layout.cshtml`para proyectos de MVC
    * Las aplicaciones de servidor increíbles que se crean a partir de la plantilla de servidor de extraordinarias ( `blazorserver` ) no están configuradas para Razor pages o MVC de forma predeterminada. Deje la entrada página de diseño en blanco.
  * Seleccione el **+** botón para crear una nueva **clase de contexto de datos**. Acepte el valor predeterminado o especifique una clase (por ejemplo, `MyApplication.Data.ApplicationDbContext` ).
* Seleccione **Agregar**.

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

En la carpeta del proyecto, ejecute el scaffolding de identidad con las opciones que desee. Por ejemplo, para configurar la identidad con la interfaz de usuario predeterminada y el número mínimo de archivos, ejecute el siguiente comando:

```dotnetcli
dotnet aspnet-codegenerator identity --useDefaultUI
```

---
