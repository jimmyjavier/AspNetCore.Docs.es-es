---
title: Almacenamiento seguro de secretos de aplicaciones en desarrollo en ASP.NET Core
author: rick-anderson
description: Obtén información sobre cómo almacenar y recuperar información confidencial como secretos de aplicación durante el desarrollo de una aplicación ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 4/20/2020
uid: security/app-secrets
ms.openlocfilehash: c62c5e59ad0a72506fb72bda82aa821a4f1719c8
ms.sourcegitcommit: c9d1208e86160615b2d914cce74a839ae41297a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/22/2020
ms.locfileid: "81791582"
---
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a>Almacenamiento seguro de secretos de aplicaciones en desarrollo en ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Por [Rick Anderson,](https://twitter.com/RickAndMSFT) [Kirk Larkin,](https://twitter.com/serpent5) [Daniel Roth](https://github.com/danroth27)y [Scott Addie](https://github.com/scottaddie)

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))

En este documento se explican las técnicas para almacenar y recuperar datos confidenciales durante el desarrollo de una aplicación ASP.NET Core en una máquina de desarrollo. Nunca almacene contraseñas u otros datos confidenciales en el código fuente. Los secretos de producción no deben usarse para el desarrollo o la prueba. Los secretos no deben implementarse con la aplicación. En su lugar, los secretos deben estar disponibles en el entorno de producción a través de un medio controlado como variables de entorno, Azure Key Vault, etc. Puede almacenar y proteger los secretos de prueba y producción de Azure con el proveedor de configuración de [Azure Key Vault.](xref:security/key-vault-configuration)

## <a name="environment-variables"></a>Variables de entorno

Las variables de entorno se usan para evitar el almacenamiento de secretos de aplicación en el código o en archivos de configuración local. Las variables de entorno invalidan los valores de configuración de todos los orígenes de configuración especificados anteriormente.

Considere una aplicación web ASP.NET Core en la que está habilitada la seguridad de cuentas de **usuario individuales.** Se incluye una cadena de conexión de base de datos `DefaultConnection`predeterminada en el archivo *appsettings.json* del proyecto con la clave . La cadena de conexión predeterminada es para LocalDB, que se ejecuta en modo de usuario y no requiere una contraseña. Durante la implementación de la aplicación, el `DefaultConnection` valor de clave se puede invalidar con el valor de una variable de entorno. La variable de entorno puede almacenar la cadena de conexión completa con credenciales confidenciales.

> [!WARNING]
> Las variables de entorno generalmente se almacenan en texto sin cifrar. Si la máquina o el proceso se ven comprometidos, las partes que no son de confianza pueden acceder a las variables de entorno. Es posible que se requieran medidas adicionales para evitar la divulgación de secretos de usuario.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a>Administrador de secretos

La herramienta Secret Manager almacena datos confidenciales durante el desarrollo de un proyecto ASP.NET Core. En este contexto, un fragmento de datos confidenciales es un secreto de aplicación. Los secretos de aplicación se almacenan en una ubicación independiente del árbol del proyecto. Los secretos de la aplicación se asocian a un proyecto específico o se comparten entre varios proyectos. Los secretos de la aplicación no se protegen en el control de código fuente.

> [!WARNING]
> La herramienta Secret Manager no cifra los secretos almacenados y no debe tratarse como un almacén de confianza. Es sólo para fines de desarrollo. Las claves y los valores se almacenan en un archivo de configuración JSON en el directorio de perfil de usuario.

## <a name="how-the-secret-manager-tool-works"></a>Cómo funciona la herramienta Secret Manager

La herramienta Secret Manager abstrae los detalles de implementación, como dónde y cómo se almacenan los valores. Puede utilizar la herramienta sin conocer estos detalles de implementación. Los valores se almacenan en un archivo de configuración JSON en una carpeta de perfil de usuario protegida por el sistema en el equipo local:

# <a name="windows"></a>[Windows](#tab/windows)

