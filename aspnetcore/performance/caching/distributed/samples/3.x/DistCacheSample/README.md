# <a name="aspnet-core-distributed-cache-sample"></a>ASP.NET Core ejemplo de caché distribuida

En este ejemplo se muestra el uso de una caché distribuida. En este ejemplo se muestra el escenario descrito en el tema [trabajar con una caché distribuida en ASP.net Core](https://docs.microsoft.com/aspnet/core/performance/caching/distributed) .

En el entorno de producción, la aplicación de ejemplo está configurada para usar una memoria caché de SQL Server distribuida. Para volver a configurar la aplicación para usar una caché en Redis distribuida, cambie la Directiva de preprocesador en la parte superior del archivo *Startup.CS* para usar REDIS ( `#define Redis // SQLServer` ). Para obtener más información, vea [directivas de preprocesador en código de ejemplo](https://docs.microsoft.com/aspnet/core/introduction-to-aspnet-core#preprocessor-directives-in-sample-code).
