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
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a><span data-ttu-id="167ff-103">Proveedor de configuración de Azure Key Vault en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="167ff-103">Azure Key Vault Configuration Provider in ASP.NET Core</span></span>

<span data-ttu-id="167ff-104">Por [Andrew Stanton-Nurse](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="167ff-104">By [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="167ff-105">En este documento se explica cómo usar el proveedor de configuración de [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) para cargar valores de configuración de aplicaciones desde secretos de Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="167ff-105">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="167ff-106">Azure Key Vault es un servicio basado en la nube que ayuda a proteger las claves criptográficas y los secretos utilizados por las aplicaciones y los servicios.</span><span class="sxs-lookup"><span data-stu-id="167ff-106">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="167ff-107">Los escenarios comunes para usar Azure Key Vault con aplicaciones ASP.NET Core incluyen:</span><span class="sxs-lookup"><span data-stu-id="167ff-107">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="167ff-108">Controlar el acceso a datos de configuración confidenciales.</span><span class="sxs-lookup"><span data-stu-id="167ff-108">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="167ff-109">Cumplir con el requisito de módulos de seguridad de hardware (HSM) validados por FIPS 140-2 nivel 2 al almacenar datos de configuración.</span><span class="sxs-lookup"><span data-stu-id="167ff-109">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="167ff-110">[Ver o descargar código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ( cómo[descargar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="167ff-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="167ff-111">Paquetes</span><span class="sxs-lookup"><span data-stu-id="167ff-111">Packages</span></span>

<span data-ttu-id="167ff-112">Agregue una referencia de paquete al paquete [Microsoft.Extensions.Configuration.AzureKeyVault.](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/)</span><span class="sxs-lookup"><span data-stu-id="167ff-112">Add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="167ff-113">Aplicación de ejemplo</span><span class="sxs-lookup"><span data-stu-id="167ff-113">Sample app</span></span>

<span data-ttu-id="167ff-114">La aplicación de ejemplo se ejecuta `#define` en cualquiera de los dos modos determinados por la instrucción en la parte superior del archivo *Program.cs:*</span><span class="sxs-lookup"><span data-stu-id="167ff-114">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="167ff-115">`Certificate`&ndash; Muestra el uso de un identificador de cliente de Azure Key Vault y un certificado X.509 para tener acceso a los secretos almacenados en Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="167ff-115">`Certificate` &ndash; Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="167ff-116">Esta versión del ejemplo se puede ejecutar desde cualquier ubicación, implementada en Azure App Service o en cualquier host capaz de servir una aplicación ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="167ff-116">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="167ff-117">`Managed`&ndash; Muestra cómo usar [identidades administradas para recursos](/azure/active-directory/managed-identities-azure-resources/overview) de Azure para autenticar la aplicación en Azure Key Vault con autenticación de Azure AD sin credenciales almacenadas en el código o la configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="167ff-117">`Managed` &ndash; Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="167ff-118">Cuando se usan identidades administradas para autenticarse, no se requieren un identificador de aplicación de Azure AD ni una contraseña (secreto de cliente).</span><span class="sxs-lookup"><span data-stu-id="167ff-118">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="167ff-119">La `Managed` versión del ejemplo debe implementarse en Azure.The version of the sample must be implement to Azure.</span><span class="sxs-lookup"><span data-stu-id="167ff-119">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="167ff-120">Siga las instrucciones de la sección [Usar las identidades administradas para recursos](#use-managed-identities-for-azure-resources) de Azure.</span><span class="sxs-lookup"><span data-stu-id="167ff-120">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="167ff-121">Para obtener más información sobre cómo configurar una`#define`aplicación <xref:index#preprocessor-directives-in-sample-code>de ejemplo mediante directivas de preprocesador ( ), consulte .</span><span class="sxs-lookup"><span data-stu-id="167ff-121">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="167ff-122">Almacenamiento secreto en el entorno de desarrollo</span><span class="sxs-lookup"><span data-stu-id="167ff-122">Secret storage in the Development environment</span></span>

<span data-ttu-id="167ff-123">Establezca secretos localmente con la [herramienta Secret Manager](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="167ff-123">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="167ff-124">Cuando la aplicación de ejemplo se ejecuta en el equipo local en el entorno de desarrollo, los secretos se cargan desde el almacén de Secret Manager local.</span><span class="sxs-lookup"><span data-stu-id="167ff-124">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local Secret Manager store.</span></span>

<span data-ttu-id="167ff-125">La herramienta Secret Manager `<UserSecretsId>` requiere una propiedad en el archivo de proyecto de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="167ff-125">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="167ff-126">Establezca el valor`{GUID}`de propiedad ( ) en cualquier GUID único:</span><span class="sxs-lookup"><span data-stu-id="167ff-126">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="167ff-127">Los secretos se crean como pares nombre-valor.</span><span class="sxs-lookup"><span data-stu-id="167ff-127">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="167ff-128">Los valores jerárquicos (secciones de configuración) utilizan un `:` (colon) como separador en los nombres de clave de configuración de ASP.NET [Core.](xref:fundamentals/configuration/index)</span><span class="sxs-lookup"><span data-stu-id="167ff-128">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="167ff-129">El Administrador secreto se utiliza desde un shell de comandos abierto `{SECRET VALUE}` a la raíz de [contenido](xref:fundamentals/index#content-root)del proyecto, donde `{SECRET NAME}` está el nombre y es el valor:</span><span class="sxs-lookup"><span data-stu-id="167ff-129">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="167ff-130">Ejecute los siguientes comandos en un shell de comandos desde la raíz de [contenido](xref:fundamentals/index#content-root) del proyecto para establecer los secretos de la aplicación de ejemplo:</span><span class="sxs-lookup"><span data-stu-id="167ff-130">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="167ff-131">Cuando estos secretos se almacenan en Azure Key Vault en el almacenamiento `_dev` secreto del `_prod`entorno de producción con la sección Azure [Key Vault,](#secret-storage-in-the-production-environment-with-azure-key-vault) el sufijo se cambia a .</span><span class="sxs-lookup"><span data-stu-id="167ff-131">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="167ff-132">El sufijo proporciona una indicación visual en la salida de la aplicación que indica el origen de los valores de configuración.</span><span class="sxs-lookup"><span data-stu-id="167ff-132">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="167ff-133">Almacenamiento secreto en el entorno de producción con Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="167ff-133">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="167ff-134">Las instrucciones proporcionadas por el tema [Inicio rápido: establecer y recuperar un secreto de Azure Key Vault mediante](/azure/key-vault/quick-create-cli) la CLI de Azure se resumen aquí para crear un almacén de claves de Azure y almacenar los secretos que usa la aplicación de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="167ff-134">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="167ff-135">Consulte el tema para obtener más detalles.</span><span class="sxs-lookup"><span data-stu-id="167ff-135">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="167ff-136">Abra Azure Cloud shell con cualquiera de los métodos siguientes en [Azure Portal:](https://portal.azure.com/)</span><span class="sxs-lookup"><span data-stu-id="167ff-136">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="167ff-137">Seleccione **Probar** en la esquina superior derecha de un bloque de código.</span><span class="sxs-lookup"><span data-stu-id="167ff-137">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="167ff-138">Use la cadena de búsqueda "CLI de Azure" en el cuadro de texto.</span><span class="sxs-lookup"><span data-stu-id="167ff-138">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="167ff-139">Abra Cloud Shell en su navegador con el botón **Iniciar Cloud Shell.**</span><span class="sxs-lookup"><span data-stu-id="167ff-139">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="167ff-140">Seleccione el botón **Cloud Shell** en el menú en la esquina superior derecha de Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="167ff-140">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="167ff-141">Para obtener más información, consulte CLI de [Azure](/cli/azure/) y [Información general sobre Azure Cloud Shell](/azure/cloud-shell/overview).</span><span class="sxs-lookup"><span data-stu-id="167ff-141">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="167ff-142">Si aún no está autenticado, inicie `az login` sesión con el comando.</span><span class="sxs-lookup"><span data-stu-id="167ff-142">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="167ff-143">Cree un grupo de recursos `{RESOURCE GROUP NAME}` con el siguiente comando, donde `{LOCATION}` está el nombre del grupo de recursos para el nuevo grupo de recursos y es la región de Azure (centro de datos):</span><span class="sxs-lookup"><span data-stu-id="167ff-143">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="167ff-144">Cree un almacén de claves en el `{KEY VAULT NAME}` grupo de recursos con `{LOCATION}` el siguiente comando, donde está el nombre del nuevo almacén de claves y es la región de Azure (centro de datos):</span><span class="sxs-lookup"><span data-stu-id="167ff-144">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="167ff-145">Cree secretos en el almacén de claves como pares nombre-valor.</span><span class="sxs-lookup"><span data-stu-id="167ff-145">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="167ff-146">Los nombres de secreto de Azure Key Vault se limitan a caracteres alfanuméricos y guiones.</span><span class="sxs-lookup"><span data-stu-id="167ff-146">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="167ff-147">Los valores jerárquicos (secciones de configuración) utilizan `--` (dos guiones) como separador.</span><span class="sxs-lookup"><span data-stu-id="167ff-147">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="167ff-148">Los dos puntos, que normalmente se utilizan para delimitar una sección de una subclave en [ASP.NET configuración de Core](xref:fundamentals/configuration/index), no se permiten en los nombres secretos del almacén de claves.</span><span class="sxs-lookup"><span data-stu-id="167ff-148">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="167ff-149">Por lo tanto, se usan dos guiones y se intercambian por dos puntos cuando los secretos se cargan en la configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="167ff-149">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="167ff-150">Los siguientes secretos se usan con la aplicación de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="167ff-150">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="167ff-151">Los valores `_prod` incluyen un sufijo `_dev` para distinguirlos de los valores de sufijo cargados en el entorno de desarrollo de los secretos de usuario.</span><span class="sxs-lookup"><span data-stu-id="167ff-151">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="167ff-152">Reemplace `{KEY VAULT NAME}` por el nombre del almacén de claves que creó en el paso anterior:</span><span class="sxs-lookup"><span data-stu-id="167ff-152">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="167ff-153">Usar el identificador de aplicación y el certificado X.509 para aplicaciones no hospedadas en Azure</span><span class="sxs-lookup"><span data-stu-id="167ff-153">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="167ff-154">Configure Azure AD, Azure Key Vault y la aplicación para que usen un identificador de aplicación de Azure Active Directory y un certificado X.509 para autenticarse en un almacén de claves **cuando la aplicación se hospeda fuera de Azure.**</span><span class="sxs-lookup"><span data-stu-id="167ff-154">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="167ff-155">Para más información, consulte el artículo [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates) (Claves, secretos y certificados).</span><span class="sxs-lookup"><span data-stu-id="167ff-155">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="167ff-156">Aunque se admite el uso de un identificador de aplicación y un certificado X.509 para aplicaciones hospedadas en Azure, se recomienda usar [identidades administradas para recursos](#use-managed-identities-for-azure-resources) de Azure al hospedar una aplicación en Azure.</span><span class="sxs-lookup"><span data-stu-id="167ff-156">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="167ff-157">Las identidades administradas no requieren almacenar un certificado en la aplicación ni en el entorno de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="167ff-157">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="167ff-158">La aplicación de ejemplo usa un identificador de `#define` aplicación y un certificado X.509 cuando la instrucción en la parte superior del archivo *de Program.cs* se establece en `Certificate`.</span><span class="sxs-lookup"><span data-stu-id="167ff-158">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="167ff-159">Cree un certificado de archivo PKCS-12 (*.pfx*).</span><span class="sxs-lookup"><span data-stu-id="167ff-159">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="167ff-160">Las opciones para crear certificados incluyen [MakeCert en Windows](/windows/desktop/seccrypto/makecert) y [OpenSSL](https://www.openssl.org/).</span><span class="sxs-lookup"><span data-stu-id="167ff-160">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="167ff-161">Instale el certificado en el almacén de certificados personal del usuario actual.</span><span class="sxs-lookup"><span data-stu-id="167ff-161">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="167ff-162">Marcar la clave como exportable es opcional.</span><span class="sxs-lookup"><span data-stu-id="167ff-162">Marking the key as exportable is optional.</span></span> <span data-ttu-id="167ff-163">Observe la huella digital del certificado, que se utiliza más adelante en este proceso.</span><span class="sxs-lookup"><span data-stu-id="167ff-163">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="167ff-164">Exporte el certificado de archivo PKCS-12 (*.pfx*) como certificado codificado con DER (*.cer*).</span><span class="sxs-lookup"><span data-stu-id="167ff-164">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="167ff-165">Registre la aplicación con Azure AD (**Registros**de aplicaciones ).</span><span class="sxs-lookup"><span data-stu-id="167ff-165">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="167ff-166">Cargue el certificado con codificación DER (*.cer*) en Azure AD:</span><span class="sxs-lookup"><span data-stu-id="167ff-166">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="167ff-167">Seleccione la aplicación en Azure AD.</span><span class="sxs-lookup"><span data-stu-id="167ff-167">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="167ff-168">Vaya a **Certificados & secretos.**</span><span class="sxs-lookup"><span data-stu-id="167ff-168">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="167ff-169">Seleccione **Cargar certificado** para cargar el certificado, que contiene la clave pública.</span><span class="sxs-lookup"><span data-stu-id="167ff-169">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="167ff-170">Un certificado *.cer*, *.pem*o *.crt* es aceptable.</span><span class="sxs-lookup"><span data-stu-id="167ff-170">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="167ff-171">Almacene el nombre del almacén de claves, el identificador de aplicación y la huella digital del certificado en el archivo *appsettings.json* de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="167ff-171">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="167ff-172">Vaya a Almacenes de claves en Azure Portal.Navigate a Almacenes de claves en Azure Portal.Navigate a **Almacenes** de claves en Azure Portal.Navigate to</span><span class="sxs-lookup"><span data-stu-id="167ff-172">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="167ff-173">Seleccione el almacén de claves que creó en la sección [Almacenamiento secreto en el entorno de producción con Azure Key Vault.](#secret-storage-in-the-production-environment-with-azure-key-vault)</span><span class="sxs-lookup"><span data-stu-id="167ff-173">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="167ff-174">Seleccione **Directivas**de acceso .</span><span class="sxs-lookup"><span data-stu-id="167ff-174">Select **Access policies**.</span></span>
1. <span data-ttu-id="167ff-175">Seleccione **Agregar directiva de acceso**.</span><span class="sxs-lookup"><span data-stu-id="167ff-175">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="167ff-176">Abra **Permisos secretos** y proporcione a la aplicación permisos **Obtener** y **Lista.**</span><span class="sxs-lookup"><span data-stu-id="167ff-176">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="167ff-177">Seleccione **Seleccionar entidad de seguridad** y seleccione la aplicación registrada por nombre.</span><span class="sxs-lookup"><span data-stu-id="167ff-177">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="167ff-178">Seleccione el botón **Seleccionar**.</span><span class="sxs-lookup"><span data-stu-id="167ff-178">Select the **Select** button.</span></span>
1. <span data-ttu-id="167ff-179">Seleccione **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="167ff-179">Select **OK**.</span></span>
1. <span data-ttu-id="167ff-180">Seleccione **Guardar**.</span><span class="sxs-lookup"><span data-stu-id="167ff-180">Select **Save**.</span></span>
1. <span data-ttu-id="167ff-181">Implemente la aplicación.</span><span class="sxs-lookup"><span data-stu-id="167ff-181">Deploy the app.</span></span>

<span data-ttu-id="167ff-182">La `Certificate` aplicación de ejemplo obtiene `IConfigurationRoot` sus valores de configuración con el mismo nombre que el nombre secreto:</span><span class="sxs-lookup"><span data-stu-id="167ff-182">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="167ff-183">Valores no jerárquicos: el valor para `SecretName` se obtiene con `config["SecretName"]`.</span><span class="sxs-lookup"><span data-stu-id="167ff-183">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="167ff-184">Valores jerárquicos (secciones): utilice `:` la notación (colon) o el `GetSection` método de extensión.</span><span class="sxs-lookup"><span data-stu-id="167ff-184">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="167ff-185">Utilice cualquiera de estos enfoques para obtener el valor de configuración:</span><span class="sxs-lookup"><span data-stu-id="167ff-185">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="167ff-186">El certificado X.509 es administrado por el sistema operativo.</span><span class="sxs-lookup"><span data-stu-id="167ff-186">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="167ff-187">La aplicación <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> llama con los valores proporcionados por el archivo *appsettings.json:*</span><span class="sxs-lookup"><span data-stu-id="167ff-187">The app calls <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="167ff-188">Valores de ejemplo:</span><span class="sxs-lookup"><span data-stu-id="167ff-188">Example values:</span></span>

* <span data-ttu-id="167ff-189">Nombre del almacén de claves:`contosovault`</span><span class="sxs-lookup"><span data-stu-id="167ff-189">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="167ff-190">ID de aplicación:`627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="167ff-190">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="167ff-191">Huella digital del certificado:`fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="167ff-191">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="167ff-192">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="167ff-192">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/3.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="167ff-193">Al ejecutar la aplicación, una página web muestra los valores secretos cargados.</span><span class="sxs-lookup"><span data-stu-id="167ff-193">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="167ff-194">En el entorno de desarrollo, `_dev` los valores secretos se cargan con el sufijo.</span><span class="sxs-lookup"><span data-stu-id="167ff-194">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="167ff-195">En el entorno de producción, `_prod` los valores se cargan con el sufijo.</span><span class="sxs-lookup"><span data-stu-id="167ff-195">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="167ff-196">Usar identidades administradas para recursos de Azure</span><span class="sxs-lookup"><span data-stu-id="167ff-196">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="167ff-197">**Una aplicación implementada en Azure** puede aprovechar las [identidades administradas para los recursos](/azure/active-directory/managed-identities-azure-resources/overview)de Azure, lo que permite que la aplicación se autentique con Azure Key Vault mediante la autenticación de Azure AD sin credenciales (identificador de aplicación y contraseña/secreto de cliente) almacenadaen en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="167ff-197">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="167ff-198">La aplicación de ejemplo usa identidades `#define` administradas para recursos de Azure `Managed`cuando la instrucción en la parte superior del archivo *Program.cs* se establece en .</span><span class="sxs-lookup"><span data-stu-id="167ff-198">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="167ff-199">Escriba el nombre del almacén en el archivo *appsettings.json* de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="167ff-199">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="167ff-200">La aplicación de ejemplo no requiere un identificador de aplicación `Managed` y una contraseña (secreto de cliente) cuando se establece en la versión, por lo que puede omitir esas entradas de configuración.</span><span class="sxs-lookup"><span data-stu-id="167ff-200">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="167ff-201">La aplicación se implementa en Azure y Azure autentica la aplicación para tener acceso a Azure Key Vault solo con el nombre del almacén almacenado en el archivo *appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="167ff-201">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="167ff-202">Implemente la aplicación de ejemplo en Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="167ff-202">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="167ff-203">Una aplicación implementada en Azure App Service se registra automáticamente con Azure AD cuando se crea el servicio.</span><span class="sxs-lookup"><span data-stu-id="167ff-203">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="167ff-204">Obtenga el identificador de objeto de la implementación para su uso en el siguiente comando.</span><span class="sxs-lookup"><span data-stu-id="167ff-204">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="167ff-205">El identificador de objeto se muestra en Azure Portal en el panel **Identidad** de App Service.</span><span class="sxs-lookup"><span data-stu-id="167ff-205">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="167ff-206">Con la CLI de Azure y el identificador `list` `get` de objeto de la aplicación, proporcione a la aplicación y permisos para tener acceso al almacén de claves:</span><span class="sxs-lookup"><span data-stu-id="167ff-206">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="167ff-207">**Reinicie la aplicación** con la CLI de Azure, PowerShell o Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="167ff-207">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="167ff-208">La aplicación de ejemplo:</span><span class="sxs-lookup"><span data-stu-id="167ff-208">The sample app:</span></span>

* <span data-ttu-id="167ff-209">Crea una instancia `AzureServiceTokenProvider` de la clase sin una cadena de conexión.</span><span class="sxs-lookup"><span data-stu-id="167ff-209">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="167ff-210">Cuando no se proporciona una cadena de conexión, el proveedor intenta obtener un token de acceso de identidades administradas para recursos de Azure.</span><span class="sxs-lookup"><span data-stu-id="167ff-210">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="167ff-211">Se <xref:Microsoft.Azure.KeyVault.KeyVaultClient> crea un `AzureServiceTokenProvider` nuevo con la devolución de llamada del token de instancia.</span><span class="sxs-lookup"><span data-stu-id="167ff-211">A new <xref:Microsoft.Azure.KeyVault.KeyVaultClient> is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="167ff-212">La <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instancia se utiliza con <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> una implementación predeterminada que carga todos`--`los valores`:`secretos y reemplaza double-dashes ( ) con dos puntos ( ) en los nombres de clave.</span><span class="sxs-lookup"><span data-stu-id="167ff-212">The <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance is used with a default implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="167ff-213">Valor de ejemplo del nombre del almacén de claves:`contosovault`</span><span class="sxs-lookup"><span data-stu-id="167ff-213">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="167ff-214">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="167ff-214">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="167ff-215">Al ejecutar la aplicación, una página web muestra los valores secretos cargados.</span><span class="sxs-lookup"><span data-stu-id="167ff-215">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="167ff-216">En el entorno de desarrollo, los valores secretos tienen el `_dev` sufijo porque los proporcionan los secretos de usuario.</span><span class="sxs-lookup"><span data-stu-id="167ff-216">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="167ff-217">En el entorno de producción, `_prod` los valores se cargan con el sufijo porque Azure Key Vault los proporciona.</span><span class="sxs-lookup"><span data-stu-id="167ff-217">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="167ff-218">Si recibe `Access denied` un error, confirme que la aplicación está registrada en Azure AD y que proporciona acceso al almacén de claves.</span><span class="sxs-lookup"><span data-stu-id="167ff-218">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="167ff-219">Confirme que ha reiniciado el servicio en Azure.Confirme que ha reiniciado el servicio en Azure.Confirm que ha reiniciado el servicio en Azure.Confirm que ha reiniciado el servicio en</span><span class="sxs-lookup"><span data-stu-id="167ff-219">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="167ff-220">Para obtener información sobre el uso del proveedor con una identidad administrada y una canalización de VSTS, consulte Creación de una conexión de servicio de Azure Resource Manager a una máquina virtual con una identidad de [servicio administrada.](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity)</span><span class="sxs-lookup"><span data-stu-id="167ff-220">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="configuration-options"></a><span data-ttu-id="167ff-221">Opciones de configuración</span><span class="sxs-lookup"><span data-stu-id="167ff-221">Configuration options</span></span>

<span data-ttu-id="167ff-222"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>puede aceptar <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions>un :</span><span class="sxs-lookup"><span data-stu-id="167ff-222"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> can accept an <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions>:</span></span>

```csharp
config.AddAzureKeyVault(
    new AzureKeyVaultConfigurationOptions()
    {
        ...
    });
```

| <span data-ttu-id="167ff-223">Propiedad</span><span class="sxs-lookup"><span data-stu-id="167ff-223">Property</span></span>         | <span data-ttu-id="167ff-224">Descripción</span><span class="sxs-lookup"><span data-stu-id="167ff-224">Description</span></span> |
| ---------------- | ----------- |
| `Client`         | <span data-ttu-id="167ff-225"><xref:Microsoft.Azure.KeyVault.KeyVaultClient>para recuperar valores.</span><span class="sxs-lookup"><span data-stu-id="167ff-225"><xref:Microsoft.Azure.KeyVault.KeyVaultClient> to use for retrieving values.</span></span> |
| `Manager`        | <span data-ttu-id="167ff-226"><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>instancia utilizada para controlar la carga secreta.</span><span class="sxs-lookup"><span data-stu-id="167ff-226"><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> instance used to control secret loading.</span></span> |
| `ReloadInterval` | <span data-ttu-id="167ff-227">`Timespan`para esperar entre los intentos de sondear el almacén de claves en busca de cambios.</span><span class="sxs-lookup"><span data-stu-id="167ff-227">`Timespan` to wait between attempts at polling the key vault for changes.</span></span> <span data-ttu-id="167ff-228">El valor `null` predeterminado es (la configuración no se vuelve a cargar).</span><span class="sxs-lookup"><span data-stu-id="167ff-228">The default value is `null` (configuration isn't reloaded).</span></span> |
| `Vault`          | <span data-ttu-id="167ff-229">URI del almacén de claves.</span><span class="sxs-lookup"><span data-stu-id="167ff-229">Key vault URI.</span></span> |

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="167ff-230">Utilice un prefijo de nombre de clave</span><span class="sxs-lookup"><span data-stu-id="167ff-230">Use a key name prefix</span></span>

<span data-ttu-id="167ff-231"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>proporciona una sobrecarga que acepta <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>una implementación de , lo que le permite controlar cómo se convierten los secretos del almacén de claves en claves de configuración.</span><span class="sxs-lookup"><span data-stu-id="167ff-231"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> provides an overload that accepts an implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="167ff-232">Por ejemplo, puede implementar la interfaz para cargar valores secretos basados en un valor de prefijo que proporcione al iniciar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="167ff-232">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="167ff-233">Esto le permite, por ejemplo, cargar secretos basados en la versión de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="167ff-233">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="167ff-234">No use prefijos en los secretos del almacén de claves para colocar secretos para varias aplicaciones en el mismo almacén de claves o para colocar secretos de entorno (por ejemplo, *el desarrollo* frente a los secretos de *producción)* en el mismo almacén.</span><span class="sxs-lookup"><span data-stu-id="167ff-234">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="167ff-235">Se recomienda que las diferentes aplicaciones y entornos de desarrollo/producción usen almacenes de claves independientes para aislar los entornos de aplicaciones para obtener el nivel más alto de seguridad.</span><span class="sxs-lookup"><span data-stu-id="167ff-235">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="167ff-236">En el ejemplo siguiente, se establece un secreto en el almacén de `5000-AppSecret` claves (y mediante la herramienta Secret Manager para el entorno de desarrollo) para (no se permiten períodos en los nombres de secreto del almacén de claves).</span><span class="sxs-lookup"><span data-stu-id="167ff-236">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="167ff-237">Este secreto representa un secreto de aplicación para la versión 5.0.0.0 de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="167ff-237">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="167ff-238">Para otra versión de la aplicación, 5.1.0.0, se agrega un secreto al `5100-AppSecret`almacén de claves (y mediante la herramienta Secret Manager) para .</span><span class="sxs-lookup"><span data-stu-id="167ff-238">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="167ff-239">Cada versión de la aplicación carga `AppSecret`su valor secreto versionado en su configuración como , eliminando la versión a medida que carga el secreto.</span><span class="sxs-lookup"><span data-stu-id="167ff-239">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="167ff-240"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>se llama con <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>una costumbre:</span><span class="sxs-lookup"><span data-stu-id="167ff-240"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> is called with a custom <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="167ff-241">La <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementación reacciona a los prefijos de versión de los secretos para cargar el secreto adecuado en la configuración:</span><span class="sxs-lookup"><span data-stu-id="167ff-241">The <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="167ff-242">`Load`carga un secreto cuando su nombre comienza con el prefijo.</span><span class="sxs-lookup"><span data-stu-id="167ff-242">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="167ff-243">Otros secretos no están cargados.</span><span class="sxs-lookup"><span data-stu-id="167ff-243">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="167ff-244">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="167ff-244">`GetKey`:</span></span>
  * <span data-ttu-id="167ff-245">Quita el prefijo del nombre secreto.</span><span class="sxs-lookup"><span data-stu-id="167ff-245">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="167ff-246">Reemplaza dos guiones en cualquier `KeyDelimiter`nombre con el , que es el delimitador utilizado en la configuración (normalmente dos puntos).</span><span class="sxs-lookup"><span data-stu-id="167ff-246">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="167ff-247">Azure Key Vault no permite dos puntos en nombres secretos.</span><span class="sxs-lookup"><span data-stu-id="167ff-247">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="167ff-248">El `Load` método es llamado por un algoritmo de proveedor que recorre en iteración los secretos del almacén para encontrar los que tienen el prefijo de versión.</span><span class="sxs-lookup"><span data-stu-id="167ff-248">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="167ff-249">Cuando se encuentra un `Load`prefijo de `GetKey` versión con , el algoritmo utiliza el método para devolver el nombre de configuración del nombre secreto.</span><span class="sxs-lookup"><span data-stu-id="167ff-249">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="167ff-250">Elimina el prefijo de versión del nombre del secreto y devuelve el resto del nombre secreto para cargarlo en los pares nombre-valor de configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="167ff-250">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="167ff-251">Cuando se implementa este enfoque:</span><span class="sxs-lookup"><span data-stu-id="167ff-251">When this approach is implemented:</span></span>

1. <span data-ttu-id="167ff-252">La versión de la aplicación especificada en el archivo de proyecto de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="167ff-252">The app's version specified in the app's project file.</span></span> <span data-ttu-id="167ff-253">En el ejemplo siguiente, la versión `5.0.0.0`de la aplicación se establece en:</span><span class="sxs-lookup"><span data-stu-id="167ff-253">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="167ff-254">Confirme que `<UserSecretsId>` una propiedad está presente en el `{GUID}` archivo de proyecto de la aplicación, donde hay un GUID proporcionado por el usuario:</span><span class="sxs-lookup"><span data-stu-id="167ff-254">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="167ff-255">Guarde los siguientes secretos localmente con la [herramienta Secret Manager:](xref:security/app-secrets)</span><span class="sxs-lookup"><span data-stu-id="167ff-255">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="167ff-256">Los secretos se guardan en Azure Key Vault mediante los siguientes comandos de la CLI de Azure:</span><span class="sxs-lookup"><span data-stu-id="167ff-256">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="167ff-257">Cuando se ejecuta la aplicación, se cargan los secretos del almacén de claves.</span><span class="sxs-lookup"><span data-stu-id="167ff-257">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="167ff-258">El secreto `5000-AppSecret` de cadena para coincide con la versión de la`5.0.0.0`aplicación especificada en el archivo de proyecto de la aplicación ( ).</span><span class="sxs-lookup"><span data-stu-id="167ff-258">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="167ff-259">La versión, `5000` (con el guión), se elimina del nombre de clave.</span><span class="sxs-lookup"><span data-stu-id="167ff-259">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="167ff-260">En toda la aplicación, la `AppSecret` lectura de la configuración con la clave carga el valor secreto.</span><span class="sxs-lookup"><span data-stu-id="167ff-260">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="167ff-261">Si se cambia la versión de `5.1.0.0` la aplicación en el archivo de `5.1.0.0_secret_value_dev` proyecto y se `5.1.0.0_secret_value_prod` vuelve a ejecutar la aplicación, el valor secreto devuelto es en el entorno de desarrollo y en producción.</span><span class="sxs-lookup"><span data-stu-id="167ff-261">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="167ff-262">También puede proporcionar <xref:Microsoft.Azure.KeyVault.KeyVaultClient> su <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>propia implementación a .</span><span class="sxs-lookup"><span data-stu-id="167ff-262">You can also provide your own <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementation to <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>.</span></span> <span data-ttu-id="167ff-263">Un cliente personalizado permite compartir una única instancia del cliente en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="167ff-263">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="167ff-264">Enlace de una matriz a una clase</span><span class="sxs-lookup"><span data-stu-id="167ff-264">Bind an array to a class</span></span>

<span data-ttu-id="167ff-265">El proveedor es capaz de leer valores de configuración en una matriz para enlazar a una matriz POCO.</span><span class="sxs-lookup"><span data-stu-id="167ff-265">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="167ff-266">Al leer desde un origen de configuración`:`que permite que las claves contengan separadores de dos`:0:` `:1:`puntos ( ), se utiliza un segmento de clave numérica para distinguir las claves que componen una matriz ( , , &hellip; `:{n}:`).</span><span class="sxs-lookup"><span data-stu-id="167ff-266">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="167ff-267">Para obtener más información, consulte [Configuración: enlazar una matriz a una clase](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="167ff-267">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="167ff-268">Las claves de Azure Key Vault no pueden usar dos puntos como separador.</span><span class="sxs-lookup"><span data-stu-id="167ff-268">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="167ff-269">El enfoque descrito en este tema`--`utiliza guiones dobles ( ) como separador para valores jerárquicos (secciones).</span><span class="sxs-lookup"><span data-stu-id="167ff-269">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="167ff-270">Las claves de matriz se almacenan en Azure Key`--0--` `--1--`Vault &hellip; `--{n}--`con guiones dobles y segmentos de clave numérica ( , , ).</span><span class="sxs-lookup"><span data-stu-id="167ff-270">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="167ff-271">Examine la siguiente configuración del proveedor de registro [de Serilog](https://serilog.net/) proporcionada por un archivo JSON.</span><span class="sxs-lookup"><span data-stu-id="167ff-271">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="167ff-272">Hay dos literales de `WriteTo` objeto definidos en la matriz que reflejan dos *receptores*serilog, que describen los destinos para la salida de registro:</span><span class="sxs-lookup"><span data-stu-id="167ff-272">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

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

<span data-ttu-id="167ff-273">La configuración que se muestra en el archivo JSON anterior`--`se almacena en Azure Key Vault mediante la notación de doble guión ( ) y segmentos numéricos:</span><span class="sxs-lookup"><span data-stu-id="167ff-273">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="167ff-274">Clave</span><span class="sxs-lookup"><span data-stu-id="167ff-274">Key</span></span> | <span data-ttu-id="167ff-275">Value</span><span class="sxs-lookup"><span data-stu-id="167ff-275">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="167ff-276">Recargar secretos</span><span class="sxs-lookup"><span data-stu-id="167ff-276">Reload secrets</span></span>

<span data-ttu-id="167ff-277">Los secretos `IConfigurationRoot.Reload()` se almacenan en caché hasta que se llama.</span><span class="sxs-lookup"><span data-stu-id="167ff-277">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="167ff-278">La aplicación no respeta los secretos caducados, deshabilitados y actualizados en el almacén de claves hasta que `Reload` se ejecute.</span><span class="sxs-lookup"><span data-stu-id="167ff-278">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="167ff-279">Secretos discapacitados y caducados</span><span class="sxs-lookup"><span data-stu-id="167ff-279">Disabled and expired secrets</span></span>

<span data-ttu-id="167ff-280">Los secretos deshabilitados y caducados lanzan un <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>archivo .</span><span class="sxs-lookup"><span data-stu-id="167ff-280">Disabled and expired secrets throw a <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>.</span></span> <span data-ttu-id="167ff-281">Para evitar que la aplicación se inicie, proporcione la configuración con un proveedor de configuración diferente o actualice el secreto deshabilitado o caducado.</span><span class="sxs-lookup"><span data-stu-id="167ff-281">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="167ff-282">Solución de problemas</span><span class="sxs-lookup"><span data-stu-id="167ff-282">Troubleshoot</span></span>

<span data-ttu-id="167ff-283">Cuando la aplicación no puede cargar la configuración mediante el proveedor, se escribe un mensaje de error en la infraestructura de [registro de núcleo de ASP.NET.](xref:fundamentals/logging/index)</span><span class="sxs-lookup"><span data-stu-id="167ff-283">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="167ff-284">Las siguientes condiciones impedirán que se cargue la configuración:</span><span class="sxs-lookup"><span data-stu-id="167ff-284">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="167ff-285">La aplicación o el certificado no están configurados correctamente en Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="167ff-285">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="167ff-286">El almacén de claves no existe en Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="167ff-286">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="167ff-287">La aplicación no está autorizada para acceder al almacén de claves.</span><span class="sxs-lookup"><span data-stu-id="167ff-287">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="167ff-288">La política de acceso `Get` `List` no incluye ni los permisos.</span><span class="sxs-lookup"><span data-stu-id="167ff-288">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="167ff-289">En el almacén de claves, los datos de configuración (par nombre-valor) se nombran incorrectamente, faltan, se deshabilitan o caducan.</span><span class="sxs-lookup"><span data-stu-id="167ff-289">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="167ff-290">La aplicación tiene el nombre`KeyVaultName`de almacén`AzureADApplicationId`de claves incorrecto (`AzureADCertThumbprint`), el identificador de aplicación de Azure AD ( ) o la huella digital del certificado de Azure AD ( ).</span><span class="sxs-lookup"><span data-stu-id="167ff-290">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="167ff-291">La clave de configuración (nombre) es incorrecta en la aplicación para el valor que está intentando cargar.</span><span class="sxs-lookup"><span data-stu-id="167ff-291">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="167ff-292">Al agregar la directiva de acceso de la aplicación al almacén de claves, se creó la directiva, pero el botón **Guardar** no se seleccionó en la interfaz de usuario **de directivas** de acceso.</span><span class="sxs-lookup"><span data-stu-id="167ff-292">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="167ff-293">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="167ff-293">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="167ff-294">Microsoft Azure: Key Vault</span><span class="sxs-lookup"><span data-stu-id="167ff-294">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="167ff-295">Documentación de Microsoft Azure: Key Vault</span><span class="sxs-lookup"><span data-stu-id="167ff-295">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="167ff-296">Generación y transferencia de claves protegidas con HSM para Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="167ff-296">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="167ff-297">Clase KeyVaultClient</span><span class="sxs-lookup"><span data-stu-id="167ff-297">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="167ff-298">Guía de inicio rápido: Establecimiento y recuperación de un secreto de Azure Key Vault mediante una aplicación web de .NET</span><span class="sxs-lookup"><span data-stu-id="167ff-298">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="167ff-299">Tutorial: Uso de Azure Key Vault con una máquina virtual Windows en .NET</span><span class="sxs-lookup"><span data-stu-id="167ff-299">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="167ff-300">En este documento se explica cómo usar el proveedor de configuración de [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) para cargar valores de configuración de aplicaciones desde secretos de Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="167ff-300">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="167ff-301">Azure Key Vault es un servicio basado en la nube que ayuda a proteger las claves criptográficas y los secretos utilizados por las aplicaciones y los servicios.</span><span class="sxs-lookup"><span data-stu-id="167ff-301">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="167ff-302">Los escenarios comunes para usar Azure Key Vault con aplicaciones ASP.NET Core incluyen:</span><span class="sxs-lookup"><span data-stu-id="167ff-302">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="167ff-303">Controlar el acceso a datos de configuración confidenciales.</span><span class="sxs-lookup"><span data-stu-id="167ff-303">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="167ff-304">Cumplir con el requisito de módulos de seguridad de hardware (HSM) validados por FIPS 140-2 nivel 2 al almacenar datos de configuración.</span><span class="sxs-lookup"><span data-stu-id="167ff-304">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="167ff-305">[Ver o descargar código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ( cómo[descargar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="167ff-305">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="167ff-306">Paquetes</span><span class="sxs-lookup"><span data-stu-id="167ff-306">Packages</span></span>

<span data-ttu-id="167ff-307">Agregue una referencia de paquete al paquete [Microsoft.Extensions.Configuration.AzureKeyVault.](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/)</span><span class="sxs-lookup"><span data-stu-id="167ff-307">Add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="167ff-308">Aplicación de ejemplo</span><span class="sxs-lookup"><span data-stu-id="167ff-308">Sample app</span></span>

<span data-ttu-id="167ff-309">La aplicación de ejemplo se ejecuta `#define` en cualquiera de los dos modos determinados por la instrucción en la parte superior del archivo *Program.cs:*</span><span class="sxs-lookup"><span data-stu-id="167ff-309">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="167ff-310">`Certificate`&ndash; Muestra el uso de un identificador de cliente de Azure Key Vault y un certificado X.509 para tener acceso a los secretos almacenados en Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="167ff-310">`Certificate` &ndash; Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="167ff-311">Esta versión del ejemplo se puede ejecutar desde cualquier ubicación, implementada en Azure App Service o en cualquier host capaz de servir una aplicación ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="167ff-311">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="167ff-312">`Managed`&ndash; Muestra cómo usar [identidades administradas para recursos](/azure/active-directory/managed-identities-azure-resources/overview) de Azure para autenticar la aplicación en Azure Key Vault con autenticación de Azure AD sin credenciales almacenadas en el código o la configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="167ff-312">`Managed` &ndash; Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="167ff-313">Cuando se usan identidades administradas para autenticarse, no se requieren un identificador de aplicación de Azure AD ni una contraseña (secreto de cliente).</span><span class="sxs-lookup"><span data-stu-id="167ff-313">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="167ff-314">La `Managed` versión del ejemplo debe implementarse en Azure.The version of the sample must be implement to Azure.</span><span class="sxs-lookup"><span data-stu-id="167ff-314">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="167ff-315">Siga las instrucciones de la sección [Usar las identidades administradas para recursos](#use-managed-identities-for-azure-resources) de Azure.</span><span class="sxs-lookup"><span data-stu-id="167ff-315">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="167ff-316">Para obtener más información sobre cómo configurar una`#define`aplicación <xref:index#preprocessor-directives-in-sample-code>de ejemplo mediante directivas de preprocesador ( ), consulte .</span><span class="sxs-lookup"><span data-stu-id="167ff-316">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="167ff-317">Almacenamiento secreto en el entorno de desarrollo</span><span class="sxs-lookup"><span data-stu-id="167ff-317">Secret storage in the Development environment</span></span>

<span data-ttu-id="167ff-318">Establezca secretos localmente con la [herramienta Secret Manager](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="167ff-318">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="167ff-319">Cuando la aplicación de ejemplo se ejecuta en el equipo local en el entorno de desarrollo, los secretos se cargan desde el almacén de Secret Manager local.</span><span class="sxs-lookup"><span data-stu-id="167ff-319">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local Secret Manager store.</span></span>

<span data-ttu-id="167ff-320">La herramienta Secret Manager `<UserSecretsId>` requiere una propiedad en el archivo de proyecto de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="167ff-320">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="167ff-321">Establezca el valor`{GUID}`de propiedad ( ) en cualquier GUID único:</span><span class="sxs-lookup"><span data-stu-id="167ff-321">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="167ff-322">Los secretos se crean como pares nombre-valor.</span><span class="sxs-lookup"><span data-stu-id="167ff-322">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="167ff-323">Los valores jerárquicos (secciones de configuración) utilizan un `:` (colon) como separador en los nombres de clave de configuración de ASP.NET [Core.](xref:fundamentals/configuration/index)</span><span class="sxs-lookup"><span data-stu-id="167ff-323">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="167ff-324">El Administrador secreto se utiliza desde un shell de comandos abierto `{SECRET VALUE}` a la raíz de [contenido](xref:fundamentals/index#content-root)del proyecto, donde `{SECRET NAME}` está el nombre y es el valor:</span><span class="sxs-lookup"><span data-stu-id="167ff-324">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="167ff-325">Ejecute los siguientes comandos en un shell de comandos desde la raíz de [contenido](xref:fundamentals/index#content-root) del proyecto para establecer los secretos de la aplicación de ejemplo:</span><span class="sxs-lookup"><span data-stu-id="167ff-325">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="167ff-326">Cuando estos secretos se almacenan en Azure Key Vault en el almacenamiento `_dev` secreto del `_prod`entorno de producción con la sección Azure [Key Vault,](#secret-storage-in-the-production-environment-with-azure-key-vault) el sufijo se cambia a .</span><span class="sxs-lookup"><span data-stu-id="167ff-326">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="167ff-327">El sufijo proporciona una indicación visual en la salida de la aplicación que indica el origen de los valores de configuración.</span><span class="sxs-lookup"><span data-stu-id="167ff-327">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="167ff-328">Almacenamiento secreto en el entorno de producción con Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="167ff-328">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="167ff-329">Las instrucciones proporcionadas por el tema [Inicio rápido: establecer y recuperar un secreto de Azure Key Vault mediante](/azure/key-vault/quick-create-cli) la CLI de Azure se resumen aquí para crear un almacén de claves de Azure y almacenar los secretos que usa la aplicación de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="167ff-329">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="167ff-330">Consulte el tema para obtener más detalles.</span><span class="sxs-lookup"><span data-stu-id="167ff-330">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="167ff-331">Abra Azure Cloud shell con cualquiera de los métodos siguientes en [Azure Portal:](https://portal.azure.com/)</span><span class="sxs-lookup"><span data-stu-id="167ff-331">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="167ff-332">Seleccione **Probar** en la esquina superior derecha de un bloque de código.</span><span class="sxs-lookup"><span data-stu-id="167ff-332">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="167ff-333">Use la cadena de búsqueda "CLI de Azure" en el cuadro de texto.</span><span class="sxs-lookup"><span data-stu-id="167ff-333">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="167ff-334">Abra Cloud Shell en su navegador con el botón **Iniciar Cloud Shell.**</span><span class="sxs-lookup"><span data-stu-id="167ff-334">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="167ff-335">Seleccione el botón **Cloud Shell** en el menú en la esquina superior derecha de Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="167ff-335">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="167ff-336">Para obtener más información, consulte CLI de [Azure](/cli/azure/) y [Información general sobre Azure Cloud Shell](/azure/cloud-shell/overview).</span><span class="sxs-lookup"><span data-stu-id="167ff-336">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="167ff-337">Si aún no está autenticado, inicie `az login` sesión con el comando.</span><span class="sxs-lookup"><span data-stu-id="167ff-337">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="167ff-338">Cree un grupo de recursos `{RESOURCE GROUP NAME}` con el siguiente comando, donde `{LOCATION}` está el nombre del grupo de recursos para el nuevo grupo de recursos y es la región de Azure (centro de datos):</span><span class="sxs-lookup"><span data-stu-id="167ff-338">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="167ff-339">Cree un almacén de claves en el `{KEY VAULT NAME}` grupo de recursos con `{LOCATION}` el siguiente comando, donde está el nombre del nuevo almacén de claves y es la región de Azure (centro de datos):</span><span class="sxs-lookup"><span data-stu-id="167ff-339">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="167ff-340">Cree secretos en el almacén de claves como pares nombre-valor.</span><span class="sxs-lookup"><span data-stu-id="167ff-340">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="167ff-341">Los nombres de secreto de Azure Key Vault se limitan a caracteres alfanuméricos y guiones.</span><span class="sxs-lookup"><span data-stu-id="167ff-341">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="167ff-342">Los valores jerárquicos (secciones de configuración) utilizan `--` (dos guiones) como separador.</span><span class="sxs-lookup"><span data-stu-id="167ff-342">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="167ff-343">Los dos puntos, que normalmente se utilizan para delimitar una sección de una subclave en [ASP.NET configuración de Core](xref:fundamentals/configuration/index), no se permiten en los nombres secretos del almacén de claves.</span><span class="sxs-lookup"><span data-stu-id="167ff-343">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="167ff-344">Por lo tanto, se usan dos guiones y se intercambian por dos puntos cuando los secretos se cargan en la configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="167ff-344">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="167ff-345">Los siguientes secretos se usan con la aplicación de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="167ff-345">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="167ff-346">Los valores `_prod` incluyen un sufijo `_dev` para distinguirlos de los valores de sufijo cargados en el entorno de desarrollo de los secretos de usuario.</span><span class="sxs-lookup"><span data-stu-id="167ff-346">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="167ff-347">Reemplace `{KEY VAULT NAME}` por el nombre del almacén de claves que creó en el paso anterior:</span><span class="sxs-lookup"><span data-stu-id="167ff-347">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="167ff-348">Usar el identificador de aplicación y el certificado X.509 para aplicaciones no hospedadas en Azure</span><span class="sxs-lookup"><span data-stu-id="167ff-348">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="167ff-349">Configure Azure AD, Azure Key Vault y la aplicación para que usen un identificador de aplicación de Azure Active Directory y un certificado X.509 para autenticarse en un almacén de claves **cuando la aplicación se hospeda fuera de Azure.**</span><span class="sxs-lookup"><span data-stu-id="167ff-349">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="167ff-350">Para más información, consulte el artículo [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates) (Claves, secretos y certificados).</span><span class="sxs-lookup"><span data-stu-id="167ff-350">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="167ff-351">Aunque se admite el uso de un identificador de aplicación y un certificado X.509 para aplicaciones hospedadas en Azure, se recomienda usar [identidades administradas para recursos](#use-managed-identities-for-azure-resources) de Azure al hospedar una aplicación en Azure.</span><span class="sxs-lookup"><span data-stu-id="167ff-351">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="167ff-352">Las identidades administradas no requieren almacenar un certificado en la aplicación ni en el entorno de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="167ff-352">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="167ff-353">La aplicación de ejemplo usa un identificador de `#define` aplicación y un certificado X.509 cuando la instrucción en la parte superior del archivo *de Program.cs* se establece en `Certificate`.</span><span class="sxs-lookup"><span data-stu-id="167ff-353">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="167ff-354">Cree un certificado de archivo PKCS-12 (*.pfx*).</span><span class="sxs-lookup"><span data-stu-id="167ff-354">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="167ff-355">Las opciones para crear certificados incluyen [MakeCert en Windows](/windows/desktop/seccrypto/makecert) y [OpenSSL](https://www.openssl.org/).</span><span class="sxs-lookup"><span data-stu-id="167ff-355">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="167ff-356">Instale el certificado en el almacén de certificados personal del usuario actual.</span><span class="sxs-lookup"><span data-stu-id="167ff-356">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="167ff-357">Marcar la clave como exportable es opcional.</span><span class="sxs-lookup"><span data-stu-id="167ff-357">Marking the key as exportable is optional.</span></span> <span data-ttu-id="167ff-358">Observe la huella digital del certificado, que se utiliza más adelante en este proceso.</span><span class="sxs-lookup"><span data-stu-id="167ff-358">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="167ff-359">Exporte el certificado de archivo PKCS-12 (*.pfx*) como certificado codificado con DER (*.cer*).</span><span class="sxs-lookup"><span data-stu-id="167ff-359">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="167ff-360">Registre la aplicación con Azure AD (**Registros**de aplicaciones ).</span><span class="sxs-lookup"><span data-stu-id="167ff-360">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="167ff-361">Cargue el certificado con codificación DER (*.cer*) en Azure AD:</span><span class="sxs-lookup"><span data-stu-id="167ff-361">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="167ff-362">Seleccione la aplicación en Azure AD.</span><span class="sxs-lookup"><span data-stu-id="167ff-362">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="167ff-363">Vaya a **Certificados & secretos.**</span><span class="sxs-lookup"><span data-stu-id="167ff-363">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="167ff-364">Seleccione **Cargar certificado** para cargar el certificado, que contiene la clave pública.</span><span class="sxs-lookup"><span data-stu-id="167ff-364">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="167ff-365">Un certificado *.cer*, *.pem*o *.crt* es aceptable.</span><span class="sxs-lookup"><span data-stu-id="167ff-365">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="167ff-366">Almacene el nombre del almacén de claves, el identificador de aplicación y la huella digital del certificado en el archivo *appsettings.json* de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="167ff-366">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="167ff-367">Vaya a Almacenes de claves en Azure Portal.Navigate a Almacenes de claves en Azure Portal.Navigate a **Almacenes** de claves en Azure Portal.Navigate to</span><span class="sxs-lookup"><span data-stu-id="167ff-367">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="167ff-368">Seleccione el almacén de claves que creó en la sección [Almacenamiento secreto en el entorno de producción con Azure Key Vault.](#secret-storage-in-the-production-environment-with-azure-key-vault)</span><span class="sxs-lookup"><span data-stu-id="167ff-368">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="167ff-369">Seleccione **Directivas**de acceso .</span><span class="sxs-lookup"><span data-stu-id="167ff-369">Select **Access policies**.</span></span>
1. <span data-ttu-id="167ff-370">Seleccione **Agregar directiva de acceso**.</span><span class="sxs-lookup"><span data-stu-id="167ff-370">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="167ff-371">Abra **Permisos secretos** y proporcione a la aplicación permisos **Obtener** y **Lista.**</span><span class="sxs-lookup"><span data-stu-id="167ff-371">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="167ff-372">Seleccione **Seleccionar entidad de seguridad** y seleccione la aplicación registrada por nombre.</span><span class="sxs-lookup"><span data-stu-id="167ff-372">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="167ff-373">Seleccione el botón **Seleccionar**.</span><span class="sxs-lookup"><span data-stu-id="167ff-373">Select the **Select** button.</span></span>
1. <span data-ttu-id="167ff-374">Seleccione **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="167ff-374">Select **OK**.</span></span>
1. <span data-ttu-id="167ff-375">Seleccione **Guardar**.</span><span class="sxs-lookup"><span data-stu-id="167ff-375">Select **Save**.</span></span>
1. <span data-ttu-id="167ff-376">Implemente la aplicación.</span><span class="sxs-lookup"><span data-stu-id="167ff-376">Deploy the app.</span></span>

<span data-ttu-id="167ff-377">La `Certificate` aplicación de ejemplo obtiene `IConfigurationRoot` sus valores de configuración con el mismo nombre que el nombre secreto:</span><span class="sxs-lookup"><span data-stu-id="167ff-377">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="167ff-378">Valores no jerárquicos: el valor para `SecretName` se obtiene con `config["SecretName"]`.</span><span class="sxs-lookup"><span data-stu-id="167ff-378">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="167ff-379">Valores jerárquicos (secciones): utilice `:` la notación (colon) o el `GetSection` método de extensión.</span><span class="sxs-lookup"><span data-stu-id="167ff-379">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="167ff-380">Utilice cualquiera de estos enfoques para obtener el valor de configuración:</span><span class="sxs-lookup"><span data-stu-id="167ff-380">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="167ff-381">El certificado X.509 es administrado por el sistema operativo.</span><span class="sxs-lookup"><span data-stu-id="167ff-381">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="167ff-382">La aplicación <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> llama con los valores proporcionados por el archivo *appsettings.json:*</span><span class="sxs-lookup"><span data-stu-id="167ff-382">The app calls <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="167ff-383">Valores de ejemplo:</span><span class="sxs-lookup"><span data-stu-id="167ff-383">Example values:</span></span>

* <span data-ttu-id="167ff-384">Nombre del almacén de claves:`contosovault`</span><span class="sxs-lookup"><span data-stu-id="167ff-384">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="167ff-385">ID de aplicación:`627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="167ff-385">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="167ff-386">Huella digital del certificado:`fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="167ff-386">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="167ff-387">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="167ff-387">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/2.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="167ff-388">Al ejecutar la aplicación, una página web muestra los valores secretos cargados.</span><span class="sxs-lookup"><span data-stu-id="167ff-388">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="167ff-389">En el entorno de desarrollo, `_dev` los valores secretos se cargan con el sufijo.</span><span class="sxs-lookup"><span data-stu-id="167ff-389">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="167ff-390">En el entorno de producción, `_prod` los valores se cargan con el sufijo.</span><span class="sxs-lookup"><span data-stu-id="167ff-390">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="167ff-391">Usar identidades administradas para recursos de Azure</span><span class="sxs-lookup"><span data-stu-id="167ff-391">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="167ff-392">**Una aplicación implementada en Azure** puede aprovechar las [identidades administradas para los recursos](/azure/active-directory/managed-identities-azure-resources/overview)de Azure, lo que permite que la aplicación se autentique con Azure Key Vault mediante la autenticación de Azure AD sin credenciales (identificador de aplicación y contraseña/secreto de cliente) almacenadaen en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="167ff-392">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="167ff-393">La aplicación de ejemplo usa identidades `#define` administradas para recursos de Azure `Managed`cuando la instrucción en la parte superior del archivo *Program.cs* se establece en .</span><span class="sxs-lookup"><span data-stu-id="167ff-393">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="167ff-394">Escriba el nombre del almacén en el archivo *appsettings.json* de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="167ff-394">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="167ff-395">La aplicación de ejemplo no requiere un identificador de aplicación `Managed` y una contraseña (secreto de cliente) cuando se establece en la versión, por lo que puede omitir esas entradas de configuración.</span><span class="sxs-lookup"><span data-stu-id="167ff-395">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="167ff-396">La aplicación se implementa en Azure y Azure autentica la aplicación para tener acceso a Azure Key Vault solo con el nombre del almacén almacenado en el archivo *appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="167ff-396">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="167ff-397">Implemente la aplicación de ejemplo en Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="167ff-397">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="167ff-398">Una aplicación implementada en Azure App Service se registra automáticamente con Azure AD cuando se crea el servicio.</span><span class="sxs-lookup"><span data-stu-id="167ff-398">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="167ff-399">Obtenga el identificador de objeto de la implementación para su uso en el siguiente comando.</span><span class="sxs-lookup"><span data-stu-id="167ff-399">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="167ff-400">El identificador de objeto se muestra en Azure Portal en el panel **Identidad** de App Service.</span><span class="sxs-lookup"><span data-stu-id="167ff-400">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="167ff-401">Con la CLI de Azure y el identificador `list` `get` de objeto de la aplicación, proporcione a la aplicación y permisos para tener acceso al almacén de claves:</span><span class="sxs-lookup"><span data-stu-id="167ff-401">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="167ff-402">**Reinicie la aplicación** con la CLI de Azure, PowerShell o Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="167ff-402">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="167ff-403">La aplicación de ejemplo:</span><span class="sxs-lookup"><span data-stu-id="167ff-403">The sample app:</span></span>

* <span data-ttu-id="167ff-404">Crea una instancia `AzureServiceTokenProvider` de la clase sin una cadena de conexión.</span><span class="sxs-lookup"><span data-stu-id="167ff-404">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="167ff-405">Cuando no se proporciona una cadena de conexión, el proveedor intenta obtener un token de acceso de identidades administradas para recursos de Azure.</span><span class="sxs-lookup"><span data-stu-id="167ff-405">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="167ff-406">Se <xref:Microsoft.Azure.KeyVault.KeyVaultClient> crea un `AzureServiceTokenProvider` nuevo con la devolución de llamada del token de instancia.</span><span class="sxs-lookup"><span data-stu-id="167ff-406">A new <xref:Microsoft.Azure.KeyVault.KeyVaultClient> is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="167ff-407">La <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instancia se utiliza con <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> una implementación predeterminada que carga todos`--`los valores`:`secretos y reemplaza double-dashes ( ) con dos puntos ( ) en los nombres de clave.</span><span class="sxs-lookup"><span data-stu-id="167ff-407">The <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance is used with a default implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="167ff-408">Valor de ejemplo del nombre del almacén de claves:`contosovault`</span><span class="sxs-lookup"><span data-stu-id="167ff-408">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="167ff-409">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="167ff-409">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="167ff-410">Al ejecutar la aplicación, una página web muestra los valores secretos cargados.</span><span class="sxs-lookup"><span data-stu-id="167ff-410">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="167ff-411">En el entorno de desarrollo, los valores secretos tienen el `_dev` sufijo porque los proporcionan los secretos de usuario.</span><span class="sxs-lookup"><span data-stu-id="167ff-411">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="167ff-412">En el entorno de producción, `_prod` los valores se cargan con el sufijo porque Azure Key Vault los proporciona.</span><span class="sxs-lookup"><span data-stu-id="167ff-412">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="167ff-413">Si recibe `Access denied` un error, confirme que la aplicación está registrada en Azure AD y que proporciona acceso al almacén de claves.</span><span class="sxs-lookup"><span data-stu-id="167ff-413">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="167ff-414">Confirme que ha reiniciado el servicio en Azure.Confirme que ha reiniciado el servicio en Azure.Confirm que ha reiniciado el servicio en Azure.Confirm que ha reiniciado el servicio en</span><span class="sxs-lookup"><span data-stu-id="167ff-414">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="167ff-415">Para obtener información sobre el uso del proveedor con una identidad administrada y una canalización de VSTS, consulte Creación de una conexión de servicio de Azure Resource Manager a una máquina virtual con una identidad de [servicio administrada.](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity)</span><span class="sxs-lookup"><span data-stu-id="167ff-415">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="167ff-416">Utilice un prefijo de nombre de clave</span><span class="sxs-lookup"><span data-stu-id="167ff-416">Use a key name prefix</span></span>

<span data-ttu-id="167ff-417"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>proporciona una sobrecarga que acepta <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>una implementación de , lo que le permite controlar cómo se convierten los secretos del almacén de claves en claves de configuración.</span><span class="sxs-lookup"><span data-stu-id="167ff-417"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> provides an overload that accepts an implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="167ff-418">Por ejemplo, puede implementar la interfaz para cargar valores secretos basados en un valor de prefijo que proporcione al iniciar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="167ff-418">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="167ff-419">Esto le permite, por ejemplo, cargar secretos basados en la versión de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="167ff-419">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="167ff-420">No use prefijos en los secretos del almacén de claves para colocar secretos para varias aplicaciones en el mismo almacén de claves o para colocar secretos de entorno (por ejemplo, *el desarrollo* frente a los secretos de *producción)* en el mismo almacén.</span><span class="sxs-lookup"><span data-stu-id="167ff-420">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="167ff-421">Se recomienda que las diferentes aplicaciones y entornos de desarrollo/producción usen almacenes de claves independientes para aislar los entornos de aplicaciones para obtener el nivel más alto de seguridad.</span><span class="sxs-lookup"><span data-stu-id="167ff-421">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="167ff-422">En el ejemplo siguiente, se establece un secreto en el almacén de `5000-AppSecret` claves (y mediante la herramienta Secret Manager para el entorno de desarrollo) para (no se permiten períodos en los nombres de secreto del almacén de claves).</span><span class="sxs-lookup"><span data-stu-id="167ff-422">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="167ff-423">Este secreto representa un secreto de aplicación para la versión 5.0.0.0 de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="167ff-423">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="167ff-424">Para otra versión de la aplicación, 5.1.0.0, se agrega un secreto al `5100-AppSecret`almacén de claves (y mediante la herramienta Secret Manager) para .</span><span class="sxs-lookup"><span data-stu-id="167ff-424">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="167ff-425">Cada versión de la aplicación carga `AppSecret`su valor secreto versionado en su configuración como , eliminando la versión a medida que carga el secreto.</span><span class="sxs-lookup"><span data-stu-id="167ff-425">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="167ff-426"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>se llama con <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>una costumbre:</span><span class="sxs-lookup"><span data-stu-id="167ff-426"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> is called with a custom <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="167ff-427">La <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementación reacciona a los prefijos de versión de los secretos para cargar el secreto adecuado en la configuración:</span><span class="sxs-lookup"><span data-stu-id="167ff-427">The <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="167ff-428">`Load`carga un secreto cuando su nombre comienza con el prefijo.</span><span class="sxs-lookup"><span data-stu-id="167ff-428">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="167ff-429">Otros secretos no están cargados.</span><span class="sxs-lookup"><span data-stu-id="167ff-429">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="167ff-430">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="167ff-430">`GetKey`:</span></span>
  * <span data-ttu-id="167ff-431">Quita el prefijo del nombre secreto.</span><span class="sxs-lookup"><span data-stu-id="167ff-431">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="167ff-432">Reemplaza dos guiones en cualquier `KeyDelimiter`nombre con el , que es el delimitador utilizado en la configuración (normalmente dos puntos).</span><span class="sxs-lookup"><span data-stu-id="167ff-432">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="167ff-433">Azure Key Vault no permite dos puntos en nombres secretos.</span><span class="sxs-lookup"><span data-stu-id="167ff-433">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="167ff-434">El `Load` método es llamado por un algoritmo de proveedor que recorre en iteración los secretos del almacén para encontrar los que tienen el prefijo de versión.</span><span class="sxs-lookup"><span data-stu-id="167ff-434">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="167ff-435">Cuando se encuentra un `Load`prefijo de `GetKey` versión con , el algoritmo utiliza el método para devolver el nombre de configuración del nombre secreto.</span><span class="sxs-lookup"><span data-stu-id="167ff-435">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="167ff-436">Elimina el prefijo de versión del nombre del secreto y devuelve el resto del nombre secreto para cargarlo en los pares nombre-valor de configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="167ff-436">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="167ff-437">Cuando se implementa este enfoque:</span><span class="sxs-lookup"><span data-stu-id="167ff-437">When this approach is implemented:</span></span>

1. <span data-ttu-id="167ff-438">La versión de la aplicación especificada en el archivo de proyecto de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="167ff-438">The app's version specified in the app's project file.</span></span> <span data-ttu-id="167ff-439">En el ejemplo siguiente, la versión `5.0.0.0`de la aplicación se establece en:</span><span class="sxs-lookup"><span data-stu-id="167ff-439">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="167ff-440">Confirme que `<UserSecretsId>` una propiedad está presente en el `{GUID}` archivo de proyecto de la aplicación, donde hay un GUID proporcionado por el usuario:</span><span class="sxs-lookup"><span data-stu-id="167ff-440">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="167ff-441">Guarde los siguientes secretos localmente con la [herramienta Secret Manager:](xref:security/app-secrets)</span><span class="sxs-lookup"><span data-stu-id="167ff-441">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="167ff-442">Los secretos se guardan en Azure Key Vault mediante los siguientes comandos de la CLI de Azure:</span><span class="sxs-lookup"><span data-stu-id="167ff-442">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="167ff-443">Cuando se ejecuta la aplicación, se cargan los secretos del almacén de claves.</span><span class="sxs-lookup"><span data-stu-id="167ff-443">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="167ff-444">El secreto `5000-AppSecret` de cadena para coincide con la versión de la`5.0.0.0`aplicación especificada en el archivo de proyecto de la aplicación ( ).</span><span class="sxs-lookup"><span data-stu-id="167ff-444">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="167ff-445">La versión, `5000` (con el guión), se elimina del nombre de clave.</span><span class="sxs-lookup"><span data-stu-id="167ff-445">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="167ff-446">En toda la aplicación, la `AppSecret` lectura de la configuración con la clave carga el valor secreto.</span><span class="sxs-lookup"><span data-stu-id="167ff-446">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="167ff-447">Si se cambia la versión de `5.1.0.0` la aplicación en el archivo de `5.1.0.0_secret_value_dev` proyecto y se `5.1.0.0_secret_value_prod` vuelve a ejecutar la aplicación, el valor secreto devuelto es en el entorno de desarrollo y en producción.</span><span class="sxs-lookup"><span data-stu-id="167ff-447">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="167ff-448">También puede proporcionar <xref:Microsoft.Azure.KeyVault.KeyVaultClient> su <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>propia implementación a .</span><span class="sxs-lookup"><span data-stu-id="167ff-448">You can also provide your own <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementation to <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>.</span></span> <span data-ttu-id="167ff-449">Un cliente personalizado permite compartir una única instancia del cliente en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="167ff-449">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="167ff-450">Enlace de una matriz a una clase</span><span class="sxs-lookup"><span data-stu-id="167ff-450">Bind an array to a class</span></span>

<span data-ttu-id="167ff-451">El proveedor es capaz de leer valores de configuración en una matriz para enlazar a una matriz POCO.</span><span class="sxs-lookup"><span data-stu-id="167ff-451">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="167ff-452">Al leer desde un origen de configuración`:`que permite que las claves contengan separadores de dos`:0:` `:1:`puntos ( ), se utiliza un segmento de clave numérica para distinguir las claves que componen una matriz ( , , &hellip; `:{n}:`).</span><span class="sxs-lookup"><span data-stu-id="167ff-452">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="167ff-453">Para obtener más información, consulte [Configuración: enlazar una matriz a una clase](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="167ff-453">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="167ff-454">Las claves de Azure Key Vault no pueden usar dos puntos como separador.</span><span class="sxs-lookup"><span data-stu-id="167ff-454">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="167ff-455">El enfoque descrito en este tema`--`utiliza guiones dobles ( ) como separador para valores jerárquicos (secciones).</span><span class="sxs-lookup"><span data-stu-id="167ff-455">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="167ff-456">Las claves de matriz se almacenan en Azure Key`--0--` `--1--`Vault &hellip; `--{n}--`con guiones dobles y segmentos de clave numérica ( , , ).</span><span class="sxs-lookup"><span data-stu-id="167ff-456">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="167ff-457">Examine la siguiente configuración del proveedor de registro [de Serilog](https://serilog.net/) proporcionada por un archivo JSON.</span><span class="sxs-lookup"><span data-stu-id="167ff-457">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="167ff-458">Hay dos literales de `WriteTo` objeto definidos en la matriz que reflejan dos *receptores*serilog, que describen los destinos para la salida de registro:</span><span class="sxs-lookup"><span data-stu-id="167ff-458">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

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

<span data-ttu-id="167ff-459">La configuración que se muestra en el archivo JSON anterior`--`se almacena en Azure Key Vault mediante la notación de doble guión ( ) y segmentos numéricos:</span><span class="sxs-lookup"><span data-stu-id="167ff-459">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="167ff-460">Clave</span><span class="sxs-lookup"><span data-stu-id="167ff-460">Key</span></span> | <span data-ttu-id="167ff-461">Value</span><span class="sxs-lookup"><span data-stu-id="167ff-461">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="167ff-462">Recargar secretos</span><span class="sxs-lookup"><span data-stu-id="167ff-462">Reload secrets</span></span>

<span data-ttu-id="167ff-463">Los secretos `IConfigurationRoot.Reload()` se almacenan en caché hasta que se llama.</span><span class="sxs-lookup"><span data-stu-id="167ff-463">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="167ff-464">La aplicación no respeta los secretos caducados, deshabilitados y actualizados en el almacén de claves hasta que `Reload` se ejecute.</span><span class="sxs-lookup"><span data-stu-id="167ff-464">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="167ff-465">Secretos discapacitados y caducados</span><span class="sxs-lookup"><span data-stu-id="167ff-465">Disabled and expired secrets</span></span>

<span data-ttu-id="167ff-466">Los secretos deshabilitados y caducados lanzan un <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>archivo .</span><span class="sxs-lookup"><span data-stu-id="167ff-466">Disabled and expired secrets throw a <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>.</span></span> <span data-ttu-id="167ff-467">Para evitar que la aplicación se inicie, proporcione la configuración con un proveedor de configuración diferente o actualice el secreto deshabilitado o caducado.</span><span class="sxs-lookup"><span data-stu-id="167ff-467">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="167ff-468">Solución de problemas</span><span class="sxs-lookup"><span data-stu-id="167ff-468">Troubleshoot</span></span>

<span data-ttu-id="167ff-469">Cuando la aplicación no puede cargar la configuración mediante el proveedor, se escribe un mensaje de error en la infraestructura de [registro de núcleo de ASP.NET.](xref:fundamentals/logging/index)</span><span class="sxs-lookup"><span data-stu-id="167ff-469">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="167ff-470">Las siguientes condiciones impedirán que se cargue la configuración:</span><span class="sxs-lookup"><span data-stu-id="167ff-470">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="167ff-471">La aplicación o el certificado no están configurados correctamente en Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="167ff-471">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="167ff-472">El almacén de claves no existe en Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="167ff-472">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="167ff-473">La aplicación no está autorizada para acceder al almacén de claves.</span><span class="sxs-lookup"><span data-stu-id="167ff-473">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="167ff-474">La política de acceso `Get` `List` no incluye ni los permisos.</span><span class="sxs-lookup"><span data-stu-id="167ff-474">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="167ff-475">En el almacén de claves, los datos de configuración (par nombre-valor) se nombran incorrectamente, faltan, se deshabilitan o caducan.</span><span class="sxs-lookup"><span data-stu-id="167ff-475">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="167ff-476">La aplicación tiene el nombre`KeyVaultName`de almacén`AzureADApplicationId`de claves incorrecto (`AzureADCertThumbprint`), el identificador de aplicación de Azure AD ( ) o la huella digital del certificado de Azure AD ( ).</span><span class="sxs-lookup"><span data-stu-id="167ff-476">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="167ff-477">La clave de configuración (nombre) es incorrecta en la aplicación para el valor que está intentando cargar.</span><span class="sxs-lookup"><span data-stu-id="167ff-477">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="167ff-478">Al agregar la directiva de acceso de la aplicación al almacén de claves, se creó la directiva, pero el botón **Guardar** no se seleccionó en la interfaz de usuario **de directivas** de acceso.</span><span class="sxs-lookup"><span data-stu-id="167ff-478">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="167ff-479">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="167ff-479">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="167ff-480">Microsoft Azure: Key Vault</span><span class="sxs-lookup"><span data-stu-id="167ff-480">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="167ff-481">Documentación de Microsoft Azure: Key Vault</span><span class="sxs-lookup"><span data-stu-id="167ff-481">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="167ff-482">Generación y transferencia de claves protegidas con HSM para Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="167ff-482">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="167ff-483">Clase KeyVaultClient</span><span class="sxs-lookup"><span data-stu-id="167ff-483">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="167ff-484">Guía de inicio rápido: Establecimiento y recuperación de un secreto de Azure Key Vault mediante una aplicación web de .NET</span><span class="sxs-lookup"><span data-stu-id="167ff-484">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="167ff-485">Tutorial: Uso de Azure Key Vault con una máquina virtual Windows en .NET</span><span class="sxs-lookup"><span data-stu-id="167ff-485">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

