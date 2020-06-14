> [!WARNING]
> Un parámetro **catch-all** puede relacionar rutas de forma incorrecta debido a un [error](https://github.com/dotnet/aspnetcore/issues/18677) en el enrutamiento. Las aplicaciones afectadas por este error tienen las características siguientes:
>
> * Una ruta catch-all (por ejemplo, `{**slug}"`)
> * La ruta catch-all causa un error al relacionar solicitudes que sí que debería relacionar.
> * Al quitar otras rutas, la ruta catch-all empieza a funcionar.
>
> Para ver casos de ejemplo relacionados con este error, consulte los errores [18677](https://github.com/dotnet/aspnetcore/issues/18677) y [16579](https://github.com/dotnet/aspnetcore/issues/16579) en GitHub.
>
> Una corrección opcional para este error se incluye en el [SDK de 3.1.301 de .net Core y versiones posteriores](https://dotnet.microsoft.com/download/dotnet-core/3.1). El código siguiente establece un conmutador interno que corrige este error:
>
>```
>public static void Main(string[] args)
>{
>    AppContext.SetSwitch("Microsoft.AspNetCore.Routing.UseCorrectCatchAllBehavior", 
>                          true);
>    CreateHostBuilder(args).Build().Run();
>}
>// Remaining code removed for brevity.
>```