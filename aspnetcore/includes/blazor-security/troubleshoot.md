## <a name="troubleshoot"></a>Solución de problemas

### <a name="cookies-and-site-data"></a>Cookies y datos de sitio

Las cookies y los datos del sitio pueden persistir entre actualizaciones de la aplicación e interferir con las pruebas y la solución de problemas. Desactive lo siguiente al realizar cambios en el código de la aplicación, cambios en la cuenta de usuario con el proveedor o cambios de configuración de la aplicación de proveedor:

* Cookies de inicio de sesión de usuario
* Cookies de aplicación
* Datos de sitio almacenados y almacenados en caché

Un enfoque para evitar que las cookies persistentes y los datos del sitio interfieran con las pruebas y la solución de problemas consiste en:

* Configurar un explorador
  * Use un explorador para las pruebas que puede configurar para eliminar todos los datos de cookies y de sitio cada vez que se cierra el explorador.
  * Asegúrese de que el explorador se cierra manualmente o mediante el IDE entre cualquier cambio en la aplicación, el usuario de prueba o la configuración del proveedor.
* Use un comando personalizado para abrir un explorador en el modo incógnito o Private en Visual Studio:
  * Abra el cuadro de diálogo **examinar con** del botón **Ejecutar** de Visual Studio.
  * Seleccione el botón **Agregar**.
  * Proporcione la ruta de acceso al explorador en el campo **programa** . Las siguientes rutas de acceso del archivo ejecutable son ubicaciones de instalación típicas para Windows 10. Si el explorador está instalado en una ubicación diferente o no usa Windows 10, proporcione la ruta de acceso al archivo ejecutable del explorador.
    * Microsoft Edge:`C:\Program Files (x86)\Microsoft\Edge\Application\msedge.exe`
    * Google Chrome:`C:\Program Files (x86)\Google\Chrome\Application\chrome.exe`
    * Mozilla Firefox:`C:\Program Files\Mozilla Firefox\firefox.exe`
  * En el campo **arguments (argumentos** ), proporcione la opción de línea de comandos que utiliza el explorador para abrir en el modo incógnito o Private. Algunos exploradores requieren la dirección URL de la aplicación.
    * Microsoft Edge:`-inprivate`
    * Google Chrome:`--incognito --new-window https://localhost:5001`
    * Mozilla Firefox:`-private -url https://localhost:5001`
  * Proporcione un nombre en el campo **nombre descriptivo** . Por ejemplo, `Firefox Auth Testing`.
  * Seleccione el botón **Aceptar**.
  * Para evitar tener que seleccionar el perfil de explorador para cada iteración de pruebas con una aplicación, establezca el perfil como predeterminado con el botón **establecer como predeterminado** .
  * Asegúrese de que el explorador esté cerrado por el IDE entre cualquier cambio realizado en la aplicación, el usuario de prueba o la configuración del proveedor.

### <a name="run-the-server-app"></a>Ejecutar la aplicación de servidor

Al probar y solucionar problemas de una aplicación increíblemente hospedada, asegúrese de que está ejecutando la aplicación desde el proyecto de **servidor** . Por ejemplo, en Visual Studio, confirme que el proyecto de servidor se resalta en **Explorador de soluciones** antes de iniciar la aplicación con cualquiera de los métodos siguientes:

* Haga clic en el botón **Ejecutar**.
* Use **depurar**  >  **iniciar depuración** en el menú.
* Presione <kbd>F5</kbd>.

### <a name="inspect-the-content-of-a-json-web-token-jwt"></a>Inspeccionar el contenido de un JSON Web Token (JWT)

Para descodificar un JSON Web Token (JWT), use la herramienta [JWT.ms](https://jwt.ms/) de Microsoft. Los valores de la interfaz de usuario nunca salen del explorador.
