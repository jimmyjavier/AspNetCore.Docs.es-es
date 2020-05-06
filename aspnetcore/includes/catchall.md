> [!WARNING]
> Un parámetro **catch-all** puede relacionar rutas de forma incorrecta debido a un [error](https://github.com/dotnet/aspnetcore/issues/18677) en el enrutamiento. Las aplicaciones afectadas por este error tienen las características siguientes:
>
> * Una ruta catch-all (por ejemplo, `{**slug}"`)
> * La ruta catch-all causa un error al relacionar solicitudes que sí que debería relacionar.
> * Al quitar otras rutas, la ruta catch-all empieza a funcionar.
>
> Para ver casos de ejemplo relacionados con este error, consulte los errores [18677](https://github.com/dotnet/aspnetcore/issues/18677) y [16579](https://github.com/dotnet/aspnetcore/issues/16579) en GitHub.
>
> Se ha previsto una corrección opcional de este error. Cuando se publique dicha corrección, este documento se actualizará. Asimismo, una vez publicada la corrección, el código siguiente establecerá un conmutador interno que corregirá el error:
>
>```
>public static void Main(string[] args)
>{
>    AppContext.SetSwitch("Microsoft.AspNetCore.Routing.UseCorrectCatchAllBehavior", true);
>    CreateHostBuilder(args).Build().Run();
>}
>// Remaining code removed for brevity.
>```