---
title: Injeção de dependência no ASP.NET Core
author: rick-anderson
description: Saiba como o ASP.NET Core implementa a injeção de dependência e como usá-la.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/21/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/dependency-injection
ms.openlocfilehash: fcfa9e93228cdf71b33e67aeab38fdd9a3295b75
ms.sourcegitcommit: 84150702757cf7a7b839485382420e8db8e92b9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/05/2020
ms.locfileid: "87819211"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="daccf-103">Injeção de dependência no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="daccf-103">Dependency injection in ASP.NET Core</span></span>

<span data-ttu-id="daccf-104">Por [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com)e [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="daccf-104">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="daccf-105">O ASP.NET Core é compatível com o padrão de design de software de DI (injeção de dependência), que é uma técnica para alcançar a [IoC (Inversão de Controle)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) entre classes e suas dependências.</span><span class="sxs-lookup"><span data-stu-id="daccf-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="daccf-106">Para obter mais informações específicas sobre injeção de dependência em controladores de MVC, consulte <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="daccf-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="daccf-107">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="daccf-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="daccf-108">Visão geral da injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="daccf-108">Overview of dependency injection</span></span>

<span data-ttu-id="daccf-109">Uma *dependência* é qualquer objeto exigido por outro objeto.</span><span class="sxs-lookup"><span data-stu-id="daccf-109">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="daccf-110">Examine a classe `MyDependency` a seguir com um método `WriteMessage` do qual outras classes em um aplicativo dependem:</span><span class="sxs-lookup"><span data-stu-id="daccf-110">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="daccf-111">Uma instância da classe `MyDependency` pode ser criada para tornar o método `WriteMessage` disponível para uma classe.</span><span class="sxs-lookup"><span data-stu-id="daccf-111">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="daccf-112">A classe  é uma dependência da classe:</span><span class="sxs-lookup"><span data-stu-id="daccf-112">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="daccf-113">A classe cria e depende diretamente da instância `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="daccf-113">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="daccf-114">As dependências de código (como no exemplo anterior) são problemáticas e devem ser evitadas por estes motivos:</span><span class="sxs-lookup"><span data-stu-id="daccf-114">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="daccf-115">Para substituir `MyDependency` por uma implementação diferente, a classe deve ser modificada.</span><span class="sxs-lookup"><span data-stu-id="daccf-115">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="daccf-116">Se `MyDependency` tiver dependências, elas deverão ser configuradas pela classe.</span><span class="sxs-lookup"><span data-stu-id="daccf-116">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="daccf-117">Em um projeto grande com várias classes dependendo da `MyDependency`, o código de configuração fica pulverizado por todo o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="daccf-117">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="daccf-118">É difícil testar a unidade dessa implementação.</span><span class="sxs-lookup"><span data-stu-id="daccf-118">This implementation is difficult to unit test.</span></span> <span data-ttu-id="daccf-119">O aplicativo deve usar uma simulação ou stub da classe `MyDependency`, o que não é possível com essa abordagem.</span><span class="sxs-lookup"><span data-stu-id="daccf-119">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="daccf-120">Injeção de dependência trata desses problemas da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="daccf-120">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="daccf-121">O uso de uma interface ou classe base para abstrair a implementação da dependência.</span><span class="sxs-lookup"><span data-stu-id="daccf-121">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="daccf-122">Registrando a dependência em um contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="daccf-122">Registration of the dependency in a service container.</span></span> <span data-ttu-id="daccf-123">O ASP.NET Core fornece um contêiner de serviço interno, o <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="daccf-123">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="daccf-124">Os serviços são registrados no método `Startup.ConfigureServices` do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="daccf-124">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="daccf-125">*Injeção* do serviço no construtor da classe na qual ele é usado.</span><span class="sxs-lookup"><span data-stu-id="daccf-125">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="daccf-126">A estrutura assume a responsabilidade de criar uma instância da dependência e de descartá-la quando não for mais necessária.</span><span class="sxs-lookup"><span data-stu-id="daccf-126">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="daccf-127">No [exemplo de aplicativo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), a interface `IMyDependency` define um método que o serviço fornece ao aplicativo:</span><span class="sxs-lookup"><span data-stu-id="daccf-127">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="daccf-128">Essa interface é implementada por um tipo concreto, `MyDependency`:</span><span class="sxs-lookup"><span data-stu-id="daccf-128">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="daccf-129">`MyDependency` solicita um <xref:Microsoft.Extensions.Logging.ILogger`1> em seu construtor.</span><span class="sxs-lookup"><span data-stu-id="daccf-129">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="daccf-130">Não é incomum usar a injeção de dependência de uma maneira encadeada.</span><span class="sxs-lookup"><span data-stu-id="daccf-130">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="daccf-131">Por sua vez, cada dependência solicitada solicita suas próprias dependências.</span><span class="sxs-lookup"><span data-stu-id="daccf-131">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="daccf-132">O contêiner resolve as dependências no grafo e retorna o serviço totalmente resolvido.</span><span class="sxs-lookup"><span data-stu-id="daccf-132">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="daccf-133">O conjunto de dependências que precisa ser resolvido normalmente é chamado de *árvore de dependência*, *grafo de dependência* ou *grafo de objeto*.</span><span class="sxs-lookup"><span data-stu-id="daccf-133">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="daccf-134">`IMyDependency` e `ILogger<TCategoryName>` devem ser registrados no contêiner do serviço.</span><span class="sxs-lookup"><span data-stu-id="daccf-134">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="daccf-135">`IMyDependency` está registrado em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="daccf-135">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="daccf-136">`ILogger<TCategoryName>` é registrado pela infraestrutura de abstrações de registro em log, portanto, é um [serviço fornecido por estrutura](#framework-provided-services) registrado por padrão pela estrutura.</span><span class="sxs-lookup"><span data-stu-id="daccf-136">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="daccf-137">O contêiner resolve `ILogger<TCategoryName>` aproveitando os [tipos abertos (genéricos)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminando a necessidade de registrar todo [tipo construído (genérico)](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span><span class="sxs-lookup"><span data-stu-id="daccf-137">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="daccf-138">No exemplo de aplicativo, o serviço `IMyDependency` está registrado com o tipo concreto `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="daccf-138">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="daccf-139">O registro define o escopo do tempo de vida do serviço para o tempo de vida de uma única solicitação.</span><span class="sxs-lookup"><span data-stu-id="daccf-139">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="daccf-140">Descreveremos posteriormente neste tópico os [tempos de vida do serviço](#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="daccf-140">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="daccf-141">Cada método de extensão `services.Add{SERVICE_NAME}` adiciona (e possivelmente configura) serviços.</span><span class="sxs-lookup"><span data-stu-id="daccf-141">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="daccf-142">Por exemplo, `services.AddMvc()` adiciona as páginas de serviços Razor e a MVC necessária.</span><span class="sxs-lookup"><span data-stu-id="daccf-142">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="daccf-143">Recomendamos que os aplicativos sigam essa convenção.</span><span class="sxs-lookup"><span data-stu-id="daccf-143">We recommended that apps follow this convention.</span></span> <span data-ttu-id="daccf-144">Coloque os métodos de extensão no namespace [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) para encapsular grupos de registros de serviço.</span><span class="sxs-lookup"><span data-stu-id="daccf-144">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="daccf-145">Se o construtor do serviço exigir um [tipo interno](/dotnet/csharp/language-reference/keywords/built-in-types-table), como `string`, o tipo poderá ser injetado usando a [configuração](xref:fundamentals/configuration/index) ou o [padrão de opções](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="daccf-145">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="daccf-146">Uma instância do serviço é solicitada por meio do construtor de uma classe, onde o serviço é usado e atribuído a um campo particular.</span><span class="sxs-lookup"><span data-stu-id="daccf-146">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="daccf-147">O campo é usado para acessar o serviço conforme o necessário na classe.</span><span class="sxs-lookup"><span data-stu-id="daccf-147">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="daccf-148">No exemplo de aplicativo, a instância `IMyDependency` é solicitada e usada para chamar o método `WriteMessage` do serviço:</span><span class="sxs-lookup"><span data-stu-id="daccf-148">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="daccf-149">Serviços injetados na inicialização</span><span class="sxs-lookup"><span data-stu-id="daccf-149">Services injected into Startup</span></span>

<span data-ttu-id="daccf-150">Somente os seguintes tipos de serviço podem ser injetados no `Startup` Construtor ao usar o host genérico ( <xref:Microsoft.Extensions.Hosting.IHostBuilder> ):</span><span class="sxs-lookup"><span data-stu-id="daccf-150">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="daccf-151">Os serviços podem ser injetados em `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="daccf-151">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="daccf-152">Para obter mais informações, consulte <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="daccf-152">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="daccf-153">Serviços fornecidos pela estrutura</span><span class="sxs-lookup"><span data-stu-id="daccf-153">Framework-provided services</span></span>

<span data-ttu-id="daccf-154">O `Startup.ConfigureServices` método é responsável por definir os serviços que o aplicativo usa, incluindo recursos de plataforma, como Entity Framework Core e ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="daccf-154">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="daccf-155">Inicialmente, o `IServiceCollection` fornecido para o `ConfigureServices` tem serviços definidos pela estrutura, dependendo de [como o host foi configurado](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="daccf-155">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="daccf-156">Não é incomum um aplicativo baseado em um modelo de ASP.NET Core ter centenas de serviços registrados pela estrutura.</span><span class="sxs-lookup"><span data-stu-id="daccf-156">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="daccf-157">Uma pequena amostra de serviços registrados na estrutura é listada na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="daccf-157">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="daccf-158">Tipo de Serviço</span><span class="sxs-lookup"><span data-stu-id="daccf-158">Service Type</span></span> | <span data-ttu-id="daccf-159">Tempo de vida</span><span class="sxs-lookup"><span data-stu-id="daccf-159">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="daccf-160">Transitório</span><span class="sxs-lookup"><span data-stu-id="daccf-160">Transient</span></span> |
| `IHostApplicationLifetime` | <span data-ttu-id="daccf-161">Singleton</span><span class="sxs-lookup"><span data-stu-id="daccf-161">Singleton</span></span> |
| `IWebHostEnvironment` | <span data-ttu-id="daccf-162">Singleton</span><span class="sxs-lookup"><span data-stu-id="daccf-162">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="daccf-163">Singleton</span><span class="sxs-lookup"><span data-stu-id="daccf-163">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="daccf-164">Transitório</span><span class="sxs-lookup"><span data-stu-id="daccf-164">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="daccf-165">Singleton</span><span class="sxs-lookup"><span data-stu-id="daccf-165">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="daccf-166">Transitório</span><span class="sxs-lookup"><span data-stu-id="daccf-166">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="daccf-167">Singleton</span><span class="sxs-lookup"><span data-stu-id="daccf-167">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="daccf-168">Singleton</span><span class="sxs-lookup"><span data-stu-id="daccf-168">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="daccf-169">Singleton</span><span class="sxs-lookup"><span data-stu-id="daccf-169">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="daccf-170">Transitório</span><span class="sxs-lookup"><span data-stu-id="daccf-170">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="daccf-171">Singleton</span><span class="sxs-lookup"><span data-stu-id="daccf-171">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="daccf-172">Singleton</span><span class="sxs-lookup"><span data-stu-id="daccf-172">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="daccf-173">Singleton</span><span class="sxs-lookup"><span data-stu-id="daccf-173">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="daccf-174">Registrar serviços adicionais com métodos de extensão</span><span class="sxs-lookup"><span data-stu-id="daccf-174">Register additional services with extension methods</span></span>

<span data-ttu-id="daccf-175">Quando um método de extensão de coleta do serviço estiver disponível para registrar um serviço (e seus serviços dependentes, se for necessário), a convenção é usar um único método de extensão `Add{SERVICE_NAME}` para registrar todos os serviços exigidos por esse serviço.</span><span class="sxs-lookup"><span data-stu-id="daccf-175">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="daccf-176">O código a seguir é um exemplo de como adicionar serviços adicionais ao contêiner usando os métodos de extensão [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) e <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> :</span><span class="sxs-lookup"><span data-stu-id="daccf-176">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="daccf-177">Para saber mais, confira a classe <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> na documentação da API.</span><span class="sxs-lookup"><span data-stu-id="daccf-177">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="daccf-178">Tempos de vida do serviço</span><span class="sxs-lookup"><span data-stu-id="daccf-178">Service lifetimes</span></span>

<span data-ttu-id="daccf-179">Escolha um tempo de vida apropriado para cada serviço registrado.</span><span class="sxs-lookup"><span data-stu-id="daccf-179">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="daccf-180">Os serviços do ASP.NET Core podem ser configurados com os seguintes tempos de vida:</span><span class="sxs-lookup"><span data-stu-id="daccf-180">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="daccf-181">Transitório</span><span class="sxs-lookup"><span data-stu-id="daccf-181">Transient</span></span>

<span data-ttu-id="daccf-182">Serviços temporários de tempo de vida (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) são criados cada vez que são solicitados pelo contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="daccf-182">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="daccf-183">Esse tempo de vida funciona melhor para serviços leves e sem estado.</span><span class="sxs-lookup"><span data-stu-id="daccf-183">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="daccf-184">Em aplicativos que processam solicitações, os serviços transitórios são descartados no final da solicitação.</span><span class="sxs-lookup"><span data-stu-id="daccf-184">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="daccf-185">Com escopo</span><span class="sxs-lookup"><span data-stu-id="daccf-185">Scoped</span></span>

<span data-ttu-id="daccf-186">Os serviços com tempo de vida (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) com escopo são criados uma vez por solicitação de cliente (conexão).</span><span class="sxs-lookup"><span data-stu-id="daccf-186">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="daccf-187">Em aplicativos que processam solicitações, os serviços com escopo são descartados no final da solicitação.</span><span class="sxs-lookup"><span data-stu-id="daccf-187">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

> [!WARNING]
> <span data-ttu-id="daccf-188">Ao usar um serviço com escopo em um middleware, injete o serviço no método `Invoke` ou `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="daccf-188">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="daccf-189">Não injetar via [injeção de Construtor](xref:mvc/controllers/dependency-injection#constructor-injection) porque força o serviço a se comportar como um singleton.</span><span class="sxs-lookup"><span data-stu-id="daccf-189">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="daccf-190">Para obter mais informações, consulte <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="daccf-190">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="daccf-191">Singleton</span><span class="sxs-lookup"><span data-stu-id="daccf-191">Singleton</span></span>

<span data-ttu-id="daccf-192">Serviços de tempo de vida singleton (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) são criados na primeira solicitação (ou quando `Startup.ConfigureServices` é executado e uma instância é especificada com o registro do serviço).</span><span class="sxs-lookup"><span data-stu-id="daccf-192">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="daccf-193">Cada solicitação subsequente usa a mesma instância.</span><span class="sxs-lookup"><span data-stu-id="daccf-193">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="daccf-194">Se o aplicativo exigir um comportamento de singleton, recomendamos permitir que o contêiner do serviço gerencie o tempo de vida do serviço.</span><span class="sxs-lookup"><span data-stu-id="daccf-194">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="daccf-195">Não implemente o padrão de design singleton e forneça o código de usuário para gerenciar o tempo de vida do objeto na classe.</span><span class="sxs-lookup"><span data-stu-id="daccf-195">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

<span data-ttu-id="daccf-196">Em aplicativos que processam solicitações, os serviços singleton são descartados quando o <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> é Descartado no desligamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="daccf-196">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed at app shutdown.</span></span>

> [!WARNING]
> <span data-ttu-id="daccf-197">É perigoso resolver um serviço com escopo de um singleton.</span><span class="sxs-lookup"><span data-stu-id="daccf-197">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="daccf-198">Pode fazer com que o serviço tenha um estado incorreto durante o processamento das solicitações seguintes.</span><span class="sxs-lookup"><span data-stu-id="daccf-198">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="daccf-199">Métodos de registro do serviço</span><span class="sxs-lookup"><span data-stu-id="daccf-199">Service registration methods</span></span>

<span data-ttu-id="daccf-200">Os métodos de extensão de registro de serviço oferecem sobrecargas que são úteis em cenários específicos.</span><span class="sxs-lookup"><span data-stu-id="daccf-200">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="daccf-201">Método</span><span class="sxs-lookup"><span data-stu-id="daccf-201">Method</span></span> | <span data-ttu-id="daccf-202">Automática</span><span class="sxs-lookup"><span data-stu-id="daccf-202">Automatic</span></span><br><span data-ttu-id="daccf-203">object</span><span class="sxs-lookup"><span data-stu-id="daccf-203">object</span></span><br><span data-ttu-id="daccf-204">descarte</span><span class="sxs-lookup"><span data-stu-id="daccf-204">disposal</span></span> | <span data-ttu-id="daccf-205">Vários</span><span class="sxs-lookup"><span data-stu-id="daccf-205">Multiple</span></span><br><span data-ttu-id="daccf-206">implementações</span><span class="sxs-lookup"><span data-stu-id="daccf-206">implementations</span></span> | <span data-ttu-id="daccf-207">Passar argumentos</span><span class="sxs-lookup"><span data-stu-id="daccf-207">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="daccf-208">Exemplo:</span><span class="sxs-lookup"><span data-stu-id="daccf-208">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="daccf-209">Sim</span><span class="sxs-lookup"><span data-stu-id="daccf-209">Yes</span></span> | <span data-ttu-id="daccf-210">Sim</span><span class="sxs-lookup"><span data-stu-id="daccf-210">Yes</span></span> | <span data-ttu-id="daccf-211">Não</span><span class="sxs-lookup"><span data-stu-id="daccf-211">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="daccf-212">Exemplos:</span><span class="sxs-lookup"><span data-stu-id="daccf-212">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="daccf-213">Sim</span><span class="sxs-lookup"><span data-stu-id="daccf-213">Yes</span></span> | <span data-ttu-id="daccf-214">Sim</span><span class="sxs-lookup"><span data-stu-id="daccf-214">Yes</span></span> | <span data-ttu-id="daccf-215">Sim</span><span class="sxs-lookup"><span data-stu-id="daccf-215">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="daccf-216">Exemplo:</span><span class="sxs-lookup"><span data-stu-id="daccf-216">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="daccf-217">Sim</span><span class="sxs-lookup"><span data-stu-id="daccf-217">Yes</span></span> | <span data-ttu-id="daccf-218">Não</span><span class="sxs-lookup"><span data-stu-id="daccf-218">No</span></span> | <span data-ttu-id="daccf-219">Não</span><span class="sxs-lookup"><span data-stu-id="daccf-219">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="daccf-220">Exemplos:</span><span class="sxs-lookup"><span data-stu-id="daccf-220">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="daccf-221">Não</span><span class="sxs-lookup"><span data-stu-id="daccf-221">No</span></span> | <span data-ttu-id="daccf-222">Sim</span><span class="sxs-lookup"><span data-stu-id="daccf-222">Yes</span></span> | <span data-ttu-id="daccf-223">Sim</span><span class="sxs-lookup"><span data-stu-id="daccf-223">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="daccf-224">Exemplos:</span><span class="sxs-lookup"><span data-stu-id="daccf-224">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="daccf-225">Não</span><span class="sxs-lookup"><span data-stu-id="daccf-225">No</span></span> | <span data-ttu-id="daccf-226">Não</span><span class="sxs-lookup"><span data-stu-id="daccf-226">No</span></span> | <span data-ttu-id="daccf-227">Sim</span><span class="sxs-lookup"><span data-stu-id="daccf-227">Yes</span></span> |

<span data-ttu-id="daccf-228">Para obter mais informações sobre o descarte de tipos, consulte a seção [Descarte de serviços](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="daccf-228">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="daccf-229">Um cenário comum para várias implementações é a [simulação de tipos para teste](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="daccf-229">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="daccf-230">Métodos `TryAdd{LIFETIME}` somente registrarão o serviço se ainda não houver uma implementação registrada.</span><span class="sxs-lookup"><span data-stu-id="daccf-230">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="daccf-231">No exemplo a seguir, a primeira linha registra `MyDependency` para `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="daccf-231">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="daccf-232">A segunda linha não tem nenhum efeito porque `IMyDependency` já tem uma implementação registrada:</span><span class="sxs-lookup"><span data-stu-id="daccf-232">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="daccf-233">Para obter mais informações, consulte:</span><span class="sxs-lookup"><span data-stu-id="daccf-233">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="daccf-234">Métodos [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) apenas registrarão o serviço se ainda não houver uma implementação *do mesmo tipo*.</span><span class="sxs-lookup"><span data-stu-id="daccf-234">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="daccf-235">Vários serviços são resolvidos via `IEnumerable<{SERVICE}>`.</span><span class="sxs-lookup"><span data-stu-id="daccf-235">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="daccf-236">Ao registrar os serviços, só convém ao desenvolvedor adicionar uma instância se até o momento nenhuma do mesmo tipo foi adicionada.</span><span class="sxs-lookup"><span data-stu-id="daccf-236">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="daccf-237">Em geral, esse método é usado por autores de biblioteca para evitar registrar duas cópias de uma instância no contêiner.</span><span class="sxs-lookup"><span data-stu-id="daccf-237">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="daccf-238">No exemplo a seguir, a primeira linha registra `MyDep` para `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="daccf-238">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="daccf-239">A segunda linha registra `MyDep` para `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="daccf-239">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="daccf-240">A terceira linha não tem nenhum efeito porque `IMyDep1` já tem uma implementação registrada de `MyDep`:</span><span class="sxs-lookup"><span data-stu-id="daccf-240">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="daccf-241">Comportamento da injeção de construtor</span><span class="sxs-lookup"><span data-stu-id="daccf-241">Constructor injection behavior</span></span>

<span data-ttu-id="daccf-242">Os serviços podem ser resolvidos por dois mecanismos:</span><span class="sxs-lookup"><span data-stu-id="daccf-242">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="daccf-243"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permite a criação de objeto sem registro de serviço no contêiner de injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="daccf-243"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="daccf-244">`ActivatorUtilities` é usado com abstrações voltadas ao usuário, como Auxiliares de Marca, controladores MVC e associadores de modelo.</span><span class="sxs-lookup"><span data-stu-id="daccf-244">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="daccf-245">Os construtores podem aceitar argumentos que não são fornecidos pela injeção de dependência, mas que precisam atribuir valores padrão.</span><span class="sxs-lookup"><span data-stu-id="daccf-245">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="daccf-246">Quando os serviços são resolvidos pelo `IServiceProvider` ou `ActivatorUtilities` , a [injeção de Construtor](xref:mvc/controllers/dependency-injection#constructor-injection) requer um construtor *público* .</span><span class="sxs-lookup"><span data-stu-id="daccf-246">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="daccf-247">Quando os serviços são resolvidos pelo `ActivatorUtilities` , a [injeção de Construtor](xref:mvc/controllers/dependency-injection#constructor-injection) requer que apenas um Construtor aplicável exista.</span><span class="sxs-lookup"><span data-stu-id="daccf-247">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="daccf-248">Há suporte para sobrecargas de construtor, mas somente uma sobrecarga pode existir, cujos argumentos podem ser todos atendidos pela injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="daccf-248">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="daccf-249">Contextos de Entity Framework</span><span class="sxs-lookup"><span data-stu-id="daccf-249">Entity Framework contexts</span></span>

<span data-ttu-id="daccf-250">Contextos do Entity Framework geralmente são adicionados ao contêiner de serviço usando o [tempo de vida com escopo](#service-lifetimes), pois o escopo de operações de banco de dados de aplicativo Web normalmente é para a solicitação do cliente.</span><span class="sxs-lookup"><span data-stu-id="daccf-250">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="daccf-251">O tempo de vida padrão será definido como escopo se um tempo de vida não for especificado por uma sobrecarga [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) ao registrar o contexto do banco de dados.</span><span class="sxs-lookup"><span data-stu-id="daccf-251">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="daccf-252">Serviços com um determinado tempo de vida não devem usar um contexto de banco de dados com um tempo de vida mais curto que aquele do serviço.</span><span class="sxs-lookup"><span data-stu-id="daccf-252">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="daccf-253">Opções de tempo de vida e de registro</span><span class="sxs-lookup"><span data-stu-id="daccf-253">Lifetime and registration options</span></span>

<span data-ttu-id="daccf-254">Para demonstrar a diferença entre as opções de tempo de vida e de registro, considere as interfaces a seguir, que representam tarefas como uma operação com um identificador exclusivo, `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="daccf-254">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="daccf-255">Dependendo de como o tempo de vida de um serviço de operações é configurado para as interfaces a seguir, o contêiner fornece a mesma instância do serviço, ou outra diferente, quando recebe uma solicitação de uma classe:</span><span class="sxs-lookup"><span data-stu-id="daccf-255">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="daccf-256">As interfaces são implementadas na classe `Operation`.</span><span class="sxs-lookup"><span data-stu-id="daccf-256">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="daccf-257">O construtor `Operation` gera um GUID, caso um não seja fornecido:</span><span class="sxs-lookup"><span data-stu-id="daccf-257">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="daccf-258">Há um `OperationService` registrado que depende de cada um dos outros `Operation` tipos.</span><span class="sxs-lookup"><span data-stu-id="daccf-258">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="daccf-259">Quando `OperationService` é solicitado por meio da injeção de dependência, ele recebe a uma nova instância de cada serviço, ou uma instância existente com base no tempo de vida do serviço dependente.</span><span class="sxs-lookup"><span data-stu-id="daccf-259">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="daccf-260">Quando os serviços temporários forem criados mediante solicitação do contêiner, o `OperationId` do serviço `IOperationTransient` será diferente do `OperationId` do `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="daccf-260">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="daccf-261">`OperationService` recebe uma nova instância da classe `IOperationTransient`.</span><span class="sxs-lookup"><span data-stu-id="daccf-261">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="daccf-262">A nova instância produz outro `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="daccf-262">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="daccf-263">Quando os serviços com escopo forem criados por solicitação do cliente, o `OperationId` do serviço `IOperationScoped` será o mesmo que o `OperationService` dentro de uma solicitação do cliente.</span><span class="sxs-lookup"><span data-stu-id="daccf-263">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="daccf-264">Em todas as solicitações do cliente, os dois serviços compartilham um valor de `OperationId` diferente.</span><span class="sxs-lookup"><span data-stu-id="daccf-264">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="daccf-265">Quando os serviços singleton e de instância singleton forem criados uma vez e usados em todas as solicitações de cliente e em todos os serviços, o `OperationId` será constante entre todas as solicitações de serviço.</span><span class="sxs-lookup"><span data-stu-id="daccf-265">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="daccf-266">Em `Startup.ConfigureServices`, cada tipo é adicionado ao contêiner de acordo com seu tempo de vida nomeado:</span><span class="sxs-lookup"><span data-stu-id="daccf-266">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="daccf-267">O serviço `IOperationSingletonInstance` está usando uma instância específica com uma ID conhecida de `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="daccf-267">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="daccf-268">Fica claro quando esse tipo está em uso (seu GUID contém apenas zeros).</span><span class="sxs-lookup"><span data-stu-id="daccf-268">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="daccf-269">O exemplo de aplicativo demonstra os tempos de vida do objeto dentro e entre solicitações individuais.</span><span class="sxs-lookup"><span data-stu-id="daccf-269">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="daccf-270">O exemplo de aplicativo `IndexModel` solicita cada tipo `IOperation` e o `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="daccf-270">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="daccf-271">Depois, a página exibe todos os valores de `OperationId` da classe de modelo de página e do serviço por meio de atribuições de propriedade:</span><span class="sxs-lookup"><span data-stu-id="daccf-271">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="daccf-272">As duas saídas a seguir mostram os resultados das duas solicitações:</span><span class="sxs-lookup"><span data-stu-id="daccf-272">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="daccf-273">**Primeira solicitação:**</span><span class="sxs-lookup"><span data-stu-id="daccf-273">**First request:**</span></span>

<span data-ttu-id="daccf-274">Operações do controlador:</span><span class="sxs-lookup"><span data-stu-id="daccf-274">Controller operations:</span></span>

<span data-ttu-id="daccf-275">Transitória: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="daccf-275">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="daccf-276">Com escopo: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="daccf-276">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="daccf-277">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="daccf-277">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="daccf-278">Instância: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="daccf-278">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="daccf-279">`OperationService` operações:</span><span class="sxs-lookup"><span data-stu-id="daccf-279">`OperationService` operations:</span></span>

<span data-ttu-id="daccf-280">Transitória: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="daccf-280">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="daccf-281">Com escopo: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="daccf-281">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="daccf-282">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="daccf-282">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="daccf-283">Instância: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="daccf-283">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="daccf-284">**Segunda solicitação:**</span><span class="sxs-lookup"><span data-stu-id="daccf-284">**Second request:**</span></span>

<span data-ttu-id="daccf-285">Operações do controlador:</span><span class="sxs-lookup"><span data-stu-id="daccf-285">Controller operations:</span></span>

<span data-ttu-id="daccf-286">Transitória: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="daccf-286">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="daccf-287">Com escopo: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="daccf-287">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="daccf-288">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="daccf-288">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="daccf-289">Instância: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="daccf-289">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="daccf-290">`OperationService` operações:</span><span class="sxs-lookup"><span data-stu-id="daccf-290">`OperationService` operations:</span></span>

<span data-ttu-id="daccf-291">Transitória: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="daccf-291">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="daccf-292">Com escopo: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="daccf-292">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="daccf-293">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="daccf-293">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="daccf-294">Instância: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="daccf-294">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="daccf-295">Observe qual dos valores de `OperationId` varia em uma solicitação, e entre as solicitações:</span><span class="sxs-lookup"><span data-stu-id="daccf-295">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="daccf-296">Os objetos *transitórios* sempre são diferentes.</span><span class="sxs-lookup"><span data-stu-id="daccf-296">*Transient* objects are always different.</span></span> <span data-ttu-id="daccf-297">O valor `OperationId` transitório da primeira e da segunda solicitações de cliente é diferente para as duas operações `OperationService` e em todas as solicitações de cliente.</span><span class="sxs-lookup"><span data-stu-id="daccf-297">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="daccf-298">Uma nova instância é fornecida para cada solicitação de serviço e solicitação de cliente.</span><span class="sxs-lookup"><span data-stu-id="daccf-298">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="daccf-299">Objetos *com escopo* são os mesmos em uma solicitação de cliente, mas diferentes entre solicitações de cliente.</span><span class="sxs-lookup"><span data-stu-id="daccf-299">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="daccf-300">Os pbjetos *singleton* são os mesmos para cada objeto e solicitação, independentemente de uma instância `Operation` ser fornecida em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="daccf-300">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="daccf-301">Chamar os serviços desde o principal</span><span class="sxs-lookup"><span data-stu-id="daccf-301">Call services from main</span></span>

<span data-ttu-id="daccf-302">Crie um <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> com [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) para resolver um serviço com escopo dentro do escopo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="daccf-302">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="daccf-303">Essa abordagem é útil para acessar um serviço com escopo durante a inicialização para executar tarefas de inicialização.</span><span class="sxs-lookup"><span data-stu-id="daccf-303">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="daccf-304">O exemplo a seguir mostra como obter um contexto para o `MyScopedService` em `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="daccf-304">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Hosting;

public class Program
{
    public static async Task Main(string[] args)
    {
        var host = CreateHostBuilder(args).Build();

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

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStartup<Startup>();
            });
}
```

## <a name="scope-validation"></a><span data-ttu-id="daccf-305">Validação de escopo</span><span class="sxs-lookup"><span data-stu-id="daccf-305">Scope validation</span></span>

<span data-ttu-id="daccf-306">Quando o aplicativo está em execução no ambiente de desenvolvimento e chama [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) para criar o host, o provedor de serviço padrão executa verificações para verificar se:</span><span class="sxs-lookup"><span data-stu-id="daccf-306">When the app is running in the Development environment and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="daccf-307">Os serviços com escopo não são resolvidos direta ou indiretamente pelo provedor de serviço raiz.</span><span class="sxs-lookup"><span data-stu-id="daccf-307">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="daccf-308">Os serviços com escopo não são injetados direta ou indiretamente em singletons.</span><span class="sxs-lookup"><span data-stu-id="daccf-308">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="daccf-309">O provedor de serviços raiz é criado quando <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> é chamado.</span><span class="sxs-lookup"><span data-stu-id="daccf-309">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="daccf-310">O tempo de vida do provedor de serviço raiz corresponde ao tempo de vida do aplicativo/servidor quando o provedor começa com o aplicativo e é descartado quando o aplicativo é desligado.</span><span class="sxs-lookup"><span data-stu-id="daccf-310">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="daccf-311">Os serviços com escopo são descartados pelo contêiner que os criou.</span><span class="sxs-lookup"><span data-stu-id="daccf-311">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="daccf-312">Se um serviço com escopo é criado no contêiner raiz, o tempo de vida do serviço é promovido efetivamente para singleton, porque ele só é descartado pelo contêiner raiz quando o aplicativo/servidor é desligado.</span><span class="sxs-lookup"><span data-stu-id="daccf-312">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="daccf-313">A validação dos escopos de serviço detecta essas situações quando `BuildServiceProvider` é chamado.</span><span class="sxs-lookup"><span data-stu-id="daccf-313">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="daccf-314">Para obter mais informações, consulte <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="daccf-314">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="daccf-315">Serviços de solicitação</span><span class="sxs-lookup"><span data-stu-id="daccf-315">Request Services</span></span>

<span data-ttu-id="daccf-316">Os serviços disponíveis em uma solicitação do ASP.NET de `HttpContext` são expostos por meio da coleção [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).</span><span class="sxs-lookup"><span data-stu-id="daccf-316">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="daccf-317">Os Serviços de Solicitação representam os serviços configurados e solicitados como parte do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="daccf-317">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="daccf-318">Quando os objetos especificam dependências, elas são atendidas pelos tipos encontrados em `RequestServices`, não em `ApplicationServices`.</span><span class="sxs-lookup"><span data-stu-id="daccf-318">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="daccf-319">Em geral, o aplicativo não deve usar essas propriedades diretamente.</span><span class="sxs-lookup"><span data-stu-id="daccf-319">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="daccf-320">Em vez disso, solicite os tipos que as classes exigem por meio de construtores de classe e permita que a estrutura Insira as dependências.</span><span class="sxs-lookup"><span data-stu-id="daccf-320">Instead, request the types that classes require via class constructors and allow the framework to inject the dependencies.</span></span> <span data-ttu-id="daccf-321">Isso resulta em classes que são mais fáceis de testar.</span><span class="sxs-lookup"><span data-stu-id="daccf-321">This yields classes that are easier to test.</span></span>

<span data-ttu-id="daccf-322">ASP.NET Core cria um escopo por solicitação e `RequestServices` expõe o provedor de serviço com escopo.</span><span class="sxs-lookup"><span data-stu-id="daccf-322">ASP.NET Core creates a scope per request and `RequestServices` exposes the scoped service provider.</span></span> <span data-ttu-id="daccf-323">Todos os serviços com escopo são válidos enquanto a solicitação está ativa.</span><span class="sxs-lookup"><span data-stu-id="daccf-323">All scoped services are valid for as long as the request is active.</span></span>

> [!NOTE]
> <span data-ttu-id="daccf-324">Prefira solicitar dependências como parâmetros de construtor para acessar a coleção `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="daccf-324">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="daccf-325">Projetar serviços para injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="daccf-325">Design services for dependency injection</span></span>

<span data-ttu-id="daccf-326">As melhores práticas são:</span><span class="sxs-lookup"><span data-stu-id="daccf-326">Best practices are to:</span></span>

* <span data-ttu-id="daccf-327">Projetar serviços para usar a injeção de dependência a fim de obter suas dependências.</span><span class="sxs-lookup"><span data-stu-id="daccf-327">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="daccf-328">Evite classes e membros com estado e estáticos.</span><span class="sxs-lookup"><span data-stu-id="daccf-328">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="daccf-329">Crie aplicativos para usar os serviços singleton, o que evita a criação de estado global.</span><span class="sxs-lookup"><span data-stu-id="daccf-329">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="daccf-330">Evite a instanciação direta das classes dependentes em serviços.</span><span class="sxs-lookup"><span data-stu-id="daccf-330">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="daccf-331">A instanciação direta acopla o código a uma implementação específica.</span><span class="sxs-lookup"><span data-stu-id="daccf-331">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="daccf-332">Verifique as classes de aplicativo pequenas, bem fatoradas e facilmente testadas.</span><span class="sxs-lookup"><span data-stu-id="daccf-332">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="daccf-333">Se parecer que uma classe tem muitas dependências injetadas, normalmente é um sinal de que a classe tem muitas responsabilidades e está violando o [Princípio da Responsabilidade Única (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="daccf-333">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="daccf-334">Tente refatorar a classe movendo algumas de suas responsabilidades para uma nova classe.</span><span class="sxs-lookup"><span data-stu-id="daccf-334">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="daccf-335">Tenha em mente que as classes Razor de modelo de página de páginas e as classes do controlador MVC devem se concentrar nas preocupações da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="daccf-335">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="daccf-336">Os detalhes de implementação de acesso aos dados e as regras de negócios devem ser mantidos em classes apropriadas a esses [interesses separados](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="daccf-336">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="daccf-337">Descarte de serviços</span><span class="sxs-lookup"><span data-stu-id="daccf-337">Disposal of services</span></span>

<span data-ttu-id="daccf-338">O contêiner chama <xref:System.IDisposable.Dispose*> para os tipos <xref:System.IDisposable> criados por ele.</span><span class="sxs-lookup"><span data-stu-id="daccf-338">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="daccf-339">Se uma instância for adicionada ao contêiner pelo código do usuário, ele não será descartado automaticamente.</span><span class="sxs-lookup"><span data-stu-id="daccf-339">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="daccf-340">No exemplo a seguir, os serviços são criados pelo contêiner de serviço e descartados automaticamente:</span><span class="sxs-lookup"><span data-stu-id="daccf-340">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="daccf-341">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="daccf-341">In the following example:</span></span>

* <span data-ttu-id="daccf-342">As instâncias de serviço não são criadas pelo contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="daccf-342">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="daccf-343">Os tempos de vida de serviço pretendidos não são conhecidos pela estrutura.</span><span class="sxs-lookup"><span data-stu-id="daccf-343">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="daccf-344">A estrutura não descarta os serviços automaticamente.</span><span class="sxs-lookup"><span data-stu-id="daccf-344">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="daccf-345">Se os serviços não forem explicitamente descartados no código do desenvolvedor, eles persistirão até que o aplicativo seja desligado.</span><span class="sxs-lookup"><span data-stu-id="daccf-345">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="daccf-346">Diretrizes de IDisposable para instâncias transitórias e compartilhadas</span><span class="sxs-lookup"><span data-stu-id="daccf-346">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="daccf-347">Tempo de vida transitório, limitado</span><span class="sxs-lookup"><span data-stu-id="daccf-347">Transient, limited lifetime</span></span>

<span data-ttu-id="daccf-348">**Cenário**</span><span class="sxs-lookup"><span data-stu-id="daccf-348">**Scenario**</span></span>

<span data-ttu-id="daccf-349">O aplicativo requer uma <xref:System.IDisposable> instância com um tempo de vida transitório para qualquer um dos seguintes cenários:</span><span class="sxs-lookup"><span data-stu-id="daccf-349">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="daccf-350">A instância é resolvida no escopo raiz.</span><span class="sxs-lookup"><span data-stu-id="daccf-350">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="daccf-351">A instância deve ser descartada antes do término do escopo.</span><span class="sxs-lookup"><span data-stu-id="daccf-351">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="daccf-352">**Solução**</span><span class="sxs-lookup"><span data-stu-id="daccf-352">**Solution**</span></span>

<span data-ttu-id="daccf-353">Use o padrão de fábrica para criar uma instância fora do escopo pai.</span><span class="sxs-lookup"><span data-stu-id="daccf-353">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="daccf-354">Nessa situação, o aplicativo geralmente teria um `Create` método que chamasse o construtor do tipo final diretamente.</span><span class="sxs-lookup"><span data-stu-id="daccf-354">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="daccf-355">Se o tipo final tiver outras dependências, a fábrica poderá:</span><span class="sxs-lookup"><span data-stu-id="daccf-355">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="daccf-356">Receber um <xref:System.IServiceProvider> em seu construtor.</span><span class="sxs-lookup"><span data-stu-id="daccf-356">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="daccf-357">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> para instanciar a instância fora do contêiner, ao usar o contêiner para suas dependências.</span><span class="sxs-lookup"><span data-stu-id="daccf-357">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="daccf-358">Instância compartilhada, tempo de vida limitado</span><span class="sxs-lookup"><span data-stu-id="daccf-358">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="daccf-359">**Cenário**</span><span class="sxs-lookup"><span data-stu-id="daccf-359">**Scenario**</span></span>

<span data-ttu-id="daccf-360">O aplicativo requer uma <xref:System.IDisposable> instância compartilhada entre vários serviços, mas o <xref:System.IDisposable> deve ter um tempo de vida limitado.</span><span class="sxs-lookup"><span data-stu-id="daccf-360">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="daccf-361">**Solução**</span><span class="sxs-lookup"><span data-stu-id="daccf-361">**Solution**</span></span>

<span data-ttu-id="daccf-362">Registre a instância com um tempo de vida de escopo.</span><span class="sxs-lookup"><span data-stu-id="daccf-362">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="daccf-363">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> para iniciar e criar um novo <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> .</span><span class="sxs-lookup"><span data-stu-id="daccf-363">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="daccf-364">Use o escopo <xref:System.IServiceProvider> para obter os serviços necessários.</span><span class="sxs-lookup"><span data-stu-id="daccf-364">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="daccf-365">Descarte o escopo quando o tempo de vida deve terminar.</span><span class="sxs-lookup"><span data-stu-id="daccf-365">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="daccf-366">Diretrizes gerais</span><span class="sxs-lookup"><span data-stu-id="daccf-366">General Guidelines</span></span>

* <span data-ttu-id="daccf-367">Não registre <xref:System.IDisposable> instâncias com um escopo transitório.</span><span class="sxs-lookup"><span data-stu-id="daccf-367">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="daccf-368">Em vez disso, use o padrão de fábrica.</span><span class="sxs-lookup"><span data-stu-id="daccf-368">Use the factory pattern instead.</span></span>
* <span data-ttu-id="daccf-369">Não resolva instâncias transitórias ou com escopo definido <xref:System.IDisposable> no escopo raiz.</span><span class="sxs-lookup"><span data-stu-id="daccf-369">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="daccf-370">A única exceção geral é quando o aplicativo cria/recria e descarta o <xref:System.IServiceProvider> , que não é um padrão ideal.</span><span class="sxs-lookup"><span data-stu-id="daccf-370">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="daccf-371">O recebimento de uma <xref:System.IDisposable> dependência por meio de di não exige que o receptor se implemente <xref:System.IDisposable> .</span><span class="sxs-lookup"><span data-stu-id="daccf-371">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="daccf-372">O receptor da <xref:System.IDisposable> dependência não deve chamar <xref:System.IDisposable.Dispose%2A> essa dependência.</span><span class="sxs-lookup"><span data-stu-id="daccf-372">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="daccf-373">Os escopos devem ser usados para controlar o tempo de vida dos serviços.</span><span class="sxs-lookup"><span data-stu-id="daccf-373">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="daccf-374">Os escopos não são hierárquicos e não há nenhuma conexão especial entre escopos.</span><span class="sxs-lookup"><span data-stu-id="daccf-374">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="daccf-375">Substituição do contêiner de serviço padrão</span><span class="sxs-lookup"><span data-stu-id="daccf-375">Default service container replacement</span></span>

<span data-ttu-id="daccf-376">O contêiner de serviço interno foi projetado para atender às necessidades da estrutura e da maioria dos aplicativos de consumidor.</span><span class="sxs-lookup"><span data-stu-id="daccf-376">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="daccf-377">É recomendável usar o contêiner interno, a menos que você precise de um recurso específico ao qual o contêiner interno não ofereça suporte, como:</span><span class="sxs-lookup"><span data-stu-id="daccf-377">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="daccf-378">Injeção de propriedade</span><span class="sxs-lookup"><span data-stu-id="daccf-378">Property injection</span></span>
* <span data-ttu-id="daccf-379">Injeção com base no nome</span><span class="sxs-lookup"><span data-stu-id="daccf-379">Injection based on name</span></span>
* <span data-ttu-id="daccf-380">Contêineres filho</span><span class="sxs-lookup"><span data-stu-id="daccf-380">Child containers</span></span>
* <span data-ttu-id="daccf-381">Gerenciamento de tempo de vida personalizado</span><span class="sxs-lookup"><span data-stu-id="daccf-381">Custom lifetime management</span></span>
* <span data-ttu-id="daccf-382">Suporte de `Func<T>` para inicialização lenta</span><span class="sxs-lookup"><span data-stu-id="daccf-382">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="daccf-383">Registro baseado em Convenção</span><span class="sxs-lookup"><span data-stu-id="daccf-383">Convention-based registration</span></span>

<span data-ttu-id="daccf-384">Os seguintes contêineres de terceiros podem ser usados com aplicativos ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="daccf-384">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="daccf-385">Autofac</span><span class="sxs-lookup"><span data-stu-id="daccf-385">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="daccf-386">DryIoc</span><span class="sxs-lookup"><span data-stu-id="daccf-386">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="daccf-387">Grace</span><span class="sxs-lookup"><span data-stu-id="daccf-387">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="daccf-388">LightInject</span><span class="sxs-lookup"><span data-stu-id="daccf-388">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="daccf-389">Lamar</span><span class="sxs-lookup"><span data-stu-id="daccf-389">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="daccf-390">Stashbox</span><span class="sxs-lookup"><span data-stu-id="daccf-390">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="daccf-391">Unity</span><span class="sxs-lookup"><span data-stu-id="daccf-391">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="daccf-392">Acesso thread-safe</span><span class="sxs-lookup"><span data-stu-id="daccf-392">Thread safety</span></span>

<span data-ttu-id="daccf-393">Crie serviços singleton thread-safe.</span><span class="sxs-lookup"><span data-stu-id="daccf-393">Create thread-safe singleton services.</span></span> <span data-ttu-id="daccf-394">Se um serviço singleton tiver uma dependência em um serviço transitório, o serviço transitório também precisará ter acesso thread-safe, dependendo de como ele é usado pelo singleton.</span><span class="sxs-lookup"><span data-stu-id="daccf-394">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="daccf-395">O método de fábrica de um único serviço, como o segundo argumento para [addsingleton \<TService> (IServiceCollection, Func \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), não precisa ser thread-safe.</span><span class="sxs-lookup"><span data-stu-id="daccf-395">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="daccf-396">Como um construtor do tipo (`static`), ele tem garantia de ser chamado uma vez por um único thread.</span><span class="sxs-lookup"><span data-stu-id="daccf-396">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="daccf-397">Recomendações</span><span class="sxs-lookup"><span data-stu-id="daccf-397">Recommendations</span></span>

* <span data-ttu-id="daccf-398">A resolução de serviço baseada em `async/await` e `Task` não é compatível.</span><span class="sxs-lookup"><span data-stu-id="daccf-398">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="daccf-399">O C# não é compatível com os construtores assíncronos. Portanto, o padrão recomendado é usar os métodos assíncronos após a resolução síncrona do serviço.</span><span class="sxs-lookup"><span data-stu-id="daccf-399">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="daccf-400">Evite armazenar dados e a configuração diretamente no contêiner do serviço.</span><span class="sxs-lookup"><span data-stu-id="daccf-400">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="daccf-401">Por exemplo, o carrinho de compras de um usuário normalmente não deve ser adicionado ao contêiner do serviço.</span><span class="sxs-lookup"><span data-stu-id="daccf-401">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="daccf-402">A configuração deve usar o [padrão de opções](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="daccf-402">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="daccf-403">Da mesma forma, evite objetos de "suporte de dados" que existem somente para permitir o acesso a outro objeto.</span><span class="sxs-lookup"><span data-stu-id="daccf-403">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="daccf-404">É melhor solicitar o item real por meio da DI.</span><span class="sxs-lookup"><span data-stu-id="daccf-404">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="daccf-405">Evite o acesso estático aos serviços (por exemplo, digitando estaticamente [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) para usar em outro lugar).</span><span class="sxs-lookup"><span data-stu-id="daccf-405">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="daccf-406">Evite usar o *padrão do localizador de serviço*.</span><span class="sxs-lookup"><span data-stu-id="daccf-406">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="daccf-407">Por exemplo, não invoque <xref:System.IServiceProvider.GetService*> para obter uma instância de serviço quando for possível usar a DI:</span><span class="sxs-lookup"><span data-stu-id="daccf-407">For example, don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="daccf-408">**Incorreto:**</span><span class="sxs-lookup"><span data-stu-id="daccf-408">**Incorrect:**</span></span>

  ```csharp
  public class MyClass()
  {
      public void MyMethod()
      {
          var optionsMonitor = 
              _services.GetService<IOptionsMonitor<MyOptions>>();
          var option = optionsMonitor.CurrentValue.Option;

          ...
      }
  }
  ```

  <span data-ttu-id="daccf-409">**Correto**:</span><span class="sxs-lookup"><span data-stu-id="daccf-409">**Correct**:</span></span>

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

* <span data-ttu-id="daccf-410">Outra variação de localizador de serviço a ser evitada é injetar um alocador que resolve as dependências em runtime.</span><span class="sxs-lookup"><span data-stu-id="daccf-410">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="daccf-411">Essas duas práticas misturam estratégias de [inversão de controle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).</span><span class="sxs-lookup"><span data-stu-id="daccf-411">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

* <span data-ttu-id="daccf-412">Evite o acesso estático a `HttpContext` (por exemplo, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="daccf-412">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="daccf-413">Como todos os conjuntos de recomendações, talvez você encontre situações em que é necessário ignorar uma recomendação.</span><span class="sxs-lookup"><span data-stu-id="daccf-413">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="daccf-414">As exceções são raras, principalmente os casos especiais dentro da própria estrutura.</span><span class="sxs-lookup"><span data-stu-id="daccf-414">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="daccf-415">A DI é uma *alternativa* aos padrões de acesso a objeto estático/global.</span><span class="sxs-lookup"><span data-stu-id="daccf-415">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="daccf-416">Talvez você não obtenha os benefícios da DI se combiná-lo com o acesso a objeto estático.</span><span class="sxs-lookup"><span data-stu-id="daccf-416">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="daccf-417">Padrões recomendados para multilocação em DI</span><span class="sxs-lookup"><span data-stu-id="daccf-417">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="daccf-418">O [Orchard Core](https://github.com/OrchardCMS/OrchardCore) fornece multilocação.</span><span class="sxs-lookup"><span data-stu-id="daccf-418">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) provides multi-tenancy.</span></span> <span data-ttu-id="daccf-419">Para obter mais informações, consulte a [documentação do Orchard Core](https://docs.orchardcore.net/en/dev/).</span><span class="sxs-lookup"><span data-stu-id="daccf-419">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="daccf-420">Consulte os aplicativos de exemplos em https://github.com/OrchardCMS/OrchardCore.Samples para obter exemplos de como criar aplicativos modulares e multilocatários usando o Orchard Core Framework sem nenhum dos recursos específicos do CMS.</span><span class="sxs-lookup"><span data-stu-id="daccf-420">See the samples apps at https://github.com/OrchardCMS/OrchardCore.Samples for examples of how to build modular and multi-tenant apps using just Orchard Core Framework without any of the CMS specific features.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="daccf-421">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="daccf-421">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="daccf-422">Quatro maneiras de descartar IDisposables em ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="daccf-422">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="daccf-423">Como gravar um código limpo no ASP.NET Core com injeção de dependência (MSDN)</span><span class="sxs-lookup"><span data-stu-id="daccf-423">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="daccf-424">Princípio de Dependências Explícitas</span><span class="sxs-lookup"><span data-stu-id="daccf-424">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="daccf-425">Inversão de Contêineres de Controle e o padrão de Injeção de Dependência (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="daccf-425">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="daccf-426">Como registrar um serviço com várias interfaces na DI do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="daccf-426">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="daccf-427">O ASP.NET Core é compatível com o padrão de design de software de DI (injeção de dependência), que é uma técnica para alcançar a [IoC (Inversão de Controle)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) entre classes e suas dependências.</span><span class="sxs-lookup"><span data-stu-id="daccf-427">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="daccf-428">Para obter mais informações específicas sobre injeção de dependência em controladores de MVC, consulte <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="daccf-428">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="daccf-429">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="daccf-429">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="daccf-430">Visão geral da injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="daccf-430">Overview of dependency injection</span></span>

<span data-ttu-id="daccf-431">Uma *dependência* é qualquer objeto exigido por outro objeto.</span><span class="sxs-lookup"><span data-stu-id="daccf-431">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="daccf-432">Examine a classe `MyDependency` a seguir com um método `WriteMessage` do qual outras classes em um aplicativo dependem:</span><span class="sxs-lookup"><span data-stu-id="daccf-432">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="daccf-433">Uma instância da classe `MyDependency` pode ser criada para tornar o método `WriteMessage` disponível para uma classe.</span><span class="sxs-lookup"><span data-stu-id="daccf-433">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="daccf-434">A classe  é uma dependência da classe:</span><span class="sxs-lookup"><span data-stu-id="daccf-434">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="daccf-435">A classe cria e depende diretamente da instância `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="daccf-435">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="daccf-436">As dependências de código (como no exemplo anterior) são problemáticas e devem ser evitadas por estes motivos:</span><span class="sxs-lookup"><span data-stu-id="daccf-436">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="daccf-437">Para substituir `MyDependency` por uma implementação diferente, a classe deve ser modificada.</span><span class="sxs-lookup"><span data-stu-id="daccf-437">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="daccf-438">Se `MyDependency` tiver dependências, elas deverão ser configuradas pela classe.</span><span class="sxs-lookup"><span data-stu-id="daccf-438">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="daccf-439">Em um projeto grande com várias classes dependendo da `MyDependency`, o código de configuração fica pulverizado por todo o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="daccf-439">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="daccf-440">É difícil testar a unidade dessa implementação.</span><span class="sxs-lookup"><span data-stu-id="daccf-440">This implementation is difficult to unit test.</span></span> <span data-ttu-id="daccf-441">O aplicativo deve usar uma simulação ou stub da classe `MyDependency`, o que não é possível com essa abordagem.</span><span class="sxs-lookup"><span data-stu-id="daccf-441">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="daccf-442">Injeção de dependência trata desses problemas da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="daccf-442">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="daccf-443">O uso de uma interface ou classe base para abstrair a implementação da dependência.</span><span class="sxs-lookup"><span data-stu-id="daccf-443">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="daccf-444">Registrando a dependência em um contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="daccf-444">Registration of the dependency in a service container.</span></span> <span data-ttu-id="daccf-445">O ASP.NET Core fornece um contêiner de serviço interno, o <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="daccf-445">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="daccf-446">Os serviços são registrados no método `Startup.ConfigureServices` do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="daccf-446">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="daccf-447">*Injeção* do serviço no construtor da classe na qual ele é usado.</span><span class="sxs-lookup"><span data-stu-id="daccf-447">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="daccf-448">A estrutura assume a responsabilidade de criar uma instância da dependência e de descartá-la quando não for mais necessária.</span><span class="sxs-lookup"><span data-stu-id="daccf-448">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="daccf-449">No [exemplo de aplicativo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), a interface `IMyDependency` define um método que o serviço fornece ao aplicativo:</span><span class="sxs-lookup"><span data-stu-id="daccf-449">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="daccf-450">Essa interface é implementada por um tipo concreto, `MyDependency`:</span><span class="sxs-lookup"><span data-stu-id="daccf-450">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="daccf-451">`MyDependency` solicita um <xref:Microsoft.Extensions.Logging.ILogger`1> em seu construtor.</span><span class="sxs-lookup"><span data-stu-id="daccf-451">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="daccf-452">Não é incomum usar a injeção de dependência de uma maneira encadeada.</span><span class="sxs-lookup"><span data-stu-id="daccf-452">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="daccf-453">Por sua vez, cada dependência solicitada solicita suas próprias dependências.</span><span class="sxs-lookup"><span data-stu-id="daccf-453">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="daccf-454">O contêiner resolve as dependências no grafo e retorna o serviço totalmente resolvido.</span><span class="sxs-lookup"><span data-stu-id="daccf-454">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="daccf-455">O conjunto de dependências que precisa ser resolvido normalmente é chamado de *árvore de dependência*, *grafo de dependência* ou *grafo de objeto*.</span><span class="sxs-lookup"><span data-stu-id="daccf-455">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="daccf-456">`IMyDependency` e `ILogger<TCategoryName>` devem ser registrados no contêiner do serviço.</span><span class="sxs-lookup"><span data-stu-id="daccf-456">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="daccf-457">`IMyDependency` está registrado em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="daccf-457">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="daccf-458">`ILogger<TCategoryName>` é registrado pela infraestrutura de abstrações de registro em log, portanto, é um [serviço fornecido por estrutura](#framework-provided-services) registrado por padrão pela estrutura.</span><span class="sxs-lookup"><span data-stu-id="daccf-458">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="daccf-459">O contêiner resolve `ILogger<TCategoryName>` aproveitando os [tipos abertos (genéricos)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminando a necessidade de registrar todo [tipo construído (genérico)](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span><span class="sxs-lookup"><span data-stu-id="daccf-459">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="daccf-460">No exemplo de aplicativo, o serviço `IMyDependency` está registrado com o tipo concreto `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="daccf-460">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="daccf-461">O registro define o escopo do tempo de vida do serviço para o tempo de vida de uma única solicitação.</span><span class="sxs-lookup"><span data-stu-id="daccf-461">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="daccf-462">Descreveremos posteriormente neste tópico os [tempos de vida do serviço](#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="daccf-462">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="daccf-463">Cada método de extensão `services.Add{SERVICE_NAME}` adiciona (e possivelmente configura) serviços.</span><span class="sxs-lookup"><span data-stu-id="daccf-463">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="daccf-464">Por exemplo, `services.AddMvc()` adiciona as páginas de serviços Razor e a MVC necessária.</span><span class="sxs-lookup"><span data-stu-id="daccf-464">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="daccf-465">Recomendamos que os aplicativos sigam essa convenção.</span><span class="sxs-lookup"><span data-stu-id="daccf-465">We recommended that apps follow this convention.</span></span> <span data-ttu-id="daccf-466">Coloque os métodos de extensão no namespace [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) para encapsular grupos de registros de serviço.</span><span class="sxs-lookup"><span data-stu-id="daccf-466">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="daccf-467">Se o construtor do serviço exigir um [tipo interno](/dotnet/csharp/language-reference/keywords/built-in-types-table), como `string`, o tipo poderá ser injetado usando a [configuração](xref:fundamentals/configuration/index) ou o [padrão de opções](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="daccf-467">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="daccf-468">Uma instância do serviço é solicitada por meio do construtor de uma classe, onde o serviço é usado e atribuído a um campo particular.</span><span class="sxs-lookup"><span data-stu-id="daccf-468">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="daccf-469">O campo é usado para acessar o serviço conforme o necessário na classe.</span><span class="sxs-lookup"><span data-stu-id="daccf-469">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="daccf-470">No exemplo de aplicativo, a instância `IMyDependency` é solicitada e usada para chamar o método `WriteMessage` do serviço:</span><span class="sxs-lookup"><span data-stu-id="daccf-470">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="daccf-471">Serviços injetados na inicialização</span><span class="sxs-lookup"><span data-stu-id="daccf-471">Services injected into Startup</span></span>

<span data-ttu-id="daccf-472">Somente os seguintes tipos de serviço podem ser injetados no `Startup` Construtor ao usar o host genérico ( <xref:Microsoft.Extensions.Hosting.IHostBuilder> ):</span><span class="sxs-lookup"><span data-stu-id="daccf-472">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="daccf-473">Os serviços podem ser injetados em `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="daccf-473">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="daccf-474">Para obter mais informações, consulte <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="daccf-474">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="daccf-475">Serviços fornecidos pela estrutura</span><span class="sxs-lookup"><span data-stu-id="daccf-475">Framework-provided services</span></span>

<span data-ttu-id="daccf-476">O `Startup.ConfigureServices` método é responsável por definir os serviços que o aplicativo usa, incluindo recursos de plataforma, como Entity Framework Core e ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="daccf-476">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="daccf-477">Inicialmente, o `IServiceCollection` fornecido para o `ConfigureServices` tem serviços definidos pela estrutura, dependendo de [como o host foi configurado](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="daccf-477">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="daccf-478">Não é incomum um aplicativo baseado em um modelo de ASP.NET Core ter centenas de serviços registrados pela estrutura.</span><span class="sxs-lookup"><span data-stu-id="daccf-478">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="daccf-479">Uma pequena amostra de serviços registrados na estrutura é listada na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="daccf-479">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="daccf-480">Tipo de Serviço</span><span class="sxs-lookup"><span data-stu-id="daccf-480">Service Type</span></span> | <span data-ttu-id="daccf-481">Tempo de vida</span><span class="sxs-lookup"><span data-stu-id="daccf-481">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="daccf-482">Transitório</span><span class="sxs-lookup"><span data-stu-id="daccf-482">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="daccf-483">Singleton</span><span class="sxs-lookup"><span data-stu-id="daccf-483">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="daccf-484">Singleton</span><span class="sxs-lookup"><span data-stu-id="daccf-484">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="daccf-485">Singleton</span><span class="sxs-lookup"><span data-stu-id="daccf-485">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="daccf-486">Transitório</span><span class="sxs-lookup"><span data-stu-id="daccf-486">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="daccf-487">Singleton</span><span class="sxs-lookup"><span data-stu-id="daccf-487">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="daccf-488">Transitório</span><span class="sxs-lookup"><span data-stu-id="daccf-488">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="daccf-489">Singleton</span><span class="sxs-lookup"><span data-stu-id="daccf-489">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="daccf-490">Singleton</span><span class="sxs-lookup"><span data-stu-id="daccf-490">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="daccf-491">Singleton</span><span class="sxs-lookup"><span data-stu-id="daccf-491">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="daccf-492">Transitório</span><span class="sxs-lookup"><span data-stu-id="daccf-492">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="daccf-493">Singleton</span><span class="sxs-lookup"><span data-stu-id="daccf-493">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="daccf-494">Singleton</span><span class="sxs-lookup"><span data-stu-id="daccf-494">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="daccf-495">Singleton</span><span class="sxs-lookup"><span data-stu-id="daccf-495">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="daccf-496">Registrar serviços adicionais com métodos de extensão</span><span class="sxs-lookup"><span data-stu-id="daccf-496">Register additional services with extension methods</span></span>

<span data-ttu-id="daccf-497">Quando um método de extensão de coleta do serviço estiver disponível para registrar um serviço (e seus serviços dependentes, se for necessário), a convenção é usar um único método de extensão `Add{SERVICE_NAME}` para registrar todos os serviços exigidos por esse serviço.</span><span class="sxs-lookup"><span data-stu-id="daccf-497">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="daccf-498">O código a seguir é um exemplo de como adicionar serviços adicionais ao contêiner usando os métodos de extensão [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) e <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> :</span><span class="sxs-lookup"><span data-stu-id="daccf-498">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="daccf-499">Para saber mais, confira a classe <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> na documentação da API.</span><span class="sxs-lookup"><span data-stu-id="daccf-499">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="daccf-500">Tempos de vida do serviço</span><span class="sxs-lookup"><span data-stu-id="daccf-500">Service lifetimes</span></span>

<span data-ttu-id="daccf-501">Escolha um tempo de vida apropriado para cada serviço registrado.</span><span class="sxs-lookup"><span data-stu-id="daccf-501">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="daccf-502">Os serviços do ASP.NET Core podem ser configurados com os seguintes tempos de vida:</span><span class="sxs-lookup"><span data-stu-id="daccf-502">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="daccf-503">Transitório</span><span class="sxs-lookup"><span data-stu-id="daccf-503">Transient</span></span>

<span data-ttu-id="daccf-504">Serviços temporários de tempo de vida (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) são criados cada vez que são solicitados pelo contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="daccf-504">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="daccf-505">Esse tempo de vida funciona melhor para serviços leves e sem estado.</span><span class="sxs-lookup"><span data-stu-id="daccf-505">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="daccf-506">Em aplicativos que processam solicitações, os serviços transitórios são descartados no final da solicitação.</span><span class="sxs-lookup"><span data-stu-id="daccf-506">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="daccf-507">Com escopo</span><span class="sxs-lookup"><span data-stu-id="daccf-507">Scoped</span></span>

<span data-ttu-id="daccf-508">Os serviços com tempo de vida (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) com escopo são criados uma vez por solicitação de cliente (conexão).</span><span class="sxs-lookup"><span data-stu-id="daccf-508">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="daccf-509">Em aplicativos que processam solicitações, os serviços com escopo são descartados no final da solicitação.</span><span class="sxs-lookup"><span data-stu-id="daccf-509">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

> [!WARNING]
> <span data-ttu-id="daccf-510">Ao usar um serviço com escopo em um middleware, injete o serviço no método `Invoke` ou `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="daccf-510">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="daccf-511">Não injetar via [injeção de Construtor](xref:mvc/controllers/dependency-injection#constructor-injection) porque força o serviço a se comportar como um singleton.</span><span class="sxs-lookup"><span data-stu-id="daccf-511">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="daccf-512">Para obter mais informações, consulte <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="daccf-512">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="daccf-513">Singleton</span><span class="sxs-lookup"><span data-stu-id="daccf-513">Singleton</span></span>

<span data-ttu-id="daccf-514">Serviços de tempo de vida singleton (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) são criados na primeira solicitação (ou quando `Startup.ConfigureServices` é executado e uma instância é especificada com o registro do serviço).</span><span class="sxs-lookup"><span data-stu-id="daccf-514">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="daccf-515">Cada solicitação subsequente usa a mesma instância.</span><span class="sxs-lookup"><span data-stu-id="daccf-515">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="daccf-516">Se o aplicativo exigir um comportamento de singleton, recomendamos permitir que o contêiner do serviço gerencie o tempo de vida do serviço.</span><span class="sxs-lookup"><span data-stu-id="daccf-516">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="daccf-517">Não implemente o padrão de design singleton e forneça o código de usuário para gerenciar o tempo de vida do objeto na classe.</span><span class="sxs-lookup"><span data-stu-id="daccf-517">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

<span data-ttu-id="daccf-518">Em aplicativos que processam solicitações, os serviços singleton são descartados quando o <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> é Descartado no desligamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="daccf-518">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed at app shutdown.</span></span>

> [!WARNING]
> <span data-ttu-id="daccf-519">É perigoso resolver um serviço com escopo de um singleton.</span><span class="sxs-lookup"><span data-stu-id="daccf-519">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="daccf-520">Pode fazer com que o serviço tenha um estado incorreto durante o processamento das solicitações seguintes.</span><span class="sxs-lookup"><span data-stu-id="daccf-520">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="daccf-521">Métodos de registro do serviço</span><span class="sxs-lookup"><span data-stu-id="daccf-521">Service registration methods</span></span>

<span data-ttu-id="daccf-522">Os métodos de extensão de registro de serviço oferecem sobrecargas que são úteis em cenários específicos.</span><span class="sxs-lookup"><span data-stu-id="daccf-522">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="daccf-523">Método</span><span class="sxs-lookup"><span data-stu-id="daccf-523">Method</span></span> | <span data-ttu-id="daccf-524">Automática</span><span class="sxs-lookup"><span data-stu-id="daccf-524">Automatic</span></span><br><span data-ttu-id="daccf-525">object</span><span class="sxs-lookup"><span data-stu-id="daccf-525">object</span></span><br><span data-ttu-id="daccf-526">descarte</span><span class="sxs-lookup"><span data-stu-id="daccf-526">disposal</span></span> | <span data-ttu-id="daccf-527">Vários</span><span class="sxs-lookup"><span data-stu-id="daccf-527">Multiple</span></span><br><span data-ttu-id="daccf-528">implementações</span><span class="sxs-lookup"><span data-stu-id="daccf-528">implementations</span></span> | <span data-ttu-id="daccf-529">Passar argumentos</span><span class="sxs-lookup"><span data-stu-id="daccf-529">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="daccf-530">Exemplo:</span><span class="sxs-lookup"><span data-stu-id="daccf-530">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="daccf-531">Sim</span><span class="sxs-lookup"><span data-stu-id="daccf-531">Yes</span></span> | <span data-ttu-id="daccf-532">Sim</span><span class="sxs-lookup"><span data-stu-id="daccf-532">Yes</span></span> | <span data-ttu-id="daccf-533">Não</span><span class="sxs-lookup"><span data-stu-id="daccf-533">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="daccf-534">Exemplos:</span><span class="sxs-lookup"><span data-stu-id="daccf-534">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="daccf-535">Sim</span><span class="sxs-lookup"><span data-stu-id="daccf-535">Yes</span></span> | <span data-ttu-id="daccf-536">Sim</span><span class="sxs-lookup"><span data-stu-id="daccf-536">Yes</span></span> | <span data-ttu-id="daccf-537">Sim</span><span class="sxs-lookup"><span data-stu-id="daccf-537">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="daccf-538">Exemplo:</span><span class="sxs-lookup"><span data-stu-id="daccf-538">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="daccf-539">Sim</span><span class="sxs-lookup"><span data-stu-id="daccf-539">Yes</span></span> | <span data-ttu-id="daccf-540">Não</span><span class="sxs-lookup"><span data-stu-id="daccf-540">No</span></span> | <span data-ttu-id="daccf-541">Não</span><span class="sxs-lookup"><span data-stu-id="daccf-541">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="daccf-542">Exemplos:</span><span class="sxs-lookup"><span data-stu-id="daccf-542">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="daccf-543">Não</span><span class="sxs-lookup"><span data-stu-id="daccf-543">No</span></span> | <span data-ttu-id="daccf-544">Sim</span><span class="sxs-lookup"><span data-stu-id="daccf-544">Yes</span></span> | <span data-ttu-id="daccf-545">Sim</span><span class="sxs-lookup"><span data-stu-id="daccf-545">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="daccf-546">Exemplos:</span><span class="sxs-lookup"><span data-stu-id="daccf-546">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="daccf-547">Não</span><span class="sxs-lookup"><span data-stu-id="daccf-547">No</span></span> | <span data-ttu-id="daccf-548">Não</span><span class="sxs-lookup"><span data-stu-id="daccf-548">No</span></span> | <span data-ttu-id="daccf-549">Sim</span><span class="sxs-lookup"><span data-stu-id="daccf-549">Yes</span></span> |

<span data-ttu-id="daccf-550">Para obter mais informações sobre o descarte de tipos, consulte a seção [Descarte de serviços](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="daccf-550">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="daccf-551">Um cenário comum para várias implementações é a [simulação de tipos para teste](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="daccf-551">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="daccf-552">Métodos `TryAdd{LIFETIME}` somente registrarão o serviço se ainda não houver uma implementação registrada.</span><span class="sxs-lookup"><span data-stu-id="daccf-552">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="daccf-553">No exemplo a seguir, a primeira linha registra `MyDependency` para `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="daccf-553">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="daccf-554">A segunda linha não tem nenhum efeito porque `IMyDependency` já tem uma implementação registrada:</span><span class="sxs-lookup"><span data-stu-id="daccf-554">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="daccf-555">Para obter mais informações, consulte:</span><span class="sxs-lookup"><span data-stu-id="daccf-555">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="daccf-556">Métodos [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) apenas registrarão o serviço se ainda não houver uma implementação *do mesmo tipo*.</span><span class="sxs-lookup"><span data-stu-id="daccf-556">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="daccf-557">Vários serviços são resolvidos via `IEnumerable<{SERVICE}>`.</span><span class="sxs-lookup"><span data-stu-id="daccf-557">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="daccf-558">Ao registrar os serviços, só convém ao desenvolvedor adicionar uma instância se até o momento nenhuma do mesmo tipo foi adicionada.</span><span class="sxs-lookup"><span data-stu-id="daccf-558">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="daccf-559">Em geral, esse método é usado por autores de biblioteca para evitar registrar duas cópias de uma instância no contêiner.</span><span class="sxs-lookup"><span data-stu-id="daccf-559">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="daccf-560">No exemplo a seguir, a primeira linha registra `MyDep` para `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="daccf-560">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="daccf-561">A segunda linha registra `MyDep` para `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="daccf-561">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="daccf-562">A terceira linha não tem nenhum efeito porque `IMyDep1` já tem uma implementação registrada de `MyDep`:</span><span class="sxs-lookup"><span data-stu-id="daccf-562">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="daccf-563">Comportamento da injeção de construtor</span><span class="sxs-lookup"><span data-stu-id="daccf-563">Constructor injection behavior</span></span>

<span data-ttu-id="daccf-564">Os serviços podem ser resolvidos por dois mecanismos:</span><span class="sxs-lookup"><span data-stu-id="daccf-564">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="daccf-565"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permite a criação de objeto sem registro de serviço no contêiner de injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="daccf-565"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="daccf-566">`ActivatorUtilities` é usado com abstrações voltadas ao usuário, como Auxiliares de Marca, controladores MVC e associadores de modelo.</span><span class="sxs-lookup"><span data-stu-id="daccf-566">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="daccf-567">Os construtores podem aceitar argumentos que não são fornecidos pela injeção de dependência, mas que precisam atribuir valores padrão.</span><span class="sxs-lookup"><span data-stu-id="daccf-567">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="daccf-568">Quando os serviços são resolvidos pelo `IServiceProvider` ou `ActivatorUtilities` , a [injeção de Construtor](xref:mvc/controllers/dependency-injection#constructor-injection) requer um construtor *público* .</span><span class="sxs-lookup"><span data-stu-id="daccf-568">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="daccf-569">Quando os serviços são resolvidos pelo `ActivatorUtilities` , a [injeção de Construtor](xref:mvc/controllers/dependency-injection#constructor-injection) requer que apenas um Construtor aplicável exista.</span><span class="sxs-lookup"><span data-stu-id="daccf-569">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="daccf-570">Há suporte para sobrecargas de construtor, mas somente uma sobrecarga pode existir, cujos argumentos podem ser todos atendidos pela injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="daccf-570">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="daccf-571">Contextos de Entity Framework</span><span class="sxs-lookup"><span data-stu-id="daccf-571">Entity Framework contexts</span></span>

<span data-ttu-id="daccf-572">Contextos do Entity Framework geralmente são adicionados ao contêiner de serviço usando o [tempo de vida com escopo](#service-lifetimes), pois o escopo de operações de banco de dados de aplicativo Web normalmente é para a solicitação do cliente.</span><span class="sxs-lookup"><span data-stu-id="daccf-572">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="daccf-573">O tempo de vida padrão será definido como escopo se um tempo de vida não for especificado por uma sobrecarga [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) ao registrar o contexto do banco de dados.</span><span class="sxs-lookup"><span data-stu-id="daccf-573">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="daccf-574">Serviços com um determinado tempo de vida não devem usar um contexto de banco de dados com um tempo de vida mais curto que aquele do serviço.</span><span class="sxs-lookup"><span data-stu-id="daccf-574">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="daccf-575">Opções de tempo de vida e de registro</span><span class="sxs-lookup"><span data-stu-id="daccf-575">Lifetime and registration options</span></span>

<span data-ttu-id="daccf-576">Para demonstrar a diferença entre as opções de tempo de vida e de registro, considere as interfaces a seguir, que representam tarefas como uma operação com um identificador exclusivo, `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="daccf-576">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="daccf-577">Dependendo de como o tempo de vida de um serviço de operações é configurado para as interfaces a seguir, o contêiner fornece a mesma instância do serviço, ou outra diferente, quando recebe uma solicitação de uma classe:</span><span class="sxs-lookup"><span data-stu-id="daccf-577">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="daccf-578">As interfaces são implementadas na classe `Operation`.</span><span class="sxs-lookup"><span data-stu-id="daccf-578">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="daccf-579">O construtor `Operation` gera um GUID, caso um não seja fornecido:</span><span class="sxs-lookup"><span data-stu-id="daccf-579">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="daccf-580">Há um `OperationService` registrado que depende de cada um dos outros `Operation` tipos.</span><span class="sxs-lookup"><span data-stu-id="daccf-580">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="daccf-581">Quando `OperationService` é solicitado por meio da injeção de dependência, ele recebe a uma nova instância de cada serviço, ou uma instância existente com base no tempo de vida do serviço dependente.</span><span class="sxs-lookup"><span data-stu-id="daccf-581">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="daccf-582">Quando os serviços temporários forem criados mediante solicitação do contêiner, o `OperationId` do serviço `IOperationTransient` será diferente do `OperationId` do `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="daccf-582">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="daccf-583">`OperationService` recebe uma nova instância da classe `IOperationTransient`.</span><span class="sxs-lookup"><span data-stu-id="daccf-583">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="daccf-584">A nova instância produz outro `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="daccf-584">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="daccf-585">Quando os serviços com escopo forem criados por solicitação do cliente, o `OperationId` do serviço `IOperationScoped` será o mesmo que o `OperationService` dentro de uma solicitação do cliente.</span><span class="sxs-lookup"><span data-stu-id="daccf-585">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="daccf-586">Em todas as solicitações do cliente, os dois serviços compartilham um valor de `OperationId` diferente.</span><span class="sxs-lookup"><span data-stu-id="daccf-586">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="daccf-587">Quando os serviços singleton e de instância singleton forem criados uma vez e usados em todas as solicitações de cliente e em todos os serviços, o `OperationId` será constante entre todas as solicitações de serviço.</span><span class="sxs-lookup"><span data-stu-id="daccf-587">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="daccf-588">Em `Startup.ConfigureServices`, cada tipo é adicionado ao contêiner de acordo com seu tempo de vida nomeado:</span><span class="sxs-lookup"><span data-stu-id="daccf-588">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="daccf-589">O serviço `IOperationSingletonInstance` está usando uma instância específica com uma ID conhecida de `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="daccf-589">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="daccf-590">Fica claro quando esse tipo está em uso (seu GUID contém apenas zeros).</span><span class="sxs-lookup"><span data-stu-id="daccf-590">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="daccf-591">O exemplo de aplicativo demonstra os tempos de vida do objeto dentro e entre solicitações individuais.</span><span class="sxs-lookup"><span data-stu-id="daccf-591">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="daccf-592">O exemplo de aplicativo `IndexModel` solicita cada tipo `IOperation` e o `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="daccf-592">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="daccf-593">Depois, a página exibe todos os valores de `OperationId` da classe de modelo de página e do serviço por meio de atribuições de propriedade:</span><span class="sxs-lookup"><span data-stu-id="daccf-593">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="daccf-594">As duas saídas a seguir mostram os resultados das duas solicitações:</span><span class="sxs-lookup"><span data-stu-id="daccf-594">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="daccf-595">**Primeira solicitação:**</span><span class="sxs-lookup"><span data-stu-id="daccf-595">**First request:**</span></span>

<span data-ttu-id="daccf-596">Operações do controlador:</span><span class="sxs-lookup"><span data-stu-id="daccf-596">Controller operations:</span></span>

<span data-ttu-id="daccf-597">Transitória: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="daccf-597">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="daccf-598">Com escopo: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="daccf-598">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="daccf-599">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="daccf-599">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="daccf-600">Instância: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="daccf-600">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="daccf-601">`OperationService` operações:</span><span class="sxs-lookup"><span data-stu-id="daccf-601">`OperationService` operations:</span></span>

<span data-ttu-id="daccf-602">Transitória: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="daccf-602">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="daccf-603">Com escopo: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="daccf-603">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="daccf-604">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="daccf-604">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="daccf-605">Instância: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="daccf-605">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="daccf-606">**Segunda solicitação:**</span><span class="sxs-lookup"><span data-stu-id="daccf-606">**Second request:**</span></span>

<span data-ttu-id="daccf-607">Operações do controlador:</span><span class="sxs-lookup"><span data-stu-id="daccf-607">Controller operations:</span></span>

<span data-ttu-id="daccf-608">Transitória: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="daccf-608">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="daccf-609">Com escopo: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="daccf-609">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="daccf-610">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="daccf-610">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="daccf-611">Instância: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="daccf-611">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="daccf-612">`OperationService` operações:</span><span class="sxs-lookup"><span data-stu-id="daccf-612">`OperationService` operations:</span></span>

<span data-ttu-id="daccf-613">Transitória: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="daccf-613">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="daccf-614">Com escopo: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="daccf-614">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="daccf-615">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="daccf-615">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="daccf-616">Instância: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="daccf-616">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="daccf-617">Observe qual dos valores de `OperationId` varia em uma solicitação, e entre as solicitações:</span><span class="sxs-lookup"><span data-stu-id="daccf-617">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="daccf-618">Os objetos *transitórios* sempre são diferentes.</span><span class="sxs-lookup"><span data-stu-id="daccf-618">*Transient* objects are always different.</span></span> <span data-ttu-id="daccf-619">O valor `OperationId` transitório da primeira e da segunda solicitações de cliente é diferente para as duas operações `OperationService` e em todas as solicitações de cliente.</span><span class="sxs-lookup"><span data-stu-id="daccf-619">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="daccf-620">Uma nova instância é fornecida para cada solicitação de serviço e solicitação de cliente.</span><span class="sxs-lookup"><span data-stu-id="daccf-620">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="daccf-621">Objetos *com escopo* são os mesmos em uma solicitação de cliente, mas diferentes entre solicitações de cliente.</span><span class="sxs-lookup"><span data-stu-id="daccf-621">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="daccf-622">Os pbjetos *singleton* são os mesmos para cada objeto e solicitação, independentemente de uma instância `Operation` ser fornecida em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="daccf-622">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="daccf-623">Chamar os serviços desde o principal</span><span class="sxs-lookup"><span data-stu-id="daccf-623">Call services from main</span></span>

<span data-ttu-id="daccf-624">Crie um <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> com [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) para resolver um serviço com escopo dentro do escopo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="daccf-624">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="daccf-625">Essa abordagem é útil para acessar um serviço com escopo durante a inicialização para executar tarefas de inicialização.</span><span class="sxs-lookup"><span data-stu-id="daccf-625">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="daccf-626">O exemplo a seguir mostra como obter um contexto para o `MyScopedService` em `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="daccf-626">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="daccf-627">Validação de escopo</span><span class="sxs-lookup"><span data-stu-id="daccf-627">Scope validation</span></span>

<span data-ttu-id="daccf-628">Quando o aplicativo está em execução no ambiente de desenvolvimento, o provedor de serviço padrão executa verificações para saber se:</span><span class="sxs-lookup"><span data-stu-id="daccf-628">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="daccf-629">Os serviços com escopo não são resolvidos direta ou indiretamente pelo provedor de serviço raiz.</span><span class="sxs-lookup"><span data-stu-id="daccf-629">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="daccf-630">Os serviços com escopo não são injetados direta ou indiretamente em singletons.</span><span class="sxs-lookup"><span data-stu-id="daccf-630">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="daccf-631">O provedor de serviços raiz é criado quando <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> é chamado.</span><span class="sxs-lookup"><span data-stu-id="daccf-631">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="daccf-632">O tempo de vida do provedor de serviço raiz corresponde ao tempo de vida do aplicativo/servidor quando o provedor começa com o aplicativo e é descartado quando o aplicativo é desligado.</span><span class="sxs-lookup"><span data-stu-id="daccf-632">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="daccf-633">Os serviços com escopo são descartados pelo contêiner que os criou.</span><span class="sxs-lookup"><span data-stu-id="daccf-633">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="daccf-634">Se um serviço com escopo é criado no contêiner raiz, o tempo de vida do serviço é promovido efetivamente para singleton, porque ele só é descartado pelo contêiner raiz quando o aplicativo/servidor é desligado.</span><span class="sxs-lookup"><span data-stu-id="daccf-634">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="daccf-635">A validação dos escopos de serviço detecta essas situações quando `BuildServiceProvider` é chamado.</span><span class="sxs-lookup"><span data-stu-id="daccf-635">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="daccf-636">Para obter mais informações, consulte <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="daccf-636">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="daccf-637">Serviços de solicitação</span><span class="sxs-lookup"><span data-stu-id="daccf-637">Request Services</span></span>

<span data-ttu-id="daccf-638">Os serviços disponíveis em uma solicitação do ASP.NET de `HttpContext` são expostos por meio da coleção [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).</span><span class="sxs-lookup"><span data-stu-id="daccf-638">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="daccf-639">Os Serviços de Solicitação representam os serviços configurados e solicitados como parte do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="daccf-639">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="daccf-640">Quando os objetos especificam dependências, elas são atendidas pelos tipos encontrados em `RequestServices`, não em `ApplicationServices`.</span><span class="sxs-lookup"><span data-stu-id="daccf-640">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="daccf-641">Em geral, o aplicativo não deve usar essas propriedades diretamente.</span><span class="sxs-lookup"><span data-stu-id="daccf-641">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="daccf-642">Em vez disso, solicite os tipos exigidos pelas classes por meio de construtores de classe e permita que a estrutura injete as dependências.</span><span class="sxs-lookup"><span data-stu-id="daccf-642">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="daccf-643">Isso resulta em classes que são mais fáceis de testar.</span><span class="sxs-lookup"><span data-stu-id="daccf-643">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="daccf-644">Prefira solicitar dependências como parâmetros de construtor para acessar a coleção `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="daccf-644">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="daccf-645">Projetar serviços para injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="daccf-645">Design services for dependency injection</span></span>

<span data-ttu-id="daccf-646">As melhores práticas são:</span><span class="sxs-lookup"><span data-stu-id="daccf-646">Best practices are to:</span></span>

* <span data-ttu-id="daccf-647">Projetar serviços para usar a injeção de dependência a fim de obter suas dependências.</span><span class="sxs-lookup"><span data-stu-id="daccf-647">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="daccf-648">Evite classes e membros com estado e estáticos.</span><span class="sxs-lookup"><span data-stu-id="daccf-648">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="daccf-649">Crie aplicativos para usar os serviços singleton, o que evita a criação de estado global.</span><span class="sxs-lookup"><span data-stu-id="daccf-649">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="daccf-650">Evite a instanciação direta das classes dependentes em serviços.</span><span class="sxs-lookup"><span data-stu-id="daccf-650">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="daccf-651">A instanciação direta acopla o código a uma implementação específica.</span><span class="sxs-lookup"><span data-stu-id="daccf-651">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="daccf-652">Verifique as classes de aplicativo pequenas, bem fatoradas e facilmente testadas.</span><span class="sxs-lookup"><span data-stu-id="daccf-652">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="daccf-653">Se parecer que uma classe tem muitas dependências injetadas, normalmente é um sinal de que a classe tem muitas responsabilidades e está violando o [Princípio da Responsabilidade Única (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="daccf-653">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="daccf-654">Tente refatorar a classe movendo algumas de suas responsabilidades para uma nova classe.</span><span class="sxs-lookup"><span data-stu-id="daccf-654">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="daccf-655">Tenha em mente que as classes Razor de modelo de página de páginas e as classes do controlador MVC devem se concentrar nas preocupações da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="daccf-655">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="daccf-656">Os detalhes de implementação de acesso aos dados e as regras de negócios devem ser mantidos em classes apropriadas a esses [interesses separados](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="daccf-656">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="daccf-657">Descarte de serviços</span><span class="sxs-lookup"><span data-stu-id="daccf-657">Disposal of services</span></span>

<span data-ttu-id="daccf-658">O contêiner chama <xref:System.IDisposable.Dispose*> para os tipos <xref:System.IDisposable> criados por ele.</span><span class="sxs-lookup"><span data-stu-id="daccf-658">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="daccf-659">Se uma instância for adicionada ao contêiner pelo código do usuário, ele não será descartado automaticamente.</span><span class="sxs-lookup"><span data-stu-id="daccf-659">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="daccf-660">No exemplo a seguir, os serviços são criados pelo contêiner de serviço e descartados automaticamente:</span><span class="sxs-lookup"><span data-stu-id="daccf-660">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="daccf-661">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="daccf-661">In the following example:</span></span>

* <span data-ttu-id="daccf-662">As instâncias de serviço não são criadas pelo contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="daccf-662">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="daccf-663">Os tempos de vida de serviço pretendidos não são conhecidos pela estrutura.</span><span class="sxs-lookup"><span data-stu-id="daccf-663">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="daccf-664">A estrutura não descarta os serviços automaticamente.</span><span class="sxs-lookup"><span data-stu-id="daccf-664">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="daccf-665">Se os serviços não forem explicitamente descartados no código do desenvolvedor, eles persistirão até que o aplicativo seja desligado.</span><span class="sxs-lookup"><span data-stu-id="daccf-665">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="daccf-666">Diretrizes de IDisposable para instâncias transitórias e compartilhadas</span><span class="sxs-lookup"><span data-stu-id="daccf-666">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="daccf-667">Tempo de vida transitório, limitado</span><span class="sxs-lookup"><span data-stu-id="daccf-667">Transient, limited lifetime</span></span>

<span data-ttu-id="daccf-668">**Cenário**</span><span class="sxs-lookup"><span data-stu-id="daccf-668">**Scenario**</span></span>

<span data-ttu-id="daccf-669">O aplicativo requer uma <xref:System.IDisposable> instância com um tempo de vida transitório para qualquer um dos seguintes cenários:</span><span class="sxs-lookup"><span data-stu-id="daccf-669">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="daccf-670">A instância é resolvida no escopo raiz.</span><span class="sxs-lookup"><span data-stu-id="daccf-670">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="daccf-671">A instância deve ser descartada antes do término do escopo.</span><span class="sxs-lookup"><span data-stu-id="daccf-671">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="daccf-672">**Solução**</span><span class="sxs-lookup"><span data-stu-id="daccf-672">**Solution**</span></span>

<span data-ttu-id="daccf-673">Use o padrão de fábrica para criar uma instância fora do escopo pai.</span><span class="sxs-lookup"><span data-stu-id="daccf-673">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="daccf-674">Nessa situação, o aplicativo geralmente teria um `Create` método que chamasse o construtor do tipo final diretamente.</span><span class="sxs-lookup"><span data-stu-id="daccf-674">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="daccf-675">Se o tipo final tiver outras dependências, a fábrica poderá:</span><span class="sxs-lookup"><span data-stu-id="daccf-675">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="daccf-676">Receber um <xref:System.IServiceProvider> em seu construtor.</span><span class="sxs-lookup"><span data-stu-id="daccf-676">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="daccf-677">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> para instanciar a instância fora do contêiner, ao usar o contêiner para suas dependências.</span><span class="sxs-lookup"><span data-stu-id="daccf-677">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="daccf-678">Instância compartilhada, tempo de vida limitado</span><span class="sxs-lookup"><span data-stu-id="daccf-678">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="daccf-679">**Cenário**</span><span class="sxs-lookup"><span data-stu-id="daccf-679">**Scenario**</span></span>

<span data-ttu-id="daccf-680">O aplicativo requer uma <xref:System.IDisposable> instância compartilhada entre vários serviços, mas o <xref:System.IDisposable> deve ter um tempo de vida limitado.</span><span class="sxs-lookup"><span data-stu-id="daccf-680">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="daccf-681">**Solução**</span><span class="sxs-lookup"><span data-stu-id="daccf-681">**Solution**</span></span>

<span data-ttu-id="daccf-682">Registre a instância com um tempo de vida de escopo.</span><span class="sxs-lookup"><span data-stu-id="daccf-682">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="daccf-683">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> para iniciar e criar um novo <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> .</span><span class="sxs-lookup"><span data-stu-id="daccf-683">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="daccf-684">Use o escopo <xref:System.IServiceProvider> para obter os serviços necessários.</span><span class="sxs-lookup"><span data-stu-id="daccf-684">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="daccf-685">Descarte o escopo quando o tempo de vida deve terminar.</span><span class="sxs-lookup"><span data-stu-id="daccf-685">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="daccf-686">Diretrizes gerais</span><span class="sxs-lookup"><span data-stu-id="daccf-686">General Guidelines</span></span>

* <span data-ttu-id="daccf-687">Não registre <xref:System.IDisposable> instâncias com um escopo transitório.</span><span class="sxs-lookup"><span data-stu-id="daccf-687">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="daccf-688">Em vez disso, use o padrão de fábrica.</span><span class="sxs-lookup"><span data-stu-id="daccf-688">Use the factory pattern instead.</span></span>
* <span data-ttu-id="daccf-689">Não resolva instâncias transitórias ou com escopo definido <xref:System.IDisposable> no escopo raiz.</span><span class="sxs-lookup"><span data-stu-id="daccf-689">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="daccf-690">A única exceção geral é quando o aplicativo cria/recria e descarta o <xref:System.IServiceProvider> , que não é um padrão ideal.</span><span class="sxs-lookup"><span data-stu-id="daccf-690">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="daccf-691">O recebimento de uma <xref:System.IDisposable> dependência por meio de di não exige que o receptor se implemente <xref:System.IDisposable> .</span><span class="sxs-lookup"><span data-stu-id="daccf-691">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="daccf-692">O receptor da <xref:System.IDisposable> dependência não deve chamar <xref:System.IDisposable.Dispose%2A> essa dependência.</span><span class="sxs-lookup"><span data-stu-id="daccf-692">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="daccf-693">Os escopos devem ser usados para controlar o tempo de vida dos serviços.</span><span class="sxs-lookup"><span data-stu-id="daccf-693">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="daccf-694">Os escopos não são hierárquicos e não há nenhuma conexão especial entre escopos.</span><span class="sxs-lookup"><span data-stu-id="daccf-694">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="daccf-695">Substituição do contêiner de serviço padrão</span><span class="sxs-lookup"><span data-stu-id="daccf-695">Default service container replacement</span></span>

<span data-ttu-id="daccf-696">O contêiner de serviço interno foi projetado para atender às necessidades da estrutura e da maioria dos aplicativos de consumidor.</span><span class="sxs-lookup"><span data-stu-id="daccf-696">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="daccf-697">É recomendável usar o contêiner interno, a menos que você precise de um recurso específico ao qual o contêiner interno não ofereça suporte, como:</span><span class="sxs-lookup"><span data-stu-id="daccf-697">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="daccf-698">Injeção de propriedade</span><span class="sxs-lookup"><span data-stu-id="daccf-698">Property injection</span></span>
* <span data-ttu-id="daccf-699">Injeção com base no nome</span><span class="sxs-lookup"><span data-stu-id="daccf-699">Injection based on name</span></span>
* <span data-ttu-id="daccf-700">Contêineres filho</span><span class="sxs-lookup"><span data-stu-id="daccf-700">Child containers</span></span>
* <span data-ttu-id="daccf-701">Gerenciamento de tempo de vida personalizado</span><span class="sxs-lookup"><span data-stu-id="daccf-701">Custom lifetime management</span></span>
* <span data-ttu-id="daccf-702">Suporte de `Func<T>` para inicialização lenta</span><span class="sxs-lookup"><span data-stu-id="daccf-702">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="daccf-703">Registro baseado em Convenção</span><span class="sxs-lookup"><span data-stu-id="daccf-703">Convention-based registration</span></span>

<span data-ttu-id="daccf-704">Os seguintes contêineres de terceiros podem ser usados com aplicativos ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="daccf-704">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="daccf-705">Autofac</span><span class="sxs-lookup"><span data-stu-id="daccf-705">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="daccf-706">DryIoc</span><span class="sxs-lookup"><span data-stu-id="daccf-706">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="daccf-707">Grace</span><span class="sxs-lookup"><span data-stu-id="daccf-707">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="daccf-708">LightInject</span><span class="sxs-lookup"><span data-stu-id="daccf-708">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="daccf-709">Lamar</span><span class="sxs-lookup"><span data-stu-id="daccf-709">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="daccf-710">Stashbox</span><span class="sxs-lookup"><span data-stu-id="daccf-710">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="daccf-711">Unity</span><span class="sxs-lookup"><span data-stu-id="daccf-711">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="daccf-712">Acesso thread-safe</span><span class="sxs-lookup"><span data-stu-id="daccf-712">Thread safety</span></span>

<span data-ttu-id="daccf-713">Crie serviços singleton thread-safe.</span><span class="sxs-lookup"><span data-stu-id="daccf-713">Create thread-safe singleton services.</span></span> <span data-ttu-id="daccf-714">Se um serviço singleton tiver uma dependência em um serviço transitório, o serviço transitório também precisará ter acesso thread-safe, dependendo de como ele é usado pelo singleton.</span><span class="sxs-lookup"><span data-stu-id="daccf-714">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="daccf-715">O método de fábrica de um único serviço, como o segundo argumento para [addsingleton \<TService> (IServiceCollection, Func \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), não precisa ser thread-safe.</span><span class="sxs-lookup"><span data-stu-id="daccf-715">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="daccf-716">Como um construtor do tipo (`static`), ele tem garantia de ser chamado uma vez por um único thread.</span><span class="sxs-lookup"><span data-stu-id="daccf-716">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="daccf-717">Recomendações</span><span class="sxs-lookup"><span data-stu-id="daccf-717">Recommendations</span></span>

* <span data-ttu-id="daccf-718">A resolução de serviço baseada em `async/await` e `Task` não é compatível.</span><span class="sxs-lookup"><span data-stu-id="daccf-718">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="daccf-719">O C# não é compatível com os construtores assíncronos. Portanto, o padrão recomendado é usar os métodos assíncronos após a resolução síncrona do serviço.</span><span class="sxs-lookup"><span data-stu-id="daccf-719">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="daccf-720">Evite armazenar dados e a configuração diretamente no contêiner do serviço.</span><span class="sxs-lookup"><span data-stu-id="daccf-720">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="daccf-721">Por exemplo, o carrinho de compras de um usuário normalmente não deve ser adicionado ao contêiner do serviço.</span><span class="sxs-lookup"><span data-stu-id="daccf-721">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="daccf-722">A configuração deve usar o [padrão de opções](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="daccf-722">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="daccf-723">Da mesma forma, evite objetos de "suporte de dados" que existem somente para permitir o acesso a outro objeto.</span><span class="sxs-lookup"><span data-stu-id="daccf-723">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="daccf-724">É melhor solicitar o item real por meio da DI.</span><span class="sxs-lookup"><span data-stu-id="daccf-724">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="daccf-725">Evite o acesso estático aos serviços (por exemplo, digitando estaticamente [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) para usar em outro lugar).</span><span class="sxs-lookup"><span data-stu-id="daccf-725">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="daccf-726">Evite usar o *padrão de localizador de serviço*, que combina a [inversão de estratégias de controle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) .</span><span class="sxs-lookup"><span data-stu-id="daccf-726">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

  * <span data-ttu-id="daccf-727">Não invoque <xref:System.IServiceProvider.GetService*> para obter uma instância de serviço quando você pode usar di em vez disso:</span><span class="sxs-lookup"><span data-stu-id="daccf-727">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="daccf-728">**Incorreto:**</span><span class="sxs-lookup"><span data-stu-id="daccf-728">**Incorrect:**</span></span>

    ```csharp
    public class MyClass()
    {
        public void MyMethod()
        {
            var optionsMonitor = 
                _services.GetService<IOptionsMonitor<MyOptions>>();
            var option = optionsMonitor.CurrentValue.Option;

            ...
        }
    }
    ```

    <span data-ttu-id="daccf-729">**Correto**:</span><span class="sxs-lookup"><span data-stu-id="daccf-729">**Correct**:</span></span>

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

  * <span data-ttu-id="daccf-730">Evite injetar uma fábrica que resolva dependências em tempo de execução usando <xref:System.IServiceProvider.GetService*> .</span><span class="sxs-lookup"><span data-stu-id="daccf-730">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>

* <span data-ttu-id="daccf-731">Evite o acesso estático a `HttpContext` (por exemplo, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="daccf-731">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="daccf-732">Como todos os conjuntos de recomendações, talvez você encontre situações em que é necessário ignorar uma recomendação.</span><span class="sxs-lookup"><span data-stu-id="daccf-732">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="daccf-733">As exceções são raras, principalmente os casos especiais dentro da própria estrutura.</span><span class="sxs-lookup"><span data-stu-id="daccf-733">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="daccf-734">A DI é uma *alternativa* aos padrões de acesso a objeto estático/global.</span><span class="sxs-lookup"><span data-stu-id="daccf-734">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="daccf-735">Talvez você não obtenha os benefícios da DI se combiná-lo com o acesso a objeto estático.</span><span class="sxs-lookup"><span data-stu-id="daccf-735">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="daccf-736">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="daccf-736">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="daccf-737">Quatro maneiras de descartar IDisposables em ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="daccf-737">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="daccf-738">Como gravar um código limpo no ASP.NET Core com injeção de dependência (MSDN)</span><span class="sxs-lookup"><span data-stu-id="daccf-738">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="daccf-739">Princípio de Dependências Explícitas</span><span class="sxs-lookup"><span data-stu-id="daccf-739">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="daccf-740">Inversão de Contêineres de Controle e o padrão de Injeção de Dependência (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="daccf-740">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="daccf-741">Como registrar um serviço com várias interfaces na DI do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="daccf-741">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end