Ruta del sistema de archivos:

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[Linux / macOS](#tab/linux+macos)

Ruta del sistema de archivos:

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

En las rutas de `<user_secrets_id>` acceso `UserSecretsId` de archivo anteriores, sustitúyala por el valor especificado en el archivo *.csproj.*

No escriba código que dependa de la ubicación o el formato de los datos guardados con la herramienta Secret Manager. Estos detalles de implementación pueden cambiar. Por ejemplo, los valores secretos no se cifran, pero podrían estar en el futuro.

## <a name="enable-secret-storage"></a>Habilitar el almacenamiento secreto

La herramienta Secret Manager funciona en los valores de configuración específicos del proyecto almacenados en su perfil de usuario.

La herramienta Secret `init` Manager incluye un comando en .NET Core SDK 3.0.100 o posterior. Para utilizar secretos de usuario, ejecute el siguiente comando en el directorio del proyecto:

```dotnetcli
dotnet user-secrets init
```

El comando anterior agrega `UserSecretsId` un `PropertyGroup` elemento dentro de un archivo *.csproj.* De forma predeterminada, `UserSecretsId` el texto interno de es un GUID. El texto interno es arbitrario, pero es único para el proyecto.

[!code-xml[](app-secrets/samples/3.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

En Visual Studio, haga clic con el botón secundario en el proyecto en el Explorador de soluciones y seleccione **Administrar secretos** de usuario en el menú contextual. Este gesto `UserSecretsId` agrega un elemento, rellenado con un GUID, al archivo *.csproj.*

## <a name="set-a-secret"></a>Establecer un secreto

Defina un secreto de aplicación que consta de una clave y su valor. El secreto está asociado con `UserSecretsId` el valor del proyecto. Por ejemplo, ejecute el siguiente comando desde el directorio en el que existe el archivo *.csproj:*

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

En el ejemplo anterior, los `Movies` dos puntos denota que es un literal de objeto con una `ServiceApiKey` propiedad.

La herramienta Secret Manager también se puede utilizar desde otros directorios. Utilice `--project` la opción para proporcionar la ruta de acceso del sistema de archivos en la que existe el archivo *.csproj.* Por ejemplo:

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a>Aplanamiento de estructura JSON en Visual Studio

El gesto **Administrar secretos** de usuario de Visual Studio abre un archivo *secrets.json* en el editor de texto. Reemplace el contenido de *secrets.json* por los pares clave-valor que se van a almacenar. Por ejemplo:

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

La estructura JSON se acopla después de las modificaciones a través `dotnet user-secrets remove` o `dotnet user-secrets set`. Por ejemplo, `dotnet user-secrets remove "Movies:ConnectionString"` la `Movies` ejecución contrae el literal del objeto. El archivo modificado es similar al siguiente:

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a>Establecer varios secretos

Un lote de secretos se puede `set` establecer canalizando JSON al comando. En el ejemplo siguiente, el contenido del archivo *input.json* se canaliza al `set` comando.

# <a name="windows"></a>[Windows](#tab/windows)

Abra un shell de comandos y ejecute el siguiente comando:

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[Linux / macOS](#tab/linux+macos)

Abra un shell de comandos y ejecute el siguiente comando:

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a>Acceder a un secreto

La API de [configuración de ASP.NET Core](xref:fundamentals/configuration/index) proporciona acceso a secretos de Secret Manager.

El origen de configuración de secretos de <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> usuario se agrega automáticamente en modo de desarrollo cuando el proyecto llama para inicializar una nueva instancia del host con valores predeterminados preconfigurados. `CreateDefaultBuilder`llamadas <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> <xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> cuando <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development>el es :

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program.cs?name=snippet_CreateHostBuilder&highlight=2)]

Cuando `CreateDefaultBuilder` no se llama, agregue explícitamente el <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A>origen de configuración de secretos de usuario llamando a . Llame `AddUserSecrets` solo cuando la aplicación se ejecute en el entorno de desarrollo, como se muestra en el ejemplo siguiente:

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program2.cs?name=snippet_Host&highlight=6-9)]

Los secretos de usuario `Configuration` se pueden recuperar a través de la API:

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a>Mapa de secretos a un POCO

