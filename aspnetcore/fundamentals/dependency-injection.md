---
title: Injeção de dependência no ASP.NET Core
author: rick-anderson
description: Saiba como o ASP.NET Core implementa a injeção de dependência e como usá-la.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 7/21/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/dependency-injection
ms.openlocfilehash: 0d8b349d0381e2902907ea841e07bbc96db5b847
ms.sourcegitcommit: ba4872dd5a93780fe6cfacb2711ec1e69e0df92c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/12/2020
ms.locfileid: "88130623"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="d3437-103">Injeção de dependência no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d3437-103">Dependency injection in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d3437-104">Por [Kirk Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com)e [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="d3437-104">By [Kirk Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="d3437-105">O ASP.NET Core é compatível com o padrão de design de software de DI (injeção de dependência), que é uma técnica para alcançar a [IoC (Inversão de Controle)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) entre classes e suas dependências.</span><span class="sxs-lookup"><span data-stu-id="d3437-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="d3437-106">Para obter mais informações específicas sobre injeção de dependência em controladores de MVC, consulte <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="d3437-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="d3437-107">Para obter mais informações sobre injeção de dependência de opções, consulte <xref:fundamentals/configuration/options> .</span><span class="sxs-lookup"><span data-stu-id="d3437-107">For more information on dependency injection of options, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="d3437-108">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d3437-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="d3437-109">Visão geral da injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="d3437-109">Overview of dependency injection</span></span>

<span data-ttu-id="d3437-110">Uma *dependência* é qualquer objeto exigido por outro objeto.</span><span class="sxs-lookup"><span data-stu-id="d3437-110">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="d3437-111">Examine a classe `MyDependency` a seguir com um método `WriteMessage` do qual outras classes em um aplicativo dependem:</span><span class="sxs-lookup"><span data-stu-id="d3437-111">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

```csharp
public class MyDependency
{
    public MyDependency()
    {
    }

    public void WriteMessage(string message)
    {
        Console.WriteLine(
            $"MyDependency.WriteMessage called. Message: {message}");
    }
}
```

<span data-ttu-id="d3437-112">Uma instância da classe `MyDependency` pode ser criada para tornar o método `WriteMessage` disponível para uma classe.</span><span class="sxs-lookup"><span data-stu-id="d3437-112">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="d3437-113">A classe  é uma dependência da classe:</span><span class="sxs-lookup"><span data-stu-id="d3437-113">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

```csharp
public class IndexModel : PageModel
{
    private readonly MyDependency _dependency = new MyDependency();

    public void OnGet()
    {
        _dependency.WriteMessage(
            "IndexModel.OnGet created this message.");
    }
}
```

<span data-ttu-id="d3437-114">A classe cria e depende diretamente da instância `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="d3437-114">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="d3437-115">As dependências de código, como o exemplo anterior, são problemáticas e devem ser evitadas pelos seguintes motivos:</span><span class="sxs-lookup"><span data-stu-id="d3437-115">Code dependencies, such as the previous example, are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="d3437-116">Para substituir `MyDependency` por uma implementação diferente, a classe deve ser modificada.</span><span class="sxs-lookup"><span data-stu-id="d3437-116">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="d3437-117">Se `MyDependency` tiver dependências, elas deverão ser configuradas pela classe.</span><span class="sxs-lookup"><span data-stu-id="d3437-117">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="d3437-118">Em um projeto grande com várias classes dependendo da `MyDependency`, o código de configuração fica pulverizado por todo o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d3437-118">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="d3437-119">É difícil testar a unidade dessa implementação.</span><span class="sxs-lookup"><span data-stu-id="d3437-119">This implementation is difficult to unit test.</span></span> <span data-ttu-id="d3437-120">O aplicativo deve usar uma simulação ou stub da classe `MyDependency`, o que não é possível com essa abordagem.</span><span class="sxs-lookup"><span data-stu-id="d3437-120">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="d3437-121">Injeção de dependência trata desses problemas da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="d3437-121">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="d3437-122">O uso de uma interface ou classe base para abstrair a implementação da dependência.</span><span class="sxs-lookup"><span data-stu-id="d3437-122">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="d3437-123">Registrando a dependência em um contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="d3437-123">Registration of the dependency in a service container.</span></span> <span data-ttu-id="d3437-124">O ASP.NET Core fornece um contêiner de serviço interno, o <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="d3437-124">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="d3437-125">Os serviços são registrados no método `Startup.ConfigureServices` do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d3437-125">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="d3437-126">*Injeção* do serviço no construtor da classe na qual ele é usado.</span><span class="sxs-lookup"><span data-stu-id="d3437-126">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="d3437-127">A estrutura assume a responsabilidade de criar uma instância da dependência e de descartá-la quando não for mais necessária.</span><span class="sxs-lookup"><span data-stu-id="d3437-127">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="d3437-128">No [exemplo de aplicativo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), a interface `IMyDependency` define um método que o serviço fornece ao aplicativo:</span><span class="sxs-lookup"><span data-stu-id="d3437-128">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="d3437-129">Essa interface é implementada por um tipo concreto, `MyDependency`:</span><span class="sxs-lookup"><span data-stu-id="d3437-129">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="d3437-130">No exemplo de aplicativo, o serviço `IMyDependency` está registrado com o tipo concreto `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="d3437-130">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="d3437-131">O <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> método registra o serviço com um tempo de vida de escopo, o tempo de vida de uma única solicitação.</span><span class="sxs-lookup"><span data-stu-id="d3437-131">The <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> method registers the service with a scoped lifetime, the lifetime of a single request.</span></span> <span data-ttu-id="d3437-132">Descreveremos posteriormente neste tópico os [tempos de vida do serviço](#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="d3437-132">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency.cs?name=snippet1)]

<span data-ttu-id="d3437-133">No exemplo de aplicativo, a instância `IMyDependency` é solicitada e usada para chamar o método `WriteMessage` do serviço:</span><span class="sxs-lookup"><span data-stu-id="d3437-133">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index2.cshtml.cs?name=snippet1)]

<span data-ttu-id="d3437-134">Com o padrão de DI:</span><span class="sxs-lookup"><span data-stu-id="d3437-134">With the DI pattern:</span></span>

* <span data-ttu-id="d3437-135">O controlador não usa o tipo concreto `MyDependency` , apenas a interface `IMyDependency` .</span><span class="sxs-lookup"><span data-stu-id="d3437-135">The controller doesn't use the concrete type `MyDependency`, only the interface `IMyDependency`.</span></span> <span data-ttu-id="d3437-136">Isso facilita a alteração da implementação que o controlador usa sem modificar o controlador.</span><span class="sxs-lookup"><span data-stu-id="d3437-136">That makes it easy to change the implementation that the controller uses without modifying the controller.</span></span>
* <span data-ttu-id="d3437-137">O controlador não cria uma instância do `MyDependency` , ele é criado pelo contêiner de di.</span><span class="sxs-lookup"><span data-stu-id="d3437-137">The controller doesn't create an instance of `MyDependency`, it's created by the DI container.</span></span>

<span data-ttu-id="d3437-138">A implementação da `IMyDependency` interface pode ser aprimorada usando a API de registro em log interna:</span><span class="sxs-lookup"><span data-stu-id="d3437-138">The implementation of the `IMyDependency` interface can be improved by using the built-in logging API:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet2)]

<span data-ttu-id="d3437-139">O `ConfigureServices` método atualizado registra a nova `IMyDependency` implementação:</span><span class="sxs-lookup"><span data-stu-id="d3437-139">The updated `ConfigureServices` method registers the new `IMyDependency` implementation:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency2.cs?name=snippet1)]

<span data-ttu-id="d3437-140">`MyDependency2`solicita um <xref:Microsoft.Extensions.Logging.ILogger`1> no construtor.</span><span class="sxs-lookup"><span data-stu-id="d3437-140">`MyDependency2` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in the constructor.</span></span> <span data-ttu-id="d3437-141">Não é incomum usar a injeção de dependência de uma maneira encadeada.</span><span class="sxs-lookup"><span data-stu-id="d3437-141">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="d3437-142">Por sua vez, cada dependência solicitada solicita suas próprias dependências.</span><span class="sxs-lookup"><span data-stu-id="d3437-142">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="d3437-143">O contêiner resolve as dependências no grafo e retorna o serviço totalmente resolvido.</span><span class="sxs-lookup"><span data-stu-id="d3437-143">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="d3437-144">O conjunto de dependências que precisa ser resolvido normalmente é chamado de *árvore de dependência*, *grafo de dependência* ou *grafo de objeto*.</span><span class="sxs-lookup"><span data-stu-id="d3437-144">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="d3437-145">`ILogger<TCategoryName>`é um [serviço fornecido pelo Framework](#framework-provided-services).</span><span class="sxs-lookup"><span data-stu-id="d3437-145">`ILogger<TCategoryName>` is a [framework-provided service](#framework-provided-services).</span></span>

<span data-ttu-id="d3437-146">O contêiner resolve aproveitando `ILogger<TCategoryName>` os [tipos abertos (genéricos)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminando a necessidade de registrar cada [tipo construído (genérico)](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span><span class="sxs-lookup"><span data-stu-id="d3437-146">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span></span>

<span data-ttu-id="d3437-147">Na terminologia de injeção de dependência, um serviço:</span><span class="sxs-lookup"><span data-stu-id="d3437-147">In dependency injection terminology, a service:</span></span>

* <span data-ttu-id="d3437-148">Normalmente é um objeto que fornece um serviço para outro código no aplicativo, como o `IMyDependency` serviço.</span><span class="sxs-lookup"><span data-stu-id="d3437-148">Is typically an object that provides a service to other code in the app, such as the `IMyDependency` service.</span></span>
* <span data-ttu-id="d3437-149">O não está relacionado a um serviço Web, embora o serviço possa usar um serviço Web.</span><span class="sxs-lookup"><span data-stu-id="d3437-149">Is not related to a web service, although the service may use a web service.</span></span>

<span data-ttu-id="d3437-150">A estrutura fornece um sistema de [registro em log](xref:fundamentals/logging/index) robusto.</span><span class="sxs-lookup"><span data-stu-id="d3437-150">The framework provides a robust [logging](xref:fundamentals/logging/index) system.</span></span> <span data-ttu-id="d3437-151">As `IMyDependency` implementações foram escritas para demonstrar a di básica, não para implementar o registro em log.</span><span class="sxs-lookup"><span data-stu-id="d3437-151">The `IMyDependency` implementations were written to demonstrate basic DI, not to implement logging.</span></span> <span data-ttu-id="d3437-152">A maioria dos aplicativos não deve precisar gravar agentes.</span><span class="sxs-lookup"><span data-stu-id="d3437-152">Most apps shouldn't need to write loggers.</span></span> <span data-ttu-id="d3437-153">O código a seguir demonstra como usar o log padrão, que não exige que nenhum serviço seja registrado no `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="d3437-153">The following code demonstrates using the default logging, which doesn't require any services to be registered in `ConfigureServices`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/About.cshtml.cs?name=snippet)]

<span data-ttu-id="d3437-154">Usando o código anterior, não há necessidade de atualizar `ConfigureServices` porque o [registro em log](xref:fundamentals/logging/index) é fornecido pela estrutura:</span><span class="sxs-lookup"><span data-stu-id="d3437-154">Using the preceding code, there is no need to update `ConfigureServices` because [logging](xref:fundamentals/logging/index) is provided by the framework:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages();
}
```

## <a name="services-injected-into-startup"></a><span data-ttu-id="d3437-155">Serviços injetados na inicialização</span><span class="sxs-lookup"><span data-stu-id="d3437-155">Services injected into Startup</span></span>

<span data-ttu-id="d3437-156">Somente os seguintes tipos de serviço podem ser injetados no `Startup` Construtor ao usar o host genérico ( <xref:Microsoft.Extensions.Hosting.IHostBuilder> ):</span><span class="sxs-lookup"><span data-stu-id="d3437-156">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="d3437-157">Os serviços podem ser injetados em `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="d3437-157">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="d3437-158">Para obter mais informações, consulte <xref:fundamentals/startup> e [acessar a configuração na inicialização](xref:fundamentals/configuration/index#access-configuration-in-startup).</span><span class="sxs-lookup"><span data-stu-id="d3437-158">For more information, see <xref:fundamentals/startup> and [Access configuration in Startup](xref:fundamentals/configuration/index#access-configuration-in-startup).</span></span>

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="d3437-159">Registrar serviços adicionais com métodos de extensão</span><span class="sxs-lookup"><span data-stu-id="d3437-159">Register additional services with extension methods</span></span>

<span data-ttu-id="d3437-160">Quando um método de extensão da coleção de serviços está disponível para registrar um serviço:</span><span class="sxs-lookup"><span data-stu-id="d3437-160">When a service collection extension method is available to register a service:</span></span>

* <span data-ttu-id="d3437-161">A Convenção é usar um método de `Add{SERVICE_NAME}` extensão único para registrar todos os serviços exigidos por esse serviço.</span><span class="sxs-lookup"><span data-stu-id="d3437-161">The convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span>
* <span data-ttu-id="d3437-162">Os serviços dependentes também são registrados.</span><span class="sxs-lookup"><span data-stu-id="d3437-162">The dependent services are also registered.</span></span>

<span data-ttu-id="d3437-163">O código a seguir é gerado pelo Razor modelo de páginas usando contas de usuário individuais e mostra como adicionar serviços adicionais ao contêiner usando os métodos de extensão <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> e <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A> :</span><span class="sxs-lookup"><span data-stu-id="d3437-163">The following code is generated by the Razor Pages template using individual user accounts and shows how to add additional services to the container using the extension methods <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A>:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupEF.cs?name=snippet)]

