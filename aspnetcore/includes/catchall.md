> [!WARNING]
> <span data-ttu-id="99579-101">Un parámetro **catch-all** puede relacionar rutas de forma incorrecta debido a un [error](https://github.com/dotnet/aspnetcore/issues/18677) en el enrutamiento.</span><span class="sxs-lookup"><span data-stu-id="99579-101">A **catch-all** parameter may match routes incorrectly due to a [bug](https://github.com/dotnet/aspnetcore/issues/18677) in routing.</span></span> <span data-ttu-id="99579-102">Las aplicaciones afectadas por este error tienen las características siguientes:</span><span class="sxs-lookup"><span data-stu-id="99579-102">Apps impacted by this bug have the following characteristics:</span></span>
>
> * <span data-ttu-id="99579-103">Una ruta catch-all (por ejemplo, `{**slug}"`)</span><span class="sxs-lookup"><span data-stu-id="99579-103">A catch-all route, for example, `{**slug}"`</span></span>
> * <span data-ttu-id="99579-104">La ruta catch-all causa un error al relacionar solicitudes que sí que debería relacionar.</span><span class="sxs-lookup"><span data-stu-id="99579-104">The catch-all route fails to match requests it should match.</span></span>
> * <span data-ttu-id="99579-105">Al quitar otras rutas, la ruta catch-all empieza a funcionar.</span><span class="sxs-lookup"><span data-stu-id="99579-105">Removing other routes makes catch-all route start working.</span></span>
>
> <span data-ttu-id="99579-106">Para ver casos de ejemplo relacionados con este error, consulte los errores [18677](https://github.com/dotnet/aspnetcore/issues/18677) y [16579](https://github.com/dotnet/aspnetcore/issues/16579) en GitHub.</span><span class="sxs-lookup"><span data-stu-id="99579-106">See GitHub bugs [18677](https://github.com/dotnet/aspnetcore/issues/18677) and [16579](https://github.com/dotnet/aspnetcore/issues/16579) for example cases that hit this bug.</span></span>
>
> <span data-ttu-id="99579-107">Se ha previsto una corrección opcional de este error.</span><span class="sxs-lookup"><span data-stu-id="99579-107">An opt-in fix for this bug is planned.</span></span> <span data-ttu-id="99579-108">Cuando se publique dicha corrección, este documento se actualizará.</span><span class="sxs-lookup"><span data-stu-id="99579-108">This doc will be updated when the patch is released.</span></span> <span data-ttu-id="99579-109">Asimismo, una vez publicada la corrección, el código siguiente establecerá un conmutador interno que corregirá el error:</span><span class="sxs-lookup"><span data-stu-id="99579-109">When the patch is released, the following code will set an internal switch that fixes this bug:</span></span>
>
>```
>public static void Main(string[] args)
>{
>    AppContext.SetSwitch("Microsoft.AspNetCore.Routing.UseCorrectCatchAllBehavior", true);
>    CreateHostBuilder(args).Build().Run();
>}
>// Remaining code removed for brevity.
>```