Asignar un literal de objeto completo a un POCO (una clase .NET simple con propiedades) es útil para agregar propiedades relacionadas.

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Para asignar los secretos anteriores `Configuration` a un POCO, utilice la característica de enlace de gráfico de [objetos](xref:fundamentals/configuration/index#bind-to-an-object-graph) de la API. El código siguiente se `MovieSettings` enlaza a un `ServiceApiKey` POCO personalizado y tiene acceso al valor de propiedad:

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

Los `Movies:ConnectionString` `Movies:ServiceApiKey` secretos y se asignan `MovieSettings`a las propiedades respectivas en:

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a>Reemplazo de cuerdas con secretos

Almacenar contraseñas en texto sin formato es inseguro. Por ejemplo, una cadena de conexión de base de datos almacenada en *appsettings.json* puede incluir una contraseña para el usuario especificado:

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

Un enfoque más seguro es almacenar la contraseña como un secreto. Por ejemplo:

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

Quite `Password` el par clave-valor de la cadena de conexión en *appsettings.json*. Por ejemplo:

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings.json?highlight=3)]

El valor del secreto se <xref:System.Data.SqlClient.SqlConnectionStringBuilder> puede establecer <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> en la propiedad de un objeto para completar la cadena de conexión:

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a>Lista de los secretos

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Ejecute el siguiente comando desde el directorio en el que existe el archivo *.csproj:*

```dotnetcli
dotnet user-secrets list
```

Se mostrará lo siguiente:

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

En el ejemplo anterior, dos puntos de los nombres de clave denota la jerarquía de objetos dentro de *secrets.json*.

## <a name="remove-a-single-secret"></a>Eliminar un solo secreto

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Ejecute el siguiente comando desde el directorio en el que existe el archivo *.csproj:*

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

El archivo *secrets.json* de la aplicación se modificó para `MoviesConnectionString` quitar el par clave-valor asociado a la clave:

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

`dotnet user-secrets list`muestra el siguiente mensaje:

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a>Eliminar todos los secretos

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Ejecute el siguiente comando desde el directorio en el que existe el archivo *.csproj:*

```dotnetcli
dotnet user-secrets clear
```

Todos los secretos de usuario de la aplicación se han eliminado del archivo *secrets.json:*

```json
{}
```

En `dotnet user-secrets list` ejecución se muestra el siguiente mensaje:

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a>Recursos adicionales

* Consulte [este problema](https://github.com/dotnet/AspNetCore.Docs/issues/16328) para obtener información sobre cómo obtener información sobre el acceso a Secret Manager desde IIS.
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Por [Rick Anderson,](https://twitter.com/RickAndMSFT) [Daniel Roth](https://github.com/danroth27)y Scott [Addie](https://github.com/scottaddie)

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))

En este documento se explican las técnicas para almacenar y recuperar datos confidenciales durante el desarrollo de una aplicación ASP.NET Core en una máquina de desarrollo. Nunca almacene contraseñas u otros datos confidenciales en el código fuente. Los secretos de producción no deben usarse para el desarrollo o la prueba. Los secretos no deben implementarse con la aplicación. En su lugar, los secretos deben estar disponibles en el entorno de producción a través de un medio controlado como variables de entorno, Azure Key Vault, etc. Puede almacenar y proteger los secretos de prueba y producción de Azure con el proveedor de configuración de [Azure Key Vault.](xref:security/key-vault-configuration)

## <a name="environment-variables"></a>Variables de entorno

Las variables de entorno se usan para evitar el almacenamiento de secretos de aplicación en el código o en archivos de configuración local. Las variables de entorno invalidan los valores de configuración de todos los orígenes de configuración especificados anteriormente.

Considere una aplicación web ASP.NET Core en la que está habilitada la seguridad de cuentas de **usuario individuales.** Se incluye una cadena de conexión de base de datos `DefaultConnection`predeterminada en el archivo *appsettings.json* del proyecto con la clave . La cadena de conexión predeterminada es para LocalDB, que se ejecuta en modo de usuario y no requiere una contraseña. Durante la implementación de la aplicación, el `DefaultConnection` valor de clave se puede invalidar con el valor de una variable de entorno. La variable de entorno puede almacenar la cadena de conexión completa con credenciales confidenciales.