<span data-ttu-id="d3437-164">Para obter mais informações, consulte <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> e <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="d3437-164">For more information, see <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> and <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="d3437-165">Consulte a seção [combinando a coleta de serviços](#csc) para obter instruções sobre como escrever um método de extensão para registrar serviços.</span><span class="sxs-lookup"><span data-stu-id="d3437-165">See the section [Combining service collection](#csc) for instructions on writing an extension method to register services.</span></span>

[!INCLUDE[](~/includes/combine-di.md)]

## <a name="service-lifetimes"></a><span data-ttu-id="d3437-166">Tempos de vida do serviço</span><span class="sxs-lookup"><span data-stu-id="d3437-166">Service lifetimes</span></span>

<span data-ttu-id="d3437-167">Escolha um tempo de vida apropriado para cada serviço registrado.</span><span class="sxs-lookup"><span data-stu-id="d3437-167">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="d3437-168">Os serviços do ASP.NET Core podem ser configurados com os seguintes tempos de vida:</span><span class="sxs-lookup"><span data-stu-id="d3437-168">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="d3437-169">Transitório</span><span class="sxs-lookup"><span data-stu-id="d3437-169">Transient</span></span>

<span data-ttu-id="d3437-170">Serviços temporários de tempo de vida (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) são criados cada vez que são solicitados pelo contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="d3437-170">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="d3437-171">Esse tempo de vida funciona melhor para serviços leves e sem estado.</span><span class="sxs-lookup"><span data-stu-id="d3437-171">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="d3437-172">Em aplicativos que processam solicitações, os serviços transitórios são descartados no final da solicitação.</span><span class="sxs-lookup"><span data-stu-id="d3437-172">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="d3437-173">Com escopo</span><span class="sxs-lookup"><span data-stu-id="d3437-173">Scoped</span></span>

<span data-ttu-id="d3437-174">Os serviços com tempo de vida (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) com escopo são criados uma vez por solicitação de cliente (conexão).</span><span class="sxs-lookup"><span data-stu-id="d3437-174">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="d3437-175">Ao usar Entity Framework Core, o <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> método de extensão registra os `DbContext` tipos com um tempo de vida com escopo definido por padrão.</span><span class="sxs-lookup"><span data-stu-id="d3437-175">When using Entity Framework Core, the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension method registers `DbContext` types with a scoped lifetime by default.</span></span>

<span data-ttu-id="d3437-176">Em aplicativos que processam solicitações, os serviços com escopo são descartados no final da solicitação.</span><span class="sxs-lookup"><span data-stu-id="d3437-176">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

<span data-ttu-id="d3437-177">Use serviços com escopo no middleware com uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="d3437-177">Use scoped services in middleware with one of the following approaches:</span></span>

* <span data-ttu-id="d3437-178">Injetar o serviço no `Invoke` `InvokeAsync` método ou.</span><span class="sxs-lookup"><span data-stu-id="d3437-178">Inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="d3437-179">Injetar por [injeção de Construtor](xref:mvc/controllers/dependency-injection#constructor-injection) gera uma exceção em tempo de execução porque força o serviço a se comportar como um singleton.</span><span class="sxs-lookup"><span data-stu-id="d3437-179">Injecting by [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) throws an exception at run time because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="d3437-180">O exemplo nas [Opções de tempo de vida e de registro](#lifetime-and-registration-options) usa a `InvokeAsync` abordagem.</span><span class="sxs-lookup"><span data-stu-id="d3437-180">The sample in the [Lifetime and registration options](#lifetime-and-registration-options) uses the `InvokeAsync` approach.</span></span>
* <span data-ttu-id="d3437-181">[Middleware baseado em fábrica](<xref:fundamentals/middleware/extensibility>).</span><span class="sxs-lookup"><span data-stu-id="d3437-181">[Factory-based middleware](<xref:fundamentals/middleware/extensibility>).</span></span> <span data-ttu-id="d3437-182">Os métodos de extensão <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> verificam se o tipo registrado de um middleware implementa <xref:Microsoft.AspNetCore.Http.IMiddleware>.</span><span class="sxs-lookup"><span data-stu-id="d3437-182"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> extension methods check if a middleware's registered type implements <xref:Microsoft.AspNetCore.Http.IMiddleware>.</span></span> <span data-ttu-id="d3437-183">Se isso acontecer, a instância <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> registrada no contêiner será usada para resolver a implementação <xref:Microsoft.AspNetCore.Http.IMiddleware> em vez de usar a lógica de ativação de middleware baseada em convenção.</span><span class="sxs-lookup"><span data-stu-id="d3437-183">If it does, the <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> instance registered in the container is used to resolve the <xref:Microsoft.AspNetCore.Http.IMiddleware> implementation instead of using the convention-based middleware activation logic.</span></span> <span data-ttu-id="d3437-184">O middleware é registrado como um serviço com escopo ou transitório no contêiner de serviço do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d3437-184">The middleware is registered as a scoped or transient service in the app's service container.</span></span>

<span data-ttu-id="d3437-185">Para obter mais informações, consulte <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="d3437-185">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

<span data-ttu-id="d3437-186">***Não*** resolva um serviço com escopo de um singleton.</span><span class="sxs-lookup"><span data-stu-id="d3437-186">Do ***not*** resolve a scoped service from a singleton.</span></span> <span data-ttu-id="d3437-187">Pode fazer com que o serviço tenha um estado incorreto durante o processamento das solicitações seguintes.</span><span class="sxs-lookup"><span data-stu-id="d3437-187">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="d3437-188">Não há problema em:</span><span class="sxs-lookup"><span data-stu-id="d3437-188">It's fine to:</span></span>

* <span data-ttu-id="d3437-189">Resolva um serviço singleton de um serviço com escopo ou transitório.</span><span class="sxs-lookup"><span data-stu-id="d3437-189">Resolve a singleton service from a scoped or transient service.</span></span>
* <span data-ttu-id="d3437-190">Resolva um serviço com escopo de outro serviço com escopo ou transitório.</span><span class="sxs-lookup"><span data-stu-id="d3437-190">Resolve a scoped service from another scoped or transient service.</span></span>

<span data-ttu-id="d3437-191">Por padrão, no ambiente de desenvolvimento, a resolução de um serviço de outro serviço com tempo de vida maior gera uma exceção.</span><span class="sxs-lookup"><span data-stu-id="d3437-191">By default, in the development environment, resolving a service from another service with longer lifetime throws an exception.</span></span> <span data-ttu-id="d3437-192">Para obter mais informações, confira [Validação de escopo](#sv).</span><span class="sxs-lookup"><span data-stu-id="d3437-192">For more information, see [Scope validation](#sv).</span></span>

### <a name="singleton"></a><span data-ttu-id="d3437-193">Singleton</span><span class="sxs-lookup"><span data-stu-id="d3437-193">Singleton</span></span>

<span data-ttu-id="d3437-194">Os serviços de vida útil singleton ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*> ) são criados:</span><span class="sxs-lookup"><span data-stu-id="d3437-194">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created either:</span></span>

* <span data-ttu-id="d3437-195">Na primeira vez que forem solicitadas.</span><span class="sxs-lookup"><span data-stu-id="d3437-195">The first time they're requested.</span></span>
* <span data-ttu-id="d3437-196">Pelo desenvolvedor, ao fornecer uma instância de implementação diretamente para o contêiner.</span><span class="sxs-lookup"><span data-stu-id="d3437-196">By the developer, when providing an implementation instance directly to the container.</span></span> <span data-ttu-id="d3437-197">Essa abordagem raramente é necessária.</span><span class="sxs-lookup"><span data-stu-id="d3437-197">This approach is rarely needed.</span></span>

<span data-ttu-id="d3437-198">Cada solicitação subsequente usa a mesma instância.</span><span class="sxs-lookup"><span data-stu-id="d3437-198">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="d3437-199">Se o aplicativo exigir um comportamento singleton, permita que o contêiner de serviço gerencie o tempo de vida do serviço.</span><span class="sxs-lookup"><span data-stu-id="d3437-199">If the app requires singleton behavior, allow the service container to manage the service's lifetime.</span></span> <span data-ttu-id="d3437-200">Não implemente o padrão de design singleton e forneça código para descartar o singleton.</span><span class="sxs-lookup"><span data-stu-id="d3437-200">Don't implement the singleton design pattern and provide code to dispose of the singleton.</span></span> <span data-ttu-id="d3437-201">Os serviços nunca devem ser descartados pelo código que resolveu o serviço de um contêiner.</span><span class="sxs-lookup"><span data-stu-id="d3437-201">Services should never be disposed by code that resolved the service from a container.</span></span> <span data-ttu-id="d3437-202">Se um tipo ou fábrica for registrado como um singleton, o contêiner descartará o singleton automaticamente.</span><span class="sxs-lookup"><span data-stu-id="d3437-202">If a type or factory is registered as a singleton, the container will dispose the singleton automatically.</span></span>

<span data-ttu-id="d3437-203">Os serviços singleton devem ser thread-safe e geralmente são usados em serviços sem estado.</span><span class="sxs-lookup"><span data-stu-id="d3437-203">Singleton services must be thread safe and are often used in stateless services.</span></span>

<span data-ttu-id="d3437-204">Em aplicativos que processam solicitações, os serviços singleton são descartados quando o <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> é Descartado no desligamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d3437-204">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed on application shutdown.</span></span> <span data-ttu-id="d3437-205">Como a memória não é liberada até que o aplicativo seja desligado, o uso de memória com um singleton deve ser considerado.</span><span class="sxs-lookup"><span data-stu-id="d3437-205">Because memory is not released until the app is shut down, memory use with a singleton must be considered.</span></span>

> [!WARNING]
> <span data-ttu-id="d3437-206">***Não*** resolva um serviço com escopo de um singleton.</span><span class="sxs-lookup"><span data-stu-id="d3437-206">Do ***not*** resolve a scoped service from a singleton.</span></span> <span data-ttu-id="d3437-207">Pode fazer com que o serviço tenha um estado incorreto durante o processamento das solicitações seguintes.</span><span class="sxs-lookup"><span data-stu-id="d3437-207">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="d3437-208">É bom resolver um serviço singleton de um serviço com escopo ou transitório.</span><span class="sxs-lookup"><span data-stu-id="d3437-208">It's fine to resolve a singleton service from a scoped or transient service.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="d3437-209">Métodos de registro do serviço</span><span class="sxs-lookup"><span data-stu-id="d3437-209">Service registration methods</span></span>

<span data-ttu-id="d3437-210">Os métodos de extensão de registro de serviço oferecem sobrecargas que são úteis em cenários específicos.</span><span class="sxs-lookup"><span data-stu-id="d3437-210">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>
<!-- Review: Auto disposal at end of app lifetime is not what you think of auto disposal  -->

| <span data-ttu-id="d3437-211">Método</span><span class="sxs-lookup"><span data-stu-id="d3437-211">Method</span></span> | <span data-ttu-id="d3437-212">Automático</span><span class="sxs-lookup"><span data-stu-id="d3437-212">Automatic</span></span><br><span data-ttu-id="d3437-213">object</span><span class="sxs-lookup"><span data-stu-id="d3437-213">object</span></span><br><span data-ttu-id="d3437-214">descarte</span><span class="sxs-lookup"><span data-stu-id="d3437-214">disposal</span></span> | <span data-ttu-id="d3437-215">Vários</span><span class="sxs-lookup"><span data-stu-id="d3437-215">Multiple</span></span><br><span data-ttu-id="d3437-216">implementações</span><span class="sxs-lookup"><span data-stu-id="d3437-216">implementations</span></span> | <span data-ttu-id="d3437-217">Passar argumentos</span><span class="sxs-lookup"><span data-stu-id="d3437-217">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="d3437-218">Exemplo:</span><span class="sxs-lookup"><span data-stu-id="d3437-218">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="d3437-219">Sim</span><span class="sxs-lookup"><span data-stu-id="d3437-219">Yes</span></span> | <span data-ttu-id="d3437-220">Sim</span><span class="sxs-lookup"><span data-stu-id="d3437-220">Yes</span></span> | <span data-ttu-id="d3437-221">Não</span><span class="sxs-lookup"><span data-stu-id="d3437-221">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="d3437-222">Exemplos:</span><span class="sxs-lookup"><span data-stu-id="d3437-222">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep(99));` | <span data-ttu-id="d3437-223">Sim</span><span class="sxs-lookup"><span data-stu-id="d3437-223">Yes</span></span> | <span data-ttu-id="d3437-224">Sim</span><span class="sxs-lookup"><span data-stu-id="d3437-224">Yes</span></span> | <span data-ttu-id="d3437-225">Sim</span><span class="sxs-lookup"><span data-stu-id="d3437-225">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="d3437-226">Exemplo:</span><span class="sxs-lookup"><span data-stu-id="d3437-226">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="d3437-227">Sim</span><span class="sxs-lookup"><span data-stu-id="d3437-227">Yes</span></span> | <span data-ttu-id="d3437-228">Não</span><span class="sxs-lookup"><span data-stu-id="d3437-228">No</span></span> | <span data-ttu-id="d3437-229">Não</span><span class="sxs-lookup"><span data-stu-id="d3437-229">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="d3437-230">Exemplos:</span><span class="sxs-lookup"><span data-stu-id="d3437-230">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep(99));` | <span data-ttu-id="d3437-231">Não</span><span class="sxs-lookup"><span data-stu-id="d3437-231">No</span></span> | <span data-ttu-id="d3437-232">Sim</span><span class="sxs-lookup"><span data-stu-id="d3437-232">Yes</span></span> | <span data-ttu-id="d3437-233">Sim</span><span class="sxs-lookup"><span data-stu-id="d3437-233">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="d3437-234">Exemplos:</span><span class="sxs-lookup"><span data-stu-id="d3437-234">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep(99));` | <span data-ttu-id="d3437-235">Não</span><span class="sxs-lookup"><span data-stu-id="d3437-235">No</span></span> | <span data-ttu-id="d3437-236">Não</span><span class="sxs-lookup"><span data-stu-id="d3437-236">No</span></span> | <span data-ttu-id="d3437-237">Sim</span><span class="sxs-lookup"><span data-stu-id="d3437-237">Yes</span></span> |

<span data-ttu-id="d3437-238">Para obter mais informações sobre o descarte de tipos, consulte a seção [Descarte de serviços](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="d3437-238">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="d3437-239">Um cenário comum para várias implementações é a [simulação de tipos para teste](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="d3437-239">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="d3437-240">`TryAdd{LIFETIME}`os métodos registram o serviço se já não houver uma implementação registrada.</span><span class="sxs-lookup"><span data-stu-id="d3437-240">`TryAdd{LIFETIME}` methods register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="d3437-241">No exemplo a seguir, a primeira linha registra `MyDependency` para `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="d3437-241">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="d3437-242">A segunda linha não tem nenhum efeito porque `IMyDependency` já tem uma implementação registrada:</span><span class="sxs-lookup"><span data-stu-id="d3437-242">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="d3437-243">Para obter mais informações, consulte:</span><span class="sxs-lookup"><span data-stu-id="d3437-243">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="d3437-244">Os métodos [TryAddEnumerable (Service Descriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) registram o serviço se ainda não houver uma implementação *do mesmo tipo*.</span><span class="sxs-lookup"><span data-stu-id="d3437-244">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="d3437-245">Vários serviços são resolvidos via `IEnumerable<{SERVICE}>`.</span><span class="sxs-lookup"><span data-stu-id="d3437-245">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="d3437-246">Ao registrar serviços, o desenvolvedor deve adicionar uma instância se um do mesmo tipo ainda não tiver sido adicionado.</span><span class="sxs-lookup"><span data-stu-id="d3437-246">When registering services, the developer should add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="d3437-247">Em geral, os autores `TryAddEnumerable` de biblioteca usam para evitar o registro de várias cópias de uma implementação no contêiner.</span><span class="sxs-lookup"><span data-stu-id="d3437-247">Generally, library authors use `TryAddEnumerable` to avoid registering multiple copies of an implementation in the container.</span></span>

<span data-ttu-id="d3437-248">No exemplo a seguir, a primeira linha registra `MyDep` para `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="d3437-248">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="d3437-249">A segunda linha registra `MyDep` para `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="d3437-249">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="d3437-250">A terceira linha não tem nenhum efeito porque `IMyDep1` já tem uma implementação registrada de `MyDep`:</span><span class="sxs-lookup"><span data-stu-id="d3437-250">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

<span data-ttu-id="d3437-251">O registro de serviço geralmente é independente de ordem, exceto ao registrar várias implementações do mesmo tipo.</span><span class="sxs-lookup"><span data-stu-id="d3437-251">Service registration is generally order independent except when registering multiple implementations of the same type.</span></span>

<span data-ttu-id="d3437-252">`IServiceCollection`é uma coleção de <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> .</span><span class="sxs-lookup"><span data-stu-id="d3437-252">`IServiceCollection` is a collection of <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>.</span></span> <span data-ttu-id="d3437-253">O código a seguir mostra como adicionar um serviço com um construtor:</span><span class="sxs-lookup"><span data-stu-id="d3437-253">The following code shows how to add a service with a constructor:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup5.cs?name=snippet)]

