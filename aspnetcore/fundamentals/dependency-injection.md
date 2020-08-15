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
ms.openlocfilehash: b0ba7c7598df13413c00934a30e03681129de98a
ms.sourcegitcommit: 503b348e9046fcd969de85898394a1ea8274ec38
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88227573"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="0cc63-103">Injeção de dependência no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0cc63-103">Dependency injection in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0cc63-104">Por [Kirk Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com)e [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="0cc63-104">By [Kirk Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="0cc63-105">O ASP.NET Core é compatível com o padrão de design de software de DI (injeção de dependência), que é uma técnica para alcançar a [IoC (Inversão de Controle)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) entre classes e suas dependências.</span><span class="sxs-lookup"><span data-stu-id="0cc63-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="0cc63-106">Para obter mais informações específicas sobre injeção de dependência em controladores de MVC, consulte <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="0cc63-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="0cc63-107">Para obter mais informações sobre injeção de dependência de opções, consulte <xref:fundamentals/configuration/options> .</span><span class="sxs-lookup"><span data-stu-id="0cc63-107">For more information on dependency injection of options, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="0cc63-108">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0cc63-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="0cc63-109">Visão geral da injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="0cc63-109">Overview of dependency injection</span></span>

<span data-ttu-id="0cc63-110">Uma *dependência* é qualquer objeto exigido por outro objeto.</span><span class="sxs-lookup"><span data-stu-id="0cc63-110">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="0cc63-111">Examine a classe `MyDependency` a seguir com um método `WriteMessage` do qual outras classes em um aplicativo dependem:</span><span class="sxs-lookup"><span data-stu-id="0cc63-111">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="0cc63-112">Uma instância da classe `MyDependency` pode ser criada para tornar o método `WriteMessage` disponível para uma classe.</span><span class="sxs-lookup"><span data-stu-id="0cc63-112">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="0cc63-113">A classe  é uma dependência da classe:</span><span class="sxs-lookup"><span data-stu-id="0cc63-113">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="0cc63-114">A classe cria e depende diretamente da instância `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="0cc63-114">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="0cc63-115">As dependências de código, como o exemplo anterior, são problemáticas e devem ser evitadas pelos seguintes motivos:</span><span class="sxs-lookup"><span data-stu-id="0cc63-115">Code dependencies, such as the previous example, are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="0cc63-116">Para substituir `MyDependency` por uma implementação diferente, a classe deve ser modificada.</span><span class="sxs-lookup"><span data-stu-id="0cc63-116">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="0cc63-117">Se `MyDependency` tiver dependências, elas deverão ser configuradas pela classe.</span><span class="sxs-lookup"><span data-stu-id="0cc63-117">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="0cc63-118">Em um projeto grande com várias classes dependendo da `MyDependency`, o código de configuração fica pulverizado por todo o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0cc63-118">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="0cc63-119">É difícil testar a unidade dessa implementação.</span><span class="sxs-lookup"><span data-stu-id="0cc63-119">This implementation is difficult to unit test.</span></span> <span data-ttu-id="0cc63-120">O aplicativo deve usar uma simulação ou stub da classe `MyDependency`, o que não é possível com essa abordagem.</span><span class="sxs-lookup"><span data-stu-id="0cc63-120">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="0cc63-121">Injeção de dependência trata desses problemas da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="0cc63-121">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="0cc63-122">O uso de uma interface ou classe base para abstrair a implementação da dependência.</span><span class="sxs-lookup"><span data-stu-id="0cc63-122">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="0cc63-123">Registrando a dependência em um contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="0cc63-123">Registration of the dependency in a service container.</span></span> <span data-ttu-id="0cc63-124">O ASP.NET Core fornece um contêiner de serviço interno, o <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="0cc63-124">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="0cc63-125">Os serviços são registrados no método `Startup.ConfigureServices` do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0cc63-125">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="0cc63-126">*Injeção* do serviço no construtor da classe na qual ele é usado.</span><span class="sxs-lookup"><span data-stu-id="0cc63-126">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="0cc63-127">A estrutura assume a responsabilidade de criar uma instância da dependência e de descartá-la quando não for mais necessária.</span><span class="sxs-lookup"><span data-stu-id="0cc63-127">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="0cc63-128">No [exemplo de aplicativo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), a interface `IMyDependency` define um método que o serviço fornece ao aplicativo:</span><span class="sxs-lookup"><span data-stu-id="0cc63-128">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="0cc63-129">Essa interface é implementada por um tipo concreto, `MyDependency`:</span><span class="sxs-lookup"><span data-stu-id="0cc63-129">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="0cc63-130">No exemplo de aplicativo, o serviço `IMyDependency` está registrado com o tipo concreto `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="0cc63-130">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="0cc63-131">O <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> método registra o serviço com um tempo de vida de escopo, o tempo de vida de uma única solicitação.</span><span class="sxs-lookup"><span data-stu-id="0cc63-131">The <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> method registers the service with a scoped lifetime, the lifetime of a single request.</span></span> <span data-ttu-id="0cc63-132">Descreveremos posteriormente neste tópico os [tempos de vida do serviço](#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="0cc63-132">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency.cs?name=snippet1)]

<span data-ttu-id="0cc63-133">No exemplo de aplicativo, a instância `IMyDependency` é solicitada e usada para chamar o método `WriteMessage` do serviço:</span><span class="sxs-lookup"><span data-stu-id="0cc63-133">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index2.cshtml.cs?name=snippet1)]

<span data-ttu-id="0cc63-134">Com o padrão de DI:</span><span class="sxs-lookup"><span data-stu-id="0cc63-134">With the DI pattern:</span></span>

* <span data-ttu-id="0cc63-135">O controlador não usa o tipo concreto `MyDependency` , apenas a interface `IMyDependency` .</span><span class="sxs-lookup"><span data-stu-id="0cc63-135">The controller doesn't use the concrete type `MyDependency`, only the interface `IMyDependency`.</span></span> <span data-ttu-id="0cc63-136">Isso facilita a alteração da implementação que o controlador usa sem modificar o controlador.</span><span class="sxs-lookup"><span data-stu-id="0cc63-136">That makes it easy to change the implementation that the controller uses without modifying the controller.</span></span>
* <span data-ttu-id="0cc63-137">O controlador não cria uma instância do `MyDependency` , ele é criado pelo contêiner de di.</span><span class="sxs-lookup"><span data-stu-id="0cc63-137">The controller doesn't create an instance of `MyDependency`, it's created by the DI container.</span></span>

<span data-ttu-id="0cc63-138">A implementação da `IMyDependency` interface pode ser aprimorada usando a API de registro em log interna:</span><span class="sxs-lookup"><span data-stu-id="0cc63-138">The implementation of the `IMyDependency` interface can be improved by using the built-in logging API:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet2)]

<span data-ttu-id="0cc63-139">O `ConfigureServices` método atualizado registra a nova `IMyDependency` implementação:</span><span class="sxs-lookup"><span data-stu-id="0cc63-139">The updated `ConfigureServices` method registers the new `IMyDependency` implementation:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency2.cs?name=snippet1)]

<span data-ttu-id="0cc63-140">`MyDependency2` solicita um <xref:Microsoft.Extensions.Logging.ILogger`1> no construtor.</span><span class="sxs-lookup"><span data-stu-id="0cc63-140">`MyDependency2` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in the constructor.</span></span> <span data-ttu-id="0cc63-141">Não é incomum usar a injeção de dependência de uma maneira encadeada.</span><span class="sxs-lookup"><span data-stu-id="0cc63-141">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="0cc63-142">Por sua vez, cada dependência solicitada solicita suas próprias dependências.</span><span class="sxs-lookup"><span data-stu-id="0cc63-142">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="0cc63-143">O contêiner resolve as dependências no grafo e retorna o serviço totalmente resolvido.</span><span class="sxs-lookup"><span data-stu-id="0cc63-143">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="0cc63-144">O conjunto de dependências que precisa ser resolvido normalmente é chamado de *árvore de dependência*, *grafo de dependência* ou *grafo de objeto*.</span><span class="sxs-lookup"><span data-stu-id="0cc63-144">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="0cc63-145">`ILogger<TCategoryName>` é um [serviço fornecido pelo Framework](#framework-provided-services).</span><span class="sxs-lookup"><span data-stu-id="0cc63-145">`ILogger<TCategoryName>` is a [framework-provided service](#framework-provided-services).</span></span>

<span data-ttu-id="0cc63-146">O contêiner resolve aproveitando `ILogger<TCategoryName>` os [tipos abertos (genéricos)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminando a necessidade de registrar cada [tipo construído (genérico)](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span><span class="sxs-lookup"><span data-stu-id="0cc63-146">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span></span>

<span data-ttu-id="0cc63-147">Na terminologia de injeção de dependência, um serviço:</span><span class="sxs-lookup"><span data-stu-id="0cc63-147">In dependency injection terminology, a service:</span></span>

* <span data-ttu-id="0cc63-148">Normalmente é um objeto que fornece um serviço para outro código no aplicativo, como o `IMyDependency` serviço.</span><span class="sxs-lookup"><span data-stu-id="0cc63-148">Is typically an object that provides a service to other code in the app, such as the `IMyDependency` service.</span></span>
* <span data-ttu-id="0cc63-149">O não está relacionado a um serviço Web, embora o serviço possa usar um serviço Web.</span><span class="sxs-lookup"><span data-stu-id="0cc63-149">Is not related to a web service, although the service may use a web service.</span></span>

<span data-ttu-id="0cc63-150">A estrutura fornece um sistema de [registro em log](xref:fundamentals/logging/index) robusto.</span><span class="sxs-lookup"><span data-stu-id="0cc63-150">The framework provides a robust [logging](xref:fundamentals/logging/index) system.</span></span> <span data-ttu-id="0cc63-151">As `IMyDependency` implementações foram escritas para demonstrar a di básica, não para implementar o registro em log.</span><span class="sxs-lookup"><span data-stu-id="0cc63-151">The `IMyDependency` implementations were written to demonstrate basic DI, not to implement logging.</span></span> <span data-ttu-id="0cc63-152">A maioria dos aplicativos não deve precisar gravar agentes.</span><span class="sxs-lookup"><span data-stu-id="0cc63-152">Most apps shouldn't need to write loggers.</span></span> <span data-ttu-id="0cc63-153">O código a seguir demonstra como usar o log padrão, que não exige que nenhum serviço seja registrado no `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="0cc63-153">The following code demonstrates using the default logging, which doesn't require any services to be registered in `ConfigureServices`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/About.cshtml.cs?name=snippet)]

<span data-ttu-id="0cc63-154">Usando o código anterior, não há necessidade de atualizar `ConfigureServices` porque o [registro em log](xref:fundamentals/logging/index) é fornecido pela estrutura:</span><span class="sxs-lookup"><span data-stu-id="0cc63-154">Using the preceding code, there is no need to update `ConfigureServices` because [logging](xref:fundamentals/logging/index) is provided by the framework:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages();
}
```

## <a name="services-injected-into-startup"></a><span data-ttu-id="0cc63-155">Serviços injetados na inicialização</span><span class="sxs-lookup"><span data-stu-id="0cc63-155">Services injected into Startup</span></span>

<span data-ttu-id="0cc63-156">Somente os seguintes tipos de serviço podem ser injetados no `Startup` Construtor ao usar o host genérico ( <xref:Microsoft.Extensions.Hosting.IHostBuilder> ):</span><span class="sxs-lookup"><span data-stu-id="0cc63-156">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="0cc63-157">Os serviços podem ser injetados em `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="0cc63-157">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="0cc63-158">Para obter mais informações, consulte <xref:fundamentals/startup> e [acessar a configuração na inicialização](xref:fundamentals/configuration/index#access-configuration-in-startup).</span><span class="sxs-lookup"><span data-stu-id="0cc63-158">For more information, see <xref:fundamentals/startup> and [Access configuration in Startup](xref:fundamentals/configuration/index#access-configuration-in-startup).</span></span>

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="0cc63-159">Registrar serviços adicionais com métodos de extensão</span><span class="sxs-lookup"><span data-stu-id="0cc63-159">Register additional services with extension methods</span></span>

<span data-ttu-id="0cc63-160">Quando um método de extensão da coleção de serviços está disponível para registrar um serviço:</span><span class="sxs-lookup"><span data-stu-id="0cc63-160">When a service collection extension method is available to register a service:</span></span>

* <span data-ttu-id="0cc63-161">A Convenção é usar um método de `Add{SERVICE_NAME}` extensão único para registrar todos os serviços exigidos por esse serviço.</span><span class="sxs-lookup"><span data-stu-id="0cc63-161">The convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span>
* <span data-ttu-id="0cc63-162">Os serviços dependentes também são registrados.</span><span class="sxs-lookup"><span data-stu-id="0cc63-162">The dependent services are also registered.</span></span>

<span data-ttu-id="0cc63-163">O código a seguir é gerado pelo Razor modelo de páginas usando contas de usuário individuais e mostra como adicionar serviços adicionais ao contêiner usando os métodos de extensão <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> e <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A> :</span><span class="sxs-lookup"><span data-stu-id="0cc63-163">The following code is generated by the Razor Pages template using individual user accounts and shows how to add additional services to the container using the extension methods <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A>:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupEF.cs?name=snippet)]

