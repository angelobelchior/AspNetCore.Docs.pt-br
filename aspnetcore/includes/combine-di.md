<a name="csc"></a>

## <a name="combining-service-collection"></a>Combinando coleção de serviços

Considere o seguinte `ConfigureServices` que contém várias coleções de serviços:

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup2.cs?name=snippet)]

Grupos relacionados de registros podem ser movidos para um método de extensão para registrar serviços. Por exemplo, os serviços de configuração são adicionados à seguinte classe:

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Options/MyConfgServiceCollectionExtensions.cs)]

Os serviços restantes são registrados em uma classe semelhante. O seguinte `ConfigureServices` usa os novos métodos de extensão para registrar os serviços:

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup4.cs?name=snippet)]

***Observação:*** Cada `services.Add{SERVICE_NAME}` método de extensão adiciona e potencialmente configura os serviços do. Por exemplo, <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A> adiciona os serviços que os controladores MVC com exibições exigem. <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A>Adiciona os serviços Razor Pages exigir. Recomendamos que os aplicativos sigam essa Convenção de nomenclatura. Coloque os métodos de extensão no namespace [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) para encapsular grupos de registros de serviço.