<span data-ttu-id="d3437-254">Os `Add{LIFETIME}` métodos usam a mesma abordagem.</span><span class="sxs-lookup"><span data-stu-id="d3437-254">The `Add{LIFETIME}` methods use the same approach.</span></span> <span data-ttu-id="d3437-255">Por exemplo, consulte o [código-fonte para Addscoped](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237).</span><span class="sxs-lookup"><span data-stu-id="d3437-255">For example, see the [source code to AddScoped](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237).</span></span>

### <a name="constructor-injection-behavior"></a><span data-ttu-id="d3437-256">Comportamento da injeção de construtor</span><span class="sxs-lookup"><span data-stu-id="d3437-256">Constructor injection behavior</span></span>

<span data-ttu-id="d3437-257">Os serviços podem ser resolvidos por dois mecanismos:</span><span class="sxs-lookup"><span data-stu-id="d3437-257">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="d3437-258"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span><span class="sxs-lookup"><span data-stu-id="d3437-258"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span></span>
  * <span data-ttu-id="d3437-259">Cria objetos sem registro de serviço no contêiner de injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="d3437-259">Creates objects without service registration in the dependency injection container.</span></span>
  * <span data-ttu-id="d3437-260">Usado com recursos de estrutura, como [auxiliares de marca](xref:mvc/views/tag-helpers/intro), controladores MVC e [ASSOCIADORES de modelo](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="d3437-260">Used with framework features, such as [Tag Helpers](xref:mvc/views/tag-helpers/intro), MVC controllers, and [model binders](xref:mvc/models/model-binding).</span></span>

<span data-ttu-id="d3437-261">Os construtores podem aceitar argumentos que não são fornecidos pela injeção de dependência, mas que precisam atribuir valores padrão.</span><span class="sxs-lookup"><span data-stu-id="d3437-261">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="d3437-262">Quando os serviços são resolvidos pelo `IServiceProvider` ou `ActivatorUtilities` , a [injeção de Construtor](xref:mvc/controllers/dependency-injection#constructor-injection) requer um construtor *público* .</span><span class="sxs-lookup"><span data-stu-id="d3437-262">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="d3437-263">Quando os serviços são resolvidos pelo `ActivatorUtilities` , a [injeção de Construtor](xref:mvc/controllers/dependency-injection#constructor-injection) requer que apenas um Construtor aplicável exista.</span><span class="sxs-lookup"><span data-stu-id="d3437-263">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="d3437-264">Há suporte para sobrecargas de construtor, mas somente uma sobrecarga pode existir, cujos argumentos podem ser todos atendidos pela injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="d3437-264">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="d3437-265">Contextos de Entity Framework</span><span class="sxs-lookup"><span data-stu-id="d3437-265">Entity Framework contexts</span></span>

<span data-ttu-id="d3437-266">Contextos do Entity Framework geralmente são adicionados ao contêiner de serviço usando o [tempo de vida com escopo](#service-lifetimes), pois o escopo de operações de banco de dados de aplicativo Web normalmente é para a solicitação do cliente.</span><span class="sxs-lookup"><span data-stu-id="d3437-266">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="d3437-267">O tempo de vida padrão será definido como escopo se um tempo de vida não for especificado por uma sobrecarga [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) ao registrar o contexto do banco de dados.</span><span class="sxs-lookup"><span data-stu-id="d3437-267">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="d3437-268">Serviços com um determinado tempo de vida não devem usar um contexto de banco de dados com um tempo de vida mais curto que aquele do serviço.</span><span class="sxs-lookup"><span data-stu-id="d3437-268">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="d3437-269">Opções de tempo de vida e de registro</span><span class="sxs-lookup"><span data-stu-id="d3437-269">Lifetime and registration options</span></span>

<span data-ttu-id="d3437-270">Para demonstrar a diferença entre as opções de tempo de vida e de registro, considere as seguintes interfaces que representam as tarefas como uma operação com um identificador, `OperationId` .</span><span class="sxs-lookup"><span data-stu-id="d3437-270">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with an identifier, `OperationId`.</span></span> <span data-ttu-id="d3437-271">Dependendo de como o tempo de vida do serviço de uma operação está configurado para as interfaces a seguir, o contêiner fornecerá a mesma instância ou outra do serviço quando solicitado por uma classe:</span><span class="sxs-lookup"><span data-stu-id="d3437-271">Depending on how the lifetime of an operation's service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="d3437-272">As interfaces são implementadas na classe `Operation`.</span><span class="sxs-lookup"><span data-stu-id="d3437-272">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="d3437-273">O `Operation` Construtor gera os quatro últimos caracteres de um GUID, se um não for fornecido:</span><span class="sxs-lookup"><span data-stu-id="d3437-273">The `Operation` constructor generates the last 4 characters of a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<!--
An `OperationService` is registered that depends on each of the other `Operation` types. When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.

* When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`. `OperationService` receives a new instance of the `IOperationTransient` class. The new instance yields a different `OperationId`.
* When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request. Across client requests, both services share a different `OperationId` value.
* When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

-->

<span data-ttu-id="d3437-274">Em `Startup.ConfigureServices`, cada tipo é adicionado ao contêiner de acordo com seu tempo de vida nomeado:</span><span class="sxs-lookup"><span data-stu-id="d3437-274">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup2.cs?name=snippet1)]

<span data-ttu-id="d3437-275">O aplicativo de exemplo demonstra os tempos de vida dos objetos dentro e entre as solicitações.</span><span class="sxs-lookup"><span data-stu-id="d3437-275">The sample app demonstrates object lifetimes within and between requests.</span></span> <span data-ttu-id="d3437-276">O aplicativo de exemplo `IndexModel` e o middleware solicitam cada tipo de `IOperation` tipo e registra o `OperationId` :</span><span class="sxs-lookup"><span data-stu-id="d3437-276">The sample app's `IndexModel` and middleware requests each kind of `IOperation` type and logs the `OperationId`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="d3437-277">O middleware é semelhante ao `IndexModel` e resolve os mesmos serviços:</span><span class="sxs-lookup"><span data-stu-id="d3437-277">The middleware is similar to the `IndexModel` and resolves the same services:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet)]

<span data-ttu-id="d3437-278">Os serviços com escopo devem ser resolvidos no `InvokeAsync` método:</span><span class="sxs-lookup"><span data-stu-id="d3437-278">Scoped services must be resolved in the `InvokeAsync` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet2&highlight=2)]

<span data-ttu-id="d3437-279">A saída do agente mostra:</span><span class="sxs-lookup"><span data-stu-id="d3437-279">The logger output shows:</span></span>

* <span data-ttu-id="d3437-280">Os objetos *transitórios* sempre são diferentes.</span><span class="sxs-lookup"><span data-stu-id="d3437-280">*Transient* objects are always different.</span></span> <span data-ttu-id="d3437-281">O `OperationId` valor transitório é diferente no `IndexModel` e no middleware.</span><span class="sxs-lookup"><span data-stu-id="d3437-281">The transient `OperationId` value is different in the `IndexModel` and the middleware.</span></span>
* <span data-ttu-id="d3437-282">Os objetos com *escopo* são os mesmos em cada solicitação, mas diferentes em cada solicitação.</span><span class="sxs-lookup"><span data-stu-id="d3437-282">*Scoped* objects are the same in each request but different across each request.</span></span>
* <span data-ttu-id="d3437-283">Os objetos *singleton* são os mesmos para cada solicitação.</span><span class="sxs-lookup"><span data-stu-id="d3437-283">*Singleton* objects are the same for every request.</span></span>

<span data-ttu-id="d3437-284">Para reduzir a saída de log, defina "log: LogLevel: Microsoft: Error" na *appsettings.Development.jsno* arquivo:</span><span class="sxs-lookup"><span data-stu-id="d3437-284">To reduce the logging output, set "Logging:LogLevel:Microsoft:Error" in the *appsettings.Development.json* file:</span></span>

[!code-json[](dependency-injection/samples/3.x/DependencyInjectionSample/appsettings.Development.json?highlight=7)]

## <a name="call-services-from-main"></a><span data-ttu-id="d3437-285">Chamar os serviços desde o principal</span><span class="sxs-lookup"><span data-stu-id="d3437-285">Call services from main</span></span>

<span data-ttu-id="d3437-286">Crie um <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> com [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) para resolver um serviço com escopo dentro do escopo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d3437-286">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="d3437-287">Essa abordagem é útil para acessar um serviço com escopo na inicialização para executar tarefas de inicialização:</span><span class="sxs-lookup"><span data-stu-id="d3437-287">This approach is useful to access a scoped service at startup to run initialization tasks:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        var host = CreateHostBuilder(args).Build();

        using (var scope = host.Services.CreateScope())
        {
            var services = scope.ServiceProvider;

            try
            {
                SeedData.Initialize(services);
            }
            catch (Exception ex)
            {
                var logger = services.GetRequiredService<ILogger<Program>>();
                logger.LogError(ex, "An error occurred seeding the DB.");
            }
        }

        host.Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStartup<Startup>();
            });
}
```

<span data-ttu-id="d3437-288">O código anterior é de [Adicionar o inicializador de semente](xref:tutorials/razor-pages/sql?#add-the-seed-initializer) no Razor tutorial de páginas.</span><span class="sxs-lookup"><span data-stu-id="d3437-288">The preceding code is from [Add the seed initializer](xref:tutorials/razor-pages/sql?#add-the-seed-initializer) in the Razor Pages tutorial.</span></span>

<span data-ttu-id="d3437-289">O exemplo a seguir mostra como obter um contexto para o `IMyDependency` em `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="d3437-289">The following example shows how to obtain a context for the `IMyDependency` in `Program.Main`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Program.cs?name=snippet)]

<a name="sv"></a>

## <a name="scope-validation"></a><span data-ttu-id="d3437-290">Validação de escopo</span><span class="sxs-lookup"><span data-stu-id="d3437-290">Scope validation</span></span>

<span data-ttu-id="d3437-291">Quando o aplicativo está em execução no [ambiente de desenvolvimento](xref:fundamentals/environments) e chama [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) para criar o host, o provedor de serviço padrão executa verificações para verificar se:</span><span class="sxs-lookup"><span data-stu-id="d3437-291">When the app is running in the [Development environment](xref:fundamentals/environments) and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="d3437-292">Os serviços com escopo não são resolvidos direta ou indiretamente pelo provedor de serviço raiz.</span><span class="sxs-lookup"><span data-stu-id="d3437-292">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="d3437-293">Os serviços com escopo não são injetados direta ou indiretamente em singletons.</span><span class="sxs-lookup"><span data-stu-id="d3437-293">Scoped services aren't directly or indirectly injected into singletons.</span></span>
* <span data-ttu-id="d3437-294">Os serviços transitórios não são inseridos direta ou indiretamente em singletons ou serviços com escopo.</span><span class="sxs-lookup"><span data-stu-id="d3437-294">Transient services aren't directly or indirectly injected into singletons or scoped services.</span></span>

<span data-ttu-id="d3437-295">O provedor de serviços raiz é criado quando <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> é chamado.</span><span class="sxs-lookup"><span data-stu-id="d3437-295">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="d3437-296">O tempo de vida do provedor de serviço raiz corresponde ao tempo de vida do aplicativo quando o provedor começa com o aplicativo e é Descartado quando o aplicativo é desligado.</span><span class="sxs-lookup"><span data-stu-id="d3437-296">The root service provider's lifetime corresponds to the app's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="d3437-297">Os serviços com escopo são descartados pelo contêiner que os criou.</span><span class="sxs-lookup"><span data-stu-id="d3437-297">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="d3437-298">Se um serviço com escopo for criado no contêiner raiz, o tempo de vida do serviço será efetivamente promovido para singleton porque é descartado apenas pelo contêiner raiz quando o aplicativo é desligado.</span><span class="sxs-lookup"><span data-stu-id="d3437-298">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app is shut down.</span></span> <span data-ttu-id="d3437-299">A validação dos escopos de serviço detecta essas situações quando `BuildServiceProvider` é chamado.</span><span class="sxs-lookup"><span data-stu-id="d3437-299">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="d3437-300">Para obter mais informações, confira [Validação de escopo](xref:fundamentals/host/web-host#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="d3437-300">For more information, see [Scope validation](xref:fundamentals/host/web-host#scope-validation).</span></span>

## <a name="request-services"></a><span data-ttu-id="d3437-301">Serviços de solicitação</span><span class="sxs-lookup"><span data-stu-id="d3437-301">Request Services</span></span>

<span data-ttu-id="d3437-302">Os serviços disponíveis em uma solicitação do ASP.NET de `HttpContext` são expostos por meio da coleção [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).</span><span class="sxs-lookup"><span data-stu-id="d3437-302">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="d3437-303">Os Serviços de Solicitação representam os serviços configurados e solicitados como parte do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d3437-303">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="d3437-304">Quando os objetos especificam dependências, elas são atendidas pelos tipos encontrados em `RequestServices`, não em `ApplicationServices`.</span><span class="sxs-lookup"><span data-stu-id="d3437-304">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="d3437-305">Em geral, o aplicativo não deve usar essas propriedades diretamente.</span><span class="sxs-lookup"><span data-stu-id="d3437-305">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="d3437-306">Em vez disso, solicite os tipos que as classes exigem por meio de construtores de classe e permita que a estrutura Insira as dependências.</span><span class="sxs-lookup"><span data-stu-id="d3437-306">Instead, request the types that classes require via class constructors and allow the framework to inject the dependencies.</span></span> <span data-ttu-id="d3437-307">Isso resulta em classes que são mais fáceis de testar.</span><span class="sxs-lookup"><span data-stu-id="d3437-307">This yields classes that are easier to test.</span></span>

<span data-ttu-id="d3437-308">ASP.NET Core cria um escopo por solicitação e `RequestServices` expõe o provedor de serviço com escopo.</span><span class="sxs-lookup"><span data-stu-id="d3437-308">ASP.NET Core creates a scope per request and `RequestServices` exposes the scoped service provider.</span></span> <span data-ttu-id="d3437-309">Todos os serviços com escopo são válidos enquanto a solicitação está ativa.</span><span class="sxs-lookup"><span data-stu-id="d3437-309">All scoped services are valid for as long as the request is active.</span></span>

> [!NOTE]
> <span data-ttu-id="d3437-310">Prefira solicitar dependências como parâmetros de construtor para acessar a coleção `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="d3437-310">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="d3437-311">Projetar serviços para injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="d3437-311">Design services for dependency injection</span></span>

<span data-ttu-id="d3437-312">As melhores práticas são:</span><span class="sxs-lookup"><span data-stu-id="d3437-312">Best practices are to:</span></span>

* <span data-ttu-id="d3437-313">Projetar serviços para usar a injeção de dependência a fim de obter suas dependências.</span><span class="sxs-lookup"><span data-stu-id="d3437-313">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="d3437-314">Evite classes e membros com estado e estáticos.</span><span class="sxs-lookup"><span data-stu-id="d3437-314">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="d3437-315">Crie aplicativos para usar os serviços singleton, o que evita a criação de estado global.</span><span class="sxs-lookup"><span data-stu-id="d3437-315">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="d3437-316">Evite a instanciação direta das classes dependentes em serviços.</span><span class="sxs-lookup"><span data-stu-id="d3437-316">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="d3437-317">A instanciação direta acopla o código a uma implementação específica.</span><span class="sxs-lookup"><span data-stu-id="d3437-317">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="d3437-318">Verifique as classes de aplicativo pequenas, bem fatoradas e facilmente testadas.</span><span class="sxs-lookup"><span data-stu-id="d3437-318">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="d3437-319">Se uma classe parecer ter muitas dependências injetadas, isso geralmente é um sinal de que a classe tem muitas responsabilidades e está violando o [único princípio de responsabilidade (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="d3437-319">If a class seems to have too many injected dependencies, that's generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="d3437-320">Tente refatorar a classe movendo algumas de suas responsabilidades para uma nova classe.</span><span class="sxs-lookup"><span data-stu-id="d3437-320">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="d3437-321">Tenha em mente que as classes Razor de modelo de página de páginas e as classes do controlador MVC devem se concentrar nas preocupações da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="d3437-321">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="d3437-322">Descarte de serviços</span><span class="sxs-lookup"><span data-stu-id="d3437-322">Disposal of services</span></span>

<span data-ttu-id="d3437-323">O contêiner chama <xref:System.IDisposable.Dispose*> para os tipos <xref:System.IDisposable> criados por ele.</span><span class="sxs-lookup"><span data-stu-id="d3437-323">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="d3437-324">Os serviços nunca devem ser descartados por nenhum código que resolveu o serviço de um contêiner.</span><span class="sxs-lookup"><span data-stu-id="d3437-324">Services should never be disposed by any code that resolved the service from a container.</span></span> <span data-ttu-id="d3437-325">Se um tipo ou fábrica for registrado como um singleton, o contêiner descartará o singleton.</span><span class="sxs-lookup"><span data-stu-id="d3437-325">If a type or factory is registered as a singleton, the container disposes the singleton.</span></span>

<span data-ttu-id="d3437-326">No exemplo a seguir, os serviços são criados pelo contêiner de serviço e descartados automaticamente:</span><span class="sxs-lookup"><span data-stu-id="d3437-326">In the following example, the services are created by the service container and disposed automatically:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Services/Service1.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="d3437-327">O console de depuração mostra a saída a seguir após cada atualização da página de índice:</span><span class="sxs-lookup"><span data-stu-id="d3437-327">The debug console shows the following output after each refresh of the Index page:</span></span>

```console
Service1: IndexModel.OnGet
Service2: IndexModel.OnGet
Service3: IndexModel.OnGet
Service1.Dispose
```

### <a name="services-not-created-by-the-service-container"></a><span data-ttu-id="d3437-328">Serviços não criados pelo contêiner de serviço</span><span class="sxs-lookup"><span data-stu-id="d3437-328">Services not created by the service container</span></span>
<!--Review: Who cares that service instances aren't disposed, singletons aren't disposed until the app shuts down anyway.
  -->
<span data-ttu-id="d3437-329">Considere o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="d3437-329">Consider the following code:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup2.cs?name=snippet)]

<span data-ttu-id="d3437-330">No código anterior:</span><span class="sxs-lookup"><span data-stu-id="d3437-330">In the preceding code:</span></span>

* <span data-ttu-id="d3437-331">As instâncias de serviço não são criadas pelo contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="d3437-331">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="d3437-332">Os tempos de vida de serviço pretendidos não são conhecidos pela estrutura.</span><span class="sxs-lookup"><span data-stu-id="d3437-332">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="d3437-333">A estrutura não descarta os serviços automaticamente.</span><span class="sxs-lookup"><span data-stu-id="d3437-333">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="d3437-334">Se os serviços não forem explicitamente descartados no código do desenvolvedor, eles persistirão até que o aplicativo seja desligado.</span><span class="sxs-lookup"><span data-stu-id="d3437-334">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="d3437-335">Diretrizes de IDisposable para instâncias transitórias e compartilhadas</span><span class="sxs-lookup"><span data-stu-id="d3437-335">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="d3437-336">Tempo de vida transitório, limitado</span><span class="sxs-lookup"><span data-stu-id="d3437-336">Transient, limited lifetime</span></span>

<span data-ttu-id="d3437-337">**Cenário**</span><span class="sxs-lookup"><span data-stu-id="d3437-337">**Scenario**</span></span>

<span data-ttu-id="d3437-338">O aplicativo requer uma <xref:System.IDisposable> instância com um tempo de vida transitório para qualquer um dos seguintes cenários:</span><span class="sxs-lookup"><span data-stu-id="d3437-338">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="d3437-339">A instância é resolvida no escopo raiz (contêiner raiz).</span><span class="sxs-lookup"><span data-stu-id="d3437-339">The instance is resolved in the root scope (root container).</span></span>
* <span data-ttu-id="d3437-340">A instância deve ser descartada antes do término do escopo.</span><span class="sxs-lookup"><span data-stu-id="d3437-340">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="d3437-341">**Solução**</span><span class="sxs-lookup"><span data-stu-id="d3437-341">**Solution**</span></span>

<span data-ttu-id="d3437-342">Use o padrão de fábrica para criar uma instância fora do escopo pai.</span><span class="sxs-lookup"><span data-stu-id="d3437-342">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="d3437-343">Nessa situação, o aplicativo geralmente teria um `Create` método que chamasse o construtor do tipo final diretamente.</span><span class="sxs-lookup"><span data-stu-id="d3437-343">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="d3437-344">Se o tipo final tiver outras dependências, a fábrica poderá:</span><span class="sxs-lookup"><span data-stu-id="d3437-344">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="d3437-345">Receber um <xref:System.IServiceProvider> em seu construtor.</span><span class="sxs-lookup"><span data-stu-id="d3437-345">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="d3437-346">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> para instanciar a instância fora do contêiner, ao usar o contêiner para suas dependências.</span><span class="sxs-lookup"><span data-stu-id="d3437-346">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="d3437-347">Instância compartilhada, tempo de vida limitado</span><span class="sxs-lookup"><span data-stu-id="d3437-347">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="d3437-348">**Cenário**</span><span class="sxs-lookup"><span data-stu-id="d3437-348">**Scenario**</span></span>

<span data-ttu-id="d3437-349">O aplicativo requer uma <xref:System.IDisposable> instância compartilhada entre vários serviços, mas o <xref:System.IDisposable> deve ter um tempo de vida limitado.</span><span class="sxs-lookup"><span data-stu-id="d3437-349">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="d3437-350">**Solução**</span><span class="sxs-lookup"><span data-stu-id="d3437-350">**Solution**</span></span>

<span data-ttu-id="d3437-351">Registre a instância com um tempo de vida de escopo.</span><span class="sxs-lookup"><span data-stu-id="d3437-351">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="d3437-352">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> para iniciar e criar um novo <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> .</span><span class="sxs-lookup"><span data-stu-id="d3437-352">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="d3437-353">Use o escopo <xref:System.IServiceProvider> para obter os serviços necessários.</span><span class="sxs-lookup"><span data-stu-id="d3437-353">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="d3437-354">Descarte o escopo quando o tempo de vida deve terminar.</span><span class="sxs-lookup"><span data-stu-id="d3437-354">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-idisposable-guidelines"></a><span data-ttu-id="d3437-355">Diretrizes gerais de IDisposable</span><span class="sxs-lookup"><span data-stu-id="d3437-355">General IDisposable guidelines</span></span>

* <span data-ttu-id="d3437-356">Não registre <xref:System.IDisposable> instâncias com um escopo transitório.</span><span class="sxs-lookup"><span data-stu-id="d3437-356">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="d3437-357">Em vez disso, use o padrão de fábrica.</span><span class="sxs-lookup"><span data-stu-id="d3437-357">Use the factory pattern instead.</span></span>
* <span data-ttu-id="d3437-358">Não resolva instâncias transitórias ou com escopo definido <xref:System.IDisposable> no escopo raiz.</span><span class="sxs-lookup"><span data-stu-id="d3437-358">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="d3437-359">A única exceção geral é quando o aplicativo cria/recria e descarta o <xref:System.IServiceProvider> , que não é um padrão ideal.</span><span class="sxs-lookup"><span data-stu-id="d3437-359">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="d3437-360">O recebimento de uma <xref:System.IDisposable> dependência por meio de di não exige que o receptor se implemente <xref:System.IDisposable> .</span><span class="sxs-lookup"><span data-stu-id="d3437-360">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="d3437-361">O receptor da <xref:System.IDisposable> dependência não deve chamar <xref:System.IDisposable.Dispose%2A> essa dependência.</span><span class="sxs-lookup"><span data-stu-id="d3437-361">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="d3437-362">Os escopos devem ser usados para controlar o tempo de vida dos serviços.</span><span class="sxs-lookup"><span data-stu-id="d3437-362">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="d3437-363">Os escopos não são hierárquicos e não há nenhuma conexão especial entre escopos.</span><span class="sxs-lookup"><span data-stu-id="d3437-363">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="d3437-364">Substituição do contêiner de serviço padrão</span><span class="sxs-lookup"><span data-stu-id="d3437-364">Default service container replacement</span></span>

<span data-ttu-id="d3437-365">O contêiner de serviço interno foi projetado para atender às necessidades da estrutura e da maioria dos aplicativos de consumidor.</span><span class="sxs-lookup"><span data-stu-id="d3437-365">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="d3437-366">É recomendável usar o contêiner interno, a menos que você precise de um recurso específico ao qual o contêiner interno não ofereça suporte, como:</span><span class="sxs-lookup"><span data-stu-id="d3437-366">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="d3437-367">Injeção de propriedade</span><span class="sxs-lookup"><span data-stu-id="d3437-367">Property injection</span></span>
* <span data-ttu-id="d3437-368">Injeção com base no nome</span><span class="sxs-lookup"><span data-stu-id="d3437-368">Injection based on name</span></span>
* <span data-ttu-id="d3437-369">Contêineres filho</span><span class="sxs-lookup"><span data-stu-id="d3437-369">Child containers</span></span>
* <span data-ttu-id="d3437-370">Gerenciamento de tempo de vida personalizado</span><span class="sxs-lookup"><span data-stu-id="d3437-370">Custom lifetime management</span></span>
* <span data-ttu-id="d3437-371">Suporte de `Func<T>` para inicialização lenta</span><span class="sxs-lookup"><span data-stu-id="d3437-371">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="d3437-372">Registro baseado em Convenção</span><span class="sxs-lookup"><span data-stu-id="d3437-372">Convention-based registration</span></span>

<span data-ttu-id="d3437-373">Os seguintes contêineres de terceiros podem ser usados com aplicativos ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="d3437-373">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="d3437-374">Autofac</span><span class="sxs-lookup"><span data-stu-id="d3437-374">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="d3437-375">DryIoc</span><span class="sxs-lookup"><span data-stu-id="d3437-375">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="d3437-376">Grace</span><span class="sxs-lookup"><span data-stu-id="d3437-376">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="d3437-377">LightInject</span><span class="sxs-lookup"><span data-stu-id="d3437-377">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="d3437-378">Lamar</span><span class="sxs-lookup"><span data-stu-id="d3437-378">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="d3437-379">Stashbox</span><span class="sxs-lookup"><span data-stu-id="d3437-379">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="d3437-380">Unity</span><span class="sxs-lookup"><span data-stu-id="d3437-380">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="d3437-381">Acesso thread-safe</span><span class="sxs-lookup"><span data-stu-id="d3437-381">Thread safety</span></span>

<span data-ttu-id="d3437-382">Crie serviços singleton thread-safe.</span><span class="sxs-lookup"><span data-stu-id="d3437-382">Create thread-safe singleton services.</span></span> <span data-ttu-id="d3437-383">Se um serviço singleton tiver uma dependência em um serviço transitório, o serviço transitório também precisará ter acesso thread-safe, dependendo de como ele é usado pelo singleton.</span><span class="sxs-lookup"><span data-stu-id="d3437-383">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="d3437-384">O método de fábrica de um único serviço, como o segundo argumento para [addsingleton \<TService> (IServiceCollection, Func \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), não precisa ser thread-safe.</span><span class="sxs-lookup"><span data-stu-id="d3437-384">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="d3437-385">Como um construtor do tipo (`static`), ele tem garantia de ser chamado uma vez por um único thread.</span><span class="sxs-lookup"><span data-stu-id="d3437-385">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="d3437-386">Recomendações</span><span class="sxs-lookup"><span data-stu-id="d3437-386">Recommendations</span></span>

* <span data-ttu-id="d3437-387">A resolução de serviço baseada em `async/await` e `Task` não é compatível.</span><span class="sxs-lookup"><span data-stu-id="d3437-387">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="d3437-388">O C# não oferece suporte a construtores assíncronos.</span><span class="sxs-lookup"><span data-stu-id="d3437-388">C# does not support asynchronous constructors.</span></span> <span data-ttu-id="d3437-389">O padrão recomendado é usar métodos assíncronos após a resolução síncrona do serviço.</span><span class="sxs-lookup"><span data-stu-id="d3437-389">The recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>
* <span data-ttu-id="d3437-390">Evite armazenar dados e a configuração diretamente no contêiner do serviço.</span><span class="sxs-lookup"><span data-stu-id="d3437-390">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="d3437-391">Por exemplo, o carrinho de compras de um usuário normalmente não deve ser adicionado ao contêiner do serviço.</span><span class="sxs-lookup"><span data-stu-id="d3437-391">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="d3437-392">A configuração deve usar o [padrão de opções](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="d3437-392">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="d3437-393">Da mesma forma, evite objetos de "suporte de dados" que existem somente para permitir o acesso a outro objeto.</span><span class="sxs-lookup"><span data-stu-id="d3437-393">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="d3437-394">É melhor solicitar o item real por meio da DI.</span><span class="sxs-lookup"><span data-stu-id="d3437-394">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="d3437-395">Evite o acesso estático aos serviços.</span><span class="sxs-lookup"><span data-stu-id="d3437-395">Avoid static access to services.</span></span> <span data-ttu-id="d3437-396">Por exemplo, Evite digitar estaticamente [IApplicationBuilder. ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) para uso em outro lugar).</span><span class="sxs-lookup"><span data-stu-id="d3437-396">For example, avoid statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>
* <span data-ttu-id="d3437-397">Mantenha as fábricas de injeção rápida e síncrona.</span><span class="sxs-lookup"><span data-stu-id="d3437-397">Keep DI factories fast and synchronous.</span></span>
* <span data-ttu-id="d3437-398">Evite usar o *padrão do localizador de serviço*.</span><span class="sxs-lookup"><span data-stu-id="d3437-398">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="d3437-399">Por exemplo, não invoque <xref:System.IServiceProvider.GetService*> para obter uma instância de serviço quando for possível usar a DI:</span><span class="sxs-lookup"><span data-stu-id="d3437-399">For example, don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="d3437-400">**Incorreto:**</span><span class="sxs-lookup"><span data-stu-id="d3437-400">**Incorrect:**</span></span>

    ![Código incorreto](dependency-injection/_static/bad.png)

  <span data-ttu-id="d3437-402">**Correto**:</span><span class="sxs-lookup"><span data-stu-id="d3437-402">**Correct**:</span></span>

  ```csharp
  public class MyClass
  {
      private readonly IOptionsMonitor<MyOptions> _optionsMonitor;

      public MyClass(IOptionsMonitor<MyOptions> optionsMonitor)
      {
          _optionsMonitor = optionsMonitor;
      }

      public void MyMethod()
      {
          var option = _optionsMonitor.CurrentValue.Option;

          ...
      }
  }
  ```
* <span data-ttu-id="d3437-403">Outra variação de localizador de serviço a ser evitada é injetar um alocador que resolve as dependências em runtime.</span><span class="sxs-lookup"><span data-stu-id="d3437-403">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="d3437-404">Essas duas práticas misturam estratégias de [inversão de controle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).</span><span class="sxs-lookup"><span data-stu-id="d3437-404">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
* <span data-ttu-id="d3437-405">Evite o acesso estático a `HttpContext` (por exemplo, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="d3437-405">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<a name="ASP0000"></a>
* <span data-ttu-id="d3437-406">Evite chamadas para <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> no `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="d3437-406">Avoid calls to <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> in `ConfigureServices`.</span></span> <span data-ttu-id="d3437-407">Chamar `BuildServiceProvider` normalmente acontece quando o desenvolvedor deseja resolver um serviço no `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="d3437-407">Calling `BuildServiceProvider` typically happens when the developer wants to resolve a service in `ConfigureServices`.</span></span> <span data-ttu-id="d3437-408">Por exemplo, considere o caso em que você precisa obter a `LoginPath` configuração de.</span><span class="sxs-lookup"><span data-stu-id="d3437-408">For example, consider the case where you need go get the `LoginPath` from configuration.</span></span> <span data-ttu-id="d3437-409">Evite o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="d3437-409">Avoid the following code:</span></span>

  ![código inadequado chamando BuildServiceProvider](~/fundamentals/dependency-injection/_static/badcodeX.png)

  <span data-ttu-id="d3437-411">Na imagem anterior, selecionar a linha ondulada verde em `services.BuildServiceProvider` mostra o seguinte aviso de ASP0000:</span><span class="sxs-lookup"><span data-stu-id="d3437-411">In the preceding image, selecting the green wavy line under `services.BuildServiceProvider` shows the following ASP0000 warning:</span></span>
    * <span data-ttu-id="d3437-412">ASP0000 chamar ' BuildServiceProvider ' do código do aplicativo resulta em uma cópia adicional dos serviços singleton que estão sendo criados.</span><span class="sxs-lookup"><span data-stu-id="d3437-412">ASP0000 Calling 'BuildServiceProvider' from application code results in an additional copy of singleton services being created.</span></span> <span data-ttu-id="d3437-413">Considere alternativas como a injeção de dependência de serviços como parâmetros para ' Configurar '.</span><span class="sxs-lookup"><span data-stu-id="d3437-413">Consider alternatives such as dependency injecting services as parameters to 'Configure'.</span></span>

   <span data-ttu-id="d3437-414">`BuildServiceProvider`A chamada cria um segundo contêiner, que pode criar singletons rasgados e causar referências a grafos de objeto em vários contêineres.</span><span class="sxs-lookup"><span data-stu-id="d3437-414">Calling `BuildServiceProvider` creates a second container, which can create torn singletons and cause references to object graphs across multiple containers.</span></span> <span data-ttu-id="d3437-415">Uma maneira correta de `LoginPath` se obter é usar o padrão de opção com di:</span><span class="sxs-lookup"><span data-stu-id="d3437-415">A correct way to get `LoginPath` is using the option pattern with DI:</span></span>

  [!code-csharp[](dependency-injection/samples/3.x/AntiPattern3/Startup.cs?name=snippet)]

* <span data-ttu-id="d3437-416">Serviços inposados transitórios são capturados pelo contêiner para descarte.</span><span class="sxs-lookup"><span data-stu-id="d3437-416">Disposable transient services are captured by the container for disposal.</span></span> <span data-ttu-id="d3437-417">Isso pode transformar em um vazamento de memória se resolvido no contêiner de nível superior.</span><span class="sxs-lookup"><span data-stu-id="d3437-417">This can turn into a memory leak if resolved from the top level container.</span></span>
* <span data-ttu-id="d3437-418">Habilite a validação de escopo para garantir que o aplicativo não tenha serviços com escopo capturando singletons.</span><span class="sxs-lookup"><span data-stu-id="d3437-418">Enable scope validation to make sure the app doesn't have scoped services capturing singletons.</span></span> <span data-ttu-id="d3437-419">Para obter mais informações, confira [Validação de escopo](#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="d3437-419">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="d3437-420">Como todos os conjuntos de recomendações, talvez você encontre situações em que é necessário ignorar uma recomendação.</span><span class="sxs-lookup"><span data-stu-id="d3437-420">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="d3437-421">As exceções são raras, principalmente os casos especiais dentro da própria estrutura.</span><span class="sxs-lookup"><span data-stu-id="d3437-421">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="d3437-422">A DI é uma *alternativa* aos padrões de acesso a objeto estático/global.</span><span class="sxs-lookup"><span data-stu-id="d3437-422">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="d3437-423">Talvez você não obtenha os benefícios da DI se combiná-lo com o acesso a objeto estático.</span><span class="sxs-lookup"><span data-stu-id="d3437-423">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="d3437-424">Padrões recomendados para multilocação em DI</span><span class="sxs-lookup"><span data-stu-id="d3437-424">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="d3437-425">O [Orchard Core](https://github.com/OrchardCMS/OrchardCore) fornece multilocação.</span><span class="sxs-lookup"><span data-stu-id="d3437-425">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) provides multi-tenancy.</span></span> <span data-ttu-id="d3437-426">Para obter mais informações, consulte a [documentação do Orchard Core](https://docs.orchardcore.net/en/dev/).</span><span class="sxs-lookup"><span data-stu-id="d3437-426">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="d3437-427">Consulte os aplicativos de exemplos em https://github.com/OrchardCMS/OrchardCore.Samples para obter exemplos de como criar aplicativos modulares e multilocatários usando o Orchard Core Framework sem nenhum dos recursos específicos do CMS.</span><span class="sxs-lookup"><span data-stu-id="d3437-427">See the samples apps at https://github.com/OrchardCMS/OrchardCore.Samples for examples of how to build modular and multi-tenant apps using just Orchard Core Framework without any of the CMS specific features.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="d3437-428">Serviços fornecidos pela estrutura</span><span class="sxs-lookup"><span data-stu-id="d3437-428">Framework-provided services</span></span>

<span data-ttu-id="d3437-429">O `Startup.ConfigureServices` método é responsável por definir os serviços que o aplicativo usa, incluindo recursos de plataforma, como Entity Framework Core e ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="d3437-429">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="d3437-430">Inicialmente, o `IServiceCollection` fornecido para o `ConfigureServices` tem serviços definidos pela estrutura, dependendo de [como o host foi configurado](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="d3437-430">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="d3437-431">Aplicativos baseados em um ASP.NET Core modelos têm mais de 250 serviços registrados pela estrutura.</span><span class="sxs-lookup"><span data-stu-id="d3437-431">Apps based on an ASP.NET Core templates have more than 250 services registered by the framework.</span></span> <span data-ttu-id="d3437-432">Uma pequena amostra de serviços registrados na estrutura é listada na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="d3437-432">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="d3437-433">Tipo de Serviço</span><span class="sxs-lookup"><span data-stu-id="d3437-433">Service Type</span></span> | <span data-ttu-id="d3437-434">Tempo de vida</span><span class="sxs-lookup"><span data-stu-id="d3437-434">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="d3437-435">Transitório</span><span class="sxs-lookup"><span data-stu-id="d3437-435">Transient</span></span> |
| <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> | <span data-ttu-id="d3437-436">Singleton</span><span class="sxs-lookup"><span data-stu-id="d3437-436">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> | <span data-ttu-id="d3437-437">Singleton</span><span class="sxs-lookup"><span data-stu-id="d3437-437">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="d3437-438">Singleton</span><span class="sxs-lookup"><span data-stu-id="d3437-438">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="d3437-439">Transitório</span><span class="sxs-lookup"><span data-stu-id="d3437-439">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="d3437-440">Singleton</span><span class="sxs-lookup"><span data-stu-id="d3437-440">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="d3437-441">Transitório</span><span class="sxs-lookup"><span data-stu-id="d3437-441">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="d3437-442">Singleton</span><span class="sxs-lookup"><span data-stu-id="d3437-442">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="d3437-443">Singleton</span><span class="sxs-lookup"><span data-stu-id="d3437-443">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="d3437-444">Singleton</span><span class="sxs-lookup"><span data-stu-id="d3437-444">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="d3437-445">Transitório</span><span class="sxs-lookup"><span data-stu-id="d3437-445">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="d3437-446">Singleton</span><span class="sxs-lookup"><span data-stu-id="d3437-446">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="d3437-447">Singleton</span><span class="sxs-lookup"><span data-stu-id="d3437-447">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="d3437-448">Singleton</span><span class="sxs-lookup"><span data-stu-id="d3437-448">Singleton</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="d3437-449">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="d3437-449">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="d3437-450">Quatro maneiras de descartar IDisposables em ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d3437-450">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="d3437-451">Como gravar um código limpo no ASP.NET Core com injeção de dependência (MSDN)</span><span class="sxs-lookup"><span data-stu-id="d3437-451">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="d3437-452">Princípio de Dependências Explícitas</span><span class="sxs-lookup"><span data-stu-id="d3437-452">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="d3437-453">Inversão de Contêineres de Controle e o padrão de Injeção de Dependência (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="d3437-453">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="d3437-454">Como registrar um serviço com várias interfaces na DI do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d3437-454">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d3437-455">Por [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com)e [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="d3437-455">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="d3437-456">O ASP.NET Core é compatível com o padrão de design de software de DI (injeção de dependência), que é uma técnica para alcançar a [IoC (Inversão de Controle)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) entre classes e suas dependências.</span><span class="sxs-lookup"><span data-stu-id="d3437-456">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="d3437-457">Para obter mais informações específicas sobre injeção de dependência em controladores de MVC, consulte <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="d3437-457">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="d3437-458">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d3437-458">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="d3437-459">Visão geral da injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="d3437-459">Overview of dependency injection</span></span>

<span data-ttu-id="d3437-460">Uma *dependência* é qualquer objeto exigido por outro objeto.</span><span class="sxs-lookup"><span data-stu-id="d3437-460">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="d3437-461">Examine a classe `MyDependency` a seguir com um método `WriteMessage` do qual outras classes em um aplicativo dependem:</span><span class="sxs-lookup"><span data-stu-id="d3437-461">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

```csharp
public class MyDependency
{
    public MyDependency()
    {
    }

    public Task WriteMessage(string message)
    {
        Console.WriteLine(
            $"MyDependency.WriteMessage called. Message: {message}");

        return Task.FromResult(0);
    }
}
```

<span data-ttu-id="d3437-462">Uma instância da classe `MyDependency` pode ser criada para tornar o método `WriteMessage` disponível para uma classe.</span><span class="sxs-lookup"><span data-stu-id="d3437-462">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="d3437-463">A classe  é uma dependência da classe:</span><span class="sxs-lookup"><span data-stu-id="d3437-463">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

```csharp
public class IndexModel : PageModel
{
    MyDependency _dependency = new MyDependency();

    public async Task OnGetAsync()
    {
        await _dependency.WriteMessage(
            "IndexModel.OnGetAsync created this message.");
    }
}
```

<span data-ttu-id="d3437-464">A classe cria e depende diretamente da instância `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="d3437-464">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="d3437-465">As dependências de código (como no exemplo anterior) são problemáticas e devem ser evitadas por estes motivos:</span><span class="sxs-lookup"><span data-stu-id="d3437-465">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="d3437-466">Para substituir `MyDependency` por uma implementação diferente, a classe deve ser modificada.</span><span class="sxs-lookup"><span data-stu-id="d3437-466">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="d3437-467">Se `MyDependency` tiver dependências, elas deverão ser configuradas pela classe.</span><span class="sxs-lookup"><span data-stu-id="d3437-467">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="d3437-468">Em um projeto grande com várias classes dependendo da `MyDependency`, o código de configuração fica pulverizado por todo o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d3437-468">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="d3437-469">É difícil testar a unidade dessa implementação.</span><span class="sxs-lookup"><span data-stu-id="d3437-469">This implementation is difficult to unit test.</span></span> <span data-ttu-id="d3437-470">O aplicativo deve usar uma simulação ou stub da classe `MyDependency`, o que não é possível com essa abordagem.</span><span class="sxs-lookup"><span data-stu-id="d3437-470">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="d3437-471">Injeção de dependência trata desses problemas da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="d3437-471">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="d3437-472">O uso de uma interface ou classe base para abstrair a implementação da dependência.</span><span class="sxs-lookup"><span data-stu-id="d3437-472">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="d3437-473">Registrando a dependência em um contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="d3437-473">Registration of the dependency in a service container.</span></span> <span data-ttu-id="d3437-474">O ASP.NET Core fornece um contêiner de serviço interno, o <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="d3437-474">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="d3437-475">Os serviços são registrados no método `Startup.ConfigureServices` do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d3437-475">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="d3437-476">*Injeção* do serviço no construtor da classe na qual ele é usado.</span><span class="sxs-lookup"><span data-stu-id="d3437-476">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="d3437-477">A estrutura assume a responsabilidade de criar uma instância da dependência e de descartá-la quando não for mais necessária.</span><span class="sxs-lookup"><span data-stu-id="d3437-477">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="d3437-478">No [exemplo de aplicativo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), a interface `IMyDependency` define um método que o serviço fornece ao aplicativo:</span><span class="sxs-lookup"><span data-stu-id="d3437-478">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="d3437-479">Essa interface é implementada por um tipo concreto, `MyDependency`:</span><span class="sxs-lookup"><span data-stu-id="d3437-479">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="d3437-480">`MyDependency` solicita um <xref:Microsoft.Extensions.Logging.ILogger`1> em seu construtor.</span><span class="sxs-lookup"><span data-stu-id="d3437-480">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="d3437-481">Não é incomum usar a injeção de dependência de uma maneira encadeada.</span><span class="sxs-lookup"><span data-stu-id="d3437-481">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="d3437-482">Por sua vez, cada dependência solicitada solicita suas próprias dependências.</span><span class="sxs-lookup"><span data-stu-id="d3437-482">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="d3437-483">O contêiner resolve as dependências no grafo e retorna o serviço totalmente resolvido.</span><span class="sxs-lookup"><span data-stu-id="d3437-483">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="d3437-484">O conjunto de dependências que precisa ser resolvido normalmente é chamado de *árvore de dependência*, *grafo de dependência* ou *grafo de objeto*.</span><span class="sxs-lookup"><span data-stu-id="d3437-484">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="d3437-485">`IMyDependency` e `ILogger<TCategoryName>` devem ser registrados no contêiner do serviço.</span><span class="sxs-lookup"><span data-stu-id="d3437-485">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="d3437-486">`IMyDependency` está registrado em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="d3437-486">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d3437-487">`ILogger<TCategoryName>` é registrado pela infraestrutura de abstrações de registro em log, portanto, é um [serviço fornecido por estrutura](#framework-provided-services) registrado por padrão pela estrutura.</span><span class="sxs-lookup"><span data-stu-id="d3437-487">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="d3437-488">O contêiner resolve `ILogger<TCategoryName>` aproveitando os [tipos abertos (genéricos)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminando a necessidade de registrar todo [tipo construído (genérico)](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span><span class="sxs-lookup"><span data-stu-id="d3437-488">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="d3437-489">No exemplo de aplicativo, o serviço `IMyDependency` está registrado com o tipo concreto `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="d3437-489">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="d3437-490">O registro define o escopo do tempo de vida do serviço para o tempo de vida de uma única solicitação.</span><span class="sxs-lookup"><span data-stu-id="d3437-490">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="d3437-491">Descreveremos posteriormente neste tópico os [tempos de vida do serviço](#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="d3437-491">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="d3437-492">Cada método de extensão `services.Add{SERVICE_NAME}` adiciona (e possivelmente configura) serviços.</span><span class="sxs-lookup"><span data-stu-id="d3437-492">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="d3437-493">Por exemplo, `services.AddMvc()` adiciona as páginas de serviços Razor e a MVC necessária.</span><span class="sxs-lookup"><span data-stu-id="d3437-493">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="d3437-494">Recomendamos que os aplicativos sigam essa convenção.</span><span class="sxs-lookup"><span data-stu-id="d3437-494">We recommended that apps follow this convention.</span></span> <span data-ttu-id="d3437-495">Coloque os métodos de extensão no namespace [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) para encapsular grupos de registros de serviço.</span><span class="sxs-lookup"><span data-stu-id="d3437-495">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="d3437-496">Se o construtor do serviço exigir um [tipo interno](/dotnet/csharp/language-reference/keywords/built-in-types-table), como `string`, o tipo poderá ser injetado usando a [configuração](xref:fundamentals/configuration/index) ou o [padrão de opções](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="d3437-496">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

```csharp
public class MyDependency : IMyDependency
{
    public MyDependency(IConfiguration config)
    {
        var myStringValue = config["MyStringKey"];

        // Use myStringValue
    }

    ...
}
```

<span data-ttu-id="d3437-497">Uma instância do serviço é solicitada por meio do construtor de uma classe, onde o serviço é usado e atribuído a um campo particular.</span><span class="sxs-lookup"><span data-stu-id="d3437-497">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="d3437-498">O campo é usado para acessar o serviço conforme o necessário na classe.</span><span class="sxs-lookup"><span data-stu-id="d3437-498">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="d3437-499">No exemplo de aplicativo, a instância `IMyDependency` é solicitada e usada para chamar o método `WriteMessage` do serviço:</span><span class="sxs-lookup"><span data-stu-id="d3437-499">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="d3437-500">Serviços injetados na inicialização</span><span class="sxs-lookup"><span data-stu-id="d3437-500">Services injected into Startup</span></span>

<span data-ttu-id="d3437-501">Somente os seguintes tipos de serviço podem ser injetados no `Startup` Construtor ao usar o host genérico ( <xref:Microsoft.Extensions.Hosting.IHostBuilder> ):</span><span class="sxs-lookup"><span data-stu-id="d3437-501">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="d3437-502">Os serviços podem ser injetados em `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="d3437-502">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="d3437-503">Para obter mais informações, consulte <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="d3437-503">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="d3437-504">Serviços fornecidos pela estrutura</span><span class="sxs-lookup"><span data-stu-id="d3437-504">Framework-provided services</span></span>

<span data-ttu-id="d3437-505">O `Startup.ConfigureServices` método é responsável por definir os serviços que o aplicativo usa, incluindo recursos de plataforma, como Entity Framework Core e ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="d3437-505">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="d3437-506">Inicialmente, o `IServiceCollection` fornecido para o `ConfigureServices` tem serviços definidos pela estrutura, dependendo de [como o host foi configurado](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="d3437-506">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="d3437-507">Não é incomum um aplicativo baseado em um modelo de ASP.NET Core ter centenas de serviços registrados pela estrutura.</span><span class="sxs-lookup"><span data-stu-id="d3437-507">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="d3437-508">Uma pequena amostra de serviços registrados na estrutura é listada na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="d3437-508">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="d3437-509">Tipo de Serviço</span><span class="sxs-lookup"><span data-stu-id="d3437-509">Service Type</span></span> | <span data-ttu-id="d3437-510">Tempo de vida</span><span class="sxs-lookup"><span data-stu-id="d3437-510">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="d3437-511">Transitório</span><span class="sxs-lookup"><span data-stu-id="d3437-511">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="d3437-512">Singleton</span><span class="sxs-lookup"><span data-stu-id="d3437-512">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="d3437-513">Singleton</span><span class="sxs-lookup"><span data-stu-id="d3437-513">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="d3437-514">Singleton</span><span class="sxs-lookup"><span data-stu-id="d3437-514">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="d3437-515">Transitório</span><span class="sxs-lookup"><span data-stu-id="d3437-515">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="d3437-516">Singleton</span><span class="sxs-lookup"><span data-stu-id="d3437-516">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="d3437-517">Transitório</span><span class="sxs-lookup"><span data-stu-id="d3437-517">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="d3437-518">Singleton</span><span class="sxs-lookup"><span data-stu-id="d3437-518">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="d3437-519">Singleton</span><span class="sxs-lookup"><span data-stu-id="d3437-519">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="d3437-520">Singleton</span><span class="sxs-lookup"><span data-stu-id="d3437-520">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="d3437-521">Transitório</span><span class="sxs-lookup"><span data-stu-id="d3437-521">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="d3437-522">Singleton</span><span class="sxs-lookup"><span data-stu-id="d3437-522">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="d3437-523">Singleton</span><span class="sxs-lookup"><span data-stu-id="d3437-523">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="d3437-524">Singleton</span><span class="sxs-lookup"><span data-stu-id="d3437-524">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="d3437-525">Registrar serviços adicionais com métodos de extensão</span><span class="sxs-lookup"><span data-stu-id="d3437-525">Register additional services with extension methods</span></span>

<span data-ttu-id="d3437-526">Quando um método de extensão de coleta do serviço estiver disponível para registrar um serviço (e seus serviços dependentes, se for necessário), a convenção é usar um único método de extensão `Add{SERVICE_NAME}` para registrar todos os serviços exigidos por esse serviço.</span><span class="sxs-lookup"><span data-stu-id="d3437-526">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="d3437-527">O código a seguir é um exemplo de como adicionar serviços adicionais ao contêiner usando os métodos de extensão [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) e <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> :</span><span class="sxs-lookup"><span data-stu-id="d3437-527">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    ...
}
```

<span data-ttu-id="d3437-528">Para saber mais, confira a classe <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> na documentação da API.</span><span class="sxs-lookup"><span data-stu-id="d3437-528">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="d3437-529">Tempos de vida do serviço</span><span class="sxs-lookup"><span data-stu-id="d3437-529">Service lifetimes</span></span>

<span data-ttu-id="d3437-530">Escolha um tempo de vida apropriado para cada serviço registrado.</span><span class="sxs-lookup"><span data-stu-id="d3437-530">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="d3437-531">Os serviços do ASP.NET Core podem ser configurados com os seguintes tempos de vida:</span><span class="sxs-lookup"><span data-stu-id="d3437-531">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="d3437-532">Transitório</span><span class="sxs-lookup"><span data-stu-id="d3437-532">Transient</span></span>

<span data-ttu-id="d3437-533">Serviços temporários de tempo de vida (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) são criados cada vez que são solicitados pelo contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="d3437-533">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="d3437-534">Esse tempo de vida funciona melhor para serviços leves e sem estado.</span><span class="sxs-lookup"><span data-stu-id="d3437-534">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="d3437-535">Em aplicativos que processam solicitações, os serviços transitórios são descartados no final da solicitação.</span><span class="sxs-lookup"><span data-stu-id="d3437-535">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="d3437-536">Com escopo</span><span class="sxs-lookup"><span data-stu-id="d3437-536">Scoped</span></span>

<span data-ttu-id="d3437-537">Os serviços com tempo de vida (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) com escopo são criados uma vez por solicitação de cliente (conexão).</span><span class="sxs-lookup"><span data-stu-id="d3437-537">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="d3437-538">Em aplicativos que processam solicitações, os serviços com escopo são descartados no final da solicitação.</span><span class="sxs-lookup"><span data-stu-id="d3437-538">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

> [!WARNING]
> <span data-ttu-id="d3437-539">Ao usar um serviço com escopo em um middleware, injete o serviço no método `Invoke` ou `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="d3437-539">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="d3437-540">Não injetar via [injeção de Construtor](xref:mvc/controllers/dependency-injection#constructor-injection) porque força o serviço a se comportar como um singleton.</span><span class="sxs-lookup"><span data-stu-id="d3437-540">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="d3437-541">Para obter mais informações, consulte <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="d3437-541">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="d3437-542">Singleton</span><span class="sxs-lookup"><span data-stu-id="d3437-542">Singleton</span></span>

<span data-ttu-id="d3437-543">Serviços de tempo de vida singleton (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) são criados na primeira solicitação (ou quando `Startup.ConfigureServices` é executado e uma instância é especificada com o registro do serviço).</span><span class="sxs-lookup"><span data-stu-id="d3437-543">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="d3437-544">Cada solicitação subsequente usa a mesma instância.</span><span class="sxs-lookup"><span data-stu-id="d3437-544">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="d3437-545">Se o aplicativo exigir um comportamento de singleton, recomendamos permitir que o contêiner do serviço gerencie o tempo de vida do serviço.</span><span class="sxs-lookup"><span data-stu-id="d3437-545">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="d3437-546">Não implemente o padrão de design singleton e forneça o código de usuário para gerenciar o tempo de vida do objeto na classe.</span><span class="sxs-lookup"><span data-stu-id="d3437-546">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

<span data-ttu-id="d3437-547">Em aplicativos que processam solicitações, os serviços singleton são descartados quando o <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> é Descartado no desligamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d3437-547">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed at app shutdown.</span></span>

> [!WARNING]
> <span data-ttu-id="d3437-548">É perigoso resolver um serviço com escopo de um singleton.</span><span class="sxs-lookup"><span data-stu-id="d3437-548">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="d3437-549">Pode fazer com que o serviço tenha um estado incorreto durante o processamento das solicitações seguintes.</span><span class="sxs-lookup"><span data-stu-id="d3437-549">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="d3437-550">Métodos de registro do serviço</span><span class="sxs-lookup"><span data-stu-id="d3437-550">Service registration methods</span></span>

<span data-ttu-id="d3437-551">Os métodos de extensão de registro de serviço oferecem sobrecargas que são úteis em cenários específicos.</span><span class="sxs-lookup"><span data-stu-id="d3437-551">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="d3437-552">Método</span><span class="sxs-lookup"><span data-stu-id="d3437-552">Method</span></span> | <span data-ttu-id="d3437-553">Automático</span><span class="sxs-lookup"><span data-stu-id="d3437-553">Automatic</span></span><br><span data-ttu-id="d3437-554">object</span><span class="sxs-lookup"><span data-stu-id="d3437-554">object</span></span><br><span data-ttu-id="d3437-555">descarte</span><span class="sxs-lookup"><span data-stu-id="d3437-555">disposal</span></span> | <span data-ttu-id="d3437-556">Vários</span><span class="sxs-lookup"><span data-stu-id="d3437-556">Multiple</span></span><br><span data-ttu-id="d3437-557">implementações</span><span class="sxs-lookup"><span data-stu-id="d3437-557">implementations</span></span> | <span data-ttu-id="d3437-558">Passar argumentos</span><span class="sxs-lookup"><span data-stu-id="d3437-558">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="d3437-559">Exemplo:</span><span class="sxs-lookup"><span data-stu-id="d3437-559">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="d3437-560">Sim</span><span class="sxs-lookup"><span data-stu-id="d3437-560">Yes</span></span> | <span data-ttu-id="d3437-561">Sim</span><span class="sxs-lookup"><span data-stu-id="d3437-561">Yes</span></span> | <span data-ttu-id="d3437-562">Não</span><span class="sxs-lookup"><span data-stu-id="d3437-562">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="d3437-563">Exemplos:</span><span class="sxs-lookup"><span data-stu-id="d3437-563">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="d3437-564">Sim</span><span class="sxs-lookup"><span data-stu-id="d3437-564">Yes</span></span> | <span data-ttu-id="d3437-565">Sim</span><span class="sxs-lookup"><span data-stu-id="d3437-565">Yes</span></span> | <span data-ttu-id="d3437-566">Sim</span><span class="sxs-lookup"><span data-stu-id="d3437-566">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="d3437-567">Exemplo:</span><span class="sxs-lookup"><span data-stu-id="d3437-567">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="d3437-568">Sim</span><span class="sxs-lookup"><span data-stu-id="d3437-568">Yes</span></span> | <span data-ttu-id="d3437-569">Não</span><span class="sxs-lookup"><span data-stu-id="d3437-569">No</span></span> | <span data-ttu-id="d3437-570">Não</span><span class="sxs-lookup"><span data-stu-id="d3437-570">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="d3437-571">Exemplos:</span><span class="sxs-lookup"><span data-stu-id="d3437-571">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="d3437-572">Não</span><span class="sxs-lookup"><span data-stu-id="d3437-572">No</span></span> | <span data-ttu-id="d3437-573">Sim</span><span class="sxs-lookup"><span data-stu-id="d3437-573">Yes</span></span> | <span data-ttu-id="d3437-574">Sim</span><span class="sxs-lookup"><span data-stu-id="d3437-574">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="d3437-575">Exemplos:</span><span class="sxs-lookup"><span data-stu-id="d3437-575">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="d3437-576">Não</span><span class="sxs-lookup"><span data-stu-id="d3437-576">No</span></span> | <span data-ttu-id="d3437-577">Não</span><span class="sxs-lookup"><span data-stu-id="d3437-577">No</span></span> | <span data-ttu-id="d3437-578">Sim</span><span class="sxs-lookup"><span data-stu-id="d3437-578">Yes</span></span> |

<span data-ttu-id="d3437-579">Para obter mais informações sobre o descarte de tipos, consulte a seção [Descarte de serviços](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="d3437-579">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="d3437-580">Um cenário comum para várias implementações é a [simulação de tipos para teste](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="d3437-580">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="d3437-581">Métodos `TryAdd{LIFETIME}` somente registrarão o serviço se ainda não houver uma implementação registrada.</span><span class="sxs-lookup"><span data-stu-id="d3437-581">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="d3437-582">No exemplo a seguir, a primeira linha registra `MyDependency` para `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="d3437-582">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="d3437-583">A segunda linha não tem nenhum efeito porque `IMyDependency` já tem uma implementação registrada:</span><span class="sxs-lookup"><span data-stu-id="d3437-583">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="d3437-584">Para obter mais informações, consulte:</span><span class="sxs-lookup"><span data-stu-id="d3437-584">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="d3437-585">Métodos [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) apenas registrarão o serviço se ainda não houver uma implementação *do mesmo tipo*.</span><span class="sxs-lookup"><span data-stu-id="d3437-585">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="d3437-586">Vários serviços são resolvidos via `IEnumerable<{SERVICE}>`.</span><span class="sxs-lookup"><span data-stu-id="d3437-586">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="d3437-587">Ao registrar os serviços, só convém ao desenvolvedor adicionar uma instância se até o momento nenhuma do mesmo tipo foi adicionada.</span><span class="sxs-lookup"><span data-stu-id="d3437-587">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="d3437-588">Em geral, esse método é usado por autores de biblioteca para evitar registrar duas cópias de uma instância no contêiner.</span><span class="sxs-lookup"><span data-stu-id="d3437-588">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="d3437-589">No exemplo a seguir, a primeira linha registra `MyDep` para `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="d3437-589">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="d3437-590">A segunda linha registra `MyDep` para `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="d3437-590">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="d3437-591">A terceira linha não tem nenhum efeito porque `IMyDep1` já tem uma implementação registrada de `MyDep`:</span><span class="sxs-lookup"><span data-stu-id="d3437-591">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="d3437-592">Comportamento da injeção de construtor</span><span class="sxs-lookup"><span data-stu-id="d3437-592">Constructor injection behavior</span></span>

<span data-ttu-id="d3437-593">Os serviços podem ser resolvidos por dois mecanismos:</span><span class="sxs-lookup"><span data-stu-id="d3437-593">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="d3437-594"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permite a criação de objeto sem registro de serviço no contêiner de injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="d3437-594"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="d3437-595">`ActivatorUtilities` é usado com abstrações voltadas ao usuário, como Auxiliares de Marca, controladores MVC e associadores de modelo.</span><span class="sxs-lookup"><span data-stu-id="d3437-595">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="d3437-596">Os construtores podem aceitar argumentos que não são fornecidos pela injeção de dependência, mas que precisam atribuir valores padrão.</span><span class="sxs-lookup"><span data-stu-id="d3437-596">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="d3437-597">Quando os serviços são resolvidos pelo `IServiceProvider` ou `ActivatorUtilities` , a [injeção de Construtor](xref:mvc/controllers/dependency-injection#constructor-injection) requer um construtor *público* .</span><span class="sxs-lookup"><span data-stu-id="d3437-597">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="d3437-598">Quando os serviços são resolvidos pelo `ActivatorUtilities` , a [injeção de Construtor](xref:mvc/controllers/dependency-injection#constructor-injection) requer que apenas um Construtor aplicável exista.</span><span class="sxs-lookup"><span data-stu-id="d3437-598">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="d3437-599">Há suporte para sobrecargas de construtor, mas somente uma sobrecarga pode existir, cujos argumentos podem ser todos atendidos pela injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="d3437-599">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="d3437-600">Contextos de Entity Framework</span><span class="sxs-lookup"><span data-stu-id="d3437-600">Entity Framework contexts</span></span>

<span data-ttu-id="d3437-601">Contextos do Entity Framework geralmente são adicionados ao contêiner de serviço usando o [tempo de vida com escopo](#service-lifetimes), pois o escopo de operações de banco de dados de aplicativo Web normalmente é para a solicitação do cliente.</span><span class="sxs-lookup"><span data-stu-id="d3437-601">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="d3437-602">O tempo de vida padrão será definido como escopo se um tempo de vida não for especificado por uma sobrecarga [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) ao registrar o contexto do banco de dados.</span><span class="sxs-lookup"><span data-stu-id="d3437-602">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="d3437-603">Serviços com um determinado tempo de vida não devem usar um contexto de banco de dados com um tempo de vida mais curto que aquele do serviço.</span><span class="sxs-lookup"><span data-stu-id="d3437-603">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="d3437-604">Opções de tempo de vida e de registro</span><span class="sxs-lookup"><span data-stu-id="d3437-604">Lifetime and registration options</span></span>

<span data-ttu-id="d3437-605">Para demonstrar a diferença entre as opções de tempo de vida e de registro, considere as interfaces a seguir, que representam tarefas como uma operação com um identificador exclusivo, `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="d3437-605">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="d3437-606">Dependendo de como o tempo de vida de um serviço de operações é configurado para as interfaces a seguir, o contêiner fornece a mesma instância do serviço, ou outra diferente, quando recebe uma solicitação de uma classe:</span><span class="sxs-lookup"><span data-stu-id="d3437-606">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="d3437-607">As interfaces são implementadas na classe `Operation`.</span><span class="sxs-lookup"><span data-stu-id="d3437-607">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="d3437-608">O construtor `Operation` gera um GUID, caso um não seja fornecido:</span><span class="sxs-lookup"><span data-stu-id="d3437-608">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="d3437-609">Há um `OperationService` registrado que depende de cada um dos outros `Operation` tipos.</span><span class="sxs-lookup"><span data-stu-id="d3437-609">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="d3437-610">Quando `OperationService` é solicitado por meio da injeção de dependência, ele recebe a uma nova instância de cada serviço, ou uma instância existente com base no tempo de vida do serviço dependente.</span><span class="sxs-lookup"><span data-stu-id="d3437-610">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="d3437-611">Quando os serviços temporários forem criados mediante solicitação do contêiner, o `OperationId` do serviço `IOperationTransient` será diferente do `OperationId` do `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="d3437-611">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="d3437-612">`OperationService` recebe uma nova instância da classe `IOperationTransient`.</span><span class="sxs-lookup"><span data-stu-id="d3437-612">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="d3437-613">A nova instância produz outro `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="d3437-613">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="d3437-614">Quando os serviços com escopo forem criados por solicitação do cliente, o `OperationId` do serviço `IOperationScoped` será o mesmo que o `OperationService` dentro de uma solicitação do cliente.</span><span class="sxs-lookup"><span data-stu-id="d3437-614">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="d3437-615">Em todas as solicitações do cliente, os dois serviços compartilham um valor de `OperationId` diferente.</span><span class="sxs-lookup"><span data-stu-id="d3437-615">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="d3437-616">Quando os serviços singleton e de instância singleton forem criados uma vez e usados em todas as solicitações de cliente e em todos os serviços, o `OperationId` será constante entre todas as solicitações de serviço.</span><span class="sxs-lookup"><span data-stu-id="d3437-616">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="d3437-617">Em `Startup.ConfigureServices`, cada tipo é adicionado ao contêiner de acordo com seu tempo de vida nomeado:</span><span class="sxs-lookup"><span data-stu-id="d3437-617">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="d3437-618">O serviço `IOperationSingletonInstance` está usando uma instância específica com uma ID conhecida de `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="d3437-618">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="d3437-619">Fica claro quando esse tipo está em uso (seu GUID contém apenas zeros).</span><span class="sxs-lookup"><span data-stu-id="d3437-619">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="d3437-620">O exemplo de aplicativo demonstra os tempos de vida do objeto dentro e entre solicitações individuais.</span><span class="sxs-lookup"><span data-stu-id="d3437-620">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="d3437-621">O exemplo de aplicativo `IndexModel` solicita cada tipo `IOperation` e o `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="d3437-621">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="d3437-622">Depois, a página exibe todos os valores de `OperationId` da classe de modelo de página e do serviço por meio de atribuições de propriedade:</span><span class="sxs-lookup"><span data-stu-id="d3437-622">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="d3437-623">As duas saídas a seguir mostram os resultados das duas solicitações:</span><span class="sxs-lookup"><span data-stu-id="d3437-623">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="d3437-624">**Primeira solicitação:**</span><span class="sxs-lookup"><span data-stu-id="d3437-624">**First request:**</span></span>

<span data-ttu-id="d3437-625">Operações do controlador:</span><span class="sxs-lookup"><span data-stu-id="d3437-625">Controller operations:</span></span>

<span data-ttu-id="d3437-626">Transitória: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="d3437-626">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="d3437-627">Com escopo: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="d3437-627">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="d3437-628">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="d3437-628">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="d3437-629">Instância: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="d3437-629">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="d3437-630">`OperationService` operações:</span><span class="sxs-lookup"><span data-stu-id="d3437-630">`OperationService` operations:</span></span>

<span data-ttu-id="d3437-631">Transitória: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="d3437-631">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="d3437-632">Com escopo: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="d3437-632">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="d3437-633">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="d3437-633">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="d3437-634">Instância: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="d3437-634">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="d3437-635">**Segunda solicitação:**</span><span class="sxs-lookup"><span data-stu-id="d3437-635">**Second request:**</span></span>

<span data-ttu-id="d3437-636">Operações do controlador:</span><span class="sxs-lookup"><span data-stu-id="d3437-636">Controller operations:</span></span>

<span data-ttu-id="d3437-637">Transitória: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="d3437-637">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="d3437-638">Com escopo: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="d3437-638">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="d3437-639">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="d3437-639">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="d3437-640">Instância: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="d3437-640">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="d3437-641">`OperationService` operações:</span><span class="sxs-lookup"><span data-stu-id="d3437-641">`OperationService` operations:</span></span>

<span data-ttu-id="d3437-642">Transitória: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="d3437-642">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="d3437-643">Com escopo: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="d3437-643">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="d3437-644">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="d3437-644">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="d3437-645">Instância: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="d3437-645">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="d3437-646">Observe qual dos valores de `OperationId` varia em uma solicitação, e entre as solicitações:</span><span class="sxs-lookup"><span data-stu-id="d3437-646">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="d3437-647">Os objetos *transitórios* sempre são diferentes.</span><span class="sxs-lookup"><span data-stu-id="d3437-647">*Transient* objects are always different.</span></span> <span data-ttu-id="d3437-648">O valor `OperationId` transitório da primeira e da segunda solicitações de cliente é diferente para as duas operações `OperationService` e em todas as solicitações de cliente.</span><span class="sxs-lookup"><span data-stu-id="d3437-648">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="d3437-649">Uma nova instância é fornecida para cada solicitação de serviço e solicitação de cliente.</span><span class="sxs-lookup"><span data-stu-id="d3437-649">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="d3437-650">Objetos *com escopo* são os mesmos em uma solicitação de cliente, mas diferentes entre solicitações de cliente.</span><span class="sxs-lookup"><span data-stu-id="d3437-650">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="d3437-651">Os pbjetos *singleton* são os mesmos para cada objeto e solicitação, independentemente de uma instância `Operation` ser fornecida em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="d3437-651">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="d3437-652">Chamar os serviços desde o principal</span><span class="sxs-lookup"><span data-stu-id="d3437-652">Call services from main</span></span>

<span data-ttu-id="d3437-653">Crie um <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> com [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) para resolver um serviço com escopo dentro do escopo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d3437-653">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="d3437-654">Essa abordagem é útil para acessar um serviço com escopo durante a inicialização para executar tarefas de inicialização.</span><span class="sxs-lookup"><span data-stu-id="d3437-654">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="d3437-655">O exemplo a seguir mostra como obter um contexto para o `MyScopedService` em `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="d3437-655">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;

public class Program
{
    public static async Task Main(string[] args)
    {
        var host = CreateWebHostBuilder(args).Build();

        using (var serviceScope = host.Services.CreateScope())
        {
            var services = serviceScope.ServiceProvider;

            try
            {
                var serviceContext = services.GetRequiredService<MyScopedService>();
                // Use the context here
            }
            catch (Exception ex)
            {
                var logger = services.GetRequiredService<ILogger<Program>>();
                logger.LogError(ex, "An error occurred.");
            }
        }
    
        await host.RunAsync();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseStartup<Startup>();
}
```

## <a name="scope-validation"></a><span data-ttu-id="d3437-656">Validação de escopo</span><span class="sxs-lookup"><span data-stu-id="d3437-656">Scope validation</span></span>

<span data-ttu-id="d3437-657">Quando o aplicativo está em execução no ambiente de desenvolvimento, o provedor de serviço padrão executa verificações para saber se:</span><span class="sxs-lookup"><span data-stu-id="d3437-657">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="d3437-658">Os serviços com escopo não são resolvidos direta ou indiretamente pelo provedor de serviço raiz.</span><span class="sxs-lookup"><span data-stu-id="d3437-658">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="d3437-659">Os serviços com escopo não são injetados direta ou indiretamente em singletons.</span><span class="sxs-lookup"><span data-stu-id="d3437-659">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="d3437-660">O provedor de serviços raiz é criado quando <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> é chamado.</span><span class="sxs-lookup"><span data-stu-id="d3437-660">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="d3437-661">O tempo de vida do provedor de serviço raiz corresponde ao tempo de vida do aplicativo/servidor quando o provedor começa com o aplicativo e é descartado quando o aplicativo é desligado.</span><span class="sxs-lookup"><span data-stu-id="d3437-661">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="d3437-662">Os serviços com escopo são descartados pelo contêiner que os criou.</span><span class="sxs-lookup"><span data-stu-id="d3437-662">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="d3437-663">Se um serviço com escopo é criado no contêiner raiz, o tempo de vida do serviço é promovido efetivamente para singleton, porque ele só é descartado pelo contêiner raiz quando o aplicativo/servidor é desligado.</span><span class="sxs-lookup"><span data-stu-id="d3437-663">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="d3437-664">A validação dos escopos de serviço detecta essas situações quando `BuildServiceProvider` é chamado.</span><span class="sxs-lookup"><span data-stu-id="d3437-664">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="d3437-665">Para obter mais informações, consulte <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="d3437-665">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>   

## <a name="request-services"></a><span data-ttu-id="d3437-666">Serviços de solicitação</span><span class="sxs-lookup"><span data-stu-id="d3437-666">Request Services</span></span>

<span data-ttu-id="d3437-667">Os serviços disponíveis em uma solicitação do ASP.NET de `HttpContext` são expostos por meio da coleção [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).</span><span class="sxs-lookup"><span data-stu-id="d3437-667">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="d3437-668">Os Serviços de Solicitação representam os serviços configurados e solicitados como parte do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d3437-668">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="d3437-669">Quando os objetos especificam dependências, elas são atendidas pelos tipos encontrados em `RequestServices`, não em `ApplicationServices`.</span><span class="sxs-lookup"><span data-stu-id="d3437-669">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="d3437-670">Em geral, o aplicativo não deve usar essas propriedades diretamente.</span><span class="sxs-lookup"><span data-stu-id="d3437-670">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="d3437-671">Em vez disso, solicite os tipos exigidos pelas classes por meio de construtores de classe e permita que a estrutura injete as dependências.</span><span class="sxs-lookup"><span data-stu-id="d3437-671">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="d3437-672">Isso resulta em classes que são mais fáceis de testar.</span><span class="sxs-lookup"><span data-stu-id="d3437-672">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="d3437-673">Prefira solicitar dependências como parâmetros de construtor para acessar a coleção `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="d3437-673">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="d3437-674">Projetar serviços para injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="d3437-674">Design services for dependency injection</span></span>

<span data-ttu-id="d3437-675">As melhores práticas são:</span><span class="sxs-lookup"><span data-stu-id="d3437-675">Best practices are to:</span></span>

* <span data-ttu-id="d3437-676">Projetar serviços para usar a injeção de dependência a fim de obter suas dependências.</span><span class="sxs-lookup"><span data-stu-id="d3437-676">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="d3437-677">Evite classes e membros com estado e estáticos.</span><span class="sxs-lookup"><span data-stu-id="d3437-677">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="d3437-678">Crie aplicativos para usar os serviços singleton, o que evita a criação de estado global.</span><span class="sxs-lookup"><span data-stu-id="d3437-678">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="d3437-679">Evite a instanciação direta das classes dependentes em serviços.</span><span class="sxs-lookup"><span data-stu-id="d3437-679">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="d3437-680">A instanciação direta acopla o código a uma implementação específica.</span><span class="sxs-lookup"><span data-stu-id="d3437-680">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="d3437-681">Verifique as classes de aplicativo pequenas, bem fatoradas e facilmente testadas.</span><span class="sxs-lookup"><span data-stu-id="d3437-681">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="d3437-682">Se parecer que uma classe tem muitas dependências injetadas, normalmente é um sinal de que a classe tem muitas responsabilidades e está violando o [Princípio da Responsabilidade Única (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="d3437-682">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="d3437-683">Tente refatorar a classe movendo algumas de suas responsabilidades para uma nova classe.</span><span class="sxs-lookup"><span data-stu-id="d3437-683">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="d3437-684">Tenha em mente que as classes Razor de modelo de página de páginas e as classes do controlador MVC devem se concentrar nas preocupações da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="d3437-684">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="d3437-685">Os detalhes de implementação de acesso aos dados e as regras de negócios devem ser mantidos em classes apropriadas a esses [interesses separados](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="d3437-685">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="d3437-686">Descarte de serviços</span><span class="sxs-lookup"><span data-stu-id="d3437-686">Disposal of services</span></span>

<span data-ttu-id="d3437-687">O contêiner chama <xref:System.IDisposable.Dispose*> para os tipos <xref:System.IDisposable> criados por ele.</span><span class="sxs-lookup"><span data-stu-id="d3437-687">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="d3437-688">Se uma instância for adicionada ao contêiner pelo código do usuário, ele não será descartado automaticamente.</span><span class="sxs-lookup"><span data-stu-id="d3437-688">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="d3437-689">No exemplo a seguir, os serviços são criados pelo contêiner de serviço e descartados automaticamente:</span><span class="sxs-lookup"><span data-stu-id="d3437-689">In the following example, the services are created by the service container and disposed automatically:</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public interface IService3 {}
public class Service3 : IService3, IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddScoped<Service1>();
    services.AddSingleton<Service2>();
    services.AddSingleton<IService3>(sp => new Service3());
}
```

<span data-ttu-id="d3437-690">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="d3437-690">In the following example:</span></span>

* <span data-ttu-id="d3437-691">As instâncias de serviço não são criadas pelo contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="d3437-691">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="d3437-692">Os tempos de vida de serviço pretendidos não são conhecidos pela estrutura.</span><span class="sxs-lookup"><span data-stu-id="d3437-692">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="d3437-693">A estrutura não descarta os serviços automaticamente.</span><span class="sxs-lookup"><span data-stu-id="d3437-693">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="d3437-694">Se os serviços não forem explicitamente descartados no código do desenvolvedor, eles persistirão até que o aplicativo seja desligado.</span><span class="sxs-lookup"><span data-stu-id="d3437-694">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="d3437-695">Diretrizes de IDisposable para instâncias transitórias e compartilhadas</span><span class="sxs-lookup"><span data-stu-id="d3437-695">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="d3437-696">Tempo de vida transitório, limitado</span><span class="sxs-lookup"><span data-stu-id="d3437-696">Transient, limited lifetime</span></span>

<span data-ttu-id="d3437-697">**Cenário**</span><span class="sxs-lookup"><span data-stu-id="d3437-697">**Scenario**</span></span>

<span data-ttu-id="d3437-698">O aplicativo requer uma <xref:System.IDisposable> instância com um tempo de vida transitório para qualquer um dos seguintes cenários:</span><span class="sxs-lookup"><span data-stu-id="d3437-698">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="d3437-699">A instância é resolvida no escopo raiz.</span><span class="sxs-lookup"><span data-stu-id="d3437-699">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="d3437-700">A instância deve ser descartada antes do término do escopo.</span><span class="sxs-lookup"><span data-stu-id="d3437-700">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="d3437-701">**Solução**</span><span class="sxs-lookup"><span data-stu-id="d3437-701">**Solution**</span></span>

<span data-ttu-id="d3437-702">Use o padrão de fábrica para criar uma instância fora do escopo pai.</span><span class="sxs-lookup"><span data-stu-id="d3437-702">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="d3437-703">Nessa situação, o aplicativo geralmente teria um `Create` método que chamasse o construtor do tipo final diretamente.</span><span class="sxs-lookup"><span data-stu-id="d3437-703">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="d3437-704">Se o tipo final tiver outras dependências, a fábrica poderá:</span><span class="sxs-lookup"><span data-stu-id="d3437-704">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="d3437-705">Receber um <xref:System.IServiceProvider> em seu construtor.</span><span class="sxs-lookup"><span data-stu-id="d3437-705">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="d3437-706">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> para instanciar a instância fora do contêiner, ao usar o contêiner para suas dependências.</span><span class="sxs-lookup"><span data-stu-id="d3437-706">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="d3437-707">Instância compartilhada, tempo de vida limitado</span><span class="sxs-lookup"><span data-stu-id="d3437-707">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="d3437-708">**Cenário**</span><span class="sxs-lookup"><span data-stu-id="d3437-708">**Scenario**</span></span>

<span data-ttu-id="d3437-709">O aplicativo requer uma <xref:System.IDisposable> instância compartilhada entre vários serviços, mas o <xref:System.IDisposable> deve ter um tempo de vida limitado.</span><span class="sxs-lookup"><span data-stu-id="d3437-709">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="d3437-710">**Solução**</span><span class="sxs-lookup"><span data-stu-id="d3437-710">**Solution**</span></span>

<span data-ttu-id="d3437-711">Registre a instância com um tempo de vida de escopo.</span><span class="sxs-lookup"><span data-stu-id="d3437-711">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="d3437-712">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> para iniciar e criar um novo <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> .</span><span class="sxs-lookup"><span data-stu-id="d3437-712">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="d3437-713">Use o escopo <xref:System.IServiceProvider> para obter os serviços necessários.</span><span class="sxs-lookup"><span data-stu-id="d3437-713">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="d3437-714">Descarte o escopo quando o tempo de vida deve terminar.</span><span class="sxs-lookup"><span data-stu-id="d3437-714">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="d3437-715">Diretrizes gerais</span><span class="sxs-lookup"><span data-stu-id="d3437-715">General Guidelines</span></span>

* <span data-ttu-id="d3437-716">Não registre <xref:System.IDisposable> instâncias com um escopo transitório.</span><span class="sxs-lookup"><span data-stu-id="d3437-716">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="d3437-717">Em vez disso, use o padrão de fábrica.</span><span class="sxs-lookup"><span data-stu-id="d3437-717">Use the factory pattern instead.</span></span>
* <span data-ttu-id="d3437-718">Não resolva instâncias transitórias ou com escopo definido <xref:System.IDisposable> no escopo raiz.</span><span class="sxs-lookup"><span data-stu-id="d3437-718">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="d3437-719">A única exceção geral é quando o aplicativo cria/recria e descarta o <xref:System.IServiceProvider> , que não é um padrão ideal.</span><span class="sxs-lookup"><span data-stu-id="d3437-719">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="d3437-720">O recebimento de uma <xref:System.IDisposable> dependência por meio de di não exige que o receptor se implemente <xref:System.IDisposable> .</span><span class="sxs-lookup"><span data-stu-id="d3437-720">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="d3437-721">O receptor da <xref:System.IDisposable> dependência não deve chamar <xref:System.IDisposable.Dispose%2A> essa dependência.</span><span class="sxs-lookup"><span data-stu-id="d3437-721">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="d3437-722">Os escopos devem ser usados para controlar o tempo de vida dos serviços.</span><span class="sxs-lookup"><span data-stu-id="d3437-722">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="d3437-723">Os escopos não são hierárquicos e não há nenhuma conexão especial entre escopos.</span><span class="sxs-lookup"><span data-stu-id="d3437-723">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="d3437-724">Substituição do contêiner de serviço padrão</span><span class="sxs-lookup"><span data-stu-id="d3437-724">Default service container replacement</span></span>

<span data-ttu-id="d3437-725">O contêiner de serviço interno foi projetado para atender às necessidades da estrutura e da maioria dos aplicativos de consumidor.</span><span class="sxs-lookup"><span data-stu-id="d3437-725">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="d3437-726">É recomendável usar o contêiner interno, a menos que você precise de um recurso específico ao qual o contêiner interno não ofereça suporte, como:</span><span class="sxs-lookup"><span data-stu-id="d3437-726">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="d3437-727">Injeção de propriedade</span><span class="sxs-lookup"><span data-stu-id="d3437-727">Property injection</span></span>
* <span data-ttu-id="d3437-728">Injeção com base no nome</span><span class="sxs-lookup"><span data-stu-id="d3437-728">Injection based on name</span></span>
* <span data-ttu-id="d3437-729">Contêineres filho</span><span class="sxs-lookup"><span data-stu-id="d3437-729">Child containers</span></span>
* <span data-ttu-id="d3437-730">Gerenciamento de tempo de vida personalizado</span><span class="sxs-lookup"><span data-stu-id="d3437-730">Custom lifetime management</span></span>
* <span data-ttu-id="d3437-731">Suporte de `Func<T>` para inicialização lenta</span><span class="sxs-lookup"><span data-stu-id="d3437-731">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="d3437-732">Registro baseado em Convenção</span><span class="sxs-lookup"><span data-stu-id="d3437-732">Convention-based registration</span></span>

<span data-ttu-id="d3437-733">Os seguintes contêineres de terceiros podem ser usados com aplicativos ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="d3437-733">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="d3437-734">Autofac</span><span class="sxs-lookup"><span data-stu-id="d3437-734">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="d3437-735">DryIoc</span><span class="sxs-lookup"><span data-stu-id="d3437-735">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="d3437-736">Grace</span><span class="sxs-lookup"><span data-stu-id="d3437-736">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="d3437-737">LightInject</span><span class="sxs-lookup"><span data-stu-id="d3437-737">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="d3437-738">Lamar</span><span class="sxs-lookup"><span data-stu-id="d3437-738">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="d3437-739">Stashbox</span><span class="sxs-lookup"><span data-stu-id="d3437-739">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="d3437-740">Unity</span><span class="sxs-lookup"><span data-stu-id="d3437-740">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="d3437-741">Acesso thread-safe</span><span class="sxs-lookup"><span data-stu-id="d3437-741">Thread safety</span></span>

<span data-ttu-id="d3437-742">Crie serviços singleton thread-safe.</span><span class="sxs-lookup"><span data-stu-id="d3437-742">Create thread-safe singleton services.</span></span> <span data-ttu-id="d3437-743">Se um serviço singleton tiver uma dependência em um serviço transitório, o serviço transitório também precisará ter acesso thread-safe, dependendo de como ele é usado pelo singleton.</span><span class="sxs-lookup"><span data-stu-id="d3437-743">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="d3437-744">O método de fábrica de um único serviço, como o segundo argumento para [addsingleton \<TService> (IServiceCollection, Func \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), não precisa ser thread-safe.</span><span class="sxs-lookup"><span data-stu-id="d3437-744">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="d3437-745">Como um construtor do tipo (`static`), ele tem garantia de ser chamado uma vez por um único thread.</span><span class="sxs-lookup"><span data-stu-id="d3437-745">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="d3437-746">Recomendações</span><span class="sxs-lookup"><span data-stu-id="d3437-746">Recommendations</span></span>

* <span data-ttu-id="d3437-747">A resolução de serviço baseada em `async/await` e `Task` não é compatível.</span><span class="sxs-lookup"><span data-stu-id="d3437-747">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="d3437-748">O C# não é compatível com os construtores assíncronos. Portanto, o padrão recomendado é usar os métodos assíncronos após a resolução síncrona do serviço.</span><span class="sxs-lookup"><span data-stu-id="d3437-748">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="d3437-749">Evite armazenar dados e a configuração diretamente no contêiner do serviço.</span><span class="sxs-lookup"><span data-stu-id="d3437-749">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="d3437-750">Por exemplo, o carrinho de compras de um usuário normalmente não deve ser adicionado ao contêiner do serviço.</span><span class="sxs-lookup"><span data-stu-id="d3437-750">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="d3437-751">A configuração deve usar o [padrão de opções](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="d3437-751">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="d3437-752">Da mesma forma, evite objetos de "suporte de dados" que existem somente para permitir o acesso a outro objeto.</span><span class="sxs-lookup"><span data-stu-id="d3437-752">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="d3437-753">É melhor solicitar o item real por meio da DI.</span><span class="sxs-lookup"><span data-stu-id="d3437-753">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="d3437-754">Evite o acesso estático aos serviços.</span><span class="sxs-lookup"><span data-stu-id="d3437-754">Avoid static access to services.</span></span> <span data-ttu-id="d3437-755">Por exemplo, Evite digitar estaticamente [IApplicationBuilder. ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) para uso em outro lugar.</span><span class="sxs-lookup"><span data-stu-id="d3437-755">For example, avoid statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere.</span></span>

* <span data-ttu-id="d3437-756">Evite usar o *padrão de localizador de serviço*, que combina a [inversão de estratégias de controle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) .</span><span class="sxs-lookup"><span data-stu-id="d3437-756">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
  * <span data-ttu-id="d3437-757">Não invoque <xref:System.IServiceProvider.GetService*> para obter uma instância de serviço quando você pode usar di em vez disso:</span><span class="sxs-lookup"><span data-stu-id="d3437-757">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="d3437-758">**Incorreto:**</span><span class="sxs-lookup"><span data-stu-id="d3437-758">**Incorrect:**</span></span>

    ```csharp
    public class MyClass()
   
      public void MyMethod()
      {
          var optionsMonitor = 
              _services.GetService<IOptionsMonitor<MyOptions>>();
          var option = optionsMonitor.CurrentValue.Option;
   
          ...
      }
      ```
   
    <span data-ttu-id="d3437-759">**Correto**:</span><span class="sxs-lookup"><span data-stu-id="d3437-759">**Correct**:</span></span>

    ```csharp
    public class MyClass
    {
        private readonly IOptionsMonitor<MyOptions> _optionsMonitor;

        public MyClass(IOptionsMonitor<MyOptions> optionsMonitor)
        {
            _optionsMonitor = optionsMonitor;
        }

        public void MyMethod()
        {
            var option = _optionsMonitor.CurrentValue.Option;

            ...
        }
    }
    ```

* <span data-ttu-id="d3437-760">Evite injetar uma fábrica que resolva dependências em tempo de execução usando <xref:System.IServiceProvider.GetService*> .</span><span class="sxs-lookup"><span data-stu-id="d3437-760">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>
* <span data-ttu-id="d3437-761">Evite o acesso estático a `HttpContext` (por exemplo, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="d3437-761">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="d3437-762">Como todos os conjuntos de recomendações, talvez você encontre situações em que é necessário ignorar uma recomendação.</span><span class="sxs-lookup"><span data-stu-id="d3437-762">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="d3437-763">As exceções são raras, principalmente os casos especiais dentro da própria estrutura.</span><span class="sxs-lookup"><span data-stu-id="d3437-763">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="d3437-764">A DI é uma *alternativa* aos padrões de acesso a objeto estático/global.</span><span class="sxs-lookup"><span data-stu-id="d3437-764">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="d3437-765">Talvez você não obtenha os benefícios da DI se combiná-lo com o acesso a objeto estático.</span><span class="sxs-lookup"><span data-stu-id="d3437-765">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d3437-766">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="d3437-766">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="d3437-767">Quatro maneiras de descartar IDisposables em ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d3437-767">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="d3437-768">Como gravar um código limpo no ASP.NET Core com injeção de dependência (MSDN)</span><span class="sxs-lookup"><span data-stu-id="d3437-768">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="d3437-769">Princípio de Dependências Explícitas</span><span class="sxs-lookup"><span data-stu-id="d3437-769">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="d3437-770">Inversão de Contêineres de Controle e o padrão de Injeção de Dependência (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="d3437-770">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="d3437-771">Como registrar um serviço com várias interfaces na DI do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d3437-771">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end
