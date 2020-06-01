---
<span data-ttu-id="4eb97-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4eb97-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4eb97-102">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="4eb97-102">'Blazor'</span></span>
- <span data-ttu-id="4eb97-103">"Identity"</span><span class="sxs-lookup"><span data-stu-id="4eb97-103">'Identity'</span></span>
- <span data-ttu-id="4eb97-104">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="4eb97-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="4eb97-105">"Razor"</span><span class="sxs-lookup"><span data-stu-id="4eb97-105">'Razor'</span></span>
- <span data-ttu-id="4eb97-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4eb97-106">'SignalR' uid:</span></span> 

---
# <a name="host-aspnet-core-in-a-windows-service"></a><span data-ttu-id="4eb97-107">Hospedaje de ASP.NET Core en un servicio de Windows</span><span class="sxs-lookup"><span data-stu-id="4eb97-107">Host ASP.NET Core in a Windows Service</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4eb97-108">Una aplicación de ASP.NET Core se puede hospedar en Windows sin usar IIS como [servicio de Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications).</span><span class="sxs-lookup"><span data-stu-id="4eb97-108">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="4eb97-109">Cuando se hospeda como un servicio de Windows, la aplicación se inicia automáticamente después de reiniciar el servidor.</span><span class="sxs-lookup"><span data-stu-id="4eb97-109">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="4eb97-110">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="4eb97-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="4eb97-111">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="4eb97-111">Prerequisites</span></span>