<span data-ttu-id="0cc63-164">Para obter mais informações, consulte <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> e <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="0cc63-164">For more information, see <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> and <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="0cc63-165">Consulte a seção [combinando a coleta de serviços](#csc) para obter instruções sobre como escrever um método de extensão para registrar serviços.</span><span class="sxs-lookup"><span data-stu-id="0cc63-165">See the section [Combining service collection](#csc) for instructions on writing an extension method to register services.</span></span>

[!INCLUDE[](~/includes/combine-di.md)]

## <a name="service-lifetimes"></a><span data-ttu-id="0cc63-166">Tempos de vida do serviço</span><span class="sxs-lookup"><span data-stu-id="0cc63-166">Service lifetimes</span></span>

<span data-ttu-id="0cc63-167">Escolha um tempo de vida apropriado para cada serviço registrado.</span><span class="sxs-lookup"><span data-stu-id="0cc63-167">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="0cc63-168">Os serviços do ASP.NET Core podem ser configurados com os seguintes tempos de vida:</span><span class="sxs-lookup"><span data-stu-id="0cc63-168">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="0cc63-169">Transitório</span><span class="sxs-lookup"><span data-stu-id="0cc63-169">Transient</span></span>

<span data-ttu-id="0cc63-170">Serviços temporários de tempo de vida (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) são criados cada vez que são solicitados pelo contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="0cc63-170">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="0cc63-171">Esse tempo de vida funciona melhor para serviços leves e sem estado.</span><span class="sxs-lookup"><span data-stu-id="0cc63-171">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="0cc63-172">Em aplicativos que processam solicitações, os serviços transitórios são descartados no final da solicitação.</span><span class="sxs-lookup"><span data-stu-id="0cc63-172">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="0cc63-173">Com escopo</span><span class="sxs-lookup"><span data-stu-id="0cc63-173">Scoped</span></span>

<span data-ttu-id="0cc63-174">Os serviços com tempo de vida (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) com escopo são criados uma vez por solicitação de cliente (conexão).</span><span class="sxs-lookup"><span data-stu-id="0cc63-174">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="0cc63-175">Ao usar Entity Framework Core, o <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> método de extensão registra os `DbContext` tipos com um tempo de vida com escopo definido por padrão.</span><span class="sxs-lookup"><span data-stu-id="0cc63-175">When using Entity Framework Core, the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension method registers `DbContext` types with a scoped lifetime by default.</span></span>

<span data-ttu-id="0cc63-176">Em aplicativos que processam solicitações, os serviços com escopo são descartados no final da solicitação.</span><span class="sxs-lookup"><span data-stu-id="0cc63-176">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

<span data-ttu-id="0cc63-177">Use serviços com escopo no middleware com uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="0cc63-177">Use scoped services in middleware with one of the following approaches:</span></span>

* <span data-ttu-id="0cc63-178">Injetar o serviço no `Invoke` `InvokeAsync` método ou.</span><span class="sxs-lookup"><span data-stu-id="0cc63-178">Inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="0cc63-179">Injetar por [injeção de Construtor](xref:mvc/controllers/dependency-injection#constructor-injection) gera uma exceção em tempo de execução porque força o serviço a se comportar como um singleton.</span><span class="sxs-lookup"><span data-stu-id="0cc63-179">Injecting by [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) throws an exception at run time because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="0cc63-180">O exemplo nas [Opções de tempo de vida e de registro](#lifetime-and-registration-options) usa a `InvokeAsync` abordagem.</span><span class="sxs-lookup"><span data-stu-id="0cc63-180">The sample in the [Lifetime and registration options](#lifetime-and-registration-options) uses the `InvokeAsync` approach.</span></span>
* <span data-ttu-id="0cc63-181">[Middleware baseado em fábrica](<xref:fundamentals/middleware/extensibility>).</span><span class="sxs-lookup"><span data-stu-id="0cc63-181">[Factory-based middleware](<xref:fundamentals/middleware/extensibility>).</span></span> <span data-ttu-id="0cc63-182">Os métodos de extensão <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> verificam se o tipo registrado de um middleware implementa <xref:Microsoft.AspNetCore.Http.IMiddleware>.</span><span class="sxs-lookup"><span data-stu-id="0cc63-182"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> extension methods check if a middleware's registered type implements <xref:Microsoft.AspNetCore.Http.IMiddleware>.</span></span> <span data-ttu-id="0cc63-183">Se isso acontecer, a instância <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> registrada no contêiner será usada para resolver a implementação <xref:Microsoft.AspNetCore.Http.IMiddleware> em vez de usar a lógica de ativação de middleware baseada em convenção.</span><span class="sxs-lookup"><span data-stu-id="0cc63-183">If it does, the <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> instance registered in the container is used to resolve the <xref:Microsoft.AspNetCore.Http.IMiddleware> implementation instead of using the convention-based middleware activation logic.</span></span> <span data-ttu-id="0cc63-184">O middleware é registrado como um serviço com escopo ou transitório no contêiner de serviço do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0cc63-184">The middleware is registered as a scoped or transient service in the app's service container.</span></span>

<span data-ttu-id="0cc63-185">Para obter mais informações, consulte <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="0cc63-185">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

<span data-ttu-id="0cc63-186">***Não*** resolva um serviço com escopo de um singleton.</span><span class="sxs-lookup"><span data-stu-id="0cc63-186">Do ***not*** resolve a scoped service from a singleton.</span></span> <span data-ttu-id="0cc63-187">Pode fazer com que o serviço tenha um estado incorreto durante o processamento das solicitações seguintes.</span><span class="sxs-lookup"><span data-stu-id="0cc63-187">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="0cc63-188">Não há problema em:</span><span class="sxs-lookup"><span data-stu-id="0cc63-188">It's fine to:</span></span>

* <span data-ttu-id="0cc63-189">Resolva um serviço singleton de um serviço com escopo ou transitório.</span><span class="sxs-lookup"><span data-stu-id="0cc63-189">Resolve a singleton service from a scoped or transient service.</span></span>
* <span data-ttu-id="0cc63-190">Resolva um serviço com escopo de outro serviço com escopo ou transitório.</span><span class="sxs-lookup"><span data-stu-id="0cc63-190">Resolve a scoped service from another scoped or transient service.</span></span>

<span data-ttu-id="0cc63-191">Por padrão, no ambiente de desenvolvimento, a resolução de um serviço de outro serviço com tempo de vida maior gera uma exceção.</span><span class="sxs-lookup"><span data-stu-id="0cc63-191">By default, in the development environment, resolving a service from another service with longer lifetime throws an exception.</span></span> <span data-ttu-id="0cc63-192">Para obter mais informações, confira [Validação de escopo](#sv).</span><span class="sxs-lookup"><span data-stu-id="0cc63-192">For more information, see [Scope validation](#sv).</span></span>

### <a name="singleton"></a><span data-ttu-id="0cc63-193">Singleton</span><span class="sxs-lookup"><span data-stu-id="0cc63-193">Singleton</span></span>

<span data-ttu-id="0cc63-194">Os serviços de vida útil singleton ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*> ) são criados:</span><span class="sxs-lookup"><span data-stu-id="0cc63-194">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created either:</span></span>

* <span data-ttu-id="0cc63-195">Na primeira vez que forem solicitadas.</span><span class="sxs-lookup"><span data-stu-id="0cc63-195">The first time they're requested.</span></span>
* <span data-ttu-id="0cc63-196">Pelo desenvolvedor, ao fornecer uma instância de implementação diretamente para o contêiner.</span><span class="sxs-lookup"><span data-stu-id="0cc63-196">By the developer, when providing an implementation instance directly to the container.</span></span> <span data-ttu-id="0cc63-197">Essa abordagem raramente é necessária.</span><span class="sxs-lookup"><span data-stu-id="0cc63-197">This approach is rarely needed.</span></span>

<span data-ttu-id="0cc63-198">Cada solicitação subsequente usa a mesma instância.</span><span class="sxs-lookup"><span data-stu-id="0cc63-198">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="0cc63-199">Se o aplicativo exigir um comportamento singleton, permita que o contêiner de serviço gerencie o tempo de vida do serviço.</span><span class="sxs-lookup"><span data-stu-id="0cc63-199">If the app requires singleton behavior, allow the service container to manage the service's lifetime.</span></span> <span data-ttu-id="0cc63-200">Não implemente o padrão de design singleton e forneça código para descartar o singleton.</span><span class="sxs-lookup"><span data-stu-id="0cc63-200">Don't implement the singleton design pattern and provide code to dispose of the singleton.</span></span> <span data-ttu-id="0cc63-201">Os serviços nunca devem ser descartados pelo código que resolveu o serviço de um contêiner.</span><span class="sxs-lookup"><span data-stu-id="0cc63-201">Services should never be disposed by code that resolved the service from a container.</span></span> <span data-ttu-id="0cc63-202">Se um tipo ou fábrica for registrado como um singleton, o contêiner descartará o singleton automaticamente.</span><span class="sxs-lookup"><span data-stu-id="0cc63-202">If a type or factory is registered as a singleton, the container will dispose the singleton automatically.</span></span>

<span data-ttu-id="0cc63-203">Os serviços singleton devem ser thread-safe e geralmente são usados em serviços sem estado.</span><span class="sxs-lookup"><span data-stu-id="0cc63-203">Singleton services must be thread safe and are often used in stateless services.</span></span>

<span data-ttu-id="0cc63-204">Em aplicativos que processam solicitações, os serviços singleton são descartados quando o <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> é Descartado no desligamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0cc63-204">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed on application shutdown.</span></span> <span data-ttu-id="0cc63-205">Como a memória não é liberada até que o aplicativo seja desligado, o uso de memória com um singleton deve ser considerado.</span><span class="sxs-lookup"><span data-stu-id="0cc63-205">Because memory is not released until the app is shut down, memory use with a singleton must be considered.</span></span>

> [!WARNING]
> <span data-ttu-id="0cc63-206">***Não*** resolva um serviço com escopo de um singleton.</span><span class="sxs-lookup"><span data-stu-id="0cc63-206">Do ***not*** resolve a scoped service from a singleton.</span></span> <span data-ttu-id="0cc63-207">Pode fazer com que o serviço tenha um estado incorreto durante o processamento das solicitações seguintes.</span><span class="sxs-lookup"><span data-stu-id="0cc63-207">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="0cc63-208">É bom resolver um serviço singleton de um serviço com escopo ou transitório.</span><span class="sxs-lookup"><span data-stu-id="0cc63-208">It's fine to resolve a singleton service from a scoped or transient service.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="0cc63-209">Métodos de registro do serviço</span><span class="sxs-lookup"><span data-stu-id="0cc63-209">Service registration methods</span></span>

<span data-ttu-id="0cc63-210">Os métodos de extensão de registro de serviço oferecem sobrecargas que são úteis em cenários específicos.</span><span class="sxs-lookup"><span data-stu-id="0cc63-210">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>
<!-- Review: Auto disposal at end of app lifetime is not what you think of auto disposal  -->

| <span data-ttu-id="0cc63-211">Método</span><span class="sxs-lookup"><span data-stu-id="0cc63-211">Method</span></span> | <span data-ttu-id="0cc63-212">Automático</span><span class="sxs-lookup"><span data-stu-id="0cc63-212">Automatic</span></span><br><span data-ttu-id="0cc63-213">object</span><span class="sxs-lookup"><span data-stu-id="0cc63-213">object</span></span><br><span data-ttu-id="0cc63-214">descarte</span><span class="sxs-lookup"><span data-stu-id="0cc63-214">disposal</span></span> | <span data-ttu-id="0cc63-215">Vários</span><span class="sxs-lookup"><span data-stu-id="0cc63-215">Multiple</span></span><br><span data-ttu-id="0cc63-216">implementações</span><span class="sxs-lookup"><span data-stu-id="0cc63-216">implementations</span></span> | <span data-ttu-id="0cc63-217">Passar argumentos</span><span class="sxs-lookup"><span data-stu-id="0cc63-217">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="0cc63-218">Exemplo:</span><span class="sxs-lookup"><span data-stu-id="0cc63-218">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="0cc63-219">Sim</span><span class="sxs-lookup"><span data-stu-id="0cc63-219">Yes</span></span> | <span data-ttu-id="0cc63-220">Sim</span><span class="sxs-lookup"><span data-stu-id="0cc63-220">Yes</span></span> | <span data-ttu-id="0cc63-221">Não</span><span class="sxs-lookup"><span data-stu-id="0cc63-221">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="0cc63-222">Exemplos:</span><span class="sxs-lookup"><span data-stu-id="0cc63-222">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep(99));` | <span data-ttu-id="0cc63-223">Sim</span><span class="sxs-lookup"><span data-stu-id="0cc63-223">Yes</span></span> | <span data-ttu-id="0cc63-224">Sim</span><span class="sxs-lookup"><span data-stu-id="0cc63-224">Yes</span></span> | <span data-ttu-id="0cc63-225">Sim</span><span class="sxs-lookup"><span data-stu-id="0cc63-225">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="0cc63-226">Exemplo:</span><span class="sxs-lookup"><span data-stu-id="0cc63-226">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="0cc63-227">Sim</span><span class="sxs-lookup"><span data-stu-id="0cc63-227">Yes</span></span> | <span data-ttu-id="0cc63-228">Não</span><span class="sxs-lookup"><span data-stu-id="0cc63-228">No</span></span> | <span data-ttu-id="0cc63-229">Não</span><span class="sxs-lookup"><span data-stu-id="0cc63-229">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="0cc63-230">Exemplos:</span><span class="sxs-lookup"><span data-stu-id="0cc63-230">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep(99));` | <span data-ttu-id="0cc63-231">Não</span><span class="sxs-lookup"><span data-stu-id="0cc63-231">No</span></span> | <span data-ttu-id="0cc63-232">Sim</span><span class="sxs-lookup"><span data-stu-id="0cc63-232">Yes</span></span> | <span data-ttu-id="0cc63-233">Sim</span><span class="sxs-lookup"><span data-stu-id="0cc63-233">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="0cc63-234">Exemplos:</span><span class="sxs-lookup"><span data-stu-id="0cc63-234">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep(99));` | <span data-ttu-id="0cc63-235">Não</span><span class="sxs-lookup"><span data-stu-id="0cc63-235">No</span></span> | <span data-ttu-id="0cc63-236">Não</span><span class="sxs-lookup"><span data-stu-id="0cc63-236">No</span></span> | <span data-ttu-id="0cc63-237">Sim</span><span class="sxs-lookup"><span data-stu-id="0cc63-237">Yes</span></span> |

<span data-ttu-id="0cc63-238">Para obter mais informações sobre o descarte de tipos, consulte a seção [Descarte de serviços](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="0cc63-238">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="0cc63-239">Um cenário comum para várias implementações é a [simulação de tipos para teste](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="0cc63-239">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="0cc63-240">`TryAdd{LIFETIME}` os métodos registram o serviço se já não houver uma implementação registrada.</span><span class="sxs-lookup"><span data-stu-id="0cc63-240">`TryAdd{LIFETIME}` methods register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="0cc63-241">No exemplo a seguir, a primeira linha registra `MyDependency` para `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="0cc63-241">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="0cc63-242">A segunda linha não tem nenhum efeito porque `IMyDependency` já tem uma implementação registrada:</span><span class="sxs-lookup"><span data-stu-id="0cc63-242">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="0cc63-243">Para obter mais informações, consulte:</span><span class="sxs-lookup"><span data-stu-id="0cc63-243">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="0cc63-244">Os métodos [TryAddEnumerable (Service Descriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) registram o serviço se ainda não houver uma implementação *do mesmo tipo*.</span><span class="sxs-lookup"><span data-stu-id="0cc63-244">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="0cc63-245">Vários serviços são resolvidos via `IEnumerable<{SERVICE}>`.</span><span class="sxs-lookup"><span data-stu-id="0cc63-245">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="0cc63-246">Ao registrar serviços, o desenvolvedor deve adicionar uma instância se um do mesmo tipo ainda não tiver sido adicionado.</span><span class="sxs-lookup"><span data-stu-id="0cc63-246">When registering services, the developer should add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="0cc63-247">Em geral, os autores `TryAddEnumerable` de biblioteca usam para evitar o registro de várias cópias de uma implementação no contêiner.</span><span class="sxs-lookup"><span data-stu-id="0cc63-247">Generally, library authors use `TryAddEnumerable` to avoid registering multiple copies of an implementation in the container.</span></span>

<span data-ttu-id="0cc63-248">No exemplo a seguir, a primeira linha registra `MyDep` para `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="0cc63-248">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="0cc63-249">A segunda linha registra `MyDep` para `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="0cc63-249">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="0cc63-250">A terceira linha não tem nenhum efeito porque `IMyDep1` já tem uma implementação registrada de `MyDep`:</span><span class="sxs-lookup"><span data-stu-id="0cc63-250">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

<span data-ttu-id="0cc63-251">O registro de serviço geralmente é independente de ordem, exceto ao registrar várias implementações do mesmo tipo.</span><span class="sxs-lookup"><span data-stu-id="0cc63-251">Service registration is generally order independent except when registering multiple implementations of the same type.</span></span>

<span data-ttu-id="0cc63-252">`IServiceCollection` é uma coleção de <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> .</span><span class="sxs-lookup"><span data-stu-id="0cc63-252">`IServiceCollection` is a collection of <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>.</span></span> <span data-ttu-id="0cc63-253">O código a seguir mostra como adicionar um serviço com um construtor:</span><span class="sxs-lookup"><span data-stu-id="0cc63-253">The following code shows how to add a service with a constructor:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup5.cs?name=snippet)]

