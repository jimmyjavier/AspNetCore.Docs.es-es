---
<span data-ttu-id="497c5-101">Título: autor: Descripción: monikerRange: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="497c5-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="497c5-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="497c5-102">'Blazor'</span></span>
- <span data-ttu-id="497c5-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="497c5-103">'Identity'</span></span>
- <span data-ttu-id="497c5-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="497c5-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="497c5-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="497c5-105">'Razor'</span></span>
- <span data-ttu-id="497c5-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="497c5-106">'SignalR' uid:</span></span> 

---
# <a name="upload-files-in-aspnet-core"></a><span data-ttu-id="497c5-107">Carga de archivos en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="497c5-107">Upload files in ASP.NET Core</span></span>

<span data-ttu-id="497c5-108">Por [Steve Smith](https://ardalis.com/) y [Rutger Storm](https://github.com/rutix)</span><span class="sxs-lookup"><span data-stu-id="497c5-108">By [Steve Smith](https://ardalis.com/) and [Rutger Storm](https://github.com/rutix)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="497c5-109">ASP.NET Core admite la carga de uno o varios archivos mediante el enlace de modelos almacenado en búfer de archivos más pequeños y el streaming no almacenado en búfer de archivos de mayor tamaño.</span><span class="sxs-lookup"><span data-stu-id="497c5-109">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="497c5-110">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="497c5-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="497c5-111">Consideraciones sobre la seguridad</span><span class="sxs-lookup"><span data-stu-id="497c5-111">Security considerations</span></span>

<span data-ttu-id="497c5-112">Tenga precaución al proporcionar a los usuarios la capacidad de cargar archivos en un servidor.</span><span class="sxs-lookup"><span data-stu-id="497c5-112">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="497c5-113">Es posible que los atacantes intenten lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="497c5-113">Attackers may attempt to:</span></span>

* <span data-ttu-id="497c5-114">Ejecutar ataques por [denegación de servicio](/windows-hardware/drivers/ifs/denial-of-service).</span><span class="sxs-lookup"><span data-stu-id="497c5-114">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="497c5-115">Cargar virus o malware.</span><span class="sxs-lookup"><span data-stu-id="497c5-115">Upload viruses or malware.</span></span>
* <span data-ttu-id="497c5-116">Poner en riesgo redes y servidores de otras maneras.</span><span class="sxs-lookup"><span data-stu-id="497c5-116">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="497c5-117">Estos son algunos de los pasos de seguridad con los que se reduce la probabilidad de sufrir ataques:</span><span class="sxs-lookup"><span data-stu-id="497c5-117">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="497c5-118">Cargue los archivos a un área de carga de archivos dedicada, preferiblemente una unidad que no sea de sistema.</span><span class="sxs-lookup"><span data-stu-id="497c5-118">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="497c5-119">Una ubicación dedicada facilita la imposición de restricciones de seguridad en los archivos cargados.</span><span class="sxs-lookup"><span data-stu-id="497c5-119">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="497c5-120">Deshabilite la ejecución de los permisos en la ubicación de carga de archivos.&dagger;</span><span class="sxs-lookup"><span data-stu-id="497c5-120">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="497c5-121">Los archivos cargados **no** se deben persistir en el mismo árbol de directorio que la aplicación.&dagger;</span><span class="sxs-lookup"><span data-stu-id="497c5-121">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="497c5-122">Use un nombre de archivo seguro determinado por la aplicación.</span><span class="sxs-lookup"><span data-stu-id="497c5-122">Use a safe file name determined by the app.</span></span> <span data-ttu-id="497c5-123">No use un nombre de archivo proporcionado por el usuario o el nombre de archivo que no es de confianza del archivo cargado. &dagger; HTML codifica el nombre de archivo que no es de confianza al mostrarlo.</span><span class="sxs-lookup"><span data-stu-id="497c5-123">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="497c5-124">Por ejemplo, registrando el nombre de archivo o mostrándose en la interfaz de usuario (la Razor salida codifica automáticamente html).</span><span class="sxs-lookup"><span data-stu-id="497c5-124">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="497c5-125">Permita solo las extensiones de archivo aprobadas para la especificación de diseño de la aplicación.&dagger;</span><span class="sxs-lookup"><span data-stu-id="497c5-125">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="497c5-126">Compruebe que las comprobaciones del lado cliente se realizan en el servidor. &dagger; Las comprobaciones del lado cliente son fáciles de eludir.</span><span class="sxs-lookup"><span data-stu-id="497c5-126">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="497c5-127">Compruebe el tamaño de un archivo cargado.</span><span class="sxs-lookup"><span data-stu-id="497c5-127">Check the size of an uploaded file.</span></span> <span data-ttu-id="497c5-128">Establezca un límite de tamaño máximo para evitar cargas grandes.&dagger;</span><span class="sxs-lookup"><span data-stu-id="497c5-128">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="497c5-129">Cuando un archivo cargado con el mismo nombre no deba sobrescribir los archivos, vuelva a comprobar el nombre de archivo en la base de datos o en el almacenamiento físico antes de cargarlo.</span><span class="sxs-lookup"><span data-stu-id="497c5-129">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="497c5-130">**Ejecute un detector de virus o malware en el contenido cargado antes de que se almacene el archivo.**</span><span class="sxs-lookup"><span data-stu-id="497c5-130">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="497c5-131">&dagger;La aplicación de ejemplo muestra un enfoque que cumple los criterios.</span><span class="sxs-lookup"><span data-stu-id="497c5-131">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="497c5-132">La carga de código malintencionado en un sistema suele ser el primer paso para ejecutar código que puede:</span><span class="sxs-lookup"><span data-stu-id="497c5-132">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="497c5-133">Obtener el control completo de un sistema.</span><span class="sxs-lookup"><span data-stu-id="497c5-133">Completely gain control of a system.</span></span>
> * <span data-ttu-id="497c5-134">Sobrecargar un sistema de manera que el sistema se bloquea.</span><span class="sxs-lookup"><span data-stu-id="497c5-134">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="497c5-135">Poner en peligro los datos del usuario o del sistema.</span><span class="sxs-lookup"><span data-stu-id="497c5-135">Compromise user or system data.</span></span>
> * <span data-ttu-id="497c5-136">Aplicar grafitis a una interfaz de usuario pública.</span><span class="sxs-lookup"><span data-stu-id="497c5-136">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="497c5-137">Vea los siguientes recursos para más información sobre cómo reducir el área expuesta de ataques al aceptar archivos de los usuarios:</span><span class="sxs-lookup"><span data-stu-id="497c5-137">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * <span data-ttu-id="497c5-138">[Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload) (Carga de archivos sin restricciones)</span><span class="sxs-lookup"><span data-stu-id="497c5-138">[Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)</span></span>
> * [<span data-ttu-id="497c5-139">Azure Security: asegúrese de que los controles adecuados estén en vigor al aceptar archivos de usuarios</span><span class="sxs-lookup"><span data-stu-id="497c5-139">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="497c5-140">Para más información sobre cómo implementar medidas de seguridad, incluidos ejemplos de la aplicación de ejemplo, consulte la sección [Validación](#validation).</span><span class="sxs-lookup"><span data-stu-id="497c5-140">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="497c5-141">Escenarios de almacenamiento</span><span class="sxs-lookup"><span data-stu-id="497c5-141">Storage scenarios</span></span>

<span data-ttu-id="497c5-142">Las opciones de almacenamiento comunes para los archivos incluyen:</span><span class="sxs-lookup"><span data-stu-id="497c5-142">Common storage options for files include:</span></span>

* <span data-ttu-id="497c5-143">Base de datos</span><span class="sxs-lookup"><span data-stu-id="497c5-143">Database</span></span>

  * <span data-ttu-id="497c5-144">En el caso de cargas de archivos pequeñas, una base de datos suele ser más rápida que las opciones de almacenamiento físico (sistema de archivos o recurso compartido de red).</span><span class="sxs-lookup"><span data-stu-id="497c5-144">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="497c5-145">Una base de datos suele ser más conveniente que las opciones de almacenamiento físico, ya que la recuperación de un registro de base de datos para los datos de usuario puede proporcionar el contenido del archivo (por ejemplo, una imagen de avatar).</span><span class="sxs-lookup"><span data-stu-id="497c5-145">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="497c5-146">Una base de datos puede ser más económica que usar un servicio de almacenamiento de datos.</span><span class="sxs-lookup"><span data-stu-id="497c5-146">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="497c5-147">Almacenamiento físico (sistema de archivos o recurso compartido de red)</span><span class="sxs-lookup"><span data-stu-id="497c5-147">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="497c5-148">Para cargas de archivos de gran tamaño:</span><span class="sxs-lookup"><span data-stu-id="497c5-148">For large file uploads:</span></span>
    * <span data-ttu-id="497c5-149">Los límites de base de datos pueden restringir el tamaño de la carga.</span><span class="sxs-lookup"><span data-stu-id="497c5-149">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="497c5-150">El almacenamiento físico suele ser menos económico que el almacenamiento en una base de datos.</span><span class="sxs-lookup"><span data-stu-id="497c5-150">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="497c5-151">El almacenamiento físico puede ser más económico que usar un servicio de almacenamiento de datos.</span><span class="sxs-lookup"><span data-stu-id="497c5-151">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="497c5-152">El proceso de la aplicación debe tener permisos de lectura y escritura en la ubicación de almacenamiento.</span><span class="sxs-lookup"><span data-stu-id="497c5-152">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="497c5-153">**Nunca conceda el permiso de ejecución.**</span><span class="sxs-lookup"><span data-stu-id="497c5-153">**Never grant execute permission.**</span></span>

* <span data-ttu-id="497c5-154">Servicio de almacenamiento de datos (por ejemplo, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span><span class="sxs-lookup"><span data-stu-id="497c5-154">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="497c5-155">Los servicios suelen ofrecer una escalabilidad y resistencia mejoradas sobre las soluciones locales que normalmente están sujetas a únicos puntos de error.</span><span class="sxs-lookup"><span data-stu-id="497c5-155">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="497c5-156">Los servicios pueden tener un costo menor en escenarios de infraestructura de almacenamiento de gran tamaño.</span><span class="sxs-lookup"><span data-stu-id="497c5-156">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="497c5-157">Para obtener más información, vea [Inicio rápido: usar .net para crear un BLOB en el almacenamiento de objetos](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span><span class="sxs-lookup"><span data-stu-id="497c5-157">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="497c5-158">Escenarios de carga de archivos</span><span class="sxs-lookup"><span data-stu-id="497c5-158">File upload scenarios</span></span>

<span data-ttu-id="497c5-159">Dos enfoques generales para cargar archivos son el almacenamiento en búfer y el streaming.</span><span class="sxs-lookup"><span data-stu-id="497c5-159">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="497c5-160">**de respuesta**</span><span class="sxs-lookup"><span data-stu-id="497c5-160">**Buffering**</span></span>

<span data-ttu-id="497c5-161">El archivo completo se lee en un <xref:Microsoft.AspNetCore.Http.IFormFile>, que es una representación de C# del archivo que se usa para procesar o guardar el archivo.</span><span class="sxs-lookup"><span data-stu-id="497c5-161">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="497c5-162">Los recursos (disco, memoria) que se usan en las cargas de archivos dependen de la cantidad y del tamaño de las cargas de archivos que se realizan simultáneamente.</span><span class="sxs-lookup"><span data-stu-id="497c5-162">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="497c5-163">Si una aplicación intenta almacenar demasiadas cargas en el búfer, el sitio se bloquea cuando se queda sin memoria o sin espacio en disco.</span><span class="sxs-lookup"><span data-stu-id="497c5-163">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="497c5-164">Si el tamaño o la frecuencia de las cargas de archivos está agotando los recursos de la aplicación, use el streaming.</span><span class="sxs-lookup"><span data-stu-id="497c5-164">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="497c5-165">Cualquier archivo almacenado en búfer único que supere los 64 KB se mueve de la memoria a un archivo temporal en el disco.</span><span class="sxs-lookup"><span data-stu-id="497c5-165">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="497c5-166">En las secciones siguientes de este tema se habla del almacenamiento en búfer de archivos pequeños:</span><span class="sxs-lookup"><span data-stu-id="497c5-166">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="497c5-167">Almacenamiento físico</span><span class="sxs-lookup"><span data-stu-id="497c5-167">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="497c5-168">Base de datos</span><span class="sxs-lookup"><span data-stu-id="497c5-168">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="497c5-169">**Streaming**</span><span class="sxs-lookup"><span data-stu-id="497c5-169">**Streaming**</span></span>

<span data-ttu-id="497c5-170">El archivo se recibe de una solicitud de varias partes y lo procesa o guarda directamente la aplicación.</span><span class="sxs-lookup"><span data-stu-id="497c5-170">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="497c5-171">El streaming no mejora considerablemente el rendimiento.</span><span class="sxs-lookup"><span data-stu-id="497c5-171">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="497c5-172">El streaming reduce las demandas de memoria o espacio en disco cuando se cargan archivos.</span><span class="sxs-lookup"><span data-stu-id="497c5-172">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="497c5-173">El streaming de archivos grandes se describe en la sección [Carga de archivos de gran tamaño con streaming](#upload-large-files-with-streaming).</span><span class="sxs-lookup"><span data-stu-id="497c5-173">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="497c5-174">Carga de archivos pequeños con enlace de modelos almacenado en búfer al almacenamiento físico</span><span class="sxs-lookup"><span data-stu-id="497c5-174">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="497c5-175">Para cargar archivos pequeños, se puede usar un formulario de varias partes o construir una solicitud POST con JavaScript.</span><span class="sxs-lookup"><span data-stu-id="497c5-175">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="497c5-176">En el ejemplo siguiente se muestra el uso de un Razor formulario de páginas para cargar un único archivo (*pages/BufferedSingleFileUploadPhysical. cshtml* en la aplicación de ejemplo):</span><span class="sxs-lookup"><span data-stu-id="497c5-176">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
            <span asp-validation-for="FileUpload.FormFile"></span>
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload" />
</form>
```

<span data-ttu-id="497c5-177">El ejemplo siguiente es análogo al ejemplo anterior, salvo en que:</span><span class="sxs-lookup"><span data-stu-id="497c5-177">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="497c5-178">([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) de JavaScript se usa para enviar los datos del formulario.</span><span class="sxs-lookup"><span data-stu-id="497c5-178">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="497c5-179">No hay ninguna validación.</span><span class="sxs-lookup"><span data-stu-id="497c5-179">There's no validation.</span></span>

```cshtml
<form action="BufferedSingleFileUploadPhysical/?handler=Upload" 
      enctype="multipart/form-data" onsubmit="AJAXSubmit(this);return false;" 
      method="post">
    <dl>
        <dt>
            <label for="FileUpload_FormFile">File</label>
        </dt>
        <dd>
            <input id="FileUpload_FormFile" type="file" 
                name="FileUpload.FormFile" />
        </dd>
    </dl>

    <input class="btn" type="submit" value="Upload" />

    <div style="margin-top:15px">
        <output name="result"></output>
    </div>
</form>

<script>
  async function AJAXSubmit (oFormElement) {
    var resultElement = oFormElement.elements.namedItem("result");
    const formData = new FormData(oFormElement);

    try {
    const response = await fetch(oFormElement.action, {
      method: 'POST',
      body: formData
    });

    if (response.ok) {
      window.location.href = '/';
    }

    resultElement.value = 'Result: ' + response.status + ' ' + 
      response.statusText;
    } catch (error) {
      console.error('Error:', error);
    }
  }
</script>
```

<span data-ttu-id="497c5-180">Para realizar la solicitud POST en JavaScript para los clientes que [no admiten Fetch API](https://caniuse.com/#feat=fetch), use uno de estos enfoques:</span><span class="sxs-lookup"><span data-stu-id="497c5-180">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="497c5-181">Use un Fetch Polyfill (por ejemplo, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="497c5-181">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="497c5-182">Mediante `XMLHttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="497c5-182">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="497c5-183">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="497c5-183">For example:</span></span>

  ```javascript
  <script>
    "use strict";

    function AJAXSubmit (oFormElement) {
      var oReq = new XMLHttpRequest();
      oReq.onload = function(e) { 
      oFormElement.elements.namedItem("result").value = 
        'Result: ' + this.status + ' ' + this.statusText;
      };
      oReq.open("post", oFormElement.action);
      oReq.send(new FormData(oFormElement));
    }
  </script>
  ```

<span data-ttu-id="497c5-184">Para admitir las cargas de archivos, los formularios HTML deben especificar un tipo de codificación (`enctype`) de `multipart/form-data`.</span><span class="sxs-lookup"><span data-stu-id="497c5-184">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="497c5-185">Para que un elemento de entrada `files` admita al carga de varios archivos, proporcione el atributo `multiple` en el elemento `<input>`:</span><span class="sxs-lookup"><span data-stu-id="497c5-185">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="497c5-186">Es posible acceder a archivos individuales cargados en el servidor a través del [enlace de modelos](xref:mvc/models/model-binding) mediante <xref:Microsoft.AspNetCore.Http.IFormFile>.</span><span class="sxs-lookup"><span data-stu-id="497c5-186">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="497c5-187">La aplicación de ejemplo muestra varias cargas de archivos almacenados en búfer para escenarios de almacenamiento físico y base de datos.</span><span class="sxs-lookup"><span data-stu-id="497c5-187">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename"></a>

> [!WARNING]
> <span data-ttu-id="497c5-188">**No** utilice la propiedad `FileName` de <xref:Microsoft.AspNetCore.Http.IFormFile> para usos distintos a la presentación y el registro.</span><span class="sxs-lookup"><span data-stu-id="497c5-188">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="497c5-189">Para fines de presentación y registro, codifique el nombre de archivo en HTML.</span><span class="sxs-lookup"><span data-stu-id="497c5-189">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="497c5-190">Un atacante puede proporcionar un nombre de archivo malintencionado, incluidas rutas de acceso completas o relativas.</span><span class="sxs-lookup"><span data-stu-id="497c5-190">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="497c5-191">Las aplicaciones deben:</span><span class="sxs-lookup"><span data-stu-id="497c5-191">Applications should:</span></span>
>
> * <span data-ttu-id="497c5-192">Quitar la ruta de acceso del nombre de archivo proporcionado por el usuario.</span><span class="sxs-lookup"><span data-stu-id="497c5-192">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="497c5-193">Guardar el nombre de archivo codificado en HTML, sin la ruta de acceso para la interfaz de usuario o el registro.</span><span class="sxs-lookup"><span data-stu-id="497c5-193">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="497c5-194">Generar un nombre de archivo aleatorio nuevo para el almacenamiento.</span><span class="sxs-lookup"><span data-stu-id="497c5-194">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="497c5-195">En el código siguiente se quita la ruta de acceso del nombre del archivo:</span><span class="sxs-lookup"><span data-stu-id="497c5-195">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="497c5-196">Los ejemplos proporcionados hasta ahora no tienen en cuenta las consideraciones de seguridad.</span><span class="sxs-lookup"><span data-stu-id="497c5-196">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="497c5-197">Se proporciona información adicional en las secciones siguientes y en la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="497c5-197">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="497c5-198">Consideraciones sobre la seguridad</span><span class="sxs-lookup"><span data-stu-id="497c5-198">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="497c5-199">Validación</span><span class="sxs-lookup"><span data-stu-id="497c5-199">Validation</span></span>](#validation)

<span data-ttu-id="497c5-200">Al cargar archivos mediante el enlace de modelos y <xref:Microsoft.AspNetCore.Http.IFormFile>, el método de acción puede aceptar:</span><span class="sxs-lookup"><span data-stu-id="497c5-200">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="497c5-201">Un <xref:Microsoft.AspNetCore.Http.IFormFile> único.</span><span class="sxs-lookup"><span data-stu-id="497c5-201">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="497c5-202">Cualquiera de las colecciones siguientes que representan varios archivos:</span><span class="sxs-lookup"><span data-stu-id="497c5-202">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="497c5-203">[Lista](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="497c5-203">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="497c5-204">El enlace coincide con los archivos de formulario por nombre.</span><span class="sxs-lookup"><span data-stu-id="497c5-204">Binding matches form files by name.</span></span> <span data-ttu-id="497c5-205">Por ejemplo, el valor `name` HTML en `<input type="file" name="formFile">` debe coincidir con la propiedad/el parámetro enlazado de C# (`FormFile`).</span><span class="sxs-lookup"><span data-stu-id="497c5-205">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="497c5-206">Para más información, consulte la sección [Coincidencia del valor de atributo de nombre con el nombre del parámetro del método POST](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="497c5-206">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="497c5-207">En el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="497c5-207">The following example:</span></span>

* <span data-ttu-id="497c5-208">Recorre en bucle uno o más archivos cargados.</span><span class="sxs-lookup"><span data-stu-id="497c5-208">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="497c5-209">Usa [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) para devolver una ruta de acceso completa de un archivo, incluido el nombre de archivo.</span><span class="sxs-lookup"><span data-stu-id="497c5-209">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="497c5-210">Guarda los archivos en el sistema de archivos local con un nombre de archivo generado por la aplicación.</span><span class="sxs-lookup"><span data-stu-id="497c5-210">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="497c5-211">Devuelve el número total y el tamaño de los archivos cargados.</span><span class="sxs-lookup"><span data-stu-id="497c5-211">Returns the total number and size of files uploaded.</span></span>

```csharp
public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> files)
{
    long size = files.Sum(f => f.Length);

    foreach (var formFile in files)
    {
        if (formFile.Length > 0)
        {
            var filePath = Path.GetTempFileName();

            using (var stream = System.IO.File.Create(filePath))
            {
                await formFile.CopyToAsync(stream);
            }
        }
    }

    // Process uploaded files
    // Don't rely on or trust the FileName property without validation.

    return Ok(new { count = files.Count, size });
}
```

<span data-ttu-id="497c5-212">Use `Path.GetRandomFileName` para generar un nombre de archivo sin una ruta de acceso.</span><span class="sxs-lookup"><span data-stu-id="497c5-212">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="497c5-213">En el ejemplo siguiente, la ruta de acceso se obtiene de la configuración:</span><span class="sxs-lookup"><span data-stu-id="497c5-213">In the following example, the path is obtained from configuration:</span></span>

```csharp
foreach (var formFile in files)
{
    if (formFile.Length > 0)
    {
        var filePath = Path.Combine(_config["StoredFilesPath"], 
            Path.GetRandomFileName());

        using (var stream = System.IO.File.Create(filePath))
        {
            await formFile.CopyToAsync(stream);
        }
    }
}
```

<span data-ttu-id="497c5-214">La ruta de acceso pasada a <xref:System.IO.FileStream> *debe* incluir el nombre de archivo.</span><span class="sxs-lookup"><span data-stu-id="497c5-214">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="497c5-215">Si no se proporciona el nombre de archivo, se produce una <xref:System.UnauthorizedAccessException> en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="497c5-215">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="497c5-216">Los archivos que se cargan usando la técnica <xref:Microsoft.AspNetCore.Http.IFormFile> se almacenan en búfer en memoria o en disco en el servidor web antes de procesarse.</span><span class="sxs-lookup"><span data-stu-id="497c5-216">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="497c5-217">Dentro del método de acción, se puede tener acceso al contenido de <xref:Microsoft.AspNetCore.Http.IFormFile> como <xref:System.IO.Stream>.</span><span class="sxs-lookup"><span data-stu-id="497c5-217">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="497c5-218">Además del sistema de archivos local, los archivos se pueden guardar en un recurso compartido de red o en un servicio de almacenamiento de archivos, como [Azure Blob Storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span><span class="sxs-lookup"><span data-stu-id="497c5-218">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="497c5-219">Para ver otro ejemplo que recorre en bucle varios archivos para cargar y usa nombres de archivo seguros, consulte *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* en la aplicación de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="497c5-219">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="497c5-220">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) arroja una <xref:System.IO.IOException> si se crean más de 65 535 archivos sin eliminar los archivos temporales anteriores.</span><span class="sxs-lookup"><span data-stu-id="497c5-220">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="497c5-221">El límite de 65 535 archivos es un límite por servidor.</span><span class="sxs-lookup"><span data-stu-id="497c5-221">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="497c5-222">Para más información sobre este límite en el sistema operativo Windows, consulte las notas en los temas siguientes:</span><span class="sxs-lookup"><span data-stu-id="497c5-222">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="497c5-223">Función GetTempFileNameA</span><span class="sxs-lookup"><span data-stu-id="497c5-223">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="497c5-224">Carga de archivos pequeños con enlace de modelos almacenado en búfer a una base de datos</span><span class="sxs-lookup"><span data-stu-id="497c5-224">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="497c5-225">Para almacenar datos de archivo binario en una base de datos con [Entity Framework](/ef/core/index), defina una propiedad de matriz <xref:System.Byte> en la entidad:</span><span class="sxs-lookup"><span data-stu-id="497c5-225">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="497c5-226">Especifique una propiedad de modelo de página para la clase que incluya un <xref:Microsoft.AspNetCore.Http.IFormFile>:</span><span class="sxs-lookup"><span data-stu-id="497c5-226">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

```csharp
public class BufferedSingleFileUploadDbModel : PageModel
{
    ...

    [BindProperty]
    public BufferedSingleFileUploadDb FileUpload { get; set; }

    ...
}

public class BufferedSingleFileUploadDb
{
    [Required]
    [Display(Name="File")]
    public IFormFile FormFile { get; set; }
}
```

> [!NOTE]
> <span data-ttu-id="497c5-227"><xref:Microsoft.AspNetCore.Http.IFormFile> se puede usar directamente como un parámetro de método de acción o como una propiedad de modelo enlazado.</span><span class="sxs-lookup"><span data-stu-id="497c5-227"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="497c5-228">En el ejemplo anterior se utiliza una propiedad de modelo enlazado.</span><span class="sxs-lookup"><span data-stu-id="497c5-228">The prior example uses a bound model property.</span></span>

<span data-ttu-id="497c5-229">`FileUpload`Se utiliza en el Razor formulario de páginas:</span><span class="sxs-lookup"><span data-stu-id="497c5-229">The `FileUpload` is used in the Razor Pages form:</span></span>

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload">
</form>
```

<span data-ttu-id="497c5-230">Cuando el formulario se publique en el servidor, copie el <xref:Microsoft.AspNetCore.Http.IFormFile> en un flujo y guárdelo como matriz de bytes en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="497c5-230">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="497c5-231">En el ejemplo siguiente, `_dbContext` almacena el contexto de base de datos de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="497c5-231">In the following example, `_dbContext` stores the app's database context:</span></span>

```csharp
public async Task<IActionResult> OnPostUploadAsync()
{
    using (var memoryStream = new MemoryStream())
    {
        await FileUpload.FormFile.CopyToAsync(memoryStream);

        // Upload the file if less than 2 MB
        if (memoryStream.Length < 2097152)
        {
            var file = new AppFile()
            {
                Content = memoryStream.ToArray()
            };

            _dbContext.File.Add(file);

            await _dbContext.SaveChangesAsync();
        }
        else
        {
            ModelState.AddModelError("File", "The file is too large.");
        }
    }

    return Page();
}
```

<span data-ttu-id="497c5-232">El ejemplo anterior es similar a un escenario que se muestra en la aplicación de ejemplo:</span><span class="sxs-lookup"><span data-stu-id="497c5-232">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="497c5-233">*Pages/BufferedSingleFileUploadDb.cshtml*</span><span class="sxs-lookup"><span data-stu-id="497c5-233">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="497c5-234">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="497c5-234">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="497c5-235">Tenga cuidado al almacenar los datos binarios en bases de datos relacionales, ya que esto puede repercutir adversamente en el rendimiento.</span><span class="sxs-lookup"><span data-stu-id="497c5-235">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="497c5-236">No se base ni confíe en la propiedad `FileName` de <xref:Microsoft.AspNetCore.Http.IFormFile> sin validarla.</span><span class="sxs-lookup"><span data-stu-id="497c5-236">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="497c5-237">La propiedad `FileName` solo debe usarse para fines de presentación y solo después de la codificación HTML.</span><span class="sxs-lookup"><span data-stu-id="497c5-237">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="497c5-238">Los ejemplos proporcionados no tienen en cuenta las consideraciones de seguridad.</span><span class="sxs-lookup"><span data-stu-id="497c5-238">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="497c5-239">Se proporciona información adicional en las secciones siguientes y en la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="497c5-239">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="497c5-240">Consideraciones sobre la seguridad</span><span class="sxs-lookup"><span data-stu-id="497c5-240">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="497c5-241">Validación</span><span class="sxs-lookup"><span data-stu-id="497c5-241">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="497c5-242">Carga de archivos de gran tamaño con streaming</span><span class="sxs-lookup"><span data-stu-id="497c5-242">Upload large files with streaming</span></span>

<span data-ttu-id="497c5-243">En el ejemplo siguiente se muestra cómo usar JavaScript para transmitir un archivo a una acción de controlador.</span><span class="sxs-lookup"><span data-stu-id="497c5-243">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="497c5-244">El token de antifalsificación del archivo se genera por medio de un atributo de filtro personalizado y se pasa a los encabezados HTTP del cliente, en lugar de en el cuerpo de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="497c5-244">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="497c5-245">Dado que el método de acción procesa los datos cargados directamente, el enlace de modelos del formulario se deshabilita por otro filtro personalizado.</span><span class="sxs-lookup"><span data-stu-id="497c5-245">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="497c5-246">Dentro de la acción, el contenido del formulario se lee usando un `MultipartReader` (que lee cada `MultipartSection` individual), de forma que el archivo se procesa o el contenido se almacena, según corresponda.</span><span class="sxs-lookup"><span data-stu-id="497c5-246">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="497c5-247">Una vez leídas las secciones de varias partes, la acción realiza su propio enlace de modelos.</span><span class="sxs-lookup"><span data-stu-id="497c5-247">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="497c5-248">La respuesta de la página inicial carga el formulario y guarda un token de antifalsificación en una cookie (a través del atributo `GenerateAntiforgeryTokenCookieAttribute`).</span><span class="sxs-lookup"><span data-stu-id="497c5-248">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="497c5-249">El atributo usa la [compatibilidad de antifalsificación](xref:security/anti-request-forgery) integrada de ASP.NET Core para establecer una cookie con un token de solicitud:</span><span class="sxs-lookup"><span data-stu-id="497c5-249">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="497c5-250">El `DisableFormValueModelBindingAttribute` se usa para deshabilitar el enlace de modelos:</span><span class="sxs-lookup"><span data-stu-id="497c5-250">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="497c5-251">En la aplicación de ejemplo, `GenerateAntiforgeryTokenCookieAttribute` y `DisableFormValueModelBindingAttribute` se aplican como filtros a los modelos de aplicación de página de `/StreamedSingleFileUploadDb` y `/StreamedSingleFileUploadPhysical` en `Startup.ConfigureServices` mediante [ Razor las convenciones de páginas](xref:razor-pages/razor-pages-conventions):</span><span class="sxs-lookup"><span data-stu-id="497c5-251">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_AddRazorPages&highlight=8-11,17-20)]

<span data-ttu-id="497c5-252">Dado que el enlace de modelos no lee el formulario, los parámetros enlazados desde el formulario no se enlazan (la consulta, la ruta y el encabezado siguen funcionando).</span><span class="sxs-lookup"><span data-stu-id="497c5-252">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="497c5-253">El método de acción funciona directamente con la propiedad `Request`.</span><span class="sxs-lookup"><span data-stu-id="497c5-253">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="497c5-254">Se usa un elemento `MultipartReader` para leer cada sección.</span><span class="sxs-lookup"><span data-stu-id="497c5-254">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="497c5-255">Los datos de clave-valor se almacenan en un `KeyValueAccumulator`.</span><span class="sxs-lookup"><span data-stu-id="497c5-255">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="497c5-256">Una vez leídas las secciones de varias partes, el contenido del `KeyValueAccumulator` se usa para enlazar los datos del formulario a un tipo de modelo.</span><span class="sxs-lookup"><span data-stu-id="497c5-256">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="497c5-257">El método `StreamingController.UploadDatabase` completo para streaming a una base de datos con EF Core:</span><span class="sxs-lookup"><span data-stu-id="497c5-257">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="497c5-258">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span><span class="sxs-lookup"><span data-stu-id="497c5-258">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="497c5-259">El método `StreamingController.UploadPhysical` completo para streaming a una ubicación física:</span><span class="sxs-lookup"><span data-stu-id="497c5-259">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="497c5-260">En la aplicación de ejemplo, las comprobaciones de validación las controla `FileHelpers.ProcessStreamedFile`.</span><span class="sxs-lookup"><span data-stu-id="497c5-260">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="497c5-261">Validación</span><span class="sxs-lookup"><span data-stu-id="497c5-261">Validation</span></span>

<span data-ttu-id="497c5-262">La clase `FileHelpers` de la aplicación de ejemplo muestra varias comprobaciones de cargas de archivos de streaming y <xref:Microsoft.AspNetCore.Http.IFormFile> almacenado en búfer.</span><span class="sxs-lookup"><span data-stu-id="497c5-262">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="497c5-263">Para procesar cargas de archivos almacenadas en búfer de <xref:Microsoft.AspNetCore.Http.IFormFile> en la aplicación de ejemplo, consulte el método `ProcessFormFile` en el archivo *Utilities/FileHelpers.cs*.</span><span class="sxs-lookup"><span data-stu-id="497c5-263">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="497c5-264">Para procesar archivos de streaming, consulte el método `ProcessStreamedFile` en el mismo archivo.</span><span class="sxs-lookup"><span data-stu-id="497c5-264">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="497c5-265">Los métodos de procesamiento de validación mostrados en la aplicación de ejemplo no examinan el contenido de los archivos cargados.</span><span class="sxs-lookup"><span data-stu-id="497c5-265">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="497c5-266">En la mayoría de los escenarios de producción, se usa una API de analizador de virus/malware en el archivo antes de que el archivo esté disponible para los usuarios u otros sistemas.</span><span class="sxs-lookup"><span data-stu-id="497c5-266">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="497c5-267">Aunque el ejemplo de tema proporciona un ejemplo funcional de técnicas de validación, no implemente la clase `FileHelpers` en una aplicación de producción a menos que:</span><span class="sxs-lookup"><span data-stu-id="497c5-267">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="497c5-268">Comprenda totalmente la implementación.</span><span class="sxs-lookup"><span data-stu-id="497c5-268">Fully understand the implementation.</span></span>
> * <span data-ttu-id="497c5-269">Modifique la implementación según corresponda en función del entorno y las especificaciones de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="497c5-269">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="497c5-270">**No implemente nunca de manera indiscriminada el código de seguridad en una aplicación sin abordar estos requisitos.**</span><span class="sxs-lookup"><span data-stu-id="497c5-270">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="497c5-271">Validación de contenido</span><span class="sxs-lookup"><span data-stu-id="497c5-271">Content validation</span></span>

<span data-ttu-id="497c5-272">**Use una API de detección de virus/malware de terceros en el contenido cargado.**</span><span class="sxs-lookup"><span data-stu-id="497c5-272">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="497c5-273">El análisis de archivos exige recursos del servidor en escenarios de gran volumen.</span><span class="sxs-lookup"><span data-stu-id="497c5-273">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="497c5-274">Si disminuye el rendimiento de procesamiento de solicitudes debido al análisis de archivos, considere la posibilidad de descargar el trabajo de análisis a un [servicio en segundo plano](xref:fundamentals/host/hosted-services), posiblemente un servicio que se ejecute en otro servidor desde el servidor de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="497c5-274">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="497c5-275">Habitualmente, los archivos cargados se almacenan en un área en cuarentena hasta que el programa de detección de virus en segundo plano los revisa.</span><span class="sxs-lookup"><span data-stu-id="497c5-275">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="497c5-276">Cuando se pasa un archivo, se mueve a la ubicación de almacenamiento de archivos habitual.</span><span class="sxs-lookup"><span data-stu-id="497c5-276">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="497c5-277">Por lo general, estos pasos se realizan junto con un registro de base de datos que indica el estado de análisis de un archivo.</span><span class="sxs-lookup"><span data-stu-id="497c5-277">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="497c5-278">Mediante el uso de este enfoque, la aplicación y el servidor de aplicaciones permanecen centrados en responder a las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="497c5-278">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="497c5-279">Validación de la extensión del archivo</span><span class="sxs-lookup"><span data-stu-id="497c5-279">File extension validation</span></span>

<span data-ttu-id="497c5-280">La extensión del archivo cargado debe comprobarse con una lista de extensiones permitidas.</span><span class="sxs-lookup"><span data-stu-id="497c5-280">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="497c5-281">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="497c5-281">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="497c5-282">Validación de firma del archivo</span><span class="sxs-lookup"><span data-stu-id="497c5-282">File signature validation</span></span>

<span data-ttu-id="497c5-283">La firma de un archivo viene determinada por los primeros bytes al principio de un archivo.</span><span class="sxs-lookup"><span data-stu-id="497c5-283">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="497c5-284">Estos bytes se pueden usar para indicar si la extensión coincide con el contenido del archivo.</span><span class="sxs-lookup"><span data-stu-id="497c5-284">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="497c5-285">La aplicación de ejemplo comprueba las firmas de archivo de algunos tipos de archivo comunes.</span><span class="sxs-lookup"><span data-stu-id="497c5-285">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="497c5-286">En el ejemplo siguiente, la firma de archivo de una imagen JPEG se comprueba con respecto al archivo:</span><span class="sxs-lookup"><span data-stu-id="497c5-286">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

```csharp
private static readonly Dictionary<string, List<byte[]>> _fileSignature = 
    new Dictionary<string, List<byte[]>>
{
    { ".jpeg", new List<byte[]>
        {
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE2 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE3 },
        }
    },
};

using (var reader = new BinaryReader(uploadedFileData))
{
    var signatures = _fileSignature[ext];
    var headerBytes = reader.ReadBytes(signatures.Max(m => m.Length));
    
    return signatures.Any(signature => 
        headerBytes.Take(signature.Length).SequenceEqual(signature));
}
```

<span data-ttu-id="497c5-287">Para obtener firmas de archivo adicionales, consulte la [base de datos de firmas de archivo](https://www.filesignatures.net/) y las especificaciones de archivo oficiales.</span><span class="sxs-lookup"><span data-stu-id="497c5-287">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="497c5-288">Seguridad de nombre de archivo</span><span class="sxs-lookup"><span data-stu-id="497c5-288">File name security</span></span>

<span data-ttu-id="497c5-289">Nunca use un nombre de archivo proporcionado por el cliente para guardar un archivo en el almacenamiento físico.</span><span class="sxs-lookup"><span data-stu-id="497c5-289">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="497c5-290">Cree un nombre de archivo seguro para el archivo con [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) o [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) para crear una ruta de acceso completa (incluido el nombre de archivo) para el almacenamiento temporal.</span><span class="sxs-lookup"><span data-stu-id="497c5-290">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

Razor<span data-ttu-id="497c5-291">HTML codifica automáticamente los valores de propiedad para la presentación.</span><span class="sxs-lookup"><span data-stu-id="497c5-291"> automatically HTML encodes property values for display.</span></span> <span data-ttu-id="497c5-292">El código siguiente es seguro de usar:</span><span class="sxs-lookup"><span data-stu-id="497c5-292">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="497c5-293">Fuera de Razor , <xref:System.Net.WebUtility.HtmlEncode*> el contenido del nombre de archivo siempre procedente de la solicitud de un usuario.</span><span class="sxs-lookup"><span data-stu-id="497c5-293">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="497c5-294">Muchas implementaciones deben incluir una comprobación de que el archivo existe; de lo contrario, el archivo se sobrescribe por un archivo con el mismo nombre.</span><span class="sxs-lookup"><span data-stu-id="497c5-294">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="497c5-295">Proporcione lógica adicional para satisfacer las especificaciones de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="497c5-295">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="497c5-296">Validación del tamaño</span><span class="sxs-lookup"><span data-stu-id="497c5-296">Size validation</span></span>

<span data-ttu-id="497c5-297">Limite el tamaño de los archivos cargados.</span><span class="sxs-lookup"><span data-stu-id="497c5-297">Limit the size of uploaded files.</span></span>

<span data-ttu-id="497c5-298">En la aplicación de ejemplo, el tamaño del archivo está limitado a 2 MB (se indica en bytes).</span><span class="sxs-lookup"><span data-stu-id="497c5-298">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="497c5-299">El límite se proporciona a través de [Configuración](xref:fundamentals/configuration/index) del archivo *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="497c5-299">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="497c5-300">`FileSizeLimit` se inserta en las clases `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="497c5-300">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

```csharp
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    private readonly long _fileSizeLimit;

    public BufferedSingleFileUploadPhysicalModel(IConfiguration config)
    {
        _fileSizeLimit = config.GetValue<long>("FileSizeLimit");
    }

    ...
}
```

<span data-ttu-id="497c5-301">Cuando el tamaño de un archivo supera el límite, se rechaza el archivo:</span><span class="sxs-lookup"><span data-stu-id="497c5-301">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="497c5-302">Coincidencia del valor de atributo de nombre con el nombre del parámetro del método POST</span><span class="sxs-lookup"><span data-stu-id="497c5-302">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="497c5-303">En los que no son Razor formularios y que envían datos de formulario `FormData` directamente, el nombre especificado en el elemento del formulario o `FormData` deben coincidir con el nombre del parámetro en la acción del controlador.</span><span class="sxs-lookup"><span data-stu-id="497c5-303">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="497c5-304">En el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="497c5-304">In the following example:</span></span>

* <span data-ttu-id="497c5-305">Cuando se usa un elemento `<input>`, el atributo `name` se establece en el valor `battlePlans`:</span><span class="sxs-lookup"><span data-stu-id="497c5-305">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="497c5-306">Cuando se usa `FormData` en JavaScript, el nombre se establece en el valor `battlePlans`:</span><span class="sxs-lookup"><span data-stu-id="497c5-306">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="497c5-307">Use un nombre coincidente para el parámetro del método de C# (`battlePlans`):</span><span class="sxs-lookup"><span data-stu-id="497c5-307">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="497c5-308">Para un Razor método de controlador de página de páginas denominado `Upload` :</span><span class="sxs-lookup"><span data-stu-id="497c5-308">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="497c5-309">Para un método de acción de controlador POST de MVC:</span><span class="sxs-lookup"><span data-stu-id="497c5-309">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="497c5-310">Configuración del servidor y de la aplicación</span><span class="sxs-lookup"><span data-stu-id="497c5-310">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="497c5-311">Límite de longitud del cuerpo de varias partes</span><span class="sxs-lookup"><span data-stu-id="497c5-311">Multipart body length limit</span></span>

<span data-ttu-id="497c5-312"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> establece el límite de la longitud de cada cuerpo de varias partes.</span><span class="sxs-lookup"><span data-stu-id="497c5-312"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="497c5-313">Las secciones del formulario que superan este límite inician una <xref:System.IO.InvalidDataException> cuando se analizan.</span><span class="sxs-lookup"><span data-stu-id="497c5-313">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="497c5-314">El valor predeterminado es 134 217 728 (128 MB).</span><span class="sxs-lookup"><span data-stu-id="497c5-314">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="497c5-315">Personalice el límite mediante el valor <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> en `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="497c5-315">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<FormOptions>(options =>
    {
        // Set the limit to 256 MB
        options.MultipartBodyLengthLimit = 268435456;
    });
}
```

<span data-ttu-id="497c5-316"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> se utiliza para establecer el <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> para una sola página o acción.</span><span class="sxs-lookup"><span data-stu-id="497c5-316"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="497c5-317">En una Razor aplicación de páginas, aplique el filtro con una [Convención](xref:razor-pages/razor-pages-conventions) en `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="497c5-317">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddRazorPages()
    .AddRazorPagesOptions(options =>
    {
        options.Conventions
            .AddPageApplicationModelConvention("/FileUploadPage",
                model.Filters.Add(
                    new RequestFormLimitsAttribute()
                    {
                        // Set the limit to 256 MB
                        MultipartBodyLengthLimit = 268435456
                    });
    });
```

<span data-ttu-id="497c5-318">En una Razor aplicación de páginas o en una aplicación MVC, aplique el filtro al método de acción o modelo de página:</span><span class="sxs-lookup"><span data-stu-id="497c5-318">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="497c5-319">Tamaño máximo del cuerpo de la solicitud de Kestrel</span><span class="sxs-lookup"><span data-stu-id="497c5-319">Kestrel maximum request body size</span></span>

<span data-ttu-id="497c5-320">En el caso de las aplicaciones hospedadas por Kestrel, el tamaño máximo predeterminado del cuerpo de solicitud es 30 000 000 bytes, que son aproximadamente 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="497c5-320">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="497c5-321">Personalice el límite con la opción [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) del servidor de Kestrel:</span><span class="sxs-lookup"><span data-stu-id="497c5-321">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel((context, options) =>
            {
                // Handle requests up to 50 MB
                options.Limits.MaxRequestBodySize = 52428800;
            })
            .UseStartup<Startup>();
        });
```

<span data-ttu-id="497c5-322"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> se usa para establecer el valor de [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) de una sola página o acción.</span><span class="sxs-lookup"><span data-stu-id="497c5-322"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="497c5-323">En una Razor aplicación de páginas, aplique el filtro con una [Convención](xref:razor-pages/razor-pages-conventions) en `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="497c5-323">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddRazorPages()
    .AddRazorPagesOptions(options =>
    {
        options.Conventions
            .AddPageApplicationModelConvention("/FileUploadPage",
                model =>
                {
                    // Handle requests up to 50 MB
                    model.Filters.Add(
                        new RequestSizeLimitAttribute(52428800));
                });
    });
```

<span data-ttu-id="497c5-324">En una Razor aplicación pages o MVC, aplique el filtro a la clase de controlador de páginas o al método de acción:</span><span class="sxs-lookup"><span data-stu-id="497c5-324">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

<span data-ttu-id="497c5-325">`RequestSizeLimitAttribute`También se puede aplicar mediante la [`@attribute`](xref:mvc/views/razor#attribute) Razor Directiva:</span><span class="sxs-lookup"><span data-stu-id="497c5-325">The `RequestSizeLimitAttribute` can also be applied using the [`@attribute`](xref:mvc/views/razor#attribute) Razor directive:</span></span>

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="497c5-326">Otros límites de Kestrel</span><span class="sxs-lookup"><span data-stu-id="497c5-326">Other Kestrel limits</span></span>

<span data-ttu-id="497c5-327">Otros límites de Kestrel pueden aplicarse a las aplicaciones hospedadas por Kestrel:</span><span class="sxs-lookup"><span data-stu-id="497c5-327">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="497c5-328">Las conexiones máximas de cliente</span><span class="sxs-lookup"><span data-stu-id="497c5-328">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="497c5-329">Tarifas de datos de solicitud y respuesta</span><span class="sxs-lookup"><span data-stu-id="497c5-329">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a><span data-ttu-id="497c5-330">Límite de longitud del contenido de IIS</span><span class="sxs-lookup"><span data-stu-id="497c5-330">IIS content length limit</span></span>

<span data-ttu-id="497c5-331">El límite predeterminado de solicitudes (`maxAllowedContentLength`) es 30 000 000 bytes, que son aproximadamente 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="497c5-331">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6MB.</span></span> <span data-ttu-id="497c5-332">Personalice el límite en el archivo *web.config*:</span><span class="sxs-lookup"><span data-stu-id="497c5-332">Customize the limit in the *web.config* file:</span></span>

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <!-- Handle requests up to 50 MB -->
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

<span data-ttu-id="497c5-333">Esto solo ocurre en IIS;</span><span class="sxs-lookup"><span data-stu-id="497c5-333">This setting only applies to IIS.</span></span> <span data-ttu-id="497c5-334">este comportamiento no sucede de forma predeterminada cuando los archivos se hospedan en Kestrel.</span><span class="sxs-lookup"><span data-stu-id="497c5-334">The behavior doesn't occur by default when hosting on Kestrel.</span></span> <span data-ttu-id="497c5-335">Para más información, consulte [Límites de solicitud \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span><span class="sxs-lookup"><span data-stu-id="497c5-335">For more information, see [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="497c5-336">Las limitaciones del módulo ASP.NET Core o la presencia del módulo de filtrado de solicitudes de IIS pueden limitar las cargas a 2 GB o 4 GB.</span><span class="sxs-lookup"><span data-stu-id="497c5-336">Limitations in the ASP.NET Core Module or presence of the IIS Request Filtering Module may limit uploads to either 2 or 4 GB.</span></span> <span data-ttu-id="497c5-337">Para obtener más información, consulte el artículo que indica que [no se pueden cargar archivos con un tamaño superior a 2 GB (aspnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span><span class="sxs-lookup"><span data-stu-id="497c5-337">For more information, see [Unable to upload file greater than 2GB in size (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="497c5-338">Solución de problemas</span><span class="sxs-lookup"><span data-stu-id="497c5-338">Troubleshoot</span></span>

<span data-ttu-id="497c5-339">Aquí incluimos algunos problemas comunes que pueden surgir al cargar archivos, así como sus posibles soluciones.</span><span class="sxs-lookup"><span data-stu-id="497c5-339">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="497c5-340">Error No encontrado al implementar en un servidor IIS</span><span class="sxs-lookup"><span data-stu-id="497c5-340">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="497c5-341">El error siguiente indica que el archivo cargado supera la longitud configurada del contenido del servidor:</span><span class="sxs-lookup"><span data-stu-id="497c5-341">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="497c5-342">Para más información sobre cómo aumentar el límite, consulte la sección [Límite de longitud del contenido de IIS](#iis-content-length-limit).</span><span class="sxs-lookup"><span data-stu-id="497c5-342">For more information on increasing the limit, see the [IIS content length limit](#iis-content-length-limit) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="497c5-343">Error de conexión</span><span class="sxs-lookup"><span data-stu-id="497c5-343">Connection failure</span></span>

<span data-ttu-id="497c5-344">Un error de conexión y una conexión del servidor de restablecimiento probablemente indica que el archivo cargado supera el tamaño máximo del cuerpo de la solicitud de Kestrel.</span><span class="sxs-lookup"><span data-stu-id="497c5-344">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="497c5-345">Para más información, consulte la sección [Tamaño máximo del cuerpo de la solicitud de Kestrel](#kestrel-maximum-request-body-size).</span><span class="sxs-lookup"><span data-stu-id="497c5-345">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="497c5-346">Los límites de conexión del cliente de Kestrel también pueden requerir ajustes.</span><span class="sxs-lookup"><span data-stu-id="497c5-346">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="497c5-347">Excepción de referencia nula con IFormFile</span><span class="sxs-lookup"><span data-stu-id="497c5-347">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="497c5-348">Si el controlador acepta archivos cargados con <xref:Microsoft.AspNetCore.Http.IFormFile>, pero el valor es `null`, confirme que el formulario HTML especifica un valor `enctype` de `multipart/form-data`.</span><span class="sxs-lookup"><span data-stu-id="497c5-348">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="497c5-349">Si este atributo no está establecido en el elemento `<form>`, no se llevará a cabo la carga del archivo y cualquier argumento <xref:Microsoft.AspNetCore.Http.IFormFile> enlazado será `null`.</span><span class="sxs-lookup"><span data-stu-id="497c5-349">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="497c5-350">Confirme también que la [nomenclatura de la carga en los datos de formulario coincide con la nomenclatura de la aplicación](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="497c5-350">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="497c5-351">El flujo era demasiado largo</span><span class="sxs-lookup"><span data-stu-id="497c5-351">Stream was too long</span></span>

<span data-ttu-id="497c5-352">Los ejemplos de este tema se basan en <xref:System.IO.MemoryStream> para almacenar el contenido del archivo cargado.</span><span class="sxs-lookup"><span data-stu-id="497c5-352">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="497c5-353">El límite de tamaño de `MemoryStream` es `int.MaxValue`.</span><span class="sxs-lookup"><span data-stu-id="497c5-353">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="497c5-354">Si el escenario de carga de archivos de la aplicación requiere almacenar contenido de archivo superior a 50 MB, use un enfoque alternativo que no dependa de un único valor de `MemoryStream` para almacenar el contenido de un archivo cargado.</span><span class="sxs-lookup"><span data-stu-id="497c5-354">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="497c5-355">ASP.NET Core admite la carga de uno o varios archivos mediante el enlace de modelos almacenado en búfer de archivos más pequeños y el streaming no almacenado en búfer de archivos de mayor tamaño.</span><span class="sxs-lookup"><span data-stu-id="497c5-355">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="497c5-356">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="497c5-356">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="497c5-357">Consideraciones sobre la seguridad</span><span class="sxs-lookup"><span data-stu-id="497c5-357">Security considerations</span></span>

<span data-ttu-id="497c5-358">Tenga precaución al proporcionar a los usuarios la capacidad de cargar archivos en un servidor.</span><span class="sxs-lookup"><span data-stu-id="497c5-358">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="497c5-359">Es posible que los atacantes intenten lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="497c5-359">Attackers may attempt to:</span></span>

* <span data-ttu-id="497c5-360">Ejecutar ataques por [denegación de servicio](/windows-hardware/drivers/ifs/denial-of-service).</span><span class="sxs-lookup"><span data-stu-id="497c5-360">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="497c5-361">Cargar virus o malware.</span><span class="sxs-lookup"><span data-stu-id="497c5-361">Upload viruses or malware.</span></span>
* <span data-ttu-id="497c5-362">Poner en riesgo redes y servidores de otras maneras.</span><span class="sxs-lookup"><span data-stu-id="497c5-362">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="497c5-363">Estos son algunos de los pasos de seguridad con los que se reduce la probabilidad de sufrir ataques:</span><span class="sxs-lookup"><span data-stu-id="497c5-363">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="497c5-364">Cargue los archivos a un área de carga de archivos dedicada, preferiblemente una unidad que no sea de sistema.</span><span class="sxs-lookup"><span data-stu-id="497c5-364">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="497c5-365">Una ubicación dedicada facilita la imposición de restricciones de seguridad en los archivos cargados.</span><span class="sxs-lookup"><span data-stu-id="497c5-365">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="497c5-366">Deshabilite la ejecución de los permisos en la ubicación de carga de archivos.&dagger;</span><span class="sxs-lookup"><span data-stu-id="497c5-366">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="497c5-367">Los archivos cargados **no** se deben persistir en el mismo árbol de directorio que la aplicación.&dagger;</span><span class="sxs-lookup"><span data-stu-id="497c5-367">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="497c5-368">Use un nombre de archivo seguro determinado por la aplicación.</span><span class="sxs-lookup"><span data-stu-id="497c5-368">Use a safe file name determined by the app.</span></span> <span data-ttu-id="497c5-369">No use un nombre de archivo proporcionado por el usuario o el nombre de archivo que no es de confianza del archivo cargado. &dagger; HTML codifica el nombre de archivo que no es de confianza al mostrarlo.</span><span class="sxs-lookup"><span data-stu-id="497c5-369">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="497c5-370">Por ejemplo, registrando el nombre de archivo o mostrándose en la interfaz de usuario (la Razor salida codifica automáticamente html).</span><span class="sxs-lookup"><span data-stu-id="497c5-370">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="497c5-371">Permita solo las extensiones de archivo aprobadas para la especificación de diseño de la aplicación.&dagger;</span><span class="sxs-lookup"><span data-stu-id="497c5-371">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="497c5-372">Compruebe que las comprobaciones del lado cliente se realizan en el servidor. &dagger; Las comprobaciones del lado cliente son fáciles de eludir.</span><span class="sxs-lookup"><span data-stu-id="497c5-372">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="497c5-373">Compruebe el tamaño de un archivo cargado.</span><span class="sxs-lookup"><span data-stu-id="497c5-373">Check the size of an uploaded file.</span></span> <span data-ttu-id="497c5-374">Establezca un límite de tamaño máximo para evitar cargas grandes.&dagger;</span><span class="sxs-lookup"><span data-stu-id="497c5-374">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="497c5-375">Cuando un archivo cargado con el mismo nombre no deba sobrescribir los archivos, vuelva a comprobar el nombre de archivo en la base de datos o en el almacenamiento físico antes de cargarlo.</span><span class="sxs-lookup"><span data-stu-id="497c5-375">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="497c5-376">**Ejecute un detector de virus o malware en el contenido cargado antes de que se almacene el archivo.**</span><span class="sxs-lookup"><span data-stu-id="497c5-376">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="497c5-377">&dagger;La aplicación de ejemplo muestra un enfoque que cumple los criterios.</span><span class="sxs-lookup"><span data-stu-id="497c5-377">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="497c5-378">La carga de código malintencionado en un sistema suele ser el primer paso para ejecutar código que puede:</span><span class="sxs-lookup"><span data-stu-id="497c5-378">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="497c5-379">Obtener el control completo de un sistema.</span><span class="sxs-lookup"><span data-stu-id="497c5-379">Completely gain control of a system.</span></span>
> * <span data-ttu-id="497c5-380">Sobrecargar un sistema de manera que el sistema se bloquea.</span><span class="sxs-lookup"><span data-stu-id="497c5-380">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="497c5-381">Poner en peligro los datos del usuario o del sistema.</span><span class="sxs-lookup"><span data-stu-id="497c5-381">Compromise user or system data.</span></span>
> * <span data-ttu-id="497c5-382">Aplicar grafitis a una interfaz de usuario pública.</span><span class="sxs-lookup"><span data-stu-id="497c5-382">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="497c5-383">Vea los siguientes recursos para más información sobre cómo reducir el área expuesta de ataques al aceptar archivos de los usuarios:</span><span class="sxs-lookup"><span data-stu-id="497c5-383">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * <span data-ttu-id="497c5-384">[Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload) (Carga de archivos sin restricciones)</span><span class="sxs-lookup"><span data-stu-id="497c5-384">[Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)</span></span>
> * [<span data-ttu-id="497c5-385">Azure Security: asegúrese de que los controles adecuados estén en vigor al aceptar archivos de usuarios</span><span class="sxs-lookup"><span data-stu-id="497c5-385">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="497c5-386">Para más información sobre cómo implementar medidas de seguridad, incluidos ejemplos de la aplicación de ejemplo, consulte la sección [Validación](#validation).</span><span class="sxs-lookup"><span data-stu-id="497c5-386">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="497c5-387">Escenarios de almacenamiento</span><span class="sxs-lookup"><span data-stu-id="497c5-387">Storage scenarios</span></span>

<span data-ttu-id="497c5-388">Las opciones de almacenamiento comunes para los archivos incluyen:</span><span class="sxs-lookup"><span data-stu-id="497c5-388">Common storage options for files include:</span></span>

* <span data-ttu-id="497c5-389">Base de datos</span><span class="sxs-lookup"><span data-stu-id="497c5-389">Database</span></span>

  * <span data-ttu-id="497c5-390">En el caso de cargas de archivos pequeñas, una base de datos suele ser más rápida que las opciones de almacenamiento físico (sistema de archivos o recurso compartido de red).</span><span class="sxs-lookup"><span data-stu-id="497c5-390">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="497c5-391">Una base de datos suele ser más conveniente que las opciones de almacenamiento físico, ya que la recuperación de un registro de base de datos para los datos de usuario puede proporcionar el contenido del archivo (por ejemplo, una imagen de avatar).</span><span class="sxs-lookup"><span data-stu-id="497c5-391">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="497c5-392">Una base de datos puede ser más económica que usar un servicio de almacenamiento de datos.</span><span class="sxs-lookup"><span data-stu-id="497c5-392">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="497c5-393">Almacenamiento físico (sistema de archivos o recurso compartido de red)</span><span class="sxs-lookup"><span data-stu-id="497c5-393">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="497c5-394">Para cargas de archivos de gran tamaño:</span><span class="sxs-lookup"><span data-stu-id="497c5-394">For large file uploads:</span></span>
    * <span data-ttu-id="497c5-395">Los límites de base de datos pueden restringir el tamaño de la carga.</span><span class="sxs-lookup"><span data-stu-id="497c5-395">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="497c5-396">El almacenamiento físico suele ser menos económico que el almacenamiento en una base de datos.</span><span class="sxs-lookup"><span data-stu-id="497c5-396">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="497c5-397">El almacenamiento físico puede ser más económico que usar un servicio de almacenamiento de datos.</span><span class="sxs-lookup"><span data-stu-id="497c5-397">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="497c5-398">El proceso de la aplicación debe tener permisos de lectura y escritura en la ubicación de almacenamiento.</span><span class="sxs-lookup"><span data-stu-id="497c5-398">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="497c5-399">**Nunca conceda el permiso de ejecución.**</span><span class="sxs-lookup"><span data-stu-id="497c5-399">**Never grant execute permission.**</span></span>

* <span data-ttu-id="497c5-400">Servicio de almacenamiento de datos (por ejemplo, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span><span class="sxs-lookup"><span data-stu-id="497c5-400">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="497c5-401">Los servicios suelen ofrecer una escalabilidad y resistencia mejoradas sobre las soluciones locales que normalmente están sujetas a únicos puntos de error.</span><span class="sxs-lookup"><span data-stu-id="497c5-401">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="497c5-402">Los servicios pueden tener un costo menor en escenarios de infraestructura de almacenamiento de gran tamaño.</span><span class="sxs-lookup"><span data-stu-id="497c5-402">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="497c5-403">Para obtener más información, vea [Inicio rápido: usar .net para crear un BLOB en el almacenamiento de objetos](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span><span class="sxs-lookup"><span data-stu-id="497c5-403">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span> <span data-ttu-id="497c5-404">En el tema se muestra <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, pero se puede usar <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> para guardar un <xref:System.IO.FileStream> en el almacenamiento de blobs cuando se trabaja con un <xref:System.IO.Stream>.</span><span class="sxs-lookup"><span data-stu-id="497c5-404">The topic demonstrates <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, but <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> can be used to save a <xref:System.IO.FileStream> to blob storage when working with a <xref:System.IO.Stream>.</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="497c5-405">Escenarios de carga de archivos</span><span class="sxs-lookup"><span data-stu-id="497c5-405">File upload scenarios</span></span>

<span data-ttu-id="497c5-406">Dos enfoques generales para cargar archivos son el almacenamiento en búfer y el streaming.</span><span class="sxs-lookup"><span data-stu-id="497c5-406">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="497c5-407">**de respuesta**</span><span class="sxs-lookup"><span data-stu-id="497c5-407">**Buffering**</span></span>

<span data-ttu-id="497c5-408">El archivo completo se lee en un <xref:Microsoft.AspNetCore.Http.IFormFile>, que es una representación de C# del archivo que se usa para procesar o guardar el archivo.</span><span class="sxs-lookup"><span data-stu-id="497c5-408">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="497c5-409">Los recursos (disco, memoria) que se usan en las cargas de archivos dependen de la cantidad y del tamaño de las cargas de archivos que se realizan simultáneamente.</span><span class="sxs-lookup"><span data-stu-id="497c5-409">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="497c5-410">Si una aplicación intenta almacenar demasiadas cargas en el búfer, el sitio se bloquea cuando se queda sin memoria o sin espacio en disco.</span><span class="sxs-lookup"><span data-stu-id="497c5-410">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="497c5-411">Si el tamaño o la frecuencia de las cargas de archivos está agotando los recursos de la aplicación, use el streaming.</span><span class="sxs-lookup"><span data-stu-id="497c5-411">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="497c5-412">Cualquier archivo almacenado en búfer único que supere los 64 KB se mueve de la memoria a un archivo temporal en el disco.</span><span class="sxs-lookup"><span data-stu-id="497c5-412">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="497c5-413">En las secciones siguientes de este tema se habla del almacenamiento en búfer de archivos pequeños:</span><span class="sxs-lookup"><span data-stu-id="497c5-413">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="497c5-414">Almacenamiento físico</span><span class="sxs-lookup"><span data-stu-id="497c5-414">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="497c5-415">Base de datos</span><span class="sxs-lookup"><span data-stu-id="497c5-415">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="497c5-416">**Streaming**</span><span class="sxs-lookup"><span data-stu-id="497c5-416">**Streaming**</span></span>

<span data-ttu-id="497c5-417">El archivo se recibe de una solicitud de varias partes y lo procesa o guarda directamente la aplicación.</span><span class="sxs-lookup"><span data-stu-id="497c5-417">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="497c5-418">El streaming no mejora considerablemente el rendimiento.</span><span class="sxs-lookup"><span data-stu-id="497c5-418">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="497c5-419">El streaming reduce las demandas de memoria o espacio en disco cuando se cargan archivos.</span><span class="sxs-lookup"><span data-stu-id="497c5-419">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="497c5-420">El streaming de archivos grandes se describe en la sección [Carga de archivos de gran tamaño con streaming](#upload-large-files-with-streaming).</span><span class="sxs-lookup"><span data-stu-id="497c5-420">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="497c5-421">Carga de archivos pequeños con enlace de modelos almacenado en búfer al almacenamiento físico</span><span class="sxs-lookup"><span data-stu-id="497c5-421">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="497c5-422">Para cargar archivos pequeños, se puede usar un formulario de varias partes o construir una solicitud POST con JavaScript.</span><span class="sxs-lookup"><span data-stu-id="497c5-422">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="497c5-423">En el ejemplo siguiente se muestra el uso de un Razor formulario de páginas para cargar un único archivo (*pages/BufferedSingleFileUploadPhysical. cshtml* en la aplicación de ejemplo):</span><span class="sxs-lookup"><span data-stu-id="497c5-423">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
            <span asp-validation-for="FileUpload.FormFile"></span>
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload" />
</form>
```

<span data-ttu-id="497c5-424">El ejemplo siguiente es análogo al ejemplo anterior, salvo en que:</span><span class="sxs-lookup"><span data-stu-id="497c5-424">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="497c5-425">([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) de JavaScript se usa para enviar los datos del formulario.</span><span class="sxs-lookup"><span data-stu-id="497c5-425">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="497c5-426">No hay ninguna validación.</span><span class="sxs-lookup"><span data-stu-id="497c5-426">There's no validation.</span></span>

```cshtml
<form action="BufferedSingleFileUploadPhysical/?handler=Upload" 
      enctype="multipart/form-data" onsubmit="AJAXSubmit(this);return false;" 
      method="post">
    <dl>
        <dt>
            <label for="FileUpload_FormFile">File</label>
        </dt>
        <dd>
            <input id="FileUpload_FormFile" type="file" 
                name="FileUpload.FormFile" />
        </dd>
    </dl>

    <input class="btn" type="submit" value="Upload" />

    <div style="margin-top:15px">
        <output name="result"></output>
    </div>
</form>

<script>
  async function AJAXSubmit (oFormElement) {
    var resultElement = oFormElement.elements.namedItem("result");
    const formData = new FormData(oFormElement);

    try {
    const response = await fetch(oFormElement.action, {
      method: 'POST',
      body: formData
    });

    if (response.ok) {
      window.location.href = '/';
    }

    resultElement.value = 'Result: ' + response.status + ' ' + 
      response.statusText;
    } catch (error) {
      console.error('Error:', error);
    }
  }
</script>
```

<span data-ttu-id="497c5-427">Para realizar la solicitud POST en JavaScript para los clientes que [no admiten Fetch API](https://caniuse.com/#feat=fetch), use uno de estos enfoques:</span><span class="sxs-lookup"><span data-stu-id="497c5-427">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="497c5-428">Use un Fetch Polyfill (por ejemplo, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="497c5-428">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="497c5-429">Mediante `XMLHttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="497c5-429">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="497c5-430">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="497c5-430">For example:</span></span>

  ```javascript
  <script>
    "use strict";

    function AJAXSubmit (oFormElement) {
      var oReq = new XMLHttpRequest();
      oReq.onload = function(e) { 
      oFormElement.elements.namedItem("result").value = 
        'Result: ' + this.status + ' ' + this.statusText;
      };
      oReq.open("post", oFormElement.action);
      oReq.send(new FormData(oFormElement));
    }
  </script>
  ```

<span data-ttu-id="497c5-431">Para admitir las cargas de archivos, los formularios HTML deben especificar un tipo de codificación (`enctype`) de `multipart/form-data`.</span><span class="sxs-lookup"><span data-stu-id="497c5-431">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="497c5-432">Para que un elemento de entrada `files` admita al carga de varios archivos, proporcione el atributo `multiple` en el elemento `<input>`:</span><span class="sxs-lookup"><span data-stu-id="497c5-432">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="497c5-433">Es posible acceder a archivos individuales cargados en el servidor a través del [enlace de modelos](xref:mvc/models/model-binding) mediante <xref:Microsoft.AspNetCore.Http.IFormFile>.</span><span class="sxs-lookup"><span data-stu-id="497c5-433">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="497c5-434">La aplicación de ejemplo muestra varias cargas de archivos almacenados en búfer para escenarios de almacenamiento físico y base de datos.</span><span class="sxs-lookup"><span data-stu-id="497c5-434">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename2"></a>

> [!WARNING]
> <span data-ttu-id="497c5-435">**No** utilice la propiedad `FileName` de <xref:Microsoft.AspNetCore.Http.IFormFile> para usos distintos a la presentación y el registro.</span><span class="sxs-lookup"><span data-stu-id="497c5-435">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="497c5-436">Para fines de presentación y registro, codifique el nombre de archivo en HTML.</span><span class="sxs-lookup"><span data-stu-id="497c5-436">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="497c5-437">Un atacante puede proporcionar un nombre de archivo malintencionado, incluidas rutas de acceso completas o relativas.</span><span class="sxs-lookup"><span data-stu-id="497c5-437">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="497c5-438">Las aplicaciones deben:</span><span class="sxs-lookup"><span data-stu-id="497c5-438">Applications should:</span></span>
>
> * <span data-ttu-id="497c5-439">Quitar la ruta de acceso del nombre de archivo proporcionado por el usuario.</span><span class="sxs-lookup"><span data-stu-id="497c5-439">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="497c5-440">Guardar el nombre de archivo codificado en HTML, sin la ruta de acceso para la interfaz de usuario o el registro.</span><span class="sxs-lookup"><span data-stu-id="497c5-440">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="497c5-441">Generar un nombre de archivo aleatorio nuevo para el almacenamiento.</span><span class="sxs-lookup"><span data-stu-id="497c5-441">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="497c5-442">En el código siguiente se quita la ruta de acceso del nombre del archivo:</span><span class="sxs-lookup"><span data-stu-id="497c5-442">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="497c5-443">Los ejemplos proporcionados hasta ahora no tienen en cuenta las consideraciones de seguridad.</span><span class="sxs-lookup"><span data-stu-id="497c5-443">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="497c5-444">Se proporciona información adicional en las secciones siguientes y en la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="497c5-444">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="497c5-445">Consideraciones sobre la seguridad</span><span class="sxs-lookup"><span data-stu-id="497c5-445">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="497c5-446">Validación</span><span class="sxs-lookup"><span data-stu-id="497c5-446">Validation</span></span>](#validation)

<span data-ttu-id="497c5-447">Al cargar archivos mediante el enlace de modelos y <xref:Microsoft.AspNetCore.Http.IFormFile>, el método de acción puede aceptar:</span><span class="sxs-lookup"><span data-stu-id="497c5-447">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="497c5-448">Un <xref:Microsoft.AspNetCore.Http.IFormFile> único.</span><span class="sxs-lookup"><span data-stu-id="497c5-448">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="497c5-449">Cualquiera de las colecciones siguientes que representan varios archivos:</span><span class="sxs-lookup"><span data-stu-id="497c5-449">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="497c5-450">[Lista](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="497c5-450">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="497c5-451">El enlace coincide con los archivos de formulario por nombre.</span><span class="sxs-lookup"><span data-stu-id="497c5-451">Binding matches form files by name.</span></span> <span data-ttu-id="497c5-452">Por ejemplo, el valor `name` HTML en `<input type="file" name="formFile">` debe coincidir con la propiedad/el parámetro enlazado de C# (`FormFile`).</span><span class="sxs-lookup"><span data-stu-id="497c5-452">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="497c5-453">Para más información, consulte la sección [Coincidencia del valor de atributo de nombre con el nombre del parámetro del método POST](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="497c5-453">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="497c5-454">En el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="497c5-454">The following example:</span></span>

* <span data-ttu-id="497c5-455">Recorre en bucle uno o más archivos cargados.</span><span class="sxs-lookup"><span data-stu-id="497c5-455">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="497c5-456">Usa [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) para devolver una ruta de acceso completa de un archivo, incluido el nombre de archivo.</span><span class="sxs-lookup"><span data-stu-id="497c5-456">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="497c5-457">Guarda los archivos en el sistema de archivos local con un nombre de archivo generado por la aplicación.</span><span class="sxs-lookup"><span data-stu-id="497c5-457">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="497c5-458">Devuelve el número total y el tamaño de los archivos cargados.</span><span class="sxs-lookup"><span data-stu-id="497c5-458">Returns the total number and size of files uploaded.</span></span>

```csharp
public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> files)
{
    long size = files.Sum(f => f.Length);

    foreach (var formFile in files)
    {
        if (formFile.Length > 0)
        {
            var filePath = Path.GetTempFileName();

            using (var stream = System.IO.File.Create(filePath))
            {
                await formFile.CopyToAsync(stream);
            }
        }
    }

    // Process uploaded files
    // Don't rely on or trust the FileName property without validation.

    return Ok(new { count = files.Count, size });
}
```

<span data-ttu-id="497c5-459">Use `Path.GetRandomFileName` para generar un nombre de archivo sin una ruta de acceso.</span><span class="sxs-lookup"><span data-stu-id="497c5-459">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="497c5-460">En el ejemplo siguiente, la ruta de acceso se obtiene de la configuración:</span><span class="sxs-lookup"><span data-stu-id="497c5-460">In the following example, the path is obtained from configuration:</span></span>

```csharp
foreach (var formFile in files)
{
    if (formFile.Length > 0)
    {
        var filePath = Path.Combine(_config["StoredFilesPath"], 
            Path.GetRandomFileName());

        using (var stream = System.IO.File.Create(filePath))
        {
            await formFile.CopyToAsync(stream);
        }
    }
}
```

<span data-ttu-id="497c5-461">La ruta de acceso pasada a <xref:System.IO.FileStream> *debe* incluir el nombre de archivo.</span><span class="sxs-lookup"><span data-stu-id="497c5-461">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="497c5-462">Si no se proporciona el nombre de archivo, se produce una <xref:System.UnauthorizedAccessException> en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="497c5-462">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="497c5-463">Los archivos que se cargan usando la técnica <xref:Microsoft.AspNetCore.Http.IFormFile> se almacenan en búfer en memoria o en disco en el servidor web antes de procesarse.</span><span class="sxs-lookup"><span data-stu-id="497c5-463">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="497c5-464">Dentro del método de acción, se puede tener acceso al contenido de <xref:Microsoft.AspNetCore.Http.IFormFile> como <xref:System.IO.Stream>.</span><span class="sxs-lookup"><span data-stu-id="497c5-464">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="497c5-465">Además del sistema de archivos local, los archivos se pueden guardar en un recurso compartido de red o en un servicio de almacenamiento de archivos, como [Azure Blob Storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span><span class="sxs-lookup"><span data-stu-id="497c5-465">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="497c5-466">Para ver otro ejemplo que recorre en bucle varios archivos para cargar y usa nombres de archivo seguros, consulte *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* en la aplicación de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="497c5-466">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="497c5-467">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) arroja una <xref:System.IO.IOException> si se crean más de 65 535 archivos sin eliminar los archivos temporales anteriores.</span><span class="sxs-lookup"><span data-stu-id="497c5-467">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="497c5-468">El límite de 65 535 archivos es un límite por servidor.</span><span class="sxs-lookup"><span data-stu-id="497c5-468">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="497c5-469">Para más información sobre este límite en el sistema operativo Windows, consulte las notas en los temas siguientes:</span><span class="sxs-lookup"><span data-stu-id="497c5-469">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="497c5-470">Función GetTempFileNameA</span><span class="sxs-lookup"><span data-stu-id="497c5-470">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="497c5-471">Carga de archivos pequeños con enlace de modelos almacenado en búfer a una base de datos</span><span class="sxs-lookup"><span data-stu-id="497c5-471">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="497c5-472">Para almacenar datos de archivo binario en una base de datos con [Entity Framework](/ef/core/index), defina una propiedad de matriz <xref:System.Byte> en la entidad:</span><span class="sxs-lookup"><span data-stu-id="497c5-472">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="497c5-473">Especifique una propiedad de modelo de página para la clase que incluya un <xref:Microsoft.AspNetCore.Http.IFormFile>:</span><span class="sxs-lookup"><span data-stu-id="497c5-473">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

```csharp
public class BufferedSingleFileUploadDbModel : PageModel
{
    ...

    [BindProperty]
    public BufferedSingleFileUploadDb FileUpload { get; set; }

    ...
}

public class BufferedSingleFileUploadDb
{
    [Required]
    [Display(Name="File")]
    public IFormFile FormFile { get; set; }
}
```

> [!NOTE]
> <span data-ttu-id="497c5-474"><xref:Microsoft.AspNetCore.Http.IFormFile> se puede usar directamente como un parámetro de método de acción o como una propiedad de modelo enlazado.</span><span class="sxs-lookup"><span data-stu-id="497c5-474"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="497c5-475">En el ejemplo anterior se utiliza una propiedad de modelo enlazado.</span><span class="sxs-lookup"><span data-stu-id="497c5-475">The prior example uses a bound model property.</span></span>

<span data-ttu-id="497c5-476">`FileUpload`Se utiliza en el Razor formulario de páginas:</span><span class="sxs-lookup"><span data-stu-id="497c5-476">The `FileUpload` is used in the Razor Pages form:</span></span>

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload">
</form>
```

<span data-ttu-id="497c5-477">Cuando el formulario se publique en el servidor, copie el <xref:Microsoft.AspNetCore.Http.IFormFile> en un flujo y guárdelo como matriz de bytes en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="497c5-477">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="497c5-478">En el ejemplo siguiente, `_dbContext` almacena el contexto de base de datos de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="497c5-478">In the following example, `_dbContext` stores the app's database context:</span></span>

```csharp
public async Task<IActionResult> OnPostUploadAsync()
{
    using (var memoryStream = new MemoryStream())
    {
        await FileUpload.FormFile.CopyToAsync(memoryStream);

        // Upload the file if less than 2 MB
        if (memoryStream.Length < 2097152)
        {
            var file = new AppFile()
            {
                Content = memoryStream.ToArray()
            };

            _dbContext.File.Add(file);

            await _dbContext.SaveChangesAsync();
        }
        else
        {
            ModelState.AddModelError("File", "The file is too large.");
        }
    }

    return Page();
}
```

<span data-ttu-id="497c5-479">El ejemplo anterior es similar a un escenario que se muestra en la aplicación de ejemplo:</span><span class="sxs-lookup"><span data-stu-id="497c5-479">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="497c5-480">*Pages/BufferedSingleFileUploadDb.cshtml*</span><span class="sxs-lookup"><span data-stu-id="497c5-480">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="497c5-481">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="497c5-481">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="497c5-482">Tenga cuidado al almacenar los datos binarios en bases de datos relacionales, ya que esto puede repercutir adversamente en el rendimiento.</span><span class="sxs-lookup"><span data-stu-id="497c5-482">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="497c5-483">No se base ni confíe en la propiedad `FileName` de <xref:Microsoft.AspNetCore.Http.IFormFile> sin validarla.</span><span class="sxs-lookup"><span data-stu-id="497c5-483">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="497c5-484">La propiedad `FileName` solo debe usarse para fines de presentación y solo después de la codificación HTML.</span><span class="sxs-lookup"><span data-stu-id="497c5-484">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="497c5-485">Los ejemplos proporcionados no tienen en cuenta las consideraciones de seguridad.</span><span class="sxs-lookup"><span data-stu-id="497c5-485">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="497c5-486">Se proporciona información adicional en las secciones siguientes y en la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="497c5-486">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="497c5-487">Consideraciones sobre la seguridad</span><span class="sxs-lookup"><span data-stu-id="497c5-487">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="497c5-488">Validación</span><span class="sxs-lookup"><span data-stu-id="497c5-488">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="497c5-489">Carga de archivos de gran tamaño con streaming</span><span class="sxs-lookup"><span data-stu-id="497c5-489">Upload large files with streaming</span></span>

<span data-ttu-id="497c5-490">En el ejemplo siguiente se muestra cómo usar JavaScript para transmitir un archivo a una acción de controlador.</span><span class="sxs-lookup"><span data-stu-id="497c5-490">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="497c5-491">El token de antifalsificación del archivo se genera por medio de un atributo de filtro personalizado y se pasa a los encabezados HTTP del cliente, en lugar de en el cuerpo de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="497c5-491">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="497c5-492">Dado que el método de acción procesa los datos cargados directamente, el enlace de modelos del formulario se deshabilita por otro filtro personalizado.</span><span class="sxs-lookup"><span data-stu-id="497c5-492">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="497c5-493">Dentro de la acción, el contenido del formulario se lee usando un `MultipartReader` (que lee cada `MultipartSection` individual), de forma que el archivo se procesa o el contenido se almacena, según corresponda.</span><span class="sxs-lookup"><span data-stu-id="497c5-493">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="497c5-494">Una vez leídas las secciones de varias partes, la acción realiza su propio enlace de modelos.</span><span class="sxs-lookup"><span data-stu-id="497c5-494">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="497c5-495">La respuesta de la página inicial carga el formulario y guarda un token de antifalsificación en una cookie (a través del atributo `GenerateAntiforgeryTokenCookieAttribute`).</span><span class="sxs-lookup"><span data-stu-id="497c5-495">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="497c5-496">El atributo usa la [compatibilidad de antifalsificación](xref:security/anti-request-forgery) integrada de ASP.NET Core para establecer una cookie con un token de solicitud:</span><span class="sxs-lookup"><span data-stu-id="497c5-496">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="497c5-497">El `DisableFormValueModelBindingAttribute` se usa para deshabilitar el enlace de modelos:</span><span class="sxs-lookup"><span data-stu-id="497c5-497">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="497c5-498">En la aplicación de ejemplo, `GenerateAntiforgeryTokenCookieAttribute` y `DisableFormValueModelBindingAttribute` se aplican como filtros a los modelos de aplicación de página de `/StreamedSingleFileUploadDb` y `/StreamedSingleFileUploadPhysical` en `Startup.ConfigureServices` mediante [ Razor las convenciones de páginas](xref:razor-pages/razor-pages-conventions):</span><span class="sxs-lookup"><span data-stu-id="497c5-498">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Startup.cs?name=snippet_AddMvc&highlight=8-11,17-20)]

<span data-ttu-id="497c5-499">Dado que el enlace de modelos no lee el formulario, los parámetros enlazados desde el formulario no se enlazan (la consulta, la ruta y el encabezado siguen funcionando).</span><span class="sxs-lookup"><span data-stu-id="497c5-499">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="497c5-500">El método de acción funciona directamente con la propiedad `Request`.</span><span class="sxs-lookup"><span data-stu-id="497c5-500">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="497c5-501">Se usa un elemento `MultipartReader` para leer cada sección.</span><span class="sxs-lookup"><span data-stu-id="497c5-501">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="497c5-502">Los datos de clave-valor se almacenan en un `KeyValueAccumulator`.</span><span class="sxs-lookup"><span data-stu-id="497c5-502">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="497c5-503">Una vez leídas las secciones de varias partes, el contenido del `KeyValueAccumulator` se usa para enlazar los datos del formulario a un tipo de modelo.</span><span class="sxs-lookup"><span data-stu-id="497c5-503">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="497c5-504">El método `StreamingController.UploadDatabase` completo para streaming a una base de datos con EF Core:</span><span class="sxs-lookup"><span data-stu-id="497c5-504">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="497c5-505">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span><span class="sxs-lookup"><span data-stu-id="497c5-505">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="497c5-506">El método `StreamingController.UploadPhysical` completo para streaming a una ubicación física:</span><span class="sxs-lookup"><span data-stu-id="497c5-506">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="497c5-507">En la aplicación de ejemplo, las comprobaciones de validación las controla `FileHelpers.ProcessStreamedFile`.</span><span class="sxs-lookup"><span data-stu-id="497c5-507">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="497c5-508">Validación</span><span class="sxs-lookup"><span data-stu-id="497c5-508">Validation</span></span>

<span data-ttu-id="497c5-509">La clase `FileHelpers` de la aplicación de ejemplo muestra varias comprobaciones de cargas de archivos de streaming y <xref:Microsoft.AspNetCore.Http.IFormFile> almacenado en búfer.</span><span class="sxs-lookup"><span data-stu-id="497c5-509">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="497c5-510">Para procesar cargas de archivos almacenadas en búfer de <xref:Microsoft.AspNetCore.Http.IFormFile> en la aplicación de ejemplo, consulte el método `ProcessFormFile` en el archivo *Utilities/FileHelpers.cs*.</span><span class="sxs-lookup"><span data-stu-id="497c5-510">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="497c5-511">Para procesar archivos de streaming, consulte el método `ProcessStreamedFile` en el mismo archivo.</span><span class="sxs-lookup"><span data-stu-id="497c5-511">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="497c5-512">Los métodos de procesamiento de validación mostrados en la aplicación de ejemplo no examinan el contenido de los archivos cargados.</span><span class="sxs-lookup"><span data-stu-id="497c5-512">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="497c5-513">En la mayoría de los escenarios de producción, se usa una API de analizador de virus/malware en el archivo antes de que el archivo esté disponible para los usuarios u otros sistemas.</span><span class="sxs-lookup"><span data-stu-id="497c5-513">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="497c5-514">Aunque el ejemplo de tema proporciona un ejemplo funcional de técnicas de validación, no implemente la clase `FileHelpers` en una aplicación de producción a menos que:</span><span class="sxs-lookup"><span data-stu-id="497c5-514">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="497c5-515">Comprenda totalmente la implementación.</span><span class="sxs-lookup"><span data-stu-id="497c5-515">Fully understand the implementation.</span></span>
> * <span data-ttu-id="497c5-516">Modifique la implementación según corresponda en función del entorno y las especificaciones de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="497c5-516">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="497c5-517">**No implemente nunca de manera indiscriminada el código de seguridad en una aplicación sin abordar estos requisitos.**</span><span class="sxs-lookup"><span data-stu-id="497c5-517">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="497c5-518">Validación de contenido</span><span class="sxs-lookup"><span data-stu-id="497c5-518">Content validation</span></span>

<span data-ttu-id="497c5-519">**Use una API de detección de virus/malware de terceros en el contenido cargado.**</span><span class="sxs-lookup"><span data-stu-id="497c5-519">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="497c5-520">El análisis de archivos exige recursos del servidor en escenarios de gran volumen.</span><span class="sxs-lookup"><span data-stu-id="497c5-520">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="497c5-521">Si disminuye el rendimiento de procesamiento de solicitudes debido al análisis de archivos, considere la posibilidad de descargar el trabajo de análisis a un [servicio en segundo plano](xref:fundamentals/host/hosted-services), posiblemente un servicio que se ejecute en otro servidor desde el servidor de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="497c5-521">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="497c5-522">Habitualmente, los archivos cargados se almacenan en un área en cuarentena hasta que el programa de detección de virus en segundo plano los revisa.</span><span class="sxs-lookup"><span data-stu-id="497c5-522">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="497c5-523">Cuando se pasa un archivo, se mueve a la ubicación de almacenamiento de archivos habitual.</span><span class="sxs-lookup"><span data-stu-id="497c5-523">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="497c5-524">Por lo general, estos pasos se realizan junto con un registro de base de datos que indica el estado de análisis de un archivo.</span><span class="sxs-lookup"><span data-stu-id="497c5-524">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="497c5-525">Mediante el uso de este enfoque, la aplicación y el servidor de aplicaciones permanecen centrados en responder a las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="497c5-525">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="497c5-526">Validación de la extensión del archivo</span><span class="sxs-lookup"><span data-stu-id="497c5-526">File extension validation</span></span>

<span data-ttu-id="497c5-527">La extensión del archivo cargado debe comprobarse con una lista de extensiones permitidas.</span><span class="sxs-lookup"><span data-stu-id="497c5-527">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="497c5-528">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="497c5-528">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="497c5-529">Validación de firma del archivo</span><span class="sxs-lookup"><span data-stu-id="497c5-529">File signature validation</span></span>

<span data-ttu-id="497c5-530">La firma de un archivo viene determinada por los primeros bytes al principio de un archivo.</span><span class="sxs-lookup"><span data-stu-id="497c5-530">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="497c5-531">Estos bytes se pueden usar para indicar si la extensión coincide con el contenido del archivo.</span><span class="sxs-lookup"><span data-stu-id="497c5-531">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="497c5-532">La aplicación de ejemplo comprueba las firmas de archivo de algunos tipos de archivo comunes.</span><span class="sxs-lookup"><span data-stu-id="497c5-532">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="497c5-533">En el ejemplo siguiente, la firma de archivo de una imagen JPEG se comprueba con respecto al archivo:</span><span class="sxs-lookup"><span data-stu-id="497c5-533">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

```csharp
private static readonly Dictionary<string, List<byte[]>> _fileSignature = 
    new Dictionary<string, List<byte[]>>
{
    { ".jpeg", new List<byte[]>
        {
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE2 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE3 },
        }
    },
};

using (var reader = new BinaryReader(uploadedFileData))
{
    var signatures = _fileSignature[ext];
    var headerBytes = reader.ReadBytes(signatures.Max(m => m.Length));
    
    return signatures.Any(signature => 
        headerBytes.Take(signature.Length).SequenceEqual(signature));
}
```

<span data-ttu-id="497c5-534">Para obtener firmas de archivo adicionales, consulte la [base de datos de firmas de archivo](https://www.filesignatures.net/) y las especificaciones de archivo oficiales.</span><span class="sxs-lookup"><span data-stu-id="497c5-534">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="497c5-535">Seguridad de nombre de archivo</span><span class="sxs-lookup"><span data-stu-id="497c5-535">File name security</span></span>

<span data-ttu-id="497c5-536">Nunca use un nombre de archivo proporcionado por el cliente para guardar un archivo en el almacenamiento físico.</span><span class="sxs-lookup"><span data-stu-id="497c5-536">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="497c5-537">Cree un nombre de archivo seguro para el archivo con [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) o [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) para crear una ruta de acceso completa (incluido el nombre de archivo) para el almacenamiento temporal.</span><span class="sxs-lookup"><span data-stu-id="497c5-537">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

Razor<span data-ttu-id="497c5-538">HTML codifica automáticamente los valores de propiedad para la presentación.</span><span class="sxs-lookup"><span data-stu-id="497c5-538"> automatically HTML encodes property values for display.</span></span> <span data-ttu-id="497c5-539">El código siguiente es seguro de usar:</span><span class="sxs-lookup"><span data-stu-id="497c5-539">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="497c5-540">Fuera de Razor , <xref:System.Net.WebUtility.HtmlEncode*> el contenido del nombre de archivo siempre procedente de la solicitud de un usuario.</span><span class="sxs-lookup"><span data-stu-id="497c5-540">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="497c5-541">Muchas implementaciones deben incluir una comprobación de que el archivo existe; de lo contrario, el archivo se sobrescribe por un archivo con el mismo nombre.</span><span class="sxs-lookup"><span data-stu-id="497c5-541">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="497c5-542">Proporcione lógica adicional para satisfacer las especificaciones de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="497c5-542">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="497c5-543">Validación del tamaño</span><span class="sxs-lookup"><span data-stu-id="497c5-543">Size validation</span></span>

<span data-ttu-id="497c5-544">Limite el tamaño de los archivos cargados.</span><span class="sxs-lookup"><span data-stu-id="497c5-544">Limit the size of uploaded files.</span></span>

<span data-ttu-id="497c5-545">En la aplicación de ejemplo, el tamaño del archivo está limitado a 2 MB (se indica en bytes).</span><span class="sxs-lookup"><span data-stu-id="497c5-545">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="497c5-546">El límite se proporciona a través de [Configuración](xref:fundamentals/configuration/index) del archivo *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="497c5-546">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="497c5-547">`FileSizeLimit` se inserta en las clases `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="497c5-547">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

```csharp
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    private readonly long _fileSizeLimit;

    public BufferedSingleFileUploadPhysicalModel(IConfiguration config)
    {
        _fileSizeLimit = config.GetValue<long>("FileSizeLimit");
    }

    ...
}
```

<span data-ttu-id="497c5-548">Cuando el tamaño de un archivo supera el límite, se rechaza el archivo:</span><span class="sxs-lookup"><span data-stu-id="497c5-548">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="497c5-549">Coincidencia del valor de atributo de nombre con el nombre del parámetro del método POST</span><span class="sxs-lookup"><span data-stu-id="497c5-549">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="497c5-550">En los que no son Razor formularios y que envían datos de formulario `FormData` directamente, el nombre especificado en el elemento del formulario o `FormData` deben coincidir con el nombre del parámetro en la acción del controlador.</span><span class="sxs-lookup"><span data-stu-id="497c5-550">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="497c5-551">En el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="497c5-551">In the following example:</span></span>

* <span data-ttu-id="497c5-552">Cuando se usa un elemento `<input>`, el atributo `name` se establece en el valor `battlePlans`:</span><span class="sxs-lookup"><span data-stu-id="497c5-552">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="497c5-553">Cuando se usa `FormData` en JavaScript, el nombre se establece en el valor `battlePlans`:</span><span class="sxs-lookup"><span data-stu-id="497c5-553">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="497c5-554">Use un nombre coincidente para el parámetro del método de C# (`battlePlans`):</span><span class="sxs-lookup"><span data-stu-id="497c5-554">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="497c5-555">Para un Razor método de controlador de página de páginas denominado `Upload` :</span><span class="sxs-lookup"><span data-stu-id="497c5-555">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="497c5-556">Para un método de acción de controlador POST de MVC:</span><span class="sxs-lookup"><span data-stu-id="497c5-556">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="497c5-557">Configuración del servidor y de la aplicación</span><span class="sxs-lookup"><span data-stu-id="497c5-557">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="497c5-558">Límite de longitud del cuerpo de varias partes</span><span class="sxs-lookup"><span data-stu-id="497c5-558">Multipart body length limit</span></span>

<span data-ttu-id="497c5-559"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> establece el límite de la longitud de cada cuerpo de varias partes.</span><span class="sxs-lookup"><span data-stu-id="497c5-559"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="497c5-560">Las secciones del formulario que superan este límite inician una <xref:System.IO.InvalidDataException> cuando se analizan.</span><span class="sxs-lookup"><span data-stu-id="497c5-560">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="497c5-561">El valor predeterminado es 134 217 728 (128 MB).</span><span class="sxs-lookup"><span data-stu-id="497c5-561">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="497c5-562">Personalice el límite mediante el valor <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> en `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="497c5-562">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<FormOptions>(options =>
    {
        // Set the limit to 256 MB
        options.MultipartBodyLengthLimit = 268435456;
    });
}
```

<span data-ttu-id="497c5-563"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> se utiliza para establecer el <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> para una sola página o acción.</span><span class="sxs-lookup"><span data-stu-id="497c5-563"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="497c5-564">En una Razor aplicación de páginas, aplique el filtro con una [Convención](xref:razor-pages/razor-pages-conventions) en `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="497c5-564">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.Conventions
            .AddPageApplicationModelConvention("/FileUploadPage",
                model.Filters.Add(
                    new RequestFormLimitsAttribute()
                    {
                        // Set the limit to 256 MB
                        MultipartBodyLengthLimit = 268435456
                    });
    })
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

<span data-ttu-id="497c5-565">En una Razor aplicación de páginas o en una aplicación MVC, aplique el filtro al método de acción o modelo de página:</span><span class="sxs-lookup"><span data-stu-id="497c5-565">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="497c5-566">Tamaño máximo del cuerpo de la solicitud de Kestrel</span><span class="sxs-lookup"><span data-stu-id="497c5-566">Kestrel maximum request body size</span></span>

<span data-ttu-id="497c5-567">En el caso de las aplicaciones hospedadas por Kestrel, el tamaño máximo predeterminado del cuerpo de solicitud es 30 000 000 bytes, que son aproximadamente 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="497c5-567">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="497c5-568">Personalice el límite con la opción [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) del servidor de Kestrel:</span><span class="sxs-lookup"><span data-stu-id="497c5-568">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            // Handle requests up to 50 MB
            options.Limits.MaxRequestBodySize = 52428800;
        });
```

<span data-ttu-id="497c5-569"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> se usa para establecer el valor de [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) de una sola página o acción.</span><span class="sxs-lookup"><span data-stu-id="497c5-569"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="497c5-570">En una Razor aplicación de páginas, aplique el filtro con una [Convención](xref:razor-pages/razor-pages-conventions) en `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="497c5-570">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.Conventions
            .AddPageApplicationModelConvention("/FileUploadPage",
                model =>
                {
                    // Handle requests up to 50 MB
                    model.Filters.Add(
                        new RequestSizeLimitAttribute(52428800));
                });
    })
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

<span data-ttu-id="497c5-571">En una Razor aplicación pages o MVC, aplique el filtro a la clase de controlador de páginas o al método de acción:</span><span class="sxs-lookup"><span data-stu-id="497c5-571">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="497c5-572">Otros límites de Kestrel</span><span class="sxs-lookup"><span data-stu-id="497c5-572">Other Kestrel limits</span></span>

<span data-ttu-id="497c5-573">Otros límites de Kestrel pueden aplicarse a las aplicaciones hospedadas por Kestrel:</span><span class="sxs-lookup"><span data-stu-id="497c5-573">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="497c5-574">Las conexiones máximas de cliente</span><span class="sxs-lookup"><span data-stu-id="497c5-574">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="497c5-575">Tarifas de datos de solicitud y respuesta</span><span class="sxs-lookup"><span data-stu-id="497c5-575">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a><span data-ttu-id="497c5-576">Límite de longitud del contenido de IIS</span><span class="sxs-lookup"><span data-stu-id="497c5-576">IIS content length limit</span></span>

<span data-ttu-id="497c5-577">El límite predeterminado de solicitudes (`maxAllowedContentLength`) es 30 000 000 bytes, que son aproximadamente 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="497c5-577">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6MB.</span></span> <span data-ttu-id="497c5-578">Personalice el límite en el archivo *web.config*:</span><span class="sxs-lookup"><span data-stu-id="497c5-578">Customize the limit in the *web.config* file:</span></span>

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <!-- Handle requests up to 50 MB -->
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

<span data-ttu-id="497c5-579">Esto solo ocurre en IIS;</span><span class="sxs-lookup"><span data-stu-id="497c5-579">This setting only applies to IIS.</span></span> <span data-ttu-id="497c5-580">este comportamiento no sucede de forma predeterminada cuando los archivos se hospedan en Kestrel.</span><span class="sxs-lookup"><span data-stu-id="497c5-580">The behavior doesn't occur by default when hosting on Kestrel.</span></span> <span data-ttu-id="497c5-581">Para más información, consulte [Límites de solicitud \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span><span class="sxs-lookup"><span data-stu-id="497c5-581">For more information, see [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="497c5-582">Las limitaciones del módulo ASP.NET Core o la presencia del módulo de filtrado de solicitudes de IIS pueden limitar las cargas a 2 GB o 4 GB.</span><span class="sxs-lookup"><span data-stu-id="497c5-582">Limitations in the ASP.NET Core Module or presence of the IIS Request Filtering Module may limit uploads to either 2 or 4 GB.</span></span> <span data-ttu-id="497c5-583">Para obtener más información, consulte el artículo que indica que [no se pueden cargar archivos con un tamaño superior a 2 GB (aspnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span><span class="sxs-lookup"><span data-stu-id="497c5-583">For more information, see [Unable to upload file greater than 2GB in size (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="497c5-584">Solución de problemas</span><span class="sxs-lookup"><span data-stu-id="497c5-584">Troubleshoot</span></span>

<span data-ttu-id="497c5-585">Aquí incluimos algunos problemas comunes que pueden surgir al cargar archivos, así como sus posibles soluciones.</span><span class="sxs-lookup"><span data-stu-id="497c5-585">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="497c5-586">Error No encontrado al implementar en un servidor IIS</span><span class="sxs-lookup"><span data-stu-id="497c5-586">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="497c5-587">El error siguiente indica que el archivo cargado supera la longitud configurada del contenido del servidor:</span><span class="sxs-lookup"><span data-stu-id="497c5-587">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="497c5-588">Para más información sobre cómo aumentar el límite, consulte la sección [Límite de longitud del contenido de IIS](#iis-content-length-limit).</span><span class="sxs-lookup"><span data-stu-id="497c5-588">For more information on increasing the limit, see the [IIS content length limit](#iis-content-length-limit) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="497c5-589">Error de conexión</span><span class="sxs-lookup"><span data-stu-id="497c5-589">Connection failure</span></span>

<span data-ttu-id="497c5-590">Un error de conexión y una conexión del servidor de restablecimiento probablemente indica que el archivo cargado supera el tamaño máximo del cuerpo de la solicitud de Kestrel.</span><span class="sxs-lookup"><span data-stu-id="497c5-590">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="497c5-591">Para más información, consulte la sección [Tamaño máximo del cuerpo de la solicitud de Kestrel](#kestrel-maximum-request-body-size).</span><span class="sxs-lookup"><span data-stu-id="497c5-591">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="497c5-592">Los límites de conexión del cliente de Kestrel también pueden requerir ajustes.</span><span class="sxs-lookup"><span data-stu-id="497c5-592">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="497c5-593">Excepción de referencia nula con IFormFile</span><span class="sxs-lookup"><span data-stu-id="497c5-593">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="497c5-594">Si el controlador acepta archivos cargados con <xref:Microsoft.AspNetCore.Http.IFormFile>, pero el valor es `null`, confirme que el formulario HTML especifica un valor `enctype` de `multipart/form-data`.</span><span class="sxs-lookup"><span data-stu-id="497c5-594">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="497c5-595">Si este atributo no está establecido en el elemento `<form>`, no se llevará a cabo la carga del archivo y cualquier argumento <xref:Microsoft.AspNetCore.Http.IFormFile> enlazado será `null`.</span><span class="sxs-lookup"><span data-stu-id="497c5-595">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="497c5-596">Confirme también que la [nomenclatura de la carga en los datos de formulario coincide con la nomenclatura de la aplicación](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="497c5-596">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="497c5-597">El flujo era demasiado largo</span><span class="sxs-lookup"><span data-stu-id="497c5-597">Stream was too long</span></span>

<span data-ttu-id="497c5-598">Los ejemplos de este tema se basan en <xref:System.IO.MemoryStream> para almacenar el contenido del archivo cargado.</span><span class="sxs-lookup"><span data-stu-id="497c5-598">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="497c5-599">El límite de tamaño de `MemoryStream` es `int.MaxValue`.</span><span class="sxs-lookup"><span data-stu-id="497c5-599">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="497c5-600">Si el escenario de carga de archivos de la aplicación requiere almacenar contenido de archivo superior a 50 MB, use un enfoque alternativo que no dependa de un único valor de `MemoryStream` para almacenar el contenido de un archivo cargado.</span><span class="sxs-lookup"><span data-stu-id="497c5-600">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end


## <a name="additional-resources"></a><span data-ttu-id="497c5-601">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="497c5-601">Additional resources</span></span>

* [<span data-ttu-id="497c5-602">Agotamiento de la solicitud de conexión HTTP</span><span class="sxs-lookup"><span data-stu-id="497c5-602">HTTP connection request draining</span></span>](xref:fundamentals/servers/kestrel#http11-request-draining)
* <span data-ttu-id="497c5-603">[Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload) (Carga de archivos sin restricciones)</span><span class="sxs-lookup"><span data-stu-id="497c5-603">[Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)</span></span>
* [<span data-ttu-id="497c5-604">Seguridad de Azure: marco de seguridad: validación de entrada | Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="497c5-604">Azure Security: Security Frame: Input Validation | Mitigations</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation)
* [<span data-ttu-id="497c5-605">Patrones de diseño en la nube de Azure: patrón de clave valet</span><span class="sxs-lookup"><span data-stu-id="497c5-605">Azure Cloud Design Patterns: Valet Key pattern</span></span>](/azure/architecture/patterns/valet-key)