* [<span data-ttu-id="4eb97-112">ASP.NET Core SDK 2.1 o posterior</span><span class="sxs-lookup"><span data-stu-id="4eb97-112">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="4eb97-113">PowerShell 6.2 o posterior</span><span class="sxs-lookup"><span data-stu-id="4eb97-113">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="worker-service-template"></a><span data-ttu-id="4eb97-114">Plantilla Worker Service</span><span class="sxs-lookup"><span data-stu-id="4eb97-114">Worker Service template</span></span>

<span data-ttu-id="4eb97-115">La plantilla Worker Service de ASP.NET Core sirve de punto de partida para escribir aplicaciones de servicio de larga duración.</span><span class="sxs-lookup"><span data-stu-id="4eb97-115">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="4eb97-116">Para usar la plantilla como base de una aplicación de servicio de Windows:</span><span class="sxs-lookup"><span data-stu-id="4eb97-116">To use the template as a basis for a Windows Service app:</span></span>

1. <span data-ttu-id="4eb97-117">Cree una aplicación Worker Service con la plantilla de .NET Core.</span><span class="sxs-lookup"><span data-stu-id="4eb97-117">Create a Worker Service app from the .NET Core template.</span></span>
1. <span data-ttu-id="4eb97-118">Siga las instrucciones de la sección [Configuración de aplicaciones](#app-configuration) para actualizar la aplicación Worker Service a fin de que se ejecute como un servicio de Windows.</span><span class="sxs-lookup"><span data-stu-id="4eb97-118">Follow the guidance in the [App configuration](#app-configuration) section to update the Worker Service app so that it can run as a Windows Service.</span></span>

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="app-configuration"></a><span data-ttu-id="4eb97-119">Configuración de aplicaciones</span><span class="sxs-lookup"><span data-stu-id="4eb97-119">App configuration</span></span>

<span data-ttu-id="4eb97-120">La aplicación requiere una referencia de paquete para [Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices).</span><span class="sxs-lookup"><span data-stu-id="4eb97-120">The app requires a package reference for [Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices).</span></span>

<span data-ttu-id="4eb97-121">Se llama a `IHostBuilder.UseWindowsService` al compilar el host.</span><span class="sxs-lookup"><span data-stu-id="4eb97-121">`IHostBuilder.UseWindowsService` is called when building the host.</span></span> <span data-ttu-id="4eb97-122">Si la aplicación se ejecuta como un servicio de Windows, el método:</span><span class="sxs-lookup"><span data-stu-id="4eb97-122">If the app is running as a Windows Service, the method:</span></span>

* <span data-ttu-id="4eb97-123">Establece la vigencia del host en `WindowsServiceLifetime`.</span><span class="sxs-lookup"><span data-stu-id="4eb97-123">Sets the host lifetime to `WindowsServiceLifetime`.</span></span>
* <span data-ttu-id="4eb97-124">Establece la [raíz del contenido](xref:fundamentals/index#content-root) en [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span><span class="sxs-lookup"><span data-stu-id="4eb97-124">Sets the [content root](xref:fundamentals/index#content-root) to [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span></span> <span data-ttu-id="4eb97-125">Para obtener más información, consulte la sección [Directorio actual y raíz del contenido](#current-directory-and-content-root).</span><span class="sxs-lookup"><span data-stu-id="4eb97-125">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span>
* <span data-ttu-id="4eb97-126">Habilita el registro en el registro de eventos:</span><span class="sxs-lookup"><span data-stu-id="4eb97-126">Enables logging to the event log:</span></span>
  * <span data-ttu-id="4eb97-127">El nombre de la aplicación se usa como nombre de origen predeterminado.</span><span class="sxs-lookup"><span data-stu-id="4eb97-127">The application name is used as the default source name.</span></span>
  * <span data-ttu-id="4eb97-128">El nivel de registro predeterminado es *Advertencia* o superior para una aplicación basada en una plantilla de ASP.NET Core que llama a `CreateDefaultBuilder` con el fin de compilar el host.</span><span class="sxs-lookup"><span data-stu-id="4eb97-128">The default log level is *Warning* or higher for an app based on an ASP.NET Core template that calls `CreateDefaultBuilder` to build the host.</span></span>
  * <span data-ttu-id="4eb97-129">Invalide el nivel de registro predeterminado con la clave `Logging:EventLog:LogLevel:Default` en *appsettings.json*/*appsettings.{Environment}.json* u otro proveedor de configuración.</span><span class="sxs-lookup"><span data-stu-id="4eb97-129">Override the default log level with the `Logging:EventLog:LogLevel:Default` key in *appsettings.json*/*appsettings.{Environment}.json* or other configuration provider.</span></span>
  * <span data-ttu-id="4eb97-130">Los administradores son los únicos que pueden crear nuevos orígenes de eventos.</span><span class="sxs-lookup"><span data-stu-id="4eb97-130">Only administrators can create new event sources.</span></span> <span data-ttu-id="4eb97-131">Cuando no se puede crear un origen de eventos con el nombre de la aplicación, se registra una advertencia para el origen *Aplicación* y los registros de eventos se deshabilitan.</span><span class="sxs-lookup"><span data-stu-id="4eb97-131">When an event source can't be created using the application name, a warning is logged to the *Application* source and event logs are disabled.</span></span>

<span data-ttu-id="4eb97-132">En `CreateHostBuilder` de *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="4eb97-132">In `CreateHostBuilder` of *Program.cs*:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseWindowsService()
    ...
```

<span data-ttu-id="4eb97-133">Las aplicaciones de ejemplo siguientes acompañan a este tema:</span><span class="sxs-lookup"><span data-stu-id="4eb97-133">The following sample apps accompany this topic:</span></span>

* <span data-ttu-id="4eb97-134">Ejemplo de Background Worker Service: un ejemplo de una aplicación que no es para la Web basado en la [plantilla Worker Service](#worker-service-template) que usa [servicios hospedados](xref:fundamentals/host/hosted-services) para las tareas en segundo plano.</span><span class="sxs-lookup"><span data-stu-id="4eb97-134">Background Worker Service Sample: A non-web app sample based on the [Worker Service template](#worker-service-template) that uses [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>
* <span data-ttu-id="4eb97-135">Ejemplo de App Service web: un ejemplo de aplicación web de Razor Pages que se ejecuta como un servicio de Windows con [servicios hospedados](xref:fundamentals/host/hosted-services) para las tareas en segundo plano.</span><span class="sxs-lookup"><span data-stu-id="4eb97-135">Web App Service Sample: A Razor Pages web app sample that runs as a Windows Service with [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>

<span data-ttu-id="4eb97-136">Para obtener instrucciones sobre MVC, vea los artículos en <xref:mvc/overview> y <xref:migration/22-to-30>.</span><span class="sxs-lookup"><span data-stu-id="4eb97-136">For MVC guidance, see the articles under <xref:mvc/overview> and <xref:migration/22-to-30>.</span></span>

## <a name="deployment-type"></a><span data-ttu-id="4eb97-137">Tipo de implementación</span><span class="sxs-lookup"><span data-stu-id="4eb97-137">Deployment type</span></span>

<span data-ttu-id="4eb97-138">Para obtener información y consejos sobre los escenarios de implementación, consulte [Implementación de aplicaciones .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="4eb97-138">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="4eb97-139">SDK</span><span class="sxs-lookup"><span data-stu-id="4eb97-139">SDK</span></span>

<span data-ttu-id="4eb97-140">En el caso de un servicio basado en una aplicación web que use los marcos Razor Pages o MVC, especifique el SDK web en el archivo de proyecto:</span><span class="sxs-lookup"><span data-stu-id="4eb97-140">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="4eb97-141">Si el servicio solo ejecuta tareas en segundo plano (por ejemplo, [servicios hospedados](xref:fundamentals/host/hosted-services)), especifique el SDK de Worker en el archivo de proyecto:</span><span class="sxs-lookup"><span data-stu-id="4eb97-141">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="4eb97-142">Implementación dependiente de marco (FDD)</span><span class="sxs-lookup"><span data-stu-id="4eb97-142">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="4eb97-143">La implementación dependiente de marco de trabajo (FDD) se basa en la presencia de una versión compartida de .NET Core en todo el sistema en el sistema de destino.</span><span class="sxs-lookup"><span data-stu-id="4eb97-143">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="4eb97-144">Cuando se adopta el escenario FDD siguiendo las instrucciones de este artículo, el SDK genera un archivo ejecutable ( *.exe*), denominado *ejecutable dependiente del marco*.</span><span class="sxs-lookup"><span data-stu-id="4eb97-144">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="4eb97-145">Si se usa el [SDK web](#sdk), para una aplicación de Windows Services no se requiere un archivo *web.config*, que normalmente se crea cuando se publica una aplicación ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4eb97-145">If using the [Web SDK](#sdk), a *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="4eb97-146">Para deshabilitar la creación de un archivo *web.config* agregue la propiedad `<IsTransformWebConfigDisabled>` establecida en `true`.</span><span class="sxs-lookup"><span data-stu-id="4eb97-146">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp3.0</TargetFramework>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="4eb97-147">Implementación autocontenida (SCD)</span><span class="sxs-lookup"><span data-stu-id="4eb97-147">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="4eb97-148">Una implementación autocontenida (SCD) no depende de la presencia de un marco compartido en el sistema host.</span><span class="sxs-lookup"><span data-stu-id="4eb97-148">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="4eb97-149">El tiempo de ejecución y las dependencias de la aplicación se implementan con la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4eb97-149">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="4eb97-150">Un [identificador en tiempo de ejecución (RID)](/dotnet/core/rid-catalog) se incluye en el `<PropertyGroup>` que contiene la plataforma de destino:</span><span class="sxs-lookup"><span data-stu-id="4eb97-150">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="4eb97-151">Para publicar para varios RID:</span><span class="sxs-lookup"><span data-stu-id="4eb97-151">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="4eb97-152">Proporcione los RID en una lista delimitada por punto y coma.</span><span class="sxs-lookup"><span data-stu-id="4eb97-152">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="4eb97-153">Use el nombre de la propiedad [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span><span class="sxs-lookup"><span data-stu-id="4eb97-153">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="4eb97-154">Para más información, vea el [Catálogo de identificadores de entorno de ejecución (RID) de .NET Core](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="4eb97-154">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

## <a name="service-user-account"></a><span data-ttu-id="4eb97-155">Cuenta de usuario de servicio</span><span class="sxs-lookup"><span data-stu-id="4eb97-155">Service user account</span></span>

<span data-ttu-id="4eb97-156">Para crear una cuenta de usuario para un servicio, use el cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) de un shell de comandos administrativos de PowerShell 6.</span><span class="sxs-lookup"><span data-stu-id="4eb97-156">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="4eb97-157">En la actualización de octubre de 2018 de Windows 10 (versión 1809/compilación 10.0.17763) o posterior:</span><span class="sxs-lookup"><span data-stu-id="4eb97-157">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="4eb97-158">En el sistema operativo Windows anterior a la actualización de octubre de 2018 de Windows 10 (versión 1809/compilación 10.0.17763):</span><span class="sxs-lookup"><span data-stu-id="4eb97-158">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="4eb97-159">Proporcione una [contraseña segura](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) cuando se le solicite.</span><span class="sxs-lookup"><span data-stu-id="4eb97-159">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="4eb97-160">A menos que el parámetro `-AccountExpires` se proporcione para el cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) con una fecha de expiración <xref:System.DateTime>, la cuenta no expirará.</span><span class="sxs-lookup"><span data-stu-id="4eb97-160">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="4eb97-161">Para más información, vea [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) y [Service User Accounts](/windows/desktop/services/service-user-accounts) (Cuentas de usuario del servicio).</span><span class="sxs-lookup"><span data-stu-id="4eb97-161">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="4eb97-162">Un enfoque alternativo de administración de usuarios al usar Active Directory consiste en usar cuentas de servicio administradas.</span><span class="sxs-lookup"><span data-stu-id="4eb97-162">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="4eb97-163">Para obtener más información, consulte [grupo Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview) (Información general sobre cuentas de servicio administradas de grupo).</span><span class="sxs-lookup"><span data-stu-id="4eb97-163">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="4eb97-164">Derechos para iniciar sesión como servicio</span><span class="sxs-lookup"><span data-stu-id="4eb97-164">Log on as a service rights</span></span>

<span data-ttu-id="4eb97-165">Para establecer los derechos de *Iniciar sesión como servicio* para una cuenta de usuario del servicio:</span><span class="sxs-lookup"><span data-stu-id="4eb97-165">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="4eb97-166">Abra el editor de la Directiva de seguridad local mediante la ejecución de *secpol.msc*.</span><span class="sxs-lookup"><span data-stu-id="4eb97-166">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="4eb97-167">Expanda el nodo **Directivas locales** y, después, seleccione **Asignación de derechos de usuario**.</span><span class="sxs-lookup"><span data-stu-id="4eb97-167">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="4eb97-168">Abra la directiva **Iniciar sesión como servicio**.</span><span class="sxs-lookup"><span data-stu-id="4eb97-168">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="4eb97-169">Seleccione **Agregar usuario o grupo**.</span><span class="sxs-lookup"><span data-stu-id="4eb97-169">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="4eb97-170">Proporcione el nombre de objeto (cuenta de usuario) mediante cualquiera de los métodos siguientes:</span><span class="sxs-lookup"><span data-stu-id="4eb97-170">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="4eb97-171">Escriba la cuenta de usuario (`{DOMAIN OR COMPUTER NAME\USER}`) en el campo del nombre de objeto y seleccione **Aceptar** para agregar el usuario a la directiva.</span><span class="sxs-lookup"><span data-stu-id="4eb97-171">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="4eb97-172">Seleccione **Opciones avanzadas**.</span><span class="sxs-lookup"><span data-stu-id="4eb97-172">Select **Advanced**.</span></span> <span data-ttu-id="4eb97-173">Seleccione **Buscar ahora**.</span><span class="sxs-lookup"><span data-stu-id="4eb97-173">Select **Find Now**.</span></span> <span data-ttu-id="4eb97-174">Seleccione la cuenta de usuario de la lista.</span><span class="sxs-lookup"><span data-stu-id="4eb97-174">Select the user account from the list.</span></span> <span data-ttu-id="4eb97-175">Seleccione **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="4eb97-175">Select **OK**.</span></span> <span data-ttu-id="4eb97-176">Seleccione **Aceptar** nuevo para agregar el usuario a la directiva.</span><span class="sxs-lookup"><span data-stu-id="4eb97-176">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="4eb97-177">Seleccione **Aceptar** o **Aplicar** para aceptar los cambios.</span><span class="sxs-lookup"><span data-stu-id="4eb97-177">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="4eb97-178">Creación y administración del servicio de Windows</span><span class="sxs-lookup"><span data-stu-id="4eb97-178">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="4eb97-179">Creación de un servicio</span><span class="sxs-lookup"><span data-stu-id="4eb97-179">Create a service</span></span>

<span data-ttu-id="4eb97-180">Use los comandos de PowerShell para registrar un servicio.</span><span class="sxs-lookup"><span data-stu-id="4eb97-180">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="4eb97-181">Desde un shell de comandos administrativos de PowerShell 6, ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="4eb97-181">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="4eb97-182">`{EXE PATH}`: ruta de acceso a la carpeta de la aplicación en el host (por ejemplo, `d:\myservice`).</span><span class="sxs-lookup"><span data-stu-id="4eb97-182">`{EXE PATH}`: Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="4eb97-183">No incluya el archivo ejecutable de la aplicación en la ruta de acceso.</span><span class="sxs-lookup"><span data-stu-id="4eb97-183">Don't include the app's executable in the path.</span></span> <span data-ttu-id="4eb97-184">No se requiere una barra diagonal al final.</span><span class="sxs-lookup"><span data-stu-id="4eb97-184">A trailing slash isn't required.</span></span>
* <span data-ttu-id="4eb97-185">`{DOMAIN OR COMPUTER NAME\USER}`: cuenta de usuario de servicio (por ejemplo, `Contoso\ServiceUser`).</span><span class="sxs-lookup"><span data-stu-id="4eb97-185">`{DOMAIN OR COMPUTER NAME\USER}`: Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="4eb97-186">`{SERVICE NAME}`: nombre de servicio (por ejemplo, `MyService`).</span><span class="sxs-lookup"><span data-stu-id="4eb97-186">`{SERVICE NAME}`: Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="4eb97-187">`{EXE FILE PATH}`: ruta de acceso ejecutable de la aplicación (por ejemplo, `d:\myservice\myservice.exe`).</span><span class="sxs-lookup"><span data-stu-id="4eb97-187">`{EXE FILE PATH}`: The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="4eb97-188">Incluya el nombre de archivo del ejecutable con la extensión.</span><span class="sxs-lookup"><span data-stu-id="4eb97-188">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="4eb97-189">`{DESCRIPTION}`: descripción del servicio (por ejemplo, `My sample service`).</span><span class="sxs-lookup"><span data-stu-id="4eb97-189">`{DESCRIPTION}`: Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="4eb97-190">`{DISPLAY NAME}`: nombre para mostrar del servicio (por ejemplo, `My Service`).</span><span class="sxs-lookup"><span data-stu-id="4eb97-190">`{DISPLAY NAME}`: Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="4eb97-191">iniciar un servicio.</span><span class="sxs-lookup"><span data-stu-id="4eb97-191">Start a service</span></span>

<span data-ttu-id="4eb97-192">Inicie el servicio con el siguiente comando de PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="4eb97-192">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="4eb97-193">Este comando tarda unos segundos en iniciar el servicio.</span><span class="sxs-lookup"><span data-stu-id="4eb97-193">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="4eb97-194">Determinación del estado de un servicio</span><span class="sxs-lookup"><span data-stu-id="4eb97-194">Determine a service's status</span></span>

<span data-ttu-id="4eb97-195">Para comprobar el estado de un servicio, use el siguiente comando de PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="4eb97-195">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="4eb97-196">El estado se notifica como uno de los siguientes valores:</span><span class="sxs-lookup"><span data-stu-id="4eb97-196">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="4eb97-197">Detención de un servicio</span><span class="sxs-lookup"><span data-stu-id="4eb97-197">Stop a service</span></span>

<span data-ttu-id="4eb97-198">Detenga un servicio con el siguiente comando de PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="4eb97-198">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="4eb97-199">Eliminación de un servicio</span><span class="sxs-lookup"><span data-stu-id="4eb97-199">Remove a service</span></span>

<span data-ttu-id="4eb97-200">Tras un breve intervalo para detener un servicio, elimine el servicio con el siguiente comando de PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="4eb97-200">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="4eb97-201">Escenarios de servidor proxy y equilibrador de carga</span><span class="sxs-lookup"><span data-stu-id="4eb97-201">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="4eb97-202">Los servicios que interactúan con las solicitudes de Internet o de una red corporativa y están detrás de un proxy o de un equilibrador de carga podrían requerir configuración adicional.</span><span class="sxs-lookup"><span data-stu-id="4eb97-202">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="4eb97-203">Para obtener más información, vea <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="4eb97-203">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="4eb97-204">Configuración de puntos de conexión</span><span class="sxs-lookup"><span data-stu-id="4eb97-204">Configure endpoints</span></span>

<span data-ttu-id="4eb97-205">ASP.NET Core se enlaza a `http://localhost:5000` de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="4eb97-205">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="4eb97-206">Configure la dirección URL y el puerto estableciendo la variable de entorno `ASPNETCORE_URLS`.</span><span class="sxs-lookup"><span data-stu-id="4eb97-206">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="4eb97-207">Para información sobre otros enfoques de configuración de direcciones URL y puertos, consulte el artículo en cuestión:</span><span class="sxs-lookup"><span data-stu-id="4eb97-207">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="4eb97-208">En la guía anterior se trata la compatibilidad con los puntos de conexión HTTPS.</span><span class="sxs-lookup"><span data-stu-id="4eb97-208">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="4eb97-209">Por ejemplo, configure la aplicación para HTTPS cuando se use la autenticación con un servicio de Windows.</span><span class="sxs-lookup"><span data-stu-id="4eb97-209">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="4eb97-210">No se admite el uso del certificado de desarrollo HTTPS de ASP.NET Core para proteger un punto de conexión de servicio.</span><span class="sxs-lookup"><span data-stu-id="4eb97-210">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="4eb97-211">Directorio actual y raíz del contenido</span><span class="sxs-lookup"><span data-stu-id="4eb97-211">Current directory and content root</span></span>

<span data-ttu-id="4eb97-212">El directorio de trabajo actual devuelto por una llamada a <xref:System.IO.Directory.GetCurrentDirectory*> para un servicio de Windows es la carpeta *C:\\WINDOWS\\system32*.</span><span class="sxs-lookup"><span data-stu-id="4eb97-212">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="4eb97-213">La carpeta *system32* no es una ubicación adecuada para almacenar los archivos de un servicio (por ejemplo los archivos de configuración).</span><span class="sxs-lookup"><span data-stu-id="4eb97-213">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="4eb97-214">Utilice uno de los siguientes enfoques para mantener los archivos de configuración y los activos de un servicio, así como para acceder a ellos.</span><span class="sxs-lookup"><span data-stu-id="4eb97-214">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="use-contentrootpath-or-contentrootfileprovider"></a><span data-ttu-id="4eb97-215">Uso de ContentRootPath o ContentRootFileProvider</span><span class="sxs-lookup"><span data-stu-id="4eb97-215">Use ContentRootPath or ContentRootFileProvider</span></span>

<span data-ttu-id="4eb97-216">Use [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) o <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> para buscar los recursos de una aplicación.</span><span class="sxs-lookup"><span data-stu-id="4eb97-216">Use [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) or <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> to locate an app's resources.</span></span>

<span data-ttu-id="4eb97-217">Cuando la aplicación se ejecuta como un servicio, <xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService*> establece la ruta de acceso <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> en [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span><span class="sxs-lookup"><span data-stu-id="4eb97-217">When the app runs as a service, <xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService*> sets the <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> to [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span></span>

<span data-ttu-id="4eb97-218">Los archivos de configuración predeterminados de la aplicación, *appsettings.json* and *appsettings.{Entorno}.json*, se cargan desde la raíz del contenido de la aplicación mediante una llamada a [CreateDefaultBuilder durante la construcción del host](xref:fundamentals/host/generic-host#set-up-a-host).</span><span class="sxs-lookup"><span data-stu-id="4eb97-218">The app's default settings files, *appsettings.json* and *appsettings.{Environment}.json*, are loaded from the app's content root by calling [CreateDefaultBuilder during host construction](xref:fundamentals/host/generic-host#set-up-a-host).</span></span>

<span data-ttu-id="4eb97-219">En el caso de otros archivos de configuración cargados por el código para desarrolladores en <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>, no es necesario llamar a <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span><span class="sxs-lookup"><span data-stu-id="4eb97-219">For other settings files loaded by developer code in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>, there's no need to call <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="4eb97-220">En el ejemplo siguiente, el archivo *custom_settings.json* ya se encuentra en la raíz del contenido de la aplicación y se carga sin establecer explícitamente una ruta de acceso base:</span><span class="sxs-lookup"><span data-stu-id="4eb97-220">In the following example, the *custom_settings.json* file exists in the app's content root and is loaded without explicitly setting a base path:</span></span>

[!code-csharp[](windows-service/samples_snapshot/CustomSettingsExample.cs?highlight=13)]

<span data-ttu-id="4eb97-221">No intente usar <xref:System.IO.Directory.GetCurrentDirectory*> para obtener una ruta de acceso a recursos porque una aplicación de servicio de Windows devuelve la carpeta *C:\\WINDOWS\\system32* como su directorio actual.</span><span class="sxs-lookup"><span data-stu-id="4eb97-221">Don't attempt to use <xref:System.IO.Directory.GetCurrentDirectory*> to obtain a resource path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder as its current directory.</span></span>

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="4eb97-222">Almacenamiento de los archivos de un servicio en una ubicación adecuada en el disco</span><span class="sxs-lookup"><span data-stu-id="4eb97-222">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="4eb97-223">Especifique una ruta de acceso absoluta con <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> al utilizar un <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> a la carpeta que contiene los archivos.</span><span class="sxs-lookup"><span data-stu-id="4eb97-223">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="4eb97-224">Solucionar problemas</span><span class="sxs-lookup"><span data-stu-id="4eb97-224">Troubleshoot</span></span>

<span data-ttu-id="4eb97-225">Para solucionar problemas de una aplicación de servicio de Windows, consulte <xref:test/troubleshoot>.</span><span class="sxs-lookup"><span data-stu-id="4eb97-225">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="4eb97-226">Errores comunes</span><span class="sxs-lookup"><span data-stu-id="4eb97-226">Common errors</span></span>

* <span data-ttu-id="4eb97-227">Se está usando una versión preliminar o antigua de PowerShell.</span><span class="sxs-lookup"><span data-stu-id="4eb97-227">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="4eb97-228">El servicio registrado no usa la salida **publicada** de la aplicación del comando [dotnet publish](/dotnet/core/tools/dotnet-publish).</span><span class="sxs-lookup"><span data-stu-id="4eb97-228">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="4eb97-229">La salida del comando [dotnet build](/dotnet/core/tools/dotnet-build) no es compatible con la implementación de aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="4eb97-229">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="4eb97-230">Los recursos publicados se encuentran en cualquiera de las siguientes carpetas, según el tipo de implementación:</span><span class="sxs-lookup"><span data-stu-id="4eb97-230">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="4eb97-231">*bin/Release/{PLATAFORMA DE DESTINO}/publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="4eb97-231">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="4eb97-232">*bin/Release/{PLATAFORMA DE DESTINO}/{IDENTIFICADOR DE RUNTIME}/publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="4eb97-232">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="4eb97-233">El servicio no está en el estado EN EJECUCIÓN.</span><span class="sxs-lookup"><span data-stu-id="4eb97-233">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="4eb97-234">Las rutas de acceso a los recursos que usa la aplicación (por ejemplo, certificados) son incorrectas.</span><span class="sxs-lookup"><span data-stu-id="4eb97-234">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="4eb97-235">La ruta de acceso base de un servicio de Windows es *c:\\Windows\\System32*.</span><span class="sxs-lookup"><span data-stu-id="4eb97-235">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="4eb97-236">El usuario no tiene derechos de *inicio de sesión como servicio*.</span><span class="sxs-lookup"><span data-stu-id="4eb97-236">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="4eb97-237">La contraseña del usuario ha expirado o se ha pasado incorrectamente al ejecutar el comando de PowerShell `New-Service`.</span><span class="sxs-lookup"><span data-stu-id="4eb97-237">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="4eb97-238">La aplicación requiere autenticación ASP.NET Core, pero no está configurada para conexiones seguras (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="4eb97-238">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="4eb97-239">El puerto de la dirección URL de solicitud es incorrecto o no está configurado correctamente en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4eb97-239">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="4eb97-240">Registros de eventos del sistema y de aplicación</span><span class="sxs-lookup"><span data-stu-id="4eb97-240">System and Application Event Logs</span></span>

<span data-ttu-id="4eb97-241">Acceda a los registros de eventos del sistema y de aplicación:</span><span class="sxs-lookup"><span data-stu-id="4eb97-241">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="4eb97-242">Abra el menú Inicio, busque *Visor de eventos* y seleccione la aplicación **Visor de eventos**.</span><span class="sxs-lookup"><span data-stu-id="4eb97-242">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="4eb97-243">En **Visor de eventos**, abra el nodo **Registros de Windows**.</span><span class="sxs-lookup"><span data-stu-id="4eb97-243">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="4eb97-244">Seleccione **Sistema** para abrir el registro de eventos del sistema.</span><span class="sxs-lookup"><span data-stu-id="4eb97-244">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="4eb97-245">Seleccione **Aplicación** para abrir el registro de eventos de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4eb97-245">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="4eb97-246">Busque los errores asociados a la aplicación objeto del error.</span><span class="sxs-lookup"><span data-stu-id="4eb97-246">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="4eb97-247">Ejecución de la aplicación en un símbolo del sistema</span><span class="sxs-lookup"><span data-stu-id="4eb97-247">Run the app at a command prompt</span></span>

<span data-ttu-id="4eb97-248">Muchos errores de inicio no generan información útil en el registro de eventos.</span><span class="sxs-lookup"><span data-stu-id="4eb97-248">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="4eb97-249">La causa de algunos errores se puede encontrar mediante la ejecución de la aplicación en un símbolo del sistema en el sistema de hospedaje.</span><span class="sxs-lookup"><span data-stu-id="4eb97-249">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="4eb97-250">Para registrar detalles adicionales de la aplicación, reduzca el [nivel de registro](xref:fundamentals/logging/index#log-level) o ejecute la aplicación en el [entorno de desarrollo](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="4eb97-250">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="4eb97-251">Borrado de memorias caché de paquetes</span><span class="sxs-lookup"><span data-stu-id="4eb97-251">Clear package caches</span></span>

<span data-ttu-id="4eb97-252">Una aplicación en funcionamiento deja de ejecutarse inmediatamente después de actualizar el SDK de .NET Core en la máquina de desarrollo o de cambiar las versiones del paquete en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4eb97-252">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="4eb97-253">En algunos casos, los paquetes incoherentes pueden interrumpir una aplicación al realizar actualizaciones importantes.</span><span class="sxs-lookup"><span data-stu-id="4eb97-253">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="4eb97-254">La mayoría de estos problemas puede corregirse siguiendo estas instrucciones:</span><span class="sxs-lookup"><span data-stu-id="4eb97-254">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="4eb97-255">Elimine las carpetas *bin* y *obj*.</span><span class="sxs-lookup"><span data-stu-id="4eb97-255">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="4eb97-256">Borre las memorias caché del paquete ejecutando [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) desde un shell de comandos.</span><span class="sxs-lookup"><span data-stu-id="4eb97-256">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="4eb97-257">Otra manera de borrar las memorias caché de paquetes es usando la herramienta [nuget.exe](https://www.nuget.org/downloads) y ejecutando el comando `nuget locals all -clear`.</span><span class="sxs-lookup"><span data-stu-id="4eb97-257">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="4eb97-258">*nuget.exe* no es una instalación agrupada con el sistema operativo de escritorio de Windows y se debe obtener de forma independiente en el [sitio web de NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="4eb97-258">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="4eb97-259">Restaure el proyecto y vuelva a compilarlo.</span><span class="sxs-lookup"><span data-stu-id="4eb97-259">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="4eb97-260">Elimine todos los archivos de la carpeta de implementación del servidor antes de volver a implementar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4eb97-260">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="4eb97-261">Aplicación lenta o bloqueada</span><span class="sxs-lookup"><span data-stu-id="4eb97-261">Slow or hanging app</span></span>

<span data-ttu-id="4eb97-262">Un *volcado de memoria* es una instantánea de la memoria del sistema que puede ayudar a determinar la causa de un bloqueo de la aplicación, un error de inicio o una aplicación lenta.</span><span class="sxs-lookup"><span data-stu-id="4eb97-262">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="4eb97-263">Bloqueo o excepción de la aplicación</span><span class="sxs-lookup"><span data-stu-id="4eb97-263">App crashes or encounters an exception</span></span>

<span data-ttu-id="4eb97-264">Obtenga y analice un volcado de memoria en [Informe de errores de Windows (WER)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="4eb97-264">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="4eb97-265">Cree una carpeta para almacenar los archivos de volcado de memoria en `c:\dumps`.</span><span class="sxs-lookup"><span data-stu-id="4eb97-265">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="4eb97-266">Ejecute el script [EnableDumps PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/EnableDumps.ps1) con el nombre del archivo ejecutable de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="4eb97-266">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```powershell
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="4eb97-267">Ejecute la aplicación en las condiciones que hacen que se produzca el bloqueo.</span><span class="sxs-lookup"><span data-stu-id="4eb97-267">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="4eb97-268">Una vez que se haya producido el bloqueo, ejecute el [script DisableDumps de PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/DisableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="4eb97-268">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/DisableDumps.ps1):</span></span>

   ```powershell
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="4eb97-269">Después de que se bloquee la aplicación y se complete la recopilación del volcado de memoria, la aplicación puede finalizar con normalidad.</span><span class="sxs-lookup"><span data-stu-id="4eb97-269">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="4eb97-270">El script de PowerShell configura WER de modo que recopile un máximo de cinco volcados de memoria por aplicación.</span><span class="sxs-lookup"><span data-stu-id="4eb97-270">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="4eb97-271">Los volcados de memoria pueden ocupar una gran cantidad de espacio en disco (hasta varios gigabytes cada uno).</span><span class="sxs-lookup"><span data-stu-id="4eb97-271">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="4eb97-272">La aplicación deja de responder, produce un error durante el inicio o se ejecuta con normalidad</span><span class="sxs-lookup"><span data-stu-id="4eb97-272">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="4eb97-273">Si una aplicación *deja de responder* (se detiene, pero no se bloquea), produce un error durante el inicio o se ejecuta con normalidad, vea [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) (Archivos de volcado de memoria en modo de usuario: selección de la mejor herramienta) para seleccionar una herramienta apropiada para generar el volcado de memoria.</span><span class="sxs-lookup"><span data-stu-id="4eb97-273">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="4eb97-274">Análisis del volcado de memoria</span><span class="sxs-lookup"><span data-stu-id="4eb97-274">Analyze the dump</span></span>

<span data-ttu-id="4eb97-275">El volcado de memoria se puede analizar de varias maneras.</span><span class="sxs-lookup"><span data-stu-id="4eb97-275">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="4eb97-276">Para obtener más información, vea [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file) (Análisis de un archivo de volcado de memoria en modo de usuario).</span><span class="sxs-lookup"><span data-stu-id="4eb97-276">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4eb97-277">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="4eb97-277">Additional resources</span></span>

* <span data-ttu-id="4eb97-278">[Kestrel: configuración de punto de conexión](xref:fundamentals/servers/kestrel#endpoint-configuration) (configuración de HTTPS y compatibilidad de SNI incluidas)</span><span class="sxs-lookup"><span data-stu-id="4eb97-278">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/generic-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="4eb97-279">Una aplicación de ASP.NET Core se puede hospedar en Windows sin usar IIS como [servicio de Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications).</span><span class="sxs-lookup"><span data-stu-id="4eb97-279">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="4eb97-280">Cuando se hospeda como un servicio de Windows, la aplicación se inicia automáticamente después de reiniciar el servidor.</span><span class="sxs-lookup"><span data-stu-id="4eb97-280">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="4eb97-281">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="4eb97-281">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="4eb97-282">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="4eb97-282">Prerequisites</span></span>

* [<span data-ttu-id="4eb97-283">ASP.NET Core SDK 2.1 o posterior</span><span class="sxs-lookup"><span data-stu-id="4eb97-283">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="4eb97-284">PowerShell 6.2 o posterior</span><span class="sxs-lookup"><span data-stu-id="4eb97-284">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a><span data-ttu-id="4eb97-285">Configuración de aplicaciones</span><span class="sxs-lookup"><span data-stu-id="4eb97-285">App configuration</span></span>

<span data-ttu-id="4eb97-286">La aplicación requiere referencias de paquetes para [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) y [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span><span class="sxs-lookup"><span data-stu-id="4eb97-286">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="4eb97-287">Para probar y depurar cuando se ejecuta fuera de un servicio, agregue código para determinar si la aplicación se ejecuta como un servicio o una aplicación de consola.</span><span class="sxs-lookup"><span data-stu-id="4eb97-287">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="4eb97-288">Compruebe si el depurador está asociado o hay presente un conmutador `--console`.</span><span class="sxs-lookup"><span data-stu-id="4eb97-288">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="4eb97-289">Si alguna de estas condiciones se cumple (la aplicación no se ejecuta como un servicio), llame a <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span><span class="sxs-lookup"><span data-stu-id="4eb97-289">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="4eb97-290">Si las condiciones no se cumplen (la aplicación se ejecuta como servicio):</span><span class="sxs-lookup"><span data-stu-id="4eb97-290">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="4eb97-291">Llame a <xref:System.IO.Directory.SetCurrentDirectory*> y use una ruta de acceso a la ubicación de publicación de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4eb97-291">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="4eb97-292">No llame a <xref:System.IO.Directory.GetCurrentDirectory*> para obtener la ruta de acceso porque una aplicación de servicio de Windows devuelve una carpeta *C:\\WINDOWS\\system32* cuando se llama a <xref:System.IO.Directory.GetCurrentDirectory*>.</span><span class="sxs-lookup"><span data-stu-id="4eb97-292">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="4eb97-293">Para obtener más información, consulte la sección [Directorio actual y raíz del contenido](#current-directory-and-content-root).</span><span class="sxs-lookup"><span data-stu-id="4eb97-293">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="4eb97-294">Este paso se realiza antes de que la aplicación se configure en `CreateWebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="4eb97-294">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="4eb97-295">Llame a <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> para ejecutar la aplicación como un servicio.</span><span class="sxs-lookup"><span data-stu-id="4eb97-295">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="4eb97-296">Dado que el [Proveedor de configuración de línea de comandos](xref:fundamentals/configuration/index#command-line-configuration-provider) requiere pares nombre-valor en los argumentos de línea de comandos, el conmutador `--console` se quita de los argumentos antes de que <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> los reciba.</span><span class="sxs-lookup"><span data-stu-id="4eb97-296">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="4eb97-297">Para escribir en el registro de eventos de Windows, agregue el proveedor EventLog a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span><span class="sxs-lookup"><span data-stu-id="4eb97-297">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="4eb97-298">Establezca el nivel de registro con la clave `Logging:LogLevel:Default` en el archivo *appsettings.Production.json*.</span><span class="sxs-lookup"><span data-stu-id="4eb97-298">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="4eb97-299">En el ejemplo siguiente de la aplicación de ejemplo, se llama a `RunAsCustomService` en lugar de a <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> con el fin de controlar los eventos de duración dentro de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4eb97-299">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="4eb97-300">Para obtener más información, consulte la sección [Control de los eventos de inicio y detención](#handle-starting-and-stopping-events).</span><span class="sxs-lookup"><span data-stu-id="4eb97-300">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a><span data-ttu-id="4eb97-301">Tipo de implementación</span><span class="sxs-lookup"><span data-stu-id="4eb97-301">Deployment type</span></span>

<span data-ttu-id="4eb97-302">Para obtener información y consejos sobre los escenarios de implementación, consulte [Implementación de aplicaciones .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="4eb97-302">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="4eb97-303">SDK</span><span class="sxs-lookup"><span data-stu-id="4eb97-303">SDK</span></span>

<span data-ttu-id="4eb97-304">En el caso de un servicio basado en una aplicación web que use los marcos Razor Pages o MVC, especifique el SDK web en el archivo de proyecto:</span><span class="sxs-lookup"><span data-stu-id="4eb97-304">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="4eb97-305">Si el servicio solo ejecuta tareas en segundo plano (por ejemplo, [servicios hospedados](xref:fundamentals/host/hosted-services)), especifique el SDK de Worker en el archivo de proyecto:</span><span class="sxs-lookup"><span data-stu-id="4eb97-305">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="4eb97-306">Implementación dependiente de marco (FDD)</span><span class="sxs-lookup"><span data-stu-id="4eb97-306">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="4eb97-307">La implementación dependiente de marco de trabajo (FDD) se basa en la presencia de una versión compartida de .NET Core en todo el sistema en el sistema de destino.</span><span class="sxs-lookup"><span data-stu-id="4eb97-307">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="4eb97-308">Cuando se adopta el escenario FDD siguiendo las instrucciones de este artículo, el SDK genera un archivo ejecutable ( *.exe*), denominado *ejecutable dependiente del marco*.</span><span class="sxs-lookup"><span data-stu-id="4eb97-308">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="4eb97-309">El [identificador en tiempo de ejecución (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contiene la plataforma de destino.</span><span class="sxs-lookup"><span data-stu-id="4eb97-309">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="4eb97-310">En el ejemplo siguiente, el RID se establece en `win7-x64`.</span><span class="sxs-lookup"><span data-stu-id="4eb97-310">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="4eb97-311">La propiedad `<SelfContained>` se establece en `false`.</span><span class="sxs-lookup"><span data-stu-id="4eb97-311">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="4eb97-312">Estas propiedades indican al SDK que genere un archivo ejecutable ( *.exe*) para Windows y una aplicación que depende del marco .NET Core compartido.</span><span class="sxs-lookup"><span data-stu-id="4eb97-312">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="4eb97-313">No se requiere un archivo *web.config*, que normalmente se produce cuando se publica una aplicación ASP.NET Core, para una aplicación de Windows Services.</span><span class="sxs-lookup"><span data-stu-id="4eb97-313">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="4eb97-314">Para deshabilitar la creación de un archivo *web.config* agregue la propiedad `<IsTransformWebConfigDisabled>` establecida en `true`.</span><span class="sxs-lookup"><span data-stu-id="4eb97-314">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="4eb97-315">Implementación autocontenida (SCD)</span><span class="sxs-lookup"><span data-stu-id="4eb97-315">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="4eb97-316">Una implementación autocontenida (SCD) no depende de la presencia de un marco compartido en el sistema host.</span><span class="sxs-lookup"><span data-stu-id="4eb97-316">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="4eb97-317">El tiempo de ejecución y las dependencias de la aplicación se implementan con la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4eb97-317">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="4eb97-318">Un [identificador en tiempo de ejecución (RID)](/dotnet/core/rid-catalog) se incluye en el `<PropertyGroup>` que contiene la plataforma de destino:</span><span class="sxs-lookup"><span data-stu-id="4eb97-318">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="4eb97-319">Para publicar para varios RID:</span><span class="sxs-lookup"><span data-stu-id="4eb97-319">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="4eb97-320">Proporcione los RID en una lista delimitada por punto y coma.</span><span class="sxs-lookup"><span data-stu-id="4eb97-320">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="4eb97-321">Use el nombre de la propiedad [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span><span class="sxs-lookup"><span data-stu-id="4eb97-321">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="4eb97-322">Para más información, vea el [Catálogo de identificadores de entorno de ejecución (RID) de .NET Core](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="4eb97-322">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

<span data-ttu-id="4eb97-323">Una propiedad `<SelfContained>` se establece en `true`:</span><span class="sxs-lookup"><span data-stu-id="4eb97-323">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a><span data-ttu-id="4eb97-324">Cuenta de usuario de servicio</span><span class="sxs-lookup"><span data-stu-id="4eb97-324">Service user account</span></span>

<span data-ttu-id="4eb97-325">Para crear una cuenta de usuario para un servicio, use el cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) de un shell de comandos administrativos de PowerShell 6.</span><span class="sxs-lookup"><span data-stu-id="4eb97-325">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="4eb97-326">En la actualización de octubre de 2018 de Windows 10 (versión 1809/compilación 10.0.17763) o posterior:</span><span class="sxs-lookup"><span data-stu-id="4eb97-326">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="4eb97-327">En el sistema operativo Windows anterior a la actualización de octubre de 2018 de Windows 10 (versión 1809/compilación 10.0.17763):</span><span class="sxs-lookup"><span data-stu-id="4eb97-327">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="4eb97-328">Proporcione una [contraseña segura](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) cuando se le solicite.</span><span class="sxs-lookup"><span data-stu-id="4eb97-328">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="4eb97-329">A menos que el parámetro `-AccountExpires` se proporcione para el cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) con una fecha de expiración <xref:System.DateTime>, la cuenta no expirará.</span><span class="sxs-lookup"><span data-stu-id="4eb97-329">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="4eb97-330">Para más información, vea [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) y [Service User Accounts](/windows/desktop/services/service-user-accounts) (Cuentas de usuario del servicio).</span><span class="sxs-lookup"><span data-stu-id="4eb97-330">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="4eb97-331">Un enfoque alternativo de administración de usuarios al usar Active Directory consiste en usar cuentas de servicio administradas.</span><span class="sxs-lookup"><span data-stu-id="4eb97-331">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="4eb97-332">Para obtener más información, consulte [grupo Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview) (Información general sobre cuentas de servicio administradas de grupo).</span><span class="sxs-lookup"><span data-stu-id="4eb97-332">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="4eb97-333">Derechos para iniciar sesión como servicio</span><span class="sxs-lookup"><span data-stu-id="4eb97-333">Log on as a service rights</span></span>

<span data-ttu-id="4eb97-334">Para establecer los derechos de *Iniciar sesión como servicio* para una cuenta de usuario del servicio:</span><span class="sxs-lookup"><span data-stu-id="4eb97-334">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="4eb97-335">Abra el editor de la Directiva de seguridad local mediante la ejecución de *secpol.msc*.</span><span class="sxs-lookup"><span data-stu-id="4eb97-335">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="4eb97-336">Expanda el nodo **Directivas locales** y, después, seleccione **Asignación de derechos de usuario**.</span><span class="sxs-lookup"><span data-stu-id="4eb97-336">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="4eb97-337">Abra la directiva **Iniciar sesión como servicio**.</span><span class="sxs-lookup"><span data-stu-id="4eb97-337">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="4eb97-338">Seleccione **Agregar usuario o grupo**.</span><span class="sxs-lookup"><span data-stu-id="4eb97-338">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="4eb97-339">Proporcione el nombre de objeto (cuenta de usuario) mediante cualquiera de los métodos siguientes:</span><span class="sxs-lookup"><span data-stu-id="4eb97-339">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="4eb97-340">Escriba la cuenta de usuario (`{DOMAIN OR COMPUTER NAME\USER}`) en el campo del nombre de objeto y seleccione **Aceptar** para agregar el usuario a la directiva.</span><span class="sxs-lookup"><span data-stu-id="4eb97-340">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="4eb97-341">Seleccione **Opciones avanzadas**.</span><span class="sxs-lookup"><span data-stu-id="4eb97-341">Select **Advanced**.</span></span> <span data-ttu-id="4eb97-342">Seleccione **Buscar ahora**.</span><span class="sxs-lookup"><span data-stu-id="4eb97-342">Select **Find Now**.</span></span> <span data-ttu-id="4eb97-343">Seleccione la cuenta de usuario de la lista.</span><span class="sxs-lookup"><span data-stu-id="4eb97-343">Select the user account from the list.</span></span> <span data-ttu-id="4eb97-344">Seleccione **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="4eb97-344">Select **OK**.</span></span> <span data-ttu-id="4eb97-345">Seleccione **Aceptar** nuevo para agregar el usuario a la directiva.</span><span class="sxs-lookup"><span data-stu-id="4eb97-345">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="4eb97-346">Seleccione **Aceptar** o **Aplicar** para aceptar los cambios.</span><span class="sxs-lookup"><span data-stu-id="4eb97-346">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="4eb97-347">Creación y administración del servicio de Windows</span><span class="sxs-lookup"><span data-stu-id="4eb97-347">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="4eb97-348">Creación de un servicio</span><span class="sxs-lookup"><span data-stu-id="4eb97-348">Create a service</span></span>

<span data-ttu-id="4eb97-349">Use los comandos de PowerShell para registrar un servicio.</span><span class="sxs-lookup"><span data-stu-id="4eb97-349">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="4eb97-350">Desde un shell de comandos administrativos de PowerShell 6, ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="4eb97-350">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="4eb97-351">`{EXE PATH}`: ruta de acceso a la carpeta de la aplicación en el host (por ejemplo, `d:\myservice`).</span><span class="sxs-lookup"><span data-stu-id="4eb97-351">`{EXE PATH}`: Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="4eb97-352">No incluya el archivo ejecutable de la aplicación en la ruta de acceso.</span><span class="sxs-lookup"><span data-stu-id="4eb97-352">Don't include the app's executable in the path.</span></span> <span data-ttu-id="4eb97-353">No se requiere una barra diagonal al final.</span><span class="sxs-lookup"><span data-stu-id="4eb97-353">A trailing slash isn't required.</span></span>
* <span data-ttu-id="4eb97-354">`{DOMAIN OR COMPUTER NAME\USER}`: cuenta de usuario de servicio (por ejemplo, `Contoso\ServiceUser`).</span><span class="sxs-lookup"><span data-stu-id="4eb97-354">`{DOMAIN OR COMPUTER NAME\USER}`: Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="4eb97-355">`{SERVICE NAME}`: nombre de servicio (por ejemplo, `MyService`).</span><span class="sxs-lookup"><span data-stu-id="4eb97-355">`{SERVICE NAME}`: Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="4eb97-356">`{EXE FILE PATH}`: ruta de acceso ejecutable de la aplicación (por ejemplo, `d:\myservice\myservice.exe`).</span><span class="sxs-lookup"><span data-stu-id="4eb97-356">`{EXE FILE PATH}`: The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="4eb97-357">Incluya el nombre de archivo del ejecutable con la extensión.</span><span class="sxs-lookup"><span data-stu-id="4eb97-357">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="4eb97-358">`{DESCRIPTION}`: descripción del servicio (por ejemplo, `My sample service`).</span><span class="sxs-lookup"><span data-stu-id="4eb97-358">`{DESCRIPTION}`: Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="4eb97-359">`{DISPLAY NAME}`: nombre para mostrar del servicio (por ejemplo, `My Service`).</span><span class="sxs-lookup"><span data-stu-id="4eb97-359">`{DISPLAY NAME}`: Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="4eb97-360">iniciar un servicio.</span><span class="sxs-lookup"><span data-stu-id="4eb97-360">Start a service</span></span>

<span data-ttu-id="4eb97-361">Inicie el servicio con el siguiente comando de PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="4eb97-361">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="4eb97-362">Este comando tarda unos segundos en iniciar el servicio.</span><span class="sxs-lookup"><span data-stu-id="4eb97-362">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="4eb97-363">Determinación del estado de un servicio</span><span class="sxs-lookup"><span data-stu-id="4eb97-363">Determine a service's status</span></span>

<span data-ttu-id="4eb97-364">Para comprobar el estado de un servicio, use el siguiente comando de PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="4eb97-364">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="4eb97-365">El estado se notifica como uno de los siguientes valores:</span><span class="sxs-lookup"><span data-stu-id="4eb97-365">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="4eb97-366">Detención de un servicio</span><span class="sxs-lookup"><span data-stu-id="4eb97-366">Stop a service</span></span>

<span data-ttu-id="4eb97-367">Detenga un servicio con el siguiente comando de PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="4eb97-367">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="4eb97-368">Eliminación de un servicio</span><span class="sxs-lookup"><span data-stu-id="4eb97-368">Remove a service</span></span>

<span data-ttu-id="4eb97-369">Tras un breve intervalo para detener un servicio, elimine el servicio con el siguiente comando de PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="4eb97-369">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="4eb97-370">Control de los eventos de inicio y detención</span><span class="sxs-lookup"><span data-stu-id="4eb97-370">Handle starting and stopping events</span></span>

<span data-ttu-id="4eb97-371">Para controlar los eventos <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> y <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*>:</span><span class="sxs-lookup"><span data-stu-id="4eb97-371">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="4eb97-372">Cree una clase que se derive de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> con los métodos `OnStarting`, `OnStarted` y `OnStopping`:</span><span class="sxs-lookup"><span data-stu-id="4eb97-372">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="4eb97-373">Cree un método de extensión para <xref:Microsoft.AspNetCore.Hosting.IWebHost> que pase `CustomWebHostService` a <xref:System.ServiceProcess.ServiceBase.Run*>:</span><span class="sxs-lookup"><span data-stu-id="4eb97-373">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="4eb97-374">En `Program.Main`, llame al método de extensión `RunAsCustomService` en lugar de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span><span class="sxs-lookup"><span data-stu-id="4eb97-374">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="4eb97-375">Para ver la ubicación de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> en `Program.Main`, consulte el ejemplo de código que se muestra en la sección [Tipo de implementación](#deployment-type).</span><span class="sxs-lookup"><span data-stu-id="4eb97-375">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="4eb97-376">Escenarios de servidor proxy y equilibrador de carga</span><span class="sxs-lookup"><span data-stu-id="4eb97-376">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="4eb97-377">Los servicios que interactúan con las solicitudes de Internet o de una red corporativa y están detrás de un proxy o de un equilibrador de carga podrían requerir configuración adicional.</span><span class="sxs-lookup"><span data-stu-id="4eb97-377">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="4eb97-378">Para obtener más información, vea <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="4eb97-378">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="4eb97-379">Configuración de puntos de conexión</span><span class="sxs-lookup"><span data-stu-id="4eb97-379">Configure endpoints</span></span>

<span data-ttu-id="4eb97-380">ASP.NET Core se enlaza a `http://localhost:5000` de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="4eb97-380">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="4eb97-381">Configure la dirección URL y el puerto estableciendo la variable de entorno `ASPNETCORE_URLS`.</span><span class="sxs-lookup"><span data-stu-id="4eb97-381">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="4eb97-382">Para información sobre otros enfoques de configuración de direcciones URL y puertos, consulte el artículo en cuestión:</span><span class="sxs-lookup"><span data-stu-id="4eb97-382">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="4eb97-383">En la guía anterior se trata la compatibilidad con los puntos de conexión HTTPS.</span><span class="sxs-lookup"><span data-stu-id="4eb97-383">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="4eb97-384">Por ejemplo, configure la aplicación para HTTPS cuando se use la autenticación con un servicio de Windows.</span><span class="sxs-lookup"><span data-stu-id="4eb97-384">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="4eb97-385">No se admite el uso del certificado de desarrollo HTTPS de ASP.NET Core para proteger un punto de conexión de servicio.</span><span class="sxs-lookup"><span data-stu-id="4eb97-385">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="4eb97-386">Directorio actual y raíz del contenido</span><span class="sxs-lookup"><span data-stu-id="4eb97-386">Current directory and content root</span></span>

<span data-ttu-id="4eb97-387">El directorio de trabajo actual devuelto por una llamada a <xref:System.IO.Directory.GetCurrentDirectory*> para un servicio de Windows es la carpeta *C:\\WINDOWS\\system32*.</span><span class="sxs-lookup"><span data-stu-id="4eb97-387">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="4eb97-388">La carpeta *system32* no es una ubicación adecuada para almacenar los archivos de un servicio (por ejemplo los archivos de configuración).</span><span class="sxs-lookup"><span data-stu-id="4eb97-388">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="4eb97-389">Utilice uno de los siguientes enfoques para mantener los archivos de configuración y los activos de un servicio, así como para acceder a ellos.</span><span class="sxs-lookup"><span data-stu-id="4eb97-389">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="4eb97-390">Configuración de la ruta de acceso raíz del contenido en la carpeta de la aplicación</span><span class="sxs-lookup"><span data-stu-id="4eb97-390">Set the content root path to the app's folder</span></span>

<span data-ttu-id="4eb97-391"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> es la misma ruta de acceso proporcionada al argumento `binPath` cuando se crea un servicio.</span><span class="sxs-lookup"><span data-stu-id="4eb97-391">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="4eb97-392">En lugar de llamar a `GetCurrentDirectory` para crear rutas de acceso a los archivos de configuración, llame a <xref:System.IO.Directory.SetCurrentDirectory*> con la ruta de acceso a la [raíz del contenido](xref:fundamentals/index#content-root) de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4eb97-392">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's [content root](xref:fundamentals/index#content-root).</span></span>

<span data-ttu-id="4eb97-393">En `Program.Main`, determine la ruta de acceso a la carpeta del archivo ejecutable del servicio y use la ruta de acceso para establecer la raíz del contenido de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="4eb97-393">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="4eb97-394">Almacenamiento de los archivos de un servicio en una ubicación adecuada en el disco</span><span class="sxs-lookup"><span data-stu-id="4eb97-394">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="4eb97-395">Especifique una ruta de acceso absoluta con <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> al utilizar un <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> a la carpeta que contiene los archivos.</span><span class="sxs-lookup"><span data-stu-id="4eb97-395">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="4eb97-396">Solucionar problemas</span><span class="sxs-lookup"><span data-stu-id="4eb97-396">Troubleshoot</span></span>

<span data-ttu-id="4eb97-397">Para solucionar problemas de una aplicación de servicio de Windows, consulte <xref:test/troubleshoot>.</span><span class="sxs-lookup"><span data-stu-id="4eb97-397">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="4eb97-398">Errores comunes</span><span class="sxs-lookup"><span data-stu-id="4eb97-398">Common errors</span></span>

* <span data-ttu-id="4eb97-399">Se está usando una versión preliminar o antigua de PowerShell.</span><span class="sxs-lookup"><span data-stu-id="4eb97-399">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="4eb97-400">El servicio registrado no usa la salida **publicada** de la aplicación del comando [dotnet publish](/dotnet/core/tools/dotnet-publish).</span><span class="sxs-lookup"><span data-stu-id="4eb97-400">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="4eb97-401">La salida del comando [dotnet build](/dotnet/core/tools/dotnet-build) no es compatible con la implementación de aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="4eb97-401">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="4eb97-402">Los recursos publicados se encuentran en cualquiera de las siguientes carpetas, según el tipo de implementación:</span><span class="sxs-lookup"><span data-stu-id="4eb97-402">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="4eb97-403">*bin/Release/{PLATAFORMA DE DESTINO}/publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="4eb97-403">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="4eb97-404">*bin/Release/{PLATAFORMA DE DESTINO}/{IDENTIFICADOR DE RUNTIME}/publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="4eb97-404">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="4eb97-405">El servicio no está en el estado EN EJECUCIÓN.</span><span class="sxs-lookup"><span data-stu-id="4eb97-405">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="4eb97-406">Las rutas de acceso a los recursos que usa la aplicación (por ejemplo, certificados) son incorrectas.</span><span class="sxs-lookup"><span data-stu-id="4eb97-406">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="4eb97-407">La ruta de acceso base de un servicio de Windows es *c:\\Windows\\System32*.</span><span class="sxs-lookup"><span data-stu-id="4eb97-407">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="4eb97-408">El usuario no tiene derechos de *inicio de sesión como servicio*.</span><span class="sxs-lookup"><span data-stu-id="4eb97-408">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="4eb97-409">La contraseña del usuario ha expirado o se ha pasado incorrectamente al ejecutar el comando de PowerShell `New-Service`.</span><span class="sxs-lookup"><span data-stu-id="4eb97-409">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="4eb97-410">La aplicación requiere autenticación ASP.NET Core, pero no está configurada para conexiones seguras (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="4eb97-410">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="4eb97-411">El puerto de la dirección URL de solicitud es incorrecto o no está configurado correctamente en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4eb97-411">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="4eb97-412">Registros de eventos del sistema y de aplicación</span><span class="sxs-lookup"><span data-stu-id="4eb97-412">System and Application Event Logs</span></span>

<span data-ttu-id="4eb97-413">Acceda a los registros de eventos del sistema y de aplicación:</span><span class="sxs-lookup"><span data-stu-id="4eb97-413">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="4eb97-414">Abra el menú Inicio, busque *Visor de eventos* y seleccione la aplicación **Visor de eventos**.</span><span class="sxs-lookup"><span data-stu-id="4eb97-414">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="4eb97-415">En **Visor de eventos**, abra el nodo **Registros de Windows**.</span><span class="sxs-lookup"><span data-stu-id="4eb97-415">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="4eb97-416">Seleccione **Sistema** para abrir el registro de eventos del sistema.</span><span class="sxs-lookup"><span data-stu-id="4eb97-416">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="4eb97-417">Seleccione **Aplicación** para abrir el registro de eventos de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4eb97-417">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="4eb97-418">Busque los errores asociados a la aplicación objeto del error.</span><span class="sxs-lookup"><span data-stu-id="4eb97-418">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="4eb97-419">Ejecución de la aplicación en un símbolo del sistema</span><span class="sxs-lookup"><span data-stu-id="4eb97-419">Run the app at a command prompt</span></span>

<span data-ttu-id="4eb97-420">Muchos errores de inicio no generan información útil en el registro de eventos.</span><span class="sxs-lookup"><span data-stu-id="4eb97-420">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="4eb97-421">La causa de algunos errores se puede encontrar mediante la ejecución de la aplicación en un símbolo del sistema en el sistema de hospedaje.</span><span class="sxs-lookup"><span data-stu-id="4eb97-421">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="4eb97-422">Para registrar detalles adicionales de la aplicación, reduzca el [nivel de registro](xref:fundamentals/logging/index#log-level) o ejecute la aplicación en el [entorno de desarrollo](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="4eb97-422">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="4eb97-423">Borrado de memorias caché de paquetes</span><span class="sxs-lookup"><span data-stu-id="4eb97-423">Clear package caches</span></span>

<span data-ttu-id="4eb97-424">Una aplicación en funcionamiento deja de ejecutarse inmediatamente después de actualizar el SDK de .NET Core en la máquina de desarrollo o de cambiar las versiones del paquete en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4eb97-424">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="4eb97-425">En algunos casos, los paquetes incoherentes pueden interrumpir una aplicación al realizar actualizaciones importantes.</span><span class="sxs-lookup"><span data-stu-id="4eb97-425">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="4eb97-426">La mayoría de estos problemas puede corregirse siguiendo estas instrucciones:</span><span class="sxs-lookup"><span data-stu-id="4eb97-426">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="4eb97-427">Elimine las carpetas *bin* y *obj*.</span><span class="sxs-lookup"><span data-stu-id="4eb97-427">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="4eb97-428">Borre las memorias caché del paquete ejecutando [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) desde un shell de comandos.</span><span class="sxs-lookup"><span data-stu-id="4eb97-428">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="4eb97-429">Otra manera de borrar las memorias caché de paquetes es usando la herramienta [nuget.exe](https://www.nuget.org/downloads) y ejecutando el comando `nuget locals all -clear`.</span><span class="sxs-lookup"><span data-stu-id="4eb97-429">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="4eb97-430">*nuget.exe* no es una instalación agrupada con el sistema operativo de escritorio de Windows y se debe obtener de forma independiente en el [sitio web de NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="4eb97-430">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="4eb97-431">Restaure el proyecto y vuelva a compilarlo.</span><span class="sxs-lookup"><span data-stu-id="4eb97-431">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="4eb97-432">Elimine todos los archivos de la carpeta de implementación del servidor antes de volver a implementar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4eb97-432">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="4eb97-433">Aplicación lenta o bloqueada</span><span class="sxs-lookup"><span data-stu-id="4eb97-433">Slow or hanging app</span></span>

<span data-ttu-id="4eb97-434">Un *volcado de memoria* es una instantánea de la memoria del sistema que puede ayudar a determinar la causa de un bloqueo de la aplicación, un error de inicio o una aplicación lenta.</span><span class="sxs-lookup"><span data-stu-id="4eb97-434">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="4eb97-435">Bloqueo o excepción de la aplicación</span><span class="sxs-lookup"><span data-stu-id="4eb97-435">App crashes or encounters an exception</span></span>

<span data-ttu-id="4eb97-436">Obtenga y analice un volcado de memoria en [Informe de errores de Windows (WER)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="4eb97-436">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="4eb97-437">Cree una carpeta para almacenar los archivos de volcado de memoria en `c:\dumps`.</span><span class="sxs-lookup"><span data-stu-id="4eb97-437">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="4eb97-438">Ejecute el script [EnableDumps PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) con el nombre del archivo ejecutable de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="4eb97-438">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="4eb97-439">Ejecute la aplicación en las condiciones que hacen que se produzca el bloqueo.</span><span class="sxs-lookup"><span data-stu-id="4eb97-439">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="4eb97-440">Una vez que se haya producido el bloqueo, ejecute el [script DisableDumps de PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="4eb97-440">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span></span>

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="4eb97-441">Después de que se bloquee la aplicación y se complete la recopilación del volcado de memoria, la aplicación puede finalizar con normalidad.</span><span class="sxs-lookup"><span data-stu-id="4eb97-441">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="4eb97-442">El script de PowerShell configura WER de modo que recopile un máximo de cinco volcados de memoria por aplicación.</span><span class="sxs-lookup"><span data-stu-id="4eb97-442">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="4eb97-443">Los volcados de memoria pueden ocupar una gran cantidad de espacio en disco (hasta varios gigabytes cada uno).</span><span class="sxs-lookup"><span data-stu-id="4eb97-443">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="4eb97-444">La aplicación deja de responder, produce un error durante el inicio o se ejecuta con normalidad</span><span class="sxs-lookup"><span data-stu-id="4eb97-444">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="4eb97-445">Si una aplicación *deja de responder* (se detiene, pero no se bloquea), produce un error durante el inicio o se ejecuta con normalidad, vea [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) (Archivos de volcado de memoria en modo de usuario: selección de la mejor herramienta) para seleccionar una herramienta apropiada para generar el volcado de memoria.</span><span class="sxs-lookup"><span data-stu-id="4eb97-445">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="4eb97-446">Análisis del volcado de memoria</span><span class="sxs-lookup"><span data-stu-id="4eb97-446">Analyze the dump</span></span>

<span data-ttu-id="4eb97-447">El volcado de memoria se puede analizar de varias maneras.</span><span class="sxs-lookup"><span data-stu-id="4eb97-447">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="4eb97-448">Para obtener más información, vea [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file) (Análisis de un archivo de volcado de memoria en modo de usuario).</span><span class="sxs-lookup"><span data-stu-id="4eb97-448">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4eb97-449">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="4eb97-449">Additional resources</span></span>

* <span data-ttu-id="4eb97-450">[Kestrel: configuración de punto de conexión](xref:fundamentals/servers/kestrel#endpoint-configuration) (configuración de HTTPS y compatibilidad de SNI incluidas)</span><span class="sxs-lookup"><span data-stu-id="4eb97-450">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="4eb97-451">Una aplicación de ASP.NET Core se puede hospedar en Windows sin usar IIS como [servicio de Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications).</span><span class="sxs-lookup"><span data-stu-id="4eb97-451">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="4eb97-452">Cuando se hospeda como un servicio de Windows, la aplicación se inicia automáticamente después de reiniciar el servidor.</span><span class="sxs-lookup"><span data-stu-id="4eb97-452">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="4eb97-453">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="4eb97-453">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="4eb97-454">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="4eb97-454">Prerequisites</span></span>

* [<span data-ttu-id="4eb97-455">ASP.NET Core SDK 2.1 o posterior</span><span class="sxs-lookup"><span data-stu-id="4eb97-455">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="4eb97-456">PowerShell 6.2 o posterior</span><span class="sxs-lookup"><span data-stu-id="4eb97-456">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a><span data-ttu-id="4eb97-457">Configuración de aplicaciones</span><span class="sxs-lookup"><span data-stu-id="4eb97-457">App configuration</span></span>

<span data-ttu-id="4eb97-458">La aplicación requiere referencias de paquetes para [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) y [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span><span class="sxs-lookup"><span data-stu-id="4eb97-458">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="4eb97-459">Para probar y depurar cuando se ejecuta fuera de un servicio, agregue código para determinar si la aplicación se ejecuta como un servicio o una aplicación de consola.</span><span class="sxs-lookup"><span data-stu-id="4eb97-459">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="4eb97-460">Compruebe si el depurador está asociado o hay presente un conmutador `--console`.</span><span class="sxs-lookup"><span data-stu-id="4eb97-460">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="4eb97-461">Si alguna de estas condiciones se cumple (la aplicación no se ejecuta como un servicio), llame a <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span><span class="sxs-lookup"><span data-stu-id="4eb97-461">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="4eb97-462">Si las condiciones no se cumplen (la aplicación se ejecuta como servicio):</span><span class="sxs-lookup"><span data-stu-id="4eb97-462">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="4eb97-463">Llame a <xref:System.IO.Directory.SetCurrentDirectory*> y use una ruta de acceso a la ubicación de publicación de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4eb97-463">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="4eb97-464">No llame a <xref:System.IO.Directory.GetCurrentDirectory*> para obtener la ruta de acceso porque una aplicación de servicio de Windows devuelve una carpeta *C:\\WINDOWS\\system32* cuando se llama a <xref:System.IO.Directory.GetCurrentDirectory*>.</span><span class="sxs-lookup"><span data-stu-id="4eb97-464">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="4eb97-465">Para obtener más información, consulte la sección [Directorio actual y raíz del contenido](#current-directory-and-content-root).</span><span class="sxs-lookup"><span data-stu-id="4eb97-465">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="4eb97-466">Este paso se realiza antes de que la aplicación se configure en `CreateWebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="4eb97-466">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="4eb97-467">Llame a <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> para ejecutar la aplicación como un servicio.</span><span class="sxs-lookup"><span data-stu-id="4eb97-467">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="4eb97-468">Dado que el [Proveedor de configuración de línea de comandos](xref:fundamentals/configuration/index#command-line-configuration-provider) requiere pares nombre-valor en los argumentos de línea de comandos, el conmutador `--console` se quita de los argumentos antes de que <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> los reciba.</span><span class="sxs-lookup"><span data-stu-id="4eb97-468">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="4eb97-469">Para escribir en el registro de eventos de Windows, agregue el proveedor EventLog a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span><span class="sxs-lookup"><span data-stu-id="4eb97-469">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="4eb97-470">Establezca el nivel de registro con la clave `Logging:LogLevel:Default` en el archivo *appsettings.Production.json*.</span><span class="sxs-lookup"><span data-stu-id="4eb97-470">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="4eb97-471">En el ejemplo siguiente de la aplicación de ejemplo, se llama a `RunAsCustomService` en lugar de a <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> con el fin de controlar los eventos de duración dentro de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4eb97-471">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="4eb97-472">Para obtener más información, consulte la sección [Control de los eventos de inicio y detención](#handle-starting-and-stopping-events).</span><span class="sxs-lookup"><span data-stu-id="4eb97-472">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a><span data-ttu-id="4eb97-473">Tipo de implementación</span><span class="sxs-lookup"><span data-stu-id="4eb97-473">Deployment type</span></span>

<span data-ttu-id="4eb97-474">Para obtener información y consejos sobre los escenarios de implementación, consulte [Implementación de aplicaciones .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="4eb97-474">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="4eb97-475">SDK</span><span class="sxs-lookup"><span data-stu-id="4eb97-475">SDK</span></span>

<span data-ttu-id="4eb97-476">En el caso de un servicio basado en una aplicación web que use los marcos Razor Pages o MVC, especifique el SDK web en el archivo de proyecto:</span><span class="sxs-lookup"><span data-stu-id="4eb97-476">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="4eb97-477">Si el servicio solo ejecuta tareas en segundo plano (por ejemplo, [servicios hospedados](xref:fundamentals/host/hosted-services)), especifique el SDK de Worker en el archivo de proyecto:</span><span class="sxs-lookup"><span data-stu-id="4eb97-477">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="4eb97-478">Implementación dependiente de marco (FDD)</span><span class="sxs-lookup"><span data-stu-id="4eb97-478">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="4eb97-479">La implementación dependiente de marco de trabajo (FDD) se basa en la presencia de una versión compartida de .NET Core en todo el sistema en el sistema de destino.</span><span class="sxs-lookup"><span data-stu-id="4eb97-479">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="4eb97-480">Cuando se adopta el escenario FDD siguiendo las instrucciones de este artículo, el SDK genera un archivo ejecutable ( *.exe*), denominado *ejecutable dependiente del marco*.</span><span class="sxs-lookup"><span data-stu-id="4eb97-480">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="4eb97-481">El [identificador en tiempo de ejecución (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contiene la plataforma de destino.</span><span class="sxs-lookup"><span data-stu-id="4eb97-481">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="4eb97-482">En el ejemplo siguiente, el RID se establece en `win7-x64`.</span><span class="sxs-lookup"><span data-stu-id="4eb97-482">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="4eb97-483">La propiedad `<SelfContained>` se establece en `false`.</span><span class="sxs-lookup"><span data-stu-id="4eb97-483">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="4eb97-484">Estas propiedades indican al SDK que genere un archivo ejecutable ( *.exe*) para Windows y una aplicación que depende del marco .NET Core compartido.</span><span class="sxs-lookup"><span data-stu-id="4eb97-484">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="4eb97-485">La propiedad `<UseAppHost>` se establece en `true`.</span><span class="sxs-lookup"><span data-stu-id="4eb97-485">The `<UseAppHost>` property is set to `true`.</span></span> <span data-ttu-id="4eb97-486">Esta propiedad proporciona el servicio con una ruta de acceso de activación (un archivo ejecutable, *.exe*) para una FDD.</span><span class="sxs-lookup"><span data-stu-id="4eb97-486">This property provides the service with an activation path (an executable, *.exe*) for an FDD.</span></span>

<span data-ttu-id="4eb97-487">No se requiere un archivo *web.config*, que normalmente se produce cuando se publica una aplicación ASP.NET Core, para una aplicación de Windows Services.</span><span class="sxs-lookup"><span data-stu-id="4eb97-487">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="4eb97-488">Para deshabilitar la creación de un archivo *web.config* agregue la propiedad `<IsTransformWebConfigDisabled>` establecida en `true`.</span><span class="sxs-lookup"><span data-stu-id="4eb97-488">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <UseAppHost>true</UseAppHost>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="4eb97-489">Implementación autocontenida (SCD)</span><span class="sxs-lookup"><span data-stu-id="4eb97-489">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="4eb97-490">Una implementación autocontenida (SCD) no depende de la presencia de un marco compartido en el sistema host.</span><span class="sxs-lookup"><span data-stu-id="4eb97-490">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="4eb97-491">El tiempo de ejecución y las dependencias de la aplicación se implementan con la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4eb97-491">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="4eb97-492">Un [identificador en tiempo de ejecución (RID)](/dotnet/core/rid-catalog) se incluye en el `<PropertyGroup>` que contiene la plataforma de destino:</span><span class="sxs-lookup"><span data-stu-id="4eb97-492">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="4eb97-493">Para publicar para varios RID:</span><span class="sxs-lookup"><span data-stu-id="4eb97-493">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="4eb97-494">Proporcione los RID en una lista delimitada por punto y coma.</span><span class="sxs-lookup"><span data-stu-id="4eb97-494">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="4eb97-495">Use el nombre de la propiedad [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span><span class="sxs-lookup"><span data-stu-id="4eb97-495">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="4eb97-496">Para más información, vea el [Catálogo de identificadores de entorno de ejecución (RID) de .NET Core](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="4eb97-496">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

<span data-ttu-id="4eb97-497">Una propiedad `<SelfContained>` se establece en `true`:</span><span class="sxs-lookup"><span data-stu-id="4eb97-497">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a><span data-ttu-id="4eb97-498">Cuenta de usuario de servicio</span><span class="sxs-lookup"><span data-stu-id="4eb97-498">Service user account</span></span>

<span data-ttu-id="4eb97-499">Para crear una cuenta de usuario para un servicio, use el cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) de un shell de comandos administrativos de PowerShell 6.</span><span class="sxs-lookup"><span data-stu-id="4eb97-499">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="4eb97-500">En la actualización de octubre de 2018 de Windows 10 (versión 1809/compilación 10.0.17763) o posterior:</span><span class="sxs-lookup"><span data-stu-id="4eb97-500">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="4eb97-501">En el sistema operativo Windows anterior a la actualización de octubre de 2018 de Windows 10 (versión 1809/compilación 10.0.17763):</span><span class="sxs-lookup"><span data-stu-id="4eb97-501">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="4eb97-502">Proporcione una [contraseña segura](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) cuando se le solicite.</span><span class="sxs-lookup"><span data-stu-id="4eb97-502">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="4eb97-503">A menos que el parámetro `-AccountExpires` se proporcione para el cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) con una fecha de expiración <xref:System.DateTime>, la cuenta no expirará.</span><span class="sxs-lookup"><span data-stu-id="4eb97-503">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="4eb97-504">Para más información, vea [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) y [Service User Accounts](/windows/desktop/services/service-user-accounts) (Cuentas de usuario del servicio).</span><span class="sxs-lookup"><span data-stu-id="4eb97-504">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="4eb97-505">Un enfoque alternativo de administración de usuarios al usar Active Directory consiste en usar cuentas de servicio administradas.</span><span class="sxs-lookup"><span data-stu-id="4eb97-505">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="4eb97-506">Para obtener más información, consulte [grupo Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview) (Información general sobre cuentas de servicio administradas de grupo).</span><span class="sxs-lookup"><span data-stu-id="4eb97-506">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="4eb97-507">Derechos para iniciar sesión como servicio</span><span class="sxs-lookup"><span data-stu-id="4eb97-507">Log on as a service rights</span></span>

<span data-ttu-id="4eb97-508">Para establecer los derechos de *Iniciar sesión como servicio* para una cuenta de usuario del servicio:</span><span class="sxs-lookup"><span data-stu-id="4eb97-508">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="4eb97-509">Abra el editor de la Directiva de seguridad local mediante la ejecución de *secpol.msc*.</span><span class="sxs-lookup"><span data-stu-id="4eb97-509">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="4eb97-510">Expanda el nodo **Directivas locales** y, después, seleccione **Asignación de derechos de usuario**.</span><span class="sxs-lookup"><span data-stu-id="4eb97-510">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="4eb97-511">Abra la directiva **Iniciar sesión como servicio**.</span><span class="sxs-lookup"><span data-stu-id="4eb97-511">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="4eb97-512">Seleccione **Agregar usuario o grupo**.</span><span class="sxs-lookup"><span data-stu-id="4eb97-512">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="4eb97-513">Proporcione el nombre de objeto (cuenta de usuario) mediante cualquiera de los métodos siguientes:</span><span class="sxs-lookup"><span data-stu-id="4eb97-513">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="4eb97-514">Escriba la cuenta de usuario (`{DOMAIN OR COMPUTER NAME\USER}`) en el campo del nombre de objeto y seleccione **Aceptar** para agregar el usuario a la directiva.</span><span class="sxs-lookup"><span data-stu-id="4eb97-514">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="4eb97-515">Seleccione **Opciones avanzadas**.</span><span class="sxs-lookup"><span data-stu-id="4eb97-515">Select **Advanced**.</span></span> <span data-ttu-id="4eb97-516">Seleccione **Buscar ahora**.</span><span class="sxs-lookup"><span data-stu-id="4eb97-516">Select **Find Now**.</span></span> <span data-ttu-id="4eb97-517">Seleccione la cuenta de usuario de la lista.</span><span class="sxs-lookup"><span data-stu-id="4eb97-517">Select the user account from the list.</span></span> <span data-ttu-id="4eb97-518">Seleccione **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="4eb97-518">Select **OK**.</span></span> <span data-ttu-id="4eb97-519">Seleccione **Aceptar** nuevo para agregar el usuario a la directiva.</span><span class="sxs-lookup"><span data-stu-id="4eb97-519">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="4eb97-520">Seleccione **Aceptar** o **Aplicar** para aceptar los cambios.</span><span class="sxs-lookup"><span data-stu-id="4eb97-520">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="4eb97-521">Creación y administración del servicio de Windows</span><span class="sxs-lookup"><span data-stu-id="4eb97-521">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="4eb97-522">Creación de un servicio</span><span class="sxs-lookup"><span data-stu-id="4eb97-522">Create a service</span></span>

<span data-ttu-id="4eb97-523">Use los comandos de PowerShell para registrar un servicio.</span><span class="sxs-lookup"><span data-stu-id="4eb97-523">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="4eb97-524">Desde un shell de comandos administrativos de PowerShell 6, ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="4eb97-524">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="4eb97-525">`{EXE PATH}`: ruta de acceso a la carpeta de la aplicación en el host (por ejemplo, `d:\myservice`).</span><span class="sxs-lookup"><span data-stu-id="4eb97-525">`{EXE PATH}`: Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="4eb97-526">No incluya el archivo ejecutable de la aplicación en la ruta de acceso.</span><span class="sxs-lookup"><span data-stu-id="4eb97-526">Don't include the app's executable in the path.</span></span> <span data-ttu-id="4eb97-527">No se requiere una barra diagonal al final.</span><span class="sxs-lookup"><span data-stu-id="4eb97-527">A trailing slash isn't required.</span></span>
* <span data-ttu-id="4eb97-528">`{DOMAIN OR COMPUTER NAME\USER}`: cuenta de usuario de servicio (por ejemplo, `Contoso\ServiceUser`).</span><span class="sxs-lookup"><span data-stu-id="4eb97-528">`{DOMAIN OR COMPUTER NAME\USER}`: Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="4eb97-529">`{SERVICE NAME}`: nombre de servicio (por ejemplo, `MyService`).</span><span class="sxs-lookup"><span data-stu-id="4eb97-529">`{SERVICE NAME}`: Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="4eb97-530">`{EXE FILE PATH}`: ruta de acceso ejecutable de la aplicación (por ejemplo, `d:\myservice\myservice.exe`).</span><span class="sxs-lookup"><span data-stu-id="4eb97-530">`{EXE FILE PATH}`: The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="4eb97-531">Incluya el nombre de archivo del ejecutable con la extensión.</span><span class="sxs-lookup"><span data-stu-id="4eb97-531">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="4eb97-532">`{DESCRIPTION}`: descripción del servicio (por ejemplo, `My sample service`).</span><span class="sxs-lookup"><span data-stu-id="4eb97-532">`{DESCRIPTION}`: Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="4eb97-533">`{DISPLAY NAME}`: nombre para mostrar del servicio (por ejemplo, `My Service`).</span><span class="sxs-lookup"><span data-stu-id="4eb97-533">`{DISPLAY NAME}`: Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="4eb97-534">iniciar un servicio.</span><span class="sxs-lookup"><span data-stu-id="4eb97-534">Start a service</span></span>

<span data-ttu-id="4eb97-535">Inicie el servicio con el siguiente comando de PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="4eb97-535">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="4eb97-536">Este comando tarda unos segundos en iniciar el servicio.</span><span class="sxs-lookup"><span data-stu-id="4eb97-536">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="4eb97-537">Determinación del estado de un servicio</span><span class="sxs-lookup"><span data-stu-id="4eb97-537">Determine a service's status</span></span>

<span data-ttu-id="4eb97-538">Para comprobar el estado de un servicio, use el siguiente comando de PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="4eb97-538">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="4eb97-539">El estado se notifica como uno de los siguientes valores:</span><span class="sxs-lookup"><span data-stu-id="4eb97-539">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="4eb97-540">Detención de un servicio</span><span class="sxs-lookup"><span data-stu-id="4eb97-540">Stop a service</span></span>

<span data-ttu-id="4eb97-541">Detenga un servicio con el siguiente comando de PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="4eb97-541">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="4eb97-542">Eliminación de un servicio</span><span class="sxs-lookup"><span data-stu-id="4eb97-542">Remove a service</span></span>

<span data-ttu-id="4eb97-543">Tras un breve intervalo para detener un servicio, elimine el servicio con el siguiente comando de PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="4eb97-543">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="4eb97-544">Control de los eventos de inicio y detención</span><span class="sxs-lookup"><span data-stu-id="4eb97-544">Handle starting and stopping events</span></span>

<span data-ttu-id="4eb97-545">Para controlar los eventos <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> y <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*>:</span><span class="sxs-lookup"><span data-stu-id="4eb97-545">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="4eb97-546">Cree una clase que se derive de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> con los métodos `OnStarting`, `OnStarted` y `OnStopping`:</span><span class="sxs-lookup"><span data-stu-id="4eb97-546">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="4eb97-547">Cree un método de extensión para <xref:Microsoft.AspNetCore.Hosting.IWebHost> que pase `CustomWebHostService` a <xref:System.ServiceProcess.ServiceBase.Run*>:</span><span class="sxs-lookup"><span data-stu-id="4eb97-547">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="4eb97-548">En `Program.Main`, llame al método de extensión `RunAsCustomService` en lugar de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span><span class="sxs-lookup"><span data-stu-id="4eb97-548">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="4eb97-549">Para ver la ubicación de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> en `Program.Main`, consulte el ejemplo de código que se muestra en la sección [Tipo de implementación](#deployment-type).</span><span class="sxs-lookup"><span data-stu-id="4eb97-549">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="4eb97-550">Escenarios de servidor proxy y equilibrador de carga</span><span class="sxs-lookup"><span data-stu-id="4eb97-550">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="4eb97-551">Los servicios que interactúan con las solicitudes de Internet o de una red corporativa y están detrás de un proxy o de un equilibrador de carga podrían requerir configuración adicional.</span><span class="sxs-lookup"><span data-stu-id="4eb97-551">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="4eb97-552">Para obtener más información, vea <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="4eb97-552">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="4eb97-553">Configuración de puntos de conexión</span><span class="sxs-lookup"><span data-stu-id="4eb97-553">Configure endpoints</span></span>

<span data-ttu-id="4eb97-554">ASP.NET Core se enlaza a `http://localhost:5000` de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="4eb97-554">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="4eb97-555">Configure la dirección URL y el puerto estableciendo la variable de entorno `ASPNETCORE_URLS`.</span><span class="sxs-lookup"><span data-stu-id="4eb97-555">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="4eb97-556">Para información sobre otros enfoques de configuración de direcciones URL y puertos, consulte el artículo en cuestión:</span><span class="sxs-lookup"><span data-stu-id="4eb97-556">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="4eb97-557">En la guía anterior se trata la compatibilidad con los puntos de conexión HTTPS.</span><span class="sxs-lookup"><span data-stu-id="4eb97-557">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="4eb97-558">Por ejemplo, configure la aplicación para HTTPS cuando se use la autenticación con un servicio de Windows.</span><span class="sxs-lookup"><span data-stu-id="4eb97-558">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="4eb97-559">No se admite el uso del certificado de desarrollo HTTPS de ASP.NET Core para proteger un punto de conexión de servicio.</span><span class="sxs-lookup"><span data-stu-id="4eb97-559">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="4eb97-560">Directorio actual y raíz del contenido</span><span class="sxs-lookup"><span data-stu-id="4eb97-560">Current directory and content root</span></span>

<span data-ttu-id="4eb97-561">El directorio de trabajo actual devuelto por una llamada a <xref:System.IO.Directory.GetCurrentDirectory*> para un servicio de Windows es la carpeta *C:\\WINDOWS\\system32*.</span><span class="sxs-lookup"><span data-stu-id="4eb97-561">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="4eb97-562">La carpeta *system32* no es una ubicación adecuada para almacenar los archivos de un servicio (por ejemplo los archivos de configuración).</span><span class="sxs-lookup"><span data-stu-id="4eb97-562">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="4eb97-563">Utilice uno de los siguientes enfoques para mantener los archivos de configuración y los activos de un servicio, así como para acceder a ellos.</span><span class="sxs-lookup"><span data-stu-id="4eb97-563">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="4eb97-564">Configuración de la ruta de acceso raíz del contenido en la carpeta de la aplicación</span><span class="sxs-lookup"><span data-stu-id="4eb97-564">Set the content root path to the app's folder</span></span>

<span data-ttu-id="4eb97-565"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> es la misma ruta de acceso proporcionada al argumento `binPath` cuando se crea un servicio.</span><span class="sxs-lookup"><span data-stu-id="4eb97-565">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="4eb97-566">En lugar de llamar a `GetCurrentDirectory` para crear rutas de acceso a los archivos de configuración, llame a <xref:System.IO.Directory.SetCurrentDirectory*> con la ruta de acceso a la [raíz del contenido](xref:fundamentals/index#content-root) de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4eb97-566">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's [content root](xref:fundamentals/index#content-root).</span></span>

<span data-ttu-id="4eb97-567">En `Program.Main`, determine la ruta de acceso a la carpeta del archivo ejecutable del servicio y use la ruta de acceso para establecer la raíz del contenido de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="4eb97-567">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="4eb97-568">Almacenamiento de los archivos de un servicio en una ubicación adecuada en el disco</span><span class="sxs-lookup"><span data-stu-id="4eb97-568">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="4eb97-569">Especifique una ruta de acceso absoluta con <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> al utilizar un <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> a la carpeta que contiene los archivos.</span><span class="sxs-lookup"><span data-stu-id="4eb97-569">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="4eb97-570">Solucionar problemas</span><span class="sxs-lookup"><span data-stu-id="4eb97-570">Troubleshoot</span></span>

<span data-ttu-id="4eb97-571">Para solucionar problemas de una aplicación de servicio de Windows, consulte <xref:test/troubleshoot>.</span><span class="sxs-lookup"><span data-stu-id="4eb97-571">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="4eb97-572">Errores comunes</span><span class="sxs-lookup"><span data-stu-id="4eb97-572">Common errors</span></span>

* <span data-ttu-id="4eb97-573">Se está usando una versión preliminar o antigua de PowerShell.</span><span class="sxs-lookup"><span data-stu-id="4eb97-573">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="4eb97-574">El servicio registrado no usa la salida **publicada** de la aplicación del comando [dotnet publish](/dotnet/core/tools/dotnet-publish).</span><span class="sxs-lookup"><span data-stu-id="4eb97-574">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="4eb97-575">La salida del comando [dotnet build](/dotnet/core/tools/dotnet-build) no es compatible con la implementación de aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="4eb97-575">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="4eb97-576">Los recursos publicados se encuentran en cualquiera de las siguientes carpetas, según el tipo de implementación:</span><span class="sxs-lookup"><span data-stu-id="4eb97-576">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="4eb97-577">*bin/Release/{PLATAFORMA DE DESTINO}/publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="4eb97-577">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="4eb97-578">*bin/Release/{PLATAFORMA DE DESTINO}/{IDENTIFICADOR DE RUNTIME}/publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="4eb97-578">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="4eb97-579">El servicio no está en el estado EN EJECUCIÓN.</span><span class="sxs-lookup"><span data-stu-id="4eb97-579">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="4eb97-580">Las rutas de acceso a los recursos que usa la aplicación (por ejemplo, certificados) son incorrectas.</span><span class="sxs-lookup"><span data-stu-id="4eb97-580">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="4eb97-581">La ruta de acceso base de un servicio de Windows es *c:\\Windows\\System32*.</span><span class="sxs-lookup"><span data-stu-id="4eb97-581">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="4eb97-582">El usuario no tiene derechos de *inicio de sesión como servicio*.</span><span class="sxs-lookup"><span data-stu-id="4eb97-582">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="4eb97-583">La contraseña del usuario ha expirado o se ha pasado incorrectamente al ejecutar el comando de PowerShell `New-Service`.</span><span class="sxs-lookup"><span data-stu-id="4eb97-583">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="4eb97-584">La aplicación requiere autenticación ASP.NET Core, pero no está configurada para conexiones seguras (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="4eb97-584">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="4eb97-585">El puerto de la dirección URL de solicitud es incorrecto o no está configurado correctamente en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4eb97-585">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="4eb97-586">Registros de eventos del sistema y de aplicación</span><span class="sxs-lookup"><span data-stu-id="4eb97-586">System and Application Event Logs</span></span>

<span data-ttu-id="4eb97-587">Acceda a los registros de eventos del sistema y de aplicación:</span><span class="sxs-lookup"><span data-stu-id="4eb97-587">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="4eb97-588">Abra el menú Inicio, busque *Visor de eventos* y seleccione la aplicación **Visor de eventos**.</span><span class="sxs-lookup"><span data-stu-id="4eb97-588">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="4eb97-589">En **Visor de eventos**, abra el nodo **Registros de Windows**.</span><span class="sxs-lookup"><span data-stu-id="4eb97-589">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="4eb97-590">Seleccione **Sistema** para abrir el registro de eventos del sistema.</span><span class="sxs-lookup"><span data-stu-id="4eb97-590">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="4eb97-591">Seleccione **Aplicación** para abrir el registro de eventos de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4eb97-591">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="4eb97-592">Busque los errores asociados a la aplicación objeto del error.</span><span class="sxs-lookup"><span data-stu-id="4eb97-592">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="4eb97-593">Ejecución de la aplicación en un símbolo del sistema</span><span class="sxs-lookup"><span data-stu-id="4eb97-593">Run the app at a command prompt</span></span>

<span data-ttu-id="4eb97-594">Muchos errores de inicio no generan información útil en el registro de eventos.</span><span class="sxs-lookup"><span data-stu-id="4eb97-594">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="4eb97-595">La causa de algunos errores se puede encontrar mediante la ejecución de la aplicación en un símbolo del sistema en el sistema de hospedaje.</span><span class="sxs-lookup"><span data-stu-id="4eb97-595">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="4eb97-596">Para registrar detalles adicionales de la aplicación, reduzca el [nivel de registro](xref:fundamentals/logging/index#log-level) o ejecute la aplicación en el [entorno de desarrollo](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="4eb97-596">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="4eb97-597">Borrado de memorias caché de paquetes</span><span class="sxs-lookup"><span data-stu-id="4eb97-597">Clear package caches</span></span>

<span data-ttu-id="4eb97-598">Una aplicación en funcionamiento deja de ejecutarse inmediatamente después de actualizar el SDK de .NET Core en la máquina de desarrollo o de cambiar las versiones del paquete en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4eb97-598">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="4eb97-599">En algunos casos, los paquetes incoherentes pueden interrumpir una aplicación al realizar actualizaciones importantes.</span><span class="sxs-lookup"><span data-stu-id="4eb97-599">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="4eb97-600">La mayoría de estos problemas puede corregirse siguiendo estas instrucciones:</span><span class="sxs-lookup"><span data-stu-id="4eb97-600">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="4eb97-601">Elimine las carpetas *bin* y *obj*.</span><span class="sxs-lookup"><span data-stu-id="4eb97-601">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="4eb97-602">Borre las memorias caché del paquete ejecutando [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) desde un shell de comandos.</span><span class="sxs-lookup"><span data-stu-id="4eb97-602">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="4eb97-603">Otra manera de borrar las memorias caché de paquetes es usando la herramienta [nuget.exe](https://www.nuget.org/downloads) y ejecutando el comando `nuget locals all -clear`.</span><span class="sxs-lookup"><span data-stu-id="4eb97-603">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="4eb97-604">*nuget.exe* no es una instalación agrupada con el sistema operativo de escritorio de Windows y se debe obtener de forma independiente en el [sitio web de NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="4eb97-604">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="4eb97-605">Restaure el proyecto y vuelva a compilarlo.</span><span class="sxs-lookup"><span data-stu-id="4eb97-605">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="4eb97-606">Elimine todos los archivos de la carpeta de implementación del servidor antes de volver a implementar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4eb97-606">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="4eb97-607">Aplicación lenta o bloqueada</span><span class="sxs-lookup"><span data-stu-id="4eb97-607">Slow or hanging app</span></span>

<span data-ttu-id="4eb97-608">Un *volcado de memoria* es una instantánea de la memoria del sistema que puede ayudar a determinar la causa de un bloqueo de la aplicación, un error de inicio o una aplicación lenta.</span><span class="sxs-lookup"><span data-stu-id="4eb97-608">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="4eb97-609">Bloqueo o excepción de la aplicación</span><span class="sxs-lookup"><span data-stu-id="4eb97-609">App crashes or encounters an exception</span></span>

<span data-ttu-id="4eb97-610">Obtenga y analice un volcado de memoria en [Informe de errores de Windows (WER)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="4eb97-610">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="4eb97-611">Cree una carpeta para almacenar los archivos de volcado de memoria en `c:\dumps`.</span><span class="sxs-lookup"><span data-stu-id="4eb97-611">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="4eb97-612">Ejecute el script [EnableDumps PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) con el nombre del archivo ejecutable de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="4eb97-612">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="4eb97-613">Ejecute la aplicación en las condiciones que hacen que se produzca el bloqueo.</span><span class="sxs-lookup"><span data-stu-id="4eb97-613">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="4eb97-614">Una vez que se haya producido el bloqueo, ejecute el [script DisableDumps de PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="4eb97-614">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span></span>

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="4eb97-615">Después de que se bloquee la aplicación y se complete la recopilación del volcado de memoria, la aplicación puede finalizar con normalidad.</span><span class="sxs-lookup"><span data-stu-id="4eb97-615">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="4eb97-616">El script de PowerShell configura WER de modo que recopile un máximo de cinco volcados de memoria por aplicación.</span><span class="sxs-lookup"><span data-stu-id="4eb97-616">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="4eb97-617">Los volcados de memoria pueden ocupar una gran cantidad de espacio en disco (hasta varios gigabytes cada uno).</span><span class="sxs-lookup"><span data-stu-id="4eb97-617">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="4eb97-618">La aplicación deja de responder, produce un error durante el inicio o se ejecuta con normalidad</span><span class="sxs-lookup"><span data-stu-id="4eb97-618">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="4eb97-619">Si una aplicación *deja de responder* (se detiene, pero no se bloquea), produce un error durante el inicio o se ejecuta con normalidad, vea [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) (Archivos de volcado de memoria en modo de usuario: selección de la mejor herramienta) para seleccionar una herramienta apropiada para generar el volcado de memoria.</span><span class="sxs-lookup"><span data-stu-id="4eb97-619">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="4eb97-620">Análisis del volcado de memoria</span><span class="sxs-lookup"><span data-stu-id="4eb97-620">Analyze the dump</span></span>

<span data-ttu-id="4eb97-621">El volcado de memoria se puede analizar de varias maneras.</span><span class="sxs-lookup"><span data-stu-id="4eb97-621">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="4eb97-622">Para obtener más información, vea [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file) (Análisis de un archivo de volcado de memoria en modo de usuario).</span><span class="sxs-lookup"><span data-stu-id="4eb97-622">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4eb97-623">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="4eb97-623">Additional resources</span></span>

* <span data-ttu-id="4eb97-624">[Kestrel: configuración de punto de conexión](xref:fundamentals/servers/kestrel#endpoint-configuration) (configuración de HTTPS y compatibilidad de SNI incluidas)</span><span class="sxs-lookup"><span data-stu-id="4eb97-624">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end
