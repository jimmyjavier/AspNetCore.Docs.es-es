---
title: Proveedor de configuración de Azure Key Vault en ASP.NET Core
author: rick-anderson
description: Aprenda a usar el proveedor de configuración de Azure Key Vault para configurar una aplicación mediante pares nombre-valor cargados en tiempo de ejecución.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: security/key-vault-configuration
ms.openlocfilehash: d78584eaa3fcd50425698e4db581060b6ca845f8
ms.sourcegitcommit: fbdb8b9ab5a52656384b117ff6e7c92ae070813c
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/13/2020
ms.locfileid: "81228171"
---
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a>Proveedor de configuración de Azure Key Vault en ASP.NET Core

Por [Andrew Stanton-Nurse](https://github.com/anurse)

::: moniker range=">= aspnetcore-3.0"

En este documento se explica cómo usar el proveedor de configuración de [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) para cargar valores de configuración de aplicaciones desde secretos de Azure Key Vault. Azure Key Vault es un servicio basado en la nube que ayuda a proteger las claves criptográficas y los secretos utilizados por las aplicaciones y los servicios. Los escenarios comunes para usar Azure Key Vault con aplicaciones ASP.NET Core incluyen:

* Controlar el acceso a datos de configuración confidenciales.
* Cumplir con el requisito de módulos de seguridad de hardware (HSM) validados por FIPS 140-2 nivel 2 al almacenar datos de configuración.

[Ver o descargar código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ( cómo[descargar](xref:index#how-to-download-a-sample))

## <a name="packages"></a>Paquetes

Agregue una referencia de paquete al paquete [Microsoft.Extensions.Configuration.AzureKeyVault.](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/)

## <a name="sample-app"></a>Aplicación de ejemplo

La aplicación de ejemplo se ejecuta `#define` en cualquiera de los dos modos determinados por la instrucción en la parte superior del archivo *Program.cs:*

* `Certificate`&ndash; Muestra el uso de un identificador de cliente de Azure Key Vault y un certificado X.509 para tener acceso a los secretos almacenados en Azure Key Vault. Esta versión del ejemplo se puede ejecutar desde cualquier ubicación, implementada en Azure App Service o en cualquier host capaz de servir una aplicación ASP.NET Core.
* `Managed`&ndash; Muestra cómo usar [identidades administradas para recursos](/azure/active-directory/managed-identities-azure-resources/overview) de Azure para autenticar la aplicación en Azure Key Vault con autenticación de Azure AD sin credenciales almacenadas en el código o la configuración de la aplicación. Cuando se usan identidades administradas para autenticarse, no se requieren un identificador de aplicación de Azure AD ni una contraseña (secreto de cliente). La `Managed` versión del ejemplo debe implementarse en Azure.The version of the sample must be implement to Azure. Siga las instrucciones de la sección [Usar las identidades administradas para recursos](#use-managed-identities-for-azure-resources) de Azure.

Para obtener más información sobre cómo configurar una`#define`aplicación <xref:index#preprocessor-directives-in-sample-code>de ejemplo mediante directivas de preprocesador ( ), consulte .

## <a name="secret-storage-in-the-development-environment"></a>Almacenamiento secreto en el entorno de desarrollo

Establezca secretos localmente con la [herramienta Secret Manager](xref:security/app-secrets). Cuando la aplicación de ejemplo se ejecuta en el equipo local en el entorno de desarrollo, los secretos se cargan desde el almacén de Secret Manager local.

La herramienta Secret Manager `<UserSecretsId>` requiere una propiedad en el archivo de proyecto de la aplicación. Establezca el valor`{GUID}`de propiedad ( ) en cualquier GUID único:

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

Los secretos se crean como pares nombre-valor. Los valores jerárquicos (secciones de configuración) utilizan un `:` (colon) como separador en los nombres de clave de configuración de ASP.NET [Core.](xref:fundamentals/configuration/index)

El Administrador secreto se utiliza desde un shell de comandos abierto `{SECRET VALUE}` a la raíz de [contenido](xref:fundamentals/index#content-root)del proyecto, donde `{SECRET NAME}` está el nombre y es el valor:

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

Ejecute los siguientes comandos en un shell de comandos desde la raíz de [contenido](xref:fundamentals/index#content-root) del proyecto para establecer los secretos de la aplicación de ejemplo:

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

Cuando estos secretos se almacenan en Azure Key Vault en el almacenamiento `_dev` secreto del `_prod`entorno de producción con la sección Azure [Key Vault,](#secret-storage-in-the-production-environment-with-azure-key-vault) el sufijo se cambia a . El sufijo proporciona una indicación visual en la salida de la aplicación que indica el origen de los valores de configuración.

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a>Almacenamiento secreto en el entorno de producción con Azure Key Vault

Las instrucciones proporcionadas por el tema [Inicio rápido: establecer y recuperar un secreto de Azure Key Vault mediante](/azure/key-vault/quick-create-cli) la CLI de Azure se resumen aquí para crear un almacén de claves de Azure y almacenar los secretos que usa la aplicación de ejemplo. Consulte el tema para obtener más detalles.

1. Abra Azure Cloud shell con cualquiera de los métodos siguientes en [Azure Portal:](https://portal.azure.com/)

   * Seleccione **Probar** en la esquina superior derecha de un bloque de código. Use la cadena de búsqueda "CLI de Azure" en el cuadro de texto.
   * Abra Cloud Shell en su navegador con el botón **Iniciar Cloud Shell.**
   * Seleccione el botón **Cloud Shell** en el menú en la esquina superior derecha de Azure Portal.

   Para obtener más información, consulte CLI de [Azure](/cli/azure/) y [Información general sobre Azure Cloud Shell](/azure/cloud-shell/overview).

1. Si aún no está autenticado, inicie `az login` sesión con el comando.

1. Cree un grupo de recursos `{RESOURCE GROUP NAME}` con el siguiente comando, donde `{LOCATION}` está el nombre del grupo de recursos para el nuevo grupo de recursos y es la región de Azure (centro de datos):

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. Cree un almacén de claves en el `{KEY VAULT NAME}` grupo de recursos con `{LOCATION}` el siguiente comando, donde está el nombre del nuevo almacén de claves y es la región de Azure (centro de datos):

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. Cree secretos en el almacén de claves como pares nombre-valor.

   Los nombres de secreto de Azure Key Vault se limitan a caracteres alfanuméricos y guiones. Los valores jerárquicos (secciones de configuración) utilizan `--` (dos guiones) como separador. Los dos puntos, que normalmente se utilizan para delimitar una sección de una subclave en [ASP.NET configuración de Core](xref:fundamentals/configuration/index), no se permiten en los nombres secretos del almacén de claves. Por lo tanto, se usan dos guiones y se intercambian por dos puntos cuando los secretos se cargan en la configuración de la aplicación.

   Los siguientes secretos se usan con la aplicación de ejemplo. Los valores `_prod` incluyen un sufijo `_dev` para distinguirlos de los valores de sufijo cargados en el entorno de desarrollo de los secretos de usuario. Reemplace `{KEY VAULT NAME}` por el nombre del almacén de claves que creó en el paso anterior:

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a>Usar el identificador de aplicación y el certificado X.509 para aplicaciones no hospedadas en Azure

Configure Azure AD, Azure Key Vault y la aplicación para que usen un identificador de aplicación de Azure Active Directory y un certificado X.509 para autenticarse en un almacén de claves **cuando la aplicación se hospeda fuera de Azure.** Para más información, consulte el artículo [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates) (Claves, secretos y certificados).

> [!NOTE]
> Aunque se admite el uso de un identificador de aplicación y un certificado X.509 para aplicaciones hospedadas en Azure, se recomienda usar [identidades administradas para recursos](#use-managed-identities-for-azure-resources) de Azure al hospedar una aplicación en Azure. Las identidades administradas no requieren almacenar un certificado en la aplicación ni en el entorno de desarrollo.

La aplicación de ejemplo usa un identificador de `#define` aplicación y un certificado X.509 cuando la instrucción en la parte superior del archivo *de Program.cs* se establece en `Certificate`.

1. Cree un certificado de archivo PKCS-12 (*.pfx*). Las opciones para crear certificados incluyen [MakeCert en Windows](/windows/desktop/seccrypto/makecert) y [OpenSSL](https://www.openssl.org/).
1. Instale el certificado en el almacén de certificados personal del usuario actual. Marcar la clave como exportable es opcional. Observe la huella digital del certificado, que se utiliza más adelante en este proceso.
1. Exporte el certificado de archivo PKCS-12 (*.pfx*) como certificado codificado con DER (*.cer*).
1. Registre la aplicación con Azure AD (**Registros**de aplicaciones ).
1. Cargue el certificado con codificación DER (*.cer*) en Azure AD:
   1. Seleccione la aplicación en Azure AD.
   1. Vaya a **Certificados & secretos.**
   1. Seleccione **Cargar certificado** para cargar el certificado, que contiene la clave pública. Un certificado *.cer*, *.pem*o *.crt* es aceptable.
1. Almacene el nombre del almacén de claves, el identificador de aplicación y la huella digital del certificado en el archivo *appsettings.json* de la aplicación.
1. Vaya a Almacenes de claves en Azure Portal.Navigate a Almacenes de claves en Azure Portal.Navigate a **Almacenes** de claves en Azure Portal.Navigate to
1. Seleccione el almacén de claves que creó en la sección [Almacenamiento secreto en el entorno de producción con Azure Key Vault.](#secret-storage-in-the-production-environment-with-azure-key-vault)
1. Seleccione **Directivas**de acceso .
1. Seleccione **Agregar directiva de acceso**.
1. Abra **Permisos secretos** y proporcione a la aplicación permisos **Obtener** y **Lista.**
1. Seleccione **Seleccionar entidad de seguridad** y seleccione la aplicación registrada por nombre. Seleccione el botón **Seleccionar**.
1. Seleccione **Aceptar**.
1. Seleccione **Guardar**.
1. Implemente la aplicación.

La `Certificate` aplicación de ejemplo obtiene `IConfigurationRoot` sus valores de configuración con el mismo nombre que el nombre secreto:

* Valores no jerárquicos: el valor para `SecretName` se obtiene con `config["SecretName"]`.
* Valores jerárquicos (secciones): utilice `:` la notación (colon) o el `GetSection` método de extensión. Utilice cualquiera de estos enfoques para obtener el valor de configuración:
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

El certificado X.509 es administrado por el sistema operativo. La aplicación <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> llama con los valores proporcionados por el archivo *appsettings.json:*

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

Valores de ejemplo:

* Nombre del almacén de claves:`contosovault`
* ID de aplicación:`627e911e-43cc-61d4-992e-12db9c81b413`
* Huella digital del certificado:`fe14593dd66b2406c5269d742d04b6e1ab03adb1`

*appsettings.json*:

[!code-json[](key-vault-configuration/samples/3.x/SampleApp/appsettings.json?highlight=10-12)]

Al ejecutar la aplicación, una página web muestra los valores secretos cargados. En el entorno de desarrollo, `_dev` los valores secretos se cargan con el sufijo. En el entorno de producción, `_prod` los valores se cargan con el sufijo.

## <a name="use-managed-identities-for-azure-resources"></a>Usar identidades administradas para recursos de Azure

**Una aplicación implementada en Azure** puede aprovechar las [identidades administradas para los recursos](/azure/active-directory/managed-identities-azure-resources/overview)de Azure, lo que permite que la aplicación se autentique con Azure Key Vault mediante la autenticación de Azure AD sin credenciales (identificador de aplicación y contraseña/secreto de cliente) almacenadaen en la aplicación.

La aplicación de ejemplo usa identidades `#define` administradas para recursos de Azure `Managed`cuando la instrucción en la parte superior del archivo *Program.cs* se establece en .

Escriba el nombre del almacén en el archivo *appsettings.json* de la aplicación. La aplicación de ejemplo no requiere un identificador de aplicación `Managed` y una contraseña (secreto de cliente) cuando se establece en la versión, por lo que puede omitir esas entradas de configuración. La aplicación se implementa en Azure y Azure autentica la aplicación para tener acceso a Azure Key Vault solo con el nombre del almacén almacenado en el archivo *appsettings.json.*

Implemente la aplicación de ejemplo en Azure App Service.

Una aplicación implementada en Azure App Service se registra automáticamente con Azure AD cuando se crea el servicio. Obtenga el identificador de objeto de la implementación para su uso en el siguiente comando. El identificador de objeto se muestra en Azure Portal en el panel **Identidad** de App Service.

Con la CLI de Azure y el identificador `list` `get` de objeto de la aplicación, proporcione a la aplicación y permisos para tener acceso al almacén de claves:

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

**Reinicie la aplicación** con la CLI de Azure, PowerShell o Azure Portal.

La aplicación de ejemplo:

* Crea una instancia `AzureServiceTokenProvider` de la clase sin una cadena de conexión. Cuando no se proporciona una cadena de conexión, el proveedor intenta obtener un token de acceso de identidades administradas para recursos de Azure.
* Se <xref:Microsoft.Azure.KeyVault.KeyVaultClient> crea un `AzureServiceTokenProvider` nuevo con la devolución de llamada del token de instancia.
* La <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instancia se utiliza con <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> una implementación predeterminada que carga todos`--`los valores`:`secretos y reemplaza double-dashes ( ) con dos puntos ( ) en los nombres de clave.

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

Valor de ejemplo del nombre del almacén de claves:`contosovault`
    
*appsettings.json*:

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

Al ejecutar la aplicación, una página web muestra los valores secretos cargados. En el entorno de desarrollo, los valores secretos tienen el `_dev` sufijo porque los proporcionan los secretos de usuario. En el entorno de producción, `_prod` los valores se cargan con el sufijo porque Azure Key Vault los proporciona.

Si recibe `Access denied` un error, confirme que la aplicación está registrada en Azure AD y que proporciona acceso al almacén de claves. Confirme que ha reiniciado el servicio en Azure.Confirme que ha reiniciado el servicio en Azure.Confirm que ha reiniciado el servicio en Azure.Confirm que ha reiniciado el servicio en

Para obtener información sobre el uso del proveedor con una identidad administrada y una canalización de VSTS, consulte Creación de una conexión de servicio de Azure Resource Manager a una máquina virtual con una identidad de [servicio administrada.](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity)

## <a name="configuration-options"></a>Opciones de configuración

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>puede aceptar <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions>un :

```csharp
config.AddAzureKeyVault(
    new AzureKeyVaultConfigurationOptions()
    {
        ...
    });
```

| Propiedad         | Descripción |
| ---------------- | ----------- |
| `Client`         | <xref:Microsoft.Azure.KeyVault.KeyVaultClient>para recuperar valores. |
| `Manager`        | <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>instancia utilizada para controlar la carga secreta. |
| `ReloadInterval` | `Timespan`para esperar entre los intentos de sondear el almacén de claves en busca de cambios. El valor `null` predeterminado es (la configuración no se vuelve a cargar). |
| `Vault`          | URI del almacén de claves. |

## <a name="use-a-key-name-prefix"></a>Utilice un prefijo de nombre de clave

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>proporciona una sobrecarga que acepta <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>una implementación de , lo que le permite controlar cómo se convierten los secretos del almacén de claves en claves de configuración. Por ejemplo, puede implementar la interfaz para cargar valores secretos basados en un valor de prefijo que proporcione al iniciar la aplicación. Esto le permite, por ejemplo, cargar secretos basados en la versión de la aplicación.

> [!WARNING]
> No use prefijos en los secretos del almacén de claves para colocar secretos para varias aplicaciones en el mismo almacén de claves o para colocar secretos de entorno (por ejemplo, *el desarrollo* frente a los secretos de *producción)* en el mismo almacén. Se recomienda que las diferentes aplicaciones y entornos de desarrollo/producción usen almacenes de claves independientes para aislar los entornos de aplicaciones para obtener el nivel más alto de seguridad.

En el ejemplo siguiente, se establece un secreto en el almacén de `5000-AppSecret` claves (y mediante la herramienta Secret Manager para el entorno de desarrollo) para (no se permiten períodos en los nombres de secreto del almacén de claves). Este secreto representa un secreto de aplicación para la versión 5.0.0.0 de la aplicación. Para otra versión de la aplicación, 5.1.0.0, se agrega un secreto al `5100-AppSecret`almacén de claves (y mediante la herramienta Secret Manager) para . Cada versión de la aplicación carga `AppSecret`su valor secreto versionado en su configuración como , eliminando la versión a medida que carga el secreto.

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>se llama con <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>una costumbre:

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

La <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementación reacciona a los prefijos de versión de los secretos para cargar el secreto adecuado en la configuración:

* `Load`carga un secreto cuando su nombre comienza con el prefijo. Otros secretos no están cargados.
* `GetKey`:
  * Quita el prefijo del nombre secreto.
  * Reemplaza dos guiones en cualquier `KeyDelimiter`nombre con el , que es el delimitador utilizado en la configuración (normalmente dos puntos). Azure Key Vault no permite dos puntos en nombres secretos.

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

El `Load` método es llamado por un algoritmo de proveedor que recorre en iteración los secretos del almacén para encontrar los que tienen el prefijo de versión. Cuando se encuentra un `Load`prefijo de `GetKey` versión con , el algoritmo utiliza el método para devolver el nombre de configuración del nombre secreto. Elimina el prefijo de versión del nombre del secreto y devuelve el resto del nombre secreto para cargarlo en los pares nombre-valor de configuración de la aplicación.

Cuando se implementa este enfoque:

1. La versión de la aplicación especificada en el archivo de proyecto de la aplicación. En el ejemplo siguiente, la versión `5.0.0.0`de la aplicación se establece en:

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. Confirme que `<UserSecretsId>` una propiedad está presente en el `{GUID}` archivo de proyecto de la aplicación, donde hay un GUID proporcionado por el usuario:

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   Guarde los siguientes secretos localmente con la [herramienta Secret Manager:](xref:security/app-secrets)

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. Los secretos se guardan en Azure Key Vault mediante los siguientes comandos de la CLI de Azure:

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. Cuando se ejecuta la aplicación, se cargan los secretos del almacén de claves. El secreto `5000-AppSecret` de cadena para coincide con la versión de la`5.0.0.0`aplicación especificada en el archivo de proyecto de la aplicación ( ).

1. La versión, `5000` (con el guión), se elimina del nombre de clave. En toda la aplicación, la `AppSecret` lectura de la configuración con la clave carga el valor secreto.

1. Si se cambia la versión de `5.1.0.0` la aplicación en el archivo de `5.1.0.0_secret_value_dev` proyecto y se `5.1.0.0_secret_value_prod` vuelve a ejecutar la aplicación, el valor secreto devuelto es en el entorno de desarrollo y en producción.

> [!NOTE]
> También puede proporcionar <xref:Microsoft.Azure.KeyVault.KeyVaultClient> su <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>propia implementación a . Un cliente personalizado permite compartir una única instancia del cliente en la aplicación.

## <a name="bind-an-array-to-a-class"></a>Enlace de una matriz a una clase

El proveedor es capaz de leer valores de configuración en una matriz para enlazar a una matriz POCO.

Al leer desde un origen de configuración`:`que permite que las claves contengan separadores de dos`:0:` `:1:`puntos ( ), se utiliza un segmento de clave numérica para distinguir las claves que componen una matriz ( , , &hellip; `:{n}:`). Para obtener más información, consulte [Configuración: enlazar una matriz a una clase](xref:fundamentals/configuration/index#bind-an-array-to-a-class).

Las claves de Azure Key Vault no pueden usar dos puntos como separador. El enfoque descrito en este tema`--`utiliza guiones dobles ( ) como separador para valores jerárquicos (secciones). Las claves de matriz se almacenan en Azure Key`--0--` `--1--`Vault &hellip; `--{n}--`con guiones dobles y segmentos de clave numérica ( , , ).

Examine la siguiente configuración del proveedor de registro [de Serilog](https://serilog.net/) proporcionada por un archivo JSON. Hay dos literales de `WriteTo` objeto definidos en la matriz que reflejan dos *receptores*serilog, que describen los destinos para la salida de registro:

```json
"Serilog": {
  "WriteTo": [
    {
      "Name": "AzureTableStorage",
      "Args": {
        "storageTableName": "logs",
        "connectionString": "DefaultEnd...ountKey=Eby8...GMGw=="
      }
    },
    {
      "Name": "AzureDocumentDB",
      "Args": {
        "endpointUrl": "https://contoso.documents.azure.com:443",
        "authorizationKey": "Eby8...GMGw=="
      }
    }
  ]
}
```

La configuración que se muestra en el archivo JSON anterior`--`se almacena en Azure Key Vault mediante la notación de doble guión ( ) y segmentos numéricos:

| Clave | Value |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a>Recargar secretos

Los secretos `IConfigurationRoot.Reload()` se almacenan en caché hasta que se llama. La aplicación no respeta los secretos caducados, deshabilitados y actualizados en el almacén de claves hasta que `Reload` se ejecute.

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a>Secretos discapacitados y caducados

Los secretos deshabilitados y caducados lanzan un <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>archivo . Para evitar que la aplicación se inicie, proporcione la configuración con un proveedor de configuración diferente o actualice el secreto deshabilitado o caducado.

## <a name="troubleshoot"></a>Solución de problemas

Cuando la aplicación no puede cargar la configuración mediante el proveedor, se escribe un mensaje de error en la infraestructura de [registro de núcleo de ASP.NET.](xref:fundamentals/logging/index) Las siguientes condiciones impedirán que se cargue la configuración:

* La aplicación o el certificado no están configurados correctamente en Azure Active Directory.
* El almacén de claves no existe en Azure Key Vault.
* La aplicación no está autorizada para acceder al almacén de claves.
* La política de acceso `Get` `List` no incluye ni los permisos.
* En el almacén de claves, los datos de configuración (par nombre-valor) se nombran incorrectamente, faltan, se deshabilitan o caducan.
* La aplicación tiene el nombre`KeyVaultName`de almacén`AzureADApplicationId`de claves incorrecto (`AzureADCertThumbprint`), el identificador de aplicación de Azure AD ( ) o la huella digital del certificado de Azure AD ( ).
* La clave de configuración (nombre) es incorrecta en la aplicación para el valor que está intentando cargar.
* Al agregar la directiva de acceso de la aplicación al almacén de claves, se creó la directiva, pero el botón **Guardar** no se seleccionó en la interfaz de usuario **de directivas** de acceso.

## <a name="additional-resources"></a>Recursos adicionales

* <xref:fundamentals/configuration/index>
* [Microsoft Azure: Key Vault](https://azure.microsoft.com/services/key-vault/)
* [Documentación de Microsoft Azure: Key Vault](/azure/key-vault/)
* [Generación y transferencia de claves protegidas con HSM para Azure Key Vault](/azure/key-vault/key-vault-hsm-protected-keys)
* [Clase KeyVaultClient](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [Guía de inicio rápido: Establecimiento y recuperación de un secreto de Azure Key Vault mediante una aplicación web de .NET](/azure/key-vault/quick-create-net)
* [Tutorial: Uso de Azure Key Vault con una máquina virtual Windows en .NET](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

En este documento se explica cómo usar el proveedor de configuración de [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) para cargar valores de configuración de aplicaciones desde secretos de Azure Key Vault. Azure Key Vault es un servicio basado en la nube que ayuda a proteger las claves criptográficas y los secretos utilizados por las aplicaciones y los servicios. Los escenarios comunes para usar Azure Key Vault con aplicaciones ASP.NET Core incluyen:

* Controlar el acceso a datos de configuración confidenciales.
* Cumplir con el requisito de módulos de seguridad de hardware (HSM) validados por FIPS 140-2 nivel 2 al almacenar datos de configuración.

[Ver o descargar código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ( cómo[descargar](xref:index#how-to-download-a-sample))

## <a name="packages"></a>Paquetes

Agregue una referencia de paquete al paquete [Microsoft.Extensions.Configuration.AzureKeyVault.](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/)

## <a name="sample-app"></a>Aplicación de ejemplo

La aplicación de ejemplo se ejecuta `#define` en cualquiera de los dos modos determinados por la instrucción en la parte superior del archivo *Program.cs:*

* `Certificate`&ndash; Muestra el uso de un identificador de cliente de Azure Key Vault y un certificado X.509 para tener acceso a los secretos almacenados en Azure Key Vault. Esta versión del ejemplo se puede ejecutar desde cualquier ubicación, implementada en Azure App Service o en cualquier host capaz de servir una aplicación ASP.NET Core.
* `Managed`&ndash; Muestra cómo usar [identidades administradas para recursos](/azure/active-directory/managed-identities-azure-resources/overview) de Azure para autenticar la aplicación en Azure Key Vault con autenticación de Azure AD sin credenciales almacenadas en el código o la configuración de la aplicación. Cuando se usan identidades administradas para autenticarse, no se requieren un identificador de aplicación de Azure AD ni una contraseña (secreto de cliente). La `Managed` versión del ejemplo debe implementarse en Azure.The version of the sample must be implement to Azure. Siga las instrucciones de la sección [Usar las identidades administradas para recursos](#use-managed-identities-for-azure-resources) de Azure.

Para obtener más información sobre cómo configurar una`#define`aplicación <xref:index#preprocessor-directives-in-sample-code>de ejemplo mediante directivas de preprocesador ( ), consulte .

## <a name="secret-storage-in-the-development-environment"></a>Almacenamiento secreto en el entorno de desarrollo

Establezca secretos localmente con la [herramienta Secret Manager](xref:security/app-secrets). Cuando la aplicación de ejemplo se ejecuta en el equipo local en el entorno de desarrollo, los secretos se cargan desde el almacén de Secret Manager local.

La herramienta Secret Manager `<UserSecretsId>` requiere una propiedad en el archivo de proyecto de la aplicación. Establezca el valor`{GUID}`de propiedad ( ) en cualquier GUID único:

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

Los secretos se crean como pares nombre-valor. Los valores jerárquicos (secciones de configuración) utilizan un `:` (colon) como separador en los nombres de clave de configuración de ASP.NET [Core.](xref:fundamentals/configuration/index)

El Administrador secreto se utiliza desde un shell de comandos abierto `{SECRET VALUE}` a la raíz de [contenido](xref:fundamentals/index#content-root)del proyecto, donde `{SECRET NAME}` está el nombre y es el valor:

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

Ejecute los siguientes comandos en un shell de comandos desde la raíz de [contenido](xref:fundamentals/index#content-root) del proyecto para establecer los secretos de la aplicación de ejemplo:

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

Cuando estos secretos se almacenan en Azure Key Vault en el almacenamiento `_dev` secreto del `_prod`entorno de producción con la sección Azure [Key Vault,](#secret-storage-in-the-production-environment-with-azure-key-vault) el sufijo se cambia a . El sufijo proporciona una indicación visual en la salida de la aplicación que indica el origen de los valores de configuración.

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a>Almacenamiento secreto en el entorno de producción con Azure Key Vault

Las instrucciones proporcionadas por el tema [Inicio rápido: establecer y recuperar un secreto de Azure Key Vault mediante](/azure/key-vault/quick-create-cli) la CLI de Azure se resumen aquí para crear un almacén de claves de Azure y almacenar los secretos que usa la aplicación de ejemplo. Consulte el tema para obtener más detalles.

1. Abra Azure Cloud shell con cualquiera de los métodos siguientes en [Azure Portal:](https://portal.azure.com/)

   * Seleccione **Probar** en la esquina superior derecha de un bloque de código. Use la cadena de búsqueda "CLI de Azure" en el cuadro de texto.
   * Abra Cloud Shell en su navegador con el botón **Iniciar Cloud Shell.**
   * Seleccione el botón **Cloud Shell** en el menú en la esquina superior derecha de Azure Portal.

   Para obtener más información, consulte CLI de [Azure](/cli/azure/) y [Información general sobre Azure Cloud Shell](/azure/cloud-shell/overview).

1. Si aún no está autenticado, inicie `az login` sesión con el comando.

1. Cree un grupo de recursos `{RESOURCE GROUP NAME}` con el siguiente comando, donde `{LOCATION}` está el nombre del grupo de recursos para el nuevo grupo de recursos y es la región de Azure (centro de datos):

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. Cree un almacén de claves en el `{KEY VAULT NAME}` grupo de recursos con `{LOCATION}` el siguiente comando, donde está el nombre del nuevo almacén de claves y es la región de Azure (centro de datos):

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. Cree secretos en el almacén de claves como pares nombre-valor.

   Los nombres de secreto de Azure Key Vault se limitan a caracteres alfanuméricos y guiones. Los valores jerárquicos (secciones de configuración) utilizan `--` (dos guiones) como separador. Los dos puntos, que normalmente se utilizan para delimitar una sección de una subclave en [ASP.NET configuración de Core](xref:fundamentals/configuration/index), no se permiten en los nombres secretos del almacén de claves. Por lo tanto, se usan dos guiones y se intercambian por dos puntos cuando los secretos se cargan en la configuración de la aplicación.

   Los siguientes secretos se usan con la aplicación de ejemplo. Los valores `_prod` incluyen un sufijo `_dev` para distinguirlos de los valores de sufijo cargados en el entorno de desarrollo de los secretos de usuario. Reemplace `{KEY VAULT NAME}` por el nombre del almacén de claves que creó en el paso anterior:

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a>Usar el identificador de aplicación y el certificado X.509 para aplicaciones no hospedadas en Azure

Configure Azure AD, Azure Key Vault y la aplicación para que usen un identificador de aplicación de Azure Active Directory y un certificado X.509 para autenticarse en un almacén de claves **cuando la aplicación se hospeda fuera de Azure.** Para más información, consulte el artículo [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates) (Claves, secretos y certificados).

> [!NOTE]
> Aunque se admite el uso de un identificador de aplicación y un certificado X.509 para aplicaciones hospedadas en Azure, se recomienda usar [identidades administradas para recursos](#use-managed-identities-for-azure-resources) de Azure al hospedar una aplicación en Azure. Las identidades administradas no requieren almacenar un certificado en la aplicación ni en el entorno de desarrollo.

La aplicación de ejemplo usa un identificador de `#define` aplicación y un certificado X.509 cuando la instrucción en la parte superior del archivo *de Program.cs* se establece en `Certificate`.

1. Cree un certificado de archivo PKCS-12 (*.pfx*). Las opciones para crear certificados incluyen [MakeCert en Windows](/windows/desktop/seccrypto/makecert) y [OpenSSL](https://www.openssl.org/).
1. Instale el certificado en el almacén de certificados personal del usuario actual. Marcar la clave como exportable es opcional. Observe la huella digital del certificado, que se utiliza más adelante en este proceso.
1. Exporte el certificado de archivo PKCS-12 (*.pfx*) como certificado codificado con DER (*.cer*).
1. Registre la aplicación con Azure AD (**Registros**de aplicaciones ).
1. Cargue el certificado con codificación DER (*.cer*) en Azure AD:
   1. Seleccione la aplicación en Azure AD.
   1. Vaya a **Certificados & secretos.**
   1. Seleccione **Cargar certificado** para cargar el certificado, que contiene la clave pública. Un certificado *.cer*, *.pem*o *.crt* es aceptable.
1. Almacene el nombre del almacén de claves, el identificador de aplicación y la huella digital del certificado en el archivo *appsettings.json* de la aplicación.
1. Vaya a Almacenes de claves en Azure Portal.Navigate a Almacenes de claves en Azure Portal.Navigate a **Almacenes** de claves en Azure Portal.Navigate to
1. Seleccione el almacén de claves que creó en la sección [Almacenamiento secreto en el entorno de producción con Azure Key Vault.](#secret-storage-in-the-production-environment-with-azure-key-vault)
1. Seleccione **Directivas**de acceso .
1. Seleccione **Agregar directiva de acceso**.
1. Abra **Permisos secretos** y proporcione a la aplicación permisos **Obtener** y **Lista.**
1. Seleccione **Seleccionar entidad de seguridad** y seleccione la aplicación registrada por nombre. Seleccione el botón **Seleccionar**.
1. Seleccione **Aceptar**.
1. Seleccione **Guardar**.
1. Implemente la aplicación.

La `Certificate` aplicación de ejemplo obtiene `IConfigurationRoot` sus valores de configuración con el mismo nombre que el nombre secreto:

* Valores no jerárquicos: el valor para `SecretName` se obtiene con `config["SecretName"]`.
* Valores jerárquicos (secciones): utilice `:` la notación (colon) o el `GetSection` método de extensión. Utilice cualquiera de estos enfoques para obtener el valor de configuración:
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

El certificado X.509 es administrado por el sistema operativo. La aplicación <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> llama con los valores proporcionados por el archivo *appsettings.json:*

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

Valores de ejemplo:

* Nombre del almacén de claves:`contosovault`
* ID de aplicación:`627e911e-43cc-61d4-992e-12db9c81b413`
* Huella digital del certificado:`fe14593dd66b2406c5269d742d04b6e1ab03adb1`

*appsettings.json*:

[!code-json[](key-vault-configuration/samples/2.x/SampleApp/appsettings.json?highlight=10-12)]

Al ejecutar la aplicación, una página web muestra los valores secretos cargados. En el entorno de desarrollo, `_dev` los valores secretos se cargan con el sufijo. En el entorno de producción, `_prod` los valores se cargan con el sufijo.

## <a name="use-managed-identities-for-azure-resources"></a>Usar identidades administradas para recursos de Azure

**Una aplicación implementada en Azure** puede aprovechar las [identidades administradas para los recursos](/azure/active-directory/managed-identities-azure-resources/overview)de Azure, lo que permite que la aplicación se autentique con Azure Key Vault mediante la autenticación de Azure AD sin credenciales (identificador de aplicación y contraseña/secreto de cliente) almacenadaen en la aplicación.

La aplicación de ejemplo usa identidades `#define` administradas para recursos de Azure `Managed`cuando la instrucción en la parte superior del archivo *Program.cs* se establece en .

Escriba el nombre del almacén en el archivo *appsettings.json* de la aplicación. La aplicación de ejemplo no requiere un identificador de aplicación `Managed` y una contraseña (secreto de cliente) cuando se establece en la versión, por lo que puede omitir esas entradas de configuración. La aplicación se implementa en Azure y Azure autentica la aplicación para tener acceso a Azure Key Vault solo con el nombre del almacén almacenado en el archivo *appsettings.json.*

Implemente la aplicación de ejemplo en Azure App Service.

Una aplicación implementada en Azure App Service se registra automáticamente con Azure AD cuando se crea el servicio. Obtenga el identificador de objeto de la implementación para su uso en el siguiente comando. El identificador de objeto se muestra en Azure Portal en el panel **Identidad** de App Service.

Con la CLI de Azure y el identificador `list` `get` de objeto de la aplicación, proporcione a la aplicación y permisos para tener acceso al almacén de claves:

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

**Reinicie la aplicación** con la CLI de Azure, PowerShell o Azure Portal.

La aplicación de ejemplo:

* Crea una instancia `AzureServiceTokenProvider` de la clase sin una cadena de conexión. Cuando no se proporciona una cadena de conexión, el proveedor intenta obtener un token de acceso de identidades administradas para recursos de Azure.
* Se <xref:Microsoft.Azure.KeyVault.KeyVaultClient> crea un `AzureServiceTokenProvider` nuevo con la devolución de llamada del token de instancia.
* La <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instancia se utiliza con <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> una implementación predeterminada que carga todos`--`los valores`:`secretos y reemplaza double-dashes ( ) con dos puntos ( ) en los nombres de clave.

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

Valor de ejemplo del nombre del almacén de claves:`contosovault`
    
*appsettings.json*:

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

Al ejecutar la aplicación, una página web muestra los valores secretos cargados. En el entorno de desarrollo, los valores secretos tienen el `_dev` sufijo porque los proporcionan los secretos de usuario. En el entorno de producción, `_prod` los valores se cargan con el sufijo porque Azure Key Vault los proporciona.

Si recibe `Access denied` un error, confirme que la aplicación está registrada en Azure AD y que proporciona acceso al almacén de claves. Confirme que ha reiniciado el servicio en Azure.Confirme que ha reiniciado el servicio en Azure.Confirm que ha reiniciado el servicio en Azure.Confirm que ha reiniciado el servicio en

Para obtener información sobre el uso del proveedor con una identidad administrada y una canalización de VSTS, consulte Creación de una conexión de servicio de Azure Resource Manager a una máquina virtual con una identidad de [servicio administrada.](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity)

## <a name="use-a-key-name-prefix"></a>Utilice un prefijo de nombre de clave

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>proporciona una sobrecarga que acepta <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>una implementación de , lo que le permite controlar cómo se convierten los secretos del almacén de claves en claves de configuración. Por ejemplo, puede implementar la interfaz para cargar valores secretos basados en un valor de prefijo que proporcione al iniciar la aplicación. Esto le permite, por ejemplo, cargar secretos basados en la versión de la aplicación.

> [!WARNING]
> No use prefijos en los secretos del almacén de claves para colocar secretos para varias aplicaciones en el mismo almacén de claves o para colocar secretos de entorno (por ejemplo, *el desarrollo* frente a los secretos de *producción)* en el mismo almacén. Se recomienda que las diferentes aplicaciones y entornos de desarrollo/producción usen almacenes de claves independientes para aislar los entornos de aplicaciones para obtener el nivel más alto de seguridad.

En el ejemplo siguiente, se establece un secreto en el almacén de `5000-AppSecret` claves (y mediante la herramienta Secret Manager para el entorno de desarrollo) para (no se permiten períodos en los nombres de secreto del almacén de claves). Este secreto representa un secreto de aplicación para la versión 5.0.0.0 de la aplicación. Para otra versión de la aplicación, 5.1.0.0, se agrega un secreto al `5100-AppSecret`almacén de claves (y mediante la herramienta Secret Manager) para . Cada versión de la aplicación carga `AppSecret`su valor secreto versionado en su configuración como , eliminando la versión a medida que carga el secreto.

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>se llama con <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>una costumbre:

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

La <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementación reacciona a los prefijos de versión de los secretos para cargar el secreto adecuado en la configuración:

* `Load`carga un secreto cuando su nombre comienza con el prefijo. Otros secretos no están cargados.
* `GetKey`:
  * Quita el prefijo del nombre secreto.
  * Reemplaza dos guiones en cualquier `KeyDelimiter`nombre con el , que es el delimitador utilizado en la configuración (normalmente dos puntos). Azure Key Vault no permite dos puntos en nombres secretos.

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

El `Load` método es llamado por un algoritmo de proveedor que recorre en iteración los secretos del almacén para encontrar los que tienen el prefijo de versión. Cuando se encuentra un `Load`prefijo de `GetKey` versión con , el algoritmo utiliza el método para devolver el nombre de configuración del nombre secreto. Elimina el prefijo de versión del nombre del secreto y devuelve el resto del nombre secreto para cargarlo en los pares nombre-valor de configuración de la aplicación.

Cuando se implementa este enfoque:

1. La versión de la aplicación especificada en el archivo de proyecto de la aplicación. En el ejemplo siguiente, la versión `5.0.0.0`de la aplicación se establece en:

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. Confirme que `<UserSecretsId>` una propiedad está presente en el `{GUID}` archivo de proyecto de la aplicación, donde hay un GUID proporcionado por el usuario:

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   Guarde los siguientes secretos localmente con la [herramienta Secret Manager:](xref:security/app-secrets)

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. Los secretos se guardan en Azure Key Vault mediante los siguientes comandos de la CLI de Azure:

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. Cuando se ejecuta la aplicación, se cargan los secretos del almacén de claves. El secreto `5000-AppSecret` de cadena para coincide con la versión de la`5.0.0.0`aplicación especificada en el archivo de proyecto de la aplicación ( ).

1. La versión, `5000` (con el guión), se elimina del nombre de clave. En toda la aplicación, la `AppSecret` lectura de la configuración con la clave carga el valor secreto.

1. Si se cambia la versión de `5.1.0.0` la aplicación en el archivo de `5.1.0.0_secret_value_dev` proyecto y se `5.1.0.0_secret_value_prod` vuelve a ejecutar la aplicación, el valor secreto devuelto es en el entorno de desarrollo y en producción.

> [!NOTE]
> También puede proporcionar <xref:Microsoft.Azure.KeyVault.KeyVaultClient> su <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>propia implementación a . Un cliente personalizado permite compartir una única instancia del cliente en la aplicación.

## <a name="bind-an-array-to-a-class"></a>Enlace de una matriz a una clase

El proveedor es capaz de leer valores de configuración en una matriz para enlazar a una matriz POCO.

Al leer desde un origen de configuración`:`que permite que las claves contengan separadores de dos`:0:` `:1:`puntos ( ), se utiliza un segmento de clave numérica para distinguir las claves que componen una matriz ( , , &hellip; `:{n}:`). Para obtener más información, consulte [Configuración: enlazar una matriz a una clase](xref:fundamentals/configuration/index#bind-an-array-to-a-class).

Las claves de Azure Key Vault no pueden usar dos puntos como separador. El enfoque descrito en este tema`--`utiliza guiones dobles ( ) como separador para valores jerárquicos (secciones). Las claves de matriz se almacenan en Azure Key`--0--` `--1--`Vault &hellip; `--{n}--`con guiones dobles y segmentos de clave numérica ( , , ).

Examine la siguiente configuración del proveedor de registro [de Serilog](https://serilog.net/) proporcionada por un archivo JSON. Hay dos literales de `WriteTo` objeto definidos en la matriz que reflejan dos *receptores*serilog, que describen los destinos para la salida de registro:

```json
"Serilog": {
  "WriteTo": [
    {
      "Name": "AzureTableStorage",
      "Args": {
        "storageTableName": "logs",
        "connectionString": "DefaultEnd...ountKey=Eby8...GMGw=="
      }
    },
    {
      "Name": "AzureDocumentDB",
      "Args": {
        "endpointUrl": "https://contoso.documents.azure.com:443",
        "authorizationKey": "Eby8...GMGw=="
      }
    }
  ]
}
```

La configuración que se muestra en el archivo JSON anterior`--`se almacena en Azure Key Vault mediante la notación de doble guión ( ) y segmentos numéricos:

| Clave | Value |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a>Recargar secretos

Los secretos `IConfigurationRoot.Reload()` se almacenan en caché hasta que se llama. La aplicación no respeta los secretos caducados, deshabilitados y actualizados en el almacén de claves hasta que `Reload` se ejecute.

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a>Secretos discapacitados y caducados

Los secretos deshabilitados y caducados lanzan un <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>archivo . Para evitar que la aplicación se inicie, proporcione la configuración con un proveedor de configuración diferente o actualice el secreto deshabilitado o caducado.

## <a name="troubleshoot"></a>Solución de problemas

Cuando la aplicación no puede cargar la configuración mediante el proveedor, se escribe un mensaje de error en la infraestructura de [registro de núcleo de ASP.NET.](xref:fundamentals/logging/index) Las siguientes condiciones impedirán que se cargue la configuración:

* La aplicación o el certificado no están configurados correctamente en Azure Active Directory.
* El almacén de claves no existe en Azure Key Vault.
* La aplicación no está autorizada para acceder al almacén de claves.
* La política de acceso `Get` `List` no incluye ni los permisos.
* En el almacén de claves, los datos de configuración (par nombre-valor) se nombran incorrectamente, faltan, se deshabilitan o caducan.
* La aplicación tiene el nombre`KeyVaultName`de almacén`AzureADApplicationId`de claves incorrecto (`AzureADCertThumbprint`), el identificador de aplicación de Azure AD ( ) o la huella digital del certificado de Azure AD ( ).
* La clave de configuración (nombre) es incorrecta en la aplicación para el valor que está intentando cargar.
* Al agregar la directiva de acceso de la aplicación al almacén de claves, se creó la directiva, pero el botón **Guardar** no se seleccionó en la interfaz de usuario **de directivas** de acceso.

## <a name="additional-resources"></a>Recursos adicionales

* <xref:fundamentals/configuration/index>
* [Microsoft Azure: Key Vault](https://azure.microsoft.com/services/key-vault/)
* [Documentación de Microsoft Azure: Key Vault](/azure/key-vault/)
* [Generación y transferencia de claves protegidas con HSM para Azure Key Vault](/azure/key-vault/key-vault-hsm-protected-keys)
* [Clase KeyVaultClient](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [Guía de inicio rápido: Establecimiento y recuperación de un secreto de Azure Key Vault mediante una aplicación web de .NET](/azure/key-vault/quick-create-net)
* [Tutorial: Uso de Azure Key Vault con una máquina virtual Windows en .NET](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