> [!WARNING]
> Las variables de entorno generalmente se almacenan en texto sin cifrar. Si la máquina o el proceso se ven comprometidos, las partes que no son de confianza pueden acceder a las variables de entorno. Es posible que se requieran medidas adicionales para evitar la divulgación de secretos de usuario.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a>Administrador de secretos

La herramienta Secret Manager almacena datos confidenciales durante el desarrollo de un proyecto ASP.NET Core. En este contexto, un fragmento de datos confidenciales es un secreto de aplicación. Los secretos de aplicación se almacenan en una ubicación independiente del árbol del proyecto. Los secretos de la aplicación se asocian a un proyecto específico o se comparten entre varios proyectos. Los secretos de la aplicación no se protegen en el control de código fuente.

> [!WARNING]
> La herramienta Secret Manager no cifra los secretos almacenados y no debe tratarse como un almacén de confianza. Es sólo para fines de desarrollo. Las claves y los valores se almacenan en un archivo de configuración JSON en el directorio de perfil de usuario.

## <a name="how-the-secret-manager-tool-works"></a>Cómo funciona la herramienta Secret Manager

La herramienta Secret Manager abstrae los detalles de implementación, como dónde y cómo se almacenan los valores. Puede utilizar la herramienta sin conocer estos detalles de implementación. Los valores se almacenan en un archivo de configuración JSON en una carpeta de perfil de usuario protegida por el sistema en el equipo local:

# <a name="windows"></a>[Windows](#tab/windows)

Ruta del sistema de archivos:

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[Linux / macOS](#tab/linux+macos)

Ruta del sistema de archivos:

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

En las rutas de `<user_secrets_id>` acceso `UserSecretsId` de archivo anteriores, sustitúyala por el valor especificado en el archivo *.csproj.*

No escriba código que dependa de la ubicación o el formato de los datos guardados con la herramienta Secret Manager. Estos detalles de implementación pueden cambiar. Por ejemplo, los valores secretos no se cifran, pero podrían estar en el futuro.

## <a name="enable-secret-storage"></a>Habilitar el almacenamiento secreto

La herramienta Secret Manager funciona en los valores de configuración específicos del proyecto almacenados en su perfil de usuario.

Para usar secretos `UserSecretsId` de usuario, defina un elemento dentro de un `PropertyGroup` archivo *.csproj.* El texto `UserSecretsId` interno de es arbitrario, pero es único para el proyecto. Los desarrolladores suelen `UserSecretsId`generar un GUID para el archivo .

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

> [!TIP]
> En Visual Studio, haga clic con el botón secundario en el proyecto en el Explorador de soluciones y seleccione **Administrar secretos** de usuario en el menú contextual. Este gesto `UserSecretsId` agrega un elemento, rellenado con un GUID, al archivo *.csproj.*

## <a name="set-a-secret"></a>Establecer un secreto

Defina un secreto de aplicación que consta de una clave y su valor. El secreto está asociado con `UserSecretsId` el valor del proyecto. Por ejemplo, ejecute el siguiente comando desde el directorio en el que existe el archivo *.csproj:*

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

En el ejemplo anterior, los `Movies` dos puntos denota que es un literal de objeto con una `ServiceApiKey` propiedad.

La herramienta Secret Manager también se puede utilizar desde otros directorios. Utilice `--project` la opción para proporcionar la ruta de acceso del sistema de archivos en la que existe el archivo *.csproj.* Por ejemplo:

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a>Aplanamiento de estructura JSON en Visual Studio

El gesto **Administrar secretos** de usuario de Visual Studio abre un archivo *secrets.json* en el editor de texto. Reemplace el contenido de *secrets.json* por los pares clave-valor que se van a almacenar. Por ejemplo:

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

La estructura JSON se acopla después de las modificaciones a través `dotnet user-secrets remove` o `dotnet user-secrets set`. Por ejemplo, `dotnet user-secrets remove "Movies:ConnectionString"` la `Movies` ejecución contrae el literal del objeto. El archivo modificado es similar al siguiente:

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a>Establecer varios secretos

Un lote de secretos se puede `set` establecer canalizando JSON al comando. En el ejemplo siguiente, el contenido del archivo *input.json* se canaliza al `set` comando.

# <a name="windows"></a>[Windows](#tab/windows)

Abra un shell de comandos y ejecute el siguiente comando:

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[Linux / macOS](#tab/linux+macos)

Abra un shell de comandos y ejecute el siguiente comando:

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a>Acceder a un secreto

La API de [configuración de ASP.NET Core](xref:fundamentals/configuration/index) proporciona acceso a secretos de Secret Manager.

Si el proyecto tiene como destino .NET Framework, instale el paquete NuGet [Microsoft.Extensions.Configuration.UserSecrets.](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets)

En ASP.NET Core 2.0 o posterior, el origen de configuración de <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> secretos de usuario se agrega automáticamente en modo de desarrollo cuando el proyecto llama para inicializar una nueva instancia del host con valores predeterminados preconfigurados. `CreateDefaultBuilder`llamadas <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> cuando <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>el es :

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]

Cuando `CreateDefaultBuilder` no se llama, agregue explícitamente el <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> origen `Startup` de configuración de secretos de usuario llamando al constructor. Llame `AddUserSecrets` solo cuando la aplicación se ejecute en el entorno de desarrollo, como se muestra en el ejemplo siguiente:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_StartupConstructor&highlight=12)]

