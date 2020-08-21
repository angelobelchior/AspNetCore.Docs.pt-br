<a name="csc"></a>

Considere o seguinte `ConfigureServices` método, que registra os serviços e configura as opções:

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup2.cs?name=snippet)]

Grupos relacionados de registros podem ser movidos para um método de extensão para registrar serviços. Por exemplo, os serviços de configuração são adicionados à seguinte classe:

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Options/MyConfigServiceCollectionExtensions.cs)]

Os serviços restantes são registrados em uma classe semelhante. O método a seguir `ConfigureServices` usa os novos métodos de extensão para registrar os serviços:

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup4.cs?name=snippet)]

**_Observação:_** Cada `services.Add{GROUP_NAME}` método de extensão adiciona e potencialmente configura os serviços do. Por exemplo, <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A> adiciona os serviços que os controladores MVC com exibições exigem e <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> adiciona os serviços Razor Pages requer. Recomendamos que os aplicativos sigam essa Convenção de nomenclatura. Coloque os métodos de extensão no namespace <xref:Microsoft.Extensions.DependencyInjection?displayProperty=fullName> para encapsular grupos de registros de serviço.
