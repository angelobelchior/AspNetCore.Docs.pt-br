> [!WARNING]
> No momento, não há suporte para o [ASP.NET Core gRPC](xref:grpc/index) no serviço Azure app ou no IIS. A implementação de HTTP/2 de Http.Sys não dá suporte a cabeçalhos de barra de resposta HTTP com os quais o gRPC se baseia. Para obter mais informações, consulte [este problema do GitHub](https://github.com/dotnet/AspNetCore/issues/9020).
