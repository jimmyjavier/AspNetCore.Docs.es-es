> [!WARNING]
> Actualmente, [gRPC de ASP.NET Core](xref:grpc/index) no es compatible con Azure App Service ni IIS. La implementación HTTP/2 de Http.Sys no admite los encabezados finales de la respuesta HTTP en los que se basa gRPC. Para más información, consulte [este problema de GitHub](https://github.com/dotnet/AspNetCore/issues/9020).
