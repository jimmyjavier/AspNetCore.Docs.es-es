<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a>Agregue los paquetes NuGet y las herramientas EF.

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a>Agregar una clase de contexto de base de datos

En el proyecto RazorPagesMovie, cree una carpeta denominada *Data*. Agregue la clase `RazorPagesMovieContext` siguiente a la carpeta *Data*:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

El código anterior crea una propiedad `DbSet` para el conjunto de entidades. En la terminología de Entity Framework, un conjunto de entidades suele corresponderse con una tabla de base de datos, mientras que una entidad lo hace con una fila de la tabla. El código no se compilará hasta que se agreguen las dependencias en un paso posterior.

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a>Agregar una cadena de conexión de base de datos

Agregue una cadena de conexión al archivo *appsettings.json*, tal como se muestra en el código resaltado siguiente:

::: moniker range=">= aspnetcore-3.0"

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a>Registro del contexto de base de datos

Agregue las instrucciones `using` siguientes en la parte superior de *Startup.cs*:

```csharp
using RazorPagesMovie.Data;
using Microsoft.EntityFrameworkCore;
```

Registre el contexto de base de datos con el contenedor de [inserción de dependencias](xref:fundamentals/dependency-injection) en `Startup.ConfigureServices`.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-9)]

### <a name="add-required-nuget-packages"></a>Adición de los paquetes NuGet necesarios

Ejecute el comando siguiente de la CLI de .NET Core para agregar SQLite y CodeGeneration.Design al proyecto:

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
```

El paquete `Microsoft.VisualStudio.Web.CodeGeneration.Design` es necesario para realizar scaffolding.

<a name="reg"></a>

### <a name="register-the-database-context"></a>Registro del contexto de base de datos

Agregue las instrucciones `using` siguientes en la parte superior de *Startup.cs*:

```csharp
using RazorPagesMovie.Models;
using Microsoft.EntityFrameworkCore;
```

Registre el contexto de base de datos con el contenedor de [inserción de dependencias](xref:fundamentals/dependency-injection) en `Startup.ConfigureServices`.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

Compile el proyecto para comprobar si hay errores.

::: moniker-end