Los secretos de usuario `Configuration` se pueden recuperar a través de la API:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a>Mapa de secretos a un POCO

Asignar un literal de objeto completo a un POCO (una clase .NET simple con propiedades) es útil para agregar propiedades relacionadas.

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Para asignar los secretos anteriores `Configuration` a un POCO, utilice la característica de enlace de gráfico de [objetos](xref:fundamentals/configuration/index#bind-to-an-object-graph) de la API. El código siguiente se `MovieSettings` enlaza a un `ServiceApiKey` POCO personalizado y tiene acceso al valor de propiedad:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

Los `Movies:ConnectionString` `Movies:ServiceApiKey` secretos y se asignan `MovieSettings`a las propiedades respectivas en:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a>Reemplazo de cuerdas con secretos

Almacenar contraseñas en texto sin formato es inseguro. Por ejemplo, una cadena de conexión de base de datos almacenada en *appsettings.json* puede incluir una contraseña para el usuario especificado:

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

Un enfoque más seguro es almacenar la contraseña como un secreto. Por ejemplo:

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

Quite `Password` el par clave-valor de la cadena de conexión en *appsettings.json*. Por ejemplo:

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

El valor del secreto se <xref:System.Data.SqlClient.SqlConnectionStringBuilder> puede establecer <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> en la propiedad de un objeto para completar la cadena de conexión:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a>Lista de los secretos

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Ejecute el siguiente comando desde el directorio en el que existe el archivo *.csproj:*

```dotnetcli
dotnet user-secrets list
```

Se mostrará lo siguiente:

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

En el ejemplo anterior, dos puntos de los nombres de clave denota la jerarquía de objetos dentro de *secrets.json*.

## <a name="remove-a-single-secret"></a>Eliminar un solo secreto

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Ejecute el siguiente comando desde el directorio en el que existe el archivo *.csproj:*

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

El archivo *secrets.json* de la aplicación se modificó para `MoviesConnectionString` quitar el par clave-valor asociado a la clave:

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

En `dotnet user-secrets list` ejecución se muestra el siguiente mensaje:

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a>Eliminar todos los secretos

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Ejecute el siguiente comando desde el directorio en el que existe el archivo *.csproj:*

```dotnetcli
dotnet user-secrets clear
```

Todos los secretos de usuario de la aplicación se han eliminado del archivo *secrets.json:*

```json
{}
```

En `dotnet user-secrets list` ejecución se muestra el siguiente mensaje:

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a>Recursos adicionales

* Consulte [este problema](https://github.com/dotnet/AspNetCore.Docs/issues/16328) para obtener información sobre cómo obtener información sobre el acceso a Secret Manager desde IIS.
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end