<span data-ttu-id="0cc63-254">Os `Add{LIFETIME}` métodos usam a mesma abordagem.</span><span class="sxs-lookup"><span data-stu-id="0cc63-254">The `Add{LIFETIME}` methods use the same approach.</span></span> <span data-ttu-id="0cc63-255">Por exemplo, consulte o [código-fonte para Addscoped](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237).</span><span class="sxs-lookup"><span data-stu-id="0cc63-255">For example, see the [source code to AddScoped](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237).</span></span>

### <a name="constructor-injection-behavior"></a><span data-ttu-id="0cc63-256">Comportamento da injeção de construtor</span><span class="sxs-lookup"><span data-stu-id="0cc63-256">Constructor injection behavior</span></span>

<span data-ttu-id="0cc63-257">Os serviços podem ser resolvidos por dois mecanismos:</span><span class="sxs-lookup"><span data-stu-id="0cc63-257">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="0cc63-258"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span><span class="sxs-lookup"><span data-stu-id="0cc63-258"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span></span>
  * <span data-ttu-id="0cc63-259">Cria objetos sem registro de serviço no contêiner de injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="0cc63-259">Creates objects without service registration in the dependency injection container.</span></span>
  * <span data-ttu-id="0cc63-260">Usado com recursos de estrutura, como [auxiliares de marca](xref:mvc/views/tag-helpers/intro), controladores MVC e [ASSOCIADORES de modelo](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="0cc63-260">Used with framework features, such as [Tag Helpers](xref:mvc/views/tag-helpers/intro), MVC controllers, and [model binders](xref:mvc/models/model-binding).</span></span>

<span data-ttu-id="0cc63-261">Os construtores podem aceitar argumentos que não são fornecidos pela injeção de dependência, mas que precisam atribuir valores padrão.</span><span class="sxs-lookup"><span data-stu-id="0cc63-261">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="0cc63-262">Quando os serviços são resolvidos pelo `IServiceProvider` ou `ActivatorUtilities` , a [injeção de Construtor](xref:mvc/controllers/dependency-injection#constructor-injection) requer um construtor *público* .</span><span class="sxs-lookup"><span data-stu-id="0cc63-262">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="0cc63-263">Quando os serviços são resolvidos pelo `ActivatorUtilities` , a [injeção de Construtor](xref:mvc/controllers/dependency-injection#constructor-injection) requer que apenas um Construtor aplicável exista.</span><span class="sxs-lookup"><span data-stu-id="0cc63-263">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="0cc63-264">Há suporte para sobrecargas de construtor, mas somente uma sobrecarga pode existir, cujos argumentos podem ser todos atendidos pela injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="0cc63-264">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="0cc63-265">Contextos de Entity Framework</span><span class="sxs-lookup"><span data-stu-id="0cc63-265">Entity Framework contexts</span></span>

<span data-ttu-id="0cc63-266">Contextos do Entity Framework geralmente são adicionados ao contêiner de serviço usando o [tempo de vida com escopo](#service-lifetimes), pois o escopo de operações de banco de dados de aplicativo Web normalmente é para a solicitação do cliente.</span><span class="sxs-lookup"><span data-stu-id="0cc63-266">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="0cc63-267">O tempo de vida padrão será definido como escopo se um tempo de vida não for especificado por uma sobrecarga [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) ao registrar o contexto do banco de dados.</span><span class="sxs-lookup"><span data-stu-id="0cc63-267">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="0cc63-268">Serviços com um determinado tempo de vida não devem usar um contexto de banco de dados com um tempo de vida mais curto que aquele do serviço.</span><span class="sxs-lookup"><span data-stu-id="0cc63-268">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="0cc63-269">Opções de tempo de vida e de registro</span><span class="sxs-lookup"><span data-stu-id="0cc63-269">Lifetime and registration options</span></span>

<span data-ttu-id="0cc63-270">Para demonstrar a diferença entre as opções de tempo de vida e de registro, considere as seguintes interfaces que representam as tarefas como uma operação com um identificador, `OperationId` .</span><span class="sxs-lookup"><span data-stu-id="0cc63-270">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with an identifier, `OperationId`.</span></span> <span data-ttu-id="0cc63-271">Dependendo de como o tempo de vida do serviço de uma operação está configurado para as interfaces a seguir, o contêiner fornecerá a mesma instância ou outra do serviço quando solicitado por uma classe:</span><span class="sxs-lookup"><span data-stu-id="0cc63-271">Depending on how the lifetime of an operation's service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="0cc63-272">As interfaces são implementadas na classe `Operation`.</span><span class="sxs-lookup"><span data-stu-id="0cc63-272">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="0cc63-273">O `Operation` Construtor gera os quatro últimos caracteres de um GUID, se um não for fornecido:</span><span class="sxs-lookup"><span data-stu-id="0cc63-273">The `Operation` constructor generates the last 4 characters of a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<!--
An `OperationService` is registered that depends on each of the other `Operation` types. When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.

* When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`. `OperationService` receives a new instance of the `IOperationTransient` class. The new instance yields a different `OperationId`.
* When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request. Across client requests, both services share a different `OperationId` value.
* When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

-->

<span data-ttu-id="0cc63-274">Em `Startup.ConfigureServices`, cada tipo é adicionado ao contêiner de acordo com seu tempo de vida nomeado:</span><span class="sxs-lookup"><span data-stu-id="0cc63-274">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup2.cs?name=snippet1)]

<span data-ttu-id="0cc63-275">O aplicativo de exemplo demonstra os tempos de vida dos objetos dentro e entre as solicitações.</span><span class="sxs-lookup"><span data-stu-id="0cc63-275">The sample app demonstrates object lifetimes within and between requests.</span></span> <span data-ttu-id="0cc63-276">O aplicativo de exemplo `IndexModel` e o middleware solicitam cada tipo de `IOperation` tipo e registra o `OperationId` :</span><span class="sxs-lookup"><span data-stu-id="0cc63-276">The sample app's `IndexModel` and middleware requests each kind of `IOperation` type and logs the `OperationId`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="0cc63-277">O middleware é semelhante ao `IndexModel` e resolve os mesmos serviços:</span><span class="sxs-lookup"><span data-stu-id="0cc63-277">The middleware is similar to the `IndexModel` and resolves the same services:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet)]

<span data-ttu-id="0cc63-278">Os serviços com escopo devem ser resolvidos no `InvokeAsync` método:</span><span class="sxs-lookup"><span data-stu-id="0cc63-278">Scoped services must be resolved in the `InvokeAsync` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet2&highlight=2)]

<span data-ttu-id="0cc63-279">A saída do agente mostra:</span><span class="sxs-lookup"><span data-stu-id="0cc63-279">The logger output shows:</span></span>

* <span data-ttu-id="0cc63-280">Os objetos *transitórios* sempre são diferentes.</span><span class="sxs-lookup"><span data-stu-id="0cc63-280">*Transient* objects are always different.</span></span> <span data-ttu-id="0cc63-281">O `OperationId` valor transitório é diferente no `IndexModel` e no middleware.</span><span class="sxs-lookup"><span data-stu-id="0cc63-281">The transient `OperationId` value is different in the `IndexModel` and the middleware.</span></span>
* <span data-ttu-id="0cc63-282">Os objetos com *escopo* são os mesmos em cada solicitação, mas diferentes em cada solicitação.</span><span class="sxs-lookup"><span data-stu-id="0cc63-282">*Scoped* objects are the same in each request but different across each request.</span></span>
* <span data-ttu-id="0cc63-283">Os objetos *singleton* são os mesmos para cada solicitação.</span><span class="sxs-lookup"><span data-stu-id="0cc63-283">*Singleton* objects are the same for every request.</span></span>

<span data-ttu-id="0cc63-284">Para reduzir a saída de log, defina "log: LogLevel: Microsoft: Error" na *appsettings.Development.jsno* arquivo:</span><span class="sxs-lookup"><span data-stu-id="0cc63-284">To reduce the logging output, set "Logging:LogLevel:Microsoft:Error" in the *appsettings.Development.json* file:</span></span>

[!code-json[](dependency-injection/samples/3.x/DependencyInjectionSample/appsettings.Development.json?highlight=7)]

## <a name="call-services-from-main"></a><span data-ttu-id="0cc63-285">Chamar os serviços desde o principal</span><span class="sxs-lookup"><span data-stu-id="0cc63-285">Call services from main</span></span>

<span data-ttu-id="0cc63-286">Crie um <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> com [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) para resolver um serviço com escopo dentro do escopo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0cc63-286">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="0cc63-287">Essa abordagem é útil para acessar um serviço com escopo na inicialização para executar tarefas de inicialização:</span><span class="sxs-lookup"><span data-stu-id="0cc63-287">This approach is useful to access a scoped service at startup to run initialization tasks:</span></span>

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

<span data-ttu-id="0cc63-288">O código anterior é de [Adicionar o inicializador de semente](xref:tutorials/razor-pages/sql?#add-the-seed-initializer) no Razor tutorial de páginas.</span><span class="sxs-lookup"><span data-stu-id="0cc63-288">The preceding code is from [Add the seed initializer](xref:tutorials/razor-pages/sql?#add-the-seed-initializer) in the Razor Pages tutorial.</span></span>

<span data-ttu-id="0cc63-289">O exemplo a seguir mostra como obter um contexto para o `IMyDependency` em `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="0cc63-289">The following example shows how to obtain a context for the `IMyDependency` in `Program.Main`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Program.cs?name=snippet)]

<a name="sv"></a>

## <a name="scope-validation"></a><span data-ttu-id="0cc63-290">Validação de escopo</span><span class="sxs-lookup"><span data-stu-id="0cc63-290">Scope validation</span></span>

<span data-ttu-id="0cc63-291">Quando o aplicativo está em execução no [ambiente de desenvolvimento](xref:fundamentals/environments) e chama [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) para criar o host, o provedor de serviço padrão executa verificações para verificar se:</span><span class="sxs-lookup"><span data-stu-id="0cc63-291">When the app is running in the [Development environment](xref:fundamentals/environments) and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="0cc63-292">Os serviços com escopo não são resolvidos direta ou indiretamente pelo provedor de serviço raiz.</span><span class="sxs-lookup"><span data-stu-id="0cc63-292">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="0cc63-293">Os serviços com escopo não são injetados direta ou indiretamente em singletons.</span><span class="sxs-lookup"><span data-stu-id="0cc63-293">Scoped services aren't directly or indirectly injected into singletons.</span></span>
* <span data-ttu-id="0cc63-294">Os serviços transitórios não são inseridos direta ou indiretamente em singletons ou serviços com escopo.</span><span class="sxs-lookup"><span data-stu-id="0cc63-294">Transient services aren't directly or indirectly injected into singletons or scoped services.</span></span>

<span data-ttu-id="0cc63-295">O provedor de serviços raiz é criado quando <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> é chamado.</span><span class="sxs-lookup"><span data-stu-id="0cc63-295">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="0cc63-296">O tempo de vida do provedor de serviço raiz corresponde ao tempo de vida do aplicativo quando o provedor começa com o aplicativo e é Descartado quando o aplicativo é desligado.</span><span class="sxs-lookup"><span data-stu-id="0cc63-296">The root service provider's lifetime corresponds to the app's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="0cc63-297">Os serviços com escopo são descartados pelo contêiner que os criou.</span><span class="sxs-lookup"><span data-stu-id="0cc63-297">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="0cc63-298">Se um serviço com escopo for criado no contêiner raiz, o tempo de vida do serviço será efetivamente promovido para singleton porque é descartado apenas pelo contêiner raiz quando o aplicativo é desligado.</span><span class="sxs-lookup"><span data-stu-id="0cc63-298">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app is shut down.</span></span> <span data-ttu-id="0cc63-299">A validação dos escopos de serviço detecta essas situações quando `BuildServiceProvider` é chamado.</span><span class="sxs-lookup"><span data-stu-id="0cc63-299">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="0cc63-300">Para obter mais informações, confira [Validação de escopo](xref:fundamentals/host/web-host#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="0cc63-300">For more information, see [Scope validation](xref:fundamentals/host/web-host#scope-validation).</span></span>

## <a name="request-services"></a><span data-ttu-id="0cc63-301">Serviços de solicitação</span><span class="sxs-lookup"><span data-stu-id="0cc63-301">Request Services</span></span>

<span data-ttu-id="0cc63-302">Os serviços disponíveis em uma solicitação do ASP.NET de `HttpContext` são expostos por meio da coleção [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).</span><span class="sxs-lookup"><span data-stu-id="0cc63-302">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="0cc63-303">Os Serviços de Solicitação representam os serviços configurados e solicitados como parte do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0cc63-303">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="0cc63-304">Quando os objetos especificam dependências, elas são atendidas pelos tipos encontrados em `RequestServices`, não em `ApplicationServices`.</span><span class="sxs-lookup"><span data-stu-id="0cc63-304">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="0cc63-305">Em geral, o aplicativo não deve usar essas propriedades diretamente.</span><span class="sxs-lookup"><span data-stu-id="0cc63-305">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="0cc63-306">Em vez disso, solicite os tipos que as classes exigem por meio de construtores de classe e permita que a estrutura Insira as dependências.</span><span class="sxs-lookup"><span data-stu-id="0cc63-306">Instead, request the types that classes require via class constructors and allow the framework to inject the dependencies.</span></span> <span data-ttu-id="0cc63-307">Isso resulta em classes que são mais fáceis de testar.</span><span class="sxs-lookup"><span data-stu-id="0cc63-307">This yields classes that are easier to test.</span></span>

<span data-ttu-id="0cc63-308">ASP.NET Core cria um escopo por solicitação e `RequestServices` expõe o provedor de serviço com escopo.</span><span class="sxs-lookup"><span data-stu-id="0cc63-308">ASP.NET Core creates a scope per request and `RequestServices` exposes the scoped service provider.</span></span> <span data-ttu-id="0cc63-309">Todos os serviços com escopo são válidos enquanto a solicitação está ativa.</span><span class="sxs-lookup"><span data-stu-id="0cc63-309">All scoped services are valid for as long as the request is active.</span></span>

> [!NOTE]
> <span data-ttu-id="0cc63-310">Prefira solicitar dependências como parâmetros de construtor para acessar a coleção `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="0cc63-310">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="0cc63-311">Projetar serviços para injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="0cc63-311">Design services for dependency injection</span></span>

<span data-ttu-id="0cc63-312">As melhores práticas são:</span><span class="sxs-lookup"><span data-stu-id="0cc63-312">Best practices are to:</span></span>

* <span data-ttu-id="0cc63-313">Projetar serviços para usar a injeção de dependência a fim de obter suas dependências.</span><span class="sxs-lookup"><span data-stu-id="0cc63-313">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="0cc63-314">Evite classes e membros com estado e estáticos.</span><span class="sxs-lookup"><span data-stu-id="0cc63-314">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="0cc63-315">Crie aplicativos para usar os serviços singleton, o que evita a criação de estado global.</span><span class="sxs-lookup"><span data-stu-id="0cc63-315">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="0cc63-316">Evite a instanciação direta das classes dependentes em serviços.</span><span class="sxs-lookup"><span data-stu-id="0cc63-316">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="0cc63-317">A instanciação direta acopla o código a uma implementação específica.</span><span class="sxs-lookup"><span data-stu-id="0cc63-317">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="0cc63-318">Verifique as classes de aplicativo pequenas, bem fatoradas e facilmente testadas.</span><span class="sxs-lookup"><span data-stu-id="0cc63-318">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="0cc63-319">Se uma classe parecer ter muitas dependências injetadas, isso geralmente é um sinal de que a classe tem muitas responsabilidades e está violando o [único princípio de responsabilidade (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="0cc63-319">If a class seems to have too many injected dependencies, that's generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="0cc63-320">Tente refatorar a classe movendo algumas de suas responsabilidades para uma nova classe.</span><span class="sxs-lookup"><span data-stu-id="0cc63-320">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="0cc63-321">Tenha em mente que as classes Razor de modelo de página de páginas e as classes do controlador MVC devem se concentrar nas preocupações da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="0cc63-321">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="0cc63-322">Descarte de serviços</span><span class="sxs-lookup"><span data-stu-id="0cc63-322">Disposal of services</span></span>

<span data-ttu-id="0cc63-323">O contêiner chama <xref:System.IDisposable.Dispose*> para os tipos <xref:System.IDisposable> criados por ele.</span><span class="sxs-lookup"><span data-stu-id="0cc63-323">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="0cc63-324">Os serviços nunca devem ser descartados por nenhum código que resolveu o serviço de um contêiner.</span><span class="sxs-lookup"><span data-stu-id="0cc63-324">Services should never be disposed by any code that resolved the service from a container.</span></span> <span data-ttu-id="0cc63-325">Se um tipo ou fábrica for registrado como um singleton, o contêiner descartará o singleton.</span><span class="sxs-lookup"><span data-stu-id="0cc63-325">If a type or factory is registered as a singleton, the container disposes the singleton.</span></span>

<span data-ttu-id="0cc63-326">No exemplo a seguir, os serviços são criados pelo contêiner de serviço e descartados automaticamente:</span><span class="sxs-lookup"><span data-stu-id="0cc63-326">In the following example, the services are created by the service container and disposed automatically:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Services/Service1.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="0cc63-327">O console de depuração mostra a saída a seguir após cada atualização da página de índice:</span><span class="sxs-lookup"><span data-stu-id="0cc63-327">The debug console shows the following output after each refresh of the Index page:</span></span>

```console
Service1: IndexModel.OnGet
Service2: IndexModel.OnGet
Service3: IndexModel.OnGet
Service1.Dispose
```

### <a name="services-not-created-by-the-service-container"></a><span data-ttu-id="0cc63-328">Serviços não criados pelo contêiner de serviço</span><span class="sxs-lookup"><span data-stu-id="0cc63-328">Services not created by the service container</span></span>
<!--Review: Who cares that service instances aren't disposed, singletons aren't disposed until the app shuts down anyway.
  -->
<span data-ttu-id="0cc63-329">Considere o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="0cc63-329">Consider the following code:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup2.cs?name=snippet)]

<span data-ttu-id="0cc63-330">No código anterior:</span><span class="sxs-lookup"><span data-stu-id="0cc63-330">In the preceding code:</span></span>

* <span data-ttu-id="0cc63-331">As instâncias de serviço não são criadas pelo contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="0cc63-331">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="0cc63-332">Os tempos de vida de serviço pretendidos não são conhecidos pela estrutura.</span><span class="sxs-lookup"><span data-stu-id="0cc63-332">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="0cc63-333">A estrutura não descarta os serviços automaticamente.</span><span class="sxs-lookup"><span data-stu-id="0cc63-333">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="0cc63-334">Se os serviços não forem explicitamente descartados no código do desenvolvedor, eles persistirão até que o aplicativo seja desligado.</span><span class="sxs-lookup"><span data-stu-id="0cc63-334">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="0cc63-335">Diretrizes de IDisposable para instâncias transitórias e compartilhadas</span><span class="sxs-lookup"><span data-stu-id="0cc63-335">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="0cc63-336">Tempo de vida transitório, limitado</span><span class="sxs-lookup"><span data-stu-id="0cc63-336">Transient, limited lifetime</span></span>

<span data-ttu-id="0cc63-337">**Cenário**</span><span class="sxs-lookup"><span data-stu-id="0cc63-337">**Scenario**</span></span>

<span data-ttu-id="0cc63-338">O aplicativo requer uma <xref:System.IDisposable> instância com um tempo de vida transitório para qualquer um dos seguintes cenários:</span><span class="sxs-lookup"><span data-stu-id="0cc63-338">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="0cc63-339">A instância é resolvida no escopo raiz (contêiner raiz).</span><span class="sxs-lookup"><span data-stu-id="0cc63-339">The instance is resolved in the root scope (root container).</span></span>
* <span data-ttu-id="0cc63-340">A instância deve ser descartada antes do término do escopo.</span><span class="sxs-lookup"><span data-stu-id="0cc63-340">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="0cc63-341">**Solução**</span><span class="sxs-lookup"><span data-stu-id="0cc63-341">**Solution**</span></span>

<span data-ttu-id="0cc63-342">Use o padrão de fábrica para criar uma instância fora do escopo pai.</span><span class="sxs-lookup"><span data-stu-id="0cc63-342">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="0cc63-343">Nessa situação, o aplicativo geralmente teria um `Create` método que chamasse o construtor do tipo final diretamente.</span><span class="sxs-lookup"><span data-stu-id="0cc63-343">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="0cc63-344">Se o tipo final tiver outras dependências, a fábrica poderá:</span><span class="sxs-lookup"><span data-stu-id="0cc63-344">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="0cc63-345">Receber um <xref:System.IServiceProvider> em seu construtor.</span><span class="sxs-lookup"><span data-stu-id="0cc63-345">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="0cc63-346">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> para instanciar a instância fora do contêiner, ao usar o contêiner para suas dependências.</span><span class="sxs-lookup"><span data-stu-id="0cc63-346">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="0cc63-347">Instância compartilhada, tempo de vida limitado</span><span class="sxs-lookup"><span data-stu-id="0cc63-347">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="0cc63-348">**Cenário**</span><span class="sxs-lookup"><span data-stu-id="0cc63-348">**Scenario**</span></span>

<span data-ttu-id="0cc63-349">O aplicativo requer uma <xref:System.IDisposable> instância compartilhada entre vários serviços, mas o <xref:System.IDisposable> deve ter um tempo de vida limitado.</span><span class="sxs-lookup"><span data-stu-id="0cc63-349">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="0cc63-350">**Solução**</span><span class="sxs-lookup"><span data-stu-id="0cc63-350">**Solution**</span></span>

<span data-ttu-id="0cc63-351">Registre a instância com um tempo de vida de escopo.</span><span class="sxs-lookup"><span data-stu-id="0cc63-351">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="0cc63-352">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> para iniciar e criar um novo <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> .</span><span class="sxs-lookup"><span data-stu-id="0cc63-352">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="0cc63-353">Use o escopo <xref:System.IServiceProvider> para obter os serviços necessários.</span><span class="sxs-lookup"><span data-stu-id="0cc63-353">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="0cc63-354">Descarte o escopo quando o tempo de vida deve terminar.</span><span class="sxs-lookup"><span data-stu-id="0cc63-354">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-idisposable-guidelines"></a><span data-ttu-id="0cc63-355">Diretrizes gerais de IDisposable</span><span class="sxs-lookup"><span data-stu-id="0cc63-355">General IDisposable guidelines</span></span>

* <span data-ttu-id="0cc63-356">Não registre <xref:System.IDisposable> instâncias com um escopo transitório.</span><span class="sxs-lookup"><span data-stu-id="0cc63-356">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="0cc63-357">Em vez disso, use o padrão de fábrica.</span><span class="sxs-lookup"><span data-stu-id="0cc63-357">Use the factory pattern instead.</span></span>
* <span data-ttu-id="0cc63-358">Não resolva instâncias transitórias ou com escopo definido <xref:System.IDisposable> no escopo raiz.</span><span class="sxs-lookup"><span data-stu-id="0cc63-358">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="0cc63-359">A única exceção geral é quando o aplicativo cria/recria e descarta o <xref:System.IServiceProvider> , que não é um padrão ideal.</span><span class="sxs-lookup"><span data-stu-id="0cc63-359">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="0cc63-360">O recebimento de uma <xref:System.IDisposable> dependência por meio de di não exige que o receptor se implemente <xref:System.IDisposable> .</span><span class="sxs-lookup"><span data-stu-id="0cc63-360">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="0cc63-361">O receptor da <xref:System.IDisposable> dependência não deve chamar <xref:System.IDisposable.Dispose%2A> essa dependência.</span><span class="sxs-lookup"><span data-stu-id="0cc63-361">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="0cc63-362">Os escopos devem ser usados para controlar o tempo de vida dos serviços.</span><span class="sxs-lookup"><span data-stu-id="0cc63-362">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="0cc63-363">Os escopos não são hierárquicos e não há nenhuma conexão especial entre escopos.</span><span class="sxs-lookup"><span data-stu-id="0cc63-363">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="0cc63-364">Substituição do contêiner de serviço padrão</span><span class="sxs-lookup"><span data-stu-id="0cc63-364">Default service container replacement</span></span>

<span data-ttu-id="0cc63-365">O contêiner de serviço interno foi projetado para atender às necessidades da estrutura e da maioria dos aplicativos de consumidor.</span><span class="sxs-lookup"><span data-stu-id="0cc63-365">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="0cc63-366">É recomendável usar o contêiner interno, a menos que você precise de um recurso específico ao qual o contêiner interno não ofereça suporte, como:</span><span class="sxs-lookup"><span data-stu-id="0cc63-366">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="0cc63-367">Injeção de propriedade</span><span class="sxs-lookup"><span data-stu-id="0cc63-367">Property injection</span></span>
* <span data-ttu-id="0cc63-368">Injeção com base no nome</span><span class="sxs-lookup"><span data-stu-id="0cc63-368">Injection based on name</span></span>
* <span data-ttu-id="0cc63-369">Contêineres filho</span><span class="sxs-lookup"><span data-stu-id="0cc63-369">Child containers</span></span>
* <span data-ttu-id="0cc63-370">Gerenciamento de tempo de vida personalizado</span><span class="sxs-lookup"><span data-stu-id="0cc63-370">Custom lifetime management</span></span>
* <span data-ttu-id="0cc63-371">Suporte de `Func<T>` para inicialização lenta</span><span class="sxs-lookup"><span data-stu-id="0cc63-371">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="0cc63-372">Registro baseado em Convenção</span><span class="sxs-lookup"><span data-stu-id="0cc63-372">Convention-based registration</span></span>

<span data-ttu-id="0cc63-373">Os seguintes contêineres de terceiros podem ser usados com aplicativos ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="0cc63-373">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="0cc63-374">Autofac</span><span class="sxs-lookup"><span data-stu-id="0cc63-374">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="0cc63-375">DryIoc</span><span class="sxs-lookup"><span data-stu-id="0cc63-375">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="0cc63-376">Grace</span><span class="sxs-lookup"><span data-stu-id="0cc63-376">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="0cc63-377">LightInject</span><span class="sxs-lookup"><span data-stu-id="0cc63-377">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="0cc63-378">Lamar</span><span class="sxs-lookup"><span data-stu-id="0cc63-378">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="0cc63-379">Stashbox</span><span class="sxs-lookup"><span data-stu-id="0cc63-379">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="0cc63-380">Unity</span><span class="sxs-lookup"><span data-stu-id="0cc63-380">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="0cc63-381">Acesso thread-safe</span><span class="sxs-lookup"><span data-stu-id="0cc63-381">Thread safety</span></span>

<span data-ttu-id="0cc63-382">Crie serviços singleton thread-safe.</span><span class="sxs-lookup"><span data-stu-id="0cc63-382">Create thread-safe singleton services.</span></span> <span data-ttu-id="0cc63-383">Se um serviço singleton tiver uma dependência em um serviço transitório, o serviço transitório também precisará ter acesso thread-safe, dependendo de como ele é usado pelo singleton.</span><span class="sxs-lookup"><span data-stu-id="0cc63-383">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="0cc63-384">O método de fábrica de um único serviço, como o segundo argumento para [addsingleton \<TService> (IServiceCollection, Func \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), não precisa ser thread-safe.</span><span class="sxs-lookup"><span data-stu-id="0cc63-384">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="0cc63-385">Como um construtor do tipo (`static`), ele tem garantia de ser chamado uma vez por um único thread.</span><span class="sxs-lookup"><span data-stu-id="0cc63-385">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="0cc63-386">Recomendações</span><span class="sxs-lookup"><span data-stu-id="0cc63-386">Recommendations</span></span>

* <span data-ttu-id="0cc63-387">A resolução de serviço baseada em `async/await` e `Task` não é compatível.</span><span class="sxs-lookup"><span data-stu-id="0cc63-387">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="0cc63-388">O C# não oferece suporte a construtores assíncronos.</span><span class="sxs-lookup"><span data-stu-id="0cc63-388">C# does not support asynchronous constructors.</span></span> <span data-ttu-id="0cc63-389">O padrão recomendado é usar métodos assíncronos após a resolução síncrona do serviço.</span><span class="sxs-lookup"><span data-stu-id="0cc63-389">The recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>
* <span data-ttu-id="0cc63-390">Evite armazenar dados e a configuração diretamente no contêiner do serviço.</span><span class="sxs-lookup"><span data-stu-id="0cc63-390">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="0cc63-391">Por exemplo, o carrinho de compras de um usuário normalmente não deve ser adicionado ao contêiner do serviço.</span><span class="sxs-lookup"><span data-stu-id="0cc63-391">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="0cc63-392">A configuração deve usar o [padrão de opções](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="0cc63-392">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="0cc63-393">Da mesma forma, evite objetos de "suporte de dados" que existem somente para permitir o acesso a outro objeto.</span><span class="sxs-lookup"><span data-stu-id="0cc63-393">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="0cc63-394">É melhor solicitar o item real por meio da DI.</span><span class="sxs-lookup"><span data-stu-id="0cc63-394">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="0cc63-395">Evite o acesso estático aos serviços.</span><span class="sxs-lookup"><span data-stu-id="0cc63-395">Avoid static access to services.</span></span> <span data-ttu-id="0cc63-396">Por exemplo, Evite digitar estaticamente [IApplicationBuilder. ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) para uso em outro lugar).</span><span class="sxs-lookup"><span data-stu-id="0cc63-396">For example, avoid statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>
* <span data-ttu-id="0cc63-397">Mantenha as fábricas de injeção rápida e síncrona.</span><span class="sxs-lookup"><span data-stu-id="0cc63-397">Keep DI factories fast and synchronous.</span></span>
* <span data-ttu-id="0cc63-398">Evite usar o *padrão do localizador de serviço*.</span><span class="sxs-lookup"><span data-stu-id="0cc63-398">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="0cc63-399">Por exemplo, não invoque <xref:System.IServiceProvider.GetService*> para obter uma instância de serviço quando for possível usar a DI:</span><span class="sxs-lookup"><span data-stu-id="0cc63-399">For example, don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="0cc63-400">**Incorreto:**</span><span class="sxs-lookup"><span data-stu-id="0cc63-400">**Incorrect:**</span></span>

    ![Código incorreto](dependency-injection/_static/bad.png)

  <span data-ttu-id="0cc63-402">**Correto**:</span><span class="sxs-lookup"><span data-stu-id="0cc63-402">**Correct**:</span></span>

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
* <span data-ttu-id="0cc63-403">Outra variação de localizador de serviço a ser evitada é injetar um alocador que resolve as dependências em runtime.</span><span class="sxs-lookup"><span data-stu-id="0cc63-403">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="0cc63-404">Essas duas práticas misturam estratégias de [inversão de controle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).</span><span class="sxs-lookup"><span data-stu-id="0cc63-404">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
* <span data-ttu-id="0cc63-405">Evite o acesso estático a `HttpContext` (por exemplo, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="0cc63-405">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<a name="ASP0000"></a>
* <span data-ttu-id="0cc63-406">Evite chamadas para <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> no `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="0cc63-406">Avoid calls to <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> in `ConfigureServices`.</span></span> <span data-ttu-id="0cc63-407">Chamar `BuildServiceProvider` normalmente acontece quando o desenvolvedor deseja resolver um serviço no `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="0cc63-407">Calling `BuildServiceProvider` typically happens when the developer wants to resolve a service in `ConfigureServices`.</span></span> <span data-ttu-id="0cc63-408">Por exemplo, considere o caso em que você precisa obter a `LoginPath` configuração de.</span><span class="sxs-lookup"><span data-stu-id="0cc63-408">For example, consider the case where you need go get the `LoginPath` from configuration.</span></span> <span data-ttu-id="0cc63-409">Evite o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="0cc63-409">Avoid the following code:</span></span>

  ![código inadequado chamando BuildServiceProvider](~/fundamentals/dependency-injection/_static/badcodeX.png)

  <span data-ttu-id="0cc63-411">Na imagem anterior, selecionar a linha ondulada verde em `services.BuildServiceProvider` mostra o seguinte aviso de ASP0000:</span><span class="sxs-lookup"><span data-stu-id="0cc63-411">In the preceding image, selecting the green wavy line under `services.BuildServiceProvider` shows the following ASP0000 warning:</span></span>
    * <span data-ttu-id="0cc63-412">ASP0000 chamar ' BuildServiceProvider ' do código do aplicativo resulta em uma cópia adicional dos serviços singleton que estão sendo criados.</span><span class="sxs-lookup"><span data-stu-id="0cc63-412">ASP0000 Calling 'BuildServiceProvider' from application code results in an additional copy of singleton services being created.</span></span> <span data-ttu-id="0cc63-413">Considere alternativas como a injeção de dependência de serviços como parâmetros para ' Configurar '.</span><span class="sxs-lookup"><span data-stu-id="0cc63-413">Consider alternatives such as dependency injecting services as parameters to 'Configure'.</span></span>

   <span data-ttu-id="0cc63-414">`BuildServiceProvider`A chamada cria um segundo contêiner, que pode criar singletons rasgados e causar referências a grafos de objeto em vários contêineres.</span><span class="sxs-lookup"><span data-stu-id="0cc63-414">Calling `BuildServiceProvider` creates a second container, which can create torn singletons and cause references to object graphs across multiple containers.</span></span> <span data-ttu-id="0cc63-415">Uma maneira correta de `LoginPath` se obter é usar o padrão de opção com di:</span><span class="sxs-lookup"><span data-stu-id="0cc63-415">A correct way to get `LoginPath` is using the option pattern with DI:</span></span>

  [!code-csharp[](dependency-injection/samples/3.x/AntiPattern3/Startup.cs?name=snippet)]

* <span data-ttu-id="0cc63-416">Serviços inposados transitórios são capturados pelo contêiner para descarte.</span><span class="sxs-lookup"><span data-stu-id="0cc63-416">Disposable transient services are captured by the container for disposal.</span></span> <span data-ttu-id="0cc63-417">Isso pode transformar em um vazamento de memória se resolvido no contêiner de nível superior.</span><span class="sxs-lookup"><span data-stu-id="0cc63-417">This can turn into a memory leak if resolved from the top level container.</span></span>
* <span data-ttu-id="0cc63-418">Habilite a validação de escopo para garantir que o aplicativo não tenha serviços com escopo capturando singletons.</span><span class="sxs-lookup"><span data-stu-id="0cc63-418">Enable scope validation to make sure the app doesn't have scoped services capturing singletons.</span></span> <span data-ttu-id="0cc63-419">Para obter mais informações, confira [Validação de escopo](#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="0cc63-419">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="0cc63-420">Como todos os conjuntos de recomendações, talvez você encontre situações em que é necessário ignorar uma recomendação.</span><span class="sxs-lookup"><span data-stu-id="0cc63-420">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="0cc63-421">As exceções são raras, principalmente os casos especiais dentro da própria estrutura.</span><span class="sxs-lookup"><span data-stu-id="0cc63-421">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="0cc63-422">A DI é uma *alternativa* aos padrões de acesso a objeto estático/global.</span><span class="sxs-lookup"><span data-stu-id="0cc63-422">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="0cc63-423">Talvez você não obtenha os benefícios da DI se combiná-lo com o acesso a objeto estático.</span><span class="sxs-lookup"><span data-stu-id="0cc63-423">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="0cc63-424">Padrões recomendados para multilocação em DI</span><span class="sxs-lookup"><span data-stu-id="0cc63-424">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="0cc63-425">O [Orchard Core](https://github.com/OrchardCMS/OrchardCore) fornece multilocação.</span><span class="sxs-lookup"><span data-stu-id="0cc63-425">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) provides multi-tenancy.</span></span> <span data-ttu-id="0cc63-426">Para obter mais informações, consulte a [documentação do Orchard Core](https://docs.orchardcore.net/en/dev/).</span><span class="sxs-lookup"><span data-stu-id="0cc63-426">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="0cc63-427">Consulte os aplicativos de exemplos em https://github.com/OrchardCMS/OrchardCore.Samples para obter exemplos de como criar aplicativos modulares e multilocatários usando o Orchard Core Framework sem nenhum dos recursos específicos do CMS.</span><span class="sxs-lookup"><span data-stu-id="0cc63-427">See the samples apps at https://github.com/OrchardCMS/OrchardCore.Samples for examples of how to build modular and multi-tenant apps using just Orchard Core Framework without any of the CMS specific features.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="0cc63-428">Serviços fornecidos pela estrutura</span><span class="sxs-lookup"><span data-stu-id="0cc63-428">Framework-provided services</span></span>

<span data-ttu-id="0cc63-429">O `Startup.ConfigureServices` método é responsável por definir os serviços que o aplicativo usa, incluindo recursos de plataforma, como Entity Framework Core e ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="0cc63-429">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="0cc63-430">Inicialmente, o `IServiceCollection` fornecido para o `ConfigureServices` tem serviços definidos pela estrutura, dependendo de [como o host foi configurado](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="0cc63-430">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="0cc63-431">Aplicativos baseados em um ASP.NET Core modelos têm mais de 250 serviços registrados pela estrutura.</span><span class="sxs-lookup"><span data-stu-id="0cc63-431">Apps based on an ASP.NET Core templates have more than 250 services registered by the framework.</span></span> <span data-ttu-id="0cc63-432">Uma pequena amostra de serviços registrados na estrutura é listada na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="0cc63-432">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="0cc63-433">Tipo de Serviço</span><span class="sxs-lookup"><span data-stu-id="0cc63-433">Service Type</span></span> | <span data-ttu-id="0cc63-434">Tempo de vida</span><span class="sxs-lookup"><span data-stu-id="0cc63-434">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="0cc63-435">Transitório</span><span class="sxs-lookup"><span data-stu-id="0cc63-435">Transient</span></span> |
| <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> | <span data-ttu-id="0cc63-436">Singleton</span><span class="sxs-lookup"><span data-stu-id="0cc63-436">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> | <span data-ttu-id="0cc63-437">Singleton</span><span class="sxs-lookup"><span data-stu-id="0cc63-437">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="0cc63-438">Singleton</span><span class="sxs-lookup"><span data-stu-id="0cc63-438">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="0cc63-439">Transitório</span><span class="sxs-lookup"><span data-stu-id="0cc63-439">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="0cc63-440">Singleton</span><span class="sxs-lookup"><span data-stu-id="0cc63-440">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="0cc63-441">Transitório</span><span class="sxs-lookup"><span data-stu-id="0cc63-441">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="0cc63-442">Singleton</span><span class="sxs-lookup"><span data-stu-id="0cc63-442">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="0cc63-443">Singleton</span><span class="sxs-lookup"><span data-stu-id="0cc63-443">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="0cc63-444">Singleton</span><span class="sxs-lookup"><span data-stu-id="0cc63-444">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="0cc63-445">Transitório</span><span class="sxs-lookup"><span data-stu-id="0cc63-445">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="0cc63-446">Singleton</span><span class="sxs-lookup"><span data-stu-id="0cc63-446">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="0cc63-447">Singleton</span><span class="sxs-lookup"><span data-stu-id="0cc63-447">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="0cc63-448">Singleton</span><span class="sxs-lookup"><span data-stu-id="0cc63-448">Singleton</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="0cc63-449">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="0cc63-449">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* [<span data-ttu-id="0cc63-450">Padrões de conferência NDC para desenvolvimento de aplicativo de DI</span><span class="sxs-lookup"><span data-stu-id="0cc63-450">NDC Conference Patterns for DI app development</span></span>](https://www.youtube.com/watch?v=x-C-CNBVTaY)
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="0cc63-451">Quatro maneiras de descartar IDisposables em ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0cc63-451">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="0cc63-452">Como gravar um código limpo no ASP.NET Core com injeção de dependência (MSDN)</span><span class="sxs-lookup"><span data-stu-id="0cc63-452">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="0cc63-453">Princípio de Dependências Explícitas</span><span class="sxs-lookup"><span data-stu-id="0cc63-453">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="0cc63-454">Inversão de Contêineres de Controle e o padrão de Injeção de Dependência (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="0cc63-454">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="0cc63-455">Como registrar um serviço com várias interfaces na DI do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0cc63-455">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0cc63-456">Por [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com)e [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="0cc63-456">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="0cc63-457">O ASP.NET Core é compatível com o padrão de design de software de DI (injeção de dependência), que é uma técnica para alcançar a [IoC (Inversão de Controle)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) entre classes e suas dependências.</span><span class="sxs-lookup"><span data-stu-id="0cc63-457">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="0cc63-458">Para obter mais informações específicas sobre injeção de dependência em controladores de MVC, consulte <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="0cc63-458">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="0cc63-459">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0cc63-459">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="0cc63-460">Visão geral da injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="0cc63-460">Overview of dependency injection</span></span>

<span data-ttu-id="0cc63-461">Uma *dependência* é qualquer objeto exigido por outro objeto.</span><span class="sxs-lookup"><span data-stu-id="0cc63-461">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="0cc63-462">Examine a classe `MyDependency` a seguir com um método `WriteMessage` do qual outras classes em um aplicativo dependem:</span><span class="sxs-lookup"><span data-stu-id="0cc63-462">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="0cc63-463">Uma instância da classe `MyDependency` pode ser criada para tornar o método `WriteMessage` disponível para uma classe.</span><span class="sxs-lookup"><span data-stu-id="0cc63-463">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="0cc63-464">A classe  é uma dependência da classe:</span><span class="sxs-lookup"><span data-stu-id="0cc63-464">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="0cc63-465">A classe cria e depende diretamente da instância `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="0cc63-465">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="0cc63-466">As dependências de código (como no exemplo anterior) são problemáticas e devem ser evitadas por estes motivos:</span><span class="sxs-lookup"><span data-stu-id="0cc63-466">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="0cc63-467">Para substituir `MyDependency` por uma implementação diferente, a classe deve ser modificada.</span><span class="sxs-lookup"><span data-stu-id="0cc63-467">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="0cc63-468">Se `MyDependency` tiver dependências, elas deverão ser configuradas pela classe.</span><span class="sxs-lookup"><span data-stu-id="0cc63-468">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="0cc63-469">Em um projeto grande com várias classes dependendo da `MyDependency`, o código de configuração fica pulverizado por todo o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0cc63-469">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="0cc63-470">É difícil testar a unidade dessa implementação.</span><span class="sxs-lookup"><span data-stu-id="0cc63-470">This implementation is difficult to unit test.</span></span> <span data-ttu-id="0cc63-471">O aplicativo deve usar uma simulação ou stub da classe `MyDependency`, o que não é possível com essa abordagem.</span><span class="sxs-lookup"><span data-stu-id="0cc63-471">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="0cc63-472">Injeção de dependência trata desses problemas da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="0cc63-472">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="0cc63-473">O uso de uma interface ou classe base para abstrair a implementação da dependência.</span><span class="sxs-lookup"><span data-stu-id="0cc63-473">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="0cc63-474">Registrando a dependência em um contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="0cc63-474">Registration of the dependency in a service container.</span></span> <span data-ttu-id="0cc63-475">O ASP.NET Core fornece um contêiner de serviço interno, o <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="0cc63-475">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="0cc63-476">Os serviços são registrados no método `Startup.ConfigureServices` do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0cc63-476">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="0cc63-477">*Injeção* do serviço no construtor da classe na qual ele é usado.</span><span class="sxs-lookup"><span data-stu-id="0cc63-477">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="0cc63-478">A estrutura assume a responsabilidade de criar uma instância da dependência e de descartá-la quando não for mais necessária.</span><span class="sxs-lookup"><span data-stu-id="0cc63-478">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="0cc63-479">No [exemplo de aplicativo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), a interface `IMyDependency` define um método que o serviço fornece ao aplicativo:</span><span class="sxs-lookup"><span data-stu-id="0cc63-479">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="0cc63-480">Essa interface é implementada por um tipo concreto, `MyDependency`:</span><span class="sxs-lookup"><span data-stu-id="0cc63-480">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="0cc63-481">`MyDependency` solicita um <xref:Microsoft.Extensions.Logging.ILogger`1> em seu construtor.</span><span class="sxs-lookup"><span data-stu-id="0cc63-481">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="0cc63-482">Não é incomum usar a injeção de dependência de uma maneira encadeada.</span><span class="sxs-lookup"><span data-stu-id="0cc63-482">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="0cc63-483">Por sua vez, cada dependência solicitada solicita suas próprias dependências.</span><span class="sxs-lookup"><span data-stu-id="0cc63-483">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="0cc63-484">O contêiner resolve as dependências no grafo e retorna o serviço totalmente resolvido.</span><span class="sxs-lookup"><span data-stu-id="0cc63-484">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="0cc63-485">O conjunto de dependências que precisa ser resolvido normalmente é chamado de *árvore de dependência*, *grafo de dependência* ou *grafo de objeto*.</span><span class="sxs-lookup"><span data-stu-id="0cc63-485">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="0cc63-486">`IMyDependency` e `ILogger<TCategoryName>` devem ser registrados no contêiner do serviço.</span><span class="sxs-lookup"><span data-stu-id="0cc63-486">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="0cc63-487">`IMyDependency` está registrado em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="0cc63-487">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="0cc63-488">`ILogger<TCategoryName>` é registrado pela infraestrutura de abstrações de registro em log, portanto, é um [serviço fornecido por estrutura](#framework-provided-services) registrado por padrão pela estrutura.</span><span class="sxs-lookup"><span data-stu-id="0cc63-488">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="0cc63-489">O contêiner resolve `ILogger<TCategoryName>` aproveitando os [tipos abertos (genéricos)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminando a necessidade de registrar todo [tipo construído (genérico)](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span><span class="sxs-lookup"><span data-stu-id="0cc63-489">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="0cc63-490">No exemplo de aplicativo, o serviço `IMyDependency` está registrado com o tipo concreto `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="0cc63-490">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="0cc63-491">O registro define o escopo do tempo de vida do serviço para o tempo de vida de uma única solicitação.</span><span class="sxs-lookup"><span data-stu-id="0cc63-491">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="0cc63-492">Descreveremos posteriormente neste tópico os [tempos de vida do serviço](#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="0cc63-492">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="0cc63-493">Cada método de extensão `services.Add{SERVICE_NAME}` adiciona (e possivelmente configura) serviços.</span><span class="sxs-lookup"><span data-stu-id="0cc63-493">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="0cc63-494">Por exemplo, `services.AddMvc()` adiciona as páginas de serviços Razor e a MVC necessária.</span><span class="sxs-lookup"><span data-stu-id="0cc63-494">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="0cc63-495">Recomendamos que os aplicativos sigam essa convenção.</span><span class="sxs-lookup"><span data-stu-id="0cc63-495">We recommended that apps follow this convention.</span></span> <span data-ttu-id="0cc63-496">Coloque os métodos de extensão no namespace [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) para encapsular grupos de registros de serviço.</span><span class="sxs-lookup"><span data-stu-id="0cc63-496">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="0cc63-497">Se o construtor do serviço exigir um [tipo interno](/dotnet/csharp/language-reference/keywords/built-in-types-table), como `string`, o tipo poderá ser injetado usando a [configuração](xref:fundamentals/configuration/index) ou o [padrão de opções](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="0cc63-497">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="0cc63-498">Uma instância do serviço é solicitada por meio do construtor de uma classe, onde o serviço é usado e atribuído a um campo particular.</span><span class="sxs-lookup"><span data-stu-id="0cc63-498">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="0cc63-499">O campo é usado para acessar o serviço conforme o necessário na classe.</span><span class="sxs-lookup"><span data-stu-id="0cc63-499">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="0cc63-500">No exemplo de aplicativo, a instância `IMyDependency` é solicitada e usada para chamar o método `WriteMessage` do serviço:</span><span class="sxs-lookup"><span data-stu-id="0cc63-500">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="0cc63-501">Serviços injetados na inicialização</span><span class="sxs-lookup"><span data-stu-id="0cc63-501">Services injected into Startup</span></span>

<span data-ttu-id="0cc63-502">Somente os seguintes tipos de serviço podem ser injetados no `Startup` Construtor ao usar o host genérico ( <xref:Microsoft.Extensions.Hosting.IHostBuilder> ):</span><span class="sxs-lookup"><span data-stu-id="0cc63-502">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="0cc63-503">Os serviços podem ser injetados em `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="0cc63-503">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="0cc63-504">Para obter mais informações, consulte <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="0cc63-504">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="0cc63-505">Serviços fornecidos pela estrutura</span><span class="sxs-lookup"><span data-stu-id="0cc63-505">Framework-provided services</span></span>

<span data-ttu-id="0cc63-506">O `Startup.ConfigureServices` método é responsável por definir os serviços que o aplicativo usa, incluindo recursos de plataforma, como Entity Framework Core e ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="0cc63-506">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="0cc63-507">Inicialmente, o `IServiceCollection` fornecido para o `ConfigureServices` tem serviços definidos pela estrutura, dependendo de [como o host foi configurado](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="0cc63-507">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="0cc63-508">Não é incomum um aplicativo baseado em um modelo de ASP.NET Core ter centenas de serviços registrados pela estrutura.</span><span class="sxs-lookup"><span data-stu-id="0cc63-508">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="0cc63-509">Uma pequena amostra de serviços registrados na estrutura é listada na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="0cc63-509">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="0cc63-510">Tipo de Serviço</span><span class="sxs-lookup"><span data-stu-id="0cc63-510">Service Type</span></span> | <span data-ttu-id="0cc63-511">Tempo de vida</span><span class="sxs-lookup"><span data-stu-id="0cc63-511">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="0cc63-512">Transitório</span><span class="sxs-lookup"><span data-stu-id="0cc63-512">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="0cc63-513">Singleton</span><span class="sxs-lookup"><span data-stu-id="0cc63-513">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="0cc63-514">Singleton</span><span class="sxs-lookup"><span data-stu-id="0cc63-514">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="0cc63-515">Singleton</span><span class="sxs-lookup"><span data-stu-id="0cc63-515">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="0cc63-516">Transitório</span><span class="sxs-lookup"><span data-stu-id="0cc63-516">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="0cc63-517">Singleton</span><span class="sxs-lookup"><span data-stu-id="0cc63-517">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="0cc63-518">Transitório</span><span class="sxs-lookup"><span data-stu-id="0cc63-518">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="0cc63-519">Singleton</span><span class="sxs-lookup"><span data-stu-id="0cc63-519">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="0cc63-520">Singleton</span><span class="sxs-lookup"><span data-stu-id="0cc63-520">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="0cc63-521">Singleton</span><span class="sxs-lookup"><span data-stu-id="0cc63-521">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="0cc63-522">Transitório</span><span class="sxs-lookup"><span data-stu-id="0cc63-522">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="0cc63-523">Singleton</span><span class="sxs-lookup"><span data-stu-id="0cc63-523">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="0cc63-524">Singleton</span><span class="sxs-lookup"><span data-stu-id="0cc63-524">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="0cc63-525">Singleton</span><span class="sxs-lookup"><span data-stu-id="0cc63-525">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="0cc63-526">Registrar serviços adicionais com métodos de extensão</span><span class="sxs-lookup"><span data-stu-id="0cc63-526">Register additional services with extension methods</span></span>

<span data-ttu-id="0cc63-527">Quando um método de extensão de coleta do serviço estiver disponível para registrar um serviço (e seus serviços dependentes, se for necessário), a convenção é usar um único método de extensão `Add{SERVICE_NAME}` para registrar todos os serviços exigidos por esse serviço.</span><span class="sxs-lookup"><span data-stu-id="0cc63-527">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="0cc63-528">O código a seguir é um exemplo de como adicionar serviços adicionais ao contêiner usando os métodos de extensão [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) e <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> :</span><span class="sxs-lookup"><span data-stu-id="0cc63-528">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="0cc63-529">Para saber mais, confira a classe <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> na documentação da API.</span><span class="sxs-lookup"><span data-stu-id="0cc63-529">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="0cc63-530">Tempos de vida do serviço</span><span class="sxs-lookup"><span data-stu-id="0cc63-530">Service lifetimes</span></span>

<span data-ttu-id="0cc63-531">Escolha um tempo de vida apropriado para cada serviço registrado.</span><span class="sxs-lookup"><span data-stu-id="0cc63-531">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="0cc63-532">Os serviços do ASP.NET Core podem ser configurados com os seguintes tempos de vida:</span><span class="sxs-lookup"><span data-stu-id="0cc63-532">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="0cc63-533">Transitório</span><span class="sxs-lookup"><span data-stu-id="0cc63-533">Transient</span></span>

<span data-ttu-id="0cc63-534">Serviços temporários de tempo de vida (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) são criados cada vez que são solicitados pelo contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="0cc63-534">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="0cc63-535">Esse tempo de vida funciona melhor para serviços leves e sem estado.</span><span class="sxs-lookup"><span data-stu-id="0cc63-535">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="0cc63-536">Em aplicativos que processam solicitações, os serviços transitórios são descartados no final da solicitação.</span><span class="sxs-lookup"><span data-stu-id="0cc63-536">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="0cc63-537">Com escopo</span><span class="sxs-lookup"><span data-stu-id="0cc63-537">Scoped</span></span>

<span data-ttu-id="0cc63-538">Os serviços com tempo de vida (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) com escopo são criados uma vez por solicitação de cliente (conexão).</span><span class="sxs-lookup"><span data-stu-id="0cc63-538">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="0cc63-539">Em aplicativos que processam solicitações, os serviços com escopo são descartados no final da solicitação.</span><span class="sxs-lookup"><span data-stu-id="0cc63-539">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

> [!WARNING]
> <span data-ttu-id="0cc63-540">Ao usar um serviço com escopo em um middleware, injete o serviço no método `Invoke` ou `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="0cc63-540">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="0cc63-541">Não injetar via [injeção de Construtor](xref:mvc/controllers/dependency-injection#constructor-injection) porque força o serviço a se comportar como um singleton.</span><span class="sxs-lookup"><span data-stu-id="0cc63-541">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="0cc63-542">Para obter mais informações, consulte <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="0cc63-542">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="0cc63-543">Singleton</span><span class="sxs-lookup"><span data-stu-id="0cc63-543">Singleton</span></span>

<span data-ttu-id="0cc63-544">Serviços de tempo de vida singleton (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) são criados na primeira solicitação (ou quando `Startup.ConfigureServices` é executado e uma instância é especificada com o registro do serviço).</span><span class="sxs-lookup"><span data-stu-id="0cc63-544">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="0cc63-545">Cada solicitação subsequente usa a mesma instância.</span><span class="sxs-lookup"><span data-stu-id="0cc63-545">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="0cc63-546">Se o aplicativo exigir um comportamento de singleton, recomendamos permitir que o contêiner do serviço gerencie o tempo de vida do serviço.</span><span class="sxs-lookup"><span data-stu-id="0cc63-546">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="0cc63-547">Não implemente o padrão de design singleton e forneça o código de usuário para gerenciar o tempo de vida do objeto na classe.</span><span class="sxs-lookup"><span data-stu-id="0cc63-547">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

<span data-ttu-id="0cc63-548">Em aplicativos que processam solicitações, os serviços singleton são descartados quando o <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> é Descartado no desligamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0cc63-548">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed at app shutdown.</span></span>

> [!WARNING]
> <span data-ttu-id="0cc63-549">É perigoso resolver um serviço com escopo de um singleton.</span><span class="sxs-lookup"><span data-stu-id="0cc63-549">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="0cc63-550">Pode fazer com que o serviço tenha um estado incorreto durante o processamento das solicitações seguintes.</span><span class="sxs-lookup"><span data-stu-id="0cc63-550">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="0cc63-551">Métodos de registro do serviço</span><span class="sxs-lookup"><span data-stu-id="0cc63-551">Service registration methods</span></span>

<span data-ttu-id="0cc63-552">Os métodos de extensão de registro de serviço oferecem sobrecargas que são úteis em cenários específicos.</span><span class="sxs-lookup"><span data-stu-id="0cc63-552">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="0cc63-553">Método</span><span class="sxs-lookup"><span data-stu-id="0cc63-553">Method</span></span> | <span data-ttu-id="0cc63-554">Automático</span><span class="sxs-lookup"><span data-stu-id="0cc63-554">Automatic</span></span><br><span data-ttu-id="0cc63-555">object</span><span class="sxs-lookup"><span data-stu-id="0cc63-555">object</span></span><br><span data-ttu-id="0cc63-556">descarte</span><span class="sxs-lookup"><span data-stu-id="0cc63-556">disposal</span></span> | <span data-ttu-id="0cc63-557">Vários</span><span class="sxs-lookup"><span data-stu-id="0cc63-557">Multiple</span></span><br><span data-ttu-id="0cc63-558">implementações</span><span class="sxs-lookup"><span data-stu-id="0cc63-558">implementations</span></span> | <span data-ttu-id="0cc63-559">Passar argumentos</span><span class="sxs-lookup"><span data-stu-id="0cc63-559">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="0cc63-560">Exemplo:</span><span class="sxs-lookup"><span data-stu-id="0cc63-560">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="0cc63-561">Sim</span><span class="sxs-lookup"><span data-stu-id="0cc63-561">Yes</span></span> | <span data-ttu-id="0cc63-562">Sim</span><span class="sxs-lookup"><span data-stu-id="0cc63-562">Yes</span></span> | <span data-ttu-id="0cc63-563">Não</span><span class="sxs-lookup"><span data-stu-id="0cc63-563">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="0cc63-564">Exemplos:</span><span class="sxs-lookup"><span data-stu-id="0cc63-564">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="0cc63-565">Sim</span><span class="sxs-lookup"><span data-stu-id="0cc63-565">Yes</span></span> | <span data-ttu-id="0cc63-566">Sim</span><span class="sxs-lookup"><span data-stu-id="0cc63-566">Yes</span></span> | <span data-ttu-id="0cc63-567">Sim</span><span class="sxs-lookup"><span data-stu-id="0cc63-567">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="0cc63-568">Exemplo:</span><span class="sxs-lookup"><span data-stu-id="0cc63-568">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="0cc63-569">Sim</span><span class="sxs-lookup"><span data-stu-id="0cc63-569">Yes</span></span> | <span data-ttu-id="0cc63-570">Não</span><span class="sxs-lookup"><span data-stu-id="0cc63-570">No</span></span> | <span data-ttu-id="0cc63-571">Não</span><span class="sxs-lookup"><span data-stu-id="0cc63-571">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="0cc63-572">Exemplos:</span><span class="sxs-lookup"><span data-stu-id="0cc63-572">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="0cc63-573">Não</span><span class="sxs-lookup"><span data-stu-id="0cc63-573">No</span></span> | <span data-ttu-id="0cc63-574">Sim</span><span class="sxs-lookup"><span data-stu-id="0cc63-574">Yes</span></span> | <span data-ttu-id="0cc63-575">Sim</span><span class="sxs-lookup"><span data-stu-id="0cc63-575">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="0cc63-576">Exemplos:</span><span class="sxs-lookup"><span data-stu-id="0cc63-576">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="0cc63-577">Não</span><span class="sxs-lookup"><span data-stu-id="0cc63-577">No</span></span> | <span data-ttu-id="0cc63-578">Não</span><span class="sxs-lookup"><span data-stu-id="0cc63-578">No</span></span> | <span data-ttu-id="0cc63-579">Sim</span><span class="sxs-lookup"><span data-stu-id="0cc63-579">Yes</span></span> |

<span data-ttu-id="0cc63-580">Para obter mais informações sobre o descarte de tipos, consulte a seção [Descarte de serviços](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="0cc63-580">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="0cc63-581">Um cenário comum para várias implementações é a [simulação de tipos para teste](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="0cc63-581">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="0cc63-582">Métodos `TryAdd{LIFETIME}` somente registrarão o serviço se ainda não houver uma implementação registrada.</span><span class="sxs-lookup"><span data-stu-id="0cc63-582">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="0cc63-583">No exemplo a seguir, a primeira linha registra `MyDependency` para `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="0cc63-583">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="0cc63-584">A segunda linha não tem nenhum efeito porque `IMyDependency` já tem uma implementação registrada:</span><span class="sxs-lookup"><span data-stu-id="0cc63-584">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="0cc63-585">Para obter mais informações, consulte:</span><span class="sxs-lookup"><span data-stu-id="0cc63-585">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="0cc63-586">Métodos [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) apenas registrarão o serviço se ainda não houver uma implementação *do mesmo tipo*.</span><span class="sxs-lookup"><span data-stu-id="0cc63-586">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="0cc63-587">Vários serviços são resolvidos via `IEnumerable<{SERVICE}>`.</span><span class="sxs-lookup"><span data-stu-id="0cc63-587">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="0cc63-588">Ao registrar os serviços, só convém ao desenvolvedor adicionar uma instância se até o momento nenhuma do mesmo tipo foi adicionada.</span><span class="sxs-lookup"><span data-stu-id="0cc63-588">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="0cc63-589">Em geral, esse método é usado por autores de biblioteca para evitar registrar duas cópias de uma instância no contêiner.</span><span class="sxs-lookup"><span data-stu-id="0cc63-589">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="0cc63-590">No exemplo a seguir, a primeira linha registra `MyDep` para `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="0cc63-590">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="0cc63-591">A segunda linha registra `MyDep` para `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="0cc63-591">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="0cc63-592">A terceira linha não tem nenhum efeito porque `IMyDep1` já tem uma implementação registrada de `MyDep`:</span><span class="sxs-lookup"><span data-stu-id="0cc63-592">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="0cc63-593">Comportamento da injeção de construtor</span><span class="sxs-lookup"><span data-stu-id="0cc63-593">Constructor injection behavior</span></span>

<span data-ttu-id="0cc63-594">Os serviços podem ser resolvidos por dois mecanismos:</span><span class="sxs-lookup"><span data-stu-id="0cc63-594">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="0cc63-595"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permite a criação de objeto sem registro de serviço no contêiner de injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="0cc63-595"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="0cc63-596">`ActivatorUtilities` é usado com abstrações voltadas ao usuário, como Auxiliares de Marca, controladores MVC e associadores de modelo.</span><span class="sxs-lookup"><span data-stu-id="0cc63-596">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="0cc63-597">Os construtores podem aceitar argumentos que não são fornecidos pela injeção de dependência, mas que precisam atribuir valores padrão.</span><span class="sxs-lookup"><span data-stu-id="0cc63-597">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="0cc63-598">Quando os serviços são resolvidos pelo `IServiceProvider` ou `ActivatorUtilities` , a [injeção de Construtor](xref:mvc/controllers/dependency-injection#constructor-injection) requer um construtor *público* .</span><span class="sxs-lookup"><span data-stu-id="0cc63-598">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="0cc63-599">Quando os serviços são resolvidos pelo `ActivatorUtilities` , a [injeção de Construtor](xref:mvc/controllers/dependency-injection#constructor-injection) requer que apenas um Construtor aplicável exista.</span><span class="sxs-lookup"><span data-stu-id="0cc63-599">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="0cc63-600">Há suporte para sobrecargas de construtor, mas somente uma sobrecarga pode existir, cujos argumentos podem ser todos atendidos pela injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="0cc63-600">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="0cc63-601">Contextos de Entity Framework</span><span class="sxs-lookup"><span data-stu-id="0cc63-601">Entity Framework contexts</span></span>

<span data-ttu-id="0cc63-602">Contextos do Entity Framework geralmente são adicionados ao contêiner de serviço usando o [tempo de vida com escopo](#service-lifetimes), pois o escopo de operações de banco de dados de aplicativo Web normalmente é para a solicitação do cliente.</span><span class="sxs-lookup"><span data-stu-id="0cc63-602">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="0cc63-603">O tempo de vida padrão será definido como escopo se um tempo de vida não for especificado por uma sobrecarga [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) ao registrar o contexto do banco de dados.</span><span class="sxs-lookup"><span data-stu-id="0cc63-603">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="0cc63-604">Serviços com um determinado tempo de vida não devem usar um contexto de banco de dados com um tempo de vida mais curto que aquele do serviço.</span><span class="sxs-lookup"><span data-stu-id="0cc63-604">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="0cc63-605">Opções de tempo de vida e de registro</span><span class="sxs-lookup"><span data-stu-id="0cc63-605">Lifetime and registration options</span></span>

<span data-ttu-id="0cc63-606">Para demonstrar a diferença entre as opções de tempo de vida e de registro, considere as interfaces a seguir, que representam tarefas como uma operação com um identificador exclusivo, `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="0cc63-606">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="0cc63-607">Dependendo de como o tempo de vida de um serviço de operações é configurado para as interfaces a seguir, o contêiner fornece a mesma instância do serviço, ou outra diferente, quando recebe uma solicitação de uma classe:</span><span class="sxs-lookup"><span data-stu-id="0cc63-607">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="0cc63-608">As interfaces são implementadas na classe `Operation`.</span><span class="sxs-lookup"><span data-stu-id="0cc63-608">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="0cc63-609">O construtor `Operation` gera um GUID, caso um não seja fornecido:</span><span class="sxs-lookup"><span data-stu-id="0cc63-609">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="0cc63-610">Há um `OperationService` registrado que depende de cada um dos outros `Operation` tipos.</span><span class="sxs-lookup"><span data-stu-id="0cc63-610">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="0cc63-611">Quando `OperationService` é solicitado por meio da injeção de dependência, ele recebe a uma nova instância de cada serviço, ou uma instância existente com base no tempo de vida do serviço dependente.</span><span class="sxs-lookup"><span data-stu-id="0cc63-611">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="0cc63-612">Quando os serviços temporários forem criados mediante solicitação do contêiner, o `OperationId` do serviço `IOperationTransient` será diferente do `OperationId` do `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="0cc63-612">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="0cc63-613">`OperationService` recebe uma nova instância da classe `IOperationTransient`.</span><span class="sxs-lookup"><span data-stu-id="0cc63-613">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="0cc63-614">A nova instância produz outro `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="0cc63-614">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="0cc63-615">Quando os serviços com escopo forem criados por solicitação do cliente, o `OperationId` do serviço `IOperationScoped` será o mesmo que o `OperationService` dentro de uma solicitação do cliente.</span><span class="sxs-lookup"><span data-stu-id="0cc63-615">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="0cc63-616">Em todas as solicitações do cliente, os dois serviços compartilham um valor de `OperationId` diferente.</span><span class="sxs-lookup"><span data-stu-id="0cc63-616">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="0cc63-617">Quando os serviços singleton e de instância singleton forem criados uma vez e usados em todas as solicitações de cliente e em todos os serviços, o `OperationId` será constante entre todas as solicitações de serviço.</span><span class="sxs-lookup"><span data-stu-id="0cc63-617">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="0cc63-618">Em `Startup.ConfigureServices`, cada tipo é adicionado ao contêiner de acordo com seu tempo de vida nomeado:</span><span class="sxs-lookup"><span data-stu-id="0cc63-618">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="0cc63-619">O serviço `IOperationSingletonInstance` está usando uma instância específica com uma ID conhecida de `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="0cc63-619">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="0cc63-620">Fica claro quando esse tipo está em uso (seu GUID contém apenas zeros).</span><span class="sxs-lookup"><span data-stu-id="0cc63-620">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="0cc63-621">O exemplo de aplicativo demonstra os tempos de vida do objeto dentro e entre solicitações individuais.</span><span class="sxs-lookup"><span data-stu-id="0cc63-621">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="0cc63-622">O exemplo de aplicativo `IndexModel` solicita cada tipo `IOperation` e o `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="0cc63-622">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="0cc63-623">Depois, a página exibe todos os valores de `OperationId` da classe de modelo de página e do serviço por meio de atribuições de propriedade:</span><span class="sxs-lookup"><span data-stu-id="0cc63-623">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="0cc63-624">As duas saídas a seguir mostram os resultados das duas solicitações:</span><span class="sxs-lookup"><span data-stu-id="0cc63-624">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="0cc63-625">**Primeira solicitação:**</span><span class="sxs-lookup"><span data-stu-id="0cc63-625">**First request:**</span></span>

<span data-ttu-id="0cc63-626">Operações do controlador:</span><span class="sxs-lookup"><span data-stu-id="0cc63-626">Controller operations:</span></span>

<span data-ttu-id="0cc63-627">Transitória: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="0cc63-627">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="0cc63-628">Com escopo: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="0cc63-628">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="0cc63-629">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="0cc63-629">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="0cc63-630">Instância: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="0cc63-630">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="0cc63-631">`OperationService` operações:</span><span class="sxs-lookup"><span data-stu-id="0cc63-631">`OperationService` operations:</span></span>

<span data-ttu-id="0cc63-632">Transitória: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="0cc63-632">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="0cc63-633">Com escopo: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="0cc63-633">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="0cc63-634">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="0cc63-634">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="0cc63-635">Instância: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="0cc63-635">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="0cc63-636">**Segunda solicitação:**</span><span class="sxs-lookup"><span data-stu-id="0cc63-636">**Second request:**</span></span>

<span data-ttu-id="0cc63-637">Operações do controlador:</span><span class="sxs-lookup"><span data-stu-id="0cc63-637">Controller operations:</span></span>

<span data-ttu-id="0cc63-638">Transitória: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="0cc63-638">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="0cc63-639">Com escopo: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="0cc63-639">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="0cc63-640">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="0cc63-640">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="0cc63-641">Instância: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="0cc63-641">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="0cc63-642">`OperationService` operações:</span><span class="sxs-lookup"><span data-stu-id="0cc63-642">`OperationService` operations:</span></span>

<span data-ttu-id="0cc63-643">Transitória: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="0cc63-643">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="0cc63-644">Com escopo: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="0cc63-644">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="0cc63-645">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="0cc63-645">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="0cc63-646">Instância: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="0cc63-646">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="0cc63-647">Observe qual dos valores de `OperationId` varia em uma solicitação, e entre as solicitações:</span><span class="sxs-lookup"><span data-stu-id="0cc63-647">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="0cc63-648">Os objetos *transitórios* sempre são diferentes.</span><span class="sxs-lookup"><span data-stu-id="0cc63-648">*Transient* objects are always different.</span></span> <span data-ttu-id="0cc63-649">O valor `OperationId` transitório da primeira e da segunda solicitações de cliente é diferente para as duas operações `OperationService` e em todas as solicitações de cliente.</span><span class="sxs-lookup"><span data-stu-id="0cc63-649">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="0cc63-650">Uma nova instância é fornecida para cada solicitação de serviço e solicitação de cliente.</span><span class="sxs-lookup"><span data-stu-id="0cc63-650">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="0cc63-651">Objetos *com escopo* são os mesmos em uma solicitação de cliente, mas diferentes entre solicitações de cliente.</span><span class="sxs-lookup"><span data-stu-id="0cc63-651">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="0cc63-652">Os pbjetos *singleton* são os mesmos para cada objeto e solicitação, independentemente de uma instância `Operation` ser fornecida em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="0cc63-652">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="0cc63-653">Chamar os serviços desde o principal</span><span class="sxs-lookup"><span data-stu-id="0cc63-653">Call services from main</span></span>

<span data-ttu-id="0cc63-654">Crie um <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> com [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) para resolver um serviço com escopo dentro do escopo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0cc63-654">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="0cc63-655">Essa abordagem é útil para acessar um serviço com escopo durante a inicialização para executar tarefas de inicialização.</span><span class="sxs-lookup"><span data-stu-id="0cc63-655">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="0cc63-656">O exemplo a seguir mostra como obter um contexto para o `MyScopedService` em `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="0cc63-656">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="0cc63-657">Validação de escopo</span><span class="sxs-lookup"><span data-stu-id="0cc63-657">Scope validation</span></span>

<span data-ttu-id="0cc63-658">Quando o aplicativo está em execução no ambiente de desenvolvimento, o provedor de serviço padrão executa verificações para saber se:</span><span class="sxs-lookup"><span data-stu-id="0cc63-658">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="0cc63-659">Os serviços com escopo não são resolvidos direta ou indiretamente pelo provedor de serviço raiz.</span><span class="sxs-lookup"><span data-stu-id="0cc63-659">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="0cc63-660">Os serviços com escopo não são injetados direta ou indiretamente em singletons.</span><span class="sxs-lookup"><span data-stu-id="0cc63-660">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="0cc63-661">O provedor de serviços raiz é criado quando <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> é chamado.</span><span class="sxs-lookup"><span data-stu-id="0cc63-661">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="0cc63-662">O tempo de vida do provedor de serviço raiz corresponde ao tempo de vida do aplicativo/servidor quando o provedor começa com o aplicativo e é descartado quando o aplicativo é desligado.</span><span class="sxs-lookup"><span data-stu-id="0cc63-662">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="0cc63-663">Os serviços com escopo são descartados pelo contêiner que os criou.</span><span class="sxs-lookup"><span data-stu-id="0cc63-663">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="0cc63-664">Se um serviço com escopo é criado no contêiner raiz, o tempo de vida do serviço é promovido efetivamente para singleton, porque ele só é descartado pelo contêiner raiz quando o aplicativo/servidor é desligado.</span><span class="sxs-lookup"><span data-stu-id="0cc63-664">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="0cc63-665">A validação dos escopos de serviço detecta essas situações quando `BuildServiceProvider` é chamado.</span><span class="sxs-lookup"><span data-stu-id="0cc63-665">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="0cc63-666">Para obter mais informações, consulte <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="0cc63-666">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>   

## <a name="request-services"></a><span data-ttu-id="0cc63-667">Serviços de solicitação</span><span class="sxs-lookup"><span data-stu-id="0cc63-667">Request Services</span></span>

<span data-ttu-id="0cc63-668">Os serviços disponíveis em uma solicitação do ASP.NET de `HttpContext` são expostos por meio da coleção [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).</span><span class="sxs-lookup"><span data-stu-id="0cc63-668">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="0cc63-669">Os Serviços de Solicitação representam os serviços configurados e solicitados como parte do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0cc63-669">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="0cc63-670">Quando os objetos especificam dependências, elas são atendidas pelos tipos encontrados em `RequestServices`, não em `ApplicationServices`.</span><span class="sxs-lookup"><span data-stu-id="0cc63-670">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="0cc63-671">Em geral, o aplicativo não deve usar essas propriedades diretamente.</span><span class="sxs-lookup"><span data-stu-id="0cc63-671">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="0cc63-672">Em vez disso, solicite os tipos exigidos pelas classes por meio de construtores de classe e permita que a estrutura injete as dependências.</span><span class="sxs-lookup"><span data-stu-id="0cc63-672">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="0cc63-673">Isso resulta em classes que são mais fáceis de testar.</span><span class="sxs-lookup"><span data-stu-id="0cc63-673">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="0cc63-674">Prefira solicitar dependências como parâmetros de construtor para acessar a coleção `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="0cc63-674">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="0cc63-675">Projetar serviços para injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="0cc63-675">Design services for dependency injection</span></span>

<span data-ttu-id="0cc63-676">As melhores práticas são:</span><span class="sxs-lookup"><span data-stu-id="0cc63-676">Best practices are to:</span></span>

* <span data-ttu-id="0cc63-677">Projetar serviços para usar a injeção de dependência a fim de obter suas dependências.</span><span class="sxs-lookup"><span data-stu-id="0cc63-677">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="0cc63-678">Evite classes e membros com estado e estáticos.</span><span class="sxs-lookup"><span data-stu-id="0cc63-678">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="0cc63-679">Crie aplicativos para usar os serviços singleton, o que evita a criação de estado global.</span><span class="sxs-lookup"><span data-stu-id="0cc63-679">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="0cc63-680">Evite a instanciação direta das classes dependentes em serviços.</span><span class="sxs-lookup"><span data-stu-id="0cc63-680">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="0cc63-681">A instanciação direta acopla o código a uma implementação específica.</span><span class="sxs-lookup"><span data-stu-id="0cc63-681">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="0cc63-682">Verifique as classes de aplicativo pequenas, bem fatoradas e facilmente testadas.</span><span class="sxs-lookup"><span data-stu-id="0cc63-682">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="0cc63-683">Se parecer que uma classe tem muitas dependências injetadas, normalmente é um sinal de que a classe tem muitas responsabilidades e está violando o [Princípio da Responsabilidade Única (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="0cc63-683">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="0cc63-684">Tente refatorar a classe movendo algumas de suas responsabilidades para uma nova classe.</span><span class="sxs-lookup"><span data-stu-id="0cc63-684">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="0cc63-685">Tenha em mente que as classes Razor de modelo de página de páginas e as classes do controlador MVC devem se concentrar nas preocupações da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="0cc63-685">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="0cc63-686">Os detalhes de implementação de acesso aos dados e as regras de negócios devem ser mantidos em classes apropriadas a esses [interesses separados](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="0cc63-686">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="0cc63-687">Descarte de serviços</span><span class="sxs-lookup"><span data-stu-id="0cc63-687">Disposal of services</span></span>

<span data-ttu-id="0cc63-688">O contêiner chama <xref:System.IDisposable.Dispose*> para os tipos <xref:System.IDisposable> criados por ele.</span><span class="sxs-lookup"><span data-stu-id="0cc63-688">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="0cc63-689">Se uma instância for adicionada ao contêiner pelo código do usuário, ele não será descartado automaticamente.</span><span class="sxs-lookup"><span data-stu-id="0cc63-689">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="0cc63-690">No exemplo a seguir, os serviços são criados pelo contêiner de serviço e descartados automaticamente:</span><span class="sxs-lookup"><span data-stu-id="0cc63-690">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="0cc63-691">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="0cc63-691">In the following example:</span></span>

* <span data-ttu-id="0cc63-692">As instâncias de serviço não são criadas pelo contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="0cc63-692">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="0cc63-693">Os tempos de vida de serviço pretendidos não são conhecidos pela estrutura.</span><span class="sxs-lookup"><span data-stu-id="0cc63-693">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="0cc63-694">A estrutura não descarta os serviços automaticamente.</span><span class="sxs-lookup"><span data-stu-id="0cc63-694">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="0cc63-695">Se os serviços não forem explicitamente descartados no código do desenvolvedor, eles persistirão até que o aplicativo seja desligado.</span><span class="sxs-lookup"><span data-stu-id="0cc63-695">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="0cc63-696">Diretrizes de IDisposable para instâncias transitórias e compartilhadas</span><span class="sxs-lookup"><span data-stu-id="0cc63-696">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="0cc63-697">Tempo de vida transitório, limitado</span><span class="sxs-lookup"><span data-stu-id="0cc63-697">Transient, limited lifetime</span></span>

<span data-ttu-id="0cc63-698">**Cenário**</span><span class="sxs-lookup"><span data-stu-id="0cc63-698">**Scenario**</span></span>

<span data-ttu-id="0cc63-699">O aplicativo requer uma <xref:System.IDisposable> instância com um tempo de vida transitório para qualquer um dos seguintes cenários:</span><span class="sxs-lookup"><span data-stu-id="0cc63-699">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="0cc63-700">A instância é resolvida no escopo raiz.</span><span class="sxs-lookup"><span data-stu-id="0cc63-700">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="0cc63-701">A instância deve ser descartada antes do término do escopo.</span><span class="sxs-lookup"><span data-stu-id="0cc63-701">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="0cc63-702">**Solução**</span><span class="sxs-lookup"><span data-stu-id="0cc63-702">**Solution**</span></span>

<span data-ttu-id="0cc63-703">Use o padrão de fábrica para criar uma instância fora do escopo pai.</span><span class="sxs-lookup"><span data-stu-id="0cc63-703">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="0cc63-704">Nessa situação, o aplicativo geralmente teria um `Create` método que chamasse o construtor do tipo final diretamente.</span><span class="sxs-lookup"><span data-stu-id="0cc63-704">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="0cc63-705">Se o tipo final tiver outras dependências, a fábrica poderá:</span><span class="sxs-lookup"><span data-stu-id="0cc63-705">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="0cc63-706">Receber um <xref:System.IServiceProvider> em seu construtor.</span><span class="sxs-lookup"><span data-stu-id="0cc63-706">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="0cc63-707">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> para instanciar a instância fora do contêiner, ao usar o contêiner para suas dependências.</span><span class="sxs-lookup"><span data-stu-id="0cc63-707">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="0cc63-708">Instância compartilhada, tempo de vida limitado</span><span class="sxs-lookup"><span data-stu-id="0cc63-708">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="0cc63-709">**Cenário**</span><span class="sxs-lookup"><span data-stu-id="0cc63-709">**Scenario**</span></span>

<span data-ttu-id="0cc63-710">O aplicativo requer uma <xref:System.IDisposable> instância compartilhada entre vários serviços, mas o <xref:System.IDisposable> deve ter um tempo de vida limitado.</span><span class="sxs-lookup"><span data-stu-id="0cc63-710">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="0cc63-711">**Solução**</span><span class="sxs-lookup"><span data-stu-id="0cc63-711">**Solution**</span></span>

<span data-ttu-id="0cc63-712">Registre a instância com um tempo de vida de escopo.</span><span class="sxs-lookup"><span data-stu-id="0cc63-712">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="0cc63-713">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> para iniciar e criar um novo <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> .</span><span class="sxs-lookup"><span data-stu-id="0cc63-713">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="0cc63-714">Use o escopo <xref:System.IServiceProvider> para obter os serviços necessários.</span><span class="sxs-lookup"><span data-stu-id="0cc63-714">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="0cc63-715">Descarte o escopo quando o tempo de vida deve terminar.</span><span class="sxs-lookup"><span data-stu-id="0cc63-715">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="0cc63-716">Diretrizes gerais</span><span class="sxs-lookup"><span data-stu-id="0cc63-716">General Guidelines</span></span>

* <span data-ttu-id="0cc63-717">Não registre <xref:System.IDisposable> instâncias com um escopo transitório.</span><span class="sxs-lookup"><span data-stu-id="0cc63-717">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="0cc63-718">Em vez disso, use o padrão de fábrica.</span><span class="sxs-lookup"><span data-stu-id="0cc63-718">Use the factory pattern instead.</span></span>
* <span data-ttu-id="0cc63-719">Não resolva instâncias transitórias ou com escopo definido <xref:System.IDisposable> no escopo raiz.</span><span class="sxs-lookup"><span data-stu-id="0cc63-719">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="0cc63-720">A única exceção geral é quando o aplicativo cria/recria e descarta o <xref:System.IServiceProvider> , que não é um padrão ideal.</span><span class="sxs-lookup"><span data-stu-id="0cc63-720">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="0cc63-721">O recebimento de uma <xref:System.IDisposable> dependência por meio de di não exige que o receptor se implemente <xref:System.IDisposable> .</span><span class="sxs-lookup"><span data-stu-id="0cc63-721">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="0cc63-722">O receptor da <xref:System.IDisposable> dependência não deve chamar <xref:System.IDisposable.Dispose%2A> essa dependência.</span><span class="sxs-lookup"><span data-stu-id="0cc63-722">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="0cc63-723">Os escopos devem ser usados para controlar o tempo de vida dos serviços.</span><span class="sxs-lookup"><span data-stu-id="0cc63-723">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="0cc63-724">Os escopos não são hierárquicos e não há nenhuma conexão especial entre escopos.</span><span class="sxs-lookup"><span data-stu-id="0cc63-724">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="0cc63-725">Substituição do contêiner de serviço padrão</span><span class="sxs-lookup"><span data-stu-id="0cc63-725">Default service container replacement</span></span>

<span data-ttu-id="0cc63-726">O contêiner de serviço interno foi projetado para atender às necessidades da estrutura e da maioria dos aplicativos de consumidor.</span><span class="sxs-lookup"><span data-stu-id="0cc63-726">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="0cc63-727">É recomendável usar o contêiner interno, a menos que você precise de um recurso específico ao qual o contêiner interno não ofereça suporte, como:</span><span class="sxs-lookup"><span data-stu-id="0cc63-727">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="0cc63-728">Injeção de propriedade</span><span class="sxs-lookup"><span data-stu-id="0cc63-728">Property injection</span></span>
* <span data-ttu-id="0cc63-729">Injeção com base no nome</span><span class="sxs-lookup"><span data-stu-id="0cc63-729">Injection based on name</span></span>
* <span data-ttu-id="0cc63-730">Contêineres filho</span><span class="sxs-lookup"><span data-stu-id="0cc63-730">Child containers</span></span>
* <span data-ttu-id="0cc63-731">Gerenciamento de tempo de vida personalizado</span><span class="sxs-lookup"><span data-stu-id="0cc63-731">Custom lifetime management</span></span>
* <span data-ttu-id="0cc63-732">Suporte de `Func<T>` para inicialização lenta</span><span class="sxs-lookup"><span data-stu-id="0cc63-732">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="0cc63-733">Registro baseado em Convenção</span><span class="sxs-lookup"><span data-stu-id="0cc63-733">Convention-based registration</span></span>

<span data-ttu-id="0cc63-734">Os seguintes contêineres de terceiros podem ser usados com aplicativos ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="0cc63-734">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="0cc63-735">Autofac</span><span class="sxs-lookup"><span data-stu-id="0cc63-735">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="0cc63-736">DryIoc</span><span class="sxs-lookup"><span data-stu-id="0cc63-736">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="0cc63-737">Grace</span><span class="sxs-lookup"><span data-stu-id="0cc63-737">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="0cc63-738">LightInject</span><span class="sxs-lookup"><span data-stu-id="0cc63-738">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="0cc63-739">Lamar</span><span class="sxs-lookup"><span data-stu-id="0cc63-739">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="0cc63-740">Stashbox</span><span class="sxs-lookup"><span data-stu-id="0cc63-740">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="0cc63-741">Unity</span><span class="sxs-lookup"><span data-stu-id="0cc63-741">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="0cc63-742">Acesso thread-safe</span><span class="sxs-lookup"><span data-stu-id="0cc63-742">Thread safety</span></span>

<span data-ttu-id="0cc63-743">Crie serviços singleton thread-safe.</span><span class="sxs-lookup"><span data-stu-id="0cc63-743">Create thread-safe singleton services.</span></span> <span data-ttu-id="0cc63-744">Se um serviço singleton tiver uma dependência em um serviço transitório, o serviço transitório também precisará ter acesso thread-safe, dependendo de como ele é usado pelo singleton.</span><span class="sxs-lookup"><span data-stu-id="0cc63-744">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="0cc63-745">O método de fábrica de um único serviço, como o segundo argumento para [addsingleton \<TService> (IServiceCollection, Func \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), não precisa ser thread-safe.</span><span class="sxs-lookup"><span data-stu-id="0cc63-745">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="0cc63-746">Como um construtor do tipo (`static`), ele tem garantia de ser chamado uma vez por um único thread.</span><span class="sxs-lookup"><span data-stu-id="0cc63-746">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="0cc63-747">Recomendações</span><span class="sxs-lookup"><span data-stu-id="0cc63-747">Recommendations</span></span>

* <span data-ttu-id="0cc63-748">A resolução de serviço baseada em `async/await` e `Task` não é compatível.</span><span class="sxs-lookup"><span data-stu-id="0cc63-748">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="0cc63-749">O C# não é compatível com os construtores assíncronos. Portanto, o padrão recomendado é usar os métodos assíncronos após a resolução síncrona do serviço.</span><span class="sxs-lookup"><span data-stu-id="0cc63-749">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="0cc63-750">Evite armazenar dados e a configuração diretamente no contêiner do serviço.</span><span class="sxs-lookup"><span data-stu-id="0cc63-750">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="0cc63-751">Por exemplo, o carrinho de compras de um usuário normalmente não deve ser adicionado ao contêiner do serviço.</span><span class="sxs-lookup"><span data-stu-id="0cc63-751">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="0cc63-752">A configuração deve usar o [padrão de opções](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="0cc63-752">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="0cc63-753">Da mesma forma, evite objetos de "suporte de dados" que existem somente para permitir o acesso a outro objeto.</span><span class="sxs-lookup"><span data-stu-id="0cc63-753">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="0cc63-754">É melhor solicitar o item real por meio da DI.</span><span class="sxs-lookup"><span data-stu-id="0cc63-754">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="0cc63-755">Evite o acesso estático aos serviços.</span><span class="sxs-lookup"><span data-stu-id="0cc63-755">Avoid static access to services.</span></span> <span data-ttu-id="0cc63-756">Por exemplo, Evite digitar estaticamente [IApplicationBuilder. ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) para uso em outro lugar.</span><span class="sxs-lookup"><span data-stu-id="0cc63-756">For example, avoid statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere.</span></span>

* <span data-ttu-id="0cc63-757">Evite usar o *padrão de localizador de serviço*, que combina a [inversão de estratégias de controle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) .</span><span class="sxs-lookup"><span data-stu-id="0cc63-757">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
  * <span data-ttu-id="0cc63-758">Não invoque <xref:System.IServiceProvider.GetService*> para obter uma instância de serviço quando você pode usar di em vez disso:</span><span class="sxs-lookup"><span data-stu-id="0cc63-758">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="0cc63-759">**Incorreto:**</span><span class="sxs-lookup"><span data-stu-id="0cc63-759">**Incorrect:**</span></span>

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
   
    <span data-ttu-id="0cc63-760">**Correto**:</span><span class="sxs-lookup"><span data-stu-id="0cc63-760">**Correct**:</span></span>

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

* <span data-ttu-id="0cc63-761">Evite injetar uma fábrica que resolva dependências em tempo de execução usando <xref:System.IServiceProvider.GetService*> .</span><span class="sxs-lookup"><span data-stu-id="0cc63-761">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>
* <span data-ttu-id="0cc63-762">Evite o acesso estático a `HttpContext` (por exemplo, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="0cc63-762">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="0cc63-763">Como todos os conjuntos de recomendações, talvez você encontre situações em que é necessário ignorar uma recomendação.</span><span class="sxs-lookup"><span data-stu-id="0cc63-763">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="0cc63-764">As exceções são raras, principalmente os casos especiais dentro da própria estrutura.</span><span class="sxs-lookup"><span data-stu-id="0cc63-764">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="0cc63-765">A DI é uma *alternativa* aos padrões de acesso a objeto estático/global.</span><span class="sxs-lookup"><span data-stu-id="0cc63-765">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="0cc63-766">Talvez você não obtenha os benefícios da DI se combiná-lo com o acesso a objeto estático.</span><span class="sxs-lookup"><span data-stu-id="0cc63-766">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0cc63-767">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="0cc63-767">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="0cc63-768">Quatro maneiras de descartar IDisposables em ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0cc63-768">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="0cc63-769">Como gravar um código limpo no ASP.NET Core com injeção de dependência (MSDN)</span><span class="sxs-lookup"><span data-stu-id="0cc63-769">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="0cc63-770">Princípio de Dependências Explícitas</span><span class="sxs-lookup"><span data-stu-id="0cc63-770">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="0cc63-771">Inversão de Contêineres de Controle e o padrão de Injeção de Dependência (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="0cc63-771">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="0cc63-772">Como registrar um serviço com várias interfaces na DI do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0cc63-772">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end
